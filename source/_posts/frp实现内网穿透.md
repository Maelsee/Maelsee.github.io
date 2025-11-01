---

title: frp实现内网穿透
typora-root-url: frp实现内网穿透
mathjax: true
date: 2025-10-27 10:21:10
tags: frp
categories: 配置
---

### 在 Linux 中使用 Docker 安装 FRP 实现内网穿透

以下是详细步骤，分为**公网服务器（服务端）**和**内网机器（客户端）**两部分操作。公网服务器 IP：`111.228.15.xxx`。

------

### 🔧 **第一步：公网服务器（服务端）配置**

#### 1. 登录公网服务器

```bash
ssh root@111.228.15.xxx -p 22  # 使用你的实际用户名和密码
```

#### 2. 创建 FRP 服务端配置文件

```bash
mkdir -p ~/frp/config && cd ~/frp/config
vim frps.toml
```

写入以下内容：

```toml
# frps.toml
bindPort = 7000 # 服务端与客户端通信端口
transport.tls.force = true # 服务端将只接受 TLS链接

auth.token = "your_secure_token" # 身份验证令牌，frpc要与frps一致

# Server Dashboard，可以查看frp服务状态以及统计信息
webServer.addr = "0.0.0.0" # 后台管理地址
webServer.port = 7500 # 后台管理端口
webServer.user = "admin" # 后台登录用户名
webServer.password = "admin123" # 后台登录密码

```

#### 3. 启动 FRP 服务端容器

```bash
docker run -d \
  --name frps \
  --restart=always \
  -p 7000:7000 \
  -p 7500:7500 \
  -p 6000:6000 \
  -v $PWD/frps.toml:/etc/frp/frps.toml \
  docker.1ms.run/snowdreamtech/frps
```

> **说明**：
>
> - `7000`：FRP 客户端连接端口
> - `7500`：仪表盘端口（访问 `http://111.228.15.xxx:7500`可查看状态）

**更推荐使用docker-compose.yml来管理容器**

```shell
cd ~/frp
vim docker-compose.yml 
```

添加以下内容：

```yml
# /frp/docker-compose.yml 
# version: '3.8'
services:
  frps:
    image: docker.1ms.run/snowdreamtech/frps
    container_name: frps
    environment:
      - TZ=Asia/Shanghai  # 设置时区
    restart: always
    ports:
      - "7000:7000"   # FRP 主服务端口
      - "7500:7500"   # 仪表盘端口
      - "6000:6000"   # 额外端口 1
    volumes:
      - ./config/frps.toml:/etc/frp/frps.toml

```

启动容器

```bash
docker compose up -d #后台启动
docker compose down  #关闭容器
docker compose restart frps #重启frps容器
docker logs -f frps  #查看frps容器日志
# 应该会输出 
# client login info: ip [118.181.xxx.xxx:xxxx] version [0.xx.0]
# tcp proxy listen port [6000]...
```

#### 4. 开放防火墙端口

```
ufw allow 7000/tcp
ufw allow 7500/tcp
ufw allow 6000/tcp
ufw reload
```

**最重要的是将服务器商的控制面板里的相应端口都开放。**

------

### 💻 **第二步：内网机器（客户端）配置**

#### 1. 在内网机器创建配置文件

```bash
mkdir -p ~/frp/config && cd ~/frp/config
vim frpc.toml
```

写入以下内容（替换 `your_secure_token`为服务端设置的令牌）：

```toml
# 启用 TLS 加密连接
transport.tls.enable = true

# 服务端地址和端口
serverAddr = "111.228.15.XXX"   # 公网服务器IP
serverPort = 7000              # 服务端通信端口

# 身份验证
auth.token = "your_secure_token"  # 替换为服务端设置的token

# 定义代理
[[proxies]]
name = "ssh"
type = "tcp"
#"127.0.0.1"是frpc容器内的地址，但是ssh是宿主机的服务。有以下几种修改方式：192.168.x.x，host.docker.internal，172.17.0.1
localIP = "host.docker.internal" 
localPort = 22
remotePort = 6000
```

> 要代理的服务在宿主机上或在不同docker网络中：将localIP从127.0.0.1改为宿主机的局域网IP（例如192.168.x.x）或者使用Docker的宿主机别名host.docker.internal，或者使用172.17.0.1（Docker默认网桥的网关地址）
>
> 如果代理的服务（web）和frpc在同一docker网络（例如：--network frp-net）中,直接使用容器名：localIP = "web"

#### 2. 启动 FRP 客户端容器

```bash
docker run -d \
  --name frpc \
  --restart=always \
  --network frp-net \
  -v $PWD/frpc.toml:/etc/frp/frpc.toml \
  docker.1ms.run/snowdreamtech/frpc
```

**更推荐使用docker-compose.yml来管理容器**

```shell
cd ~/frp
vim docker-compose.yml 
```

添加以下内容：

```yml
# /frp/docker-compose.yml 
# version: '3.8'
services:
  frpc:
    image: docker.1ms.run/snowdreamtech/frpc
    container_name: frpc
    environment:
      - TZ=Asia/Shanghai  # 设置时区
    restart: always
    volumes:
      - ./config/frpc.toml:/etc/frp/frpc.toml
    networks:
      - frp-net
    extra_hosts:
      - "host.docker.internal:host-gateway"

networks:
  frp-net:
    driver: bridge

```

启动容器

```bash
docker compose up -d #后台启动
docker compose down  #关闭容器
docker compose restart frpc #重启frpc容器
docker logs -f frpc  #查看frpc容器日志
# 应该会输出 
# login to server success, get run id ...
# [ssh] start proxy success

```

------

### ✅ **第三步：验证穿透是否成功**

1. **检查服务端仪表盘** 访问 `http://111.228.15.xxx:7500`，输入账号 `admin`/密码 `admin123`，查看客户端是否在线。

2. **通过公网访问内网服务**

   使用 SSH 连接内网机器（通过公网服务器的 `6000`端口）：

   ```
   ssh 内网用户名@111.228.15.xxx -p 6000
   ```

------

### 🔄 **管理命令**

| 功能           | 命令                       |
| -------------- | -------------------------- |
| 查看服务端日志 | `docker logs frps`         |
| 重启客户端     | `docker restart frpc`      |
| 更新配置       | 修改 `.toml`文件后重启容器 |

------

### ⚠️ **安全建议**

1. **修改默认令牌**：替换 `your_secure_token`为高强度字符串。
2. **限制访问 IP**：在服务端 `frps.toml`添加 `allow_ports`限制可访问端口。
3. **使用 HTTPS**：若暴露 Web 服务，建议在 FRP 配置中启用 TLS。

> 以上步骤已完整覆盖 FRP 内网穿透的 Docker 部署流程。如有问题，可检查防火墙或查看容器日志定位错误。

---
title: github配置SSH-Key保姆教程
date: 2025-04-15 16:02:40
tags: github
categories: 配置    
---

# github配置SSH-Key保姆级教程

### 配置过程

### 第1步：查看 或者 生成一个[SSH-Key](https://zhida.zhihu.com/search?content_id=241042157&content_type=Article&match_order=1&q=SSH-Key&zhida_source=entity)

```cpp
// 新环境大概率会报错 ，因为这个目录不存在
$ cd ~/.ssh
```

如果报错如下

![img](https://picx.zhimg.com/v2-8cd1f45e079593a46cbb4bc449c86609_1440w.jpg)



使用下面命令生成ssh-key

```cpp
ssh-keygen -t rsa -C "xxx@xxx.com"  // 将 "xxx@xxx.com" 替换为你自己GitHub的邮箱地址
```

然后一直按 “enter”键，如下图

![img](https://pic3.zhimg.com/v2-0be2a290a6a935679ac33b3eb9518686_1440w.jpg)



### 第2步：获取ssh key公钥内容

```cpp
// 进入ssh目录
$ cd ~/.ssh
//其中：
//id_rsa：是私钥
//id_rsa.pub：是公钥
// 查看ssh 公钥  进行复制
$ cat id_rsa.pub
```



![img](https://pic3.zhimg.com/v2-3dec0306103257b6adfdb581aa640f28_1440w.jpg)



### 第3步：GitHub设置中添加公钥

点击GitHub中设置标签，然后点击 **SSH and GPG keys** 、 **New SSH key** 将复制好的链接粘贴进去

![img](https://pic4.zhimg.com/v2-61ad52bd47aedb08f6d39fa4750ad847_1440w.jpg)



![img](https://picx.zhimg.com/v2-7ff3ec3c4945801c9174f40dda7a7ba3_1440w.jpg)



### 第4步：检查是否设置成功

```cpp
$ ssh -T git@github.com
```

看到**successfully**字样就成功了



### 第5步：GitHub中创建仓库，并使用ssh链接进行下拉

```cpp
$ git clone + "ssh链接"
```



### 第6步：编码，代码上传

```cpp
解释// 查看更改项
$ git status
// 添加所有更改项
$ git add .
// 提交commit , 这一步可能会有问题
$ git commit -m "feat:add code"
// 推送至远端
$git push
```

在commit的时候可能会碰到问题 “**please tell me who you are**”,按照提示进行设置就行

```cpp
$ git config --global user.email "you@example.com"   // "you@example.com"替换为你的GitHub邮箱地址
$ git config --global user.name "Your Name"   // "Your Name"替换为你的GitHub名称
```




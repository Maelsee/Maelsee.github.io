---
title: hexo分支管理及图片显示
typora-root-url: hexo分支管理及图片显示
mathjax: true
date: 2025-07-25 11:57:34
tags: hexo
categories: 配置
---

## Hexo 分支

在使用 Hexo 搭建 GitHub Page 博客时，通常会遇到如何保存 Hexo 环境和博客源码的问题。一个常见的解决方案是使用 GitHub 分支来管理不同的内容。

### 创建和管理分支

首先，在 GitHub 上创建一个新的仓库，例如 *`username.github.io`*。然后，在该仓库下创建两个分支：*`master`* 和 *`hexo`*。其中，*`master`* 分支用于保存 `Hexo` 生成的静态 HTML 文件，而 *`hexo`* 分支用于保存 `Hexo` 环境和博客的 Markdown 源码。

### 初始化 Hexo 环境

在本地克隆仓库后，依次执行以下命令来初始化 Hexo 环境：

``` shell
npm install hexo

hexo init

npm install

npm install hexo-deployer-git
```

接着，配置 Hexo 的 *_config.yml* 文件中的 *deploy* 参数，将分支设置为 *master*：

```yaml
deploy:

type: git

repo: git@github.com:username/username.github.io.git

branch: master
# 现在GitHub已经默认主分支为main
```

### 提交和部署

每次修改或新增博文后，依次执行以下命令将博客源码提交到 *`hexo`* 分支：

```shell
git add

git commit -m "更新博客"

git push origin hexo
```

然后，执行 *`hexo generate -d`* 命令生成网站并部署到 GitHub 上的 *master* 分支：

```shell
hexo generate -d
```

### 日常操作

在完成初始配置后，日常操作只需重复上述提交和部署步骤即可。在本地修改博客内容后，通过以下命令将改动推送到 GitHub：

```shell
git add

git commit -m "更新博客"

git push origin hexo
```

然后，执行以下命令发布网站到 *master* 分支：

```shell
hexo generate -d
```

### 处理本地资料丢失

如果需要在其他电脑上修改博客，可以通过以下步骤恢复环境：

- 克隆仓库到本地：

```shell
git clone git@github.com:username/username.github.io.git
```

- 重新安装 Hexo 编译环境：

```shell
npm install hexo

npm install

npm install hexo-deployer-git
```

注意：*`hexo init`* 命令会初始化 Hexo 环境，但会清空 *.git* 文件夹，导致版本控制信息丢失。因此，除非环境损坏，否则无需运行此命令

通过以上步骤，可以高效地管理 Hexo 环境和博客源码，确保博客内容的安全和可维护性。

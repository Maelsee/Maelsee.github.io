# Maelsee.github.io

本项目基于 [Hexo](https://hexo.io/zh-cn/) 搭建，用于个人学习与技术记录，支持 Markdown 写作与自动化部署。

---

## 📁 目录结构

```text
├── _config.yml           # Hexo 主配置文件
├── package.json          # 项目依赖与脚本
├── db.json               # Hexo 生成的数据文件（已忽略）
├── public/               # 生成的静态网页目录（已忽略）
├── source/               # 文章与页面源码
│   ├── _posts/           # 博文 Markdown 文件
│   ├── about/            # 关于页面
│   ├── contact/          # 联系方式页面
│   ├── friends/          # 友链页面
│   ├── tags/             # 标签页面
│   └── categories/       # 分类页面
├── themes/               # 主题目录（如 hexo-theme-matery）
├── scaffolds/            # 模板文件（新建文章/页面的默认结构）
├── .deploy_git/          # 部署相关目录（自动生成）
├── .gitignore            # Git 忽略文件
└── README.md             # 项目说明文档
```

---

## 🛠️ 技术栈
- Node.js
- Hexo 7.x
- hexo-theme-matery（或其它主题）
- Markdown
- Git & GitHub Pages

### 主要依赖（部分）
- hexo
- hexo-deployer-git
- hexo-generator-archive/category/index/tag
- hexo-renderer-ejs/marked/stylus
- hexo-server
- hexo-wordcount

详见 `package.json`。

---

## 🖼️ Typora 图片显示与 `typora-root-url`

为保证在本地（Typora 编辑器）和 Hexo 博客中图片都能正常显示，建议在每篇 Markdown 文件开头添加如下内容：

```markdown
typora-root-url: ../../
```

- 作用：指定图片根路径，Typora 会自动拼接图片相对路径，保证本地预览和博客发布一致。
- 建议：根据实际目录结构调整 `typora-root-url` 的层级（如 `../`、`../../` 等）。
- 示例：

  ```markdown
  typora-root-url: ../../
  ---
  title: 我的博客文章
  date: 2024-06-01
  ```

- 相关文档：[Typora 官方文档 - 图片根路径](https://support.typora.io/Images/#root-image-folder)

---

## 📦 文章资源管理

- **图片与附件推荐存放方式**：
  - 建议开启 `post_asset_folder: true`（已在 _config.yml 默认开启），每篇文章的图片、附件等资源与 Markdown 文件同名同目录，便于管理。
  - 示例：
    ```
    source/_posts/
      ├── hello-world.md
      └── hello-world/
          └── image.png
    ```
  - 引用方式：`![](hello-world/image.png)`

---

## 🎨 主题自定义建议

- 主题配置建议在 `themes/主题名/_config.yml` 中进行，避免直接修改主题源码，便于后续升级。
- 可通过 `source/_data/` 下的 `custom.css`、`custom.js` 或 `custom.yml` 进行样式和配置扩展（部分主题支持）。
- 推荐定期备份主题自定义内容。

---

## ❓ 常见问题与解决方案

- **本地图片不显示/部署后图片丢失**：
  - 检查 `typora-root-url` 设置和 `post_asset_folder` 配置。
  - 确认图片路径为相对路径，且资源已随文章一同推送。
- **部署失败/无权限**：
  - 检查 GitHub Token 权限，或重新配置 hexo-deployer-git。
- **依赖冲突/报错**：
  - 删除 `node_modules` 和 `package-lock.json`，重新 `npm install`。

---

## 🤖 自动化部署建议

- 推荐使用 GitHub Actions 实现自动化部署，推送到主分支后自动构建并发布到 gh-pages。
- 参考：[Hexo + GitHub Actions 自动部署教程](https://hexo.io/zh-cn/docs/github-pages#%E4%BD%BF%E7%94%A8-GitHub-Actions-%E8%87%AA%E5%8A%A8%E9%83%A8%E7%BD%B2)

---

## ✅ Hexo 常用命令

下面列出在本项目中常用的 Hexo 命令，包含说明与示例组合命令（适用于 Windows PowerShell）。在执行命令前，建议先在项目根目录运行 `npm install` 确保依赖已安装。

- 安装依赖（首次或依赖变更后）：

```powershell
npm install
```

- 本地预览（启动 Hexo 本地服务器，默认监听 http://localhost:4000）：

```powershell
# 在开发时使用，实时监听并在文件变更时自动重载
npx hexo clean; npx hexo server
# 或者（更明确）
# npx hexo clean; npx hexo server --draft --future
```

- 仅生成静态文件到 `public/`（不启动服务器）：

```powershell
npx hexo clean; npx hexo generate
# 简写：npx hexo g
```

- 部署到远程（使用 hexo-deployer-git，根据 _config.yml 的 deploy 配置进行推送）：

```powershell
npx hexo deploy
# 简写：npx hexo d
```

- 本地调试生成并部署（常用组合）：

```powershell
# 清理旧生成文件，生成最新静态文件并推送
npx hexo clean; npx hexo generate; npx hexo deploy
```

- 新建文章 / 草稿：

```powershell
# 新建正式文章（会放入 source/_posts/）
npx hexo new "你的文章标题"
# 新建草稿（放入 source/_drafts/）
npx hexo new draft "草稿标题"
```

- 清理缓存与生成内容（遇到奇怪的生成/资源问题时执行）：

```powershell
npx hexo clean
```

- 列出可用命令与帮助：

```powershell
npx hexo help
```

### 常见工作流示例

1) 本地写作到发布（推荐）

```powershell
# 1. 新建文章并编辑
npx hexo new "my-post"
# 编辑完成后
npx hexo clean; npx hexo generate
# 检查本地预览
npx hexo server
# 发布到 GitHub Pages
npx hexo deploy
```

2) 使用 GitHub Actions 自动部署（仓库已配置）

- 本地只需推送到仓库主分支（或仓库的 action 触发分支），CI 会自动执行 `npm ci`、`npx hexo generate` 并将 `public/` 部署到 GitHub Pages。

### 小贴士

- 使用 `npx` 可避免全局安装 hexo；若你更常用全局安装，可改用 `hexo` 替换 `npx hexo`。
- 遇到依赖问题，先删除 `node_modules` 和 `package-lock.json`，然后重新 `npm install`。
- 如果图片在本地可见但部署后缺失，检查 `post_asset_folder` 是否已开启，以及图片路径是否正确。

---

*已将 Hexo 常用命令加入本 README，便于本地开发与自动部署。*

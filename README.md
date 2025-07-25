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

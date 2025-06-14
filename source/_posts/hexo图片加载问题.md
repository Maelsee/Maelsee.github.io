---
title: hexo图片加载问题
date: 2025-04-27 20:08:54
tags: hexo
categories: 配置
---

### 一、通用解决方案（推荐）

#### 1. **启用资源文件夹并配置插件**
- 步骤1：在_config.yml中配置


    ```yaml
    post_asset_folder: true
    ```

- 步骤 2：安装适配新版 Hexo 的插件（二选一）


    ```bash
    # 方案一：改进版插件（兼容性更强）
    npm uninstall hexo-asset-image --save
    npm install hexo-asset-img --save
    
    # 方案二：官方推荐语法（无需插件）
    npm install hexo-renderer-marked --save
    ```


    在_config.yml中补充配置：

    ```yaml
    marked:
      prependRoot: true
      postAsset: true
    ```

#### 2. **Typora 与 Markdown 协作优化**

- Typora设置：` 偏好设置 → 图像 → 复制到 ./${filename}`，实现图片自动存入同名文件夹


 - Markdown 语法：


    ```markdown
    <!-- 插件方案 -->
    {% asset_img image.jpg 图片描述 %}
    
    <!-- 无插件方案（需 hexo-renderer-marked） -->
    ![图片描述](image.jpg)
    ```

#### 3. **验证生成路径**

- 执行
  ```
  hexo clean && hexo g --debug
  ```
  检查 `public/年份/月份/日期/文章名/` 目录下是否包含图片文件

  

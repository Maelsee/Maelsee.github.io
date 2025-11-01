---
title: nginx配置压缩和智能分发
typora-root-url: nginx配置压缩和智能分发
mathjax: true
date: 2025-10-27 11:31:42
tags: nginx
categories: 配置
---

# Nginx配置优化：压缩与智能分发

## 概述

本文档详细分析前后端分离Django项目的Nginx配置，重点介绍压缩优化和智能分发策略。

## 配置结构分析

### 双Server块设计

```
# 主站点服务器 - example.com
server {
    listen 80;
    server_name example.com;
    # 配置内容...
}

# 管理后台服务器 - admin.example.com  
server {
    listen 80;
    server_name admin.example.com;
    # 配置内容...
}
```

**设计优势**：

- 基于域名进行流量分发
- 主站和管理后台分离，提高安全性
- 相同端口不同服务，简化部署

## 智能压缩配置

### 1. 响应压缩（建议添加）

```
# 在http上下文中添加（nginx.conf主配置文件）
gzip on;
gzip_vary on;
gzip_min_length 1024;
gzip_types
    text/plain
    text/css
    text/xml
    text/javascript
    application/javascript
    application/xml+rss
    application/json
    image/svg+xml;
```

### 2. 客户端压缩支持检测

```
# 可添加的智能压缩检测
map $http_accept_encoding $gzip_extension {
    default     "";
    "~*gzip"    ".gz";
}
```

## 智能分发策略

### 1. WebP智能图片分发

```
# WebP支持检测映射
map $http_accept $webp_suffix {
    default   "";
    "~*webp"  ".webp";
}

# 应用WebP智能分发
location ~* \.(png|jpg|jpeg)$ {
    try_files $uri$webp_suffix $uri =404;
    add_header Vary Accept;
}
```

**工作机制**：

- 检测客户端Accept头是否包含"webp"
- 优先返回WebP格式图片（体积更小）
- 回退到原始格式保证兼容性

### 2. 路径智能路由

```
# API请求路由到Django后端
location ~ ^/(api|ckeditor)/ {
    proxy_pass http://web:8000;
    # 代理配置...
}

# 静态资源服务
location / {
    root /usr/share/nginx/html;
    try_files $uri $uri/ $uri.html /index.html;
}

# 媒体文件处理
location /media/ {
    alias /app/media/;
}
```

## 缓存控制策略

### 1. 开发环境禁用缓存

```
# 完全禁用缓存配置
sendfile off;
tcp_nopush off;
tcp_nodelay on;
add_header Cache-Control "no-cache, no-store, must-revalidate";
add_header Pragma "no-cache";
add_header Expires "0";
etag off;
proxy_cache off;
proxy_buffering off;
```

### 2. 生产环境缓存建议

```
# 生产环境推荐配置
location ~* \.(js|css|png|jpg|jpeg|gif|ico|svg)$ {
    expires 1y;
    add_header Cache-Control "public, immutable";
}

location / {
    try_files $uri $uri/ /index.html;
    add_header Cache-Control "no-cache";
}
```

## 安全优化配置

### 1. 文件上传限制

```
client_max_body_size 50M;  # 防止大文件攻击
```

### 2. 管理路径隐藏

```
# 隐藏原始admin路径
location ~ ^/admin(/|$) {
    return 500;
}
```

### 3. 独立管理后台域名

```
server_name admin.example.com;  # 管理后台独立域名
```

## 性能优化配置

### 1. 连接超时设置

```
proxy_connect_timeout 30s;
proxy_send_timeout 30s;
proxy_read_timeout 30s;
```

### 2. WebSocket支持

```
proxy_http_version 1.1;
proxy_set_header Upgrade $http_upgrade;
proxy_set_header Connection "upgrade";
```

## URL重写优化

### 1. 无扩展名访问

```
# 重定向.html到无扩展名URL
location ~ \.html$ {
    rewrite ^(.*)\.html$ $scheme://$http_host$1 permanent;
}
```

**修复问题**：保留原始请求的端口信息，避免重定向后端口丢失。

## 健康检查与监控

```
location /health {
    access_log off;
    return 200 "healthy\n";
    add_header Content-Type text/plain;
}
```

## Docker部署注意事项

### 1. 静态文件路径映射

```
# 前端静态文件
root /usr/share/nginx/html;

# Django静态文件  
alias /app/staticfiles/;

# 媒体文件
alias /app/media/;
```

### 2. 服务发现配置

```
# Docker容器间通信
proxy_pass http://web:8000;
```

## 完整优化配置示例

```
# 压缩配置
gzip on;
gzip_vary on;
gzip_min_length 1024;
gzip_comp_level 6;
gzip_types text/plain text/css application/json application/javascript text/xml application/xml image/svg+xml;

# WebP智能检测
map $http_accept $webp_suffix {
    default   "";
    "~*webp"  ".webp";
}

server {
    # 基础配置...
    
    # 智能压缩与分发
    location ~* \.(js|css|html)$ {
        gzip_static on;
        brotli_static on;
        expires 1h;
        add_header Cache-Control "public";
    }
    
    # 图片智能分发
    location ~* \.(png|jpg|jpeg)$ {
        try_files $uri$webp_suffix $uri =404;
        expires 1M;
        add_header Cache-Control "public, immutable";
        add_header Vary Accept;
    }
}
```

## 总结

本配置实现了：

- ✅ **智能压缩**：根据客户端支持自动选择最优格式
- ✅ **路径分发**：基于请求路径智能路由到不同服务
- ✅ **缓存优化**：开发环境实时更新，生产环境长期缓存
- ✅ **安全防护**：文件上传限制、管理路径隐藏
- ✅ **性能调优**：连接超时、WebSocket支持等
- ✅ **Docker友好**：容器化部署最佳实践

这种配置方案特别适合前后端分离项目，在保证开发效率的同时提供生产级性能。

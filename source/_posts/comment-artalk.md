---
title: Artalk
date: 2024-09-20 16:48:23
categories: 评论
tags:
#sticky:
#cover:
toc: true
excerpt: "为 Kratos : Rebirth 主题添加 Artalk 评论系统支持"
---

## 官方介绍

一款简洁的自托管评论系统

- 开源仓库: https://github.com/ArtalkJS/Artalk
- 配置文档: https://artalk.js.org/zh/guide/deploy.html

## 配置说明

### 引入的新文件

请将下述文件放置于 source 目录中的合适位置，在此处我们使用的是 `comments` 目录。

```js artalk.js
(() => {
  const loadComments = async () => {
    if (typeof Artalk === "undefined") {
      setTimeout(loadComments, 100);
    } else {
      const container = document.getElementById('artalk-container');
      if (!container) {
        return;
      }
      const path = container.getAttribute("data-path");
      const siteName = document.querySelector('meta[property="og:site_name"]').getAttribute("content");
      Artalk.init({
        el: '#artalk-container',
        pageKey: path,
        server: '<您的 Artalk 服务器地址>',
        site: siteName,
      });
    }
  };

  window.loadComments = loadComments;
  window.addEventListener('pjax:success', () => {
    window.loadComments = loadComments;
  });
})();
```

需要替换的内容：

- `<您的 Artalk 服务器地址>` 需要替换成您的 Artalk 服务器地址，例如 `http://artalk.example.com:8080`。

### 更新的配置项

需要更新 `comments` ，**设置为**以下内容：

```yml
comments:
  core:
    enable_at:
      - post
      - page
    template:
      _shared: <div id="artalk-container" class="kr-comments lazy-load" data-path="$PATH"></div>
```

需要更新 `additional_injections` ，**加入**以下内容：

```yml
additional_injections:
  head: |
    <link href="<您的 Artalk 服务器地址>/dist/Artalk.css" rel="stylesheet" />
  after_footer: |
    <script src="<您的 Artalk 服务器地址>/dist/Artalk.js"></script>
    <script src="/comments/artalk.js"></script>
```

需要替换的内容：

- `<您的 Artalk 服务器地址>` 需要替换成您的 Artalk 服务器地址，例如 `http://artalk.example.com:8080`。

假设您把上述的文件放置在 source 目录的 comments 目录中，且您的站点配置为根目录模式，那么您应当可以通过上述的路径 `/comments/artalk.js` 访问到对应的文件。如果您使用的是其他配置，您需要对应调整这个路径。

当您配置完成后重启 Hexo ，您应当可以看见正在加载的 Artalk 实例。预祝您使用愉快。

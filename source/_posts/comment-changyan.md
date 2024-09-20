---
title: 畅言云评论
date: 2024-09-20 16:48:23
categories: 评论
tags:
#sticky:
#cover:
toc: true
excerpt: "为 Kratos : Rebirth 主题添加 畅言云评论 评论系统支持"
---

## 官方介绍

国内最大，操作简单，完全免费的智能评论管理平台。

- 官方网站: https://changyan.kuaizhan.com/

## 配置说明

### 引入的新文件

请将下述文件放置于 source 目录中的合适位置，在此处我们使用的是 `comments` 目录。

```js changyan.js
(() => {
  const loadComments = async () => {
    const container = document.getElementById('SOHUCS');
    if (container) {
      window.changyan.api.config({
        appid: '<您的 APPID>',
        conf: '<您的 Conf>',
      });

      const d = document, s = d.createElement('script');
      s.src = 'https://changyan.kuaizhan.com/upload/changyan.js';
      s.charset = 'utf-8';
      s.type = 'text/javascript';
      (d.head || d.body).appendChild(s);
    }
  };

  window.loadComments = loadComments;
  window.addEventListener('pjax:success', () => {
    window.loadComments = loadComments;
  });
})();
```

需要替换的内容：

- `<您的 APPID>` 和 `<您的 Conf>` 需要替换成您根据 畅言云评论 官方文档中描述的内容获取的信息。

### 更新的配置项

需要更新 `comments` ，**设置为**以下内容：

```yml
comments:
  core:
    enable_at:
      - post
      - page
    template:
      _shared: <div id="SOHUCS" class="kr-comments lazy-load" sid="$PATH"></div>
```

需要更新 `additional_injections` ，**加入**以下内容：

```yml
additional_injections:
  after_footer: |
    <script src="/comments/changyan.js"></script>
```

假设您把上述的文件放置在 source 目录的 comments 目录中，且您的站点配置为根目录模式，那么您应当可以通过上述的路径 `/comments/changyan.js` 访问到对应的文件。如果您使用的是其他配置，您需要对应调整这个路径。

当您配置完成后重启 Hexo ，您应当可以看见正在加载的 畅言云评论 实例。预祝您使用愉快。

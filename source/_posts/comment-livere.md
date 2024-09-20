---
title: LiveRe
date: 2024-09-20 16:48:23
categories: 评论
tags:
#sticky:
#cover:
toc: true
excerpt: "为 Kratos : Rebirth 主题添加 LiveRe 评论系统支持"
---

{% alertbar warning "

这个文档由 V2 的实现迁移过来，无法保证是否完整正确；如果遇到任何问题，欢迎随时 [开启一个 issue](https://github.com/kratos-rebirth/ecosystem/issues/new) 来跟进。

" %}

## 官方介绍

Comments beyond Smart

- 官方网站: https://livere.com/

## 配置说明

### 引入的新文件

请将下述文件放置于 source 目录中的合适位置，在此处我们使用的是 `comments` 目录。

```js livere.js
(() => {
  const loadComments = async () => {
    const container = document.getElementById('lv-container');
    if (container) {
      const path = container.getAttribute("data-full-path").replace(/\/index\.html$/, "/");
      window.livereOptions = {
        refer: path.replace(/^[a-zA-Z]+?\:\/\//, ""),
        site: path,
        eagerLoading: true,
      };
    } else {
      window.livereOptions = null;
    }
    
    if (typeof LivereTower === "undefined") {
      const d = document, s = d.createElement('script');
      s.src = 'https://cdn-city.livere.com/js/embed.dist.js';
      (d.head || d.body).appendChild(s);
    } else {
      window.LivereTower.start();
    }
  };

  window.loadComments = loadComments;
  window.addEventListener('pjax:success', () => {
    window.loadComments = loadComments;
  });
})();
```

### 更新的配置项

需要更新 `comments` ，**设置为**以下内容：

```yml
comments:
  core:
    enable_at:
      - post
      - page
    template:
      _shared: <div id="lv-container" class="kr-comments lazy-load" data-full-path="$PATH_FULL" data-id="<您的 LiveRe ID>" data-uid="<您的 LiveRe UID>"></div>
```

需要替换的内容：

- `<您的 LiveRe ID>` 和 `<您的 LiveRe UID>` 需要替换成您在 LiveRe 设置的具体信息。

需要更新 `additional_injections` ，**加入**以下内容：

```yml
additional_injections:
  after_footer: |
    <script src="/comments/livere.js"></script>
```

假设您把上述的文件放置在 source 目录的 comments 目录中，且您的站点配置为根目录模式，那么您应当可以通过上述的路径 `/comments/livere.js` 访问到对应的文件。如果您使用的是其他配置，您需要对应调整这个路径。

当您配置完成后重启 Hexo ，您应当可以看见正在加载的 LiveRe 实例。预祝您使用愉快。

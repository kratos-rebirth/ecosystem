---
title: Valine
date: 2024-09-20 16:48:23
categories: 评论
tags:
#sticky:
#cover:
toc: true
excerpt: "为 Kratos : Rebirth 主题添加 Valine 评论系统支持"
---

{% alertbar warning "

这个文档由 V2 的实现迁移过来，无法保证是否完整正确；如果遇到任何问题，欢迎随时 [开启一个 issue](https://github.com/kratos-rebirth/ecosystem/issues/new) 来跟进。

" %}

## 官方介绍

A fast, simple & powerful comment system.

- 开源仓库: https://github.com/xCss/Valine
- 配置文档: https://valine.js.org/configuration.html

## 配置说明

### 引入的新文件

请将下述文件放置于 source 目录中的合适位置，在此处我们使用的是 `comments` 目录。

```js valine.js
(() => {
  const loadComments = async () => {
    if (typeof Valine === "undefined") {
      setTimeout(loadComments, 100);
    } else {
      const container = document.getElementById('v-comments');
      if (!container) {
        return;
      }
      const path = container.getAttribute("data-path");
      new Valine({
        el: '#v-comments',
        appId: '<您的 App ID>',
        appKey: '<您的 App Key>',
        path,
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

- `<您的 App ID>` 和 `<您的 App Key>` 需要替换成您根据 Valine 官方文档中描述的内容创建的 LeanCloud 应用 的信息。

### 更新的配置项

需要更新 `comments` ，**设置为**以下内容：

```yml
comments:
  core:
    enable_at:
      - post
      - page
    template:
      _shared: <div id="v-comments" class="kr-comments lazy-load" data-path="$PATH"></div>
```

需要更新 `additional_injections` ，**加入**以下内容：

```yml
additional_injections:
  after_footer: |
    <script src='//unpkg.com/valine/dist/Valine.min.js'></script>
    <script src="/comments/valine.js"></script>
```

假设您把上述的文件放置在 source 目录的 comments 目录中，且您的站点配置为根目录模式，那么您应当可以通过上述的路径 `/comments/valine.js` 访问到对应的文件。如果您使用的是其他配置，您需要对应调整这个路径。

当您配置完成后重启 Hexo ，您应当可以看见正在加载的 Valine 实例。预祝您使用愉快。

---
title: DisqusJS
date: 2024-09-20 16:48:23
categories: 评论
tags:
#sticky:
#cover:
toc: true
excerpt: "为 Kratos : Rebirth 主题添加 DisqusJS 评论系统支持"
---

{% alertbar warning "

这个文档由 V2 的实现迁移过来，无法保证是否完整正确；如果遇到任何问题，欢迎随时 [开启一个 issue](https://github.com/kratos-rebirth/ecosystem/issues/new) 来跟进。

" %}

## 官方介绍

纯前端、超轻量级的「评论基础模式」实现：使用 Disqus API 渲染评论列表

- 开源仓库: https://github.com/SukkaW/DisqusJS
- 配置文档: https://github.com/SukkaW/DisqusJS?tab=readme-ov-file#%E5%AE%89%E8%A3%85%E5%92%8C%E4%BD%BF%E7%94%A8

## 配置说明

### 引入的新文件

请将下述文件放置于 source 目录中的合适位置，在此处我们使用的是 `comments` 目录。

```js disqusjs.js
(() => {
  let disqusjs = null;
  const loadComments = async () => {
    if (typeof DisqusJS === "undefined") {
      setTimeout(loadComments, 100);
    } else {
      const container = document.getElementById('disqusjs');
      if (!container) {
        return;
      }
      disqusjs = new DisqusJS({
        shortname: '<您的站点实例缩略名>',
        identifier: container.getAttribute("data-path"),
        url: container.getAttribute("data-full-path"),
        apikey: '<您的 Disqus API Key>',
      });
      disqusjs.render(container);
    }
  };

  window.loadComments = loadComments;
  window.addEventListener('pjax:before', () => {
    // 销毁 DisqusJS 实例
    disqusjs.destroy();
  });
  window.addEventListener('pjax:success', () => {
    window.loadComments = loadComments;
  });
})();
```

需要替换的内容：

- `<您的站点实例缩略名>` 需要替换成您在 Disqus 设置的具体的站点实例短ID，例如 `candiblog` 。
- `<您的 Disqus API Key>` 需要替换成您从 Disqus 处申请得到的 API Key。

### 更新的配置项

需要更新 `comments` ，**设置为**以下内容：

```yml
comments:
  core:
    enable_at:
      - post
      - page
    template:
      _shared: <div id="disqusjs" class="kr-comments lazy-load" data-path="$PATH" data-full-path="$PATH_FULL"></div>
```

需要更新 `additional_injections` ，**加入**以下内容：

```yml
additional_injections:
  head: |
    <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/disqusjs@3.0/dist/browser/styles/disqusjs.css">
  after_footer: |
    <script src="https://cdn.jsdelivr.net/npm/disqusjs@3.0/dist/browser/disqusjs.es2015.umd.min.js"></script>
    <script src="/comments/disqusjs.js"></script>
```

假设您把上述的文件放置在 source 目录的 comments 目录中，且您的站点配置为根目录模式，那么您应当可以通过上述的 `/comments/disqusjs.js` 访问到对应的文件。如果您使用的是其他配置，您需要对应调整这个路径。

当您配置完成后重启 Hexo ，您应当可以看见正在加载的 DisqusJS 实例。预祝您使用愉快。

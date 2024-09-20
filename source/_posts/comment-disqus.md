---
title: Disqus
date: 2024-09-20 16:48:23
categories: 评论
tags:
#sticky:
#cover:
toc: true
excerpt: "为 Kratos : Rebirth 主题添加 Disqus 评论系统支持"
---

{% alertbar warning "

这个文档由 V2 的实现迁移过来，无法保证是否完整正确；如果遇到任何问题，欢迎随时 [开启一个 issue](https://github.com/kratos-rebirth/ecosystem/issues/new) 来跟进。

" %}

## 官方介绍

The #1 way to build your audience

- 官方网站: https://disqus.com/

## 配置说明

### 引入的新文件

请将下述文件放置于 source 目录中的合适位置，在此处我们使用的是 `comments` 目录。

```js disqus.js
(() => {
  const loadComments = async () => {
    if (typeof DISQUS === "undefined") {
      setTimeout(loadComments, 100);
    } else {
      const container = document.getElementById('disqus_thread');
      if (!container) {
        return;
      }
      DISQUS.reset({
        reload: true,
        config: function () {
          this.page.url = container.getAttribute("data-full-path");
          this.page.identifier = container.getAttribute("data-path");
        }
      });
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
      _shared: <div id="disqus_thread" class="kr-comments lazy-load" data-path="$PATH" data-full-path="$PATH_FULL"></div>
```

需要更新 `additional_injections` ，**加入**以下内容：

```yml
additional_injections:
  after_footer: |
    <script src="https://<您的站点实例缩略名>.disqus.com/embed.js"></script>
    <script async id="dsq-count-scr" src="https://<您的站点实例缩略名>.disqus.com/count.js"></script>
    <script src="/comments/disqus.js"></script>
```

需要替换的内容：

- `<您的站点实例缩略名>` 需要替换成您在 Disqus 设置的具体的站点实例短ID，例如 `candiblog` 。

假设您把上述的文件放置在 source 目录的 comments 目录中，且您的站点配置为根目录模式，那么您应当可以通过上述的 `/comments/disqus.js` 访问到对应的文件。如果您使用的是其他配置，您需要对应调整这个路径。

当您配置完成后重启 Hexo ，您应当可以看见正在加载的 Disqus 实例。预祝您使用愉快。

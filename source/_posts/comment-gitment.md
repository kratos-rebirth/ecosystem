---
title: Gitment
date: 2024-09-20 16:48:23
categories: 评论
tags:
#sticky:
#cover:
toc: true
excerpt: "为 Kratos : Rebirth 主题添加 Gitment 评论系统支持"
---

{% alertbar warning "

这个文档由 V2 的实现迁移过来，无法保证是否完整正确；如果遇到任何问题，欢迎随时 [开启一个 issue](https://github.com/kratos-rebirth/ecosystem/issues/new) 来跟进。

" %}

## 官方介绍

Gitment is a comment system based on GitHub Issues, which can be used in the frontend without any server-side implementation.

- 开源仓库: https://github.com/imsun/gitment
- 配置文档: https://github.com/imsun/gitment?tab=readme-ov-file#get-started

## 配置说明

### 引入的新文件

请将下述文件放置于 source 目录中的合适位置，在此处我们使用的是 `comments` 目录。

```js gitment.js
(() => {
  const loadComments = async () => {
    if (typeof Gitment === "undefined") {
      setTimeout(loadComments, 100);
    } else {
      const container = document.getElementById('gitment-container');
      if (!container) {
        return;
      }
      const path = container.getAttribute("data-path");
      const gitment = new Gitment({
        id: path,
        owner: '<存储评论使用的 issue 的仓库所属>',
        repo: '<存储评论使用的 issue 的仓库名称>',
        oauth: {
          client_id: '<GitHub Application Client ID>',
          client_secret: '<GitHub Application Client Secret>',
        },
      });

      gitment.render('gitment-container');
    }
  };

  window.loadComments = loadComments;
  window.addEventListener('pjax:success', () => {
    window.loadComments = loadComments;
  });
})();
```

需要替换的内容：

- `<GitHub Application Client ID>` 和 `<GitHub Application Client Secret>` 需要替换成您根据 Gitment 官方文档中描述的内容创建的 GitHub Application 的信息。
- `<存储评论使用的 issue 的仓库名称>` 和 `<存储评论使用的 issue 的仓库所属>` 需要替换成您用于存储评论时需要使用的仓库对应的名称。例如对于 `Candinya/Kratos-Rebirth` 来说，仓库名称是 `Kratos-Rebirth` ，仓库所属是 `Candinya` 。

### 更新的配置项

需要更新 `comments` ，**设置为**以下内容：

```yml
comments:
  core:
    enable_at:
      - post
      - page
    template:
      _shared: <div id="gitment-container" class="kr-comments lazy-load" data-path="$PATH"></div>
```

需要更新 `additional_injections` ，**加入**以下内容：

```yml
additional_injections:
  head: |
    <link rel="stylesheet" href="https://imsun.github.io/gitment/style/default.css">
  after_footer: |
    <script src="https://imsun.github.io/gitment/dist/gitment.browser.js"></script>
    <script src="/comments/gitment.js"></script>
```

假设您把上述的文件放置在 source 目录的 comments 目录中，且您的站点配置为根目录模式，那么您应当可以通过上述的路径 `/comments/gitment.js` 访问到对应的文件。如果您使用的是其他配置，您需要对应调整这个路径。

当您配置完成后重启 Hexo ，您应当可以看见正在加载的 Gitment 实例。预祝您使用愉快。

---
title: Waline
date: 2024-09-20 16:48:23
categories: 评论
tags:
#sticky:
#cover:
toc: true
excerpt: "为 Kratos : Rebirth 主题添加 Waline 评论系统支持"
---

## 官方介绍

一款简洁、安全的评论系统。

- 开源仓库: https://github.com/walinejs/waline
- 配置文档: https://waline.js.org/cookbook/import/project.html

## 配置说明

这里的配置较为进阶，同时处理了访问统计和评论两种功能，如果您只需要其中的一部分内容，可以自行调整精简相关的处理逻辑。

请记得根据您的实际需要调整 Waline 具体的加载配置，例如 服务器 URL 或其他相关参数。

### 引入的新文件

请将下述两个文件放置于 source 目录中的合适位置，在此处我们使用的是 `comments` 目录。

```css waline.css
#w-comments .wl-reaction-list {
    gap: 24px;
}
#w-comments .wl-reaction-img {
    width: 72px;
    height: 72px;
}
#w-comments .wl-reaction-votes {
    font-size: 1em;
}
```

```js waline.js
import { init, commentCount  } from 'https://unpkg.com/@waline/client@v3/dist/waline.js';
import { pageviewCount } from 'https://unpkg.com/@waline/client@v3/dist/pageview.js';

(() => {
  const serverURL = '<这里输入您的服务器 URL>';
  let firstVisit = true;

  const loadComments = async () => {
    const container = document.getElementById('w-comments');
    if (!!container) {
      // 是文章或页面，完整加载 Waline
      const path = container.getAttribute("data-path");
      init({
        el: container,
        path,
        dark: 'html[data-theme="dark"]',
        serverURL,
        pageview: true,
        comment: true,
      });
    } else {
      // 是首页，只展示页面访问和评论数量，不渲染评论区
      pageviewCount({
        serverURL,
        update: false,
      });
      commentCount({
        serverURL,
      });
    }

    if (firstVisit) {
      // 站点的访问统计，仅生效一次
      firstVisit = false;
      pageviewCount({
        serverURL,
        path: "/index.html",
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

- `<这里输入您的服务器 URL>` 需要替换成您实际部署的 Waline 后端服务器的访问链接。

### 更新的配置项

需要更新 `comments` 和 `visit_count` ，**设置为**以下内容：

```yml
comments:
  core:
    enable_at:
      - index
      - post
      - page
    template:
      _shared: ""
      index: ""
      post: <div id="w-comments" class="kr-comments" data-path="$PATH"></div>
      page: <div id="w-comments" class="kr-comments" data-path="$PATH"></div>
  count:
    enable_at:
      - index
      - post
    template:
      _shared: <span data-path="$PATH" class="waline-comment-count"> </span>
      index: ""
      post: ""

visit_count:
  enable_at:
    - index
    - post
  template:
    _shared: <span data-path="$PATH" class="waline-pageview-count"> </span>
    index: ""
    post: ""
```

您可以将站点级别的访问统计添加到页脚小字，就像这样：

```yml
footer:
  components:
    additional:
      - - 您是本站的第 <span data-path="/index.html" class="waline-pageview-count"></span> 位访客
```

需要更新 `additional_injections` ，**加入**以下内容：

```yml
additional_injections:
  head: |
    <link rel="stylesheet" href="https://unpkg.com/@waline/client@v3/dist/waline.css" />
    <link rel="stylesheet" href="/comments/waline.css" />
  after_footer: |
    <script defer type="module" src="/comments/waline.js"></script>
```

假设您把上述的两个文件放置在 source 目录的 comments 目录中，且您的站点配置为根目录模式，那么您应当可以通过上述的两个路径 `/comments/waline.css` 和 `/comments/waline.js` 访问到对应的文件。如果您使用的是其他配置，您需要对应调整这两个路径。

当您配置完成后重启 Hexo ，您应当可以看见正在加载的 Waline 实例。预祝您使用愉快。

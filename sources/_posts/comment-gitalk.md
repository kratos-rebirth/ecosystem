---
title: Gitalk
date: 2024-06-26 00:14:05
categories: 评论
tags:
#sticky:
#cover:
toc: true
excerpt: "为 Kratos : Rebirth 主题添加 Gitalk 评论系统支持"
---

## 官方介绍

Gitalk 是一个基于 GitHub Issue 和 Preact 开发的评论插件。

- 开源仓库: https://github.com/gitalk/gitalk
- 配置文档: https://github.com/gitalk/gitalk/blob/master/readme-cn.md

## 配置说明

### 引入的新文件

请将下述两个文件放置于 source 目录中的合适位置，在此处我们使用的是 `comments` 目录。

```css gitalk.css
.gt-counts,
.gt-header-textarea,
.gt-user-name {
  color: var(--kr-theme-text) !important;
}

.gt-header-textarea,
.gt-header-preview,
.gt-btn-preview,
.gt-comment-content,
.gt-popup {
  background-color: var(--kr-theme-page-bg) !important;
}

.gt-popup {
  border-color: #6190e8 !important;
}
```

```js gitalk.js
(() => {
  const loadComments = async () => {
    if (typeof Gitalk === "undefined") {
      setTimeout(loadComments, 100);
    } else {
      const container = document.getElementById('gitalk-container');
      if (!container) {
        return;
      }
      const path = container.getAttribute("data-path");
      const gitalk = new Gitalk(Object.assign({
          id: path,
          path: path,
      }, {
        clientID: '<GitHub Application Client ID>',
        clientSecret: '<GitHub Application Client Secret>',
        repo: "<存储评论使用的 issue 的仓库名称>",
        owner: "<存储评论使用的 issue 的仓库所属>",
        admin: ["<管理员账户的 GitHub 用户名>"],
        distractionFreeMode: false,
      }));

      gitalk.render('gitalk-container');
    }
  };

  window.loadComments = loadComments;
  window.addEventListener('pjax:success', () => {
    window.loadComments = loadComments;
  });
})();
```

需要替换的内容：

- `<GitHub Application Client ID>` 和 `<GitHub Application Client Secret>` 需要替换成您根据 Gitalk 官方文档中描述的内容创建的 GitHub Application 的信息。
- `<存储评论使用的 issue 的仓库名称>` 和 `<存储评论使用的 issue 的仓库所属>` 需要替换成您用于存储评论时需要使用的仓库对应的名称。例如对于 `Candinya/Kratos-Rebirth` 来说，仓库名称是 `Kratos-Rebirth` ，仓库所属是 `Candinya` 。
- `<管理员账户的 GitHub 用户名>` 需要设置成管理员的 GitHub 用户名，可以添加多个。只有这个列表里的用户可以初始化评论区。

### 更新的配置项

需要更新 `comments` ，**设置为**以下内容：

```yml
comments:
  core:
    enable_at:
      - post
      - page
    template:
      _shared: <div id="gitalk-container" class="kr-comments lazy-load" data-path="$PATH"></div>
```

需要更新 `additional_injections` ，**加入**以下内容：

```yml
additional_injections:
  head: |
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/gitalk/1.8.0/gitalk.min.css" integrity="sha512-0hsIlRjJbiUWaKMhXXNDmjWI2qPvUlhNBLHMhqeF5jIma+bedec27N5FoT2JEeHz5TUmOGCsm1Y89EsX/P0wOg==" crossorigin="anonymous" referrerpolicy="no-referrer" />
    <link rel="stylesheet" href="/comments/gitalk.css" />
  after_footer: |
    <script src="https://cdnjs.cloudflare.com/ajax/libs/gitalk/1.8.0/gitalk.min.js" integrity="sha512-Z6q/CxNjbGeWbPamDjp/I737f5LnynAOjJl85FlT75YW3eWmldnn2wj3nhMVEaqMeatyUmvKyfCaM5TND0yRhQ==" crossorigin="anonymous" referrerpolicy="no-referrer"></script>
    <script src="/comments/gitalk.js"></script>
```

假设您把上述的两个文件放置在 source 目录的 comments 目录中，且您的站点配置为根目录模式，那么您应当可以通过上述的两个路径 `/comments/gitalk.css` 和 `/comments/gitalk.js` 访问到对应的文件。如果您使用的是其他配置，您需要对应调整这两个路径。

当您配置完成后重启 Hexo ，您应当可以看见正在加载的 Gitalk 实例。预祝您使用愉快。

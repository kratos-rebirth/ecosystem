---
title: Mermaid
date: 2024-12-20 11:04:20
categories: 其他
tags:
toc: true
excerpt: "为 Kratos : Rebirth 主题添加 Mermaid 流程图支持"
---

{% alertbar warning "

这个文档由 V2 的实现迁移过来，无法保证是否完整正确；如果遇到任何问题，欢迎随时 [开启一个 issue](https://github.com/kratos-rebirth/ecosystem/issues/new) 来跟进。

" %}

## 配置说明

### 引入的新文件

请将下述文件放置于 source 目录中的合适位置，在此处我们使用的是 `others` 目录。

```js mermaid.js
(() => {
  // 初始化函数
  const init = () => {
    if (typeof mermaid === "undefined") {
      // 重试
      setTimeout(init, 200);
    } else {
      // 开始初始化
      mermaid.initialize({
        theme: "dark", // 这里可以根据自己的需要调整成自己想要的主题
      });
    }
  };

  // 页面更新时的重载函数
  const reload = () => {
    mermaid.init(undefined, ".mermaid");
  };

  // 加载时初始化一次
  init();

  // 在 PJAX 之后重载
  window.addEventListener('pjax:complete', reload);
})();
```

### 更新的配置项

需要更新 `additional_injections` ，**加入**以下内容：

```yml
additional_injections:
  after_footer: |
    <script src='https://unpkg.com/mermaid/dist/mermaid.min.js'></script>
    <script src="/others/mermaid.js"></script>
```

假设您把上述的文件放置在 source 目录的 others 目录中，且您的站点配置为根目录模式，那么您应当可以通过上述的路径 `/others/mermaid.js` 访问到对应的文件。如果您使用的是其他配置，您需要对应调整这个路径。

### 更新站点配置

如果您需要使用代码块模式加载 mermaid ，为避免原始内容被高亮损坏，您可能需要在站点配置中调整以下配置：

```yml
highlight:
  exclude_languages:
    - mermaid
```

需要注意的是，由于 mermaid 会被处理成图块，因而代码块模式下的复制按钮无法获得原始的 mermaid 代码数据。如果您有更好的解决方案，欢迎随时向我们提出一个 PR 。

当您配置完成后重启 Hexo ，您应当可以看见 Mermaid 图块被生成。预祝您使用愉快。

---
title: 用于 Kratos-Rebirth 的独立 LaTeX 数学公式渲染插件
date: 2026-10-26 20:24:24
categories: 其他
tags:
#sticky:
#cover:
toc: false
---

# 用于 Kratos-Rebirth 的独立 LaTeX 数学公式渲染插件

> **项目地址**：[https://github.com/TsumugiWenders/Kratos-Rebirth-Latex-Fix](https://github.com/TsumugiWenders/Kratos-Rebirth-Latex-Fix)
> **在线演示**：[https://blog.nekopara.net/自然科学/latex-test/](https://blog.nekopara.net/自然科学/latex-test/)

## 接入博客工作流

在现有 GitHub Actions 工作流的 steps 下增加：

```yaml
      - name: Checkout LaTeX plugin
        uses: actions/checkout@v4
        with:
          repository: TsumugiWenders/kratos-rebirth-latex-fix
          path: latex-plugin
```

复制完博客自定义文件后、Hexo 构建前增加：

```yaml
      - name: Install standalone LaTeX plugin
        run: node latex-plugin/tools/install.js template
```

继续运行原来的 pnpm clean 和 pnpm build 即可。在本插件目录无需执行 npm install、pnpm install 或预构建。

移除先前添加的 Pandoc 安装步骤和 MathJax 注入脚本。模板自带的 Marked 可以保留，本插件会接管 Markdown 渲染。

## 公式

支持行内 $...$、\(...\)，块级 $$...$$、\[...\]，以及独立行上的 equation、align、alignat、gather 及其星号环境；也支持不带外层定界符的 aligned、alignedat、gathered。矩阵放在数学定界符内。

```latex
$$
\begin{aligned}
\operatorname{Gain}(S,A)
&=H(S)-\sum_{i=1}^{n}\frac{|S_i|}{|S|}H(S_i) \\
&=H(S)-H(S\mid A)
\end{aligned}
$$
```

公式在构建时生成 HTML + MathML，本地 CSS 和字体随网站输出，支持主题 PJAX 切换、明暗主题与宽公式横向滚动。默认非法公式会中止构建并显示文章路径。

可选配置放在博客根 _config.yml：

```yaml
kratos_latex:
  strict: true
  macros:
    '\RR': '\mathbb{R}'
```

## 文件与支持范围

公式支持范围以 KaTeX 为准：https://katex.org/docs/supported.html 。插件替换 Markdown 渲染器，Pandoc 特有语法和配置不会自动继承。保留 Markdown 原有 HTML，原生 HTML 标签里的公式不在解析范围内。

卸载时移除博客里的 scripts/kratos-latex.js 和 kratos-latex-runtime/，再清理重建。

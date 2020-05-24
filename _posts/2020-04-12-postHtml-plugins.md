---
layout: post # 使用的布局（不需要改）
title: PostHtml中的一些比较有用的插件 # 标题
subtitle: #副标题
date: 2020-03-27 # 时间
author: Lauginwing # 作者
# header-img: img/post-html.jpg #这篇文章标题背景图片
catalog: true # 是否归档
tags: #标签
  - 项目构建
---

### 一、PostHtml 的工作流程：

1. PostHtml 将 HTML 文档转化为一课节点树
2. PostHtml 的插件在这棵树上做各种处理，以实现需求
3. PostHtml 将节点树转化回 HTML 文档

> [PostHtml 文档](https://github.com/posthtml/posthtml)

### 二、常用插件

#### posthtml-pug

将 pug 转化成 html

#### posthtml-md

将 md 语法转化为 html 语法

#### posthtml-retext

根据规则转化自然语言（例如特定字符串转成 emoji 表情）

#### posthtml-head-elements

将 JSON 配置生成 head 元素内容

#### posthtml-include

引入 html 片段（实现 html 的模块化）

#### posthtml-modules

同上，而且她能实现 vue 中 slot 的效果

#### posthtml-inline-assets

将外部的 css、js、图片等资源内联进 html

#### posthtml-cache

在某标签的某属性值后增加随机查询参数，如

```html
<script src="script.js"></script>
<!-- 处理后 -->
<script src="script.js?v=93ce_Ltuub"></script>
```

#### posthtml-spaceless

删除指定区域内标签之间的空格

#### posthtml-postcss

使用 postcss 处理 html 内的样式（style 标签和内联样式）

#### posthtml-px2rem

在 html 内使用 px2rem 处理 style 标签和内联样式

#### posthtml-inline-css

可将外部样式或 style 标签内样式弄成内联样式

#### posthtml-collect-inline-styles

可将内联样式提取到 style 标签内

#### posthtml-style-to-file

可将内联样式和 style 标签内样式提取到独立文件内

#### posthtml-color-shorthand-hex-to-six-digit

将缩写的 hex color 转化成 6 个字符的格式

#### posthtml-minifier

压缩

#### htmlnano

同上

#### posthtml-remove-attributes

根据属性名或值去除指定属性

#### posthtml-remove-tags

去除指定名称的标签

#### Posthtml-remove-duplicates

根据标签名删除内容完全一样的标签

#### posthtml-transfomer

1.将标记的引用外部文件的 script 或 link 标签的内容，内联进 html 2.将标记的标签删除 3.将标记的多个引用外部文件的 script 或 link 标签的内容，内联进 html 内的同一标签内

#### posthtml-tidy

整理 html、清理无用的标签

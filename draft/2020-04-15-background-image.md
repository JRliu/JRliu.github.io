---
layout: post # 使用的布局（不需要改）
title: 使用background-image实现一些东西 # 标题
subtitle: #副标题
date: 2020-04-15 # 时间
author: Lauginwing # 作者
# header-img: img/post-html.jpg #这篇文章标题背景图片
catalog: true # 是否归档
tags: #标签
  - css
---

>

### 可以设置多个值，之间用逗号分隔

叠加效果，顺序靠前的在上层

### 使用 radial-gradient 做出镂空效果

### 使用 linear-gradient 做出 border 的效果

### 使用 box-shadow 做出 border 的效果

语法：

```scss
/** x偏移量 | y偏移量 | 阴影模糊半径 | 阴影扩散半径 | 阴影颜色 **/
/* 任意数量的阴影，以逗号分隔 */
box-shadow: inset 2px 2px 2px 1px rgba(0, 0, 0, 0.2), 4px 4px 10px 0 green;
```

<iframe height="300" style="width: 100%;" scrolling="no" title="多边框" src="https://codepen.io/jrliu/embed/jObmLRW?height=265&theme-id=light&default-tab=css,result" frameborder="no" allowtransparency="true" allowfullscreen="true" loading="lazy">
  See the Pen <a href='https://codepen.io/jrliu/pen/jObmLRW'>多边框</a> by lauginwing
  (<a href='https://codepen.io/jrliu'>@jrliu</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

<iframe height="300" style="width: 100%;" scrolling="no" title="镂空效果" src="https://codepen.io/jrliu/embed/jObmGOq?height=265&theme-id=light&default-tab=css,result" frameborder="no" allowtransparency="true" allowfullscreen="true" loading="lazy">
  See the Pen <a href='https://codepen.io/jrliu/pen/jObmGOq'>镂空效果</a> by lauginwing
  (<a href='https://codepen.io/jrliu'>@jrliu</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

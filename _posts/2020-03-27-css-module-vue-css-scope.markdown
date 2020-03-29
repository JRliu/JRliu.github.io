---
layout: post # 使用的布局（不需要改）
title: css module 与 vue 中的 scoped css # 标题
subtitle: #副标题
date: 2020-03-27 # 时间
author: Lauginwing # 作者
header-img: img/post-css-module.jpg #这篇文章标题背景图片
catalog: true # 是否归档
tags: #标签
  - css
---

> 最近在使用 taro 开发多端应用，使用了 css module 处理 css 的作用域。之前一直是使用 vue 开发的，相应的也是使用 vue 内置的 css scoped 处理。下面对两种方式做一个小小的总结~

### 实现原理

#### vue scoped css（vue 项目）

```tsx
// test.vue
<div class='test'>哈哈</div>
<style scoped>
.test {
  color: green;
  /deep/ {
      .box {
          color: red;
      }
  }
}
</style>
```

相当于

```tsx
<style>
.test[data-v-d372c4ba] {
  color: green;
}

.test[data-v-d372c4ba] .box{
  color: red;
}
</style>

<div class='test' data-v-d372c4ba>哈哈</div>
```

其原理是给组件内的所有元素添加同一个全局唯一的属性：data-v-[hash]，组件的 css 内的每个 class 末尾也加上对应的选择器

#### css module（react 项目）

```tsx
// style.module.css
.test {
    color: red;
}

// test.tsx
import style from 'style.module.css';

<div className={style.test}>哈哈</div>

```

相当于

```tsx
<style>
    .style-module__test__af14f {
        color: red;
    }
<style>

<div class="style-module__test__af14f"></div>

```

其原理是给元素设置全局唯一的 class：[fileName]\_\_[className]\_\_[hash]，具体的命名规则可以在 css-loader 的配置中设置

### 如何覆盖子组件的样式？

#### vue scoped css

```scss
// parent.scss
.parent-class::v-deep {
  .child-class {
  }
}

或 .parent-class {
  >>> {
    .child-class {
    }
  }
}

或 .parent-class {
  /deep/ {
    .child-class {
    }
  }
}
```

相当于

```scss
.parent-class[data-v-asdf2] .child-class {
}
```

其原理是，在编译过程中，遇到 ::v-deep、>>>、/deep/ 这三种伪元素或选择器时，会停止在 class 末尾添加‘[data-v-asdf]’，所以这个 class 就能作用到子组件里的元素了

#### css module

由于 css module 处理 css 作用域的方式有所不同，是通过将 class 名称唯一化达到目的的，而我们在写代码时是无法知道编译后的 class 名称的。所以只能通过在子组件的特定元素上添加特有属性等方式，让我们在写父组件的样式时，能定位到子组件的特定元素~，
例如现在要在父组件修改子组件中‘哈哈’的颜色为绿色：

```tsx
// parent.tsx
import style from "parent.module.scss";
import Child from "child.tsx";

<div className={style.box}>
  <Child />
</div>;

// parent.module.scss
.box {
    color: red;

    .text[data-role] {
        color: green;
    }
}

// child.tsx
import style from "child.module.scss";

<div className={style.box}>
    <div className={style.text} data-role>哈哈<div>
</div>;
```

或者在全局作用域下声明 class，前提是子组件‘哈哈’所在的元素的 class 没有使用 css module 的 class：

```scss
// parent.module.scss
:global(.text) {
  color: green;
}

或 :global() {
  .text {
    color: green;
  }
}
```

全局作用域下声明的 class 编译时不会加上 hash。这样就可以覆盖子元素中 class 为 text 的元素了

其实，为了代码的可维护性，除了在“迫不得已”的情况下，我们不应该在父组件的 css 中修改子组件的样式，而是通过传参的方式，将样式传递给子组件使用。另外，在编写一些与业务无关的组件，如 button、popup 等组件时，尽量不要使用 scoped css、css module 等方式管理 css 作用域，而是使用一套严格规范的 class 命名规则来约束，因为这些组件被修改样式的频率会大很多，而且很可能会被抽出来单独一个库。如果使用了 css module 来管理 css 作用域，使用者修改了的样式，很可能在你更新了组件库之后就失效了~

### css module 与 js

使用 css module 时，我们在 js 中引入的，实际上是一个包含着 class 编译前和编译后名称映射的对象，如：

```scss
// test.module.scss
.text {
  color: green;
}
.title {
  font-size: 16px;
}
```

```js
// test.ts
import style from "test.module.scss";
console.log(style);
//输出 {
//     text: 'chiid__text__safsf1',
//     title: 'chiid__title__1wfqwf'
// }
```

此外，我们也可以在 css 中指定要导出的内容。比较常用的就是导出在 scss 中定义的变量：

```scss
// test.module.scss
$text-color: #222;

:export {
  textColor: $text-color;
}
```

```ts
// test.ts
import style from "test.module.scss";

console.log(style.textColor); // 输出 ‘#222’
```

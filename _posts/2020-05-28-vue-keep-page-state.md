---
layout: post # 使用的布局（不需要改）
title: vue单页面应用实现原生app保留页面状态的效果
subtitle: #副标题
date: 2020-05-24 # 时间
author: Lauginwing # 作者
# header-img: img/post-html.jpg #这篇文章标题背景图片
catalog: true # 是否归档
tags: #标签
  - vue
---

在很多场景里，特别是 B 端，越来越多的包壳 spa 开始取代原生 app 的位置。但是，包壳 spa 对比原生 app，体验上还是有很大的差距的，其中很明显的一点是，原生 app 从页面 A 打开新页面 B 后，返回页面 A，页面 A 还是原来的状态；而 vue 的 spa 则会根据路由，重新渲染对应的 A 页面的组件（没有使用`keep-alive`），状态也会丢失，如果使用了`keep-alive`，确实能保存页面状态，但是之后打开新的 A 页面，还是会沿用旧的页面状态，这时我们可以使用`activated`钩子去重置状态，但是工作量会很大。
有没有办法，使得从 A 页面跳转到 B 页面时保存 A 页面的状态，而从 A 页面返回的话，销毁 A 页面的状态呢？

实现方法：

1. 使用 keep-alive 组件；
2. 判断本次路由跳转时前进还是后退，如果是后退的话，销毁页面组件的实例；

第一点，我们只需在 app 根组件，使用`keep-alive`组件包裹`router-view`组件即可

第二点，需要分成两个任务，一个是判断前进还是后退，一个是销毁`keep-alive`组件缓存的组件实例
想要知道当前路由跳转时前进还是后退，我们需要维护一个路由历史。

- 判断前进或后退

```js
import _ from 'lodash'
const afterEnterFn = null
const historyList = []
const mixins = {
    beforeRouteLeave(to, from, next) {
        if (!historyList.length){
            historyList.push(from.fullPath)
        }

        // 前往的页面是否历史中倒数第二个
        const isToLastButOne = _.findLastIndex(historyList, item=> {
                    return item === to.fullPath
                }) ===
                    routerList.length - 2)

        if ((historyList.length > 1 && isToLastButOne) {
            // 判断为后退
            routerList.splice(routerList.length - 1, 1)

            /**
              *销毁当前页面组件实例的缓存
            **/
            destoryCache(this)

        } else{
            // 判断为前进
            afterEnterFn = ()=> {
                // 在下一页面的beforeRouteEnter内再将其加入历史，因为有可能在beforeRouteLeave取消掉导航
                historyList.push(to.fullPath)
            }
        }
    },
    beforeRouteEnter() {
        if (typeof afterEnterFn === 'function') {
            afterEnterFn()
            afterEnterFn = null
        }
    }
}
```

- 实现 destoryCache(销毁当前页面组件实例的缓存)：

```js
function destoryCache(scope) {
  if (!scope.$vnode) {
    return;
  }

  if (
    scope.$vnode.parent &&
    // 父组件(keep-alive组件)实例
    scope.$vnode.parent.componentInstance &&
    scope.$vnode.parent.componentInstance.cache
  ) {
    if (scope.$vnode.componentOptions) {
      // 将源码中获取缓存key的代码复制过来~
      let key =
        scope.$vnode.key == null
          ? scope.$vnode.componentOptions.Ctor.cid +
            (scope.$vnode.componentOptions.tag
              ? `::${scope.$vnode.componentOptions.tag}`
              : "")
          : scope.$vnode.key;

      let cache = scope.$vnode.parent.componentInstance.cache;

      let keys = scope.$vnode.parent.componentInstance.keys;

      if (cache[key]) {
        if (keys.length) {
          let index = keys.indexOf(key);
          if (index > -1) {
            keys.splice(index, 1);
          }
        }
        // 删除缓存的组件实例
        delete cache[key];
      }
    }
  }

  scope.$destroy();
}
```

另外，还需处理手动刷新页面（F5）的情况，内存里的`historyList`会被清空。我们可以在 sectionStorage 里维护`historyList`来解决；
在上面的 mixin 里，我们还可以处理其他的事务，如后退后恢复滚动位置（vue-router 使用 hash 模式时）等。

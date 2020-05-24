---
layout: post # 使用的布局（不需要改）
title: 如何优雅地修改node_module内依赖包的内容
subtitle: #副标题
date: 2020-05-24 # 时间
author: Lauginwing # 作者
# header-img: img/post-html.jpg #这篇文章标题背景图片
catalog: true # 是否归档
tags: #标签
  - 项目构建
---

> 在开发中，我们经常会遇到一种情况，就是安装的依赖包有个地方有个小 bug，也没有足够的时间等作者处理 pr。这时候，一些硬核的朋友会把源码拷贝到开发目录中进行修改;文明一些的朋友会将修改后的代码，改个名字，发布一个新包~ 那么，有没有更优雅的方法处理这种情况呢？

### patch-package 能解决问题

#### 实现过程

如果你修改了库 A，patch-package 可以帮你生成一份补丁。下次再安装库 A 的时候，patch-package 会自动将补丁安装到 node_module 对应的文件内（也可以手动安装补丁）。

#### 使用方法

使用非常简单，主要有以下几点：

- yarn add patch-package
- 在 package.json 的 scripts 中加入 { "postinstall": "patch-package" }，这是 npm 脚本命令的钩子，会在依赖包被 install 之后执行。（npm 还提供 prepublish，postpublish，preinstall 等默认钩子）
- 生成补丁。例如，修改 lodash 库后，执行命令 `yarn patch-package lodash`，默认在项目根目录生成`patches`目录，里面存放的就是‘补丁’

现在，你把`node_module`目录删掉之后，重新安装依赖，你会发现`node_module`内的对应的源码还是你修改后的状态！

此外，使用 `patch-package` 生成补丁时，还可配置 options：

- --include \<regexp> ： 创建补丁时忽略匹配的路径
- --exclude \<regexp> ： 创建补丁时只考虑匹配的路径
- --patch-dir ： 补丁文件存放位置
- --reverse ： 回退所有补丁

直接执行`yarn patch-package`，则会安装所有补丁到 node_module 目录中

#### 补丁原理

![log](/img/in-post/patch-package-process.png)

从生成补丁时的 log 可以看出，主要过程是：在临时文件夹中安装‘干净’的、对应版本的 lodash，然后使用`git diff`对比本地 node_module 中的文件，生成补丁文件
因为补丁是`git diff`，生成的，所以我们也可以使用`git apply`去直接使用补丁~

```
git apply --ignore-whitespace patches/lodash+4.17.15.patch
```

项目地址：[https://github.com/ds300/patch-package#readme](https://github.com/ds300/patch-package#readme)

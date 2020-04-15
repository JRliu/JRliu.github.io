---
layout: post # 使用的布局（不需要改）
title: 写给自己的webpack总结 # 标题
subtitle: #副标题
date: 2020-03-27 # 时间
author: Lauginwing # 作者
# header-img: img/post-css-module.jpg #这篇文章标题背景图片
catalog: true # 是否归档
tags: #标签
  - 构建工具
---

好无聊~，总结一下 webpack 常用的配置吧~

## context [编译的上下文]

基础目录，绝对路径，用于从配置中解析 `entry` 和 `loader`，默认是当前目录，一般的项目很少会改这个配置~

```js
{
  context: path.resolve(__dirname, "app");
}
```

## entry [应用打包的入口]

以打包网页为例，一个 html 一个`entry`~，多个 html 就多个`entry`

多个`entry`的栗子：

```js
{
    entry: {
        home: './home.js',
        about: './about.js',
        multi: ['./multi1.js', './multi2.js'] // 数组内的模块整合成一个文件输出
    }
}
```

## output [输出文件]

栗子

```js
{
    output: {
        path: path.resolve(__dirname, 'dist'),
        filename: '[name].[chunkhash].bundle.js',
        publicPath: 'https://qn.com/haha/',
        // 如果你想打包为库，需要设置以下两项
        library: 'myLibrary',
+       libraryTarget: 'umd'
    }
}
```

#### output.path

设置输出文件的目录，使用绝对路径

#### output.filename

设置输出文件的文件名

#### output.publicPath

设置项目中所有资源（js、css、图片等）的基础路径

## resolve [解析模块]

> 配置模块如何解析。主要就是影响 `import 'some library'` 等操作

#### resolve.alias

创建 import 或 require 的别名，来确保模块引入变得更简单。
引用路径可以使用变量（如环境变量），从而实现，同一个`import`，在不同环境下引入不同的模块。

栗子：

```js
{
  resolve: {
    alias: {
      config: path.resolve(__dirname, `src/config/${process.env.APP_ENV}`);
    }
  }
}
```

在 typescript 中，相当于配置 tsconfig.json 中的 path：

```js
{
    compilerOptions: {
        paths:  {
            "@/*": ["src/*"]
        }
    }
}
```

#### resolve.aliasFields

指定一个字段，例如 browser，如果引入的模块的 package.json 内包含 browser 字段，那么打包时会使用该 browser 字段的值作为该模块的别名。

#### resolve.extensions

”自动解析指定的扩展“。
默认值是`['.wasm', '.mjs', '.js', '.json']`，没有包含`.ts`，所以想要引入 typescript 文件，需要加上`.ts`~

#### resolve.symlinks

是否使用软链接寻找模块。影响 `npm link` 等操作。

## module [处理模块]

> 配置如何处理不同类型的模块

#### module.noParse

webpack 解析时，忽略指定的模块。
如果你能确定一个模块（如 jquery）没有引用其他库，则可以忽略该模块，提高构建性能~

#### module.rules

配置规则数组，实现使用不同的 loader 处理不同类型的模块
一个比较丰满的栗子

```js
{
    module: {
        rules: {
            {
                test: /\.sass$/,
                oneOf: [ // 子规则数组，只使用第一个匹配到的子规则。
                    {
                        resourceQuery: /module/,
                        test: /\.module\.\w+$/,  // 子规则也可设置各种匹配规则
                        use: [ // loader的执行顺序是 ’下 --> 上， 右 --> 左‘
                            {
                                loader: 'vue-style-loader', // 将处理好的css以style标签的形式插入到文档中
                                options: {
                                    sourceMap: false,
                                    shadowMode: false
                                }
                            },
                            {
                                loader: 'css-loader', // 解析、处理 js中引入的css模块
                                options: {
                                    sourceMap: false,
                                    importLoaders: 2,
                                    modules: true,
                                    localIdentName: '[name]_[local]_[hash:base64:5]'
                                }
                            },
                            {
                                loader: 'postcss-loader',  // 使用postcss处理css
                                options: {
                                    sourceMap: false
                                }
                            },
                            {
                                loader: 'sass-loader', // 解析、处理 js中引入的scss模块
                                options: {
                                    sourceMap: false,
                                    indentedSyntax: true
                                }
                            }
                        ]
                    }
                ]
            }
        }
    }
}
```

## externals [外部拓展]

防止将某些 import 的包(package)打包到 bundle 中，而是在运行时(runtime)再去从外部获取这些扩展依赖(external dependencies)。

栗子

```js
{
    externals: {
        jquery: 'jQuery',
        lodash : {
            commonjs: 'lodash',
            amd: 'lodash',
            root: '_' // 指向全局变量
        }
    }
}
```

## plugin [使用插件]

> 使用 webpack 插件~

#### 专有名词：

- compiler: 代表了完整的 webpack 环境配置。这个对象在启动 webpack 时被一次性建立，并配置好所有可操作的设置，包括 options，loader 和 plugin。当在 webpack 环境中应用一个插件时，插件将收到此 compiler 对象的引用。可以使用它来访问 webpack 的主环境。

- compilation: 代表了一次资源版本构建。当运行 webpack 开发环境中间件时，每当检测到一个文件变化，就会创建一个新的 compilation，从而生成一组新的编译资源。一个 compilation 对象表现了当前的模块资源、编译生成资源、变化的文件、以及被跟踪依赖的状态信息。compilation 对象也提供了很多关键时机的回调，以供插件做自定义处理时选择使用。

#### 插件执行的原理：

插件函数的 prototype 上定义的 `apply` 方法接收到参数 -- `compiler` 对象， `compiler.plugin('hook', callback)` 将我们的定义的函数 `callback` 绑定到 webpack 自身的事件钩子 `hook` 上。其中，`callback` 函数能接收到参数 -- `compilation`，我们可以根据`compilation`提供的数据实现我们的需求了~

#### webpack 提供的[所有钩子](https://www.webpackjs.com/api/compiler-hooks/)

#### 常用的插件

- DefinePlugin - 创建一个在编译时可以配置的全局常量。
- HtmlWebpackPlugin - 生成 html，并将打包后的资源插入文档中对应的位置。
- CopyWebpackPlugin - 复制文件~
- HotModuleReplacementPlugin - 模块热替换(HMR)
- CommonsChunkPlugin - 提取公共代码
- DllPlugin - 一般用于将不会改动的代码单独打包成一个动态库
- DllReferencePlugin - 使用 DllPlugin 打包出来的动态库
- CaseSensitivePathsPlugin - 强制校验模块引用路径的大小写~
- ForkTsCheckerWebpackPlugin - TypeScript 类型检查

<hr>
<br>

> 杂项： [webpack-chain 尝试通过提供可链式或顺流式的 API 创建和修改 webpack 配置](https://github.com/Yatoo2018/webpack-chain/tree/zh-cmn-Hans)；

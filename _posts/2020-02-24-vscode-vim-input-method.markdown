---
layout: post # 使用的布局（不需要改）
title: Vscode内的Vim自动切换输入法 # 标题
subtitle: #副标题
date: 2020-02-24 # 时间
author: Lauginwing # 作者
header-img: img/post-vim.jpg #这篇文章标题背景图片
catalog: true # 是否归档
tags: #标签
  - 周边
---

在使用 Vscode 的 Vim 模式写字时，经常会遇到一个问题：在 insert 模式下输入完一段中文后，`Esc` 回到 normal 模式后，想 `J` 切到下一行，却跳出来中文输入法，有点难受。

如果编辑器能记住我 insert 模式下使用的输入法，在退出到 normal 模式时自动切换到英文输入法，再次进入 insert 模式后，自动切换到上次 insert 模式使用的输入法，那就挺不错了。

Vscode 里面的 Vim 插件就有这样的功能：[https://github.com/VSCodeVim/Vim#input-method](https://github.com/VSCodeVim/Vim#input-method)

<!-- <br> -->

- ### 我们首先需要安装 im-select: 这个命令行工具可以获取当前输入法及切换输入法：

```
curl -Ls https://raw.githubusercontent.com/daipeihust/im-select/master/install_mac.sh | sh
```

获取当前输入法：

```
/usr/local/bin/im-select
```

切换输入法：

```
/usr/local/bin/im-select com.apple.keylayout.ABC
```

<br>

- ### 配置 vscode 插件
  ![setting](/img/in-post/vscode-vim-input-method-setting.png)

<br>

- ### 重启编辑器就可以开始敲代码啦~

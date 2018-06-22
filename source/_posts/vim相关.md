---
title: vim相关
date: 2018-06-12 17:38:20
tags: vim
---

# 移动到行首 第一个非空字符

^:移动光标到行首第一个非空字符上去



# 在文中做标记

m[a-z]:在文中做标记，标记号可为a-z的26个字母，用`a可以移动到标记a处



# surround 插件

webStorm 的 vim 添加 surround插件

1. 在 ~/ 下新建 .ideavimrc 文件

2. 进入该文件 ，在文件内添加 `set surround`

3. `source ~/.ideavimrc`

```
 if x*>3 {                 ysiw(        if ( x>3 ) {
```
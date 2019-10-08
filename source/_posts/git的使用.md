---
title: git的使用
date: 2019-10-08 18:08:47
tags:
---
#### 新建文件夹，查看当前路径
```
$ mkdir learngit
$ cd learngit
$ pwd
-----------------------
/Users/michael/learngit
```
#### 初始化-init
```Git
$ git init
-----------------------
Initialized empty Git repository in /Users/michael/learngit/.git/
```
<!--more-->
- 目录默认是隐藏的，用ls -ah命令就可以看见
```Git
$ ls -ah
```
- 创建文件(按esc后键入:wq退出)
```Git
$ vi readme.txt
```
#### 添加文件-add
```Git
$ git add readme.txt
```
```Git
$ git add -A
```
#### 提交文件-commit
```Git
$ git commit -m "wrote a readme file"
------------------------
[master (root-commit) eaadf4e] wrote a readme file
 1 file changed, 2 insertions(+)
 create mode 100644 readme.txt
 ```
 
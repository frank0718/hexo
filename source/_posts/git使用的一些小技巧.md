title: git使用的一些小技巧
date: 2018-11-27 14:58:32
tags:
categories:
description:
---

![](/upload/image/git.png)
### git delete untracked files
`git clean -fdx
-f - force
-d - directories too
-x - remove ignored files too ( don't use this if you don't want to remove ignored files)`

### 修改远端url 
` git remote set-url origin https://username@github.com/sre/abc.git`
 
 
### 误提交了密码到了某个版本 在git库中删除这个版本
回滚到上个个版本
git reset --hard HEAD^
或者某个版本
git reset --hard 3628164 
 
push这个分支 到git server
git push --force
commit就从历史中删除了

###  untrack file 不想trace某个文件了
git rm --cached autologin/auto_login.py
不会删除本地文件。。

### 找回git rm的文件 
git reset HEAD
Unstaged changes after reset:
D	auto_login.py


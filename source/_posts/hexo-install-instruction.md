title: hexo install instruction
date: 2015-12-21 12:02:12
tags: hexo
categories:
description:
---

hexo 搭建博客指引

## (1) 安装npm
由于机器上之前的npm是Ubuntu12.04的源, 版本比较低,装起来会报错,解决方法(卸载npm,添加第三方的源,重新安装npm,nodejs)参考下面链接:

stackoverflow.com/questions/12913141/message-failed-to-fetch-from-registry-while-trying-to-install-any-module

注意npm要换源.
```
vim ~/.npmrc   //打开配置文件
registry =https://registry.npm.taobao.org   //写入配置文件
```

## (2)安装 hexo hexo-cli 
参考
http://wsgzao.github.io/post/hexo-guide/

npm -g install hexo-cli
npm install hexo --save


## (3) 创建博客的目录
```
hexo init  hexo
```
## (4)创建一篇post 

```
cd hexo;
hexo n post "title"  //生成文章
```

## (5) 启动hexo服务器
```
hexo s  // 启动server 本地预览
//访问 127.0.0.1:4000
```

## (6) 生成静态文件.
```
hexo g  
```
通过hexo s预览的时候并不需要这一步,  由于这些静态文件部署到 github , github并不直接支持hexo的方式,github支持直接访问静态html文件,css文件的传统方式. (注意与jekyll的思路不一样)


## (7) 部署

```
hexo d 
```

### (a) 自动部署到git (推荐)
安装hexo的git插件, 参考
http://wsgzao.github.io/post/hexo-guide/

需要注意的是:  deploy的 方式 是git 不是github 
参考的配置方式, 需要修改_config.yml 

```
deploy:
  type: git
  repo: https://github.com/frank0718/frank0718.github.io.git    
##如果是repo: git@github.com:username/username.github.io.git 这样就自动使用ssh协议推送了 .
  branch: master
  message: hexo deploy
```

参考
[https://github.com/hexojs/hexo/issues/1040
https://github.com/hexojs/hexo/issues/1154](http://)

### (b)手动部署到git 
hexo g 之后会生成public 目录, 把public目录手动复制到 user.github.io的repo中, 然后push .直接就可以访问了. 

## (8) 安装主题

安装jacman主题
http://blog.fens.me/hexo-blog-github/  这个有点旧  pacman 已经404 . 
https://github.com/wuchong/jacman
http://wsgzao.github.io/post/hexo-jacman/  
http://wuchong.me/jacman/2014/11/20/how-to-use-jacman/


方法:
### (1)git clone https://github.com/wuchong/jacman.git themes/jacman

### (2)_config.yml
中间修改主题的名字 为jacman

### (3)  hexo clean 删除旧的静态文件
```
root@netease:~/hexo# hexo clean 
INFO  Deleted database.
INFO  Deleted public folder.

```
hexo g;
hexo d;
重新发布 


### (4)主题颜色更改 

在博客的目录下输入下面指令
cd node_modules/hexo-renderer-stylus 
npm install stylus@0.49.2  #根据系统文件的权限不同，有的不需要加sudo
然后更改在主题目录下的_config.yml的 theme_color 下 theme 值。

```
### Theme Color 
theme_color:
    theme: '#2ca6cb'    ##the defaut theme color is blue
```
可以查看rgb的数值选个喜欢的颜色

## (9)添加一些页面 

### 1.命令手动生成自定义页面
```
hexo n page "about"
```
### 2.编辑hexo/source/about/index.md内容
###3.修改themes/jacman/_config.yml文件
```

menu:
  关于: /about

```
##  (10)修改post预设格式

建议按照个人习惯修改hexo\scaffolds中的post.md
```

title: {{ title }}
date: {{ date }}
categories:
description:
```


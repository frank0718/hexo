title: linux lib库介绍
date: 2018-11-29 17:06:26
tags:
categories:
description:
---


# glibc 
	- 是Linux系统中最底层的API 
	- centos6 版本一般是2.12 如果升级需要编译新版本。 
	- 注意glibc是各种命令的依赖库， 如果安装出问题 直接影响命令的使用。 
	
`
	rpm -qa|grep glibc
glibc-headers-2.12-1.166.el6.x86_64
compat-glibc-headers-2.5-46.2.x86_64
glibc-common-2.12-1.166.el6.x86_64
glibc-devel-2.12-1.166.el6.x86_64
compat-glibc-2.5-46.2.x86_64
glibc-2.12-1.166.el6.i686
glibc-2.12-1.166.el6.x86_64
`


# gcc 
	- gcc 是c c++的编译环境
	- centos6 版本一般是4.4.7 
	- 如果升级需要编译新版本或者用rpm安装 
`
rpm -qa|grep gcc
libgcc-4.4.7-16.el6.i686
gcc-c++-4.4.7-16.el6.x86_64
libgcc-4.4.7-16.el6.x86_64
gcc-gfortran-4.4.7-16.el6.x86_64
gcc-4.4.7-16.el6.x86_64
`

# libstdc++
	- libstdc++ 这两个一般是一些bin文件的依赖库
	- gcc + libstdc++  版本是一致的。
`
rpm -qa|grep libstdc++
libstdc++-devel-4.4.7-16.el6.x86_64
libstdc++-4.4.7-16.el6.x86_64
compat-libstdc++-33-3.2.3-69.el6.x86_64
compat-libstdc++-296-2.96-144.el6.i686
`

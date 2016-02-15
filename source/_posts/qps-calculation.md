title: qps calculation
date: 2016-01-13 17:58:44
tags:
categories:
description:
---


# 分享一个计算qps的脚本
## 主要思路就是根据nginx log 来定时取一分钟的log数目.
## 需要注意的是:
* 日志切割的时间点qos,要加上切割之前的log的那分钟的数据,否则是不完整的
* 由于考虑到对机器的压力,每分钟跑一次然后除60得到qps
* 具体如下

```
#!/bin/bash

if [ $# -eq 2 ];then
    LOG=$1
    OLDLOG=$2
else
    LOG="/home/nginx/logs/access.log"
    OLDLOG='/home/nginx/logs/backup/access.log.1'
fi


if [ ! -f $LOG ];then
    echo "log file not exist"
    exit 1
else
   d=`date -d '1 min ago' +%Y:%H:%M`
   #for test
   #d="2016:17:05"
   if [ `head -1 $LOG|grep -c "$d"` -ge 1 ];then
        if [ -f $OLDLOG ];then
            old_count=`grep -c "$d" $OLDLOG`
        else
            old_count=0
        fi
        new_count=`grep -c "$d" $LOG`
        count=$(($new_count + $old_count))
    else
        count=`grep -c "$d" $LOG`
    fi
fi
qps=$(($count / 60))

echo "==qps"
echo "qps=$qps"


```

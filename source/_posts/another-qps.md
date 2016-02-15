title: another qps
date: 2016-01-14 11:01:33
tags:
categories: shell
description:
---


## 另外一个计算qps的脚本
* nginx log在同一个目录里面
* 每个host的log是单独的文件
* oldlog是用logrotate做的,delay compress

### ws.
```
#!/bin/bash

loglist=($(find /var/log/nginx/ -regextype "posix-egrep" -regex ".*-access.log"))
d=`date -d '1 min ago' +%d/%b/%Y:%H:%M`
#for test
#d="2016:06:25"

sum=0
for log in ${loglist[*]} ; do
	if [ `head -1 $log|grep -c "$d"` -eq 1 ];then 
		oldlog=${log}".1"
		if [ -f $oldlog ];then
			count_old=`tail -5000 $oldlog|grep -c "$d"`
		else
			count_old=0
		fi
		count_new=`head -5000 $log|grep -c "$d"`
		count=$(($count_old +$count_new))
	else 
		count=`tail -10000 $log|grep -c "$d"`
	fi
	
	sum=$(($sum + $count))
done
qps=$(($sum / 60))

echo "==ws_qps"
echo "ws_qps=$qps"
```

### another wap
```
#!/bin/bash

loglist=($(find /home/nginx/logs/ -type f -name "*-access.log"))
d=`date -d '1 min ago' +%d/%b/%Y:%H:%M`
#for test
#d="14/Jan/2016:00:05"

sum=0
for log in ${loglist[*]} ; do
        if [ `head -1 $log|grep -c "$d"` -eq 1 ];then
                suffix=`echo -n $log|awk -F '/' '{print $NF}'`
                ye=`date -d '1 day ago' +%Y%m%d`
                oldlog="/home/nginx/logs/backup/""${suffix}-${ye}"
                if [ -f $oldlog ];then
                        count_old=`tail -50000 $oldlog|grep -c "$d"`
                else
                        count_old=0
                fi
                count_new=`head -50000 $log|grep -c "$d"`
                count=$(($count_old +$count_new))
        else
                count=`tail -100000 $log|grep -c "$d"`
        fi

        sum=$(($sum + $count))
done
qps=$(($sum / 60))

echo "==wap_qps"
echo "wap_qps=$qps"
```

title: http code monitor
date: 2016-01-14 18:35:42
tags:
categories: bash
description:
---


## 一个bash 脚本.

* 计算qps
* 用户请求的状态码
* upstream状态码,
* 2xx/3xx的比率
* 需要注意的问题 写在脚本的注释了.

```
#!/bin/bash

if [ $# -eq 2 ]; then
	log=$1
	oldlog=$2
else 
	log="/home/nginx/logs/xxxxx-access.log"
	ye=`date -d '1 day ago' +%Y%m%d`
	oldlog="/home/nginx/logs/backup/xxxx-access.log-$ye"
fi

d=`date -d '1 min ago' +%d/%b/%Y:%H:%M`
#for test
#d="14/Jan/2016:00:05"


if [ `head -1 $log|grep -c "$d"` -eq 1 ];then
	## log rotate的特殊分钟考虑.
	if [ -f $oldlog ];then
		tail -50000 $oldlog > /tmp/3g.log
		head -50000 $log >> /tmp/3g.log	
        ##放在一个文件里，因为放在一个变量里var=`cat file` , var里面没法按行去读
	else 
		head -50000 $log > /tmp/3g.log	
	fi
    ## 主要涉及
    (1)shell 变量传给awk
    (2)awk 正则匹配普通数值或字符串//, 匹配变量
    (3)log中特殊字段的值重置, 比如log的最后一个字段 upstream_cache_status, 可能为"-", 这个数据放在awk里面的数组当做key会报错, 所以把它替换成999, 另外注意不要在END阶段去替换,在key被存入数组之前替换
    (4)log中 NF字段有可能是200"  这样的, 左边少个", 在直接执行eval后面的内容是没有问题,但是和eval一起执行的时候,bash报错,提示少", 所以用正则把^[0-9]开头的替换为000; 保证bash不报错.
    (5) awk变量传给shell, 用eval
    (6) awk if else if else语法;
    (7) awk 数组赋值及取值.
	eval $(tail -100000 /tmp/3g.log|awk -v date="$d" '$4 ~ date{count+=1;status[$9]+=1; if($NF ~ /^[0-9]/) u="000" ; else if($NF ~ /-/) u="999" ;else u=$NF; upstatus[u]+=1}END{printf("count=%d\n", count);for (i in status){printf("code[%s]=%s\n;",i,status[i]);} ; for (j in upstatus){printf("upcode[%s]=%s\n;",j,upstatus[j])}}')
    
    
else 
	## fix a bug 
	
	eval $(tail -100000 /home/nginx/logs/xxxx.log|awk -v date="$d" '$4 ~ date{count+=1;status[$9]+=1; if($NF ~ /^[0-9]/) u="000" ; else if($NF ~ /-/) u="999" ;else u=$NF; upstatus[u]+=1}END{printf("count=%d\n", count);for (i in status){printf("code[%s]=%s\n;",i,status[i]);} ; for (j in upstatus){printf("upcode[%s]=%s\n;",j,upstatus[j])}}')
fi

## 计算qps
echo "==wap_qps"
qps=$(($count/60))
echo "qps=$qps,"

bad=0
baduc=0
echo "==wap_status"

##遍历关联数组
for c in ${!code[*]} ;do
	echo -n "code$c=${code[$c]},"
	if [[ $c =~ '50' ]] || [[ $c =~ '40' ]];then  ##判断5xx 4xx的数量
		bad=$(($bad + ${code[$c]}))
	fi	
done
ok=$(($count - $bad))
ratio=$(echo "scale=5; $ok*100/$count"|bc)  ##计算比率 ,bash $(()) 得不到小数, 注意用bc来计算得到小数,并且可以设置小数的位数
echo 
echo "==ratio"
echo  "ratio=$ratio"

echo "==wap_up_status"
for uc in ${!upcode[*]} ;do 
	echo -n "upcode$uc=${upcode[$uc]},"
	if [[ $uc =~ '50' ]]  ||  [[ $uc =~ '40' ]];then
		baduc=$(($baduc + ${upcode[$uc]}))
	fi
done
ok_uc=$(($count - $baduc))
ratio_uc=$(echo "scale=5; ${ok_uc}*100/$count"|bc )
echo 
echo "==ratio_uc"
echo "ratio_uc=${ratio_uc}"


```

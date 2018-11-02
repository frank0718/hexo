title: rsync by log
date: 2016-03-08 11:24:06
tags: bash rsync
categories:
description:
---



# 一个根据log同步文件的脚本
### 实现增量同步
### 后台运行 

    
![](/upload/image/sync.png)


```
#!/bin/bash

RSYNC_LOCK='/home/sh/pro.lock'
rm -f ${RSYNC_LOCK}

first_time=true
while [ 1 ]
do
echo "===================="
da=`date "+%y/%m/%d %H:%M:%S"`
echo $da

if [ -e ${RSYNC_LOCK} ]; then
    sleep 5
   continue
fi
    touch ${RSYNC_LOCK}
    echo "first_time: "${first_time}

## fisrt run
    if [ ${first_time} == true ];then
        init_php_line=1
        init_ftp_line=1
    else
        init_php_line=$php_last_line
        init_ftp_line=$ftp_last_line
    fi
### log rotated 

    rotate_time=`echo "$da"|awk '{print $2}'|awk -F':' '{print $1$2}'`
    echo ${rotate_time}
    if [[ ${rotate_time} =~ 0002 ]];then
        init_php_line=1
        init_ftp_line=1
    fi 
    echo "php init line number = "$init_php_line
    echo "ftp init line number = "$init_ftp_line 
        
    phplog='/var/log/php_upload.log'
    php_last_line=`wc -l $phplog|awk '{print $1}'`
    echo "php_last_line: ${php_last_line}"
    if [[ $init_php_line -eq $php_last_line ]];then
        echo "-------- no new php log"
        has_new=false
    else 
        php_content=`sed -n "$init_php_line,$ p" $phplog`
        ##echo "php_content : $php_content"
        if [[ -n ${php_content} ]];then
            sed -n "$init_php_line,$ p" $phplog| awk '{print $9}'|sed 's/^\/home1/\./g' > /home/sh/pro.file
            has_new=true
        ## log rotating
        else 
            has_new=false
        fi
    fi
    
    echo "----------"
    ftplog='/var/log/xferlog'
    ftp_last_line=`wc -l $ftplog|awk '{print $1}'`
    echo "ftp_last_line: ${ftp_last_line}"
    if [[ $init_ftp_line -eq $ftp_last_line ]];then
        echo "------- no new ftp log"
    else
        ftp_content=`sed -n "$init_ftp_line,$ p" $ftplog`
        ##echo "ftp_content :  $ftp_content"
        if [[ -n $ftp_content ]];then 
            if [[ $has_new == true ]];then
                echo "phplog has new: $has_new"
                sed -n "$init_ftp_line,$ p" $ftplog |awk '{print $9}'|sed -e 's/^\/home1/\./g' -e 's/^\/data\/home1/\./g' >> /home/sh/pro.file
            else
                echo "phplog has new: $has_new"
                sed -n "$init_ftp_line,$ p" $ftplog |awk '{print $9}'|sed -e 's/^\/home1/\./g' -e 's/^\/data\/home1/\./g' > /home/sh/pro.file
            fi
        fi
    fi

    first_time=false

    if [ -s /home/sh/pro.file ];then
        /usr/bin/rsync -au --timeout=30 --ignore-errors --files-from=/home/sh/pro.file /home1/ 1.1.1.1::pic/ 
        cat /dev/null > /home/sh/pro.file
    fi
    rm -f ${RSYNC_LOCK}
    sleep 5;
done

```

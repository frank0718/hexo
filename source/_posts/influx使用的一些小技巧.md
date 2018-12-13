title: influx使用的一些小技巧
date: 2018-11-27 14:49:59
tags:
categories:
description:
---
    
    
![](/upload/image/influx.png)

### 查询使用正则
`SELECT "filed_key" from "measurement" WHERE field_key =~ /val/
`

### 设置时间格式
`precision rfc3339 
`

### mac上安装
参考： 
https://blog.csdn.net/qq_36004521/article/details/80521683

做几个alias
`alias inf="influxd -config /usr/local/etc/influxdb.conf &"
alias inf="influxd > /dev/null 2>&1 &"
alias infc="influx -precision rfc3339"
`

### 查看retention策略
`SHOW RETENTION POLICIES on dbname
`
设置此策略后 要设置成defalt才生效
比如

`create retention policy "2_hours" on "test" duration 2h replication 1 default
`


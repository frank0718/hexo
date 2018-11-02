title: python 时间str、ts 转换
date: 2018-11-02 12:58:28
tags:
categories:
description:
---
![](/upload/image/time.png)

## 获取当前时间的str 
``` 
datetime.datetime.now().strftime("%Y-%m-%d %H:%M:%S")

```

## 获取当前时间的ts 

```
int(time.mktime(datetime.datetime.now().timetuple())) 
int(time.time())

```

## str -> ts 
```
s = "01/12/2011"
time.mktime(datetime.datetime.strptime(s, "%d/%m/%Y").timetuple())

```


## ts - > str 
ts = 1535624940

## utc 时间 少8个小时。
``` 
datetime.datetime.utcfromtimestamp(ts).strftime('%Y-%m-%d %H:%M:%S')

```
## 非utc时间。用这个 
``` 
datetime.datetime.fromtimestamp(ts).strftime('%Y-%m-%d %H:%M:%S')

```



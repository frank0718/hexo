title: python log analyze
date: 2016-02-01 20:05:48
tags:
categories: python log 
description:
---



# nginx log url统计

* python tailf 每条nginx的log, 把url存放在redis,设置过期时间,后台运行
* 每条key ,放在一个redis的list中, 保证list中每个item是唯一的
* 设置这个list的过期时间是一天
* 每分钟从这个list中遍历元素,即是redis的set,每次取set的value放在python的dict中
* 遍历python的dict,然后print 访问量前n的数据

set key的代码
python insert_redis.py > /dev/null 2>&1  & 

```
#!/usr/bin/python

import sys,os
import redis
import time
import subprocess
import select

r = redis.Redis(host='localhost',port=6379,db=0)

def insert_redis(insert_key,expire):
    url_list = r.lrange('url',0,-1)
    #print url_list
    k = 'URL_' + str(insert_key)
    k = k.strip('\n')
    if k in url_list:
        print "{0} in ".format(k)
        r.incr(k)
    else:
        print "{0} not in".format(k)
        r.setex(k,1,expire)
        r.lpush('url',k)
    times = r.get(k)

    print "========================"

     
filename = '/home/nginx/logs/access.log'
f = subprocess.Popen(['tail','-F',filename],\
        stdout=subprocess.PIPE,stderr=subprocess.PIPE)
p = select.poll()
p.register(f.stdout)

while True:
    if p.poll(1):
        line = f.stdout.readline()
	insert_key = line.split()[6]
	print insert_key
	insert_redis(insert_key,86400)
    time.sleep(0.2)


```


get key的代码
python pyredis.py

```

#!/usr/bin/python
import sys,os
import redis

#print r.get('URL:nc.apk')
#r.lrange('url',0,3) return times 
#print r.lrange('abc',0,-1)
## if not exist touch 'abc

def get_redis():
    url_list = r.lrange('url',0,-1)
    print url_list
    for k in url_list:
    	times = r.get(k)
    	print times
        if times == None:
            continue
        else:
    	    url_dict[k] = int(times)
def set_expire():
    import datetime 
    date = datetime.datetime.now()
    hm= date.strftime("%H:%M")
    print hm
    if hm == '00:00':
        r.expire('url',86400)

def sort_by_key_top(n):
    top = 1
    for key,value in sorted(url_dict.iteritems(), key=lambda d:d[1], reverse = True):
        url_dict.setdefault(key,0)
        if top == n :
    	    print key + "=" + str(value) + ',',
            break
         
        print key + "=" + str(value) + ',',
        top += 1

if __name__ == '__main__':
    r = redis.Redis(host='localhost',port=6379,db=0)
    url_dict = {}
    get_redis()
    sort_by_key_top(10)
    set_expire()

```



title: nginx 499 very short request time
date: 2017-04-19 22:37:37
tags:
categories:
description:
---

```
业务调用的超时时间是5s， inrouter单个proxy请求是5s， u1花了5s，没拿到结果，inrouter主动断开链接了，u1记录499 ； 开始重试u2 ，由于业务超时是5s，所以inrouter主动断开这个链接，u2记录499 ，而且这个时间很短。  这就解释了 为啥有时候499的时候，request_time 几乎都是0.001 s 
可以在u1 u2上设置 请求uu读取超时是2.5s，这样前端inrouter的5s超时就有用了，最差情况请求u1 2.5s超时，再请求u2 2.5 超时，但是不至于只给u1 0.001s的机会
```

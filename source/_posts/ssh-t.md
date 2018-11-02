title: ssh -t 
date: 2018-11-02 11:47:45
tags: ssh 
categories: Linux
description: 登录到机器上之后自动执行命令
---

![](/upload/image/ssh.png)
##  man ssh 
```
-t   Force pseudo-terminal allocation.  This can be used to execute arbitrary screen-based programs on a remote machine, which can be very useful, e.g. when implementing menu services.  Multiple -t options force tty allocation, even if ssh has no local tty.```

 

## login
```
ssh -t host 'sudo -iu user'```


登录机器之后自动切换到特权用户。省去来回切换的问题。
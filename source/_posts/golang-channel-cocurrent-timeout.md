title: golang-channel-cocurrent-timeout
date: 2017-04-14 10:11:39
tags:
categories:
description:
---

```
whole timeout 
package main

import (
	"fmt"
	"time"
)

func Afunction(ch chan int, i int) {
	// time.Sleep(2 * time.Second)
	fmt.Println(i)
	ch <- 1
}

func main() {
	var (
		ch        chan int = make(chan int, 20) //可以同时运行的routine数量为20
		dutycount int      = 200
	)
	for i := 0; i < dutycount; i++ {
		go Afunction(ch, i)
	}

	done := make(chan bool, 1)

	// 知道了任务总量，可以像这样利用固定循环次数的循环检测所有的routine是否工作完毕
	// 必须用goroutine 不然此步for是阻塞的
	go func() {
		for i := 0; i < dutycount; i++ {
			<-ch
		}

		// fmt.Println(len(ch))
		if len(ch) == 0 {
			done <- true
		}
	}()

	select {
	case <-done:
		fmt.Println("done")
	case <-time.After(1 * time.Second):
		// Timed out after 1 seconds!
		fmt.Print("timeout")
	}

}
```


``` 
single timeout and whole timeout 
tckage main

import (
	"fmt"
	"time"
)

func Afunction(ch chan int, i int) {
	// time.Sleep(2 * time.Second)
	subdone := make(chan bool, 1)

	go func() {
		time.Sleep(1 * time.Second)

		fmt.Println(i)
		ch <- 1
		subdone <- true
	}()

	select {
	case <-subdone:
		fmt.Println("subdone")
	case <-time.After(2 * time.Second):
		// Timed out after 1 seconds!
		fmt.Println("sub timeout")
	}
}

func main() {
	var (
		ch        chan int = make(chan int, 20) //可以同时运行的routine数量为20
		dutycount int      = 20
	)
	for i := 0; i < dutycount; i++ {
		go Afunction(ch, i)
	}

	done := make(chan bool, 1)

	// 知道了任务总量，可以像这样利用固定循环次数的循环检测所有的routine是否工作完毕
	// 必须用goroutine 不然此步for是阻塞的
	go func() {
		for i := 0; i < dutycount; i++ {
			<-ch
		}

		// fmt.Println(len(ch))
		if len(ch) == 0 {
			done <- true
		}
	}()

	select {
	case <-done:
		fmt.Println("done")
	case <-time.After(3 * time.Second):
		// Timed out after 1 seconds!
		fmt.Println("timeout")
	}

}

```

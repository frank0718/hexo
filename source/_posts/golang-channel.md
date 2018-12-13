title: golang channel
date: 2018-12-13 14:41:39
tags:
categories:
description:
---

## channel


### 1 

`
package main

import "fmt"

var quit chan int // 只开一个信道

func foo(id int) {
	fmt.Println(id)
	quit <- 0 // ok, finished
}

func main() {
	count := 1000
	quit = make(chan int) // 无缓冲
	for i := 0; i < count; i++ {
		go foo(i)
	}

	for i := 0; i < count; i++ {
		<-quit
	}
}
`


### 2 
`
package main

import "fmt"

var quit chan int // 只开一个信道

func foo(id int) {
	fmt.Println(id)
	quit <- 0 // ok, finished
}

func main() {
	count := 1000
	quit = make(chan int, count) // 无缓冲

	for i := 0; i < count; i++ {
		go foo(i)
	}

	for i := 0; i < count; i++ {
		<-quit
	}
}

`


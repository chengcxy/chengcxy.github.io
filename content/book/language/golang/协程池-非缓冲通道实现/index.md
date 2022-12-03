
---
weight: 3
title: "协程池-非缓冲通道实现"
---


```go
package main

import (
	"log"
	"os"
	"sync"
)

var logger *log.Logger

func init() {
	logger = log.New(os.Stdout, "task:workerpool-nocache ", 0)
	logger.SetFlags(log.LstdFlags | log.Lshortfile)
	logger.Printf("init")
}

func worker(wid int, jobs chan int, wg *sync.WaitGroup) {
	defer wg.Done()
	for job := range jobs {
		logger.Printf("worker:%d,job:%d \n", wid, job)
	}
}

func genTasks() chan int {
	jobs := make(chan int)
	go func() {
		chunks := 100
		for i := 0; i < chunks; i++ {
			jobs <- i
		}
		close(jobs)
	}()
	return jobs
}

func main() {
	jobs := genTasks()
	workers := 5
	wg := sync.WaitGroup{}
	wg.Add(workers)
	for i := 0; i < workers; i++ {
		go worker(i, jobs, &wg)
	}
	wg.Wait()
}

```

## 输出结果

```
task:workerpool-nocache 2022/12/03 14:53:00 wp_no_cache.go:14: init
task:workerpool-nocache 2022/12/03 14:53:00 wp_no_cache.go:20: worker:0,job:0 
task:workerpool-nocache 2022/12/03 14:53:00 wp_no_cache.go:20: worker:2,job:3 
task:workerpool-nocache 2022/12/03 14:53:00 wp_no_cache.go:20: worker:2,job:6 
task:workerpool-nocache 2022/12/03 14:53:00 wp_no_cache.go:20: worker:2,job:7 
task:workerpool-nocache 2022/12/03 14:53:00 wp_no_cache.go:20: worker:2,job:8 
task:workerpool-nocache 2022/12/03 14:53:00 wp_no_cache.go:20: worker:2,job:9 
task:workerpool-nocache 2022/12/03 14:53:00 wp_no_cache.go:20: worker:4,job:1 
task:workerpool-nocache 2022/12/03 14:53:00 wp_no_cache.go:20: worker:2,job:10 
task:workerpool-nocache 2022/12/03 14:53:00 wp_no_cache.go:20: worker:1,job:4 
task:workerpool-nocache 2022/12/03 14:53:00 wp_no_cache.go:20: worker:4,job:11 
task:workerpool-nocache 2022/12/03 14:53:00 wp_no_cache.go:20: worker:2,job:12 
task:workerpool-nocache 2022/12/03 14:53:00 wp_no_cache.go:20: worker:0,job:5 
task:workerpool-nocache 2022/12/03 14:53:00 wp_no_cache.go:20: worker:3,job:2 
task:workerpool-nocache 2022/12/03 14:53:00 wp_no_cache.go:20: worker:4,job:14 
task:workerpool-nocache 2022/12/03 14:53:00 wp_no_cache.go:20: worker:0,job:15 
task:workerpool-nocache 2022/12/03 14:53:00 wp_no_cache.go:20: worker:0,job:19 
task:workerpool-nocache 2022/12/03 14:53:00 wp_no_cache.go:20: worker:0,job:20 
task:workerpool-nocache 2022/12/03 14:53:00 wp_no_cache.go:20: worker:0,job:21 
task:workerpool-nocache 2022/12/03 14:53:00 wp_no_cache.go:20: worker:1,job:13 
task:workerpool-nocache 2022/12/03 14:53:00 wp_no_cache.go:20: worker:1,job:23 
task:workerpool-nocache 2022/12/03 14:53:00 wp_no_cache.go:20: worker:3,job:16 
task:workerpool-nocache 2022/12/03 14:53:00 wp_no_cache.go:20: worker:4,job:17 
task:workerpool-nocache 2022/12/03 14:53:00 wp_no_cache.go:20: worker:4,job:26 
task:workerpool-nocache 2022/12/03 14:53:00 wp_no_cache.go:20: worker:1,job:24 
task:workerpool-nocache 2022/12/03 14:53:00 wp_no_cache.go:20: worker:1,job:28 
task:workerpool-nocache 2022/12/03 14:53:00 wp_no_cache.go:20: worker:4,job:27 
task:workerpool-nocache 2022/12/03 14:53:00 wp_no_cache.go:20: worker:4,job:30 
task:workerpool-nocache 2022/12/03 14:53:00 wp_no_cache.go:20: worker:4,job:31 
task:workerpool-nocache 2022/12/03 14:53:00 wp_no_cache.go:20: worker:4,job:32 
task:workerpool-nocache 2022/12/03 14:53:00 wp_no_cache.go:20: worker:4,job:33 
task:workerpool-nocache 2022/12/03 14:53:00 wp_no_cache.go:20: worker:4,job:34 
task:workerpool-nocache 2022/12/03 14:53:00 wp_no_cache.go:20: worker:4,job:35 
task:workerpool-nocache 2022/12/03 14:53:00 wp_no_cache.go:20: worker:4,job:36 
task:workerpool-nocache 2022/12/03 14:53:00 wp_no_cache.go:20: worker:4,job:37 
task:workerpool-nocache 2022/12/03 14:53:00 wp_no_cache.go:20: worker:0,job:22 
task:workerpool-nocache 2022/12/03 14:53:00 wp_no_cache.go:20: worker:4,job:38 
task:workerpool-nocache 2022/12/03 14:53:00 wp_no_cache.go:20: worker:4,job:40 
task:workerpool-nocache 2022/12/03 14:53:00 wp_no_cache.go:20: worker:0,job:39 
task:workerpool-nocache 2022/12/03 14:53:00 wp_no_cache.go:20: worker:4,job:41 
task:workerpool-nocache 2022/12/03 14:53:00 wp_no_cache.go:20: worker:4,job:43 
task:workerpool-nocache 2022/12/03 14:53:00 wp_no_cache.go:20: worker:1,job:29 
task:workerpool-nocache 2022/12/03 14:53:00 wp_no_cache.go:20: worker:4,job:44 
task:workerpool-nocache 2022/12/03 14:53:00 wp_no_cache.go:20: worker:0,job:42 
task:workerpool-nocache 2022/12/03 14:53:00 wp_no_cache.go:20: worker:0,job:47 
task:workerpool-nocache 2022/12/03 14:53:00 wp_no_cache.go:20: worker:4,job:46 
task:workerpool-nocache 2022/12/03 14:53:00 wp_no_cache.go:20: worker:4,job:49 
task:workerpool-nocache 2022/12/03 14:53:00 wp_no_cache.go:20: worker:4,job:50 
task:workerpool-nocache 2022/12/03 14:53:00 wp_no_cache.go:20: worker:4,job:51 
task:workerpool-nocache 2022/12/03 14:53:00 wp_no_cache.go:20: worker:2,job:18 
task:workerpool-nocache 2022/12/03 14:53:00 wp_no_cache.go:20: worker:4,job:52 
task:workerpool-nocache 2022/12/03 14:53:00 wp_no_cache.go:20: worker:2,job:53 
task:workerpool-nocache 2022/12/03 14:53:00 wp_no_cache.go:20: worker:2,job:55 
task:workerpool-nocache 2022/12/03 14:53:00 wp_no_cache.go:20: worker:2,job:56 
task:workerpool-nocache 2022/12/03 14:53:00 wp_no_cache.go:20: worker:0,job:48 
task:workerpool-nocache 2022/12/03 14:53:00 wp_no_cache.go:20: worker:0,job:58 
task:workerpool-nocache 2022/12/03 14:53:00 wp_no_cache.go:20: worker:2,job:57 
task:workerpool-nocache 2022/12/03 14:53:00 wp_no_cache.go:20: worker:0,job:59 
task:workerpool-nocache 2022/12/03 14:53:00 wp_no_cache.go:20: worker:0,job:61 
task:workerpool-nocache 2022/12/03 14:53:00 wp_no_cache.go:20: worker:0,job:62 
task:workerpool-nocache 2022/12/03 14:53:00 wp_no_cache.go:20: worker:0,job:63 
task:workerpool-nocache 2022/12/03 14:53:00 wp_no_cache.go:20: worker:2,job:60 
task:workerpool-nocache 2022/12/03 14:53:00 wp_no_cache.go:20: worker:2,job:65 
task:workerpool-nocache 2022/12/03 14:53:00 wp_no_cache.go:20: worker:3,job:25 
task:workerpool-nocache 2022/12/03 14:53:00 wp_no_cache.go:20: worker:2,job:66 
task:workerpool-nocache 2022/12/03 14:53:00 wp_no_cache.go:20: worker:1,job:45 
task:workerpool-nocache 2022/12/03 14:53:00 wp_no_cache.go:20: worker:1,job:69 
task:workerpool-nocache 2022/12/03 14:53:00 wp_no_cache.go:20: worker:4,job:54 
task:workerpool-nocache 2022/12/03 14:53:00 wp_no_cache.go:20: worker:1,job:70 
task:workerpool-nocache 2022/12/03 14:53:00 wp_no_cache.go:20: worker:1,job:72 
task:workerpool-nocache 2022/12/03 14:53:00 wp_no_cache.go:20: worker:0,job:64 
task:workerpool-nocache 2022/12/03 14:53:00 wp_no_cache.go:20: worker:3,job:67 
task:workerpool-nocache 2022/12/03 14:53:00 wp_no_cache.go:20: worker:3,job:75 
task:workerpool-nocache 2022/12/03 14:53:00 wp_no_cache.go:20: worker:3,job:76 
task:workerpool-nocache 2022/12/03 14:53:00 wp_no_cache.go:20: worker:3,job:77 
task:workerpool-nocache 2022/12/03 14:53:00 wp_no_cache.go:20: worker:3,job:78 
task:workerpool-nocache 2022/12/03 14:53:00 wp_no_cache.go:20: worker:3,job:79 
task:workerpool-nocache 2022/12/03 14:53:00 wp_no_cache.go:20: worker:4,job:71 
task:workerpool-nocache 2022/12/03 14:53:00 wp_no_cache.go:20: worker:3,job:80 
task:workerpool-nocache 2022/12/03 14:53:00 wp_no_cache.go:20: worker:3,job:82 
task:workerpool-nocache 2022/12/03 14:53:00 wp_no_cache.go:20: worker:0,job:74 
task:workerpool-nocache 2022/12/03 14:53:00 wp_no_cache.go:20: worker:3,job:83 
task:workerpool-nocache 2022/12/03 14:53:00 wp_no_cache.go:20: worker:3,job:85 
task:workerpool-nocache 2022/12/03 14:53:00 wp_no_cache.go:20: worker:3,job:86 
task:workerpool-nocache 2022/12/03 14:53:00 wp_no_cache.go:20: worker:3,job:87 
task:workerpool-nocache 2022/12/03 14:53:00 wp_no_cache.go:20: worker:3,job:88 
task:workerpool-nocache 2022/12/03 14:53:00 wp_no_cache.go:20: worker:4,job:81 
task:workerpool-nocache 2022/12/03 14:53:00 wp_no_cache.go:20: worker:4,job:90 
task:workerpool-nocache 2022/12/03 14:53:00 wp_no_cache.go:20: worker:4,job:91 
task:workerpool-nocache 2022/12/03 14:53:00 wp_no_cache.go:20: worker:0,job:84 
task:workerpool-nocache 2022/12/03 14:53:00 wp_no_cache.go:20: worker:0,job:93 
task:workerpool-nocache 2022/12/03 14:53:00 wp_no_cache.go:20: worker:0,job:94 
task:workerpool-nocache 2022/12/03 14:53:00 wp_no_cache.go:20: worker:0,job:95 
task:workerpool-nocache 2022/12/03 14:53:00 wp_no_cache.go:20: worker:2,job:68 
task:workerpool-nocache 2022/12/03 14:53:00 wp_no_cache.go:20: worker:1,job:73 
task:workerpool-nocache 2022/12/03 14:53:00 wp_no_cache.go:20: worker:1,job:98 
task:workerpool-nocache 2022/12/03 14:53:00 wp_no_cache.go:20: worker:3,job:89 
task:workerpool-nocache 2022/12/03 14:53:00 wp_no_cache.go:20: worker:1,job:99 
task:workerpool-nocache 2022/12/03 14:53:00 wp_no_cache.go:20: worker:4,job:92 
task:workerpool-nocache 2022/12/03 14:53:00 wp_no_cache.go:20: worker:2,job:97 
task:workerpool-nocache 2022/12/03 14:53:00 wp_no_cache.go:20: worker:0,job:96 

```
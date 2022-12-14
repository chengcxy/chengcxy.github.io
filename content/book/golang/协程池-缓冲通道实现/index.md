
---
weight: 2
title: "协程池-缓冲通道实现"
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
	logger = log.New(os.Stdout, "task:workerpool ", 0)
	logger.SetFlags(log.LstdFlags | log.Lshortfile)
	logger.Printf("init")
}

func worker(wid int, jobs chan int, wg *sync.WaitGroup) {
	defer wg.Done()
	for job := range jobs {
		logger.Printf("worker:%d,job:%d \n", wid, job)
	}
}
func genTasks(chunks int) chan int {
	jobs := make(chan int, chunks)
	for i := 0; i < chunks; i++ {
		jobs <- i
	}
	close(jobs)
	return jobs
}

func main() {
	chunks := 100
	jobs := genTasks(chunks)
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
task:workerpool 2022/12/03 14:59:46 batch.go:14: init
task:workerpool 2022/12/03 14:59:46 batch.go:20: worker:4,job:1 
task:workerpool 2022/12/03 14:59:46 batch.go:20: worker:4,job:4 
task:workerpool 2022/12/03 14:59:46 batch.go:20: worker:3,job:3 
task:workerpool 2022/12/03 14:59:46 batch.go:20: worker:3,job:6 
task:workerpool 2022/12/03 14:59:46 batch.go:20: worker:3,job:7 
task:workerpool 2022/12/03 14:59:46 batch.go:20: worker:3,job:8 
task:workerpool 2022/12/03 14:59:46 batch.go:20: worker:3,job:9 
task:workerpool 2022/12/03 14:59:46 batch.go:20: worker:3,job:10 
task:workerpool 2022/12/03 14:59:46 batch.go:20: worker:3,job:11 
task:workerpool 2022/12/03 14:59:46 batch.go:20: worker:2,job:2 
task:workerpool 2022/12/03 14:59:46 batch.go:20: worker:2,job:13 
task:workerpool 2022/12/03 14:59:46 batch.go:20: worker:2,job:14 
task:workerpool 2022/12/03 14:59:46 batch.go:20: worker:2,job:15 
task:workerpool 2022/12/03 14:59:46 batch.go:20: worker:2,job:16 
task:workerpool 2022/12/03 14:59:46 batch.go:20: worker:2,job:18 
task:workerpool 2022/12/03 14:59:46 batch.go:20: worker:4,job:5 
task:workerpool 2022/12/03 14:59:46 batch.go:20: worker:1,job:17 
task:workerpool 2022/12/03 14:59:46 batch.go:20: worker:3,job:12 
task:workerpool 2022/12/03 14:59:46 batch.go:20: worker:3,job:22 
task:workerpool 2022/12/03 14:59:46 batch.go:20: worker:3,job:23 
task:workerpool 2022/12/03 14:59:46 batch.go:20: worker:3,job:24 
task:workerpool 2022/12/03 14:59:46 batch.go:20: worker:1,job:21 
task:workerpool 2022/12/03 14:59:46 batch.go:20: worker:1,job:26 
task:workerpool 2022/12/03 14:59:46 batch.go:20: worker:1,job:27 
task:workerpool 2022/12/03 14:59:46 batch.go:20: worker:1,job:28 
task:workerpool 2022/12/03 14:59:46 batch.go:20: worker:2,job:19 
task:workerpool 2022/12/03 14:59:46 batch.go:20: worker:1,job:29 
task:workerpool 2022/12/03 14:59:46 batch.go:20: worker:1,job:31 
task:workerpool 2022/12/03 14:59:46 batch.go:20: worker:2,job:30 
task:workerpool 2022/12/03 14:59:46 batch.go:20: worker:1,job:32 
task:workerpool 2022/12/03 14:59:46 batch.go:20: worker:1,job:34 
task:workerpool 2022/12/03 14:59:46 batch.go:20: worker:1,job:35 
task:workerpool 2022/12/03 14:59:46 batch.go:20: worker:1,job:36 
task:workerpool 2022/12/03 14:59:46 batch.go:20: worker:1,job:37 
task:workerpool 2022/12/03 14:59:46 batch.go:20: worker:1,job:38 
task:workerpool 2022/12/03 14:59:46 batch.go:20: worker:1,job:39 
task:workerpool 2022/12/03 14:59:46 batch.go:20: worker:1,job:40 
task:workerpool 2022/12/03 14:59:46 batch.go:20: worker:1,job:41 
task:workerpool 2022/12/03 14:59:46 batch.go:20: worker:1,job:42 
task:workerpool 2022/12/03 14:59:46 batch.go:20: worker:1,job:43 
task:workerpool 2022/12/03 14:59:46 batch.go:20: worker:1,job:44 
task:workerpool 2022/12/03 14:59:46 batch.go:20: worker:1,job:45 
task:workerpool 2022/12/03 14:59:46 batch.go:20: worker:1,job:46 
task:workerpool 2022/12/03 14:59:46 batch.go:20: worker:1,job:47 
task:workerpool 2022/12/03 14:59:46 batch.go:20: worker:1,job:48 
task:workerpool 2022/12/03 14:59:46 batch.go:20: worker:1,job:49 
task:workerpool 2022/12/03 14:59:46 batch.go:20: worker:1,job:50 
task:workerpool 2022/12/03 14:59:46 batch.go:20: worker:1,job:51 
task:workerpool 2022/12/03 14:59:46 batch.go:20: worker:1,job:52 
task:workerpool 2022/12/03 14:59:46 batch.go:20: worker:1,job:53 
task:workerpool 2022/12/03 14:59:46 batch.go:20: worker:1,job:54 
task:workerpool 2022/12/03 14:59:46 batch.go:20: worker:4,job:20 
task:workerpool 2022/12/03 14:59:46 batch.go:20: worker:1,job:55 
task:workerpool 2022/12/03 14:59:46 batch.go:20: worker:1,job:57 
task:workerpool 2022/12/03 14:59:46 batch.go:20: worker:1,job:58 
task:workerpool 2022/12/03 14:59:46 batch.go:20: worker:4,job:56 
task:workerpool 2022/12/03 14:59:46 batch.go:20: worker:1,job:59 
task:workerpool 2022/12/03 14:59:46 batch.go:20: worker:4,job:60 
task:workerpool 2022/12/03 14:59:46 batch.go:20: worker:1,job:61 
task:workerpool 2022/12/03 14:59:46 batch.go:20: worker:4,job:62 
task:workerpool 2022/12/03 14:59:46 batch.go:20: worker:1,job:63 
task:workerpool 2022/12/03 14:59:46 batch.go:20: worker:1,job:65 
task:workerpool 2022/12/03 14:59:46 batch.go:20: worker:1,job:66 
task:workerpool 2022/12/03 14:59:46 batch.go:20: worker:1,job:67 
task:workerpool 2022/12/03 14:59:46 batch.go:20: worker:3,job:25 
task:workerpool 2022/12/03 14:59:46 batch.go:20: worker:1,job:68 
task:workerpool 2022/12/03 14:59:46 batch.go:20: worker:3,job:69 
task:workerpool 2022/12/03 14:59:46 batch.go:20: worker:1,job:70 
task:workerpool 2022/12/03 14:59:46 batch.go:20: worker:1,job:72 
task:workerpool 2022/12/03 14:59:46 batch.go:20: worker:1,job:73 
task:workerpool 2022/12/03 14:59:46 batch.go:20: worker:1,job:74 
task:workerpool 2022/12/03 14:59:46 batch.go:20: worker:1,job:75 
task:workerpool 2022/12/03 14:59:46 batch.go:20: worker:1,job:76 
task:workerpool 2022/12/03 14:59:46 batch.go:20: worker:1,job:77 
task:workerpool 2022/12/03 14:59:46 batch.go:20: worker:1,job:78 
task:workerpool 2022/12/03 14:59:46 batch.go:20: worker:1,job:79 
task:workerpool 2022/12/03 14:59:46 batch.go:20: worker:4,job:64 
task:workerpool 2022/12/03 14:59:46 batch.go:20: worker:4,job:81 
task:workerpool 2022/12/03 14:59:46 batch.go:20: worker:1,job:80 
task:workerpool 2022/12/03 14:59:46 batch.go:20: worker:3,job:71 
task:workerpool 2022/12/03 14:59:46 batch.go:20: worker:1,job:83 
task:workerpool 2022/12/03 14:59:46 batch.go:20: worker:3,job:84 
task:workerpool 2022/12/03 14:59:46 batch.go:20: worker:1,job:85 
task:workerpool 2022/12/03 14:59:46 batch.go:20: worker:3,job:86 
task:workerpool 2022/12/03 14:59:46 batch.go:20: worker:3,job:88 
task:workerpool 2022/12/03 14:59:46 batch.go:20: worker:3,job:89 
task:workerpool 2022/12/03 14:59:46 batch.go:20: worker:3,job:90 
task:workerpool 2022/12/03 14:59:46 batch.go:20: worker:3,job:91 
task:workerpool 2022/12/03 14:59:46 batch.go:20: worker:3,job:92 
task:workerpool 2022/12/03 14:59:46 batch.go:20: worker:3,job:93 
task:workerpool 2022/12/03 14:59:46 batch.go:20: worker:3,job:94 
task:workerpool 2022/12/03 14:59:46 batch.go:20: worker:1,job:87 
task:workerpool 2022/12/03 14:59:46 batch.go:20: worker:3,job:95 
task:workerpool 2022/12/03 14:59:46 batch.go:20: worker:3,job:97 
task:workerpool 2022/12/03 14:59:46 batch.go:20: worker:3,job:98 
task:workerpool 2022/12/03 14:59:46 batch.go:20: worker:3,job:99 
task:workerpool 2022/12/03 14:59:46 batch.go:20: worker:1,job:96 
task:workerpool 2022/12/03 14:59:46 batch.go:20: worker:4,job:82 
task:workerpool 2022/12/03 14:59:46 batch.go:20: worker:2,job:33 
task:workerpool 2022/12/03 14:59:46 batch.go:20: worker:0,job:0  
```
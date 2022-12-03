---
title: "协程池 非缓冲通道收集任务结果"
date: 2022-12-03T23:03:26+08:00
---

```go
package main

import (
	logger "fmt"
)

type Task struct {
	Id int
}

type Result struct {
	task *Task
	wid  int
}

type Wresult struct {
	sum   int
	tasks int
}

func worker(wid int, tasks chan *Task, results chan *Result, dones chan int) {
	defer func() {
		dones <- 1
	}()
	for task := range tasks {
		r := &Result{
			wid:  wid,
			task: task,
		}
		results <- r
	}

}

func genTasks(total int) chan *Task {
	tasks := make(chan *Task)
	go func() {
		defer close(tasks)
		for i := 0; i < total; i++ {
			t := &Task{
				Id: i,
			}
			tasks <- t
		}
	}()
	return tasks
}

//var logger *log.Logger
//
//func init() {
//	logger = log.New(os.Stdout, "", 0)
//	logger.SetFlags(log.LstdFlags | log.Lshortfile)
//	logger.Printf("init")
//}

func main() {
	total := 100
	worker_num := 10
	dones := make(chan int, worker_num)
	tasks := genTasks(total)
	results := make(chan *Result)
	for i := 0; i < worker_num; i++ {
		go worker(i, tasks, results, dones)
	}
	go func() {
		for i := 0; i < worker_num; i++ {
			<-dones
		}
		close(results)
	}()
	static := make(map[int]*Wresult, 0)
	for r := range results {
		if _, ok := static[r.wid]; ok {
			static[r.wid].sum += r.task.Id
			static[r.wid].tasks += 1
		} else {
			static[r.wid] = &Wresult{
				sum:   r.task.Id,
				tasks: 1,
			}
		}
		logger.Println("result is ", r.wid, r.task.Id)
	}
	totals := 0
	ltasks := 0
	for wid, wresult := range static {
		logger.Printf("wid %d execute tasks:%d result %d \n ", wid, wresult.tasks, wresult.sum)
		totals += wresult.sum
		ltasks += wresult.tasks
	}
	logger.Printf("totals is %d,ltasks:%d ", totals, ltasks)

}

```


## 运行结果
```
...
...
wid 8 execute tasks:11 result 556 
wid 2 execute tasks:11 result 523 
wid 1 execute tasks:11 result 550 
wid 5 execute tasks:10 result 512 
wid 4 execute tasks:11 result 499 
wid 7 execute tasks:9 result 450 
wid 6 execute tasks:10 result 524 
wid 0 execute tasks:9 result 449 
wid 3 execute tasks:9 result 442 
wid 9 execute tasks:9 result 445 
totals is 4950,ltasks:100 

```
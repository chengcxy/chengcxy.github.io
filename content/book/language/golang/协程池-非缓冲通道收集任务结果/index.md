---
title: "协程池 非缓冲通道收集任务结果"
date: 2022-12-03T23:03:26+08:00
---

```go
package main

import "fmt"

type Task struct {
	Id int
}

type Result struct {
	task *Task
	wid  int
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

func main() {
	total := 100
	worker_num := 10
	dones := make(chan int, worker_num)
	tasks := genTasks(total)
	results := make(chan *Result)
	//开了10个线程 需要堵塞
	for i := 0; i < worker_num; i++ {
		go worker(i, tasks, results, dones)
	}
	go func() {
		for i := 0; i < worker_num; i++ {
			<-dones
		}
		close(results)
	}()
	for r := range results {
		fmt.Println("result is ", r.wid, r.task.Id)
	}

}

```
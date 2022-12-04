
---
weight: 3
title: "http大文件并发下载"
---

## 代码

```go
package main

import (
	logger "fmt"
	"io"
	"math"
	"net/http"
	"os"
	"strconv"
)

type Params struct {
	id       int
	start    int64
	end      int64
	url      string
	fileName string
}

func (p *Params) Download() (io.ReadCloser, error) {
	request, err := http.NewRequest("GET", p.url, nil)
	if err != nil {
		return nil, err
	}
	request.Header.Set(
		"Range",
		"bytes="+strconv.FormatInt(p.start, 10)+"-"+strconv.FormatInt(p.end, 10),
	)

	resp, err := http.DefaultClient.Do(request)
	if err != nil {
		return nil, err
	}
	return resp.Body, nil

}

type Result struct {
	param *Params
	wid   int
	name  string
}

type Wresult struct {
	tasks int
	names []string
}

func worker(wid int, params chan *Params, results chan *Result, dones chan int) {
	defer func() {
		dones <- 1
	}()
	for param := range params {
		body, err := param.Download()
		if err == nil {
			name := logger.Sprintf("%s.%d", param.fileName, param.id)
			fs, _ := os.Create(name)
			defer fs.Close()
			io.Copy(fs, body)
			r := &Result{
				wid:   wid,
				param: param,
				name:  name,
			}
			results <- r

		}

	}

}

func genParams(total int64, batch int64, url, fileName string) chan *Params {
	params := make(chan *Params)
	go func() {
		defer close(params)
		n := 0
		var start int64
		for start < total {
			end := start + batch
			if end >= total {
				end = total + 1
			}
			p := &Params{
				id:       n,
				start:    start,
				end:      end,
				url:      url,
				fileName: fileName,
			}
			n += 1
			start = end
			params <- p
		}
	}()
	return params
}


func main() {
	url := "https://oss.aliyun.com/xx/base_crawl_data_202212040200.gz"
	fileName := "/data/base_crawl_data_202212040200.gz"
	resp, _ := http.Get(url)
	total := resp.ContentLength
	batch := int64(10 * 1024 * 1024)
	totalParams := int(math.Ceil(float64(total) / float64(batch)))
	logger.Printf("totalParams:%d ", totalParams)
	logger.Println("total is ", total)
	worker_num := 20
	dones := make(chan int, worker_num)
	params := genParams(total, batch, url, fileName)
	results := make(chan *Result)
	for i := 0; i < worker_num; i++ {
		go worker(i, params, results, dones)
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
			static[r.wid].names = append(static[r.wid].names, r.name)
			static[r.wid].tasks += 1
		} else {
			names := make([]string, 0)
			names = append(names, r.name)
			static[r.wid] = &Wresult{
				names: names,
				tasks: 1,
			}
		}
	}
	names := make([]string, 0)
	ltasks := 0
	for wid, wresult := range static {
		logger.Printf("wid %d execute tasks:%d names %d \n ", wid, wresult.tasks, len(wresult.names))
		names = append(names, wresult.names...)
		ltasks += wresult.tasks
	}
	logger.Printf("names is %d,total-tasks:%d ", len(names), ltasks)

}

```

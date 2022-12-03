---
weight: 2
title: "io.ReadFull"
---

# io.ReadFull

ReadFull 读取指定长度的内容，如果长度不足返回错误，普通的 Read 会读取到最后一个字节，返回长度，而不是返回错误。

```go
package main

import (
	"fmt"
	"io"
	"log"
	"strings"
)

func main() {
	r := strings.NewReader("some io.Reader stream to be read\n")

	buf := make([]byte, 4)
	if _, err := io.ReadFull(r, buf); err != nil {
		log.Fatal(err)
	}
	fmt.Printf("%s\n", buf)

	// minimal read size bigger than io.Reader stream
	longBuf := make([]byte, 64)
	if _, err := io.ReadFull(r, longBuf); err != nil {
		fmt.Println("error:", err)
	}

}
```

输出

```
some
error: unexpected EOF
```

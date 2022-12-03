---
weight: 1
title: "[]byte操作"
---

# []byte操作

## 赋值给自己

并不会产生新的拷贝，只是把内存中的指针做了调整

```go
buf := []byte("01234567890123456789")
t.Logf("buf pointer %p\n", buf)
buf = buf[10:]
t.Logf("buf pointer %p\n", buf)
```

output:

```go
buf pointer 0x1400001c0c0
buf pointer 0x1400001c0ca
```
## 反复 make 自己
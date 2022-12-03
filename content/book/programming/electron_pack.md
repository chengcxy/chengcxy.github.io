---
title: "用electron打包react和go"
---
# 用electron打包react和go

## 结构

```
frontend/
backend/
window/
scripts/
└── gen.sh
```

frontend - 前端 react 代码

backend - 后端 go 代码

window - electron 代码

scripts/gen.sh 打包脚本

## 准备工作

frontend/package.json

```
 "scripts": {
    "start": "react-scripts start",
    "build": "BUILD_PATH='../bin/frontend' react-scripts build",
    "test": "react-scripts test",
    "eject": "react-scripts eject"
  },
```

BUILD_PATH='../bin/frontend' 指定 yarn build 指令输出到指定目录。

# 脚本

gen.sh

```bash
#!/bin/bash

echo "清理上一次打包的内容"
rm -rf ../../bin
mkdir ../../bin

cd ../../backend
echo "编译 backend mac"
go build -o ../bin/backend

echo "编译 backend win"
GOOS=windows GOARCH=amd64 go build -o ../bin/backend.exe

echo "编译 前端"
cd ../frontend
yarn build

rm -rf ../../window/src/bin
mv ../../bin ../../window/src
cd ../../window
yarn dist

```

bin 目录存放编译好的执行文件，编译完成后统一 mv 到electron的 window 下。

## electron 启动 go

安装 freeport
```
yarn add freeport
```

window/src/main.js 启动go部分代码

```js

const freeport = require("freeport");
const { execFile } = require("child_process");

freeport(function (err, port) {
    backendPort = port;
    let backendBin = "./chimp3_backend";
    if (process.platform == "darwin") {
      backendBin = "./chimp3_backend";
    } else {
      backendBin = "./chimp3_backend.exe";
    }

    try {
      const child = execFile(backendBin, ["--port", port], {
        cwd: __dirname + "/bin",
        env: {PATH: process.env.PATH },
      });
      let url = "http://127.0.0.1:" + port + "/app";

      child.stdout.on("data", (data) => {
        if (win != null) {
          return;
        }
    }
}
```

通过 freeport 查找一个没有被占用的端口。
通过 child_process 传参给 backend 启动，backend 内部启动http服务与React通讯。

注意事项：`env: {PATH: process.env.PATH }`这行如果不传递，backend无法拿到PATH环境变量，会产生很多莫名其妙新问题。

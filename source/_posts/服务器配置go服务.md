---
title: 服务器配置go服务
date: 2023-01-03 00:36:40
tags:
---

# 宝塔面板傻瓜式配置 go 环境

1. 下载

```shell
cd /www/server && wget -O golang.tar.gz wget -O golang.tar.gz https://dl.google.com/go/go1.18.3.linux-amd64.tar.gz
```

2. 解压

```
tar -xzvf golang.tar.gz
```

3. 添加环境变量

```
vim /etc/profile
```

这里附带 vim 的使用：
① 按一下字母「i」就可以进入「插入模式（Insert mode）」
② 输入执行`: wq`存盘并退出

在 profile 最底部添加：

```
export GOROOT=/www/server/go

export GOBIN=$GOROOT/bin

export GOPKG=$GOROOT/pkg/tool/linux_amd64

export GOARCH=amd64

export GOOS=linux

export GOPATH=/www/wwwroot/Golang

export PATH=$PATH:$GOBIN:$GOPKG:$GOPATH/bin
```

执行环境文件，使环境变量生效

```
source /etc/profile
```

4. 测试

```
go version
```

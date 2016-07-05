---
layout: post
title:  "在多台服务器上并行执行脚本"
date:   2016-08-08 13:00:00 +0800
categories: Command
---
工作中经常碰到要在多台服务器上查日志，下面这个脚本可以同时在多台服务器上并发地执行命令。

```
#!/bin/bash
servers="server1 server2 server3"
for server in $servers; do
    ssh $server "grep pattern logfiles" &
done
wait
```

---
layout: post
title:  "批量杀进程的脚本"
date:   2016-07-14 16:10:00 +0800
categories: Command
---
```
ps -ef | grep something_to_find | grep -v grep | cut -c 9-15 | xargs kill -9
```

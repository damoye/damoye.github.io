---
layout: post
title:  "Redis EVALSHA's best practice"
date:   2017-04-14 12:30:00 +0800
categories: Redis
---
If an EVAL is performed against a Redis instance all the subsequent EVALSHA calls will succeed.
So the best practice of EVALSHA command of Redis is:

1. Try EVALSHA
2. If EVALSHA failed, do EVAL

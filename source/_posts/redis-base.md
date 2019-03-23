---
title: redis 基础
date: 2018-12-22 23:27:22
tags:
- redis
categories:
- redis
---

## redis数据结构

1. STRING
2. LIST
3. SET
4. HASH
5. ZSET

<!-- more -->

### STRING
GET
SET
DEL

### LIST
LPUSH RPUSH
LPOP RPOP
LRANGE
LINDEX

### SET
SADD
SREM
SISMEMBER
SEMBERS

### HASH
HSET
HGET
HGETALL
HDEL

### ZSET
ZADD
ZRANGE
ZRANGEBYSCORE
ZREM

### redis命令
### 字符串
INCR key
DECR key
INCRBY key int
DECRBY key int
INCRBYFLOAT key float

APPEND key value
GETRANGE key start end
SETRANGE key offset value
---
published: true
date: 2017-08-27T08:32:31.000Z
---
更新时如何保持redis和DB数据一致。先更新redis，再更新DB Or 先更新DB，再更新redis；写读redis Or 先读DB

## 先读redis再db
redis和MySQL数据的同步，代码级别大致可以这样做：
读: 读redis->没有，读mysql->把mysql数据写回redis
写: 写mysql->成功，写redis

**并发不高的情况：**
读: 读redis->没有，读mysql->把mysql数据写回redis，有的话直接从redis中取；
写: 写mysql->成功，再写redis；

**并发高的情况：**
读: 读redis->没有，读mysql->把mysql数据写回redis，有的话直接从redis中取；
写：异步话，先写入redis的缓存，就直接返回；定期或特定动作将数据保存到mysql，可以做到多次更新，一次保存；

**问题：** 更新数据时，先清理redis key，再更新DB。清理redis和更新DB直接，读请求还是会读到老的数据，造成缓存失效。怎么破？直接更新redis再同步DB？

## 一致性要求高的以DB数据为准
不要求强一致实时性的读请求，都由redis处理
要求强一致实时性的读请求，由数据库处理
写请求有2种处理方式，由数据库处理
* 应用先写到数据库，然后更新redis
* 应用先写到数据库，然后其它daemon同步到redis

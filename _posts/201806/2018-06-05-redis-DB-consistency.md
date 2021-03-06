---
layout: post
title: redis/DB读写一致性
categories: [redis]
---
# Redis/DB读写一致性
大的原则是
* 强一致性以读写DB为准
* redis缓存要有过期时间，这样即使有脏数据也会被清理掉

## 读数据
先读redis，redis中没有读DB并更新Redis

## 写数据/更新数据
更新时如何保持redis和DB数据一致。
* 先更新redis，再更新DB
* 先更新DB，再更新redis
* 先更新DB，再删除Redis key
* 先删除Redis Key，再更新DB

### 先更新redis，再更新DB
多线程场景下同时有请求A和请求B进行更新操作，那么会出现
（1）线程A更新了redis
（2）线程B更新了redis
（3）线程B更新了DB
（4）线程A更新了DB
这样造成DB/Redis数据不一致。

### 先更新DB，再更新redis
多线程场景下同时有请求A和请求B进行更新操作，那么会出现
（1）线程A更新了数据库
（2）线程B更新了数据库
（3）线程B更新了redis
（4）线程A更新了redis
这就出现请求A更新缓存应该比请求B更新缓存早才对，但是因为网络等原因，B却比A更早更新了缓存。这就导致了脏数据。

### 先更新DB，再删除Redis
假设这会有两个请求，一个请求A做查询操作，一个请求B做更新操作，那么会有如下情形产生
（1）缓存刚好失效
（2）请求A查询数据库，得一个旧值
（3）请求B将新值写入数据库
（4）请求B删除缓存
（5）请求A将查到的旧值写入缓存
如果发生上述情况，确实是会发生脏数据。发生上述情况有一个条件，就是步骤（3）的写数据库操作比步骤（2）的读数据库操作耗时更短，才有可能使得步骤（4）先于步骤（5）。一般来说写DB比读DB慢，所以可能性比较小。如果一定要解决可以采用延时删除策略。

### 先删除Redis，再更新DB
假设有两个请求，一个请求A进行更新操作，另一个请求B进行查询操作。
（1）请求A进行写操作，删除缓存
（2）请求B查询发现缓存不存在
（3）请求B去数据库查询得到旧值
（4）请求B将旧值写入缓存
（5）请求A将新值写入数据库
这样就会导致redis/db不一致。

假设有两个请求，一个请求A进行更新操作，另一个请求B进行查询操作。
（1）请求A进行写操作，删除缓存
（2）请求A将数据写入数据库了，
（3）请求B查询缓存发现，缓存没有值
（4）请求B去从库查询，这时，还没有完成主从同步，因此查询到的是旧值
（5）请求B将旧值写入缓存
（6）数据库完成主从同步，从库变为新值
这样就会导致redis/db不一致。

解决方案是采用延时双删策略。这里的sleep延时时间是上面步骤请求B的消耗时间+一个阀值。

```java
public void write(String key,Object data){
        redis.delKey(key);
        db.updateData(data);
        Thread.sleep(1000);
        redis.delKey(key);
    }
```
注意：
* 考虑写操作延时可能有性能问题，可以把删除作为异步操作（比如通过消息系统），同时保证删除一定要成功（重试）
* 考虑到先删除Redis，再更新DB后再次删除Redis的双删除方案。可以直接采用更新DB，再删除Redis方案。

## 参考
http://www.cnblogs.com/rjzheng/p/9041659.html
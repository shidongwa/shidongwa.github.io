---
title: 构建高性能Web站点读后
categories: [iteye, 高性能]
layout: post
excerpt_separator: <!--more-->
---
构建高性能Web站点读后<!--more-->

- 数据库层主要查询建索引,一个是全表扫描，一个是常量时间。通过explain 查询语句就知道了   
- 通过memcached分布式缓存减少数据库或者磁盘文件IO,原则是把数据缓存到离使用者最近的地方   
- 写操作也可以缓存，如果不要求实时更新到数据库的话。比如把某个页面的点击次数存储到数据库中的话，并不要求每点击一次就写一次数据库，可以积累到10次或者100次的时候再做一次更新。这对用户体验影响不大。（只要不直接读数据库这条记录，也就是要通过缓存的接口取值）   
- Web站点重定向实现的负载均衡Load Balance
镜像站点通过Http Redirect 302 和 Location header头镜像到各个子站点（用户所在地区的镜像，CDN）。通过重定向到子站点，可以很大程度扩充主Web站点的吞吐量（reqeust/second）     
选择子站点有两种方式：     
     - Round Robin，顺序选择。可以实现绝对的均衡，但需要记住最后一次分配的站点，代价比较高，需要考虑并发互斥存储该变量。     
     - 随机选择，hash当前时间，hash IP都是不错的选择。   
- DNS实现的负载均衡,同一个域名配多个IP（注意DNS配置中的A记录和CNAME记录）
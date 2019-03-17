---
layout: post
title: Java Agent
categories: [JVM Instrument]
---
Java Agent机制

## 两个线程

- Signal Dispatcher(随jvm启动而启动)
- Attach Listener, 这个线程通过Unix Domain Socket跟外部进程通讯（.java_pid<pid>）

> On Linux and Solaris, the client creates a file named .attach_pid<pid> and sends a SIGQUIT to the target JVM process. The existence of this file causes the SIGQUIT handler in HotSpot to start the attach listener thread.


## 两个文件

- .attach_pid<pid> （启动attach listener线程用）
- .java_pid<pid> （Unix socket domain）



## 参考

[LinuxVirtualMachine](https://github.com/frohoff/jdk8u-dev-jdk/blob/master/src/solaris/classes/sun/tools/attach/LinuxVirtualMachine.java)
[你假笨@JVM](http://lovestblog.cn/blog/2014/06/18/jvm-attach/)
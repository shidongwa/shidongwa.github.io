---
layout: post
title: spring boot auto configuration and starter
categories: [spring boot, starter, auto configuration]
---
# spring boot auto configuration

## auto configure

* 创建configuration
* condition on class
* condition on missing bean
* new bean in spring context

## spring.factory loader

扫描resources/META-INF/spring.factories下的auto configure

## enableXXX

也可以不通过spring factory loader，直接应用enableXXX
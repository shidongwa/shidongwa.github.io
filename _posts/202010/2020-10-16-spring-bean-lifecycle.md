---
layout: post
title: spring bean初始化顺序
categories: [spring]
---
spring bean初始化顺序（不包括BeanFactory的创建/属性注入准备）

Constructor（构造函数） > @PostConstruct > InitializingBean > init-method

代码验证如下。主要注意的是@PostConstruct执行的InitDestroyAnnotationBeanPostProcessor的postProcessBeforeInitialization
```java
public class InitSequenceBean implements InitializingBean {  

    public InitSequenceBean() {  
       System.out.println("InitSequenceBean: constructor");  
    }  

    @PostConstruct  
    public void postConstruct() {  
       System.out.println("InitSequenceBean: postConstruct");  
    }  

    public void initMethod() {  
       System.out.println("InitSequenceBean: init-method");  
    }  

    @Override  
    public void afterPropertiesSet() throws Exception {  
       System.out.println("InitSequenceBean: afterPropertiesSet");  
    }  
}  
```


## 参考
- [github](https://github.com/shidongwa/spring-demo.git)
- [参考](https://programmer.help/blogs/after-properties-set-and-init-method-postconstruct-of-spring-initializing-bean.html)
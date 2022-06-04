---
title: Spring Boot 整合 Elasticsearch 客户端出现异常 NoSuchFieldError： IGNORE_DEPRECATIONS，解决方案
date: 2020-10-22
tags: Java,springboot,Spring,Linux,Bigdata,NoSQL,Elasticsearch
---


# Spring Boot 整合 Elasticsearch 客户端出现异常 NoSuchFieldError: IGNORE_DEPRECATIONS，解决方案 

### 启动的时候爆出异常信息：

```
org.springframework.beans.factory.BeanCreationException: Error creating bean with name 'restHighLevelClient' defined in com.example.demo.MyHighLevelEsRestDemoApplication: Bean instantiation via factory method failed; nested exception is org.springframework.beans.BeanInstantiationException: Failed to instantiate [org.elasticsearch.client.RestHighLevelClient]: Factory method 'restHighLevelClient' threw exception; nested exception is java.lang.NoSuchFieldError: IGNORE_DEPRECATIONS
    at org.springframework.beans.factory.support.ConstructorResolver.instantiate(ConstructorResolver.java:655) ~[spring-beans-5.2.8.RELEASE.jar:5.2.8.RELEASE]
    at org.springframework.beans.factory.support.ConstructorResolver.instantiateUsingFactoryMethod(ConstructorResolver.java:483) ~[spring-beans-5.2.8.RELEASE.jar:5.2.8.RELEASE]
    at org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory.instantiateUsingFactoryMethod(AbstractAutowireCapableBeanFactory.java:1336) ~[spring-beans-5.2.8.RELEASE.jar:5.2.8.RELEASE]
    at org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory.createBeanInstance(AbstractAutowireCapableBeanFactory.java:1176) ~[spring-beans-5.2.8.RELEASE.jar:5.2.8.RELEASE]
    at org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory.doCreateBean(AbstractAutowireCapableBeanFactory.java:556) ~[spring-beans-5.2.8.RELEASE.jar:5.2.8.RELEASE]
    at org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory.createBean(AbstractAutowireCapableBeanFactory.java:516) ~[spring-beans-5.2.8.RELEASE.jar:5.2.8.RELEASE]
    at org.springframework.beans.factory.support.AbstractBeanFactory.lambda$doGetBean$0(AbstractBeanFactory.java:324) ~[spring-beans-5.2.8.RELEASE.jar:5.2.8.RELEASE]
    at org.springframework.beans.factory.support.DefaultSingletonBeanRegistry.getSingleton(DefaultSingletonBeanRegistry.java:226) ~[spring-beans-5.2.8.RELEASE.jar:5.2.8.RELEASE]
    at org.springframework.beans.factory.support.AbstractBeanFactory.doGetBean(AbstractBeanFactory.java:322) ~[spring-beans-5.2.8.RELEASE.jar:5.2.8.RELEASE]
    at org.springframework.beans.factory.support.AbstractBeanFactory.getBean(AbstractBeanFactory.java:202) ~[spring-beans-5.2.8.RELEASE.jar:5.2.8.RELEASE]
    at org.springframework.beans.factory.support.DefaultListableBeanFactory.preInstantiateSingletons(DefaultListableBeanFactory.java:897) ~[spring-beans-5.2.8.RELEASE.jar:5.2.8.RELEASE]
    at org.springframework.context.support.AbstractApplicationContext.finishBeanFactoryInitialization(AbstractApplicationContext.java:879) ~[spring-context-5.2.8.RELEASE.jar:5.2.8.RELEASE]
    at org.springframework.context.support.AbstractApplicationContext.refresh(AbstractApplicationContext.java:551) ~[spring-context-5.2.8.RELEASE.jar:5.2.8.RELEASE]
    at org.springframework.boot.SpringApplication.refresh(SpringApplication.java:758) [spring-boot-2.3.2.RELEASE.jar:2.3.2.RELEASE]
    at org.springframework.boot.SpringApplication.refresh(SpringApplication.java:750) [spring-boot-2.3.2.RELEASE.jar:2.3.2.RELEASE]
    at org.springframework.boot.SpringApplication.refreshContext(SpringApplication.java:397) [spring-boot-2.3.2.RELEASE.jar:2.3.2.RELEASE]
    at org.springframework.boot.SpringApplication.run(SpringApplication.java:315) [spring-boot-2.3.2.RELEASE.jar:2.3.2.RELEASE]
    at org.springframework.boot.SpringApplication.run(SpringApplication.java:1237) [spring-boot-2.3.2.RELEASE.jar:2.3.2.RELEASE]
    at org.springframework.boot.SpringApplication.run(SpringApplication.java:1226) [spring-boot-2.3.2.RELEASE.jar:2.3.2.RELEASE]
    at com.example.demo.MyHighLevelEsRestDemoApplication.main(MyHighLevelEsRestDemoApplication.java:23) [classes/:na]
Caused by: org.springframework.beans.BeanInstantiationException: Failed to instantiate [org.elasticsearch.client.RestHighLevelClient]: Factory method 'restHighLevelClient' threw exception; nested exception is java.lang.NoSuchFieldError: IGNORE_DEPRECATIONS
    at org.springframework.beans.factory.support.SimpleInstantiationStrategy.instantiate(SimpleInstantiationStrategy.java:185) ~[spring-beans-5.2.8.RELEASE.jar:5.2.8.RELEASE]
    at org.springframework.beans.factory.support.ConstructorResolver.instantiate(ConstructorResolver.java:650) ~[spring-beans-5.2.8.RELEASE.jar:5.2.8.RELEASE]
    ... 19 common frames omitted
Caused by: java.lang.NoSuchFieldError: IGNORE_DEPRECATIONS
    at org.elasticsearch.client.RestHighLevelClient.<clinit>(RestHighLevelClient.java:1911) ~[elasticsearch-rest-high-level-client-7.8.1.jar:7.8.1]
    at com.example.demo.MyHighLevelEsRestDemoApplication.restHighLevelClient(MyHighLevelEsRestDemoApplication.java:29) [classes/:na]
    at com.example.demo.MyHighLevelEsRestDemoApplication$$EnhancerBySpringCGLIB$$a7cdbb42.CGLIB$restHighLevelClient$1(<generated>) ~[classes/:na]
    at com.example.demo.MyHighLevelEsRestDemoApplication$$EnhancerBySpringCGLIB$$a7cdbb42$$FastClassBySpringCGLIB$$c395303f.invoke(<generated>) ~[classes/:na]
    at org.springframework.cglib.proxy.MethodProxy.invokeSuper(MethodProxy.java:244) ~[spring-core-5.2.8.RELEASE.jar:5.2.8.RELEASE]
    at org.springframework.context.annotation.ConfigurationClassEnhancer$BeanMethodInterceptor.intercept(ConfigurationClassEnhancer.java:331) ~[spring-context-5.2.8.RELEASE.jar:5.2.8.RELEASE]
    at com.example.demo.MyHighLevelEsRestDemoApplication$$EnhancerBySpringCGLIB$$a7cdbb42.restHighLevelClient(<generated>) ~[classes/:na]
    at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method) ~[na:1.8.0_242]
    at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:62) ~[na:1.8.0_242]
    at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43) ~[na:1.8.0_242]
    at java.lang.reflect.Method.invoke(Method.java:498) ~[na:1.8.0_242]
    at org.springframework.beans.factory.support.SimpleInstantiationStrategy.instantiate(SimpleInstantiationStrategy.java:154) ~[spring-beans-5.2.8.RELEASE.jar:5.2.8.RELEASE]
    ... 20 common frames omitted

```

### 这是SpringBoot的elasticsearch版本冲突，导致的无法找到的问题

> 需要修改导入依赖包去掉version

```
<dependency>
    <groupId>org.elasticsearch.client</groupId>
    <artifactId>elasticsearch-rest-high-level-client</artifactId>
    <version>7.8.1</version>
</dependency>
```

修改为

```
<dependency>
    <groupId>org.elasticsearch.client</groupId>
    <artifactId>elasticsearch-rest-high-level-client</artifactId>
</dependency>
```
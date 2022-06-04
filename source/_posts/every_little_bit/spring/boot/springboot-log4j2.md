---
title: Spring Boot 更换 Logging 框架为 Log4J2
date: 2020-10-14
tags: SpringBoot,Logging,Log4J2,Java
---

# Spring Boot 更换 Logging 框架为 Log4J2

### 排除掉SpringBoot现有Logback依赖

```
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
    <exclusions>
        <exclusion>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-logging</artifactId>
        </exclusion>
    </exclusions>
</dependency>
```

### 加入Log4J2 的依赖

```
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-log4j2</artifactId>
</dependency>
```

### 配置application.yml 配置文件

```
logging:
    config: classpath: config/log4j2-dev.xml
```

### log4j2-dev.xml 根据官方文档进行配置

地址： https://logging.apache.org/log4j/2.x/manual/layouts.html

推荐配置：(网络上找到的不错的配置)

```
<Configuration status="INFO" monitorInterval="30">
    <Properties>
        <!--  输出路径  -->
        <Property name="logpath">/Log4j/logs/log/dev</Property>
    </Properties>
    <Appenders>
        <Console name="Console" target="SYSTEM_OUT">
            <PatternLayout
                    pattern="[%d][%-5p][%t] %m (%F:%L)%n"/>
        </Console>
        <RollingFile name="debug" fileName="${logpath}/debug/erp_debug.log"
                     filePattern="${logpath}/debug/erp_debug_%d{yyyy-MM-dd}-%i.log">
            <Filters>
                <ThresholdFilter level="info" onMatch="DENY" onMismatch="NEUTRAL"/>
                <ThresholdFilter level="debug" onMatch="ACCEPT" onMismatch="DENY"/>
            </Filters>
            <PatternLayout pattern="[%d][%-5p][%t] %m (%F:%L)%n"/>
            <Policies>
                <TimeBasedTriggeringPolicy interval="24" modulate="true"/>
                <SizeBasedTriggeringPolicy size="50 MB"/>\
            </Policies>
            <DefaultRolloverStrategy max="30">
                <Delete basePath="${logpath}/debug" maxDepth="1">
                    <IfFileName glob="erp_debug_*.log"/>
                    <IfLastModified age="15d"/>
                </Delete>
            </DefaultRolloverStrategy>
        </RollingFile>
        <RollingFile name="info" fileName="${logpath}/info/erp_info.log"
                     filePattern="${logpath}/info/erp_info_%d{yyyy-MM-dd}-%i.log">
            <Filters>
                <ThresholdFilter level="warn" onMatch="DENY" onMismatch="NEUTRAL"/>
                <ThresholdFilter level="info" onMatch="ACCEPT" onMismatch="DENY"/>
            </Filters>
            <PatternLayout pattern="[%d][%-5p][%t] %m (%F:%L)%n"/>
            <Policies>
                <TimeBasedTriggeringPolicy interval="24" modulate="true"/>
                <SizeBasedTriggeringPolicy size="50 MB"/>\
            </Policies>
            <DefaultRolloverStrategy max="30">
                <Delete basePath="${logpath}/info" maxDepth="1">
                    <IfFileName glob="erp_info_*.log"/>
                    <IfLastModified age="15d"/>
                </Delete>
            </DefaultRolloverStrategy>
        </RollingFile>
        <RollingFile name="warn" fileName="${logpath}/warn/erp_warn.log"
                     filePattern="${logpath}/warn/erp_warn_%d{yyyy-MM-dd}-%i.log">
            <Filters>
                <ThresholdFilter level="error" onMatch="DENY" onMismatch="NEUTRAL"/>
                <ThresholdFilter level="warn" onMatch="ACCEPT" onMismatch="DENY"/>
            </Filters>
            <PatternLayout pattern="[%d][%-5p][%t] %m (%F:%L)%n"/>
            <Policies>
                <TimeBasedTriggeringPolicy interval="24" modulate="true"/>
                <SizeBasedTriggeringPolicy size="50 MB"/>\
            </Policies>
            <DefaultRolloverStrategy max="30">
                <Delete basePath="${logpath}/warn" maxDepth="1">
                    <IfFileName glob="erp_warn_*.log"/>
                    <IfLastModified age="15d"/>
                </Delete>
            </DefaultRolloverStrategy>
        </RollingFile>
        <RollingFile name="error" fileName="${logpath}/error/erp_error.log"
                     filePattern="${logpath}/error/erp_error_%d{yyyy-MM-dd}-%i.log">
            <Filters>
                <ThresholdFilter level="fatal" onMatch="DENY" onMismatch="NEUTRAL"/>
                <ThresholdFilter level="error" onMatch="ACCEPT" onMismatch="DENY"/>
            </Filters>
            <PatternLayout pattern="[%d][%-5p][%t] %m (%F:%L)%n"/>
            <Policies>
                <TimeBasedTriggeringPolicy interval="24" modulate="true"/>
                  <!--   每个文件最大50M -->
                <SizeBasedTriggeringPolicy size="50 MB"/>\
            </Policies>
            <DefaultRolloverStrategy max="30">
                <Delete basePath="${logpath}/error" maxDepth="1">
                    <IfFileName glob="erp_error_*.log"/>
                     <!-- 设置最大保存时间为15天-->
                    <IfLastModified age="15d"/>
                </Delete>
            </DefaultRolloverStrategy>
        </RollingFile>

    </Appenders>
   <!--切换输出级别-->
    <Loggers>
        <Root level="info">
            <AppenderRef ref="Console"/>
            <AppenderRef ref="debug"/>
            <AppenderRef ref="info"/>
            <AppenderRef ref="warn"/>
            <AppenderRef ref="error"/>
        </Root>
    </Loggers>
</Configuration>
```

### 注意的地方

log4j2的配置文件是支持yml格式，但是如果使用yml格式的需要加入yml解析依赖包才能起作用，要不然有错误


```
<dependency>
    <groupId>com.fasterxml.jackson.dataformat</groupId>
    <artifactId>jackson-dataformat-yaml</artifactId>
</dependency>
```

> 如果不加这个依赖，会有项目启动报错：Could not initialize Log4J2 logging from classpath:log4j2-dev.yml

### SpringBoot配置文件中还可以加入打印级别指定

```
logging:
    level:
        org.springframework=INFO
```
---
title: 使用druid连接池的超时回收机制排查连接泄露问题
date: 2018-03-01 22:48:52
tags: druid, springboot, java
---
### 在工程中使用了druid连接池，运行一段时间后系统出现异常：

```base
Caused by: org.springframework.jdbc.CannotGetJdbcConnectionException: Could not get JDBC Connection; nested exception is com.alibaba.druid.pool.GetConnectionTimeoutException: wait millis 60009, active 50  
                at org.springframework.jdbc.datasource.DataSourceUtils.getConnection(DataSourceUtils.java:80)  
                at org.springframework.jdbc.support.JdbcUtils.extractDatabaseMetaData(JdbcUtils.java:280)  
                ... 64 more  
Caused by: com.alibaba.druid.pool.GetConnectionTimeoutException: wait millis 60000, active 50  
                at com.alibaba.druid.pool.DruidDataSource.getConnectionInternal(DruidDataSource.java:1071)  
                at com.alibaba.druid.pool.DruidDataSource.getConnectionDirect(DruidDataSource.java:898)  
                at com.alibaba.druid.filter.FilterChainImpl.dataSource_connect(FilterChainImpl.java:4544)  
```

> mysql数据库最大连接数设置为500，使用客户端能正常连接。连接数被未被占满。

### 分析原因应该是程序中有地方连接未关闭造成的。那如何来定呢？使用druid连接池的超时回收机制，在配置中增加以下内容：

```
<!-- 超过时间限制是否回收 -->  
<property name="removeAbandoned" value="true" />  
<!-- 超时时间；单位为秒。180秒=3分钟 -->  
<property name="removeAbandonedTimeout" value="180" />  
<!-- 关闭abanded连接时输出错误日志 -->  
<property name="logAbandoned" value="true" />     
```

### 运行程序，当连接超过3分钟后会强制进行回收，并输出异常日志。

```
2014-10-13 16:02:28,919 ERROR [com.alibaba.druid.pool.DruidDataSource] - <abandon connection, open stackTrace  
        at java.lang.Thread.getStackTrace(Thread.java:1567)  
        at com.alibaba.druid.pool.DruidDataSource.getConnectionDirect(DruidDataSource.java:995)  
        at com.alibaba.druid.filter.FilterChainImpl.dataSource_connect(FilterChainImpl.java:4544)  
        at com.alibaba.druid.filter.stat.StatFilter.dataSource_getConnection(StatFilter.java:661)  
        at com.alibaba.druid.filter.FilterChainImpl.dataSource_connect(FilterChainImpl.java:4540)  
        at com.alibaba.druid.pool.DruidDataSource.getConnection(DruidDataSource.java:919)  
        at com.alibaba.druid.pool.DruidDataSource.getConnection(DruidDataSource.java:911)  
        at com.alibaba.druid.pool.DruidDataSource.getConnection(DruidDataSource.java:98)  
          
        at cn.org.xxx.xxx.xxx.PaginationInterceptor.intercept(PaginationInterceptor.java:96)  
          
        at org.apache.ibatis.plugin.Plugin.invoke(Plugin.java:60)  
        at com.sun.proxy.$Proxy59.query(Unknown Source)  
        at org.apache.ibatis.session.defaults.DefaultSqlSession.selectList(DefaultSqlSession.java:108)  
```

> 很清楚地看到是在哪里打开的连接未关闭一直在占有。
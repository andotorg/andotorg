---
title: Spring配置文件p冒号driverClass等元素错误
date: 2018-03-01 22:52:01
tags: spring, p, <p>
---

### 在Spring配置文件中配置数据源使用SpEL、p:driverClass等元素时不小心会出现下面错误

```
The prefix "p" for attribute "p:jdbc.driverClassName" associatedwith an element type "bean" is not bound
意思是: 前缀“p”属性“p:jdbc。driverClassName”与某个元素类型相关“bean”却不被捆绑
 
这是因为在使用XML Schema作为配置文件的语义约束时,没有添加下面这条语句
<?xml version="1.0"encoding="UTF-8"?> 
<beansxmlns="http://www.springframework.org/schema/beans"xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"xmlns:tx="http://www.springframework.org/schema/tx"xmlns:aop="http://www.springframework.org/schema/aop"xmlns:context="http://www.springframework.org/schema/context"xmlns:jee="http://www.springframework.org/schema/jee"
**** 重要的一句****>   xmlns:p="http://www.springframework.org/schema/p"
  xsi:schemaLocation="http://www.springframework.org/schema/txhttp://www.springframework.org/schema/tx/spring-tx-3.2.xsdhttp://www.springframework.org/schema/aophttp://www.springframework.org/schema/aop/spring-aop-3.2.xsdhttp://www.springframework.org/schema/beanshttp://www.springframework.org/schema/beans/spring-beans-3.2.xsdhttp://www.springframework.org/schema/contexthttp://www.springframework.org/schema/context/spring-context-3.2.xsdhttp://www.springframework.org/schema/jeehttp://www.springframework.org/schema/jee/spring-jee-3.2.xsd">
<!-- 使用c3p0配置数据源--> 
<bean id="dataSource"class="com.mchange.v2.c3p0.ComboPooledDataSource"destroy-method="close"p:jdbc.driverClassName="com.mysql.jdbc.Driver"
 p:jdbcUrl="jdbc:mysql://localhost:3306/hrSystem"
 p:user="root"
 p:password="mrxing"
 p:maxPoolSize="40"
 p:minPoolSize="1" 
 p:initialPoolSize="1"
 p:maxIdleTime="20"
</bean>
</beans>
```

<html>
    <h4 style="color:#F00;">XML Schema 中加入：xmlns:p="http://www.springframework.org/schema/p"</h4>
</html>


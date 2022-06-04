---
title: Maven 打包Java应用的时候动态传输打包后jar包的名称
date: 2020-10-29
tags: Java,devops,auto,package,Maven,Linux
---

# Maven 打包Java应用的时候动态传输打包后jar包的名称

## 设置最终名称

```xml
<build>
    <finalName>share-cloud-1.0.0</finalName>
    ...
</build>
```

打包之后的名称就是share-cloud-1.0.0.jar

> 但是我不想每次需要修改pom文件的版本，改为自动传入可不可以，直接执行命令

## 那我们可以写一个sh命令，来传入参数执行

> 直接传入参数到finalName中是不行的，需要中转一下

### 第一步：设置打包的时候可以传入的参数 -D

-D是把参数放入properties

```xml
<properties>
    <share.cloud.version>1.0.0</share.cloud.version>
</properties>
```

然后设置finalName获取整个version

```xml
<build>
    <finalName>share-cloud-${share.cloud.version}</finalName>
    ...
</build>
```

然后打包的时候就可以直接传入

```
mvn clean compile package -Pdev -Dshare.cloud.version=$1
```

> -P 是设置profile的环境的
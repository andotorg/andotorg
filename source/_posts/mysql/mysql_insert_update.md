---
title: 深入分析MySQL replace 与 on duplicate key update 关键词
date: 2021-10-14 15:42:00
tags: replace, duplicate, on duplicate key update, insert and update, mysql
index_img: /images/mysql/index.png # 缩略图
banner_img: /images/post_banner.jpeg # 文章banner图
categories: MySQL
---

# 深入分析MySQL replace 与 on duplicate key update 关键词

## 建立一张数据表

```sql 
CREATE TABLE `test`.`test_unique_key` (
 `id` varchar(32) NOT NULL,
 `name` varchar(255) NULL,
 `term_id` varchar(64) NULL,
 `class_id` varchar(64) NULL,
 `course_id` varchar(64) NULL,
 PRIMARY KEY (`id`),
 UNIQUE INDEX `unique`(`term_id`, `class_id`, `course_id`) USING BTREE
);
```

查看REPLACE ...
 
## 语句执行情况

### 1、先测试主键一样，唯一键不一样
```sql
REPLACE INTO test_unique_key ( id, NAME, term_id, class_id, course_id )
VALUES( '17b2ee26583000163e0b2e12010c2001', '您好', '17b2ee26583000163e0b2e12010c2002', '17b2ee26583000163e0b2e12010c2003', '17b2ee26583000163e0b2e12010c2004');
 
REPLACE INTO test_unique_key ( id, NAME, term_id, class_id, course_id )
VALUES( '17b2ee26583000163e0b2e12010c2001', '您好', '17b2ee26583000163e0b2e12010c2002', '17b2ee26583000163e0b2e12010c2003', NULL );
 
REPLACE INTO test_unique_key ( id, NAME, term_id, class_id, course_id )
VALUES( '17b2ee26583000163e0b2e12010c2001', '您好', '17b2ee26583000163e0b2e12010c2002', '17b2ee26583000163e0b2e12010c2003', NULL );
```

> 执行结果，只剩一条数据 （按照主键进行了更新）
​

![](/images/mysql/insertupdate/1.png)

### 2、主键不一样，唯一键一样
```sql
REPLACE INTO test_unique_key ( id, NAME, term_id, class_id, course_id )
VALUES( '17b2ee26583000163e0b2e12010c2005', '您好', '17b2ee26583000163e0b2e12010c2002', '17b2ee26583000163e0b2e12010c2003', '17b2ee26583000163e0b2e12010c2004' );

REPLACE INTO test_unique_key ( id, NAME, term_id, class_id, course_id )
VALUES( '17b2ee26583000163e0b2e12010c2006', '您好', '17b2ee26583000163e0b2e12010c2002', '17b2ee26583000163e0b2e12010c2003', '17b2ee26583000163e0b2e12010c2004' );
```

> 由此看出，主键不一样，执行两次，最终剩余一条数据，所以是按照唯一键更新
​

![](/images/mysql/insertupdate/3.png)

### 3、所以说明

在主键相同唯一键不同时，则使用主键更新，唯一键相同主键不同时使用唯一键更新
如果存在主键和唯一索引，主键和唯一索引有一个相同的则被看做唯一标识，所以官方说明的是或者的关系
官方原文说明

> except that if an old row in the table has the same value as a new row for a PRIMARY KEY or a UNIQUE index, the old row is deleted before the new row is inserted.

### 4、语句优点

书写比较简单，就是把插入语句的insert关键字修改replace即可

### 5、语句缺点

但是replace实现原理为如果找到匹配的，是先删除后重新插入

### 6、权限说明
要使用REPLACE，您必须同时拥有表的INSERT和 DELETE权限。

## 再看看INSERT ... ON DUPLICATE KEY UPDATE 语句
 
### 1、主键相同，唯一索引不同
```sql
INSERT INTO test_unique_key ( `id`, `NAME`, `term_id`, `class_id`, `course_id` )
VALUES
 ( '17b2ee26583000163e0b2e12010c2011', '您好Duplicate', '17b2ee26583000163e0b2e12010c2012', '17b2ee26583000163e0b2e12010c20010', '17b2ee26583000163e0b2e12010c2011' ) 
ON DUPLICATE KEY UPDATE name = '张三'
-- 第二次执行，主键相同
INSERT INTO test_unique_key ( `id`, `NAME`, `term_id`, `class_id`, `course_id` )
VALUES
 ( '17b2ee26583000163e0b2e12010c2011', '您好Duplicate', '17b2ee26583000163e0b2e12010c2012', '17b2ee26583000163e0b2e12010c20010', '17b2ee26583000163e0b2e12010c2011' ) 
ON DUPLICATE KEY UPDATE name = '张三'
```

> 执行结果，因为不存在，则直接插入
​

![](/images/mysql/insertupdate/4.png)


![](/images/mysql/insertupdate/5.png)

### 2、唯一索引相同，主键不同
```sql
INSERT INTO test_unique_key ( `id`, `NAME`, `term_id`, `class_id`, `course_id` )
VALUES( '17b2ee26583000163e0b2e12010c2012', '您好Duplicate', '17b2ee26583000163e0b2e12010c2012', '17b2ee26583000163e0b2e12010c20010', '17b2ee26583000163e0b2e12010c2011' ) 
ON DUPLICATE KEY UPDATE name = '张三1'
```

> 主键不同，唯一索引，也是更新数据
​

![](/images/mysql/insertupdate/6.png)

### 3、综上所述
在主键相同唯一键不同时，则使用主键更新，唯一键相同主键不同时使用唯一键更新 主键与唯一索引也为或者关系

### 4、语句优点
实际执行，则不存在为insert操作，如果存在则是update操作

### 5、语句缺点
该语句书写起来比较麻烦，相当于是一个插入语句与更新语句的结合体
只是不需要书写update 的 where提交，然后MySQL会根据唯一索引或者主键进行判断

### 6、权限说明
要使用on duplicate key update 您必须同时拥有表的INSERT和 UPDATE权限。
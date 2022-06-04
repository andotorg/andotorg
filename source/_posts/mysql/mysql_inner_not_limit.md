---
title: MySQL不支持在IN和EXISTS里面加入LIMIT
date: 2020-12-09
tags: Java,MySQL,IN,EXISTS,SQL优化
index_img: /images/mysql/index.png # 缩略图
banner_img: /images/post_banner.jpeg # 文章banner图
categories: MySQL
---

# MySQL不支持在IN和EXISTS里面加入LIMIT

> 有一需求是需要上传一个excel之后进行查看，但是Excel的列是不固定的，还可以进行反复上传，
但是Excel中有一列是必须要有的那就是ID列，这个列可以用来判断第二次上传是更新还是有新加入的数据

> 在子查询里面使用了LIMIT，因为需要分页

### 在官网中找到了这个限制和解决方式

https://dev.mysql.com/doc/mysql-reslimits-excerpt/8.0/en/subquery-restrictions.html

### 解决方案：在子查询里面在加入一个孙子查询，把LIMIT进行实现了，就可以规避这个问题 

```
MySQL permits a subquery to refer to a stored function that has data-modifying side effects such as inserting rows into a table. For example, if f() inserts rows, the following query can modify data:

SELECT ... WHERE x IN (SELECT f() ...);
This behavior is an extension to the SQL standard. In MySQL, it can produce nondeterministic results because f() might be executed a different number of times for different executions of a given query depending on how the optimizer chooses to handle it.

For statement-based or mixed-format replication, one implication of this indeterminism is that such a query can produce different results on the source and its slaves.
```
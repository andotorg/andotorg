---
title: 使用MySQL管理型数据库实现不定列数据存储
date: 2020-12-09
tags: Java,MySQL,IN,EXISTS,SQL优化,Design
index_img: /images/mysql/index.png # 缩略图
banner_img: /images/post_banner.jpeg # 文章banner图
categories: MySQL
---

# 使用MySQL管理型数据库实现不定列数据存储

> 有一需求是需要上传一个excel之后进行查看，但是Excel的列是不固定的，还可以进行反复上传，
但是Excel中有一列是必须要有的那就是ID列，这个列可以用来判断第二次上传是更新还是有新加入的数据


>  就对于这个需求呢，我在MySQL中设计了三张表， 表中重点字段为：

> 只列举重要字段

> 第一张表sample字段为：

```sql
sample_number（样本编号 - 生成策略当前上传的日期然后另外加入4位数的自增从0001开始例如第一个202012090001）、sample_name（上传excel的名字）、excel_url（上传excel存储在服务器的地址）
```

> 第二张表sample_field字段 （存储列名）

```sql
sample_number（样本编号）、field_name（字段名称）、field_type（STRING、INT、DATE）（字段类型）、is_primary（是否为主键）、field_index（字段排列顺序）
```

> 第三张表sample_data 字段（存储数据）：

```sql
sample_number（样本编号）、field_name（字段名称）、row_id（行id，用来区分哪些数据是属于同一行）、field_value（具体的列的值，因为字段已经表示了类型，
所以存储的时候直接都存储字符串，根据需要的时候在去做转换）
```

# 错误起源

> 所以，有了上面三张表之后，那就需要查询数据了，在查询数据的过程中呢，因为需要分页和条件筛选，按照逻辑进行组织了SQL，出错了

###  错误提示

```
This version of MySQL doesn't yet support 'LIMIT & IN/ALL/ANY/SOME subquery'
```

### 在官网中找到了这个限制和解决方式

https://dev.mysql.com/doc/mysql-reslimits-excerpt/8.0/en/subquery-restrictions.html

### 解决方案：在子查询里面在加入一个孙子查询，把LIMIT进行实现了，就可以规避这个问题 

```
MySQL permits a subquery to refer to a stored function that has data-modifying side effects such as inserting rows into a table. For example, if f() inserts rows, the following query can modify data:

SELECT ... WHERE x IN (SELECT f() ...);
This behavior is an extension to the SQL standard. In MySQL, it can produce nondeterministic results because f() might be executed a different number of times for different executions of a given query depending on how the optimizer chooses to handle it.

For statement-based or mixed-format replication, one implication of this indeterminism is that such a query can produce different results on the source and its slaves.
```

## 最终实现SQL

```sql
SELECT
	hsd.row_id,
	hsd.sample_number,
	hsd.field_name,
	hsd.field_value 
FROM
	hwcss_sample_data AS hsd 
WHERE
	EXISTS (
	SELECT
		row_id 
	FROM
		( SELECT row_id FROM hwcss_sample_data phsd WHERE del_flag = 0
			 	AND EXISTS (SELECT row_id FROM hwcss_sample_data WHERE field_name = 'S_Serial' AND field_value = '1001' AND  phsd.row_id = row_id ) 
				AND EXISTS (SELECT row_id FROM hwcss_sample_data WHERE field_name = 'ID' AND field_value = 'B112527' AND  phsd.row_id = row_id )
				# TODO 可以再加入筛选条件
			GROUP BY row_id LIMIT 10 OFFSET 1 ) AS ihsd 
	WHERE
		hsd.row_id = ihsd.row_id 
	) 
ORDER BY
	hsd.row_id
```

### 索引优化

```
在 row_id 和 field_name 上面做了索引优化，使用B+TREE索引，索引类型为 NORMAL
```

附录：

> MySQL单表数据限制：https://dev.mysql.com/doc/refman/8.0/en/table-size-limit.html
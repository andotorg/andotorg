---
title: 什么是ClickHouse？
date: 2020-10-17 20:04:38
tags: ClickHouse,BigData,Java
---

# 什么是ClickHouse

> ClickHouse是一个面向列的数据库管理系统（DBMS），用于联机分析处理查询（OLAP）。

在面向行的“普通”DBMS中，数据按以下顺序存储：

| Row | WatchID | JavaEnable | Title | GoodEvent | EventTime
|---|---|---|---|---|---|
|#0 | 89354350662 | 1 | Investor Relations | 1 | 2016-05-18 05:19:20|
|#1 | 90329509958 | 0 | Contact us | 1 | 2016-05-18 08:10:20|
|#2 | 89953706054 | 1 | Mission | 1 | 2016-05-18 07:38:00|
|#N | … | … | … | … | …|



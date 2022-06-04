---
title: Docker compose 启动的容器设置开启自动启该容器
date: 2020-10-12
tags: docker,docker compose,start,Linux
---

# Docker compose 启动的容器设置开启自动启该容器

### 在docker配置文件 docker-compose.yml 添加属性

```
version: '3.3'
services:
web:
    image: demo:1.0.0
    ports:
      - 8000:8000
    restart: always
```


---
title: COLA
author: Louis
date: 2023-07-25 09:40:12
categories: Architecture
tags: [COLA]
---

[COLA](https://github.com/alibaba/COLA) 是 Clean Object-Oriented and Layered Architecture的缩写，代表“整洁面向对象分层架构”。
![cola架构图](https://www.goodserendipity.com/asserts/architecture/cola%20architecture.png)

```shell
# 创建web工程
mvn archetype:generate \
    -DgroupId=com.alibaba.cola.demo.web \
    -DartifactId=demo-web \
    -Dversion=1.0.0-SNAPSHOT \
    -Dpackage=com.alibaba.demo \
    -DarchetypeArtifactId=cola-framework-archetype-web \
    -DarchetypeGroupId=com.alibaba.cola \
    -DarchetypeVersion=4.3.2

#创建service工程
mvn archetype:generate \
    -DgroupId=com.alibaba.cola.demo.service \
    -DartifactId=demo-service \
    -Dversion=1.0.0-SNAPSHOT \
    -Dpackage=com.alibaba.demo \
    -DarchetypeArtifactId=cola-framework-archetype-service \
    -DarchetypeGroupId=com.alibaba.cola \
    -DarchetypeVersion=4.3.2
```

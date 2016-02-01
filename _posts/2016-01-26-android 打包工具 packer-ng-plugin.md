---
layout: post
title: sonar 分析配置
date: 2016-02-01 17:31:54
categories: blog
tags: [sonar 分析配置,代码检查,code review]
description:  
---


## 最新版本
 

##项目介绍

     org.jacoco:jacoco-maven-plugin:prepare-agent clean test sonar:sonar
     
### 说明：  覆盖率，以及单元测试 soanr分析，因为分析基本基于build，所以尽量添加clean

##项目介绍

    -Dsonar.porfile=xxx

### 说明：  添加sonar 指定检查规则
     

---
title: AndroidAppRE 01 - Beginning RE with jadx
date: 2020-01-12 07:51:04 +0800
draft: false
tags: 
  - Android
  - App RE
---

你作为一个病毒分析人员，分析一个付费短信诈骗（premium sms fraud）的app

<!--more-->

## 目标
1. 学习jadx的使用
2. 了解 android app 的 组成
3. 初步了解分析病毒app的思路与方法

## 付费短信诈骗的3个特点
1.发送SMS短信
 * sendTextMessage . 
 * sendMultipartTextMessage  

2.发送SMS短信到付费账号（通常是短代码  
3.不需要用户同意 后台运行   

## 敏感 API 调用
![](https://pic.lushx.cn/15788267627105.jpg)

## 敏感字符串
![](https://pic.lushx.cn/15788269260488.jpg)

## Manifest.xml 寻找入口点
![](https://pic.lushx.cn/15788271291660.jpg)

---
title: 通过cmd来使用数据库
date: 2022-12-05 20:05:05
tags:
---

# 通过cmd来使用数据库

## 在公司很多时候是不给用navicat这类软件，GUI收费啊！！！！
1. 第一步要先下载mysql
2. 开服务net start mysql
3. 进bin开cmd
4. 密码登录改密码为123456
5. 使用新密码登录 mysql -u root -p 123456
6. create database test建库
7. show databases;记得加;才有效，看库
8. use <basename\>操作/使用数据库
9. 通过create table user命令建表
10. describe <tablename\> 用于查表
11. select * from <tablename\> 查表
12. drop table table_name 删除表
---
title: 像你的mysql
date: 2022-12-14 19:51:06
tags:
---

# User 表

```sql
CREATE TABLE user ( 
    id INTEGER PRIMARY KEY AUTO_INCREMENT, 
    username VARCHAR(255) NOT NULL, 
    password VARCHAR(255) NOT NULL, 
    email VARCHAR(255) NOT NULL UNIQUE, 
    phone VARCHAR(255) NOT NULL UNIQUE, 
    createtime DATETIME NOT NULL, 
    updatetime DATETIME NOT NULL 
);
```

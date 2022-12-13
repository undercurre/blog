---
title: excel批量处理器
date: 2022-12-10 03:16:38
tags:
---

# excel批量处理器

## 背景

故事发生在公司的某一天，硬件产品部那边突然开了个会说要每个员工抽点时间帮忙录20万个报表，我一听这个丢事我就震惊了，然后开完会发现那个node.js跑一下就好了，最后成功解救了15000个工时的浪费。

根据这个案例，可以很快明白到一个需求——很多时候我们会从一堆表格里拿到一堆数据，然后通过过滤、计算、合并进行批量操作以后生成新的表格，然后导入数据库或者系统里，基本上说是数据迁移的一种吧。

## 需求

那现在我们来澄清这个需求，

1. 用户要能建立这个表

2. 用户能把表里的数据指向某个表的某一项

3. 能批量导入处理

## 分析难点

1. 第一点很通用，用sheet.js解析一个表并且给用户看和填就行了，这里难的是

2. 第二点，需要支持表的结构可视化，我想到的结构最深奥的就是二叉树

3. 批量就是做好多个文件遍历，如果能搞个多线程更牛，这里涉及一个node.js的线程探究

## 干活

先写node脚本确保核心代码能顺利处理文本

### 第一步——node准备

首先你要知道commonjs和module,这个东西对应了package.json中的type属性，默认是commonjs，除了type可以指定模块化规范外，通过文件的后缀来指定文件所遵循的模块化规范，以.mjs结尾的文件就是使用的ESModule规范，以.cjs结尾的遵循的是commonjs规范。

```
新建个文件夹，文件夹名为项目名
然后进去pnpm init新建一个项目
```

### 新时代肯定要用ts

需要注意以下几点：

1. pnpm i --save @types/node
2. 引入第三方库： npm i --save 第三方库名 npm i --save @types/第三方库名
3. tsc --init 初始化tsconfig.json
4. ts-node可以直接运行ts代码,pnpm i ts-node

### 进入正题

1. 安装库
```
pnpm install xlsx
```
```
var XLSX = require('xlsx');
```
2. 读取excel文件
```
var workbook = XLSX.readFile('./lib/study.xlsx');

var sheets = workbook.Sheets;

var sheetNames = workbook.SheetNames;
```
3. 写入excel文件
```
var newBook = XLSX.utils.book_new();

// 遍历表
sheetNames.forEach((element: string) => {
    // 取得表数据
    var aoa = XLSX.utils.sheet_to_json(sheets[element], {header: 1});
    // 处理表中的日期时间数据
    aoa.forEach((item: any, index: Number) => {
        if (index === 0) return
        item[1] = XLSX.SSF.parse_date_code(item[1]);
        item[1] = `${item[1].y}/${item[1].m}/${item[1].d}`;
    });
    // 制作新表
    var newSheet = XLSX.utils.aoa_to_sheet(aoa);
    // 插入新表
    XLSX.utils.book_append_sheet(newBook, newSheet, `翻译后${element}`);
    // 导出文件
    XLSX.writeFile(newBook, './dist/hihi.xlsx');
});
```
4、处理数据
Dates 默认存储为数字，使用XLSX.SSF.parse_date_code进行转换
```
item[1] = XLSX.SSF.parse_date_code(item[1]);
item[1] = `${item[1].y}/${item[1].m}/${item[1].d}`;
```

### 增值功能——输出图形

### 增值功能——输出ppt

### 服务器加速

### 浏览器本地保密
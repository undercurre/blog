---
title: hexo博客指南
date: 2022-10-03 17:38:49
tags:
---
# hexo
Hexo 是一个快速、简洁且高效的博客框架。Hexo 使用 Markdown（或其他渲染引擎）解析文章，在几秒内，即可利用靓丽的主题生成静态网页。

## 原理
基于配置生成静态网页

## 环境准备

### 1、安装Node.js (Node.js 版本需不低于 10.13，建议使用 Node.js 12.0 及以上版本)
### 2、安装Git
### 3、我使用的是pnpm（可选）

## 安装hexo

```
npm install -g hexo-cli // 安装
hexo init myblog // 初始化文件
pnpm i // 安装依赖
hexo g // 生成静态文件
hexo server // 启动服务
```

## 放GitHub上建站访问
1、新建guthub仓库 ：用户名+.github.io

2、安装上传插件pnpm install hexo-deployer-git --save

3、对_config.yml文件进行编辑
```
deploy:
  type: git
  repo: https://github.com/undercurre/undercurre.github.io.git
  branch: master
```
4、编辑package.json的script命令并执行pnpm upload
```
"upload": "hexo clean && hexo generate && hexo deploy",
"hot": "hexo clean && hexo generate && hexo server"
```
5、然后就可以根据仓库地址访问博客了

## 当然啦，看上去和粗糙，所以要换主题

1、在github上搜索hexo theme就有很多主题然后根据readme进行安装就好了
2、推荐Fluid，比较新颖，这个博客就是用它做的，底部有Hexo和Fluid的文档链接

## 后续维护，新增文章
```
hexo new "新文章的名字"
```
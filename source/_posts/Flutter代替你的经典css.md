---
title: Flutter代替你的经典css
date: 2022-12-30 11:19:04
tags:
---
# Flutter替换经典css笔录

## 背景

作为一个前端，Flutter陌生的像个鸡一样（我这的不知道这是啥比喻，我羊了，体谅一下我下降的智商），现在收集一下这些常用的样式，后面再做个字典吧~

### 渐变背景——background-image: lineargradient()

```dart
Container(
      width: MediaQuery.of(context).size.width,
      height: MediaQuery.of(context).size.height,
      decoration: const BoxDecoration(
        gradient: LinearGradient(
          begin: Alignment.centerLeft,
          end: Alignment.centerRight,
          stops: [0.0, 0.5, 1.0],
          colors: [
            Color.fromRGBO(192, 132, 252, 1),
            Color.fromRGBO(236, 72, 153, 1),
            Color.fromRGBO(239, 68, 68, 1)
          ],
        ),
      ),
    )
```

### 绝对定位——position:absolute
```dart

```

### 居中（水平+垂直）

```dart
Center(child: Container()) 
```
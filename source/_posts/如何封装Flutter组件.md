---
title: 如何封装Flutter组件
date: 2022-11-09 09:54:17
tags:
---

# 如何封装Flutter组件

组件分两种，stateful 和 stateless

## 封装stateful组件(滑动条)

```dart
import 'dart:math';
import 'package:flutter/material.dart';

// 步骤1：class组件集成statefulwidget
class MzSlider extends StatefulWidget {

  // 步骤2：定义组件内的参数，这里就是你待会使用组件的时候要传进来的参数
  // 渐变色数组
  final List<Color> activeColors;

  // 滑动条宽高
  final double width;
  final double height;

  // 是否半圆
  final bool rounded;

  // 非半圆圆角大小
  final double radius;

  // 圆点半径
  final double ballRadius;

  // 数值
  final num max;
  final num min;
  final num step;
  final num value;

  // 是否禁用操作
  final bool disabled;

  // 滑动回调，传递出进度值和当前颜色
  final void Function(num value, Color activeColor)? onChanging;
  final void Function(num value, Color activeColor)? onChanged;

  // 插槽
  // final Widget child;

  // 动画时长，不传表示不执行动画
  final Duration? duration;

  // 组件内边距，用于拓展手势区域，提高用户体验
  final EdgeInsetsGeometry padding;


  // 步骤3：根据上面的定义的状态参数定义构造函数，在这个过程中你可以定义构造组件时的默认值
  const MzSlider({
    super.key,
    required this.value,
    this.duration,
    this.onChanged,
    this.onChanging,
    this.activeColors = const [Color(0xFF267AFF), Color(0xFF267AFF)],
    this.max = 100,
    this.min = 0,
    this.width = 100,
    this.height = 20,
    this.rounded = false,
    this.radius = 10,
    this.ballRadius = 6,
    this.step = 1,
    this.disabled = false,
    this.padding = const EdgeInsets.all(20),
  });

  // 步骤4：最后抛出createState的方法就完成组件的定义
  @override
  State<MzSlider> createState() => _MzSliderState();
}

// 步骤5：class State
class _MzSliderState extends State<MzSlider>
    with TickerProviderStateMixin {

  // 步骤6：定义state
  // 轨道的key
  final GlobalKey _railKey = GlobalKey();

  // 动画控制器
  AnimationController? controller;

  // 当前滑动条的状态
  late num value;

  // 如果设置了动画，需要使用该变量设置动画最终值，并传递给父组件
  late num toValue;
  bool isPanUpdate = false;
  bool isPanning = false;
  late Offset latestPosition;

  // 步骤7：抛出state的初始化，这里可以通过widget获取到组件中的参数
  @override
  void initState() {
    super.initState();
    value = widget.value < widget.min
        ? widget.min
        : widget.value > widget.max
        ? widget.max
        : widget.value;
  }

  @override
  void didUpdateWidget(MzSlider oldWidget) {
    if (isPanning) return;
    if (controller != null && controller!.status == AnimationStatus.forward) {
      return;
    }
    var oldValue = value;
    var newValue = clampValue(widget.value);
    super.didUpdateWidget(oldWidget);
    doAnimation(newValue, oldValue);
  }

  // 步骤8：通过build方法构建组件
  @override
  Widget build(BuildContext context) {
    return GestureDetector(
      onPanDown: (e) => onPanDown(e),
      //手指滑动时会触发此回调
      onPanUpdate: (e) => onPanUpdate(e),
      onPanEnd: (e) => onPanUp(),
      onPanCancel: () => onPanUp(),
      child: Container(
        padding: widget.padding,
        decoration: const BoxDecoration(color: Colors.transparent),
        child: ConstrainedBox(
          constraints: BoxConstraints.tightFor(
            height: widget.height,
            width: widget.width,
          ),
          child: Stack(
            alignment: Alignment.centerLeft, //指定未定位或部分定位widget的对齐方式
            children: <Widget>[
              DecoratedBox(
                key: _railKey,
                decoration: BoxDecoration(
                  color: const Color(0xFF1F1F1F),
                  borderRadius: widget.rounded
                      ? BorderRadius.circular(widget.height / 2)
                      : BorderRadius.circular(widget.radius),
                ),
                child: ConstrainedBox(
                  constraints: BoxConstraints.tightFor(
                    height: widget.height,
                    width: widget.width,
                  ),
                ),
              ),
              DecoratedBox(
                decoration: BoxDecoration(
                  gradient: LinearGradient(colors: getActiveColor()),
                  borderRadius: widget.rounded
                      ? BorderRadius.circular(widget.height / 2)
                      : BorderRadius.circular(widget.radius),
                ),
                child: ConstrainedBox(
                  constraints: BoxConstraints.tightFor(
                    height: widget.height,
                    width: getActiveRailWidth(),
                  ),
                ),
              ),
              Positioned(
                left: getBallLeft(),
                child: Container(
                  width: widget.ballRadius * 2,
                  height: widget.ballRadius * 2,
                  decoration: BoxDecoration(
                    color: const Color(0xFFFFFFFF),
                    borderRadius: BorderRadius.circular(widget.ballRadius),
                  ),
                ),
              ),
            ],
          ),
        ),
      ),
    );
  }

  /// 值计算
  // 获取当前圆点的滑动条颜色
  List<Color> getActiveColor() {
    Color leftColor = widget.activeColors[0];
    Color rightColor = widget.activeColors[1];
    int curRed =
    ((rightColor.red - leftColor.red) * valueToPercentage(value) + leftColor.red)
        .round();
    int curGreen =
    ((rightColor.green - leftColor.green) * valueToPercentage(value) + leftColor.green)
        .round();
    int curBlue =
    ((rightColor.blue - leftColor.blue) * valueToPercentage(value) + leftColor.blue)
        .round();
    int curAlpha =
    ((rightColor.alpha - leftColor.alpha) * valueToPercentage(value) + leftColor.alpha)
        .round();
    return [
      widget.activeColors[0],
      Color.fromARGB(curAlpha, curRed, curGreen, curBlue)
    ];
  }

  // 获取白色圆点的left距离
  double getBallLeft() {
    return widget.height -
        ((widget.height - widget.ballRadius * 2) / 2 + widget.ballRadius * 2) +
        valueToPercentage(value) * (widget.width - widget.height);
  }

  // 获取激活部分滑动条长度
  double getActiveRailWidth() {
    return widget.height + valueToPercentage(value) * (widget.width - widget.height);
  }

  num clampValue(num value) {
    return min(widget.max, max(widget.min, value));
  }

  num valueToPercentage(num value) {
    return (value - widget.min) / (widget.max - widget.min);
  }

  num percentageToValue(num percentage) {
    return widget.min + (widget.max - widget.min) * percentage;
  }

  int getPrecision() {
    var stepString = widget.step.toString();
    if (!stepString.contains('.')) {
      return 0;
    } else {
      return stepString.length - stepString.indexOf('.') - 1;
    }
  }

  num steppingValue(num nextValue) {
    if (widget.step <= 0) return nextValue;
    final precision = getPrecision();
    final currentStep =
    num.parse((nextValue / widget.step).toStringAsFixed(precision));
    final leftStep = num.parse((currentStep.floor() * widget.step).toStringAsFixed(precision));
    final rightStep = num.parse(((currentStep.floor() + 1) * widget.step).toStringAsFixed(precision));
    final closestStep = (nextValue - leftStep).abs() - (nextValue - rightStep).abs() > 0 ? rightStep : leftStep;
    return closestStep;
  }

  // 执行动画（如果不存在duration则没有动画过程）
  void doAnimation(num newValue, num oldValue) {
    if (widget.duration != null) {
      // 传入动画执行时间，需要执行动画
      // 如果当前动画还在执行，需要先停掉上一次动画
      if (controller?.status == AnimationStatus.forward) {
        controller!.stop();
      }
      // 执行本次动画
      controller = AnimationController(
        duration: widget.duration,
        vsync: this,
      );
      CurvedAnimation curve =
      CurvedAnimation(parent: controller!, curve: Curves.easeInOut);
      Animation<num> animation =
      Tween(begin: oldValue, end: newValue).animate(curve);
      animation.addListener(() {
        setState(() {
          value = animation.value;
        });
      });
      controller!.forward();
    } else {
      setState(() {
        value = newValue;
      });
    }
  }

  /// 事件处理
  void onPanDown(DragDownDetails e) {
    if (widget.disabled) return;
    RenderBox railRenderObject = _railKey.currentContext?.findRenderObject() as RenderBox;
    final percentage = (railRenderObject.globalToLocal(e.globalPosition).dx / railRenderObject.paintBounds.width);
    latestPosition = e.globalPosition;
    isPanning = true;
    isPanUpdate = false;
    toValue = clampValue(steppingValue(percentageToValue(percentage)));
    doAnimation(toValue, value);
  }

  void onPanUpdate(DragUpdateDetails e) {
    if (widget.disabled) return;
    isPanUpdate = true;
    latestPosition = e.globalPosition;
    //用户手指滑动时，更新偏移，重新构建
    RenderBox railRenderObject = _railKey.currentContext?.findRenderObject() as RenderBox;
    final percentage = (railRenderObject.globalToLocal(latestPosition).dx / railRenderObject.paintBounds.width);
    final percentageValue = percentageToValue(percentage);
    final emitValue = clampValue(steppingValue(percentageValue));
    setState(() {
      value = clampValue(percentageValue);
      toValue = clampValue(percentageValue);
    });
    widget.onChanging?.call(emitValue, getActiveColor()[1]);
  }

  void onPanUp() {
    if (widget.disabled) return;
    RenderBox railRenderObject = _railKey.currentContext?.findRenderObject() as RenderBox;
    final percentage = (railRenderObject.globalToLocal(latestPosition).dx / railRenderObject.paintBounds.width);
    final temp = clampValue(steppingValue(percentageToValue(percentage)));
    isPanning = false;
    setState(() {
      value = temp;
      toValue = temp;
    });
    widget.onChanged?.call(toValue, getActiveColor()[1]);
  }
}
```

## 封装stateless组件（金属泛光卡片）

```dart
import 'package:flutter/material.dart';

// 步骤1：先定义组件继承StatelessWidget
class GlassCard extends StatelessWidget {

  // 步骤2：定义组件内参数
  final double width;
  final double height;
  final Widget child;

  // 步骤3：定义组件构造函数
  const GlassCard(
      {super.key, this.width = 312, this.height = 112, required this.child});

  // 步骤4：抛出build方法
  @override
  Widget build(BuildContext context) {
    return Container(
      width: width,
      height: height,
      margin: const EdgeInsets.fromLTRB(0, 0, 0, 16),
      decoration: BoxDecoration(
        borderRadius: BorderRadius.circular(10.0),
        border: Border.all(width: 0.2, color: const Color(0xff979797)),
        gradient: const LinearGradient(
          begin: Alignment(-1, 1), //右上
          end: Alignment(0, 0.365), //左下
          stops: [0.0, 1.0], //[渐变起始点, 渐变结束点]
          colors: [Color(0xFF393E43), Color(0xFF333135)],
        ),
      ),
      child: child,
    );
  }
}
```
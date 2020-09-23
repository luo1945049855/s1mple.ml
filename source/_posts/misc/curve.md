---
title:  "Curve"
date:   2020-09-09
categories: Misc
---

## Bezier Curve

之前使用Qt画过贝塞尔曲线，用户可拖动`2个`控制点进行调整，此种方案属于交互式。

## 给定一系列点，画曲线

此处找到的方式有：

* Krita（基于Qt的画图工具）基于Bezier曲线的实现。

* JScurve求平均值方式，可以使点平滑。

* [burningmime](https://github.com/burningmime/curves)基于`Philip J. Schneider's least-squares method`实现的方案。此方案效果较好，但是有出错情况而且性能较差。

* [Draw a Smooth Curve through a Set of 2D Points with Bezier Primitives](https://www.codeproject.com/Articles/31859/Draw-a-Smooth-Curve-through-a-Set-of-2D-Points-wit)。
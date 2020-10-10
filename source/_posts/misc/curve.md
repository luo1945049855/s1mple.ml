---
title:  "Curve"
date:   2020-09-09
categories: Misc
---

## Bezier Curve

贝塞尔曲线通过两个`端点`和两个`控制点`指定。
曲线不会通过控制点，但是控制点会像磁铁一样将曲线拉向控制点的方向，并影响曲线的弯曲。
如下图所示：

![bezier_splines](/images/bezier_spline.png)

注意：
曲线从端点p1开始，向控制点c1移动。曲线在端点p1处的`切线`是p1和c1的连线。
曲线在端点p2处的切线是p2和c2的连线。

## 给定一系列点，画曲线

此处找到的方式有：

* Krita（基于Qt的画图工具）基于Bezier曲线的实现。

* JScurve求平均值方式，可以使点平滑。

* [burningmime](https://github.com/burningmime/curves)基于`Philip J. Schneider's least-squares method`实现的方案。此方案效果较好，但是有出错情况而且性能较差。

* [Draw a Smooth Curve through a Set of 2D Points with Bezier Primitives](https://www.codeproject.com/Articles/31859/Draw-a-Smooth-Curve-through-a-Set-of-2D-Points-wit)。

## 参考链接

1. [Bezier Splines](https://docs.microsoft.com/en-us/windows/win32/gdiplus/-gdiplus-bezier-splines-about)
2. [GDI+ curve “overflowing”](https://stackoverflow.com/questions/30791706/gdi-curve-overflowing/49693235)

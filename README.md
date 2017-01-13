# Phase-Field-Method
## 摘要
   用Fortran 90实现了Phase Field Method(PS)界面捕捉方法，通过计算Rotation of a Zalesak disk问题，进行验证和分析。
## 一、Rotation of a Zalesak disk问题
   Zalesak 圆盘旋转问题是一个二维验证算例，初始设置如下图所示，流场是绕圆心旋转的定常流场。在这个问题中，四个尖角是难点，在圆盘旋转整数个周期 T 后，四个尖角应该依然存在。这个算例可以很明显的测试出界面捕捉方法对于尖角捕捉的准确性。

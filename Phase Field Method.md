# Phase-Field-Method
## 摘要
用Fortran 90实现了Phase Field Method(PS)界面捕捉方法，通过计算Rotation of a Zalesak disk问题，进行验证和分析。

## 一、Rotation of a Zalesak disk问题
Zalesak 圆盘旋转问题是一个二维验证算例，初始设置如下图所示，流场是绕圆心旋转的定常流场。在这个问题中，四个尖角是难点，在圆盘旋转整数个周期 T 后，四个尖角应该依然存在。这个算例可以很明显的测试出界面捕捉方法对于尖角捕捉的准确性。

![](https://github.com/KhalilWong/Phase-Field-Method/blob/master/Pic/%E5%9B%BE%E7%89%871.png?raw=true)

图中红色线代表界面位置，其中相关参数如下：
- 计算域：1 X 1
- 圆心： O（0.5，0.5）
- 半径： R=0.4
- 矩形上两个端点： A（0.4,0.7）, B（0.6,0.7）
- 速度场为： u =-2π(y-0.5) ，v = 2π(x-0.5)

## 二、初始化
以平衡态下体积分数的分布来初始化相场，圆形或者直线的平衡态容易由公式

> ![](https://github.com/KhalilWong/Phase-Field-Method/blob/master/Pic/%E5%9B%BE%E7%89%872.png?raw=true)

得出，其中ε是表征界面厚度的量。将初始计算区域根据界面特性划分为9个区域，分别进行初始化，如图所示：

![](https://github.com/KhalilWong/Phase-Field-Method/blob/master/Pic/%E5%9B%BE%E7%89%873.png?raw=true)

预期结果：

![](https://github.com/KhalilWong/Phase-Field-Method/blob/master/Pic/%E5%9B%BE%E7%89%874.png?raw=true)

##三、界面推进
PF方法直接推进界面方程即可，无需界面重新初始化或者界面重构。  
界面推进步骤：
- 使用简单迎风格式来得到对流项在网格每条边上的通量，从而得到对流项![](https://github.com/KhalilWong/Phase-Field-Method/blob/master/Pic/%E5%9B%BE%E7%89%875.png?raw=true)
- 使用中心差分格式来得到体积分数的二阶偏导![](https://github.com/KhalilWong/Phase-Field-Method/blob/master/Pic/%E5%9B%BE%E7%89%876.png?raw=true)
- 计算出每个网格上的化学势![](https://github.com/KhalilWong/Phase-Field-Method/blob/master/Pic/%E5%9B%BE%E7%89%877.png?raw=true)
- 使用中心差分格式来得到化学势的二阶偏导![](https://github.com/KhalilWong/Phase-Field-Method/blob/master/Pic/%E5%9B%BE%E7%89%878.png?raw=true)
- 显示推进：![](https://github.com/KhalilWong/Phase-Field-Method/blob/master/Pic/%E5%9B%BE%E7%89%879.png?raw=true)

##四、数值计算步骤
1. 初始化  
2. 体积分数推进方程  
3. 保证体积分数大于0并且小于1  
4. 重复2、3两步

##五、计算结果与分析
###5.1 网格收敛性验证
分别选取了50X50,100X100,200X200三种网格尺度进行初始化过程，分别如图所示：

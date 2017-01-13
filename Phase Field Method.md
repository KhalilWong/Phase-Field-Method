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
- 计算出每个网格上的化学势

> ![](https://github.com/KhalilWong/Phase-Field-Method/blob/master/Pic/%E5%9B%BE%E7%89%877.png?raw=true)

- 使用中心差分格式来得到化学势的二阶偏导![](https://github.com/KhalilWong/Phase-Field-Method/blob/master/Pic/%E5%9B%BE%E7%89%878.png?raw=true)
- 显示推进:

> ![](https://github.com/KhalilWong/Phase-Field-Method/blob/master/Pic/%E5%9B%BE%E7%89%879.png?raw=true)

##四、数值计算步骤
1. 初始化  
2. 体积分数推进方程  
3. 保证体积分数大于0并且小于1  
4. 重复2、3两步

##五、计算结果与分析
###5.1 网格收敛性验证
分别选取了50X50,100X100,200X200三种网格尺度进行初始化过程，分别如图所示：
- **50X50**

![](https://github.com/KhalilWong/Phase-Field-Method/blob/master/Pic/%E5%9B%BE%E7%89%8710.png?raw=true)

- **100X100**

![](https://github.com/KhalilWong/Phase-Field-Method/blob/master/Pic/%E5%9B%BE%E7%89%8711.png?raw=true)

- **200X200**

![](https://github.com/KhalilWong/Phase-Field-Method/blob/master/Pic/%E5%9B%BE%E7%89%8712.png?raw=true)

**三种网格比较图：**

![](https://github.com/KhalilWong/Phase-Field-Method/blob/master/Pic/%E5%9B%BE%E7%89%8713.png?raw=true)

由图可以看出，在200X200时，网格就可以认为已经收敛。

###5.2 随时间变化的计算结果
采用网格200X200，以C=0.5作为界面位置，局限于计算量设置

> ![](https://github.com/KhalilWong/Phase-Field-Method/blob/master/Pic/%E5%9B%BE%E7%89%8718.png?raw=true)

> ![](https://github.com/KhalilWong/Phase-Field-Method/blob/master/Pic/%E5%9B%BE%E7%89%8719.png?raw=true)

**t=1T时：**

![](https://github.com/KhalilWong/Phase-Field-Method/blob/master/Pic/%E5%9B%BE%E7%89%8714.png?raw=true)

**t=2T时：**

![](https://github.com/KhalilWong/Phase-Field-Method/blob/master/Pic/%E5%9B%BE%E7%89%8715.png?raw=true)

**t=3T时：**

![](https://github.com/KhalilWong/Phase-Field-Method/blob/master/Pic/%E5%9B%BE%E7%89%8716.png?raw=true)

**t=0、1、2、3T对比图：**

![](https://github.com/KhalilWong/Phase-Field-Method/blob/master/Pic/%E5%9B%BE%E7%89%8717.png?raw=true)

###5.3 简要分析
由结果可以看出，随着时间的推进，界面的局部尖角特征逐渐趋于圆滑，直线部分也存在一定的变形；且整体的体积也有一定程度的耗散。部分原因应该是离散方法有误，导致界面变形，有待改进。

##代码
'
PROGRAM phase_field
implicit none
real :: pi,e,et,yl,t
integer ::n,i,j,m
character (len=25)::fname
real,dimension (200):: x
real,dimension (200):: y
real,dimension (200,200):: C
real,dimension (0:201,0:201):: CC
real,dimension (0:201,0:201):: U
real,dimension (0:201,0:201):: V
real,dimension (200,200):: UdC
real,dimension (200,200):: VdC
real,dimension (200,200):: FAI
real,dimension (0:201,0:201):: FFAI
real,dimension (200,200):: d2F

n=200
pi=3.14159265
e=1.0/n
et=e**2
yl=0.5-sqrt(0.4**2-0.1**2)

DO i=1,n

x(i)=(i-0.5)/n
y(i)=(i-0.5)/n
U(0:n+1,i)=-2*pi*((i-0.5)/n-0.5)
V(i,0:n+1)=2*pi*((i-0.5)/n-0.5)

END DO

C=0                                        !初始化C场

DO i=1,n
DO j=1,n
    
IF((y(j)<=0.7.AND.y(j)>=yl.AND.x(i)<=0.4).OR.(y(j)<=0.7.AND.y(j)>=yl.AND.x(i)>=0.6))THEN
C(i,j)=0.5+0.5*tanh(min(0.4-sqrt((x(i)-0.5)**2+(y(j)-0.5)**2),max(0.4-x(i),x(i)-0.6))/(2*1.414*e))
END IF

IF(x(i)<=0.6.AND.x(i)>=0.4.AND.y(j)>=0.7)THEN
C(i,j)=0.5+0.5*tanh(min(0.4-sqrt((x(i)-0.5)**2+(y(j)-0.5)**2),y(j)-0.7)/(2*1.414*e))
END IF

IF(x(i)<=0.6.AND.x(i)>=0.4.AND.y(j)<=0.7.AND.y(j)>=yl)THEN
C(i,j)=0.5+0.5*tanh(max(max(0.4-x(i),x(i)-0.6),y(j)-0.7)/(2*1.414*e))
END IF

IF((x(i)<=0.4.AND.y(j)>=0.7).OR.(x(i)>=0.6.AND.y(j)>=0.7))THEN
C(i,j)=0.5+0.5*tanh(min(0.4-sqrt((x(i)-0.5)**2+(y(j)-0.5)**2),min(sqrt((x(i)-0.4)**2+(y(j)-0.7)**2),sqrt((x(i)-0.6)**2+(y(j)-0.7)**2)))/(2*1.414*e))
END IF

IF(y(j)<=yl.AND.abs(0.5-x(i))>=0.1*(0.5-y(j))/sqrt(0.4**2-0.1**2))THEN
C(i,j)=0.5+0.5*tanh((0.4-sqrt((x(i)-0.5)**2+(y(j)-0.5)**2))/(2*1.414*e))
END IF

IF(y(j)<=yl.AND.abs(0.5-x(i))<0.1*(0.5-y(j))/sqrt(0.4**2-0.1**2))THEN
C(i,j)=0.5-0.5*tanh(min(sqrt((x(i)-0.4)**2+(y(j)-yl)**2),sqrt((x(i)-0.6)**2+(y(j)-yl)**2))/(2*1.414*e))
END IF

END DO
END DO


t=0                                                  !界面推进
m=0
DO WHILE(t<3)

CC=0                                                 !C场分别向外增加一格
DO i=0,n+1
DO j=0,n+1

IF(i>0.AND.i<n+1.AND.j>0.AND.j<n+1)THEN
CC(i,j)=C(i,j)
END IF

IF(i==0.AND.j>0.AND.j<n+1)THEN
CC(i,j)=2*C(i+1,j)-C(i+2,j)
END IF

IF(i==n+1.AND.j>0.AND.j<n+1)THEN
CC(i,j)=2*C(i-1,j)-C(i-2,j)
END IF

IF(j==0.AND.i>0.AND.i<n+1)THEN
CC(i,j)=2*C(i,j+1)-C(i,j+2)
END IF

IF(j==n+1.AND.i>0.AND.i<n+1)THEN
CC(i,j)=2*C(i,j-1)-C(i,j-2)
END IF

END DO
END DO


DO i=1,n
DO j=1,n

IF(U(i,j)>0)THEN                                  !迎风格式求对流项
UdC(i,j)=(U(i,j)*CC(i,j)-U(i-1,j)*CC(i-1,j))/e
ELSE
UdC(i,j)=(U(i+1,j)*CC(i+1,j)-U(i,j)*CC(i,j))/e
END IF

IF(V(i,j)>0)THEN
VdC(i,j)=(V(i,j)*CC(i,j)-V(i,j-1)*CC(i,j-1))/e
ELSE
VdC(i,j)=(V(i,j+1)*CC(i,j+1)-V(i,j)*CC(i,j))/e
END IF
                                                   !势函数
FAI(i,j)=CC(i,j)**3-1.5*CC(i,j)**2+0.5*CC(i,j)-e**2*((CC(i+1,j)+CC(i-1,j)-2*CC(i,j))+(CC(i,j+1)+CC(i,j-1)-2*CC(i,j)))/e**2

END DO
END DO


FFAI=0                                              !势函数分别向外增加一格
DO i=0,n+1
DO j=0,n+1

IF(i>0.AND.i<n+1.AND.j>0.AND.j<n+1)THEN
FFAI(i,j)=FAI(i,j)
END IF

IF(i==0.AND.j>0.AND.j<n+1)THEN
FFAI(i,j)=2*FAI(i+1,j)-FAI(i+2,j)
END IF

IF(i==n+1.AND.j>0.AND.j<n+1)THEN
FFAI(i,j)=2*FAI(i-1,j)-FAI(i-2,j)
END IF

IF(j==0.AND.i>0.AND.i<n+1)THEN
FFAI(i,j)=2*FAI(i,j+1)-FAI(i,j+2)
END IF

IF(j==n+1.AND.i>0.AND.i<n+1)THEN
FFAI(i,j)=2*FAI(i,j-1)-FAI(i,j-2)
END IF

END DO
END DO


DO i=1,n
DO j=1,n
                                               !势函数2阶偏导
d2F(i,j)=((FFAI(i+1,j)+FFAI(i-1,j)-2*FFAI(i,j))+(FFAI(i,j+1)+FFAI(i,j-1)-2*FFAI(i,j)))/e**2

END DO
END DO

DO i=1,n
DO j=1,n
                                               !C场推进
C(i,j)=C(i,j)-et*(UdC(i,j)+VdC(i,j))+et*e*d2F(i,j)
C(i,j)=min(1.,max(0.,C(i,j)))

END DO
END DO

m=m+1
t=m*et
write(*,*)t

IF(mod(m,n**2)==0)THEN
write(fname,*)'output_C',m/n**2,'.txt'
open(17,file=Trim(fname))
do j=1,n
write(17,270)(C(:,j))
270 FORMAT (200F10.5)
end do
close(17)
END IF

END DO

!open(19,file="output_C.txt")
!do j=1,n
!write(19,290)(C(:,j))
!290 FORMAT (200F10.5)
!end do
!close(19)

stop
END PROGRAM'

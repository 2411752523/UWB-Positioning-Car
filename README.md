# 项目说明
视频链接：

https://www.bilibili.com/video/BV1Rh4y1g7G8

https://www.bilibili.com/video/BV1ko4y1J7MC

https://www.bilibili.com/video/BV1ps4y1y7U1

利用超宽带（UWB）定位技术，实现对智能车位置的实时获取和定位。上位机进行路径规划，并通过WIFI将坐标偏差信息发送给智能车，实现导航和控制。
系统使用了STM32F103RCT6作为智能车的主控芯片，通过舵机控制车辆的方向，H桥驱动电机旋转以实现车辆的前进和后退。编码器被应用于闭环控制，以确保车速的精确控制，使用ESP8266模块与上位机进行WIFI通信，实现了上位机对车辆的起跑和停止控制。
下面分以下三个部分进行说明。
## 1.UWB基站与标签
UWB代码没贴出来，因为是使用浩如科技的代码，自己太垃圾了，没移植成功官方的DEMO。其他部分都是自己一行一行敲出来的。
UWB的基站摆放如下图所示，充电宝供电，离地高度1.5m以上，每两个基站相隔5m以上，附近尽量不要有大面积玻璃金属的墙壁等，减少干扰。UP亲测，5m以上的测距误差基本在10cm以内，5m以内的话误差确实很大，最大能有1m多。
![image](https://github.com/2411752523/UWB-Positioning-Car/blob/main/Picture/%E5%9B%BE1.bmp)
图 1  UWB基站摆放示意图

这几个基站中的其中一个要与电脑连接，通过串口，将所有基站测得的距离发送给上位机，上位机在解算出坐标位置，如下图2所示。

![image](https://github.com/2411752523/UWB-Positioning-Car/blob/main/Picture/%E5%9B%BE2.bmp)
图 2  UWB基站摆放示意图

标签插在车上，定位车的位置，如下图3所示。

![image](https://github.com/2411752523/UWB-Positioning-Car/blob/main/Picture/%E5%9B%BE3.bmp)
图 3  UWB基站在车上

## 2.上位机
首先打开热点，需要小车与电脑连接在同一个WIFI下。打开上位机后，首先在左侧更新基站的坐标和小车行驶的终点，如果是四个基站，则勾选上A3，如下图4所示。坐标纸上，绿线是规划的路径，蓝点是基站的位置。

![image](https://github.com/2411752523/UWB-Positioning-Car/blob/main/Picture/%E5%9B%BE4.bmp)
图 4  上位机图

更新完基本信息后，需要选择串口号和波特率，还有端口号，确认选择后，打开设备，会提示成功与否，如下图5所示。这里有个小BUG，打开上位机之前要先插上设备，我上位机只在打开那一下扫描一下串口设备，所以打开之后再插上就不会被扫描到，由于本人太懒，没有改，就一直这么凑活使。

![image](https://github.com/2411752523/UWB-Positioning-Car/blob/main/Picture/%E5%9B%BE5.bmp) 
图 5  上位机图

提示：如果你WIFI通信不成功，可以检查一下你的防火墙。
当有数据时候，解析出来的位置就会打印在坐标纸上，左下角的临时按键是假数据，如果你没有数据输入，你可以点击一下它，看到他打印线的效果。打印的是粗虚线，但是平时使用时，由于移动速度慢，一般都是实线的效果。如下图6所示。

![image](https://github.com/2411752523/UWB-Positioning-Car/blob/main/Picture/%E5%9B%BE6.bmp)
图 6  位置轨迹图

如下图7所示，可以上下左右移动图像，可以放大缩小，可以清屏，清屏后，图像恢复初始大小的比例，坐标纸上的红色轨迹被清楚，其他不变。

![image](https://github.com/2411752523/UWB-Positioning-Car/blob/main/Picture/%E5%9B%BE7.bmp)
图 7  上位机图

## 3.小车
小车采用8.4V锂电池供电，供电电路板负责总体的供电，其负责降压5V、6V为系统供电，5V降3.3V为芯片供电，8.4V升12V为控制MOS开关，这里一个小BUG是NMOS做了上管，导致开驱动或者所需电流太大时，系统会重启，其实很大一部分原因也是因为降5V的DC-DC上的一个电阻太大，不过不影响系统的正常工作，还是太懒，就没改，一直凑活使。
主控板上为STM32F103RCT6及其外设接口。因为作品已经上交，这些都是之前做的老图。
具体模块都是什么，如下图8的标注。

![image](https://github.com/2411752523/UWB-Positioning-Car/blob/main/Picture/%E5%9B%BE8.bmp)
图 8  小车模块示意图

# 软件说明
差不多就是这个流程，画的不是很好，和实际代码的思路有点出入。测速、陀螺仪测角度和PWM的调节我都放在了定时器中断里。

![image](https://github.com/2411752523/UWB-Positioning-Car/blob/main/Picture/%E5%9B%BE9.bmp)
图 9  上位机代码流程图

![image](https://github.com/2411752523/UWB-Positioning-Car/blob/main/Picture/%E5%9B%BE10.bmp)
图 10  小车软件流程图

下图11所示的代码为调速，将pwmduty设为常数，就可以不闭环了。

![image](https://github.com/2411752523/UWB-Positioning-Car/blob/main/Picture/%E5%9B%BE11.bmp)
图 11  PID调速代码

# 项目属性
本项目为首次公开，为本人原创项目。本人华北理工大学电气工程学院，为本人的毕业设计，在此之前，项目未参加任何比赛。代码一堆屎山，仅供参考。

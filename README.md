# EBAZ4205

#### 介绍
EBAZ4205Linux移植笔记

#### 板子插图


#### 硬件资源

1.  主控：    ZYNQ XC7Z010-1CLG400I
2.  DDR：     128M x 16bit
3.  Flash：   128M的NAND FLASH
4.  网络：    1个百兆网口PL端
5.  串口：    2个串口（PS端TTL+PL端USB转232）
6.  蜂鸣器：  无源蜂鸣器1个
7.  LCD：    240*240分辨率 SPI接口IC：ST7789v
8.  LED：    5个（PS端2个，PL端3个）
9.  HDMI：   1路HDMI（PL端）
10. 按键：   7个按键（两个PS端1个未焊接，5个PL端）
11. SD卡座： 1个
12. 启动模式：SD卡启动

#### vivado硬件工程

采用vivado2018.3根据原理图配置接口资源

##### 1.外设配置

![输入图片说明](%E6%8F%92%E5%9B%BE/%E5%A4%96%E8%AE%BE%E9%85%8D%E7%BD%AE1.png)

![输入图片说明](%E6%8F%92%E5%9B%BE/%E5%A4%96%E8%AE%BE%E9%85%8D%E7%BD%AE2.png)

##### 2.IO配置

![输入图片说明](%E6%8F%92%E5%9B%BE/IO%E9%85%8D%E7%BD%AE1.png)

![输入图片说明](%E6%8F%92%E5%9B%BE/IO%E9%85%8D%E7%BD%AE2.png)

##### 3.管脚约束

![输入图片说明](%E6%8F%92%E5%9B%BE/%E7%AE%A1%E8%84%9A%E7%BA%A6%E6%9D%9F1.png)

![输入图片说明](%E6%8F%92%E5%9B%BE/%E7%AE%A1%E8%84%9A%E7%BA%A6%E6%9D%9F2.png)

##### 4.连线

![输入图片说明](%E6%8F%92%E5%9B%BE/EBAZ4205%E5%B7%A5%E7%A8%8B%E9%85%8D%E7%BD%AE.png)

##### 5.导出HDF文件

![输入图片说明](%E6%8F%92%E5%9B%BE/%E5%AF%BC%E5%87%BA%E9%85%8D%E7%BD%AE%E6%96%87%E4%BB%B6.png)

##### 6.导出bit文件

先编译工程，编译完成后导出bit文件

![输入图片说明](%E6%8F%92%E5%9B%BE/%E7%BC%96%E8%AF%91%E5%AF%BC%E5%87%BAbit.png)

![输入图片说明](%E6%8F%92%E5%9B%BE/%E7%BC%96%E8%AF%91%E5%AF%BC%E5%87%BAbit2.png)

![输入图片说明](%E6%8F%92%E5%9B%BE/%E7%BC%96%E8%AF%91%E5%AF%BC%E5%87%BAbit3.png)

#### 移植linux
采用Ubuntu16安装的Petalinux2018.3，移植过程主要参考了正点原子的教程




#### 特技



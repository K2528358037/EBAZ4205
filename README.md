# EBAZ4205学习笔记

板子为闲鱼购买“超级大电工”的板子，并带有扩展板，大佬附带有板子的使用资料和教程，链接：[你好，FPGA](http://www.hellofpga.com/)

![盗图一张 :smirk: ](%E5%AE%9E%E7%89%A9%E5%9B%BE.jpg)

#### 介绍
EBAZ4205Linux移植笔记

1. 移植采用petalinux移植linux系统到EBAZ4205

2. 利用framebuffer驱动板子上的LCD显示屏

3. 移植LVGL

4. 移植QT5

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
12. 启动模式：已修改为SD卡启动

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

1. 打开Linux终端，运行安装目录下的 settings.sh 来配置编译环境

2. 新建一个文件夹如：EBAZ4205，将vivado下导出的xxx.sdk文件夹拷贝到该目录下

3. 创建Petalinux工程

   创建一个名为zynq_linux的工程：`petalinux-create -t project --template zynq -n zynq_linux` 

4. 配置petalinux工程

   根据vivado导出的hdf文件配置工程

    `cd zynq_linux`

    `petalinux-config --get-hw-description ../EBAZ4205.sdk/`

    需要修改以下几项配置
    
    ![输入图片说明](%E6%8F%92%E5%9B%BE/config.png)

    串口配置

    进入 Subsystem AUTO Hardware Settings  --->  选择 Serial Settings  --->  我将UART0作为调试串口，配置如下：

    ![输入图片说明](%E6%8F%92%E5%9B%BE/%E4%B8%B2%E5%8F%A3%E9%85%8D%E7%BD%AE.png)

    配置Uboot、kernel为SD卡镜像
    ![输入图片说明](%E6%8F%92%E5%9B%BE/imagestorage.png)
   
    配置uboot 

    进入  u-boot Configuration  --->  此处修改为0x8000000
    ![输入图片说明](%E6%8F%92%E5%9B%BE/ubootconfig.png)

    配置rootfs为SD卡镜像

    进入 Image Packaging Configuration  ---> 
    ![输入图片说明](%E6%8F%92%E5%9B%BE/rootfstype.png)

    配置自动登录

    进入 Yocto Settings  --->
    ![输入图片说明](%E6%8F%92%E5%9B%BE/%E8%87%AA%E5%8A%A8%E7%99%BB%E5%BD%95.png)
#### 特技



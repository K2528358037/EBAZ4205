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

##### 串口配置

进入 Subsystem AUTO Hardware Settings  --->  选择 Serial Settings  --->  我将UART0作为调试串口，配置如下：

![输入图片说明](%E6%8F%92%E5%9B%BE/%E4%B8%B2%E5%8F%A3%E9%85%8D%E7%BD%AE.png)

##### 配置Uboot、kernel为SD卡镜像
    
进入 Subsystem AUTO Hardware Settings  ---> 选择  [*]   Advanced bootable images storage Settings  --->   

![输入图片说明](%E6%8F%92%E5%9B%BE/imagestorage.png)
   
##### 配置uboot 

进入  u-boot Configuration  --->  此处修改为0x8000000

![输入图片说明](%E6%8F%92%E5%9B%BE/ubootconfig.png)

##### 配置rootfs为SD卡镜像

进入 Image Packaging Configuration  ---> 
    
![输入图片说明](%E6%8F%92%E5%9B%BE/rootfstype.png)

##### 配置自动登录

进入 Yocto Settings  --->
    
![输入图片说明](%E6%8F%92%E5%9B%BE/%E8%87%AA%E5%8A%A8%E7%99%BB%E5%BD%95.png)

配置完成后，移动光标至`save`按回车进行保存，双击键盘上的`ESC`一步步退出终端，等待工程配置完成

![输入图片说明](%E6%8F%92%E5%9B%BE/%E4%BF%9D%E5%AD%98%E9%80%80%E5%87%BA.png)

##### 修改设备树

打开`project-spec/meta-user/recipes-bsp/device-tree/files/system-user.dtsi`文件添加自己的设备树

我在设备树中添加了两个led灯、5个按键以及LCD显示屏的描述（LCD的驱动在后续章节进行添加）

```
/include/ "system-conf.dtsi"

#define GPIO_ACTIVE_HIGH 1
#define GPIO_ACTIVE_LOW 0
/ {

	compatible = "EBAZ4205,zynq-7010","xlnx,zynq-7000";

	leds {
        	compatible = "gpio-leds";

       		led6_Red {
        		label = "led6_Red";
                	gpios = <&gpio0 63 0>;
                	default-state = "on";
        	};

        	led6_Green {
                	label = "led6_Green";
                	gpios = <&gpio0 62 0>;
                	linux,default-trigger = "cpu";
        	};
		
	};
	
	keys {
		compatible = "gpio-keys-polled";
		poll-interval = <20>;
		autorepeat;
	 
		S2{
			label = "S2";
			gpios = <&gpio0 20 GPIO_ACTIVE_LOW>;
			linux,code = <111>; // ESC
		};
		S3 {
                	label = "S3";
                	gpios = <&gpio0 34 GPIO_ACTIVE_LOW>;
                	linux,code = <158>; // 9
        	};
		key_up {
                	label = "key_up";
                	gpios = <&gpio0 57 GPIO_ACTIVE_HIGH>;
                	linux,code = <103>; // up
        	};
		key_left {
               		label = "key_left";
                	gpios = <&gpio0 58 GPIO_ACTIVE_HIGH>;
                	linux,code = <105>; // left
        	};
		key_ok {
                	label = "key_ok";
                	gpios = <&gpio0 59 GPIO_ACTIVE_HIGH>;
                	linux,code = <28>; // ok
        	};
		key_down {
        	        label = "key_down";
        	        gpios = <&gpio0 60 GPIO_ACTIVE_HIGH>;
        	        linux,code = <108>; // down
        	};
		key_right {
        	        label = "key_right";
        	        gpios = <&gpio0 61 GPIO_ACTIVE_HIGH>;
        	        linux,code = <106>; // right
       		};
	};

	amba: amba {
		u-boot,dm-pre-reloc;
		compatible = "simple-bus";
		#address-cells = <1>;
		#size-cells = <1>;
		interrupt-parent = <&intc>;
		ranges;
		
	slcr@f8000000 {
		pinctrl@700 {
				pinctrl_led_default: led-default {
					mux {
						groups = "gpio0_0_grp";
						function = "gpio0";
					};

					conf {
						pins = "MIO0";
						io-standard = <1>;
						bias-disable;
						slew-rate = <0>;
					};
				};
			};
		};

	 spi0: spi@e0006000 {
			compatible = "xlnx,zynq-spi-r1p6";
			reg = <0xe0006000 0x1000>;
			status = "okay";
			interrupt-parent = <&intc>;
			interrupts = <0 26 4>;
			clocks = <&clkc 25>, <&clkc 34>;
			clock-names = "ref_clk", "pclk";
			#address-cells = <1>;
			#size-cells = <0>;
			is-decoded-cs = <0x0>;
			num-cs = <0x1>;
			st7789v@0{
				 compatible = "darkmoon,st7789v";
				 reg = <0>;
				 status = "okay";
				 spi-max-frequency = <100000000>;
				 pinctrl-names = "default";
				 pinctrl-0 = <&pinctrl_led_default>;
				 lcd-bl = <&gpio0 54 0>;
				 lcd-dc = <&gpio0 55 0>;
				 lcd-res = <&gpio0 56 0>;
				 debug=<7>;
	 		};
		};

	};
	
};

```

##### 编译镜像

1.编译工程 `petalinux-buildls` 

2.合成启动文件 `petalinux-package --boot --fsbl ./images/linux/zynq_fsbl.elf --fpga ../EBAZ4205.sdk/EBAZ4205_wrapper_hw_platform_0/EBAZ4205_wrapper.bit --u-boot ./images/linux/u-boot.elf --force`

3.制作SDK 取一张小于32GB的内存卡，创建两个分区，第一个分区为boot 分区类型为FAT32(大小可为100MB)，剩余空间创建为第二个分区rootfs 分区类型为ext4

4.将 ./images/linux/ 下的BOOT.bin和image.ub这两个文件拷贝到boot分区中，将 ./images/linux/下的rootfs.tar.gz 解压到rootfs分区中，弹出SD卡插入板子的SD卡座上电，通过调试串口可登入linux终端

![输入图片说明](linux%E5%90%AF%E5%8A%A8.png)

#### 利用FrameBuffer驱动LCD屏幕

创建一个驱动 `petalinux-create -t modules --name st7789vdrv`

执行命令可以在`project-spec/meta-user/recipes-modules/st7789vdrv/files`下创建一个`st7789vdrv.c`文件，里面添加了一个驱动模板，直接将文件中的内容替换为如下内容：

```
#include <linux/interrupt.h>
#include <linux/workqueue.h>
#include <linux/device.h>
#include <linux/kernel.h>
#include <linux/slab.h>
#include <linux/sysfs.h>
#include <linux/list.h>
#include <linux/spi/spi.h>
#include <linux/err.h>
#include <linux/module.h>
#include <linux/of_gpio.h>
#include <linux/delay.h>
#include <linux/fb.h>
#include <linux/dma-mapping.h>
#include <linux/time.h>
#include <linux/timer.h>

#define LCD_H 240
#define LCD_W 240

struct st7789v_char_dev{
    	const char *       	name;
	int 		   	major;
	struct class       	*class;            //类
	struct device_node 	*nd;               //设备树的设备节点
	struct spi_device  	*spidevice;
	struct spi_driver  	*spidriver;
	struct file_operations 	*fops;
	int                	lcd_bl;    //gpio号
	int                	lcd_dc;    //gpio号
	int                	lcd_res;    //gpio号
};
static struct timer_list tm;
struct timeval oldtv;

/* 声明设备结构体 */
static struct st7789v_char_dev st7789v_dev = {
    .name = "st7789v",
};
static struct task_struct *lcd_kthread;
static unsigned int pseudo_palette[16];

static struct fb_info *oled_fb_info;
unsigned short GRAM[240*240]={0};

static void lcd_write_cmd_data(unsigned char uc_data,unsigned char uc_cmd)
{
	if(uc_cmd==0)
	{
		gpio_set_value(st7789v_dev.lcd_dc, !!0);
	}
	else
	{
		gpio_set_value(st7789v_dev.lcd_dc, 1);
	}
	spi_write(st7789v_dev.spidevice, &uc_data, 1);
	gpio_set_value(st7789v_dev.lcd_dc, 1);
}

static void lcd_write_datas(unsigned char *buf, int len)
{
	spi_write(st7789v_dev.spidevice, buf, len);
}

void LCD_WR_REG(unsigned char uc_data)
{
	lcd_write_cmd_data(uc_data,0);
}

void LCD_WR_DATA8(unsigned char uc_data)
{
	lcd_write_cmd_data(uc_data,1);
}

 void LCD_WR_DATA(u16 dat)
{
    u8 spi_dat[2];
    spi_dat[0]=dat>>8;
    spi_dat[1]=dat;
	
    lcd_write_datas(spi_dat,2);
}
/*
void set_pont(unsigned int x,unsigned int y,unsigned short data)
{
	unsigned char *p=(unsigned char *)&data;
	GRAM[x][y] = (unsigned short)(p[0]<<8)|(p[1]);
	
}
*/
void Address_set(unsigned int x1,unsigned int y1,unsigned int x2,unsigned int y2)
{
   LCD_WR_REG(0x2a);
   LCD_WR_DATA8(x1>>8);
   LCD_WR_DATA8(x1);
   LCD_WR_DATA8(x2>>8);
   LCD_WR_DATA8(x2);
   LCD_WR_REG(0x2b);
   LCD_WR_DATA8(y1>>8);
   LCD_WR_DATA8(y1);
   LCD_WR_DATA8(y2>>8);
   LCD_WR_DATA8(y2);
   LCD_WR_REG(0x2C);
}

void Lcd_Init(void)
{
	gpio_set_value(st7789v_dev.lcd_bl, 0);//XGpioPs_WritePin(&Gpio, EMIO_LCD_BL, 0);
	gpio_set_value(st7789v_dev.lcd_bl, 0);//XGpioPs_WritePin(&Gpio, EMIO_LCD_BL, 0);
	gpio_set_value(st7789v_dev.lcd_res, 0);//XGpioPs_WritePin(&Gpio, EMIO_LCD_RES, 0);
	msleep(10);//delay();
	gpio_set_value(st7789v_dev.lcd_res, 1);//XGpioPs_WritePin(&Gpio, EMIO_LCD_RES, 1);
	msleep(10);//delay();
	LCD_WR_REG(0x36);
	LCD_WR_DATA8(0x00);
	LCD_WR_REG(0x3A);
	LCD_WR_DATA8(0x05);
	LCD_WR_REG(0xB2);
	LCD_WR_DATA8(0x0C);
	LCD_WR_DATA8(0x0C);
	LCD_WR_DATA8(0x00);
	LCD_WR_DATA8(0x33);
	LCD_WR_DATA8(0x33);
	LCD_WR_REG(0xB7);
	LCD_WR_DATA8(0x35);
	LCD_WR_REG(0xBB);
	LCD_WR_DATA8(0x19);
	LCD_WR_REG(0xC0);
	LCD_WR_DATA8(0x2C);
	LCD_WR_REG(0xC2);
	LCD_WR_DATA8(0x01);
	LCD_WR_REG(0xC3);
	LCD_WR_DATA8(0x12);
	LCD_WR_REG(0xC4);
	LCD_WR_DATA8(0x20);
	LCD_WR_REG(0xC6);
	LCD_WR_DATA8(0x0F);
	LCD_WR_REG(0xD0);
	LCD_WR_DATA8(0xA4);
	LCD_WR_DATA8(0xA1);
	LCD_WR_REG(0xE0);
	LCD_WR_DATA8(0xD0);
	LCD_WR_DATA8(0x04);
	LCD_WR_DATA8(0x0D);
	LCD_WR_DATA8(0x11);
	LCD_WR_DATA8(0x13);
	LCD_WR_DATA8(0x2B);
	LCD_WR_DATA8(0x3F);
	LCD_WR_DATA8(0x54);
	LCD_WR_DATA8(0x4C);
	LCD_WR_DATA8(0x18);
	LCD_WR_DATA8(0x0D);
	LCD_WR_DATA8(0x0B);
	LCD_WR_DATA8(0x1F);
	LCD_WR_DATA8(0x23);
	LCD_WR_REG(0xE1);
	LCD_WR_DATA8(0xD0);
	LCD_WR_DATA8(0x04);
	LCD_WR_DATA8(0x0C);
	LCD_WR_DATA8(0x11);
	LCD_WR_DATA8(0x13);
	LCD_WR_DATA8(0x2C);
	LCD_WR_DATA8(0x3F);
	LCD_WR_DATA8(0x44);
	LCD_WR_DATA8(0x51);
	LCD_WR_DATA8(0x2F);
	LCD_WR_DATA8(0x1F);
	LCD_WR_DATA8(0x1F);
	LCD_WR_DATA8(0x20);
	LCD_WR_DATA8(0x23);
	LCD_WR_REG(0x21);
	LCD_WR_REG(0x11);
	LCD_WR_REG(0x29);
	gpio_set_value(st7789v_dev.lcd_bl, 1);//XGpioPs_WritePin(&Gpio, EMIO_LCD_BL, 1);
}

static int request_one_gpio(struct device_node *node,char *name,int index)
{
	//int of_get_named_gpio(struct device_node *np, const char *propname, int index);
	/* 获取节点中gpio标号 */
	int gpio,ret;
	gpio = of_get_named_gpio(node,name,index);
	if(gpio < 0)
	{
		printk("can not get %s",name);
		return -EINVAL;
	}
	else
	{
		printk("%s num = %d\r\n",name,gpio);
	
		/* 申请gpio标号对应的引脚 */
		ret = gpio_request(gpio, name);
		if(ret != 0)
		{
			printk("can not request %s\r\n",name);
		}
	
		/* 把这个io设置为输出 */
		ret = gpio_direction_output(gpio, 1);
		if(ret < 0)
		{
			printk("can not set %s\r\n",name);
		}
	}
	return gpio;
}  

static long st7789v_ioctl(struct file *file, unsigned int cmd, unsigned long arg)
{

	return 0;
}

/* 定义自己的file_operations结构体                                              */
static struct file_operations st7789v_fops = {
	.owner	 = THIS_MODULE,
	.unlocked_ioctl = st7789v_ioctl,
};

void myfb_del(void) //此函数在spi设备驱动remove时被调用
{
    kthread_stop(lcd_kthread); //让刷图线程退出
    unregister_framebuffer(oled_fb_info);
    dma_free_coherent(NULL, oled_fb_info->screen_size, oled_fb_info->screen_base, oled_fb_info->fix.smem_start);
    framebuffer_release(oled_fb_info);
}
static inline unsigned int chan_to_field(unsigned int chan, struct fb_bitfield *bf)
{
	chan &= 0xffff;
	chan >>= 16 - bf->length;
	return chan << bf->offset;
}

static int tft_lcdfb_setcolreg(unsigned int regno, unsigned int red,
			     unsigned int green, unsigned int blue,
			     unsigned int transp, struct fb_info *info)
{
	unsigned int val;
	
	if (regno > 16)
	{
		return 1;
	}

	/* 用red,green,blue三原色构造出val  */
	val  = chan_to_field(red,	&info->var.red);
	val |= chan_to_field(green, &info->var.green);
	val |= chan_to_field(blue,	&info->var.blue);
	
	pseudo_palette[regno] = val;
	return 0;
}

void show_fb(struct fb_info *fbi)
{
    int x, y;
    unsigned int k;
    unsigned int *p = (unsigned int *)(fbi->screen_base);
    unsigned short c;
    unsigned char *pp;
    unsigned short *memory = GRAM;
    Address_set(0,0,LCD_W-1,LCD_H-1);
    for (y = 0; y < fbi->var.yres; y++)
    {
        for (x = 0; x < fbi->var.xres; x++)
        {
            k = p[y*fbi->var.xres+x];//取出一个像素点的32位数据
            // rgb8888 --> rgb565       
            pp = (unsigned char *)&k;
            c = pp[0] >> 3; //蓝色
            c |= (pp[1]>>2)<<5; //绿色
            c |= (pp[2]>>3)<<11; //红色
            //发出像素数据的rgb565
            *((unsigned short *)memory+y*fbi->var.yres+x) = ((c&0xff)<<8)|((c&0xff00)>>8);
        }
    }
	spi_write(st7789v_dev.spidevice,memory,115200);
}
static int lcd_thread(void *data)
{
/*	unsigned char i,j,cnt=0;
	unsigned short temp[2]={WHITE,BLUE};*/
	while (1)
	{
		/* 把Framebuffer的数据刷到OLED上去 */
		show_fb(oled_fb_info);cnt++;
		set_current_state(TASK_INTERRUPTIBLE);
		schedule_timeout(HZ/100);

		if (kthread_should_stop()) {
			set_current_state(TASK_RUNNING);
			break;
		}
	}
	return 0;	
}
static struct fb_ops myfb_ops = {
	.owner		= THIS_MODULE,
	.fb_setcolreg	= tft_lcdfb_setcolreg,
	.fb_fillrect	= cfb_fillrect,
	.fb_copyarea	= cfb_copyarea,
	.fb_imageblit	= cfb_imageblit,
};
static int fb_init(struct spi_device *spi)
{	
	u32  phy_addr;

	/* 分配/设置/注册 fb_info */
	oled_fb_info = framebuffer_alloc(0, &spi->dev);


	/* 1.2 设置fb_info */
	/* a. var : LCD分辨率、颜色格式 */
	oled_fb_info->var.xres_virtual = oled_fb_info->var.xres = 240;
	oled_fb_info->var.yres_virtual = oled_fb_info->var.yres = 240;
	
	oled_fb_info->var.bits_per_pixel = 32;  /* rgb565 */
	oled_fb_info->var.red.offset = 16;
        oled_fb_info->var.red.length = 8;
        oled_fb_info->var.green.offset = 8;
        oled_fb_info->var.green.length = 8;
        oled_fb_info->var.blue.offset = 0;
        oled_fb_info->var.blue.length = 8;
    
	/* b. fix */
	strcpy(oled_fb_info->fix.id, "darkmoon_lcd");
	oled_fb_info->fix.smem_len = oled_fb_info->var.xres * oled_fb_info->var.yres * oled_fb_info->var.bits_per_pixel / 8;
	
	/* c. 分配显存 */
	oled_fb_info->screen_base = dma_alloc_wc(NULL, oled_fb_info->fix.smem_len, &phy_addr,
					 GFP_KERNEL);
	oled_fb_info->fix.smem_start = phy_addr;  /* fb的物理地址 */
	oled_fb_info->screen_size = oled_fb_info->fix.smem_len;

	oled_fb_info->fix.type = FB_TYPE_PACKED_PIXELS;
	oled_fb_info->fix.visual = FB_VISUAL_TRUECOLOR;

	oled_fb_info->fix.line_length = oled_fb_info->var.xres * oled_fb_info->var.bits_per_pixel / 8;

	/* d. fbops */
	oled_fb_info->fbops = &myfb_ops;
	oled_fb_info->pseudo_palette = pseudo_palette;	

	register_framebuffer(oled_fb_info);

	return 0;
}
static int st7789v_probe(struct spi_device *spi)
{	
	printk("%s %s %d\n", __FILE__, __FUNCTION__, __LINE__);
	
	st7789v_dev.spidevice = spi;
	
	st7789v_dev.nd = of_find_node_by_path("/amba/spi@e0006000/st7789v@0");
	if(st7789v_dev.nd == NULL)
	{
		printk("alinx_char node not find\r\n");
		return -EINVAL;
	}
	else
	{
		printk("alinx_char node find\r\n");
	}

	spi->mode = SPI_MODE_2;	/*MODE0，CPOL=0，CPHA=0 */
	spi->max_speed_hz = of_get_named_gpio(st7789v_dev.nd,"spi-max-frequency",0); 
	spi_setup(spi); 

	st7789v_dev.lcd_bl=request_one_gpio(st7789v_dev.nd,"lcd-bl",0);
	st7789v_dev.lcd_dc=request_one_gpio(st7789v_dev.nd,"lcd-dc",0);
	st7789v_dev.lcd_res=request_one_gpio(st7789v_dev.nd,"lcd-res",0);
	/* register_chrdev */
	st7789v_dev.major = register_chrdev(0, st7789v_dev.name , st7789v_dev.fops);  

	/* class_create */
	st7789v_dev.class = class_create(THIS_MODULE, st7789v_dev.name);

	/* device_create */
	device_create(st7789v_dev.class, NULL, MKDEV(st7789v_dev.major, 0), NULL,st7789v_dev.name);

	fb_init(spi);
	msleep(10);
	Lcd_Init();
	LCD_Test();
	lcd_kthread = kthread_create(lcd_thread, NULL, "darkmoon_lcd");
	wake_up_process(lcd_kthread);
	return 0;
}

static int st7789v_remove(struct spi_device *spi)
{
	myfb_del();
	device_destroy(st7789v_dev.class, MKDEV(st7789v_dev.major, 0));
	class_destroy(st7789v_dev.class);
	unregister_chrdev(st7789v_dev.major, st7789v_dev.name);

	return 0;
}

static const struct of_device_id st7789v_of_match[] = {
	{.compatible = "darkmoon,st7789v"},
	{}
};

static struct spi_driver st7789v_driver = {
	.driver = {
		.name	= "st7789v",
		.of_match_table = st7789v_of_match,
	},
	.probe		= st7789v_probe,
	.remove		= st7789v_remove,
};

int st7789v_init(void)
{
	st7789v_dev.spidriver=&st7789v_driver;
	st7789v_dev.fops=&st7789v_fops;
	return spi_register_driver(st7789v_dev.spidriver);
}

static void st7789v_exit(void)
{
	spi_unregister_driver(st7789v_dev.spidriver);
}

module_init(st7789v_init);
module_exit(st7789v_exit);

MODULE_LICENSE("GPL v2");

```
在设备树`system-user.dtsi`文件中添加相关驱动，



```
	amba: amba {
		u-boot,dm-pre-reloc;
		compatible = "simple-bus";
		#address-cells = <1>;
		#size-cells = <1>;
		interrupt-parent = <&intc>;
		ranges;
		
	slcr@f8000000 {
		pinctrl@700 {
				pinctrl_led_default: led-default {
					mux {
						groups = "gpio0_0_grp";
						function = "gpio0";
					};

					conf {
						pins = "MIO0";
						io-standard = <1>;
						bias-disable;
						slew-rate = <0>;
					};
				};
			};
		};

	 spi0: spi@e0006000 {
			compatible = "xlnx,zynq-spi-r1p6";
			reg = <0xe0006000 0x1000>;
			status = "okay";
			interrupt-parent = <&intc>;
			interrupts = <0 26 4>;
			clocks = <&clkc 25>, <&clkc 34>;
			clock-names = "ref_clk", "pclk";
			#address-cells = <1>;
			#size-cells = <0>;
			is-decoded-cs = <0x0>;
			num-cs = <0x1>;
			st7789v@0{
				 compatible = "darkmoon,st7789v";
				 reg = <0>;
				 status = "okay";
				 spi-max-frequency = <100000000>;
				 pinctrl-names = "default";
				 pinctrl-0 = <&pinctrl_led_default>;
				 lcd-bl = <&gpio0 54 0>;
				 lcd-dc = <&gpio0 55 0>;
				 lcd-res = <&gpio0 56 0>;
	 		};
		};

	};
```



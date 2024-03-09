# 树莓派各版本及其配置

<img src=".\资源\树莓派家族.png" alt="树莓派家族" style="zoom: 67%;" />

# 远程登陆

tf（micro sd）卡插入读卡器，然后在tf卡中其中添加几个文件。

## 添加wifi配置

添加文件"[wpa_supplicant.conf](.\资源\wpa_supplicant.conf)"

其内容为

```shell
country=CN
ctrl_interface=DIR=/var/run/wpa_supplicant GROUP=netdev
update_config=1

network={
	ssid="wifi名称"
	psk="wifi密码"
	key_mgmt=WPA-PSK
	priority=1
}

#添加多个wifi
#network={
# 	ssid="lalala"
# 	psk="22222222"
#	key_mgmt=WPA-PSK
#	priority=2
#}
```

- ssid后填入wifi名称

- psk后填入wifi密码

这样，树莓派会在**开机后自动连接**指定的wifi。

## 添加ssh

在tf卡中添加名为”ssh“的文件，里面不需要写内容，空文件即可。注意该文件不需要后缀，文件全名就叫“ssh”。

这样，树莓派就会开启ssh服务器。

只要树莓派连接上自己的wifi（树莓派与笔记本处在同一局域网中），就可以通过ssh远程访问树莓派。

## 添加默认密码

刚烧录的系统中默认存在一个用户pi，该用户可能没有密码，会导致ssh连接时密码验证失败（因为用户没有密码，所以输入什么密码都是错误的）。

在tf卡中添加文件[userconf.txt](.\资源\userconf.txt)

其内容为：

```txt
pi:$6$/4.VdYgDm7RJ0qM1$FwXCeQgDKkqrOU3RIRuDSKpauAbBvP11msq9X58c8Que2l1Dwq3vdJMgiZlQSbEXGaY5esVHGBNbCxKLVNqZW1
```

表示默认pi用户的密码为：**raspberry**。

## 开启VNC

有了上述的操作，在树莓派开启后，会自动连接上指定的wifi，如果笔记本也连接这个wifi，就可以在笔记本的终端中（windows的cmd）使用ssh远程连接树莓派，这时就可以通过终端命令来操纵树莓派了。

输入终端命令

```bash
sudo raspi-config
```

就能在终端中进入树莓派的配置界面，进入interface options，光标移动到VNC然后回车，选择yes，开启VNC服务器。然后一直Esc退出配置界面，返回终端。

注意：

- 配置界面中Esc表示返回。
- 回车表示确认。
- 方向键上下左右可以移动光标。
- tab键可以把光标切换到最下面的两个选项中。

# 更新软件源

需要使用国内的镜像软件源，才能保证使用apt命令下载和更新软件时网络畅通。

要更换软件源，要改写两个系统文件。

## 一、

需要更改树莓派系统中**`/etc/apt/sources.list`**中内容（建议修改前先把这个文件复制出来一份当作备份）

通过下面的网址，选择树莓派系统的版本，来生成sources.list文件中需要写入的内容。

[raspbian | 镜像站使用帮助 | 清华大学开源软件镜像站 | Tsinghua Open Source Mirror](https://mirrors.tuna.tsinghua.edu.cn/help/raspbian/)

查看树莓派系统版本：

```bash
lsb_release -a
```

## 二、

同样的方法，这次更改**`/etc/apt/sources.list.d/raspi.list`**中的内容

[raspberrypi | 镜像站使用帮助 | 清华大学开源软件镜像站 | Tsinghua Open Source Mirror](https://mirrors.tuna.tsinghua.edu.cn/help/raspberrypi/)

# GPIO

查看GPIO引脚编号与作用

<img src=".\资源\树莓派GPIO.jpg" alt="树莓派GPIO" style="zoom: 80%;" />



## 查看gpio

### 板载gpio引脚编号

终端命令：

```bash
pinout  # 查看板载编号
```

执行结果：

```
,--------------------------------.
| oooooooooooooooooooo J8     +====
| 1ooooooooooooooooooo        | USB
|                             +====
|      Pi Model 3B  V1.2         |
|      +----+                 +====
| |D|  |SoC |                 | USB
| |S|  |    |                 +====
| |I|  +----+                    |
|                   |C|     +======
|                   |S|     |   Net
| pwr        |HDMI| |I||A|  +======
`-| |--------|    |----|V|-------'

Revision           : a22082
SoC                : BCM2837
RAM                : 1GB
Storage            : MicroSD
USB ports          : 4 (of which 0 USB3)
Ethernet ports     : 1 (100Mbps max. speed)
Wi-fi              : True
Bluetooth          : True
Camera ports (CSI) : 1
Display ports (DSI): 1

J8:
   3V3  (1) (2)  5V
 GPIO2  (3) (4)  5V
 GPIO3  (5) (6)  GND
 GPIO4  (7) (8)  GPIO14
   GND  (9) (10) GPIO15
GPIO17 (11) (12) GPIO18
GPIO27 (13) (14) GND
GPIO22 (15) (16) GPIO23
   3V3 (17) (18) GPIO24
GPIO10 (19) (20) GND
 GPIO9 (21) (22) GPIO25
GPIO11 (23) (24) GPIO8
   GND (25) (26) GPIO7
 GPIO0 (27) (28) GPIO1
 GPIO5 (29) (30) GND
 GPIO6 (31) (32) GPIO12
GPIO13 (33) (34) GND
GPIO19 (35) (36) GPIO16
GPIO26 (37) (38) GPIO20
   GND (39) (40) GPIO21
```

### 查看所有gpio引脚编号

gpio引脚编号方式出了板载编号还有bcm编号和wiringpi编号。

终端命令：

```bash
gpio readall  # 查看所有的gpio引脚编号与功能（需要下载wiringpi库才能使用）
```

执行结果：

```bash
 +-----+-----+---------+------+---+---Pi 3B--+---+------+---------+-----+-----+
 | BCM | wPi |   Name  | Mode | V | Physical | V | Mode | Name    | wPi | BCM |
 +-----+-----+---------+------+---+----++----+---+------+---------+-----+-----+
 |     |     |    3.3v |      |   |  1 || 2  |   |      | 5v      |     |     |
 |   2 |   8 |   SDA.1 |   IN | 1 |  3 || 4  |   |      | 5v      |     |     |
 |   3 |   9 |   SCL.1 |   IN | 1 |  5 || 6  |   |      | 0v      |     |     |
 |   4 |   7 | GPIO. 7 |   IN | 1 |  7 || 8  | 0 | IN   | TxD     | 15  | 14  |
 |     |     |      0v |      |   |  9 || 10 | 1 | IN   | RxD     | 16  | 15  |
 |  17 |   0 | GPIO. 0 |   IN | 0 | 11 || 12 | 0 | IN   | GPIO. 1 | 1   | 18  |
 |  27 |   2 | GPIO. 2 |   IN | 0 | 13 || 14 |   |      | 0v      |     |     |
 |  22 |   3 | GPIO. 3 |   IN | 0 | 15 || 16 | 0 | IN   | GPIO. 4 | 4   | 23  |
 |     |     |    3.3v |      |   | 17 || 18 | 0 | IN   | GPIO. 5 | 5   | 24  |
 |  10 |  12 |    MOSI |   IN | 0 | 19 || 20 |   |      | 0v      |     |     |
 |   9 |  13 |    MISO |   IN | 0 | 21 || 22 | 0 | IN   | GPIO. 6 | 6   | 25  |
 |  11 |  14 |    SCLK |   IN | 0 | 23 || 24 | 1 | IN   | CE0     | 10  | 8   |
 |     |     |      0v |      |   | 25 || 26 | 1 | IN   | CE1     | 11  | 7   |
 |   0 |  30 |   SDA.0 |   IN | 1 | 27 || 28 | 1 | IN   | SCL.0   | 31  | 1   |
 |   5 |  21 | GPIO.21 |   IN | 1 | 29 || 30 |   |      | 0v      |     |     |
 |   6 |  22 | GPIO.22 |   IN | 1 | 31 || 32 | 0 | IN   | GPIO.26 | 26  | 12  |
 |  13 |  23 | GPIO.23 |   IN | 0 | 33 || 34 |   |      | 0v      |     |     |
 |  19 |  24 | GPIO.24 |   IN | 0 | 35 || 36 | 0 | IN   | GPIO.27 | 27  | 16  |
 |  26 |  25 | GPIO.25 |   IN | 0 | 37 || 38 | 0 | IN   | GPIO.28 | 28  | 20  |
 |     |     |      0v |      |   | 39 || 40 | 0 | IN   | GPIO.29 | 29  | 21  |
 +-----+-----+---------+------+---+----++----+---+------+---------+-----+-----+
 | BCM | wPi |   Name  | Mode | V | Physical | V | Mode | Name    | wPi | BCM |
 +-----+-----+---------+------+---+---Pi 3B--+---+------+---------+-----+-----+
```

## gpio控制方式



### 概述

主要控制gpio的**两个属性**：

- 方向：输出模式或者输入模式
- 大小：如果是输出模式，则输出高电平或者低电平

树莓派有几种控制gpio的**主要方式**：

- 操纵系统文件
- 使用python的rpi.gpio库
- 使用c语言的wiringpi库
- 终端命令

注意：

- python的rpi.gpio库和c语言的wiringpi库都是最常用的树莓派库，虽然不是官方编写的，但是是官方推荐使用的。

### 操纵系统文件

进入到目录`/sys/class/gpio`中，该目录下有两个文件：

- **export**：往该文件写入内容（gpio序号），就可以生成相应的gpio口的控制文件。
- **unexport**：往该文件写入内容（gpio序号），就可以“卸载掉”相应的gpio的控制文件。

例如：

如果要控制gpio4

```bash
echo 4 > export  # 写入“4”到文件export
# 就会在/sys/class目录下 生成目录gpio4
# 目录gpio4包含了控制gpio4的设备文件
```

注意：

- gpio4是指bcm编号下的gpio，不是wiringpi编号的gpio4。
- export和unexport是两个特殊的文件，是操作系统暴露给我们使用的接口，无法用cat或其他命令查看其内容。
- 使用完gpio，记得要卸载掉相应的控制文件，例如：echo 4 > unexport

目录/sys/class/gpio/gpio4中有两个文件：

- **direction**：往该文件写入内容（`in`或者`out`），来控制gpio是输出模式还是输入模式。
- **value**：输出模式下，往该文件写入内容（`1`或者`0`），来控制gpio输出高电平还是低电平。

例如：

```bash
# 加载gpio4的控制文件，并进去gpio4的控制目录
echo 4 > export
cd gpio4

# 控制gpio4
echo out > direction  # 设置gpio4为输出模式
echo 1 > value  # 设置gpio4输出高电平
echo 0 > value  # 设置gpio4输出低电平
echo in > direction  # 设置gpio4为输入模式

# 卸载gpio4的控制文件
cd ..
echo 4 > unexport  # 这条指令执行后，/sys/class/gpio目录恢复原样

```

### python库编程

在python脚本中使用rpi.gpio库可以控制树莓派的gpio口。

基本步骤：

- `import RPi.GPIO as GPIO`：导入库
- `GPIO.setmode(GPIO.BCM)`：设置gpio编号方式，编号方式除了BCM，还有BOARD（板载编号）
- `GPIO.setwarnings(False)`：取消警告
- `GPIO.setup(4, GPIO.OUT)`：设置工作模式（输入或者输出）
- `GPIO.output(4, GPIO.HIGH)`：设置gpio输出高低电平

例如：

```python
import RPi.GPIO as gpio

# 设置接下来要使用的gpio编号方式，并关闭警告
gpio.setmode(gpio.BCM)
gpio.setwarnings(False)

# 设置gpio4为输出模式，并输出低电平
gpio.setup(4, gpio.OUT)
gpio.output(4, gpio.LOW)
# gpio.setup(4, gpio.in)  # 设置gpio4为输入模式
# gpio.input(4)  # 读取gpio4
```

### wiringpi库编程

在c语言代码中导入wiringPi库可以控制树莓派得gpio口。

基本函数

- `wiringPiSetup();`：初始化。
- `pinMode(7, OUTPUT);`：设置gpio7为输出模式。（gpio7是在wiringPi编码下gpio7）
- `digitalWrite(7, HIGH);`：设置gpio7输出高电平。

例如：

```c
#include <wiringPi.h>
#include <stdio.h>

int main()
{
    // 初始化
    wiringPiSetup();  

    // 设置gpio7为输出模式。
    pinMode(7, OUTPUT);  

    // 设置gpio7输出高电平。
    printf("输出gpio7为高电平\n");
    digitalWrite(7, HIGH);  

    return 0;
}
```

注意：

- 使用wiringPi时，gpio的编号方式是wiringPi的编号方式，不是BCM编号方式。

- 库名称wiringPi得“p”是大写。
- 函数名wiringPiSetup的“s”是大写。

### 终端命令方式

gpio指令是wiringPi库的一部分，只要正确安装了wiringPi库才能在终端中使用gpio指令。

格式：`gpio [选项] 操作 [gpio序号 [操作参数]]` 

选项：

- -g：表示使用BCM编号方式，默认使用wiringPi编号方式。
- -v：查看该指令版本号

操作：

- readall：显示所有gpio的详细信息。
- mode：指定gpio的工作模式（输出或输入）。
- write：使得指定的gpio输出高/低电平。
- read：读取指定的gpio。

例如：

```bash
gpio -v  # 查看gpio的版本，如果没有报错，说明wiringPi安装成功。

gpio -g mode 4 in  # 设置gpio4（BCM编号）为输入模式。
gpio -g read 4  # 读入gpio4的电平状态。

gpio -g mode 4 out  # 设置gpio4（BCM编号）为输出模式。
# gpio mode 7 out  # 与上面这条命令等价，即wiringPi的gpio7 就是 BCM的gpio4。
gpio -g write 4 1  # 设置gpio4输出高电平。
```


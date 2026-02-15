# 一、工程模板

## 1.1 建立工程目录

工程目录结构：

- **`start`**：
  - stm32的**启动文件**（启动函数）：
    - `startup_xx.s`：
    - `system_xxx.c/.h`：
  - stm32的**寄存器定义**文件（就是一堆的宏定义）：
    - 内核寄存器定义：`core_cm3.c/.h`
    - 外设寄存器定义：`stm32f10x.h`
- **`lib`**：存储stm32的**库文件**
  - 内核库函数：`misc.h/.c`
  - 外设库函数：`stm32f10x_adc.c/.h`、`stm32f10x_can.c/.h`等。
- **`src`**：
  - 配置文件：`stm32f10x_conf.h`
  - 中断函数：`stm32f10x_it.c\.h`
  - 自定义函数：`main.c`等



## 1.2 keil中管理工程

### 1.2.1 在keil工程管理面板中复制目录层级

​		在文件夹中创建的各目录和文件与keil中工程管理面板并不是互通的，而是独立的。因此刚刚在文件夹中作了那么多修改，并不影响keil的工程管理面板（仍然是空的），为了方便管理，需要把工程目录中的文件都添加到工程管理面板中：

- 创建组：
  - 在Target1下右击：选择add group（新建组）并重命名，共添加三个组，对应工程目录下的三个文件夹；
    - `start`、`lib`、`src`
- 添加文件到组：
  - 在刚创建的组上右击（例如在src上右击）：选择`Add Existing Files to Group "src"`
  - 然后在跳出的文件选择窗口中选择组名对应的工程目录下的所有文件（按住Ctrl多选），然后点击`Add`。

### 1.2.2 配置工程选项

- 添加头文件目录：
  - 点击魔术棒按钮（Options for Target），
  - 选择c/c++选项卡，
  - 找到include Paths项：
    - 点击include Paths项中最右侧的省略号按钮，
    - 在新跳出的窗口中点击New(Insert) 按钮，
    - 再点击新建项的省略号按钮，
    - 在跳出的文件选择窗口中选择工程目录中包含头文件的目录，
    - 然后点击选择文件夹。

![](D:\!学习\笔记\notes\stm32笔记\图片\keil中添加头文件目录的方法.jpg)

- 解释：
  - 虽然我们已经把各种头文件都放入了工程目录下，而且也把这些头文件都加入到了keil的工程管理面板中，
  - 但是keil内置的编译器还是不知道哪些目录包含了头文件
    - keil只是知道了你用到了哪些头文件，
    - 但是keil并没有告诉编译器这些头文件在哪个目录下
  - 而编译器运行时（即我们执行编译时）需要知道头文件都在哪些目录，
    - 因此我们还要手动把包含头文件件的目录通过keil的选项卡告诉编译器。
  - 这确实很麻烦，但之所以这么做的理由涉及到编译器（c/c++编译器）的工作方式，所以先这么干吧。

### 1.2.3 添加宏定义

- 同样点击魔术棒按钮，再点击c/c++选项卡，
- 在Define项中输入：`USE_STDPERIPH_DRIVER`。

<img src="D:\!学习\笔记\notes\stm32笔记\图片\keil中添加宏定义.png" style="zoom:67%;" />

- 解释：

  - 这么做的效果其实跟在我们的main.c文件中的第一行添加`#define USE_STDPERIPH_DRIVER`，效果一样。

    - 只是把这个宏定义在这里而不是定义在main.c文件里是为了好看。

  - 定义这个宏的作用是启用标准外设库：

    - 我们的main.c文件中一般都会`#include"stm32f10x.h`，

    - 在stm32f10x.h中，有一段语句：

      ```c++
      #ifdef USE_STDPERIPH_DRIVER
      	#include "stm32f10x_conf.h"
      #endif
      ```

    - 而在stm32f10x_conf.h中，包含了所有标准外设库的头文件。

  - 即：

    - 如果我定义了`USE_STDPERIPH_DRIVER`，
    - 那在`stm32f10x.h`中就会包含`stm32f10x_conf.h`
    - 而`stm32f10x_conf.h`就又包含了所有外设头文件：`stm32f10x_adc.h`、`stm32f10x_can.h`等，
    - 所以这个宏的作用就是包含了所有的外设头文件。





# 二、GPIO

## 2.1 GPIO简介

​		GPIO是General Purpose Input/Output的缩写，意思为通用（目的）输入/输出（端口）。规定高电平为3.3v，低电平为0v，部分引脚可容忍5V。

根据需要，GPIO可以配置为输入或者输出模式：

- **输出模式**：可以控制端口输出高/低电平。
  - 应用：驱动LED、控制蜂鸣器、模拟通信协议时序等。
- **输入模式**：可读取端口的高低电平或电压。
  - 应用：读取按键输入、外接模块电平信号输入、ADC电压采集、模拟通信协议接收数据等。

## 2.2 GPIO结构

<img src="D:\!学习\笔记\notes\stm32笔记\图片\GPIO位结构.png" style="zoom:80%;" />

GPIO的其中一位的结构如上图所示，**主要组成**有：

- **IO端口**：
  - **保护二极管**：当IO引脚电压大于额定电压时，用于保护单片机不受损坏。
    - 当IO引脚上的电压大于3.3v时，电流会通过（上面的）保护二级管流入VDD，而不会流入单片机内部。
    - 当IO引脚上的电压小于0V时，电流会通过（下面的）保护二级管注入VSS，而不会注入单片机内部。
  - **IO引脚**：对应单板机上具体的一个引脚。
- **输入部分**：从左至右依次介绍
  - 驱动器部分：
    - **VDD开关与VSS开关**：
      - 当VDD闭合、VSS断开时，输入为**上拉输入**，即引脚默认值为1；
      - 当VDD断开、VSS闭合时，输入为**下拉输入**，即引脚默认值为0；
      - 当VDD断开、VSS也断开时，输入为**浮空输入**，即引脚默认值是不确定的；
      - 当VDD闭合，VSS也闭合时，会导致短路（VDD直通VSS），不存在这个功能。
    - **施密特触发器**（为图上的肖特基触发器，图上标注错误）：用于滤波
      - 施密特触发器规定上下限（假设下限为0.7v，上限为2.6v），
      - 当输入电压从小变大，且大于上限时，输出为高电平，
      - 当输入电压由大变小，且小于下限时，输出为低电平。
      - 这样避免了当高电压不稳定时（例如在2.6v上下波动），施密特触发器会输出稳定的高电平3.3v。
      - 低电平不稳定时也是同样的道理。
  - 寄存器部分：
    - **输入数据寄存器**：用于保存IO口的数据（电平）。
      - IO口的数据（电平）经过施密特触发器整形后传递到输入数据寄存器的输入端，
      - 然后经过一个时钟脉冲后，IO口的电平被锁存在了输入数据寄存器中，这样IO口的电平就被保存了下来，
      - 之后内核CPU可以读取输入数据寄存器的电平来获得该IO口的电平。
- **输出部分**：
  - 驱动器部分：
    - **P-MOS与N-MOS**：用于配置输出是高电平还是低电平。
      - 当P-MOS导通，N-MOS断开，VDD与IO口连接，输出高电平；
      - 当P-MOS断开，N-MOS导通，VSS与IO口连接，输出低电平；
      - 当P-MOS断开，N-MOS也断开，IO口关闭（悬空），不输出任何电平；
      - 当P-MOS导通，N-MOS导通，VDD与VSS短接，不存在这个功能。
    - **输出控制**：用于配置IO口的输出模式（通过控制两个MOS管的通断）
      - **推挽输出**：
        - 输出高电平：P-MOS导通，N-MOS断开；
        - 输出低电平：P-MOS断开，N-MOS导通。

      - **开漏输出**：
        - 输出高阻态：P-MOS断开，N-MOS断开；
        - 输出低电平：P-MOS断开，N-MOS导通。

      - 说明：
        - 推挽输出时VDD和VSS分别与IO口直连，因此有较强的驱动能力；
        - 开漏输出无法输出高电平，因此这个模式下的输出为这两种：输出低电平与不输出低电平，一般用于IIc等通信协议中。

  - 寄存器部分：
    - **输出数据寄存器**：与输入数据寄存器类似，用于输出数据（电平）给IO口。
      - 输出数据寄存器上低16位的值就对应了GPIO端口上的16个引脚的电平（高16位保留，为空）。
      - CPU将要输出给IO的电平先锁存在输出数据寄存器中，
      - 然后输出数据寄存器将保存的数据传递到输出控制上来实现IO口输出高低电平。

    - **位设置/清除寄存器**：
      - 由于输出数据寄存器是32位的，每次只能读写32位的数据，如果我只想GPIO操作某一个引脚，每次都要写32位的数据太过麻烦，因此用位设置/清除寄存器来简化操作。
      - 位设置寄存器：可以只将输出数据寄存器上某一位的值置为1；
      - 位清除寄存器：可以只将输出数据顺丰器上某一个的值置为0。

- **IO功能复用**：
  - **模拟输入**：片上外设直接读取IO输入进来的数据（不读取经过施密特触发器整形后的数据），就可以获得IO输入进来的模拟依赖。
  - **复用功能**：片上外设可以通过读取施密特触发器整形后的数据与写入输出控制器来实现对IO的控制（与CPU控制IO方式相同），从而实现片上外设与CPU共同使用这个IO口，所以也叫做IO口复用（共同使用）。




## 2.3 GPIO模式

根据IO上VDD开关与VSS开关的通断，及P-MOS管和N-MOS管的导通情况等，GPIO共有8种工作模式：

- 输入：
  - **上拉输入**：IO口无外接线路时，电平为高电平；
  - **下拉输入**：IO口无外接线路时，电平为低电平；
  - **浮空输入**：IO口无外接线路时，电平未知；
  - **模拟输入**：读取IO口的模拟输入；
- 输出：
  - **推挽输出**：高电平时接VDD，低电平时接VSS；
  - **开漏输出**：”高电平“时为高阻态，低电平时接VSS；
  - **复用推挽输出**：IO口为推挽输出模式，由片上外设控制IO口；
  - **复用开漏输出**：IO口为开漏输出模式，由片上外设控制IO口。



## 2.4 GPIO控制

控制GPIO一般要经过下面几个步骤：

- 启用GPIO

- 配置GPIO

- 使用GPIO

### 2.4.1 启用GPIO

​		GPIO是转载到APB2总线上的（也就是一组引脚默认是接到APB2总线上的），而APB2总线时钟默认是全部关闭的，如果没有时钟，GPIO就无法工作，因此如果要使用GPIO，用要先启动APB2上的GPIO时钟（也就是把系统的时钟线连接到GPIO的时钟线上）。

启动GPIO的需要使用`RCC_APB1PeriphClockCmd`函数：

- 定义：`void RCC_APB1PeriphClockCmd(uint32_t RCC_APB1Periph, FunctionalState NewState);`

- 作用：控制APB2上外设时钟的状态。

- 参数：

  - `RCC_APB1Periph`：外设名称，可以为
    - RCC_APB2Periph_GPIOA；
    - RCC_APB2Periph_GPIOB；
    - RCC_APB2Periph_GPIOC；
    - 还有很多，见附录。
  - `NewState`：启用状态
    - `DISABLE`：禁用；
    - `ENABLE`：启用。

- 示例：

  ```c
  // 启用GPIOA的时钟
  RCC_APB1PeriphClockCmd(RCC_APB2Periph_GPIOA, ENABLE);
  ```

> **RCC**：Reset and Colck Contrl，复位与时钟控制模块
>
> - stm32上基本上所有的时钟都由这个模块管理，
> - 因此要启用GPIO的时钟，也要通过RCC来启动。



### 2.4.2 配置GPIO

在启用了GPIO的时钟后，还需要使用`GPIO_Init`配置GPIO的工作模式：

- 定义：`void GPIO_Init(GPIO_TypeDef* GPIOx, GPIO_InitTypeDef* GPIO_InitStruct);`

- 作用：使用GPIO_InitStruct结构体的成员来初始化GPIO的工作模式。

- 参数：

  - `GPIOx`：GPIO端口名称

    - 可以为`GPIOA` - `GPIOG`。

  - `GPIO_InitStruct`：初始化结构体，其中包含了各种初始化参数，其中的成员有

    - `GPIO_Pin`：IO引脚名称
      - 可以为`GPIO_Pin_0` - `GPIO_Pin_15`，或者`GPIO_Pin_All`
      - 也可以同时指定多个引脚，用`|`来连接，例如：`GPIO_Pin_0 | GPIO_Pin_15 | GPIO_Pin_14`
      
    - `GPIO_Speed`：输出速度，可以为
      - `GPIO_Speed_10MHz`；
      
      -  `GPIO_Speed_2MHz`；
      
      - `GPIO_Speed_50MHz`。
      
    - `GPIO_Mode`：GPIO的工作模式，8种工作模式
      - `GPIO_Mode_AIN`：模拟输入；
      - `GPIO_Mode_IN_FLOATING`：浮空输入；
      - `GPIO_Mode_IPD`：下拉输入；
      - `GPIO_Mode_IPU`：上拉输入；
      - `GPIO_Mode_Out_OD`：开漏输出，”Open Drain“；
      - `GPIO_Mode_Out_PP`：推挽输出：“push pull”；
      - `GPIO_Mode_AF_OD`：复用开漏输出；
      - `GPIO_Mode_AF_PP`：复用推挽输出。
  
- 示例：

  ```c++
  GPIO_InitTypeDef GPIO_InitStruct;  // 申明一个GPIO初始化结构体
  GPIO_InitStruct.GPIO_Pin = GPIO_Pin_13;  // 指定初始化的引用为13脚
  GPIO_InitStruct.GPIO_Speed = GPIO_Speed_50MHz;  // 指定13脚的速度为50Mz
  GPIO_InitStruct.GPIO_Mode = GPIO_Mode_Out_PP;  // 指定13脚的工作模式为推挽输出
  GPIO_Init(GPIOC, &GPIO_InitStruct);  // 将初始化结构体传递给GPIO_Init函数
  ```
  
  - 注意：GPIO_Init函数传入的是GPIO_InitStruct的地址，而不是变量。

> GPIO_InitStructp定义：
>
> ```c
> typedef struct
> {
>   uint16_t GPIO_Pin;             /*!< Specifies the GPIO pins to be configured.
>                                       This parameter can be any value of @ref GPIO_pins_define */
> 
>   GPIOSpeed_TypeDef GPIO_Speed;  /*!< Specifies the speed for the selected pins.
>                                       This parameter can be a value of @ref GPIOSpeed_TypeDef */
> 
>   GPIOMode_TypeDef GPIO_Mode;    /*!< Specifies the operating mode for the selected pins.
>                                       This parameter can be a value of @ref GPIOMode_TypeDef */
> }GPIO_InitTypeDef;
> ```
>

### 2.4.3 使用GPIO

常用的GPIO操作函数有：

```c++
// GPIO输出：
void GPIO_SetBits(GPIO_TypeDef* GPIOx, uint16_t GPIO_Pin);
void GPIO_ResetBits(GPIO_TypeDef* GPIOx, uint16_t GPIO_Pin);
void GPIO_WriteBit(GPIO_TypeDef* GPIOx, uint16_t GPIO_Pin, BitAction BitVal);
void GPIO_Write(GPIO_TypeDef* GPIOx, uint16_t PortVal);

// GPIO输入：
uint8_t GPIO_ReadInputDataBit(GPIO_TypeDef* GPIOx, uint16_t GPIO_Pin);
uint16_t GPIO_ReadInputData(GPIO_TypeDef* GPIOx);
uint8_t GPIO_ReadOutputDataBit(GPIO_TypeDef* GPIOx, uint16_t GPIO_Pin);
uint16_t GPIO_ReadOutputData(GPIO_TypeDef* GPIOx);
```



## 2.5 GPIO输出示例

### 2.5.1 LED电路

LED是light-emitting diode的缩写，意思为发光二极管。发光二极管有各种颜色，一般长脚为正极，短脚为负极（长正短负）。

<img src="D:\!学习\笔记\notes\stm32笔记\图片\LED.jpg" style="zoom:80%;" />

驱动电路：

- 高电平驱动：将LED的**正极**经过限流电阻接GPIO，负极接地，当GPIO输出高电平时灯亮，低电平时灯灭。
- 低电平驱动：将LED的**负极**经过限流电阻接GPIO，正极接VCC，当GPIO输出高电平时灯亮，高电平时灯灭。
  - 一种采用这种驱动方式，因为这种方式只需要输出低电平，对stm32的负载小。

<img src="D:\!学习\笔记\notes\stm32笔记\图片\LED两种驱动电路.png" style="zoom: 50%;" />

### 2.5.2 灯闪烁

**定义一个LED类**：

```c++
class LED{
    public:
		// 构造函数
        LED();
        LED(GPIO_TypeDef* IO_port, uint16_t pin);
	
        void enable_clock();  // 启用时钟
        void on();  // 灯亮
        void off();	// 灯灭
	    void twinkle_once();  // LED闪烁一次
        void twinkle_once(int interval_ms);  // LED闪烁一次，指定间隔时间
		void twinkle();  // LED持续闪烁，阻塞式地
		void twinkle(int interval_ms);  // LED持续闪烁（阻塞式地），指定间隔时间
    protected:
        GPIO_TypeDef* _IO_port;
        uint16_t _IO_pin;
};
```

---

在构造函数中实现**启用与配置LED需要的GPIO**：

```c++
// 构造函数：指定led的端口和引脚
LED::LED(GPIO_TypeDef* IO_port, uint16_t pin){  
	// 读取参数
    this->_IO_port = IO_port;
    this->_IO_pin = pin;
    
    // 启用GPIO时钟
    this->enable_clock();   
    
    // 配置GPIO           
    GPIO_InitTypeDef GPIO_InitStruct;
    GPIO_InitStruct.GPIO_Pin = _IO_pin;
    GPIO_InitStruct.GPIO_Speed = GPIO_Speed_50MHz;
    GPIO_InitStruct.GPIO_Mode = GPIO_Mode_Out_PP;
    GPIO_Init(_IO_port, &GPIO_InitStruct);
	
	// 关闭灯
	this->off();
}

// 根据端口启动时钟
void LED::enable_clock(){
    if(_IO_port == GPIOA){
        RCC_APB2PeriphClockCmd(RCC_APB2Periph_GPIOA, ENABLE);
    }
    else if(_IO_port == GPIOB){
        RCC_APB2PeriphClockCmd(RCC_APB2Periph_GPIOB, ENABLE);
    }
    else if(_IO_port == GPIOC){
        RCC_APB2PeriphClockCmd(RCC_APB2Periph_GPIOC, ENABLE);
    }
	else if(_IO_port == GPIOD){
        RCC_APB2PeriphClockCmd(RCC_APB2Periph_GPIOD, ENABLE);
    }
    else if(_IO_port == GPIOE){
        RCC_APB2PeriphClockCmd(RCC_APB2Periph_GPIOE, ENABLE);
    }
    else if(_IO_port == GPIOF){
        RCC_APB2PeriphClockCmd(RCC_APB2Periph_GPIOF, ENABLE);
    }
    else if(_IO_port == GPIOG){
        RCC_APB2PeriphClockCmd(RCC_APB2Periph_GPIOG, ENABLE);
    }
}
```

---

配置完成后，**实现led闪烁**：

```c++
// LED闪烁一次
void LED::twinkle_once(){
    this->on();
    Delay_ms(500);
    this->off();
    Delay_ms(500);
}

//
// LED持续闪烁（阻塞式地）
void LED::twinkle(){
	while(1) this->twinkle_once();
}
```

---

**主函数**：

```
LED light(GPIOA, GPIO_Pin_3);
light.twinkle();
```



### 2.5.3 流水灯

流水灯需要多个led轮流闪烁，即多个GPIO配合，因此**定义一个LEDGroup类**，进行统一管理：

```c++
class LEDGroup: public LED{
	public:
		using LED::_IO_port;
        LEDGroup(GPIO_TypeDef* IO_port, uint16_t *pins, uint8_t num_light);
        void on();  // 灯亮
        void off();	// 灯灭
        void waterfall(int interval_ms);  // 流水灯
    
    protected:
        uint16_t *_IO_pins;
        uint8_t _num_light;
};
```

---

完成**多个GPIO的统一启用与配置**：

```c++
// 构造函数
LEDGroup::LEDGroup(GPIO_TypeDef* IO_port, uint16_t *pins, uint8_t num_light){
	// 读取参数
    this->_IO_port = IO_port;
    this->_IO_pins = pins; 
    this->_num_light = num_light;
    this->_IO_pin = 0;
    for(int i=0; i<this->_num_light; i++) _IO_pin |= this->_IO_pins[i];

    // 启用GPIO时钟
    this->enable_clock();
    
    // 配置GPIO           
    GPIO_InitTypeDef GPIO_InitStruct;
    GPIO_InitStruct.GPIO_Pin = _IO_pin;
    GPIO_InitStruct.GPIO_Speed = GPIO_Speed_50MHz;
    GPIO_InitStruct.GPIO_Mode = GPIO_Mode_Out_PP;
    GPIO_Init(_IO_port, &GPIO_InitStruct);
	
	// 关闭灯
	this->off();
}
```

- 通过pins数组传递多个IO口；
- 但是当前函数只实现了同一组端口下的流水灯，多个端口的流水灯比较麻烦。

---

**流水灯实现**：

```c++
// 流水灯
void LEDGroup::waterfall(int interval_ms){
    uint8_t index = 0;
    int8_t dir = 1;
    while(1){
        GPIO_ResetBits(this->_IO_port, this->_IO_pins[index]);
        Delay_ms(interval_ms);
        GPIO_SetBits(this->_IO_port, this->_IO_pins[index]);
        Delay_ms(interval_ms);

        if(index == this->_num_light-1)
            dir = -1;
        else if(index==0)
            dir = 1;
        index += dir;
    }
}
```

思路：

- 轮流对各GPIO输出低电平和高电平，即每个LED轮流闪烁一次。

---

**主函数**：

```c++
uint16_t pins[6] = {GPIO_Pin_0, GPIO_Pin_1, GPIO_Pin_2, GPIO_Pin_3, GPIO_Pin_4, GPIO_Pin_5};
LEDGroup leds(GPIOA, pins, 6);
leds.twinkle(500);
```

### 2.5.4 蜂鸣器

蜂鸣器是可以发声的元件，分为：

- 有源蜂鸣器：
  - 使用简单；
  - 只要接通触发电平（高电平或低电平），就会发声。
- 无源蜂鸣器：
  - 相对麻烦；
  - 需要手动给震荡信号，可以通过调节震荡信号的频率来调节蜂鸣器的音调。

<img src="D:\!学习\笔记\notes\stm32笔记\图片\有源蜂鸣器.jpg" style="zoom:80%;" />

## 2.6 GPIO输入





# 附录

## RCC

### 外设时钟变量

**APB1外设**：

- **TIM**：
  - `RCC_APB1Periph_TIM2` -- `RCC_APB1Periph_TIM7`；
  - `RCC_APB1Periph_TIM12` -- `RCC_APB1Periph_TIM14`。
- **IIC**：
  - `RCC_APB1Periph_I2C1`；
  - `RCC_APB1Periph_I2C2`。
- **SPI**：
  - `RCC_APB1Periph_SPI2`；
  - ``RCC_APB1Periph_SPI3`。
- **USART**：
  - `RCC_APB1Periph_USART2` -- `RCC_APB1Periph_USART5`
- **USB**：
  - `RCC_APB1Periph_USB`。
- **CAN**：
  - `RCC_APB1Periph_CAN1`。

* **WWDG**：
     * `RCC_APB1Periph_WWDG`
* **BKP**：
     * `RCC_APB1Periph_BKP`。
* **PWR**：
     * `RCC_APB1Periph_PWR`。
* **DAC**：
     *  `RCC_APB1Periph_DAC`。
* **CEC**：
     -  `RCC_APB1Periph_CEC`。

---

**APB2外设**：

- **AFIO**：
  - `RCC_APB2Periph_AFIO`。
- **GPIO**：
  - `RCC_APB2Periph_GPIOA` -- `RCC_APB2Periph_GPIOG`。
- **ADC**：
  - `RCC_APB2Periph_ADC1` -- `RCC_APB2Periph_ADC3`。
- **TIM**：
  - `RCC_APB2Periph_TIM1`；
  - `RCC_APB2Periph_TIM8` -- `RCC_APB2Periph_TIM11`；
  - `RCC_APB2Periph_TIM15` -- `RCC_APB2Periph_TIM17`。
- **SPI**：
  - `RCC_APB2Periph_SPI1`。
- **UART**：
  - `RCC_APB2Periph_USART1`。

### 常用函数

```c
void RCC_AHBPeriphClockCmd(uint32_t RCC_AHBPeriph, FunctionalState NewState);
void RCC_APB2PeriphClockCmd(uint32_t RCC_APB2Periph, FunctionalState NewState);
void RCC_APB1PeriphClockCmd(uint32_t RCC_APB1Periph, FunctionalState NewState);
```

## GPIO

```c
void GPIO_DeInit(GPIO_TypeDef* GPIOx);
void GPIO_AFIODeInit(void);
void GPIO_Init(GPIO_TypeDef* GPIOx, GPIO_InitTypeDef* GPIO_InitStruct);
void GPIO_StructInit(GPIO_InitTypeDef* GPIO_InitStruct);
uint8_t GPIO_ReadInputDataBit(GPIO_TypeDef* GPIOx, uint16_t GPIO_Pin);
uint16_t GPIO_ReadInputData(GPIO_TypeDef* GPIOx);
uint8_t GPIO_ReadOutputDataBit(GPIO_TypeDef* GPIOx, uint16_t GPIO_Pin);
uint16_t GPIO_ReadOutputData(GPIO_TypeDef* GPIOx);
void GPIO_SetBits(GPIO_TypeDef* GPIOx, uint16_t GPIO_Pin);
void GPIO_ResetBits(GPIO_TypeDef* GPIOx, uint16_t GPIO_Pin);
void GPIO_WriteBit(GPIO_TypeDef* GPIOx, uint16_t GPIO_Pin, BitAction BitVal);
void GPIO_Write(GPIO_TypeDef* GPIOx, uint16_t PortVal);
```


# 一、编译单个文件

```bash
gcc -Wall main.c -o main
```

- `-o`：指定生成的可执行文件的名称，如果不加`-o`，则默认生成”a.out“文件。
- `-Wall`：W代表warning，Wall的含义就是“warning all”，意为开启所有警告信息（常用的警告）。
- 如果不加`-Wall`，则编译出错时也不会打印警告（我们就不知道编译出错）。
- 一般最好都把`-Wall`加上。
- 注意`-Wall`要区分大小写。

# 二、编译多个文件

## 1. 全部编译

```bash
gcc -Wall main.c hello.c -o main
```

```bash
gcc -v -Wall main.c  # 打印编译时的所有信息
```

- `-v`：v代表“verbose”，打印编译时的所有信息，包括编译器从哪里找库文件等，用于调试编译器。

## 2. 独立编译

当一个**项目很大**时，可能会有上万个，甚至上亿个文件，例如：windows系统的源文件就有千万个甚至几亿个。这么多源程序如果每次都要全部重新编译，**非常耗时**，而且大部分源文件都是没有更改过又重新编译了一遍，编译效率很低。

因此在大项目开发中，往往采用只编译更改过的少量文件的策略来开发。将修改过的少量源程序编译为目标文件，再将这些目标文件与原先开发的目标文件通过链接器链接起来，就能完成项目的更新，即生成新版本的可执行文件。

链接目标文件比编译源程序所需要的时间要少很多，即**链接比编译块**，因此采用“**先编译成目标文件，再链接目标文件**”的策略，可以大大提高开发效率。

### 2.1 生成目标文件

```bash
gcc -c -Wall main.c  # 生成main.o
gcc -c -Wall hello.c  # 生成hello.o
```

- `-c`：c代表“compile”，表示**生成目标文件**，而不生成可执行文件。
- linux下目标文件的后缀名为".o"，windows下可执行文件的后缀名为“.obj”，都为”object“的意思。
- 生成目标文件时可以不用指定生成的文件名，默认就是更换了后缀的原文件名

### 2.2 链接

```bash
gcc main.o hello.o -o main
```

- gcc自动识别文件后缀名为".o"，然后执行链接操作
- gcc命令实际上是“外壳”、**前端**。实际上链接时，gcc调用了**`ld`**来链接目标文件，`ld`是另一个独立的程序

**注意：**

- 在传统的类unix系统里，命令行中的文件顺序遵循一定规则，被引用的文件需要放在引用其他文件的文件后边，即**定义文件写在调用文件右边**，否则会报错：没有定义（definition）。
- 一些新的编译器（包括gcc）没有这个惯例，文件可以任意顺序。
- 但是为了考虑到我们以后会使用到许多不同的编译器，最好遵循被引用文件在右的惯例。

### 2.3 链接库文件

人们将许多开发好的有一定功能的源程序编译成目标文件，然后合并成一个大的目标文件，这个目标文件的集合称为库文件。因此，库文件是一个目标文件，或者说多个目标文件的集合。

linux中**静态**库文件的后缀名为“.a”，意为“archive”。windows中**静态**库文件的后缀名为“.lib”，意为“library”。

```bash
gcc -Wall main.o /usr/bin/libm.a
gcc -Wall main.o -lm  # -lm 是 /usr/bin/libm.a的缩写
```

- 如果想要链接静态库，直接像链接多个目标文件一样链接就行，因为库文件本身就是目标文件。
- libm.a是c的标准库文件，包含了许多数学函数。
- 如果源程序中`#include<math.h>`，老版本的gcc需要手动指定出库文件的路径，即/usr/bin/libm.a。
- c的标准库文件常常以`libxxx.a`命名，标准库路径太长时，可以用`-lxxx`来代替。

**注意：**

- 使用`-lxxx`指定库文件时，gcc会去默认的系统路径查找，而不在当前目录查找。因此如果库文件在当前目录，并且用了`-lxxx`的方式，则gcc会报错，说找不到定义。
- 例如：当前目录有个库libmylib.a，如果`gcc -Wall main.o -lmylib`则会报错。
  - 需要`gcc -Wall main.o ./libmylib.a`，即：不使用`-lxxx`的方式才能找到库文件。
  - 配置环境变量与命令行中指定搜索路径也可以解决，详细说明在下节。


### 2.4 指定头文件与库文件路径

#### 2.4.1 默认路径

gcc默认查找的**头文件路径：**

```bash
/usr/include
/usr/local/include
```

gcc默认查找的**库文件路径：**

```
/usr/lib
/usr/local/lib
```

如果我们的头文件没在这些默认路径中，gcc会报错：

```bash
File.h:No such file or directory  # 找不到头文件（在生成目标文件过程中）
/usr/bin/ld:cannot find library  # 找不到库文件（在链接过程中）
```

#### 2.4.2 命令行指定路径

`-I`：后面跟**头文件**的路径。

例如：

```bash
# 生成目标文件main.o
gcc -Wall -I../include -c main.c  # -I../include表示头文件在上级目录下的include目录里。
```

**注意：**

- 如果头文件在当前路径下就不用指定头文件。

`-L`：后面跟**库文件**的路径。

例如：

```bash
# 生成可执行文件main
gcc -Wall -L../lib main.c -lmylib -o main  # -L../lib 表示库文件（静态库）在上级目录下的lib目录里
```

**注意：**

- 如果包含的库文件不是c的标准库，生成可执行文件时还要指定是哪个库文件，像上例的`-lmylib`。
- 即便库文件在当前文件夹下，gcc也无法识别，仍然需要设置路径。
- 只有链接静态库时才需要设置库文件的路径，如果直接链接目标文件，则不需要指定路径。

#### 2.4.3 环境变量设置路径

C_INCLUDE_PATH：用于指定C语言程序 **头文件路径的环境变量**。

CPLUS_INCLUDE_PATH：用于指定C++程序 头文件路径的环境变量。

LIBRARY_PATH：用于指定**静态库文件路径的环境变量。**

LD_LIBRARAY_PATH：用于指定**动态库文件路径的环境变量。**

设置环境变量：

```bash
# 设置环境变量C_INCLUDE_PATH为 /your_path
export C_INCLUDE_PATH=/your_path:$C_INCLUDE_PATH
# 取消环境变量C_INCLUDE_PATH
unset C_INCLUDE_PATH  
# 查看环境变量
echo $C_INCLUDE_PATH  # "$"表示引用变量
```

**注意：**

- 一个环境变量可以包含多个路径，多个路径之间用`:`隔开
- `/your_path:$C_INCLUDE_PATH`的写法是为了保证**不覆盖掉原先指定的路径**，而只是添加上路径

#### 2.4.4 gcc查找顺序

gcc在编译时:

- 先查看**命令行指定的路径**，即`-I`和`-L`后的路径。
- 然后检查**环境变量指定的路径**。
- 最后检查**默认的系统路径**

### 生成静态库文件

```bash
ar cr libxxx.a func1.o func2.o  # 将func1.o和func2.o合并为库文件libxxx.a
ar t libxxx.a  # 列出库文件的组成，即列出组成库文件的目标文件
```

# 三、C标准

## 1. 综述

c语言有多种标准，著名的标准规范有**ANSI/ISO C标准**，但是GNU中也有自己的c语言方言。

**GNU方言**：

- 就是除了c语言标准语法之外，增添了一些额外的语法，例如：支持函数嵌套定义 和 可变长度数组等。
- **这种非标准的、扩展的语法被称为方言**，GNU的方言叫做GNU C标准。
- GNU C是GNU/Linux系统开发时所用的标准，该标准（或语法）的特性被在GNU/Linux系统中被大量使用。

在使用gcc编译时，**默认以GNU C的语法标准进行编译**，如果要以ANSI/IOS C标准编译，可以加上选项来指定标准。

- `-ansi`：使用ANSI/ISO标准

- `-pedantic`

- `-std=xxx`：指定c标准

例如：

```bash
gcc -Wall -ansi main.c -o main  # 使用ansi c标准来编译main.c
gcc -Wall -pedantic main.c -o main  # 也是使用ansi c标准（好像和ansi有些差别，不清楚）

gcc -Wall -std=c89 main.c -o main  # 使用c89标准
gcc -Wall -std=iso9899:1990 main.c -o main  # 使用c89标准

gcc -Wall -std=c99 main.c -o main  # 使用c99标准
gcc -Wall -std=iso9899:1999 main.c -o main  # 使用c99标准
```

**注意：**

- -ansi和-pedantic 连用，会严格地按照ANSI/ISO标准检查源程序。
- c89是1989年的c标准。c99是1999年的c标准，在c89上有细节上的改动。除此之外比较著名的还有c94(iso9899:199409)标准。
- c99是最常用的。

## 2. 不同标准的差异

### 2.1 示例一

main.c的内容如下：

```c
#include <stdio.h>
int main()
{
        char asm[]="1234";
        printf("The asm is %s\n", asm);
        return 0;
}
```

- 如果使用`gcc -Wall main.c -o main`，则会报错。
- 因为 gcc 默认使用 GNU C标准，而“**asm**”是**GNU C标准中的关键字**（表示汇编的意思），因此会报错。
- 使用`gcc -Wall -ansi main.c -o main`，指定使用ANSI标准编译，则asm就是普通的变量名，编译通过

### 2.2 示例二

main.c的内容如下：

```c
#include <stdio.h>
#include <math.h>
int main()
{
        printf("The pi is %f\n", M_PI);
        return 0;
}
```

命令行中编译：

```bash
gcc -Wall -ansi main2.c -o main2  # 报错
# main2.c: In function ‘main’:
# main2.c:5:27: error: ‘M_PI’ undeclared (first use in this function)
#     5 |  printf("The pi is %f\n", M_PI);
#       |                           ^~~~
# main2.c:5:27: note: each undeclared identifier is reported only once for each function it appears in

gcc -Wall -ansi -D_GNU_SOURCE main2.c -o main2  # 通过
```

- ”**M_PI**“在GNU C 中是**预定义的宏**，代表圆周率pi。

- 如果不指定c标准，则默认就是用GNU C来编译，编译通过。

- 如果指定用 ANSI标准编译，就会报错，说M_PI没有定义。

  - 通过命令行中添加`-D_GNU_SOURCE`，可以通过编译并正常运行

  - -D表明定义一个宏，宏名为后面跟的字符串，例如：`-D_GNU_SOURCE`表示定义一个名叫`_GNU_SOURCE`的宏。

  - 有了`_GNU_SOURCE`这个宏之后，在预处理时就会定义M_PI，程序就会编译通过并正常运行。

  - 类似这种原理：

  - ```c
    #ifdef _GNU_SOURCE
        #define M_IP 3.1415926
    #endif
    ```

### 2.3 示例三

main.c的内容如下：

```c
#include <stdio.h>
int main()
{
        int i, n=10;
        double d[n];
        for(i=0;i<n;i++)
        {
                d[i]=i;
                printf("%f\n", d[i]);
        }
        return 0;
}
```

命令行中编译：

```bash
gcc -Wall -ansi -pedantic main.c -o main  # 报错
# main3.c: In function ‘main’:
# main3.c:5:2: warning: ISO C90 forbids variable length array ‘d’ [-Wvla]
#     5 |  double d[n];
gcc -Wall -ansi main.c -o main  # 编译通过
gcc -Wall main.c -o main  # 编译通过
```

- 源代码中数组d在定义时，长度是个变量n，这在标准的c语法中是不允许的。但是**可变长度数组**符合GNU C 标准。
- 指定`-ansi`编译可以通过，可能检查不是很严谨，加上`-pedantic`后，就会严格按照c标准的规则仔细检查。



# 四、warning选项

## 1. 错误类型

选项`-Wall`中包含了多个warning类型，这些warning类型包括：

- `-Wcomment`：注释错误
- `-Wformait`：字符格式错误
- `-Wunused`：未使用的变量
- `-Wimplicit`：隐式的错误
- `-Wreturn-type`：返回值类型错误

## 2. 引发错误的情况

### 2.1 注释错误

c语言的注释语法为 /*一段注释\*/，c++的注释语法为 // 一条注释。

在使用 /\*\*/注释程序时，如果被注释的代码段中已经包含了 /\*\*/，就会发生**注释嵌套**，这在c语法中是不允许的。

例如：

```c
/*
/* 打印一个hello world */
void print(){
    print("hello world!")}
*/
```

- 在注释掉print函数时，其代码段中已经有了注释，就会发生**注释嵌套**。

可以用下面的方法解决：

```c
#if 0
/* 打印一个hello world */
void print(){
    print("hello world!")}
#endif
```

- 用**预处理命令**来注释程序，就可以避免注释嵌套。

### 2.2 字符格式错误

常常发生在函数`printf`和`scanf`中

例如：

```c
printf("The num is %s", 10);  // 10是整型数字，不应该用%s
printf("The ineger is %.2f", 20);  // 20是整型数字，不应该用%.2f
```

### 2.3 未使用的变量

常常发生在定义了变量，但是**引用时变量名写错了**

例如：

```c
int i, j;
for(i=0;i<10;i++)
    print("%d", i);

for(i=0;i<10;i++)
    print("%d", i);
```

- 定义了j，但是引用时把`j`写成了`i`

### 2.4 隐式的错误

常常发生在调用了函数，但是函数没有申明。很有可能**忘记包含了相关的头文件**。

### 2.5 返回类型错误

- 定义了一个没有返回值的函数，但是定义函数返回类型时不是void。
- 定义函数返回类型为void，然是返回了一个值

## 3. 其他warning选项

- -W
- -Wconversion
- -Wshadow
- -Wcast-qual
- -Wwrite-strings
- -Wtraditional



# 五、编译过程

## 1. 预处理

- 执行预处理（preprocess）的程序称为预处理器（preprocessor）。

- **GNU的预处理器为cpp**，是“c pre-processor”的缩写，不是“c plus plus”的缩写。
- 在执行gcc编译程序时，自动调用了cpp。

## 2. 预处理中的一些技巧

有main.c的内容如下：

```c
#include <stdio.h>
int main()
{
#ifdef TEST
        printf("这是一次测试\n");
        printf("测试号为：%d\n", TEST);
#endif
        printf("hello world!\n");
        return 0;
}
```

编译和执行结果：

```bash
gcc -Wall main.c -o main  # 基本编译语句
./main  # 打印"hello world\n"

gcc -Wall -DTEST -o main  # 定义一个TEST宏
./main  
# 打印结果：
# 这是一次测试
# 测试号为：1
# hello world

gcc -Wall -DTEST=10 -o main  # 定义一个TEST宏
./main  
# 打印结果：
# 这是一次测试
# 测试号为：10
# hello world
```

- **`-D`**：可以在命令行中定义指定一个宏，该宏在源程序中定义。
- `-Dmacor=xxx`：可以指定宏的内容，如果不指定，默认为1
- `-D`与`ifdef xxx`、`endif`联用，可以实现程序分别在**调试状态下编译**，与发行状态下编译。

## 3. 生成中间文件

c语言源代码到可执行文件的过程：

1. 源程序（main.c）
2. 预处理后的文件（main.i）
3. 汇编程序（main.s）
4. 目标文件（main.o）
5. 可执行文件（main）

中间文件：

- main.i：为预编译后，宏扩展后的文件
- main.s：源程序经过编译后生成的**汇编文件**
- main.o：汇编文件经过汇编后生成的**目标文件**

gcc选项：

- `-E`：可以生成源文件**预处理后**的文件（main.i）。（E应该表示“extend”）
  - 默认扩展后的内容直接打印在终端上，而不保存文件。
  - 如果要保存内容，可以在命令中加上选项`-o 文件名`。
  - 或者用重定向`>`保存内容，例如：`gcc -Wall -E main.c -o main.temp` 或者 `gcc -Wall -E main.c > main.temp`
- `-S`：生成**汇编文件**（main.s）。（S应该表示“assembly”）
- `-c`：生成**目标文件**（main.o）。（c应该表示“compile”）
- `-save-tepms`：将源程序编译过程中的**所有临时文件**都保存下来，
  - 包括中间文件：main.i、main.s、main.o 。
  - 可执行文件main，如果没指定可执行文件名，默认为a.out。

或者：

- `cpp main.c`：调用cpp（预处理器）生成预处理后的文件。
- `as main.s`：用as（汇编器）来生成目标文件。



# 六、debug

## 1. 综述

- gcc的调试器器（debugger）是**gdb**，gdb是“GNU debugger”的缩写。windows中也有一个著名的调试器，叫做windbg（“windows debug”）。
- 在gcc编译c源程序时，会在生成的可执行文件中加入**符号表**（需要设置，默认不加）。
  - 选项`-g`表示在可执行文件中添加符号表
  - 这些符号表说明了函数名、变量名等符号在源程序中的行号等信息，这些信息用于记录源程序与目标程序的关系，方便debug。
  - 符号表会让可执行文件占用空间变大。

- 在进程运行中，如果异常崩溃掉了，会留下**core文件**。core文件中记录了进程崩溃前的运行状态等信息，相当于可执行程序的遗体。
  - 占用空间：core文件占用空间较大，因此linux系统为了节省空间，会限制生成core文件的最大占用空间。
    - 使用 `ulimit -c`，可以查看系统设置的core文件的最大占用空间，默认为0，即不生成core文件。
    - `ulimit -c unlimited`可以设置core文件的占用空间为没有限制。
  - 保存路径：`sysctl kernel.core_pattern`可以查看**core文件默认的保存路径**
    - `sudo sysctl -w kernel.core_pattern=core.%p`，可以设置core文件的保存路径为当前目录下，%p表示进程号
    - 这样设置core文件的保存路径的方式这是**临时有效**，当系统重启后kernel.core_pattern的值又会恢复。
- core文件和可执行文件的符号表包含的信息合起来，就可以让我们方便得找到远程中崩溃的代码位置，方便调试。
  - `gdb main core.xxxx`：使用gdb开始调试程序
  - gdb指令：print、**quit**（退出调试）


## 2. 示例

有main.c文件如下：

```c
#include <stdio.h>
int y(int* x);
// 一段会引发崩溃的程序，访问了错误的内存
int main()
{
        int* num=0;
        int res=0;
        res = y(num);
        printf("This is: %d\n", res);
        return 0;
}

int y(int* x)
{
        int res=*x;
        return res;
}
```

编译：

```bash
gcc -Wall -g main.c -o main  #　-g表示在可执行文件中添加符号表
```

配置core文件选项：

```bash
ulimit -c  #　查看core文件最大允许占用内存
ulimit -c unlimited  # 使core文件的大小不受限制
sudo sysctl -w kernel.core_pattern=core.%p  # 设置core文件生成在当前目录下
```

调试：

```bash
./main  # 此时代码崩溃
# 报错：
# 段错误 (核心已转储)
# 当前路径生成core.9753，9753是进程号（PID）
dbg main core.9753  # 使用调试器dbg开始调试
```

**注意：**

- 使用dbg时，后面跟的**文件顺序**要写对，必须是可执行文件在前，core文件在后。
  - 例如：
  - `dbg main core.9753`是正确的。
  - `dbg core.9753 main`就会报错。



# 七、程序优化

## 1. 综述

- c源程序最后要编译成可执行文件才可以运行，可执行文件的内容是一条条的机器指令。
- 优化原因：
  - 不同的CPU有不用的指令集，CPU不同，c编译后的可执行文件不同，即在不同平台下，最后编译结果是不同的。
  - 要保证编译出的可执行程序在不同的CPU下都具有良好的效果，编译器在生成可执行文件时会对其作改动以提高运行速度、降低可执行文件大小，这个过程叫做优化。
- 优化目的：
  - 可执行文件内存小，同时运行快。
  - 内存与运行速度大部分时候是矛盾的，需要权衡（trade-off）。
- 在pc机上，为了追求速度，我们可以容忍程序的内存大小。但是在嵌入式系统中，硬件能力匮乏，运行速度和内存需要兼顾。

**注意：**

- gcc编译时，默认**不使用优化算法**。

## 2. 优化算法

### 2.1 源码层次

源码层次的优化算法**与CPU无关**。

#### 2.1.1 CSE

CSE是"common subexpression elimination"的缩写，共同子表达式消除。

例如：

有main.c内容如下：

```c
#include <stdio.h>
#include <math.h>
int main()
{
        double x = 0;
        double v = 1.4, u = 1.4;
        x = cos(v)*(1 + sin(u/2)) + sin(v)*(1 - sin(u/2));
        printf("The result is: %.2f\n", x);
        return 0;
}
```

采用CSE 优化后，源码会变为：

```c
#include <stdio.h>
#include <math.h>
int main()
{
        double x = 0;
        double v = 1.4, u = 1.4;
    	t = sin(u/2);
        x = cos(v)*(1 + t) + sin(v)*(1 - t);
        printf("The result is: %.2f\n", x);
        return 0;
}
```

- 原来的`x = cos(v)*(1 + sin(u/2)) + sin(v)*(1 - sin(u/2));`中`sin(u/2)`出现了两次
  - sin()计算是一个比较耗时的计算。
  - 如果不加优化的编译，则`sin(u/2)`会被计算两次，耗费时间。
  - 将第一次`sin(u/2)`的计算结果保存为临时变量，然后在计算x时使用临时变量，这样就节省了计算`sin(u/2)`的时间，提高了效率。
- 这种采用中间变量来减少重复计算的方法就是CSE。
  - 例子中`sin(u/2)`就是common subexpression（CS），通过使用临时变量`t`来eliminate CS。
- CSE不仅能够减少可执行文件的占用大小（因为sin(u/2)的计算代码只有一次），又能提高程序运行速度，是少数几个**大小与速度兼顾**的算法。

#### 2.1.2 FL

FL是"function inline"的缩写，内嵌函数。

- **调用函数**需要一定的开销。
  - 从汇编语言可以知道，每调用一次函数，至少都需要保留现场的操作。
  - 即进入函数体前段寄存器、指令指针寄存器入栈及函数中使用的寄存器入栈，函数返回时这些内容又一一出栈。
  - 对于一些函数内容少，又被多次调用的函数，出栈又入栈的过程耗费了大量时间。
- 直接把**函数内容复制到调用函数的地方**，就可以省去函数调用的开销。
- FL也能减小可执行文件的大小的**同时提高**运行速度。

例如：

有main.c内容如下：

```c
int func(int num);
int main()
{
    int n = 1000;
    int i;
    int sum;
    for(i=0;i<n;i++)
    {
        sum += func(i);
    }
    return 0;
}
int func(int num)
{
    return num/2;
}
```

经过FL后：

```c
for(i=0;i<n;i++)
{
    sum += num/2;
}
```

- 直接把函数func中内容复制到调用函数的位置，减少调用函数的开销。

- 要使用内嵌函数算法，需要在函数名前加上关键字inline，如：

- ```c
  inline int func(int num){
      return num/2;}
  ```

  inline的语法属于GNU方言。

#### 2.1.3 unrolling-loop

- 循环时循环体每执行完一次，都需要判断结束条件是否满足。
- 判断循环结束条件消耗了一定的时间。
- unrolling-loop通过**减少、或消除判断结束条件**来加快程序运行速度。

**例如：**

有main.c程序如下：

```c
int main()
{
    int a[5] = {0,};
    int i=0;
    for(i=0;i<5;i++)
    	a[i] = i;
    return 0;
}
```

使用unrolling-loop后：

```c
int main()
{
    int a[5] = {0,};
    int i=0;
    
    a[i] = 0;
    a[i] = 1;
    a[i] = 2;
    a[i] = 3;
    a[i] = 4;
    
    return 0;
}
```

- for循环的语句直接被展开了，循环判断被消除了，加快了程序运行速度。
- 但是程序**占用了更大的内存**。

**当循环次数不确定时：**

有循环程序如下：

```c
for(i=0;i<n;i++)
    a[i] = i;
```

使用unrolling-loop后：

```c
for(i=0;i<(n%2);i++)
    a[i] = i;
    
for(;i+1<n;i+=2)
    a[i] = i;
	a[i+1] = i + 1;
```

- 循环的步长从1增加到2，循环结束的**判断次数减少了1倍**。
- 第一个for循环用来消除奇偶性。
  - 如果n为偶数，则第一个循环不执行。剩下的循环次数是**偶数次**。
  - 如果n为技术，则a[0] = 0; ，此时剩下的循环次数也是偶数次。

### 2.2 机器码层次

一个c语言源程序编译后的机器指令顺序会影响程序的运行速度，有些CPU允许执行本条指令还没结束，就加载执行下一条指令。

合理的**安排**（**scheduling**）机器指令的顺序，使其运用CPU的特性、并行地运行程序，能够尽可能地发挥CPU的性能，提高运行速度。

scheduling 不会改变可执行文件的大小，但是可以加快程序运行速度。

scheduling 会**降低编译速度**，不过这一般是可以忍受的。

## 3. gcc优化等级

### 3.1 概述

gcc编译时默认**不进行优化**，gcc把优化分为三个等级，最高4级，最低1级，等级越高，优化程度越高。

- `-O`：指定优化等级，例如：`gcc -Wall -O2 main.c`，指定优化等级为2级，`-O0`表示不优化。
- `-funroll-loops`：使用unrolling loop优化，可以与`-O`联用
- 优化等级越高，付出的代价越大，例如：编译速度变慢、暴露出一些bug、debug困难（因为优化修改了程序）。
- 一般来说高优化等级越高，效果越好，但并不是都是这样。 
- 在程序开发阶段，优化等级为0，方便debug。
- 在程序投入使用阶段，优化等级为2或3，提高程序性能。

`time`：一个bash指令，检测这个程序的运行时间。例如：`time ./main`。

### 3.2 示例

#### 3.2.1 示例一

有main.c内容如下：

```c
#include <stdio.h>
void delay(int num);
int main()
{
        long num = 10000000;
        int i = 0;
        for(i=0;i<5;i++)
        {
                printf("hello world!\n");
                delay(num);
        }
        return 0;
}

void delay(int num)
{
        int i=0;
        for(;i<num;i++);
}
```

编译运行：

```bash
gcc -Wall main.c -o main  # 可执行文件大小 16728 B
time ./main
# 结果：
# real    0m0.092s
# user    0m0.088s
# sys     0m0.004s

gcc -Wall main.c -O2 main_o2  # 可执行文件大小 16728 B
time ./main_o2
# real    0m0.001s
# user    0m0.001s
# sys     0m0.000s
```

- 优化执行速度提升很多。
- 优化前后可执行文件大小不变。

#### 3.2.2 示例二

有main.c内容如下：

```c
#include<stdio.h>

double powern(double d, unsigned n)
{
// 求幂次方，d为底数，n为指数
        double x = 1.0;
        unsigned j;
        for(j=1;j<=n;j++)
                x *= d;
        return x;
}

int main()
{
        double sum = 0.0;
        unsigned i;
        for(i=1;i<=100000000;i++)
                sum += powern(i, i%5);

        printf("sum = %g\n", sum);
        return 0;
}
```

编译运行：

```bash
# 没有优化
gcc -Wall main.c -o main  # 可执行文件 16728 B
time ./main
# real    0m0.675s
# user    0m0.674s
# sys     0m0.000s

# 一级优化
gcc -Wall -O1 main.c -o main_o1  # 可执行文件 16736 B
time ./main_01
# real    0m0.137s
# user    0m0.137s
# sys     0m0.000s

# 二级优化
gcc -Wall -O2 main.c -o main_o2  # 可执行文件 16736 B
time ./main_o2
# real    0m0.120s
# user    0m0.120s
# sys     0m0.000s

# 三级优化
gcc -Wall -O3 main.c -o main_o3  # 可执行文件 16736 B
time ./main_o3
# real    0m0.117s
# user    0m0.109s
# sys     0m0.008s

# 二级优化 + unrolling loop
gcc -Wall -O2 -funroll main.c -o main_o2unroll  # 可执行文件 16736 B
time ./main_o2unroll
# real    0m0.100s
# user    0m0.099s
# sys     0m0.000s
```

## 4. 优化和调试

- 一般的编译器不允许优化和调试同时存在，即不允许调试优化后的程序。
  - 因为优化后的程序已经被编译器修改过了，debug时难以将可执行文件与源代码的内容对应起来。

- 优化程度越高，调试越困难。

- **gcc允许同时优化和调试**，可以在选项中同时打开优化开关和调试开关，例如：`gcc -Wall -O2 -g main.c -o main`。

## 5. 优化和警告

- 在优化时，编译器会对程序进行数据流分析，会找到额外的bug。
- 因此有时不优化时，没有警告，开了优化，有警告。
- 如果程序太复杂了，优化中可能误报警告，即使程序没错，也应当要简化程序，为了提高可读性（因为编译器都被你骗了）。



# c++编译

## 1. 概述

- GNU的c++编译器叫做**g++**，g++是一个真的c++编译器。
  - 因为他直接编译c++源程序到汇编文件。
  - 其他的c++编译器都是先将c++原文文件编译为c语言程序，再将c语言程序编译为汇编文件。

- g++用法基本上与gcc相同，-Wall等指令同样能使用。

- c++文件的后缀名一般是“.cc”，或者“.cpp”。

- g++的预处理后的文件的后缀一般是".ii"。

**注意：**

- g++和gcc产生的目标文件不能链接在一起，即：不能用gcc链接g++编译的目标文件，反过来也是。
- 因为c++程序使用的是c++的标准库，c语言程序使用的是c语言的标准库。
- 都是.o文件，容易弄混。

## 2. 示例

有main.cc内容如下：

```c++
#include<iostream>

int main()
{
        std::cout << "hello world!" << std::endl;
        return 0;
}
```

编译：

```bash
g++ -Wall main.cc -o main  # 生成可执行文件 main
./main # 运行
```



# 九、其他工具

file

elf coff

lsb msb

not striped 符号表、

nm T

ldd

gprof 记录函数 调用次数 执行时长 用于优化 profiler

-pg 内嵌代码 gmon.out

gcov coverage 覆盖度测试




# 一、Makefile文件

## 1. 规则

### 1.1 格式

一条规则的格式：

```makefile
target:prerequisite
	recipes
```

- 第一行冒号前为构建的目标（target），冒号后为构建目标所需要的依赖（prerequisite、dependency）。
- 第二行为实现目标的具体方法recipe）。
- 这一段内容称作一个规则。
- makefile文件就是由一条条**规则**（rules）组成的。

### 1.2 示例

有makefile文件内容如下：

```makefile
main: main.cc
        g++ -Wall main.cc -o main
```

- 其中第一行，main是一个目标。
- main.cc为依赖。
- 第二行及以下有缩进的内容为方法。
- 执行构建只要在当前目录下的终端下运行`make`就够了。

**注意：**

- 缩进必须使用**tab缩进**，不能是4个空格，否则make会检测错误。
- 有些文本编辑器会自动把tab转为4个空格，要当心。

上例通常会写成这样：

```makefile
main: main.o
        g++ -Wall main.o -o main

main.o: main.cc
        g++ -Wall -c main.cc 
```

- 分成了两步
  - 先将main.cc编译为目标文件。
  - 再将目标文件链接为可执行文件。
- 这是为了方便后期的更改。

或者另一种写法：

```makefile
main: main.o; g++ -Wall main.o -o main
main.o: main.cc; g++ -Wall -c main.cc 
```

- 方法和依赖之间用 `;` 隔开。

## 2. 文件名规范

make默认会**按顺序查找**当前目录下的

1. GNUmakefile
2. makefile
3. Makefile

建议文件名为**Makefile**。GNUmakefile只有GNUmake会查找，其他make工具不会查找GNUmakefile

如果make文件不叫上面三个名字，可以命令行中指定make文件名：

```bash
make -f mkf  # 按照mkf文件的规则构造
make --file=mkf  # 跟上行的作用一样
```

## 3. 文件组成

makefile文件通常有5中类型的内容组成。

- 显示规则（explicit rules）：显示得指明如何生成或更新目标文件，即详细地写出方法。显示规则包括目标、依赖和方法。
- 隐式规则（implicit rules）：根据文件自动推导如何从依赖生成目标文件。
- 变量（variables）：定义变量并指定值，值是字符串。相当于C语言中的`#define`
- 指令（directive）：在make读取Makefile内容过程中，做一些特殊的操作
  - 读取（包含）另一个Makefile文件，相当于C语言的`#include`。
  - 确定是否掠过文件中的某些内容，相当于C语言的`#if`。
  - 定义多行变量。
- 注释（comment）：以`#`开头，表示后面（同一行）的内容是注释内容。
  - 注释建议写在 目标的上面一行。

## 4. 目标

### 4.1 书写规范

mailefile组成及书写规则

<img src=".\图片\makefile书写过程.png" alt="mailefile组成即书写规则" style="zoom: 33%;" />

- 文件中，下方依次指定 生成上方目标依赖的规则。即，**先写出总目标，再依次解决总目标的依赖**。
- 如果文件中有目标跟最终目标**没有依赖关系**，则该目标的规则不会被执行。
  - 构造时，如果一个目标的依赖文件没有被更新过（检查该规则的目标文件和依赖文件的时间戳），则不会重新执行该条规则。
  - 如果一个目标的依赖文件被更新过，则会重新执行该条规则。
- 默认**第一行被认为是最终目标**。因此，最终目标写在最上方。
- 如果要指定其他目标为最终目标，可以定义全局变量 `.DEFAULT_GOAL = xxx`。

例如：

构建sudoku（数独）的makefile文件如下

```makefile
main: block.o command.o input.o main.o scene.o test.o
        g++ -Wall -o main block.o command.o input.o main.o scene.o test.o

block.o: block.cpp common.h block.h color.h
        g++ -Wall -c block.cpp

command.o: command.cpp scene.h common.h block.h command.h
        g++ -Wall -c command.cpp

input.o: input.cpp common.h utility.inl
        g++ -Wall -c input.cpp

main.o: main.cpp scene.h common.h block.h command.h input.h test.h
        g++ -Wall -c main.cpp

scene.o: scene.cpp common.h scene.h block.h command.h utility.inl
        g++ -Wall -c scene.cpp

test.o: test.cpp test.h scene.h common.h block.h command.h
        g++ -Wall -c test.cpp

clear:
        rm *.o
        rm main
```

- 第一个规则的目标是main，因此main也被当作最终目标。接下来的规则都是为了解决最终目标的依赖。
- clear没有和最终目标有依赖关系，因此在构建时clear的方法没有被执行。
  - 在命令行中手动构建clear目标：`make clear`
- 目标和依赖项手动列出太麻烦，不太实际。
  - 可以用命令 `g++ -MM main.cpp`。
  - 该命令会自动查找main.o的依赖项（各种头文件），并打印在终端上。

### 4.2 伪目标

- 一把来说，一个目标对应一个文件。构建一个目标，就会生成一个与目标重名的文件。
  - 在构建目标时，make会检查目标文件是否已经生成。
    - 如果没有生成，则会执行方法生成该目标文件
    - 如果**已经生成**，则会检查目标的依赖项是否更新，以确定是否执行方法，更新文件。
- 如果一个目标没有的文件，或者说不需要生成相应的文件，那么这个目标被称为**伪目标**。
  - 如果不加指定，所有的目标都**默认为普通目标**。
  - 伪目标最经典的例子就是 目标clean，用于清除make产生的文件。
    - 如果文件中有一个和clean重名的文件，clean的方法就不会被执行。
    - 因为make会认为clean文件的依赖项没有更新（没有依赖项也就没有更新），不需要执行方法更新目标文件。
  - 需要加上规则指明哪些是伪目标。

伪目标指定方法：

- 编写一条特殊规则：`.PHONY: clean`，用于指定clean为伪目标。
- `.PHONY`是一个特殊的目标，其后的依赖项为伪目标名。
- 该规则不用写方法。
- 伪目标不考虑目标文件更新问题，**每次构建伪目标时方法都会被执行**。

例如：

```makefile
# 指定 clean 为伪目标
.PHONY: clean
clean:
        rm *.o
        rm main
```

- .PHONY与其他目标一样可以有多个依赖项，用于指定多个伪目标。

另一种用法：

```makefile
# 指定 main 为伪目标
# 这样，这条规则的方法不论如何（不考虑更新问题）都会被执行一次。
.PHONY: main
main: block.o command.o input.o main.o scene.o test.o
        g++ -Wall -o main block.o command.o input.o main.o scene.o test.o
```

## 5. 依赖

- 依赖分为普通依赖和order-only依赖。
- 普通依赖就是冒号后跟着得依赖项。
  - 如果普通依赖是由其他规则生成的，则会先去执行生成该依赖的那条规则。
  - 普通依赖每次会更新后，会重新执行该条规则的方法，以更新目标文件。

- order-only依赖写在普通依赖的后面，用 `|` 隔开。
  - 如果order-only依赖文件不存在，则会执行生成该依赖的那条规则。
  - 如果order-only依赖文件已经存在，则该依赖文件更新也不会导致该目标被更新。
  - 也就是order-only只有在被生成时，会更新目标。生成后就不更新目标。

例如：

```makefile
# test.o为 order-only依赖，其余为普通依赖
main: block.o command.o input.o main.o scene.o | test.o
        g++ -Wall -o main block.o command.o input.o main.o scene.o test.o
```

- test.o被修改（更新），也不会引起main目标被重新构造。
- order-only和普通依赖都可以为空。即下方两种写法，语法上都允许。
  - `main: | block.o command.o input.o main.o scene.o  test.o`
  - `main: block.o command.o input.o main.o scene.o test.o |` 

## 6. 方法

### 6.1 执行进程

方法中的多行指令运行在不同的进程下。即make调用多个sh进程来执行命令。

例如：

```makefile
test:
		@echo $(SHELL)  # 打印 执行命令的SHELL
        a=2
        @echo a=$${a}  # 打印 a= ，因为无法获取变量a的值，所有a=空。
```

- 第二行定义的变量a，并不能在第三行的命令中取值。
- 因此每一行命令，都是运行在不同进程下的。
- 使用`.ONESHELL:`可以指定同方法的不同行命令在运行在通过一个sh中。

```makefile
.ONESHELL:
test:
        @echo $(SHELL)  # 打印 执行命令的SHELL
        a=2
        @echo a=$${a}  # 打印a=2
```

- 打印结果为：a=2，说明这些方法是在同一个shell中进行的。
- 之所以要用两个`$`来引用变量，是因为`$`在makefile中是引用makefile变量的意思，而这里a是shell中的变量，符号冲突了。
  - makefile中的两个$表示命令行中的一个$。
  - makefile中的一个$表示引用makefile中定义的变量（在方法之外定义的），不会被翻译在命令行中。
  - 下面关于变量的笔记会详细讲到$。


### 6.2 回显问题

make规则的方法中，每执行一条命令前都要先打印这条命令。

- 可以命令前添加@符号，使make不会打印该条命令。
- 或者添加特殊目标`.SILENT`，指定哪些目标不同打印。

例如：

```makefile
test:
        @echo haha  # 打印 haha
```

- 如果不加@，则么先打印命令"echo haha"，再打印执行结果"haha"。
- 如果加上@后，会直接打印执行结果"haha"，而不打印命令。

```makefile
.SILENT: test  # 指定test目标 不打印命令
test:
        @echo haha  # 打印 haha
```

### 6.3 错误忽略

如果方法中有一条命令出错，就不会继续执行下一条命令。

例如：

```makefile
clean:
	rm *.o
	rm main
```

- 如果当前文件夹中没有`*.o`，但是有`main`，
  - 那么执行`make clean`，则执行`rm *.o`就会报错，导致`rm main`无法执行。

命令前加上`-`，make就会忽略该条指令的错误，继续执行下一条命令。

例如：

```makefile
clean:
	-rm *.o
	-rm main
```

- 如果`rm *.o`报错， `rm main`仍然执行。
- `rm *.o` 和 `rm main`都会被执行。

# 二、变量

## 1. 用变量简化makefile

makefile中的变量**类似于**C语言中的**宏定义**，变量值为字符串。

原先的makefile就可以改写为：

```makefile
objs = block.o command.o input.o main.o scene.o test.o

main: $(objs)
        g++ -Wall -o main $(objs)

block.o: block.cpp common.h block.h color.h
        g++ -Wall -c block.cpp

command.o: command.cpp scene.h common.h block.h command.h
        g++ -Wall -c command.cpp

input.o: input.cpp common.h utility.inl
        g++ -Wall -c input.cpp

main.o: main.cpp scene.h common.h block.h command.h input.h test.h
        g++ -Wall -c main.cpp

scene.o: scene.cpp common.h scene.h block.h command.h utility.inl
        g++ -Wall -c scene.cpp

test.o: test.cpp test.h scene.h common.h block.h command.h
        g++ -Wall -c test.cpp

clear:
        rm $(objs)
        rm main
```

- 定义了一个objs的变量，其中包含了所有的目标文件。
- 在需要写出这些目标文件时，只需要引用objs变量就可以了。

## 2. make执行过程

make在执行过程大致有两个阶段

- 第一阶段（读取阶段）：
  - make读取全部Makefile中的内容。
  - 根据Makefile中的内容，在程序内建立起变量。
  - 在程序内构建起显示规则和隐式规则。
  - 建立目标和依赖关系的依赖图。
- 第二阶段（执行阶段）：
  - 用第一阶段构建起来的数据，确定哪些目标需要更新，然后执行那些需要更新的目标的方法。

**注意：**

- 在执行阶段才会对变量（宏）进行展开，这意味着如果第一阶段对同一变量定义了两次，则**第二次的定义会覆盖第一次的定义**。

例如：

```makefile
a = 1
test:
	@echo a=$(a)  # 打印 a=2
a = 2
```

- 打印结果为`a=2`，而不是`a=1`
- 是因为变量的定义在，第一阶段结束后，a=2。
- 执行echo命令在第二阶段，此时a已经为2，而不是1。

## 3. 变量定义

### 2.1 变量定义格式

- a = 1：延迟展开。
- a := 1：立即展开。
- a ::= 1：立即展开，与 `:=` 一样。
- a += 1：追加赋值。
- a ?= 1：条件赋值。
- a != 1：shell赋值。

### 2.2 延迟展开和立即展开：

#### 2.2.1 概述

- 在如果定义变量的值是引用其他变量（或者说对其他变量取值），例如：a=1、b = $(a)。
  - 如果是延迟展开，则在第一阶段时，先不对$(a)展开，仍然保持b = $(a)，在执行阶段再对$(a)展开。
  - 如果是立即展开，则在第一阶段时，变量b的定义$(a)立即被展开成1。
  - 总结：延迟展开是变量的定义第一阶段不展开，留着第二阶段展开。
    - 立即展开是变量的定义第一阶段就展开。

**注意：**

- 延迟展开和立即展开是指**对变量的定义是否立即或延迟展开**。
  - 而不是执行指令中的变量立即被展开。因为一定是第一阶段变量读取完后，才会开始执行指令。

#### 2.2.2 示例1

延迟展开

```makefile
a = 1
b = $(a)
test:
	@echo b=$(b)
a = 2
```

- 打印`b=2`，而不是`b=1`。
- 因为第一阶段结束后b = $(a)、a = 2。
- 第二阶段执行echo $(b)时，会把b展开为$(a)，即$(b) = $(a)，
  - $(b)展开后依然是个引用，继续展开a为2，即$(a) = 2，
  - 因此，echo $(b) -> echo $(a) -> echo 2

而立即展开是这样

```makefile
a = 1
b := $(a)
test:
	@echo b=$(b)
a = 2
```

- 打印`b=1`，而不是`b=2`。
- 在第一阶段时，make从上往下扫描变量，扫描到 b := $(a)时，立刻对a进行展开，此时$(a) = 1。
  - 因此b = 1，而不是b = $(a)。
- 在第二阶段执行echo $(b)时，b就被展开一次，即b = 1。

#### 2.2.3 示例2

```makefile
bar2 := ThisIsBar2No.1
foo := $(bar)
foo2 := $(bar2)

all:
        @echo $(foo)    # 空串，没有内容
        @echo $(foo2)    # ThisIsBar2No.1
        @echo $(ugh)    #

bar := $(ugh)
ugh := Huh?
bar2 := ThisIsBar2No.2
```

- 第一阶段后
  - bar2 = ThisIsBar2No.2。
  - foo = （空，因为bar还没定义）。
  - foo2 = ThisIsBar2No.1。
  - bar = （空）。
  - ugh = Huh？
- 第二阶段
  - echo $(foo) -> echo
  - echo $(foo2) -> echo ThisIsBar2No.1
  - echo $(ugh) -> echo

#### 2.2.4 练习

```makefile
x = hello
y = world
a := $(x)$(y)

x = y
y = z
a := $($(x))

x = y
y = z
z = u
a := $($($(x)))

x = $(y)
y = z
z = Hello
a := $($(x))
```

### 2.3 条件赋值

格式：`a ?= b`

作用：

- 如果a已经定义过了，即a已经有值了，则这条定义不生效。
- 如果a没有定义，则a=b。
- 即a如果是第一次定义，则赋值，否则忽略。

例如：

```makefile
a = 2
a ?= 200
```

- 第一阶段结束后，a = 2。

```makefile
a ?= 200
```

- 第一阶段结束后，a = 200。

### 2.4 追加赋值

格式：`a += b`

作用：

- 在已有的变量的基础上添加内容（用空格隔开）。

例如：

```makefile
a = main.cpp
a += hellp.cpp
```

- 第一阶段后，a=main.cpp hello.cpp

### 2.5 命令赋值

格式：`a != command`

作用：

- 把command执行的结果赋值给变量a。

例如：

```makefile
a != ls
```

### 2.6 多行变量 

如果要定义一个**多行内容**的变量可以使用以下格式：

```makefile
define 变量名
	...
	...  # 多行内容
endef
```

例如：

```makefile
define debug_info
        @echo haha
        @echo lala
        @echo guagua
endef

test:
        $(debug_info)

# 打印结果
# haha
# lala
# guagua
```

- 多行变量的内容常常是多条指令，这几条指令在多个目标中反复用到。
  - 有了多行变量，只要在方法中引用这个变量，就相当于写入了这几条常用的指令。

### 2.7 取消定义

使用`undefine`关键字来取消变量的定义。

例如：

```makefile
a = 100
undefine a
test:
        @echo a=$(a)
```

- 打印空行，因为变量a已经被取消定义了。

### 2.8 环境变量

makefile中可以使用shell中的环境变量。

例如：

bash中定义环境变量：

```bash
export a=2
```

makefile内容：

```makefile
test:
	@echo a=$(a)
```

打印结果为：a=2。

## 4. 变量替换引用

格式：$(变量名:旧字符串=新字符串)

作用：在变量的定义中，查找旧字符串，并将其替换为新字符串。

例如：

```makefile
source = block.cpp command.cpp input.cpp main.cpp scene.cpp test.cpp

main: $(source:.cpp=.o)
        g++ -Wall -o main $(source:.cpp=.o)
        # 或者 g++ -Wall -o main $(source:%.cpp=%.o) %代表文件名主干部分（文件名去除后缀名的部分）

block.o: block.cpp common.h block.h color.h
        g++ -Wall -c block.cpp

command.o: command.cpp scene.h common.h block.h command.h
        g++ -Wall -c command.cpp

input.o: input.cpp common.h utility.inl
        g++ -Wall -c input.cpp

main.o: main.cpp scene.h common.h block.h command.h input.h test.h
        g++ -Wall -c main.cpp

scene.o: scene.cpp common.h scene.h block.h command.h utility.inl
        g++ -Wall -c scene.cpp

test.o: test.cpp test.h scene.h common.h block.h command.h
        g++ -Wall -c test.cpp

clean:
        rm $(source:.cpp=.o)
        rm main
```

- 把source变量值中的所有”.cpp“字符串改为“.o”。
- 变量值中的各项要空格隔开。

## 5. 变量覆盖

在命令行中，可以定义一个变量，并为其赋值。

该变量会被认为在文件末尾定义，因此会覆盖掉之前的定义。

例如：

有makefile文件内容：

```makefile
a = 1
test:
	@echo a=$(a)
```

bash中：

```bash
make a=2
```

- 打印`a=2`，而不是`a=1`。
- 因为命令行定义的a的值把原先的值覆盖了。

如果希望变量不被覆盖，可以在变量名前加上关键字`override`

例如：

```makefile
override a = 1
test:
	@echo a=$(a)
a = 200
```

- 打印a=1
- 无论是在makefile里，还是命令行中，都不会覆盖掉变量a的值（a=1）。

## 6. 目标变量

之前定义的变量都是在全局变量，在所有的目标都可以被引用。

目标变量的作用范围只有一个或几个目标，可以使用以下格式定义目标变量。 

```makfile
target: 变量名 [第二个变量名...]
```

例如：

```makefile
global = hahaha

.PHONY = main first.c second.o third.c
.SILENT: main first.c second.o third.c
main:first.c second.c third.c

first.c: target1 = this is first variable
second.c: target2 = this is secnond variable
first.c:
        echo in first.c:
        echo global variable = $(global)
        echo target variable1 = $(target1)
        echo target variable2 = $(target2)

second.c:
        echo in second.c:
        echo global variable = $(global)
        echo target variable1 = $(target1)
        echo target variable2 = $(target2)

third.c:
        echo in third.c:
        echo global variable = $(global)
        echo target variable1 = $(target1)
        echo target variable2 = $(target2)

# 打印结果
# in first.c:
# global variable = hahaha
# target variable1 = this is first variable
# target variable2 =

# in second.c:
# global variable = hahaha
# target variable2 = this is secnond variable

# in third.c:
# global variable = hahaha
# target variable1 =
# target variable2 =
```

- variable1只在目标first.c中有效。
- variable2只在目标second.c中有效。
- 定义目标变量时，**一个变量需要一行**，无法一行中定义多个变量。

可以使用模式匹配来定义多个目标间的变量

例如：

```makefile
global = hahaha
prerequisite = first.c second.o third.o  # 定义三个依赖

.PHONY = main $(prerequisite)  # 定义所有目标为伪目标，保证每次方法都会被执行
.SILENT: main $(prerequisite)  # 所有规则的方法都不打印命令

main: $(prerequisite)

first.c: target1 = this is first variable  # 变量target1 只在first.c中有效
%.o: target2 = this is second   # 变量target2 只在以.o结尾的目标中有效

first.c:
        echo in first.c:
        echo global variable = $(global)
        echo target variable1 = $(target1)
        echo target variable2 = $(target2)

second.o:
        echo in second.c:
        echo global variable = $(global)
        echo target variable1 = $(target1)
        echo target variable2 = $(target2)

third.o:
        echo in third.c:
        echo global variable = $(global)
        echo target variable1 = $(target1)
        echo target variable2 = $(target2)
        
# in first.c:
# global variable = hahaha
# target variable1 = this is first variable
# target variable2 =

# in second.c:
# global variable = hahaha
# target variable1 =
# target variable2 = this is second

# in third.c:
# global variable = hahaha
# target variable1 =
# target variable2 = this is second
```

- %.o: target2 = this is second：以.o结尾的目标共享target2变量。

## 7. 自动变量

自动变量是在方法中使用的变量

- `@`：表示当前所在规则的目标名。
- `^`：表示当前所在规则的所有依赖。
  - 不包含order-only依赖。
  - 依赖名重复**会合并**。
- `+`：表示当前所在规则的所有依赖。
  - 不包含order-only依赖。
  - 依赖名重复**不会合并**。
- `<`：表示依赖项的第一个。
- `?`：被修改过的依赖
  - 依赖文件的修改时间晚于目标文件的修改时间。
- `|`：表示所有order-only依赖
- `*`：表示目标的主干部分，简单理解为去除后缀的目标名。
- `%`：如果目标不是归档文件，则为空。如果目标是归档文件，则为归档文件的成员文件。

---

- `D`：表示"directory"，必须跟上面的变量连用。表示D前的变量对应的文件所在的路径。
- `F`：表示“file”，必须跟上面的变量连用。表示F前的变量对应的文件的名称。

例如：

有makefile文件内容如下：

```makefile
objs = block.o command.o input.o main.o scene.o test.o

~/play/make/02_sudoku/main: block.o block.o command.o input.o main.o scene.o | test.o
        -@echo "@:$@"
        -@echo "<:$<"
        -@echo "^:$^"
        -@echo "+:$+"
        -@echo "|:$|"
        -@echo "?:$?"
        -@echo "*:$*"
        -@echo "@D:$(@D)"
        -@echo "<F:$(<F)"

        g++ -Wall -o main $(objs)

block.o: block.cpp common.h block.h color.h
        -@echo "*:$*"
        g++ -Wall -c block.cpp

command.o: command.cpp scene.h common.h block.h command.h
        g++ -Wall -c command.cpp

input.o: input.cpp common.h utility.inl
        g++ -Wall -c input.cpp

main.o: main.cpp scene.h common.h block.h command.h input.h test.h
        g++ -Wall -c main.cpp

scene.o: scene.cpp common.h scene.h block.h command.h utility.inl
        g++ -Wall -c scene.cpp

test.o: test.cpp test.h scene.h common.h block.h command.h
        g++ -Wall -c test.cpp

.PHONY: clean
clean:
        rm $(objs)
        rm main
```

接着执行bash命令：

```makefile
touch block.cpp  # 更新command.cpp时间戳，模拟command.cpp文件被改动，引起目标command.o和main被重新构建
make  # 开始构建

# 打印结果：
# *:block
# g++ -Wall -c block.cpp

# @:main
# <:block.o
# ^:block.o command.o input.o main.o scene.o
# +:block.o block.o command.o input.o main.o scene.o
# |:test.o
# ?:block.o
# *:
# @D:/home/zw/play/make/02_sudoku
# <F:block.o
# g++ -Wall -o main block.o command.o input.o main.o scene.o test.o
```

同时sudoku的makefile文件也可以写成这样：

```makefile
objs = block.o command.o input.o main.o scene.o test.o

main: $(objs)
        g++ -Wall -o main $(objs)

block.o: block.cpp common.h block.h color.h
        g++ -Wall -c $<

command.o: command.cpp scene.h common.h block.h command.h
        g++ -Wall -c $<

input.o: input.cpp common.h utility.inl
        g++ -Wall -c $<

main.o: main.cpp scene.h common.h block.h command.h input.h test.h
        g++ -Wall -c $<

scene.o: scene.cpp common.h scene.h block.h command.h utility.inl
        g++ -Wall -c $<

test.o: test.cpp test.h scene.h common.h block.h command.h
        g++ -Wall -c $<

.PHONY: clean
clean:
        rm $(objs)
        rm main
```



## 8. 二次展开

- 依赖中的变量在make读取到后会直接被展开。
- 如果希望依赖中的变量延迟展开，需要特殊目标`.SECONDEXPENSION`。
  - 同时在引用变量时使用两个`$`。

例如：

```makefile
objs = main.cpp hello.cpp

.PHONY: main
.SECONDEXPANSION:
main: $$(objs)
        @echo $^

objs = main.cpp hello.cpp test.cpp
```

- 执行结果是打印`main.cpp hello.cpp test.cpp`，而不是`main.cpp hello.cpp`

# 三、多目标与多方法

## 1. 独立多目标

makefile中一个规则可以有多个目标。当几个不同的目标具有相同的依赖或具有相同的方法时，就可以使用**独立多目标**的写法来**简化**文件。

独立多目标的意思是多个目标写在一条规则中（因为这些目标具有相同的依赖或方法），其等价于每个目标对应一个规则。但是由于这些目标或方法都是一样的，如果一个目标一条规则的话，会写的每场重复、冗余。

例如：

有makefile文件如下

```makefile
all: t1 t2 t3 t4

haha: t1 t2 t3 t4

t1:
        @echo recipe $@ is processing

t2:
        @echo recipe $@ is processing

t3:
        @echo recipe $@ is processing

t4:
        @echo recipe $@ is processing
```

- 目标all和目标haha的依赖是相同的。
- 目标t1、t2、t3、t4的方法是相同的。

上面的makefile可以简化为下面这种形式：

```makefile
all haha: t1 t2 t3 t4
# 等价于：
# all: t1 t2 t3 t4
# haha: t1 t2 t3 t4

t1 t2 t3 t4:
        @echo recipe $@ is processing
# 等价于
# t1:
        @echo recipe $@ is processing
# t2:
        @echo recipe $@ is processing
# t3:
        @echo recipe $@ is processing
# t4:
        @echo recipe $@ is processing    
```

- 独立多目标只是当多个目标有内容相同时（依赖相同和目标相同）的一种简写形式，与之相对应的有组合多目标。

sudoku的makefile文件可以简写为：

```makefile
objs = block.o command.o input.o main.o scene.o test.o

main: $(objs)
        g++ -Wall -o main $(objs)

$(objs):
        g++ -Wall -c $(@:%.o=%.cpp)

.PHONY: clean
clean:
        rm $(objs)
        rm main
```

- 每个目标文件`xxx.o`，都是由对应的`xxx.cpp`得来的。
  - 因此生成每个目标文件的规则都可以是`g++ -Wall -c $(@:%.o=%.cpp)`。
    - @是自动变量，表示目标名`xxx.o`。
    - %为变量@的主干部分。
    - `$(@:%.o=%.cpp)`就表示根据目标文件名`xxx.o`，指定规则`g++ -Wall -c xxx.cpp`
  - 这几个目标文件的生成规则是相同的，因此可以用独立多目标简写为一条规则。
  - 但是这几个目标的依赖不同。
    - 要么把依赖全都写上去（写在独立多目标的依赖项上）。
    - 要么就不写依赖，让makefile自动推导。
- 使用独立多目标就**大大简化**了makefile。

**注意：**

- 独立多目标虽然大大简化了makefile，但是也带来了相关的依赖问题，可以使用静态模式来解决。

## 2. 组合多目标

- 独立多目标是多个目标对应的规则有冗余的简写。

- 而组合多个目标是把多个目标看作一个整体，看作一个大目标。

例如：

如果使用组合多目标，则sudoku的makefile要这么写

```makefile
objs = block.o command.o input.o main.o scene.o test.o

main: $(objs)
        g++ -Wall -o main $(objs)

$(objs) &:
        g++ -Wall -c block.cpp
        g++ -Wall -c command.cpp
        g++ -Wall -c input.cpp
        g++ -Wall -c main.cpp
        g++ -Wall -c scene.cpp
        g++ -Wall -c test.cpp

.PHONY: clean
clean:
        -rm $(objs)
        -rm main
```

## 3. 静态模式

### 3.1 依赖问题

独立多目标虽然非常方便，但同时也带来了依赖问题。

- 由于没有指明依赖，当我们改动头文件或源文件时，make并不检测这些依赖文件是否被改动过（因为makefile不指定这些是依赖），因此make也**不会自动更新**。
- 因此还是要想办法指明一下依赖。
  - 使用静态模式来写出依赖。

静态模式格式：

```makefile
目标名: 目标模式 : 依赖模式
	方法
```

### 3.2 解决一

sodoku的makefile文件可以写成：

```makefile
objs = block.o command.o input.o main.o scene.o test.o

main: $(objs)
        g++ -Wall -o main $(objs)

$(objs): %.o : %.cpp
        g++ -Wall -c $(@:%.o=%.cpp)
        # 或者 g++ -Wall -c $<

.PHONY: clean
clean:
        rm $(objs)
        rm main
```

- 首先第二条规则是独立多目标规则，其等价于

  - ```makefile
    block.o: %.o : %.cpp
              g++ -Wall -c $(@:%.o=%.cpp)
    command.o: %.o : %.cpp
              g++ -Wall -c $(@:%.o=%.cpp)
    ...
    ```

- 其中`%.o : %.cpp`的意思是找到本规则中以.o结尾的目标，并指出该目标对应的依赖为%.cpp。

  - 其中%代表目标的主干部分，即目标名去除后缀名的部分。

  - 就变成了：

  - ```makefile
    block.o: block.cpp
              g++ -Wall -c $(@:%.o=%.cpp)
    command.o: command.cpp
              g++ -Wall -c $(@:%.o=%.cpp)
    ...
    ```

- 这样就实现了为每个独立多目标指明依赖（主要依赖）。

### 3.2 解决二

解决方法一仍存在一定的问题：只是指出了`.cpp`文件为依赖，没有指出`.h`文件（头文件）为依赖（一般来说有一个.cpp文件就有一个.h文件）。这会导致当头文件被改动时，make也检测不到。

- 因此应该再增加`.h`依赖。
- 但是main.cpp没有main.h，因此目标main.o要单独写。

所以sudoku的makefile文件改写为这样：

```makefile
objs = block.o command.o input.o scene.o test.o

main: $(objs) main.o
        g++ -Wall -o main $(objs) main.o

$(objs): %.o : %.cpp %.h
        g++ -Wall -c $<

main.o: %.o : %.cpp
        g++ -Wall -c $<

.PHONY: clean
clean:
        rm $(objs) main.o
        rm main
```

- 目标main.o单独写成一条规则，因为他的依赖项包括min.cpp但不包括main.h。
- main.o也不包含在objs变量里，在构建main目标时，
  - 要手动在依赖项上添加main.o。
  - 在方法上也要手动添加main.o目标文件。

## 4. 多方法

如果一个目标对应着多个方法，例如：

```makefile
main: t1 t2
        @echo $^

main: t3 t4
        @echo $^

t1 t2 t3 t4:
        @echo $@ is excuting
```

- 目标main有两条规则，一条的依赖是t1、t2，另一条t3、t4。

如果在bash中执行make：

```bash
zhangsan@zw-virtual-machine:~/make_practice$ make
Makefile:5: 警告：覆盖关于目标“main”的配方
Makefile:2: 警告：忽略关于目标“main”的旧配方
t3 is excuting
t4 is excuting
t1 is excuting
t2 is excuting
t3 t4 t1 t2
```

- 有两条警告
  - 第一条构建main目标的方法被第二条构建main目标的方法覆盖了。
  - 旧的构建main目标的方法被忽略了。
  - 意味着如果一个目标有多个方法，则只会执行最后一个方法，前面的方法会被丢弃。
- 所有的依赖项都被构建了，并且都被包含进来了。
  - 意味着，虽然main目标的方法只保留了一个，但是多个main目标的依赖项都被包含进来了。
  - make会合并同名目标的依赖项。

利用这个特性，可以用来向目标中添加依赖。例如：

```makefile
main: t1 t2
        @echo $^

main: t3 t4

t1 t2 t3 t4:
        @echo $@ is excuting
```

如果在bash中执行make：

```bash
zhangsan@zw-virtual-machine:~/make_practice$ make
t1 is excuting
t2 is excuting
t3 is excuting
t4 is excuting
t1 t2 t3 t4
```

- 第二个构建main目标的规则只写了依赖，而没写方法。
- make就会执行第一条规则，但是也会把第二条规则包含进来。
- 这样就实现了**向目标中添加依赖的目的，同时又不改变构建目标的方法**。





# 四、指定依赖路径

## 1. VPATH

- 现在工作文件夹的结构是，所有的源程序都是堆在同一目录下。

  - 但是实际中，往往工作文件夹都是结构化的，一般都包括文件夹src（放源程序）、include（放头文件）。

- make一般只在**当前目录**搜索头文件和源程序（.cpp），或者在系统目录中与环境变量指定的目录中搜索。

  - 如果我们把这些文件放到src或include目录下，make就会报错，说找不到依赖。

  - ```bash
    make: *** 没有规则可制作目标“block.cpp”，由“block.o” 需求。 停止。
    ```

- 可以用VPATH来告诉make额外的搜索路径。

  - VPATH是个变量。
  - 而接下来还要介绍的vpath是个make的指令。
  - **注意区分大小写**。

VPATH格式：

```makefile
VPATH = 目录 [:第二个目录]
```

- 把搜索目录存放在VPATH变量中。
- 如果有多个目录的话用`:`隔开。

例如：

当前的工作目录的结构为：

```makefile
.
├── include
│   ├── block.h
│   ├── color.h
│   ├── command.h
│   ├── common.h
│   ├── input.h
│   ├── scene.h
│   ├── test.h
│   └── utility.inl
├── main.cpp
├── Makefile
└── src
    ├── block.cpp
    ├── command.cpp
    ├── hello.cpp
    ├── input.cpp
    ├── scene.cpp
    └── test.cpp
```

如果不告诉make依赖的搜索路径，make会报错：

```bash
zhangsan@zw-virtual-machine:~/play/make/03_sudoku_stuctured$ make
make: *** 没有规则可制作目标“block.cpp”，由“block.o” 需求。 停止。
```

sudoku的makefile文件可以写为：

```makefile
objs = block.o command.o input.o scene.o test.o

VPATH = src:include  # 指定额外的搜索路径为 目录src和inclued（相对路径）

main: $(objs) main.o
        g++ -Wall -o $@ $^

$(objs): %.o : %.cpp %.h
        g++ -Wall -c $<

main.o: %.o : %.cpp
        g++ -Wall -c $<

.PHONY: clean
clean:
        rm $(objs) main.o
        rm main
```

- 这样make就能找到这些被整理起来的依赖文件。

- 但是make没有告诉g++这些路径，因此g++会报错：

  - ```
    zhangsan@zw-virtual-machine:~/play/make/03_sudoku_stuctured$ make
    g++ -Wall -c src/block.cpp #-Iinclude
    src/block.cpp:3:10: fatal error: common.h: 没有那个文件或目录
        3 | #include "common.h"
          |          ^~~~~~~~~~
    compilation terminated.
    make: *** [Makefile:11：block.o] 错误 
    ```

- 因此还要修改一下规则中的方法，告诉g++头文件的路径：

- ```makefile
   g++ -Wall -c $< -Iinclude
  ```

所以**最终**，sudoku的makefile文件可以写为：

```makefile
objs = block.o command.o input.o scene.o test.o

VPATH = src:include  # 指定额外的搜索路径为 目录src和inclued

main: $(objs) main.o
        g++ -Wall -o $@ $^

$(objs): %.o : %.cpp %.h
        g++ -Wall -c $< -Iinclude
        
main.o: %.o : %.cpp
        g++ -Wall -c $< -Iinclude

.PHONY: clean
clean:
        rm $(objs) main.o
        rm main
```

**注意：**

- 指定路径时，路径可以是相对路径也可以是绝对路径。
- 多个路径用`:`隔开。

## 2. vpath

不同于VPATH，vpath是一个make的指令，相较于VAPTH更加灵活。

格式：

```makefile
vpath 匹配模式 路径
```

例如：

```makefile
vapth %.h include  # 表示如果要找 *.h文件，去目录inlcude下搜索 
vpath %.cpp src  # 表示如果要找 *.cpp文件，去目录src下搜索 
vapth % include:  # 表示如果要找 任意文件，去目录inlcude或src下搜索 
```

则sudoku的makefile文件也可以写为：

```makefile
objs = block.o command.o input.o scene.o test.o

vpath %.h include
vpath %.cpp src
# 或者
# vpath % src:include

main: $(objs) main.o
        g++ -Wall -o $@ $^

$(objs): %.o : %.cpp %.h
        g++ -Wall -c $< -Iinclude

main.o: %.o : %.cpp
        g++ -Wall -c $< -Iinclude

.PHONY: clean
clean:
        rm $(objs) main.o
        rm main
```

# 五、条件判断

## 1. ifdef、ifnef

- 判断一个变量是否已经定义，或者是否没有定义。
- 如果满足条件，就执行下面一段的程序。

格式：

```makefile
ifdef 变量名
	...
[else] [[ifdef]]
endif
```

例如：

```makefile
#var = haha
var2 = lala

haha:
        @echo

ifdef var
        $(info var = $(var))
else ifdef var2
        $(info var = $(var2))
else
        $(info var is not defined)
endif
```

- 如果定义了变量var，就执行`$(info var = $(var))`。
- 如果没定义变量var，但是定义了变量var2，就执行`$(info var = $(var2))`。
- 如果都没定义，就执行`$(info var is not defined)`。
- 目标haha是为了确保make能够运行的起来，不是终点。

## 2. ifeq、ifneq

判断两个字符串（变量）是否相等或不等。

格式：

```
ifeq (string1, string2)
	...
endif
```

例如：

```makefile
var = lalz
var2 = lala

haha:
        @echo

ifeq ($(var), $(var2))
        $(info var = var2)
else ifeq ("123", "123")
        $(info 123 = 123)
else
        $(info var != var2)
endif
```

- 如果 变量var 和 变量var2相等，则执行$(info var = var2)。
- 字符串也可以用 `''` 或 `""` 表示。

# 六、函数

## 概述

makefile中的函数调用类似于引用变量的格式，都使用了符号`$`。

格式：

```
$(函数名 参数[,第二个参数])
```

**注意：**

- 函数参数之间用逗号隔开。
- 函数参数之间不要有空格，否则空格会被当作参数。

一个基本函数：

```makefile
$(info 要输出的内容)
```

- info后跟要输出的内容。

例如：

```makefile
name = zhangsan
$(info my name is $(name))

haha:
	@echo
```

- 打印结果：my name is zhangsan

## 函数定义

函数的定义和定义变量的格式差不多。

格式：

```
函数名 = 函数体
```

- 函数的返回值就是函数体内的所有内容，即函数名等号后的所有内容。
- 在函数体内引用$(1)、$(2)……来引用传入的参数，$(1)代表参数1，$(2)代表参数2。
- 如果相应的参数没有传进来，该引用为空串。

例如：

```makefile
hello = hello, $(1)!  # 定义一个hello 函数
result = $(call hello,world)  # 定义一个变量，这个变量用于执行hello函数，并向函数中传入参数world

.PHONY: print
print:
        $(info result: $(result))  # 引用result变量，就相当于执行了result中的内容，也就是执行了hello函数
```

执行结果：

```bash
result: hello, world!
```

- hello是函数名，world是实参，$(1)是形参。

**注意**：

- **自定义的函数只能**通过`$(call 函数名，参数列表)`的格式来调用；
- 不能像makefile内置函数一样用`$(函数名，参数列表)`的格式来调用；
- 只能**内置函数能**用`$(函数名，参数列表)`的格式来调用。

## 字符串处理函数

### subst

字符串替换。

格式：

```makefile
$(subst 旧字符,新字符,字符串)
# 把字符串中的旧字符替换为新字符
```

例如：

```makefile
files = main.cpp src/command.cpp src/block.cpp src/input.cpp src/scene.cpp src/test.cpp
result = $(subst .cpp,.o,$(files))

print:
        $(info origin: $(files))
        $(info result: $(result))
```

执行结果：

```bash
origin: main.cpp src/command.cpp src/block.cpp src/input.cpp src/scene.cpp src/test.cpp
result: main.o src/command.o src/block.o src/input.o src/scene.o src/test.o
```

- files中的".c"都被替换为了".o"。

### patsubst

使用模式匹配来替换字符串。

格式：

```makefile
$(patsubst 旧模式,新模式,字符串)
```

例如：

```makefile
files = main.cppsrc/command.cpp src/block.cpp src/input.cpp src/scene.cpp src/test.cpp
result = $(patsubst %.cpp,%.o,$(files))

.PHONY: print
print:
        $(info origin: $(files))
        $(info result: $(result))
```

执行结果：

```bash
origin: main.cppsrc/command.cpp src/block.cpp src/input.cpp src/scene.cpp src/test.cpp
result: main.cppsrc/command.o src/block.o src/input.o src/scene.o src/test.o
```

- files中空格两边的字符串被认为是一项，模式匹配是匹配项，而不是匹配整个字符串。
- 每一项的后缀都从`.cpp`变为了`.o`。
- main.cpp没有变成main.o，因为他和src/command.cpp之间没有空格，两个一起被当作一项。
- 如果使用subst就可以使main.cpp变为main.o。
  - 因为subst是把搜索匹配整个字符串。
  - 这也是`subst`和`patsubst`的区别。

### strip

去除首位的空格，与中间多余的空格。

格式：

```makefile
$(strip 字符串)
```

例如：

```makefile
files =    main.cpp     src/command.cpp src/block.cpp src/input.cpp src/scene.cpp src/test.cpp
result = $(strip $(files))

.PHONY: print
print:
        $(info origin: $(files))
        $(info result: $(result))
```

执行结果：

```bash
origin: main.cpp     src/command.cpp src/block.cpp src/input.cpp src/scene.cpp src/test.cpp
result: main.cpp src/command.cpp src/block.cpp src/input.cpp src/scene.cpp src/test.cpp
```

- 中间多余的空格被去除了。

### find

在字符串中查找子字符串。如果查找到就返回查找到的结果，如果没有查找到就返回空串。

格式：

```makefile
$(find 子字符串,字符串)
```

例如：

```makefile
files = main.cpp src/command.cpp src/block.cpp src/input.cpp src/scene.cpp src/test.cpp
result = $(findstring .cpp,$(files))  # 在字符串中查找子字符串.cpp
result2 = $(findstring .h,$(files))  # 在字符串中查找子字符串.h

.PHONY: print
print:
        $(info origin: $(files))
        $(info result: $(result))
        $(info result2: $(result2))
```

执行结果：

```bash
origin: main.cpp src/command.cpp src/block.cpp src/input.cpp src/scene.cpp src/test.cpp
result: .cpp
result2:
```

- 找到了就打印结果（好像有点鸡肋）。

### filter

在字符串中匹配项。

格式：

```makefile
$(filter 匹配模式,字符串 )
```

例如：

```makefile
files = main.cpp main.h main.o src/command.cpp src/block.cpp src/input.cpp src/scene.cpp src/test.cpp
result = $(filter %.cpp,$(files))
result2 = $(filter %.h,$(files))
result3 = $(filter %.o,$(files))

.PHONY: print
print:
        $(info origin: $(files))
        $(info result: $(result))
        $(info result2: $(result2))
        $(info result3: $(result3))
```

执行结果：

```bash
origin: main.cpp main.h main.o src/command.cpp src/block.cpp src/input.cpp src/scene.cpp src/test.cpp
result: main.cpp src/command.cpp src/block.cpp src/input.cpp src/scene.cpp src/test.cpp
result2: main.h
result3: main.o
```

- result是搜索后缀为.cpp的文件。
- result2搜索后缀为.h的文件。
- result3搜索后缀为.o的文件。

### filter-out

和filter的作用相反，返回去除掉匹配项的字符串。

格式：

```makefile
$(fileter-out 子字符串,字符串)
```

例如：

```makefile
files = main.cpp main.h main.o src/command.cpp src/block.cpp src/input.cpp src/scene.cpp src/test.cpp
result = $(filter-out %.cpp,$(files))
result2 = $(filter-out %.h,$(files))
result3 = $(filter-out %.o,$(files))

.PHONY: print
print:
        $(info origin: $(files))
        $(info result: $(result))
        $(info result2: $(result2))
        $(info result3: $(result3))
```

执行结果：

```bash
origin: main.cpp main.h main.o src/command.cpp src/block.cpp src/input.cpp src/scene.cpp src/test.cpp
result: main.h main.o
result2: main.cpp main.o src/command.cpp src/block.cpp src/input.cpp src/scene.cpp src/test.cpp
result3: main.cpp main.h src/command.cpp src/block.cpp src/input.cpp src/scene.cpp src/test.cpp
```

- result是排除了`.cpp项`的原字符串。
- result2是排除了`.h项`的字符串。
- result3是排除`.o`项的字符串。

### sort

对字符串各项按照字典顺序排序。

格式：

```makefile
$(sort 字符串)
```

例如：

```makefile
files = main.cpp main.h main.o src/command.cpp src/block.cpp src/input.cpp src/scene.cpp src/test.cpp
result = $(sort $(files))

.PHONY: print
print:
        $(info origin: $(files))
        $(info result: $(result))
```

执行结果：

```bash
origin: main.cpp main.h main.o src/command.cpp src/block.cpp src/input.cpp src/scene.cpp src/test.cpp
result: main.cpp main.h main.o src/block.cpp src/command.cpp src/input.cpp src/scene.cpp src/test.cpp
```

- 返回按照字典顺序的变量files中的各项。

### word

得到第n项。

格式：

```makefile
$(word n,字符串)
```

例如：

```makefile
files = main.cpp main.h main.o src/command.cpp src/block.cpp src/input.cpp src/scene.cpp src/test.cpp
result = $(word 3,$(files))

.PHONY: print
print:
        $(info origin: $(files))
        $(info result: $(result))
```

执行结果：

```bash
origin: main.cpp main.h main.o src/command.cpp src/block.cpp src/input.cpp src/scene.cpp src/test.cpp
result: main.o
```

- 第三项是main.o。

**注意：**

- 项的下标是**从1开始**的。
- 如果索引下标**小于**1（例如：0），则make会报错。
- 如果索引下标**大于**字符串所有的项数，则返回空串。

### wordlist

得到字符串中第m项到第n项。

格式：

```makefile
$(wordlist m,n,字符串)
```

例如：

```makefile
files = main.cpp main.h main.o src/command.cpp src/block.cpp src/input.cpp src/scene.cpp src/test.cpp
result = $(wordlist 2,4,$(files))

.PHONY: print
print:
        $(info origin: $(files))
        $(info result: $(result))
```

执行结果：

```bash
origin: main.cpp main.h main.o src/command.cpp src/block.cpp src/input.cpp src/scene.cpp src/test.cpp
result: main.h main.o src/command.cpp
```

- 打印变量files中第二项到第4项的内容，包括第二项和第四项

### words

得到字符串的项数。被逗号分隔的称作一项。

格式：

```makefile
$(words 字符串)
```

例如：

```makefile
files = main.cpp main.h main.o src/command.cpp src/block.cpp src/input.cpp src/scene.cpp src/test.cpp
result = $(words $(files))

.PHONY: print
print:
        $(info origin: $(files))
        $(info result: $(result))
```

执行结果：

```bash
origin: main.cpp main.h main.o src/command.cpp src/block.cpp src/input.cpp src/scene.cpp src/test.cpp
result: 8
```

- 变量files中有8项。

### firstword

得到字符串中第1项。

格式：

```makefile
$(firstword 字符串)
```

例如：

```makefile
files = main.cpp main.h main.o src/command.cpp src/block.cpp src/input.cpp src/scene.cpp src/test.cpp
result = $(firstword $(files))

.PHONY: print
print:
        $(info origin: $(files))
        $(info result: $(result))
```

执行结果：

```bash
origin: main.cpp main.h main.o src/command.cpp src/block.cpp src/input.cpp src/scene.cpp src/test.cpp
result: main.cpp
```

- 变量files中的第一项为main.cpp

### lastword

得到字符串中最后一项。

格式：

```makefile
$(lastword 字符串)
```

例如：

```makefile
files = main.cpp main.h main.o src/command.cpp src/block.cpp src/input.cpp src/scene.cpp src/test.cpp
result = $(lastword $(files))

.PHONY: print
print:
        $(info origin: $(files))
        $(info result: $(result))
```

执行结果：

```bash
origin: main.cpp main.h main.o src/command.cpp src/block.cpp src/input.cpp src/scene.cpp src/test.cpp
result: src/test.cpp
```

- 变量files中的最后一项是 src/test.cpp

## 文件名处理函数

### dir

得到字符串中各项所在的目录。

格式：

```makefile
$(dir 字符串)
```

例如：

```makefile
files = main.cpp main.h main.o src/command.cpp src/block.cpp src/input.cpp src/scene.cpp src/test.cpp
result = $(dir $(files))

.PHONY: print
print:
        $(info origin: $(files))
        $(info result: $(result))
```

执行结果：

```bash
origin: main.cpp main.h main.o src/command.cpp src/block.cpp src/input.cpp src/scene.cpp src/test.cpp
result: ./ ./ ./ src/ src/ src/ src/ src/
```

- 每一项都被打印目录。

**注意：**

- **每一项的目录并不是make搜索到的**。
- make只是根据字符串的内容，简单地提取出每一项最后一个`/`前的内容作为目录。
- 所以可以看到 main.h和main.o这两个不存在的文件，make也打印出了他们的目录。

### notdir

和dir的作用相反，得到字符串每项去除掉路径后的文件名。

格式：

```makefile
$(notdir 字符串)
```

例如：

```makefile
files = ~/main.cpp main.h main.o src/command.cpp src/block.cpp src/input.cpp src/scene.cpp src/test.cpp
result = $(notdir $(files))

.PHONY: print
print:
        $(info origin: $(files))
        $(info result: $(result))
```

执行结果：

```bash
origin: ~/main.cpp main.h main.o src/command.cpp src/block.cpp src/input.cpp src/scene.cpp src/test.cpp
result: main.cpp main.h main.o command.cpp block.cpp input.cpp scene.cpp test.cpp
```

- 每一项都被去除了路径。

注意：

- 和dir一样，make只是根据简单地每项的字符串内容来得到文件名。
- 下面的函数也都是这样，**这些函数都是简单地对字符串处理**，而不会去探究文件和路径的真实性。

### suffix

得到字符串中每项的后缀名。

格式：

```makefile
$(suffix 字符串)
```

例如：

```makefile
files = ~/main.cpp main.h main.o src/command.cpp src/block.cpp src/input.cpp src/scene.cpp src/test.cpp
result = $(notdir $(files))

.PHONY: print
print:
        $(info origin: $(files))
        $(info result: $(result))
```

执行结果：

```bash
origin: ~/main.cpp main.h main.o src/command.cpp src/block.cpp src/input.cpp src/scene.cpp src/test.cpp
result: .cpp .h .o .cpp .cpp .cpp .cpp .cpp
```

- 得到变量files中每项的后缀名。

### basename

与suffix相反，得到字符串中去除掉后缀的每项。

格式：

```makefile
$(basename 字符串)
```

例如：

```makefile
files = ~/main.cpp main.h main.o src/command.cpp src/block.cpp src/input.cpp src/scene.cpp src/test.cpp
result = $(basename $(files))

.PHONY: print
print:
        $(info origin: $(files))
        $(info result: $(result))
```

执行结果：

```bash
origin: ~/main.cpp main.h main.o src/command.cpp src/block.cpp src/input.cpp src/scene.cpp src/test.cpp
result: ~/main main main src/command src/block src/input src/scene src/test
```

- 得到去除掉后缀的每项。
- basename包括文件的路径，只是简单地去除掉每项的后缀。

### addsuffix

为字符串中每一项添加后缀。

格式：

```makefile
$(addsuffix 后缀,字符串)
```

例如：

```makefile
files = main.cpp main.h main.o src/command.cpp src/block.cpp src/input.cpp src/scene.cpp src/test.cpp
result = $(addsuffix .x,$(files))

.PHONY: print
print:
        $(info origin: $(files))
        $(info result: $(result))
```

执行结果：

```bash
origin: main.cpp src/command.cpp src/block.cpp src/input.cpp src/scene.cpp src/test.cpp
result: main.cpp.x src/command.cpp.x src/block.cpp.x src/input.cpp.x src/scene.cpp.x src/test.cpp.x
```

- 变量files中的每一项都被添加了后缀`.x`。
- make不会管你原来的后缀是什么，它只是简单地在每项后面添加字符串。

### addprefix

为字符串中每一项添加前缀。

格式：

```makefile
$(addprefix 前缀,字符串)
```

例如：

```makefile
files = main.cpp main.h main.o src/block.cpp src/input.cpp src/scene.cpp src/test.cpp
result = $(addprefix /,$(files))

.PHONY: print
print:
        $(info origin: $(files))
        $(info result: $(result))
```

执行结果：

```bash
origin: main.cpp main.h main.o src/block.cpp src/input.cpp src/scene.cpp src/test.cpp
result: /main.cpp /main.h /main.o /src/block.cpp /src/input.cpp /src/scene.cpp /src/test.cpp
```

- 变量files每一项前都被添加了前缀`/`。

### join

将两个列表中的内容一一连接，如果两列表长度不一致，而不匹配的部分保持原样，类似于python的zip函数。

格式：

```makefile
$(join 列表1,列表2)
```

例如：

```makefile
files = main.cpp main.h main.o
suffixs = .c .x
result = $(join $(files),$(suffixs))

.PHONY: print
print:
        $(info origin: $(files))
        $(info result: $(result))
```

执行结果：

```bash
origin: main.cpp main.h main.o
result: main.cpp.c main.h.x main.o
```

- `main.cpp`与`.c`连接了起来，`main.h`与`.x`连接了起来。
- main.o没有字符串可以连，所以保持原样。

### wildcard

用**通配符**匹配当前目录内容，相当于在makefile中执行`ls 通配符`。

格式：

```makefile
$(wildcard 通配符)
```

例如：

当前目录内容：

```bash
zhangsan@zw-virtual-machine:~/play/make/03_sudoku_stuctured$ ls
include  main.cpp  Makefile  makefiles  src
```

makefile内容：

```makefile
files = main.cpp main.h main.o src/block.cpp src/input.cpp src/scene.cpp src/test.cpp
result = $(wildcard src/*.cpp)
result2 = $(wildcard *.cpp)

.PHONY: print
print:
        $(info origin: $(files))
        $(info result: $(result))
        $(info result2: $(result2))
```

执行结果：

```bash
origin: main.cpp main.h main.o src/block.cpp src/input.cpp src/scene.cpp src/test.cpp
result: src/input.cpp src/command.cpp src/scene.cpp src/hello.cpp src/block.cpp src/test.cpp
result2: main.cpp
```

- result列出了src目录（相对目录）下所有后缀为`.cpp`的文件。
- result2列出了当前目录下所有后缀为`.cpp`的文件。

### realpath

得到字符串中每项的绝对路径。

格式：

```makefile
$(realpath 字符串)
```

例如：

```makefile
files = main.cpp main.h main.o
result = $(realpath $(files))

.PHONY: print
print:
        $(info origin: $(files))
        $(info result: $(result))
```

执行结果：

```bash
origin: main.cpp main.h main.o
result: /home/zhangsan/play/make/03_sudoku_stuctured/main.cpp
```

注意：

- 这个函数make真真地搜索路径，而不是简单地字符串处理。
- 如果搜索不到，会返回空串。
  - main.h和main.o实际不存在，所有返回的是空串。

### abspath

得到字符串中第m项到第n项。

格式：

```makefile
$(abspath 字符串)
```

例如：

```makefile
files = main.cpp main.h main.o
result = $(abspath $(files))

.PHONY: print
print:
        $(info origin: $(files))
        $(info result: $(result))
```

执行结果：

```bash
origin: main.cpp main.h main.o
result: /home/zhangsan/play/make/03_sudoku_stuctured/main.cpp /home/zhangsan/play/make/03_sudoku_stuctured/main.h /home/zhangsan/play/make/03_sudoku_stuctured/main.o
```

- 同样是得到绝对路径，与realpath不同的是，
  - abspath不会考究文件是否存在，依然简单地在字符串层面上处理。
  - abspath只是简单地在每项前加上了当前工作路径作为前缀。

## 条件判断

### if

相当于c语言中的三元运算符。如果条件为真或者不为空，则返回第二个参数，否则返回第三个参数。

格式：

```makefile
$(if 条件,返回值1,返回值2)
```

例如：

```makefile
files = main.cpp main.h main.o src/block.cpp src/input.cpp src/scene.cpp src/test.cpp
result = $(if $(files),1,0)
result2 = $(if $(files2),1,0)

.PHONY: print
print:
        $(info origin: $(files))
        $(info result: $(result))
        $(info result2: $(result2))
```

执行结果：

```bash
origin: main.cpp main.h main.o src/block.cpp src/input.cpp src/scene.cpp src/test.cpp
result: 1
result2: 0
```

- 变量files有内容，所以result的值是1。
- 变量files2未定义，即没有内容，所以result2的值是0。

### and

如果条件中全有内容，则返回最后一个条件。

如果条件中有一个为空，则返回空串。

格式：

```makefile
$(and 条件1,[条件2,[条件3,...]])
```

例如：

```makefile
v1 = 1
v2 = 2
v3 =
v4 = 4
result = $(and $(v1),$(v2),$(v3),$(v4))
result2 = $(and $(v1),$(v2),$(v4))

.PHONY: print
print:
        $(info result: $(result))
        $(info result2: $(result2))
```

执行结果：

```bash
result:
result2: 4
```

- 因为v3为空，所以result为空。
- 因为v1、v2、v4都不为空，所以result2的值为v4的值。

### or

如果条件不全为空，则返回第一个不为空的条件。

如果条件全为空，则返回空串。

格式：

```makefile
$(or 条件1,[条件2,[条件3,...]])
```

例如：

```makefile
v1 =
v2 = 2
v3 =
v4 = 4
result = $(or $(v1),$(v2),$(v3),$(v4))

.PHONY: print
print:
        $(info result: $(result))
```

执行结果：

```bash
result: 2
```

- 第一个不为空的值是变量v2的值：2。

### intcmp

比较两个整数的大小。

- 如果只给定这两个整数作为参数，则
  - 只有当两个整数相等时，返回该相等的值。
  - 当两个整数不相等时，返回空串。
- 如果给定**三个**参数，则
  - 当整数1小于整数2时，返回第3个参数。
  - 其他情况都为空。
- 如果给定**四个**参数，则
  - 当整数1小于整数2时，返回第3个参数。
  - 当整数1小于整数2时，返回第4个参数。
  - 其他情况都为空。
- 如果给定**五个**参数，则
  - 当整数1小于整数2时，返回第3个参数。
  - 当整数1等于整数2时，返回第4个参数。
  - 当整数1大于整数2时，返回第5个参数。

**需要make版本4.4以上才可以。**

格式：

```makefile
$(intcmp 整数1,整数2,[参数3,[参数4,[参数5]]])
```

版本不够，没试验过。

## 其他函数

### file

读写文件内容。

格式：

```makefile
$(file 操作 文件名,[内容])
```

操作：

- `>`：写入。
- `>>`：追加。
- `<`：读取。

例如：

```makefile
text = this is from makefile
$(file > 111.txt,$(text))
$(file >> 111.txt,add something)
read = $(file < 111.txt)

.PHONY: print
print:
        $(info have read: $(read))
```

执行结果：

当前工作目录下生成了111.txt，其中内容是

```bash
this is from makefile
add something
```

命令行窗口打印：

```bash
have read: this is from makefile
add something
```

- 111.txt先被make创建出来，然后写入变量text的内容。
- 然后111.txt再被追加"add something"。
- 然后111.txt被读取到变量read中，被info函数打印出来。

### foreach

对列表中的每一项执行操作，类似于python中的for。

格式：

```makefile
$(foreach 变量名,列表,对变量的操作)
```

例如：

```makefile
files = main.cpp command.cpp block.cpp scene.cpp
result = $(foreach f,$(files),$(patsubst %.cpp,%.o,$(f)))

.PHONY: print
print:
        $(info result: $(result))
```

相当于python的：

```python
files = ["main.cpp", "command.cpp", "block.cpp", "scene.cpp"]
result = [f.replace(".cpp", ".o") for f in files]
print(result)
```

执行结果：

```bash
result: main.o command.o block.o scene.o
```

### call

调用一个函数。

格式：

```makefile
$(call 函数名,[函数参数])
```

例如：

```makefile
dirof = $(dir $(realpath $(1)))  # 得到文件的绝对路径（不包括文件名）
result = $(call dirof,main.cpp)

.PHONY: print
print:
        $(info result: $(result))
```

执行结果：

```bash
result: /home/zhangsan/play/make/03_sudoku_stuctured/  # main.cpp的绝对路径
```

- dirof是自定的函数，可以使用call命令调用。
- 自定义函数没法像makefile中的内置函数一样用`$`调用，只能通过call函数调用

### value

- 对于一个延迟展开（递归展开）的变量，可以查看该变量的原始定义。
- 对于一个立即展开（简单展开）的变量，可以查看该变量的值。

格式：

```makefile
$(value 变量名)
```

例如：

```makefile
v1 = 123
v2 = $(v1)
v3 := $(v1)

result = $(value v2)
result2 = $(value v3)

.PHONY: print
print:
        $(info result: $(result))
        $(info result2: $(result2))
```

执行结果：

```bash
result: $(v1)
result2: 123
```

- 变量v2是延迟展开，因此result的值是v2的原始定义。
- 变量v3是立即展开，因此result2的值是123。

### origin

得到一个变量的来源。

格式：

```makefile
$(origin 变量名)
```

变量来源：

- undefined：未定义变量。
- file：自定义变量。
- default：默认变量。
- automatic：自动变量。
- enviroment：环境变量。

例如：

```makefile
v1 = 123
v2 = $(v1)

result = $(origin v2)
result2 = $(origin v3)
result3 = $(origin CC)
result4 = $(origin @)
result5 = $(origin USER)

.PHONY: print
print:
        $(info result: $(result))
        $(info result2: $(result2))
        $(info result3: $(result3))
        $(info result4: $(result4))
        $(info result5: $(result5))
```

执行结果：

```bash
result: file
result2: undefined
result3: default
result4: automatic
result5: environment
```

- v2是自定义的变量。
- v3是未定义的变量。
- CC是makefile中内置的（默认的）变量，用于指定默认的c语言编译器。
- @是自动变量，表示目标名。
- USER是bash中的环境变量。

### flavor

得到变量的类型：简单展开（立即展开）、递归展开（延迟展开）和未定义。

格式：

```makefile
$(flavor 变量名)
```

例如：

```makefile
v1 = 123
v2 = $(v1)
v3 := $(v1)

result = $(flavor v1)
result2 = $(flavor v2)
result3 = $(flavor v3)
result4 = $(flavor v4)

.PHONY: print
print:
        $(info result: $(result))
        $(info result2: $(result2))
        $(info result3: $(result3))
        $(info result4: $(result4))
```

执行结果：

```bash
result: recursive
result2: recursive
result3: simple
result4: undefined
```

- v1使用的赋值符号是`=`，所以result是recursive，表示递归展开。
- v2使用的赋值符号也是`=`，所以result2是recursive，表示递归展开。
- v3使用的赋值符号是`:=`，所以result3是simple，表示简单展开。
- v1未定义，所以result是undefined。

### eval

把一串字符串，当作实际的makefile内容。

格式：

```makefile
$(eval 字符串)
```

例如：

```makefile
define haha_project =
haha:
        @echo haha
endef

$(eval $(haha_project))
```

执行结果：

```bash
haha
```

- 变量haha_project的值，被当作makefile的内容处理，因此生成了目标haha，并执行了echo haha。

### shell

执行shell命令，并返回执行结果，类似于变量赋值里的`!=`。

格式：

```makefile
$(shell shell命令)
```

例如：

当前工作目录：

```bash
zhangsan@zw-virtual-machine:~/play/make/03_sudoku_stuctured$ ls
include  main.cpp  Makefile  makefiles  src
```

makefile内容如下：

```makefile
result = $(shell ls)

.PHONY: print
print:
        $(info $(result))
```

执行结果：

```bash
include main.cpp Makefile makefiles src
```

- result的值是shell命令ls的结果。

### let

常用于分离变量。这个也需要GUN4.4版本以上。

格式：

```makefile
$(let 变量名 [变量名2 [变量3 ...]],列表,对变量的操作)
```

版本不够，没法测试。

## 信息提示与控制函数

### error

提示错误信息。

格式：

```makefile
$(error 错误信息)
```

例如：

```makefile
msg = something wrong!
.PHONY: print
print:
        $(error $(msg))
        $(info nothing wrong)
```

执行结果：

```bash
Makefile:4: *** something wrong!。 停止。
```

- 输出错误信息“something wrong!”。
- 并且make停止执行，info函数没有被执行。

### warning

提示警告信息。

格式：

```makefile
$(warning 警告信息)
```

例如：

```makefile
msg = something wrong!
.PHONY: print
print:
        $(warning $(msg))
        $(info nothing wrong)
```

执行结果：

```bash
Makefile:4: something wrong!
nothing wrong
```

- 输出警告信息“something wrong!”。
- 并且make继续执行，info函数被执行。

### info

提示信息。

格式：

```makefile
$(info 信息)
```

例如：

```makefile
msg = something wrong!
.PHONY: print
print:
        $(info $(msg))
```

执行结果：

```bash
something wrong!
```

- 输出信息“something wrong!”。
- 没有make的警告或错误标志，只是输出一段字符串。

# 七、自动推导与隐式规则

有一些常用的规则会被make自动推导出来，例如：从.cpp文件到.o文件，从.o文件到可执行文件。这些规则的方法可以不用写出来，make会帮你自动补全。

## 1. 预定义的规则

### c语言编译

从`.c`文件到`.o`文件

```makefile
$(CC) $(CPPFLAGS) $(CFLAGS) -c
```

### c++编译

从`.cpp`、`.cc`、`.C`文件到`.o`文件

```makefile
$(CXX) $(CPPFLAGS) $(CXXFLAGS) -c
```

### 链接

从`.o`文件到可执行文件

```makefile
$(CC) $(LDFLAGS) *.o $(LDLIBS) $(LOADLIBES)
```

## 2. 隐式规则常用的变量

下面这些变量会被自动推导时调用，来构建隐式规则。

这些变量都有默认值，如果想要操纵隐式规则，就可以自己更改这些变量的值。- 

- `CC`：c语言的编译器，默认为`gcc`。

- `CXX`：c++的编译器，默认为`g++`。

- `CPP`：c语言的预处理器，默认为`gcc -E`。

- `AR`：归档器，默认为`ar`。

- `RM`：删除命令，默认为`rm -f`。

- `CFLAGS`：使用c语言编译器时的选项，默认为空。
  - 可以添加`-I`来指定头文件路径。

- `CXXFLAGS`：使用c++编译器时的选项，默认为空。
  - 可以添加`-I`来指定头文件路径。

- `LDFLAGS`：使用链接器时的选项，默认为空。
  - 可以添加`-L`来指定库文件的路径

- `LDLIBS`：使用链接器时的选项，默认为空。
  - 可以添加`-l`来指定要链接的库文件。





# 八、模式匹配



# 九、嵌套makefile



```makefile
# 指定编译选项
CXX = g++
CXXFLAGS = -Wall -I$(include_dir)
LDFLAGS = -Wall
VPATH = ../include

# 用于给文件名添加路径前缀，第一个参数为路径，第二个参数为文件名列表
add_path = $(2:%=$(1)/%)
target_bin = $(call add_path,$(bin_dir),$@)

# 指定各编译文件路径
src_dir = .
include_dir = ../include
bin_dir = ../bin

# 各文件名
srcs = main.cpp common.cpp graph.cpp AMGraph.cpp ALGraph.cpp OLGraph.cpp
objs = $(srcs:.cpp=.o)
srcs_full = $(call add_path,$(src_dir),$(srcs))
objs_full = $(call add_path,$(bin_dir),$(objs))

main: $(objs)
    $(CXX) $(LDFLAGS) $(objs_full) -o $(target_bin)
    $(target_bin)

main.o: main.cpp AMLGraph.h
    $(CXX) $(CXXFLAGS) -c $< -o $(target_bin)

%.o: %.cpp %.h
    $(CXX) $(CXXFLAGS) -c $< -o $(target_bin)

.PHONY:clean
clean:
    -rm $(bin_dir)/*
```


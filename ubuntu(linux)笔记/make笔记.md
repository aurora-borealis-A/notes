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
  - 如果order-only依赖文件已经存在，则不会该依赖文件更新也不会导致该目标被更新。
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
        a=3
        @echo a=$(a)  # 打印 a= ，因为无法获取变量a的值，所有a=空。
```

- 第二行定义的变量a，并不能在第三行的命令中取值。
- 因此每一行命令，都是运行在不同进程下的。
- 使用`.ONESHELL:`可以指定同方法的不同行命令在运行在通过一个sh中。

```makefile
.ONESHELL:
test:
        @echo $(SHELL)  # 打印 执行命令的SHELL
        a=2
        @echo a=${a}  # 打印a=2
```

- 这个没有试成功，应该是要打印a=2的，但是不知道为啥失败了。
- 可能是这个版本不支持 .ONESHELL。

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





依赖展开 SECONDEXPENSION

独立多目标

多方法 合并依赖 添加依赖
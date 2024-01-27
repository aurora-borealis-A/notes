# man

manual 的缩写，可以用来找到帮助文档，”有问题，找男人“

```bash
man ls  # 得到ls的帮助文档
```

# ls

list 的缩写，列出文件结构

```bash
ls  # 列出当前目录中有哪些文件和子目录
ls -a  # 包含隐藏文件（以“.” 开头的文件和目录）
ls -l  # 所有文件的详细信息（包括权限信息等）
ll  # ls -l 的别名，和ls -l 等效，写起来更短
ls /etc  # 列出指定目录下的内容 这个例子中”/etc“ 是指定目录
```

# cd

change directory 的缩写，进入指定目录

```bash
cd src  # 进入当前目录下的src目录（相对路径）
cd /etc  # 进入指定目录 这个例子中”/etc“ 是指定目录（绝对路径）
```

# rm

remove 的缩写，删除文件或目录

```bash
rm file_name  # 删除文件
rm -r directory  # 删除文件夹
rmdir directory  # 删除文件夹
```

# mv

move 的缩写，移动文件或目录

```bash
mv 111.txt dest_directory  # 把源文件 移动到目标文件夹下
mv 111.txt dest_directory/111.txt  # 把源文件 移动到指定位置
mv 111.txt 222.txt  # 把源文件 移动到指定位置，同时源文件的名称也被更改为目标文件的名称，可以用来 重命名文件
mv directory dest_directory/111.txt  # 把指定文件夹移动到 目标文件夹下，同时名称发生改变，注意此时文件夹名字是111.txt，他还是文件夹而不是文件
```

# touch

创建文件（一个空文件），或者更改文件时间戳（文件修改时间）

```bash
touch 111.txt  # 如果当前目录下没有111.txt文件，则创建一个
```

# cat

concatenate的缩写，查看文件内容

```bash
cat 111.txt  # 在终端上打印文件111.txt的内容
```

合并文件内容

```bash
cat 111.txt 222.txt  # 把两个文件里的内容一起打印到控制台上
cat 111.txt 222.txt > 333.txt  # 把两个文件的内容合并后写入333.txt
```

创建文件并写入或追加文件，**Ctrl + D表示输入结束**

```bash
cat > 111.txt  # 会创建一个文件111.txt，然后在终端上提示输入文件内容，之后将该内容写入文件
cat > 111.txt  # 会创建一个文件111.txt，然后在终端上提示输入文件内容，之后将该内容追加进文件
```

# more

也用于查看文件，但适合查看内容较多的文件，查看时可以滚动屏幕和翻页

```bash
more 111.txt
```

# echo

打印字符串

```bash
echo 'haha'  # 则终端打印 “haha”，没什么用，一般跟 “>”或“>>”一起用，用于将字符串写入文件或追加进文件
echo 'haha' > 111.txt  # 将字符串“haha”写入文件111.txt
echo 'haha' >> 222.txt  # 将字符串“haha”追加进文件111.txt
```

打印变量值

```bash
name=haha  # 定义一个变量name
echo $name  # 打印变量name的值
```

# grep

global regular expression print的缩写，文本搜索工具

格式：`grep 匹配模式 文件名`

参数：

- -i 忽略大小写
- -E：正则匹配
- -n 打印行号
- -A<num\> 打印匹配行的 后num行
- -B<num\> 打印匹配行的 前num行
- -C<num\> 打印匹配行的 前后num行

例如：

```bash
grep 'haha' 111.txt  # 在文件111.txt中搜索字符串‘haha’，并把该行打印出来
grep -E '^1[0-9]$' 111.txt  # 在文件111.txt中搜索以‘1’为行开头，‘$’为行结尾的字符串
```

正则表达式详细见：[正则表达式](./正则表达式.md)

注意：

- 匹配模式默认意思就是要匹配的字符串
- 在正则匹配中，匹配模式就是正则表达式

# find

在指定目录查找文件

格式：`find 目录 变量名 变量值`

参数：

- -name：文件名称
- -type：文件类型
- -maxdepth：最大递归查找深度（深度为1时表示只在当前目录查找）

例如：

```bash
find . -name 'haha.txt'  # 在当前目录递归查找 名叫‘haha.txt’的文件
find /home/zw -type d '111'  # 在/home/zw目录查找 名叫‘111’的 文件夹
find /home/zw -type f  # 在/home/zw目录查找 所有文件（不包括文件夹）
find . -maxdepth 1 -name haha  # 只在当前目录查找（不递归查找） 名叫'haha'的文件
find . -name '*.txt'  # 在当前目录递归查找 以‘.txt’为后缀名的文件（使用了通配符）
```

注意：

- -maxdepth要作为第一个变量

# xargs

eXtended arguments的缩写，以为扩展的参数处理命令，可用于分割多条参数，通常与管道一起使用

格式：`命令1 | xargs 命令2`

参数：

- -d(delimiter)：指定定界符
- -n：指定每个命令的参数个数

```bash
# 把管道前命令的标准输出'111.txt 222.txt'作为xargs的标准输入
# xargs将标准输入以分隔符‘ ’（空格）分割成多份参数多次传入cat命令中
# 作用是打印111.txt和222.txt文件的内容
echo '111.txt 222.txt' | xargs cat  

# 111.txt文件的内容被作为xargs的标准输入
# xargs将标准输入以分隔符‘ ’（空格）分割成多份参数多次传入echo命令中
# 作用是打印111.txt文件的内容
xargs echo < 111.txt  
xargs -d ',' echo < 111.txt  # xargs将','作为分隔符（定界符），而不是默认的' '（空格）

# 管道前命令的作用：找到以'.txt'为后缀名的文件
# 这些文件名被xargs分割后，作为grep的标准输入
# grep命令分别查找这些文件中包含字符串‘main’的行，并打印出来，-n打印时显示行号
# 总的作用是 查找文本文件中的字符串main所在的行
find ./ -name '*,txt' | xargs grep -n 'main'
```

# sed

stream editor的缩写，流编辑器，一个文本处理命令，可以用于对文本进行替换、删除、插入、打印等操作



# read

# ssh 

secure shell的缩写，安全远程通信

```bash
ssh user_name@host_ip
ssh user_name@host_name
```

# pwd

print word directory的缩写，打印当前目录

# ps

打印当前进程

# fd

free disk的缩写，查看磁盘使用情况

# systemctl

system control的缩写，管理系统服务

# vim

visual interface improved的缩写，一个文本编辑工具。详细间[vim笔记](vim笔记)

# Git

版本控制工具。详细见[git笔记](git笔记)

# GCC

GCC 是 “GNU Complier Collection”的缩写（原来是"GNU C Complier"），一个GNU的开发工具包

GCC也是一条命令，用于c语言程序的编译

```bash
gcc -o output main.c  # 生成可执行文件
gcc -shared -o output.so main.c  # 生成共享库
```

**G++**用于编译**c++**程序

# CMake

编译配置工具，主要用于生成makefile。详细见[cmake笔记](cmake笔记)与[别人的cmake笔记](别人的cmake笔记)


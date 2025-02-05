# 基本命令

## man

manual 的缩写，可以用来找到帮助文档，”有问题，找男人“

```bash
man ls  # 得到ls的帮助文档
```

## ls

list 的缩写，列出文件结构

格式：`ls [选项] [文件或目录]`

选项：

- `-l`：查看详细信息
- `-a`：包含隐藏文件
- `-h`：以人性化的方式显示信息，一般都和`-l`联用

```bash
ls  # 列出当前目录中有哪些文件和子目录
ls -a  # 包含隐藏文件（以“.” 开头的文件和目录）
ls -l  # 所有文件（不包括隐藏文件）的详细信息（包括权限信息等）
ll -lh  # 列出的详细信息中，文件大小信息有了单位。
ll  # ls -la 的别名，和ls -la 等效，写起来更短
ls /etc  # 列出指定目录下的内容 这个例子中”/etc“ 是指定目录
ls *.txt  # 列出所有文本文件
```

## touch

创建文件（一个空文件），或者更改文件时间戳（文件修改时间）

```bash
touch 111.txt  # 如果当前目录下没有111.txt文件，则创建一个
```

## mkdir

make directory的缩写，创建一个目录

格式：`mkdir 目录名`

参数：

- `-p`：递归地创建目录

例如：

```bash
mkdir haha  # 创建一个名为"haha"的目录
mkdir -p haha/lala  # 创建目录 haha/lala
```

## cd

change directory 的缩写，进入指定目录（更换当前工作目录）

```bash
cd src  # 进入当前目录下的src目录（相对路径）
cd /etc  # 进入指定目录 这个例子中”/etc“ 是指定目录（绝对路径）
```

## rm

remove 的缩写，删除文件或目录

```bash
rm file_name  # 删除文件
rm -r directory  # 删除文件夹
rmdir directory  # 删除文件夹
```

## mv

move 的缩写，移动文件或目录

格式：`mv 源文件/目录 目标文件/目录`

选项：

- `-r`：递归地移动目录

例如：

```bash
mv 111.txt dest_directory  # 把源文件 移动到目标文件夹下
mv 111.txt dest_directory/111.txt  # 把源文件 移动到指定位置
mv 111.txt 222.txt  # 把源文件 移动到指定位置，同时源文件的名称也被更改为目标文件的名称，可以用来 重命名文件
mv directory dest_directory/111.txt  # 把指定文件夹移动到 目标文件夹下，同时名称发生改变，注意此时文件夹名字是111.txt，他还是文件夹而不是文件
```

## cp

copy 的缩写，复制文件或目录，用法与mv基本相同

格式：`cp 源文件/目录 目标文件/目录`

选项：

- `-r`：递归地复制目录
- `-i`：覆盖文件前提示

```bash
cp 111.txt dest_directory  # 把源文件 复制到目标文件夹下
cp 111.txt dest_directory/111.txt  # 把源文件 复制到指定位置
cp 111.txt 222.txt  # 把源文件 复制到指定位置，同时源文件的名称也被更改为目标文件的名称
```

## cat

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

## more

也用于查看文件，但适合查看内容较多的文件，查看时可以滚动屏幕和翻页

命令：

- `f`：向下翻页
- `space`：向下翻页
- `b`：向上翻页
- `enter`：向下滚动一行
- `!`：调用shell命令
- `v`：使用vim编辑

```bash
more 111.txt
ls -l | more
```

## echo

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

## tree

以树状图形式显示目录结构

格式：`tree [选项] [指定目录]`

选项：

- `-d`：树状图中只包含目录，不包括文件

例如：

```bash
tree  # 以树状图形式显示当前目录结构
tree ~  # 显示家目录的结构
tree -d ~ # 不包括文件
```

## grep

global regular expression print的缩写，文本搜索工具

格式：`grep 匹配模式 文件名`

选项：

- `-i`：忽略大小写
- `-r`：递归搜索
- `-E`：正则匹配
- `-n`： 打印行号
- `-A<num>`：打印匹配行的 后num行
- `-B<num>`：打印匹配行的 前num行
- `-C<num>`：打印匹配行的 前后num行

例如：

```bash
grep 'haha' 111.txt  # 在文件111.txt中搜索字符串‘haha’，并把该行打印出来
grep -E '^1[0-9]$' 111.txt  # 在文件111.txt中搜索以‘1’为行开头，‘$’为行结尾的字符串
```

正则表达式详细见：[正则表达式](./正则表达式.md)

注意：

- 匹配模式默认意思就是要匹配的字符串
- 在正则匹配中，匹配模式就是正则表达式

# 高级命令

## find

在指定目录查找文件

格式：`find 目录 变量名 变量值`

选项：

- `-name`：文件名称
- `-type`：文件类型
- `-maxdepth`：最大递归查找深度（深度为1时表示只在当前目录查找）

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

## ln

link的缩写，创建链接。

格式：`ln [选项] 被链接文件 链接文件名`

选项：

- `-s`：创建软链接

例如：

```bash
ln -s ~/haha/lala/heihei.txt ruan.txt  # 创建 /home/user/haha/lala/heihei.txt的软链接，链接文件名叫ruan.txt
ln ~/haha/lala/heihei.txt ruan.txt  # 创建 /home/user/haha/lala/heihei.txt的硬链接，链接文件名叫ruan.txt
```

注意：

- 创建软链接时，应当使用被链接文件的**绝对路径**，而不用相对路径。
- 使用相对路径的话，如果链接文件被移动位置，链接就失效了。

## tar

tape archive 的缩写，打包工具（不包括压缩）

格式：tar [选项]

选项：

- `-c`：创建一个档案文件（.tar）
- `-v`：显示打包的详细信息
- `-f`：指定打包文件的名称
- `-x`：解压档案文件
- `-C`：指定解包后的文件目录
- `-z`：打包时使用gzip压缩，解包时使用gzip解压缩
- `-j`：打包时使用bzip2压缩，解包时使用bzip2解压缩

注意：

- 大多数情况下打包就是选项组合：`-cvf`，解包就是`-xvf`
- 打包并没有压缩，如果要压缩，要加命令`-z`或`-j`
- `-z`表示使用gzip压缩，打包压缩后的文件为`xxx.tar.gz`
- `-j`表示使用bzip2压缩，打包压缩后的文件为`xxx.tar.bz2`

例如：

```bash
# 打包压缩
tar -cvf txt.tar *.txt  # 将所有以“.txt”结尾的文件打包成一个“txt.tar”的文件
tar -zcvf txt.tar *.txt  # 将所有以“.txt”结尾的文件打包成一个“txt.tar.gz”的文件 并压缩
tar -jcvf txt.tar *.txt  # 将所有以“.txt”结尾的文件打包成一个“txt.tar.bz2”的文件 并压缩

# 解压
tar -zxvf txt.tar txt.tar.gz  # 将压缩文件“txt.tar.gz”解压缩和解包 到当前目录
tar -jxvf txt.tar txt.tar.bz2 -C ~/haha # 将压缩文件“txt.tar.bz2”解压缩和解包 到 ~/haha目录
```

## ar



## shutdown

关机或重启

格式：`shutdown [选项] [参数]`

选项：

- `-r`：重启
- `-c`：取消接下来的重启/关机

例如：

```bash
shutdown -r  # 系统将在1分钟后重启
shutdown now  # 立刻关机
shutdown -r +10  # 系统10分钟后重启
shutdown 20:20  # 系统将在今天20:20关机
```

## xargs

eXtended arguments的缩写，以为扩展的参数处理命令，可用于分割多条参数，通常与管道一起使用

格式：`命令1 | xargs 命令2`

选项：

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

## sed

stream editor的缩写，流编辑器，一个文本处理命令，可以用于对文本进行替换、删除、插入、打印等操作。

详细见[sed笔记](sed笔记)。

## read

## pwd

print working directory的缩写，打印当前目录

## which

打印命令所在的路径

```bash
which ls  # 打印命令ls所在的路径：/usr/bin/ls
```

# 远程管理

运程管理ubuntu系统往往是通过`ssh`命令来实现，ssh是一种通信协议，使用时需要双方具备ssh服务器与客户端（这里的服务器和客户端都是指应用程序）：

- ssh服务器：被运程控制电脑需要下载ssh服务器并开启服务程序；
- ssh客户端：运程控制电脑的发起者需要安装ssh客户端，然后发起运程通信请求。

注意：

- ubuntu系统默认是自带了ssh客户端，但是没有安装ssh服务器。
- 安装ssh服务器：`sudo apt install openssh-server`
- 启动ssh服务：`sudo systemctl start ssh`

## ssh 

secure shell的缩写，安全远程通信

选项：

- `-p`：指定端口号，ssh服务端口号默认是22，可以不指定

例如：

```bash
ssh user_name@host_ip
ssh -p 22 user_name@host_ip
ssh user_name@host_name
```

给 `user_name@host_ip` **配置别名**：

- 在.ssh目录下，创建或修改config文件，向其中追加：

```txt
Host 随便一个好记的名字
  HostName 192.168.84.41  # ip地址或域名
  User username  # 用户名
```

## ssh-keygen

产生一对钥匙，公钥与私钥，可以用于免密登录。

```bash
ssh-keygen  # 一路回车后 在~/.ssh/ 目录下产生id_rsa(私钥), id_rsa.pub
```

## ssh-copy-id

复制本地公钥到指定电脑。

如果复制成功，会在对方家目录下的.ssh文件创建名为“authorized_keys”的文件（windows系统下在c:/user/user_name/.ssh），其内容为公钥内容。

```bash
ssh-copy-id user@host_ip  
```

## scp

secure copy的缩写，安全远程拷贝

用法与cp基本相同，不过scp用于不同电脑之间的文件拷贝

格式：scp [选项] 源文件 目标文件

选项:

- `-P`：指定端口号。
  - 注意这是大P，ssh的端口选项是小p

```bash
scp -r user@host:demo ~  # 把远程电脑的demo文件夹 拷贝到本地家目录下
```

## whoami

”我是谁“，打印当前的用户名

## who

打印当前所有在线的用户列表

## chmod

change mode的缩写，修改文件权限

格式：`chmod 权限 文件`

例如：

```bash
chmod +x hello.py  # 增加hello.py的可执行权限
chmod +rw 111.txt  # 增加111.txt的可读可写权限
chmod -x dir  # 去除文件夹dir的权限，即无法进入文件夹dir
chmod 766 hello.py  # hello.py的权限为 rwxrw-rw-
chmod 321 hello.py  # hello.py的权限为 -wx-w---x
```

## 组管理

### groupadd

添加组

格式：`groupadd 组名`

注意：

- 添加组需要有管理员权限，一般都要在`groupadd`前加`sudo`

例如：

```bash
sudo groupadd dev  # 添加一个名叫“dev”的组
```

所有组的信息在 /etc/group 文件中

```bash
cat /etc/group  # 查看所有组的信息
cat /etc/group | grep dev  # 查找 dev组 的信息
```

/etc/group**文件结构**：

4样信息，用3个`: `隔开。

```bash
组名:组密码(如果加密则显示"x"):组号(Uid):组用户[, 第二个组用户]
例如：
zw:x:1000:  # zw组的组号是1000，其中包含zw用户（zw组是zw用户的初始组，zw组中不显示用户zw）
dev:x:1001:zhangsan, lisi  # dev组的组号是1001，其中包含zhangsan、lisi用户
```

### groupdel

删除组

格式：`groupdel 组名`

注意：

- 删除组需要有管理员权限，一般都要在`groupdel`前加`sudo`

例如：

```bash
sudo groupdel dev  # 删除名叫“dev”的组
```

### chgrp

更改文件所属组

格式：`chgrp [选项] 组名 文件名`

选项：

- `-R`：递归地修改文件所属组

例如：

```bash
chgrp -R dev haha  # 令 “haha”文件夹及文件下内的所有文件 属于组 dev
```

## 用户管理

### useradd

添加用户

格式：`useradd [选项] 用户名`

选项：

- `-m`：创建家目录
- `-g`：指定所属组（主组）

注意：

- 创建用户时`-m`、`-g`一般都要加上。
- 如果不加`-m`，要自己建立新用户的家目录，太麻烦
  - 如果创建时忘记加`-m`，建议用户删了重新创建
- `-g`指定的是用户的主组
  - 如果不加`-g`，则会自动创建一个**跟用户同名的组**，并让该用户属于该组

例如：

```bash
useradd -m -g dev zhangsan  # 添加新用户张三，并自动创建张三的家目录，该用户属于dev组
```

查看所有用户信息：

```bash
cat /etc/passwd  # 查看所有用户信息
cat /etc/passwd | grep zw  # 查看用户zw的信息
```

/etc/passwd文件文件结构：

7样信息，用六个`: `隔开。

```txt
用户名:用户密码(如果加密则显示“x”):用户号(Uid):用户主组号(Gid):用户全称:用户家目录:登录shell
```

### passwd

设置用户密码

格式：`passwd [用户名]`

例如：

```bash
passwd  # 修改当前用户密码
passwd zhangsan  # 修改张三用户的密码
sudo passwd zhangsan  # 强制修改密码
```

### userdel

删除用户

格式：`userdel [选项] 用户名`

选项：

- `-r`：删除用户的同时删除对应家目录

例如：

```bash
userdel -r zhangsan  # 删除用户zhangsan，同时删除zhangsan用户的家目录
```

### usermod

更改用户属性

格式：usermod 选项 用户

选项：

- `-g`：更改主组
- `-G`：添加附加组

- `-s`：更改登录shell

例如：

```bash
usermod -g dev zhangsan  # 令zhangsan用户的主组为dev
usermod -G sudo zhangsan  # 令zhangsan用户的附加组为sudo，即拥有sudo（超级用户）的权限
usermod -s /bin/bash zhangsan  # 令zhangsan用户的登录shell为bash
```

### su

switch user的缩写，切换用户。

格式：`su [-] 用户名`

选项：

- `-`：切换用户后，当前目录也切换为对应用户的家目录。

- `exit`：在切换为别的用户后，exit退出登录，回到原先的用户。

### chown

change owner的缩写，更改文件所属用户。

格式：`chown [选项] 用户名 文件名`

选项：

- `-R`：递归地修改文件权限



# 系统管理

## lsb_release

lsb是linux standard base_release的缩写，查看当前系统版本

格式：`lsb_release -a`

## apt

ubuntu中安装软件可以使用`apt`（Advanced Package Tools“）命令，apt是一个软件包管理工具，类似于python中的`pip`，都可以通过`apt install 软件包`的格式安装软件，不过一般命令加还要加上`sudo`以提供足够的权限来安装软件，即：`sudo apt install 软件包`

格式：`apt 选项`

选项：

- `update`：访问软件源。
  - 并将软件源中的各软件（匹配当前系统版本的软件）的元数据同步（缓存）到本地。
  - 同时检查本地已安装软件是否有可更新的版本。

- `upgrade`：更新软件。
  - 往往跟在`update`命令后：`update`找到可以更新的软件，然后upgrade执行软件的更新。

- `install`：安装软件。
  - 常用的开发用的软件见`开发命令`章节

- `remove`：卸载软件。
- `list --installed`：列出已安装的软件

> 背景知识：
>
> - 软件包：软件包文件以.deb或.rpm为后缀，是一个打包文件，其中包含了：
>   - 二进制文件（可执行文件）：是软件包的核心，是可以直接运行的文件（通过`路径/文件名`运行），一般被安装在`/usr/bin`目录下；
>   - 库文件：包括静态库（.a）与动态库（.so）；
>   - 头文件；
>
>   - 辅助文件：
>     - 安装脚本；
>     - 说明文档等。
>
> - 软件源：一个存储软件的服务器（即一台电脑），通常通过网络访问。
>
>   - 软件源中保存了软件的元数据（版本号、依赖关系等）和软件的实体（各种可执行文件与库文件）。
>
> 

注意：

- 安装、卸载软件是系统管理级别的操作，需要使用管理员权限`sudo`
- 使用apt安装的软件大部分都保存在`/usr/bin`，`/usr/lib`、`/usr/include`等目录。
  - 而一些用户自己部署的软件（即不使用apt安装的软件），比如通过cmake部署到本地的软件，则都保存在`/usr/local/bin`、`/usr/local/lib`、`/usr/local/include`下。
  - 另外像ROS这样的软件，也是可以不使用apt安装的大软件，则一般安装在/opt目录下，
    - `/opt`目录下的软件往往有自己独立的目录，比如`/opt/ROS`


例如：

```bash
sudo apt install sl  # 安装一个名为“sl”的软件（一个小火车软件）
sudo apt install htop  # 安装一个名为“htop”的软件（一个升级版的top）
sudo apt remove sl  # 卸载名叫“sl”的软件
```

## date

查看当前日期

格式：`date`

效果预览：

```bash
date
# 2024年 01月 31日 星期三 20:23:22 CST
```

## cal

calendar的缩写，查看日历

格式：`cal [选项]`

选项：

- `-y`：显示当年的日历

效果预览：

```bash
cal
#       一月 2024
# 日 一 二 三 四 五 六
#     1  2  3  4  5  6
#  7  8  9 10 11 12 13
# 14 15 16 17 18 19 20
# 21 22 23 24 25 26 27
# 28 29 30 31
```

## df

disk free的缩写，查看磁盘使用情况

格式：`df`

选项：

- `-h`：以人可读的方式显示

## du

disk usage的缩写，查看文件的磁盘使用情况

格式：`du [选项] [文件]`

选项：

- `-h`：以人可读的方式显示

例如：

```bash
du -h .  # 查看当前目录的占用大小
du -h 111.txt  # 查看111.txt文件的占用大小
```

## ps

process status的缩写，打印当前进程。

格式：`ps [选项]`

选项：

- a：打印的进程信息包含其他用户的进程（默认只包含自己的进程）。
- u：打印进程的详细信息（包括进程id、cpu使用率、内存使用率、哪个用户的创建的等信息）。
- x：打印所有的进程信息，包括系统进程（内容很多很多，不常用）。

注意：

- ps默认打印的进程都是那些在**bash中开启的进程**。
- ps的选项没有`-`，例如：`ps a` 而不是 `ps -a`

例如：

```bash
ps a  # 打印所有用户的进程信息
ps au  # 打印所有用户的详细进程信息
```

## top

table of process的缩写，动态地显示进程运行状况，类似于windows的任务管理器。

注意：

- top命令会一直运行，按`q`退出

## kill

关闭进程

格式：`kill 进程号(pid)`

选项：

- `-9`：强制关闭

## systemctl

system control的缩写，管理系统服务

格式：`systemctl 操作 服务名`

操作：

- `start`：启动服务。
- `stop`：关闭服务。
- `restart`：重启服务。
- `status`：查看服务状态。
- `enable`：开机自动启动指定服务。
- `disable`：开启不自动启动指定服务。
- `list-units`：列出当前系统正在运行或已经加载的服务。
- `list-unit-files`：列出当前系统中已安装的unit文件及其状态。
- `daemon-reload`：重新加载系统中systemd的配置文件。



# 开发命令

## 安装开发工具

使用`apt`工具安装软件包：`sudo apt install 软件包名`

软件包：

- `build-essential`：包含gcc、g++、make、c标准库等**c/c++开发**的主要部件。
- `vim`：用于在终端中快速编辑文本，提供丰富的功能与插件，但是上手难度较大。

## vim

visual interface improved的缩写，一个文本编辑工具。详细见[vim笔记](vim笔记)

## Git

版本控制工具。详细见[git笔记](git笔记)

## GCC

GCC 是 “GNU Complier Collection”的缩写（原来是"GNU C Complier"），一个GNU的开发工具包

GCC也是一条命令，用于c语言程序的编译，详细见[gcc笔记](./gcc笔记)

```bash
gcc -o output main.c  # 生成可执行文件
gcc -shared -o main.c output.so  # 生成共享库
```

**G++**用于编译**c++**程序，用法与gcc差不多。

## Make

构建工具，根据makefile的规则，来执行各项编译操作。详细见 [make笔记](make笔记)与[别人的make笔记](别人的make笔记)

## CMake

编译配置工具，主要用于生成makefile。详细见[cmake笔记](./cmake笔记)与[别人的cmake笔记](./别人的cmake笔记)


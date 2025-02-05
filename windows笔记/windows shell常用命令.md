所有命令后添加`/?` 查看命令用法与帮助。

# 网络配置

## ipcnofig

查看网络配置，主要用来查看ip地址（包括ipv6地址、mac地址）。

选项：

- `/all`：打印详细信息。

例如：

```powershell
ipconfig  # 查看网卡信息
ipconfig /all  # 查看网卡详细信息，包括网卡mac地址
```

## ping

测试与目标主机之间网络情况（ICMP协议）。

选项：

- `/i`：指定最大生存时间（TTL）

```powershell
ping 192.168.31.42  # 测试当前计算机与192.168.31.42网络连接是否正常
ping 127.0.0.1  # 测试自己计算机网卡是否正常工作，（127.0.0.1是ipv4的本地环回地址）
ping ::1  # 测试自己计算机网卡是否正常工作，（::1是ipv6的本地环回地址）
ping www.baidu.com -i 2  # 设置ping的消息包最大生存之间为2，即最多通过两个路由器
```

## nslookup

使用dns服务器解析域名，可以用来dns服务器是否正常工作，或就用来解析域名。

- `exit`：退出

例如：

```powershell
C:\Users\86152>nslookup
默认服务器:  xiaoqiang  # 为我解析域名的服务器是xiaoqiang
Address:  192.168.31.1  # 该服务器ip地址是192.168.3.1

> www.baidu.com
服务器:  xiaoqiang
Address:  192.168.31.1

非权威应答:
名称:    www.a.shifen.com
Addresses:  240e:e9:6002:15a:0:ff:b05c:1278  # 百度的ipv6公网地址(好像错的)
          240e:e9:6002:15c:0:ff:b015:146f
          180.101.50.188  # 百度的ipv4公网地址
          180.101.50.242
Aliases:  www.baidu.com  # 别名百度

> exit  # 退出
```

## tracert

跟踪本地计算机与目标计算机的网络路径

例如：

```powershell
C:\Users\86152>tracert www.baidu.com

通过最多 30 个跃点跟踪
到 www.a.shifen.com [180.101.50.242] 的路由:

  1     6 ms     3 ms     4 ms  xiaoqiang [192.168.31.1]
  2    60 ms    11 ms     6 ms  192.168.1.1 [192.168.1.1]
  3    17 ms    13 ms    10 ms  100.66.8.1
  4     *        *        *     请求超时。
  5     9 ms    12 ms     9 ms  61.175.94.245
  6     *        *       12 ms  61.175.73.185
  7     *        *        *     请求超时。
  8    25 ms     *       29 ms  58.213.94.98
  9    62 ms     *       23 ms  58.213.94.210
 10    41 ms    22 ms    26 ms  58.213.96.66
 11     *        *        *     请求超时。
 12     *        *        *     请求超时。
 13     *        *        *     请求超时。
 14     *        *        *     请求超时。
 15    25 ms    20 ms    33 ms  180.101.50.242

跟踪完成。
```

## netstat

查看网络端口状态，包括源地址、源端口、目标地址、目标端口等信息。

选项：

- `/n`：以数字形式显示（默认host是主机名而不是ip地址）。

## route print

打印电脑中缓存的”路由表“





# 用户管理

# net 

选项：

- /add
- /del

- user
  - <username\> <password\>
- localgroup
  - <groupname\>

```powershell
net user  # 列出所有用户
net user administrator  # 查看用户administrator的详细信息
net user laowang /add  # 添加新用户，该用户名为laowang，新添加用户所在组为 users（普通用户）
net uesr laowang /del  # 删除用户laowang
net user laowang laowang  # 设置用户laowang的密码为“laowang”

net localgroup  # 列出所有组
net localgroup  administrators  # 查看administrators组包含的用户
net localgroup administrators laowang /add  # 将用户laowang添加到administrators组中
net localgroup administraotr laowang /del  # 将用户laowang从administrators组中去除（不删除用户）

```


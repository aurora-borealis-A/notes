工程目录下：

```cmake
# 工程名称
PROJECT(hello)

# 添加工程子目录
ADD_SUBDIRECTORY(src bin)
```

子目录下：

```cmake
# 添加（生成）可执行文件
ADD_EXECUTABLE(hello main.cpp)
# 添加包含头文件路径
INCLUDE_LIBRARIES(/usr/include/haha)  # /usr/include/haha是存储haha.h的目录
# 链接库文件
## TARGET_LINK_LIBRARIES(hello libhaha.so)
TARGET_LINK_LIBRARIES(hello haha)  # 可以只输入库的名字，而不用输入库文件的名称
TARGET_LINK_LIBRARIES(hello libhaha.a)  # 可链接静态库

# 添加（生成）静态库
ADD_LIBRARY(hello STATIC main.cpp)

# 添加（生成）动态库
ADD_LIBRARY(hello SHARED main.cpp)
```

安装（部署）：

```cmake
# 安装文件
NSTALL(FILES main.h DESTINATION include)

# 安装脚本等
INSTALL(PROGRAMS run_main.sh DESTINATION bin)

# 安装可执行程序（库文件）
# 静态库是LIBRARY 动态库是ARCHIVE
INSTALL(TARGETS  hello hello_static LIBRARY DESTINATION lib ARCHIVE DESTINATION lib)
```

其他：

```cmake
SET_TARGET_PROPERTIES(hello_static PROPERTIES OUTPUT_NAME "hello")  # 输出的目标文件是‘hello’
SET_TARGET_PROPERTIES(hello_static PEOPERTIES CLEAN_DIRECT_OUTPUT 1)  # 让hello_static的文件不被清理（同名时不被清理）
```


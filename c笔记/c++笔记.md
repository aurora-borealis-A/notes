# 一、函数新特性

## 1. 函数重载

​		当两个函数名称相同，但是参数不同时，c++允许两个同名的函数同时存在。编译器会根据函数调用时传入的实参来匹配合适的函数版本。

**作用**：

- 这可以实现一个函数的多个版本同时存在，
- 函数的多个版本可以分别处理不同的参数类型，最终完成相同（或相似）的功能。

**重载规则**：

- **参数数量**不同；
- **参数类型**不同；
- **参数顺序**不同。

**注意**：

- 函数**返回类型不同**时不能发生重载，而是会报错。

**示例**：

```c++
void print(int a){
	std::cout << a << std::endl;
}

void print(int a, b){
	std::cout << a << b << std::endl;
}

int main(){
    int a=1, b=2;
    print(a);  // 调用第一个函数
    print(b);  // 调用第一个函数
    print(a, b);  // 调用第二个函数
    return 0;
}
```



## 2.函数默认参数

​		在定义函数时，可以为函数的参数提供默认值，这样在调用函数时，可以不指定这些参数，函数会自动传入默认值。

**示例**：

```c++
void print(int a=1){
    std::cout << a << std::endl;
}

int main(){
	print();   // 打印数字1
    print(2);  // 打印数字2
    return 0;
}
```

> 这其实也相当是函数重载的功能：同时存在着 void print() 和 void print(a) 的函数。

**注意**：

- 申明中和具体实现的参数列表中，**只能出现一次默认参数**。
  - 当分文件编写时，往往要写函数签名两次，头文件一次，具体实现一次，
  - 如果在头文件申明函数时指定了默认参数，则在.cpp文件中的函数具体实现中，就不能指定默认参数，否则会报错。
  - 如果在.cpp文件实现函数时指定了默认参数，则在头文件中申明函数时，就不能指定默认参数，否则会报错。
  - **一般会在申明函数时指定默认参数**，而不是在实现时指定。

> **背景知识**
>
> 函数签名：函数的申明部分，包含了函数的基本信息
>
> - 返回值类型；
> - 函数名；
> - 参数列表。

# 二、面向对象编程

## 1. 封装

### 1.1 类与对象

#### 1.1.1 类的定义

​		在现实中，一个物体（具有某种功能的，不论生物还是非生物）往往是由多个部分组成的，例如：汽车就是由轮子、外壳、发动机等部件组成，人由头部、手脚等器官组成。在编程中，将多个相关的变量看作一个整体（或者说这个整体由多个变量组成）也是借用了这种思想。

语法：

```c++
class 类名[: 继承方式 父类]{
访问属性:
    // 公有成员
};
```

#### 1.1.2 类的成员

类中的成员有两类：成员变量与成员函数。

- 成员变量：
- 成员函数：

#### 成员访问属性

public：外部可以访问

private：仅在内部可以访问，子类也不可访问。

protected：仅在内部可以访问，子类可以访问。

#### 构造函数

#### 析构函数

#### 新建对象

#### 重载运算符

### 1.2 结构体

## 2. 继承

定义

虚函数

纯虚函数

## 3. 多态

## 迭代器

# 三、模板

## 1. 函数模板

函数模板用于定义一个适用不同变量类型的函数。

**格式**：

- **定义格式**：

  ```c++
  template <typename T [, typename T2]>
  返回类型 函数名称(参数列表){
      // 函数体
  }
  ```

  - 在函数体前加`template`关键字可以申明该条语句后紧跟着定义的函数是一个函数模板。
  - 在`template <typename T>`中：
    - 通过`typename`关键字，定义了一个占位符`T`，用于表示一种任意的数据类型，在实际运行时会被替换为实际存在的数据类型；
    - 可以定义多个数据类型，只需要在`<>`中追加定义，如：`template <typename T1, typename T2>。`
    - 可以指定数据类型的默认值，如：`template <typename T=int>`。
  - 模板函数具体实现代码中的数据类型都使用`typename`定义的占位符来替代。
    - 例如：`T num;`，在调用函数时会被替代成`int num`或`char num`;

- **申明格式**：申明格式类型于普通函数

  ```c++
  template <typename T [, typename T2]>
  返回类型 函数名称(参数列表);
  ```

- **调用格式**：

  - 普通调用：`函数名称(参数列表)`，跟一般的函数调用没什么区别；
    - 此时编译器会自动推导类型。
  - 指定类型：`函数名称<类型 [,第二个类型]>(参数列表)`。

## 2. 类模板

类模板与函数模板定义类似。

**格式**：

```c++
template <typename T [,typename T2]>
class 类名称{
    // 类定义  
};
```





# 四、命名空间

## 1. 命名空间

## 2. using用法



# 五、常用库

## 1. stl

### 1.0 简介

stl是"Standard Template Library"，标准模板库，其中定义了很多数据类型的模板（称为”容器“）。

常用的有容器有：

- **顺序容器**：这些容器按顺序存储元素，元素在窗口中的顺序是有序的。
  - `vector`：线性表，类似数组或者python中的列表；
  - `deque`：双端队列，支持在两端进行插入和删除的队列；
  - `list`：双向链表，支持在两端和中间进行插入和删除；
  - `array`：固定大小的数组（c++11中新增）。
    - 相比于vecotr，array在编译时大小就已经固定。
- **容器适配器**：是对其他窗口的封装，提供特定的操作接口。
  - `stack`：栈；
  - `queue`：队列。
- **关联容器**：这些容器根据数据的键值对存储元素，并自动保持元素的有序性。
  - `set`：存储唯一元素，自动排序，不允许重复；
  - `muliset`：类似set，但允许元素重复；
  - `map`：存储键值对，键唯一，自动排序；
  - `multimap`：类似map，但允许重复键。
- **无序容器**：这些容器内部的元素是无序的，基于哈希表实现
  - `unordered_set`：存储唯一元素，不允许重复，但没有顺序；
  - `unordered_muliset`：类似set，但允许元素重复，一样没有顺序；
  - `unorderd_map`：存储键值对，键唯一，但没有顺序；
  - `unordered_multimap`：类似map，但允许重复键，一样没有顺序。

### 1.1 vector

vector容器支持随机访问，并且自动扩展大小。

---

**创建**：

- `std::vector<int> v;`：创建一个空的vector（其中没有元素）；
- `std::vector<int> v(2);`：创建一个长度为2的vector（即其中有两个int类型的元素），所有元素数值为0；
- `std::vector<int> v(3, 1);`：他去一个长度为3的vector，所有元素数值为1；
- `std::vector<int> v={1, 2, 3, 4};`：按照`{}`内指定的元素创建vector。

---

**添加元素**：

- 

### 1.2 stack

### 1.3 queue

使用queue容器需要包含头文件：`#include<queue>`

**定义容器**：

- **格式**：`queue<元素类型> 容器名（变量名）`
- **例如**：
  - `std::queue<int> array`
  - 一个由整型变量组成的队列，队列名为array。

**常用的操作**：

- `push(elem)`：入列，向队尾插入一个元素；
  - `elem`是元素。
- `pop()`：出队，移除队头元素；
- `front()`：返回队头元素的引用；
- `back()`：返回队尾元素的引用；
- `empty()`：检查队列是否为空；
- `size()`：返回队列元素的个数。



## 2. yaml-cpp

yaml-cpp是c++中读取和操作yaml文件的常用库

### 2.1 安装与导入库

在ubuntu下，**安装yaml-cpp库**，可以使用命令：

- `sudo apt install libyaml-cpp-dev`
- 或者：`sudo apt install libyaml-cpp-dev`

---

安装完成后，可以**一般安装目录**为：

- 头文件：`/usr/include/yaml-cpp/yaml.h`
- 库文件：
  - 静态库：`/usr/lib/x86_64-linux-gnu/libyaml-cpp.a`；
  - 动态库（共享库）：`/usr/lib/x86_64-linux-gnu/libyaml-cpp.so`；

### 2.2  编译选项

**用g++编译**（与链接时）：

- 默认g++对头文件和库文件的搜索路径就包括`/usr/include`与/usr/lib。
  - 因此使用g++编译时，不需要对搜索路径特殊指明。
  - 即，**不需要**`：-I/usr/include`，`-L/usr/lib`
- 但是需要指明使用了这个库：
  - 即，**需要**：`-lyaml-cpp`
- 例如：`g++ main.cpp -o main -lyaml-cpp`
  - **注意**：`-lyaml-cpp`一定要写在`-o main`后面，不让编译器（实际上是链接器）就是说找不定义。

### 2.3 **源程序中使用**

**定义的类型**：

- `YAML::Node`：Node类型是yaml-cpp库操作yaml文件的基本类型。
  - **索引**：`YAML::Node node["索引"]`
  - **遍历**：`for(auto i:node)`
    - yaml-cpp的Node提供了begin()和end()函数，因此可以直接使用简化的for循环写法。
  - **方法**：
    - `as<类型>`：将node中的值转换为需要的类型，可以是`int`，或者`std::string`

**提供的函数**：

- `YAML::LoadFile("文件名.yaml")`：从yaml文件中创建Node。
- `YAML::Load("字符串")`：从字符串中创建Node。

---

### 2.4 示例

有一yaml文件`people.yaml`：

```yaml
name: xiaoming
height: 1.75
hobbies:
  - basketball
  - music
friend:
  - {name: xiaogang, height: 1.6}
  - {name: xiaoqiang, height: 1.9}
```

在`main.cpp`中：

```c++
#include <iostream>
#include <yaml-cpp/yaml.h>  // 导入头文件

void main(){
    YAML::Node people = YAML::LoadFile("people.yaml");  // 从people.yaml文件中创建Node
    std::string name = people["name"].as<std::string>();  // 通过[]索引到键"name"对应的值，再通过as函数，转换为需要的类型

    // 使用迭代器遍历hobbies项
    YAML::Node hobbies = People["hobbies"];  // yaml文件中的每项都是一个Node   
    for(auto it=hobbies.begin(); it!=hobbies.end(); it++){
        std::cout << (*it).as<std::string>() << std::endl;  // 使用解引用运算符获得hobbies每项的值，并使用as函数转换类型
    }
    
    // 使用迭代器的简化写法
    YAML Node friends = people["friends"];
    for(auto friend:friends){
        std::cout << friend["name"].as<std::string>() << friend["height"].as<int>() << std::endl;
    }
     
}
```

编译并执行：

```bash
g++ main.cpp -o main -lyaml-cpp  # 编译
./main  # 执行
```


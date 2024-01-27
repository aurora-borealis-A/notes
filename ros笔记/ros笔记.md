# 编译ros

在工作空间目录下执行下面语句：

```
catkin_make
```



# 创建package

在 工作空间/src 目录下执行下面语句：

```
catkin_create_pkg package_name dependencies
```

其中：

- package_name 是创建的package的名称
- dependencies 是package的依赖项 常用的是 `rospy`   `roscpp`    `std_msgs`



# 话题（topic）

## 话题发布（publish）

1. 初始化ros节点

    ```c++
    ros::init(argc, argv, "node_name");  // node_name 为自定义节点名称
    ```

2. 定义ros 句柄

    ```c++
    ros::NodeHandler nh;  // nh为自定义的变量名
    ```

3. 创建发布者对象

    ```c++
    ros::Publisher pub = nh.advertise<std_msgs::String>("topic_name", 10);  // 10 为序列长度（queue_size）
    ```

4. 创建消息对象

    ```c++
    std_mgs::String msgs;  // 定义消息对象
    msg.data = "hello world";  // 指定消息内容
    ```

5. 发送消息

   ```c++
   pub.publish(msg);
   ```


## 例子

```c++
#include <ros/ros.h> 
#include <std_msgs/String.h>

int main(int argc, char *argv[])
{
    ros::init(argc, argv, "chao_node");
    ros::NodeHandle nh;
    ros::Publisher pub = nh.advertise<std_msgs::String>("kai_hei_qun", 10);

    ros::Rate rater(10);  // 频率10hz，即：每秒10次
    while(ros::ok())
    {
        printf("开始刷屏");
        std_msgs::String msg;
        msg.data = "hello";
        pub.publish(msg);
        rater.sleep();
    }
    return 0;
}
```

## 话题订阅（subsribe）

1. 初始化ros节点

   ```c++
   ros::init(argc, argv, "node_name");  // node_name 为自定义节点名称
   ```

2. 定义ros 句柄

   ```c++
   ros::NodeHandler nh;  // nh为自定义的变量名
   ```

3. 定义处理消息的回调函数（以处理的消息为`std_msgs/String`为例）

   ```c++
   void msg_handler(std_msgs::String msg)
   {
       received = msg.data;  // 读取 msg 消息中的 data（实际上msg中只有data）
       ROS_INFO("接收到 %s", received);
   }
   ```

4. 创建订阅者对象

   ```c++
   ros::Subscriber sub = nh.subscribe<std_msgs::String>("topic_name", 10, msg_handler);  // 10 为序列长度（queue_size）
   ```

5. 处理回调函数

   ```c++
   ros::spin();  // 循环处理回调函数
   // 或者
   ros::spinOnce(); // 处理一次回调函数
   ```

## rostopic

### 常用指令

- ```
  rostopic list
  ```

- ```
  rostopic echo 话题名称
  ```

- ```
  rostopic hz 话题名称
  ```

### 更多指令与用法

在终端执行下面语句

```
rostopic
```

返回rostopic的命令与解释

`-h`得到更详细解释



# launch 文件

## xml格式

```xml
<大盒子>
    <!-- 这是注释格式 -->
	<小盒子 颜色=“黄色” 形状="方形" />
</大盒子>
```

## lauch 文件

### 运行节点

```xml
<node pkg="pkg_name" type="node_name" name="custom_name" />
```

### 运行节点时传递参数

```xml
<!-- args 可以用来传参 -->
<node pkg="rviz" type="rviz" name="rviz" args="-d /xxx.rviz">
```

### 运行其他launch文件

```xml
<!-- include 可以用来运行其他launch文件 -->
<!-- $(find pkg_name) 用来得到 pkg_name 软件包的目录 -->
<include file="$(find pkg_name)/launch/xxx.launch"/>
```

### 设置参数

```xml
<param name="param_name" value="param_value"/>
```

### 例子

```xml
<launch>
    
    <node pkg="pkg_name" type="node_name" name="custom_name" />
    
    <!-- output 指定输出在终端屏幕上 -->
    <node pkg="pkg2_name" type="node2_name" name="custom_name2" output="scree"/>
                                                                               
    <!-- launch-prefix="gnome-terminal -e" 表示这个节点新建一个终端窗口运行 -->
    <node pkg="pkg3_name" type="node3_name" name="custom_name3" launch-prefix="gnome-terminal -e"/>  
    
    <!-- args 可以用来传参 -->
    <node pkg="rviz" type="rviz" name="rviz" args="-d /xxx.rviz">
    
    <!-- include 可以用来运行其他launch文件 -->
    <!-- $(find pkg_name) 用来得到 pkg_name 软件包的目录 -->
    <include file="$(find pkg_name)/launch/xxx.launch"/>
                                                                              
</launch>
```



# 机器人运动

## 运动分解

- **平移运动 ** 单位：***m/s***
- **旋转运动 ** 单位：***rad/s***

## 相关包

ros标准的运动控制消息包：**geometry_msgs**

### 消息格式

geometry_msgs/twist：

```python
geometry_msgs/vector3 linear
geometry_msgs/vector3 angular
```

geometry_msgs/vector3

```python
float32 x
float32 y
float32 z
```



# 雷达

## 话题

`/scan`

## 消息格式

**sensor_msgs/LaserScan：**

```python
# Single scan from a planar laser range-finder
#
# If you have another ranging device with different behavior (e.g. a sonar
# array), please find or create a different message, since applications
# will make fairly laser-specific assumptions about this data

std_msgs/Header header # timestamp in the header is the acquisition time of
                             # the first ray in the scan.
                             #
                             # in frame frame_id, angles are measured around
                             # the positive Z axis (counterclockwise, if Z is up)
                             # with zero angle being forward along the x axis

float32 angle_min            # start angle of the scan [rad]
float32 angle_max            # end angle of the scan [rad]
float32 angle_increment      # angular distance between measurements [rad]

float32 time_increment       # time between measurements [seconds] - if your scanner
                             # is moving, this will be used in interpolating position
                             # of 3d points
float32 scan_time            # time between scans [seconds]

float32 range_min            # minimum range value [m]
float32 range_max            # maximum range value [m]

float32[] ranges             # range data [m]
                             # (Note: values < range_min or > range_max should be discarded)
float32[] intensities        # intensity data [device-specific units].  If your
                             # device does not provide intensities, please leave
                             # the array empty.
```



# IMU

## 话题

- imu/data_raw（sensor_msgs/Imu）：裸数据（包括加速度计输出的角加速度 和 陀螺仪输出的角速度）
- **imu/data**（sensor_msgs/Imu）：裸数据 加上 四元数，这个比较常用。（四元数从裸数据中计算得出的，用来描述欧拉角）
- imu/mag（sensor_msgs/MegnaticField）：磁强计输出的磁强数据

## 数据

sensor_msgs/Imu：

```python
# This is a message to hold data from an IMU (Inertial Measurement Unit)
#
# Accelerations should be in m/s^2 (not in g's), and rotational velocity should be in rad/sec
#
# If the covariance of the measurement is known, it should be filled in (if all you know is the
# variance of each measurement, e.g. from the datasheet, just put those along the diagonal)
# A covariance matrix of all zeros will be interpreted as "covariance unknown", and to use the
# data a covariance will have to be assumed or gotten from some other source
#
# If you have no estimate for one of the data elements (e.g. your IMU doesn't produce an
# orientation estimate), please set element 0 of the associated covariance matrix to -1
# If you are interpreting this message, please check for a value of -1 in the first element of each
# covariance matrix, and disregard the associated estimate.

std_msgs/Header header

geometry_msgs/Quaternion orientation
float64[9] orientation_covariance # Row major about x, y, z axes

geometry_msgs/Vector3 angular_velocity
float64[9] angular_velocity_covariance # Row major about x, y, z axes

geometry_msgs/Vector3 linear_acceleration
float64[9] linear_acceleration_covariance # Row major x, y z
```

## 四元数转为欧拉角

首先导入tf包，借用tf包来转换四元数为欧拉角

```c++
#include<tf::tf.h>
```

用Imu消息构建tf的四元数，得到一个`tf::Quaternion`类型的数据

```c++
tf::Quaternion quaternion(
imu_msg.Orientation.x,
imu_msg.Orientation.y,
imu_msg.Orientation.z,
imu_msg.Orientation.w
);
```

用tf::Matrix3x3来转换quaternion，然后用getRPY方法得到 roll, pitch, yaw

```c++
double roll, pitch, yaw;
tf::Matrix3x3(quaternion).getRPY(roll, pitch, yaw);
```



# ROS消息包

## std_msgs

<img src=".\图片\std_msgs.png" alt="std_msgs" style="zoom: 50%;" />

## common_msgs

- action_msgs    行为消息包
- diagnostic_msgs    诊断消息包
- **geometry_msgs**    几何消息包
- nav_msgs    导航消息包
- **sensor_msgs**    传感器消息包
- shape_msgs    形状消息包
- stereo_msgs    双目视觉消息包
- trajectory_msgs    运动轨迹消息包
- visualization_msgs    图形显示消息包

### geometry_msgs 

<img src=".\图片\geometry_msgs.png" alt="geometry_msgs" style="zoom:60%;" />

### sensor_msgs

<img src="./图片\sensor_msgs.png" alt="sensor_msgs" style="zoom:60%;" />



# 自定义消息类型

## 相关ros指令

- ```
  rosmsg show 消息类型
  ```

- ```
  rosmsg list
  ```

## 步骤

1. 创建消息包（ros包），需要**依赖**：`std_msgs`、`message_generation`、`message_runtime`。（std_msgs是自定义消息包中需要的数据类型）

2. 创建msg目录，并在其下创建 **MyMsg.msg** 文件，并在msg文件中定义消息格式

3. cmkelist.txt 中

   ```cmake
   add_message_file(
   	FILES
   	MyMsg.msg
   )
   
   generate_message(
   	DEPENDENCIES
   	std_msgs
   )
   
   catkin_package(
   	CATKIN_DEPENDS std_msgs message_generation message_runtime
   )
   ```

4. package.xml中添加：

   ```
   <bulid_depend>message_generation</bulid_depend>
   <bulid_depend>message_runtime</bulid_depend>
   
   <exec_depend>message_generation</exec_depend>
   <exec_depend>message_runtime</exec_depend>
   ```



# 栅格地图

## 话题

`/map`

## 消息格式

nav_msgs/OccupancyGrid.msg：

```python
# This represents a 2-D grid map, in which each cell represents the probability of
# occupancy.

Header header 

# MetaData for the map
MapMetaData info

# The map data, in row-major order, starting with (0,0).  Occupancy
# probabilities are in the range [0,100].  Unknown is -1.
int8[] data
```

nav_msgs/MapMetaData.msg：

```python
# This hold basic information about the characterists of the OccupancyGrid

# The time at which the map was loaded
time map_load_time
# The map resolution [m/cell]
float32 resolution
# Map width [cells]
uint32 width
# Map height [cells]
uint32 height
# The origin of the map [m, m, rad].  This is the real-world pose of the
# cell (0,0) in the map.
geometry_msgs/Pose origin
```

## 相关包

**`hector_mapping`**：用于建图与定位，即（slam）（simultaneous location and mapping）

- 订阅话题：/scan
- 发布话题：/map

从`/scan`中获得雷达信息，得到栅格地图到并把栅格地图发布到`/map`



# TF

## 相关包

rqt_tf_tree 以树状图形式显示tf关系

```
rosrun rqt_tf_tree rqt_tf_tree
```

## 话题

`/tf`

## 消息格式

tf2_msgs/TFMessage.msg：

```
geometry_msgs/TransformStamped[] transforms
```

geometry_msgs/TransformStamped.msg：

```python
# This expresses a transform from coordinate frame header.frame_id
# to the coordinate frame child_frame_id
#
# This message is mostly used by the 
# <a href="http://wiki.ros.org/tf">tf</a> package. 
# See its documentation for more information.

Header header
string child_frame_id # the frame id of the child frame
Transform transform
```

geometry_msgs/Transform.msg：

```python
# This represents the transform between two coordinate frames in free space.

Vector3 translation
Quaternion rotation
```



# Gmapping

## 话题

- 订阅话题：`/scan`， `/tf`（laserscan_to_base_link、base_link_to_odmetry）
- 发布话题：`/map`，`/tf`（odmetry_to_map）

## slam_gmapping

用于slam建图与定位

```bash
rosrun gmapping slam_gammping
```



# map_server

用于保存与发布地图

## 地图文件

- my_map.png
- mymap.yaml：

```yaml
image: my_map.png
resolution: 0.1
origin: [0.0, 0.0, 0.0]
occupied_thresh: 0.65
free_thresh: 0.196
negate: 0
```

## 节点

### map_saver

- 用于保存地图，生成两个地图文件 my_map.png、my_map.yaml
- 订阅话题：`/map`（nav_msgs/OccupancyGrid

```bash
rosrun map_server map_saver -f my_map  # 将/map话题的中的栅格地图保存到磁盘上，my_map为自定义路径与文件名
```

### map_server

- 用于发布地图

- 发布话题：`/map`（nav_msgs/OccupancyGrid）

```bash
rosrun map_server map_server my_map.yaml  # 从磁盘上读取my_map.yaml文件，并发布栅格地图到/map话题
```


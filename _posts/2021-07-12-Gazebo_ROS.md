---
layout:     post
title:      Gazebo/ROS
subtitle:    
date:       2021-07-12
author:     ChenWH
header-img: img/the-first.png
catalog:   true
tags:

    - Robotics
---

# 配置安装

[Ubuntu18.04安装PX4并与ROS联合实验_boyhoodme的博客-CSDN博客_ubuntu18.04安装px4](https://blog.csdn.net/boyhoodme/article/details/96611182)

## ROS安装

http://wiki.ros.org/Installation/Ubuntu  注意版本号，完全安装即可，包含Gazebo

## Gazebo与ROS连接

https://www.geek-share.com/detail/2807627991.html

```shell
# 安装ROS-Gazebo接口
sudo apt install ros-melodic-gazebo-ros-pkgs ros-melodic-gazebo-msgs ros-melodic-gazebo-plugins
# 验证是否连接成功
roscore
rosrun gazebo_ros gazebo
rostopic list
```

## MAVROS

https://github.com/mavlink/mavros/blob/master/mavros/README.md#installation

创建存放mavros的工作空间，这里推荐放在一个新建的单独的工作空间，因为按照这个教程走下去后，该工作空间只能用catkin build指令编译而不能用catkin_make了。

## 安装FastRTPS和Nuttx及他们的依赖

https://github.com/eProsima/Fast-DDS

```shell
# 注意 -j12  cpu线程数
```

# ROS Tutorials

## 创建ROS功能包

```shell
# 创建工作空间
cd ~/catkin_ws
catkin_make                       //创建工作空间
. ~/catkin_ws/devel/setup.bash    //将这个工作空间添加到环境环境中，source一下
source /home/chenwh/catkin_ws/devel/setup.bash
# 创建功能包
cd ~/catkin_ws/src
catkin_create_pkg drone std_msgs rospy roscpp   //功能包的依赖
catkin_create_pkg offboard roscpp mavros geometry_msgs
//eg: catkin_create_pkg <package_name> [depend1] [depend2] [depend3]
```

### 软件包依赖关系

```shell
//rospack depends1 beginner_tutorials   查看一级依赖包，这些依赖关系存储在package.xml文件中。
//roscd beginner_tutorials
//cat package.xml

//rospack depends1 rospy               间接依赖
//rospack depends beginner_tutorials   递归检测出所有嵌套的依赖包
```

### 自定义package.xml

不能缺少，定义了package的属性，相当于一个包的自我描述。

```xml
<?xml version="1.0"?>
<package format="2">
  <name>basic</name>
  <version>0.0.0</version>
  <description>The basic package</description> <!-- 描述信息，应当简短 -->
  <!-- 维护者信息，例如<maintainer email="you@yourdomain.tld">Your Name</maintainer> -->
  <maintainer email="chenwh@todo.todo">chenwh</maintainer>
  <!-- 许可证标签，一般使用BSD许可证，因为其他核心ROS组件已经在使用它，例如<license>BSD</license> -->
  <license>TODO</license>

  <!-- 依赖项标签，参考http://wiki.ros.org/catkin/package.xml#Build.2C_Run.2C_and_Test_Dependencies -->
  <buildtool_depend>catkin</buildtool_depend>
  <build_depend>geometry_msgs</build_depend>
  <build_depend>mavros</build_depend>
  <build_depend>roscpp</build_depend>
  <build_export_depend>geometry_msgs</build_export_depend>
  <build_export_depend>mavros</build_export_depend>
  <build_export_depend>roscpp</build_export_depend>
  <exec_depend>geometry_msgs</exec_depend>
  <exec_depend>mavros</exec_depend>
  <exec_depend>roscpp</exec_depend>

  <export>
      
  </export>
</package>
```

## 构建ROS功能包

`build` 目录是构建空间的默认位置，同时`cmake`和`make`也是在这里被调用来配置和构建你的软件包。而`devel`目录是开发空间的默认位置, 在安装软件包之前，这里可以存放可执行文件和库。

## 使用roslaunch

```xml
<launch>

   <group ns="turtlesim1"> # 两个分组，利用 namespace 命名空间标签区分
     <!-- node pkg 功能包   name为运行时的名称   type为实际运行的节点名称 -->
     <node pkg="turtlesim" name="sim" type="turtlesim_node"/>
   </group>
 
   <group ns="turtlesim2">
     <node pkg="turtlesim" name="sim" type="turtlesim_node"/>
   </group>
   模仿节点
   <node pkg="turtlesim" name="mimic" type="mimic">
     <remap from="input" to="turtlesim1/turtle1"/>
     <remap from="output" to="turtlesim2/turtle1"/>
   </node>
 
</launch>

<![CDATA[
	arg标签为设置launch文件内部的局部变量，运行时可以指定值或默认
	include为包含其他launch文件
	remap为重映射，类似重命名  eg:  <remap from="/talk" to="/re_talk" />
	machine为设置运行该节点的PC名称、address、ros-root还有ros-package-path等
	env为设置环境变量，如路径和IP
	group用于分组正在运行的节点
	rosparam可以load yaml文件（xml）来导入参数，xml语法较launch简单
	param标签定义要在上设置的参数参数服务器。
		1.textfile="$(find pkg-name)/path/file.txt" （可选）
		文件的内容将被读取并存储为字符串。该文件必须可在本地访问，但强烈建议您使用与包相关$(find)/file.txt语法来指定位置。
		2.binfile="$(find pkg-name)/path/file" （可选）
		该文件的内容将被读取并存储为 base64 编码的 XML-RPC 二进制对象。该文件必须可在本地访问，但强烈建议您使用与包相关的$(find)/file.txt语法来指定位置。
		3.command="$(find pkg-name)/exe '$(find pkg-name)/arg.txt'" (可选)
		命令的输出将被读取并存储为字符串。强烈建议您使用与包相关的$(find)/file.txt语法来指定文件参数。由于 XML 转义要求，您还应该使用单引号引用文件参数。
]]>
```

## 使用rosed

```shell
rosed [package_name] [filename] # 快速定位功能包
```

## 创建ROS消息和服务

1. msg（消息）：msg文件就是文本文件，用于描述ROS消息的字段。它们用于为不同编程语言编写的消息生成源代码。
2. srv（服务）：一个srv文件描述一个服务。它由两部分组成：请求（request）和响应（response）。

msg文件存放在软件包的msg目录下，srv文件则存放在srv目录下。

ROS中还有一个特殊的数据类型：`Header`，它含有时间戳和ROS中广泛使用的坐标帧信息。在msg文件的第一行经常可以看到`Header header`。

```cmake
## msg示例
Header header
string child_frame_id
geometry_msgs/PoseWithCovariance pose
geometry_msgs/TwistWithCovariance twist

## 打开 package.xml确保以下两行没有被注释
<build_depend>message_generation</build_depend>
<exec_depend>message_runtime</exec_depend>

## 打开 CMakeLists.txt 添加依赖 message_generation

find_package(catkin REQUIRED COMPONENTS
   roscpp
   rospy
   std_msgs
   message_generation
)

catkin_package(
  ...
  CATKIN_DEPENDS message_runtime ...
  ...)
# 取消依赖并修改 msg 文件名
add_message_files(
   FILES
   my_message.msg
)

generate_messages(
  DEPENDENCIES
  std_msgs
)

## 识别msg
rosmsg show beginner_tutorials/my_message
```

```cmake
## srv示例
string first_name
string last_name
uint8 age
uint32 score

## roscp
roscp [package_name] [file_to_copy_path] [copy_path]

## 同上配置 package.xml 和 CMakeLists.txt，注意唯一特殊之处
add_service_files(
  FILES
  my_service.srv
)

## 识别srv
rossrv show beginner_tutorials/my_service

```

## 发布者和订阅者

```c++
#include "ros/ros.h"
#include "std_msgs/String.h"
#include <sstream>

int main(int argc, char **argv)
{
  ros::init(argc, argv, "talker"); // 初始化ROS，指定节点名称

  ros::NodeHandle n; //为这个进程的节点创建句柄，创建的第一个NodeHandle实际上将执行节点的初始化。
  
  ros::Publisher chatter_pub = n.advertise<std_msgs::String>("chatter", 1000); // 告诉主节点我们将要在chatter话题上发布一个类型为std_msgs/String的消息。这会让主节点告诉任何正在监听chatter的节点，我们将在这一话题上发布数据。第二个参数是发布队列的大小。在本例中，如果我们发布得太快，它将最多缓存1000条消息，不然就会丢弃旧消息。

  ros::Rate loop_rate(10); // ros::Rate对象能让你指定循环的频率。它会记录从上次调用Rate::sleep()到现在已经有多长时间，并休眠正确的时间。在本例中，我们告诉它希望以10Hz运行。

  int count = 0;
  while (ros::ok()) // roscpp将安装一个SIGINT处理程序，它能够处理Ctrl+C操作，让ros::ok()返回false。
  {
    std_msgs::String msg;

    std::stringstream ss;
    ss << "hello world " << count;
    msg.data = ss.str();

    ROS_INFO("%s", msg.data.c_str()); // 取代printf/cout进行输出，有五级log类型

    chatter_pub.publish(msg); //发布消息

    ros::spinOnce(); // 调用回调函数

    loop_rate.sleep(); // 睡眠以控制频率
    ++count;
  }


  return 0;
}
```

```c++
#include "ros/ros.h"
#include "std_msgs/String.h"

// 回调函数，当有新消息到达chatter话题时它就会被调用。
void chatterCallback(const std_msgs::String::ConstPtr& msg)
{
  ROS_INFO("I heard: [%s]", msg->data.c_str());
}

int main(int argc, char **argv)
{
  ros::init(argc, argv, "listener");

  ros::NodeHandle n;

  ros::Subscriber sub = n.subscribe("chatter", 1000, chatterCallback); // 通过主节点订阅chatter话题。每当有新消息到达时，ROS将调用chatterCallback()函数。第二个参数是队列大小，以防我们处理消息的速度不够快。在本例中，如果队列达到1000条，再有新消息到达时旧消息会被丢弃。

  ros::spin(); // ros::spin()启动了一个自循环，它会尽可能快地调用消息回调函数。

  return 0;
}
```

```cmake
# CmakeLists中build模块添加
其中的 node 均为节点文件名而非功能包名
add_executable(node src/node.cpp)
target_link_libraries(node ${catkin_LIBRARIES})
# 订阅者和发布者需要
add_dependencies(talker beginner_tutorials_generate_messages_cpp)
add_dependencies(listener beginner_tutorials_generate_messages_cpp)
# 服务端和客户端需要
add_dependencies(add_two_ints_server beginner_tutorials_gencpp)
add_dependencies(add_two_ints_client beginner_tutorials_gencpp)
```

## 服务和客户端

```c++
#include "ros/ros.h"
#include "beginner_tutorials/AddTwoInts.h" //创建的srv文件生成的头文件

bool add(beginner_tutorials::AddTwoInts::Request  &req,    //生成的结构体
         beginner_tutorials::AddTwoInts::Response &res)
{
  res.sum = req.a + req.b;    
  ROS_INFO("request: x=%ld, y=%ld", (long int)req.a, (long int)req.b);
  ROS_INFO("sending back response: [%ld]", (long int)res.sum);
  return true;
}

int main(int argc, char **argv)
{
  ros::init(argc, argv, "add_two_ints_server");
  ros::NodeHandle n;

  ros::ServiceServer service = n.advertiseService("add_two_ints", add); // 创建服务，告诉主节点
  ROS_INFO("Ready to add two ints.");
  ros::spin();

  return 0;
}
```

```c++
#include "ros/ros.h"
#include "beginner_tutorials/AddTwoInts.h"
#include <cstdlib>

int main(int argc, char **argv)
{
  ros::init(argc, argv, "add_two_ints_client");
  if (argc != 3)
  {
    ROS_INFO("usage: add_two_ints_client X Y");
    return 1;
  }

  ros::NodeHandle n;
  ros::ServiceClient client = n.serviceClient<beginner_tutorials::AddTwoInts>("add_two_ints"); // 创建客户端
  beginner_tutorials::AddTwoInts srv;
  srv.request.a = atoll(argv[1]);
  srv.request.b = atoll(argv[2]);
  if (client.call(srv)) // 判断服务是否调用成功
  {
    ROS_INFO("Sum: %ld", (long int)srv.response.sum);
  }
  else
  {
    ROS_ERROR("Failed to call service add_two_ints");
    return 1;
  }

  return 0;
}
```

## 录制和回放数据

```shell
rosbag record -a # 带选项-a，表明所有发布的话题都应该积累在一个bag文件中。
rosbag info <your bagfile> # 检查bag文件的内容而不回放它
rosbag play <your bagfile> # 保持turtlesim但关闭turtle_teleop_key即可回放
rosbag play -r 2 <your bagfile> # 二倍速，-s制定某个时间开始
rosbag record -O subset /turtle1/cmd_vel /turtle1/pose # 录制指定话题，subset为指定的文件名
```

## 从bag文件中读取消息

```shell
$ time rosbag info demo.bag  
path:        demo.bag
version:     2.0
duration:    20.0s
start:       Mar 21 2017 19:37:58.00 (1490150278.00)
end:         Mar 21 2017 19:38:17.00 (1490150298.00)
size:        696.2 MB
messages:    5390
compression: none [600/600 chunks]
types:       bond/Status                      [eacc84bf5d65b6777d4c50f463dfb9c8]
             diagnostic_msgs/DiagnosticArray  [60810da900de1dd6ddd437c3503511da]
             diagnostic_msgs/DiagnosticStatus [d0ce08bc6e5ba34c7754f563a9cabaf1]
             nav_msgs/Odometry                [cd5e73d190d741a2f92e81eda573aca7]
             radar_driver/RadarTracks         [6a2de2f790cb8bb0e149d45d297462f8]
             sensor_msgs/Image                [060021388200f6f0f447d0fcd9c64743]
             sensor_msgs/NavSatFix            [2d3a8cd499b9b4a0249fb98fd05cfa48]
             sensor_msgs/PointCloud2          [1158d486dd51d683ce2f1be655c3c181]
             sensor_msgs/Range                [c005c34273dc426c67a020a87bc24148]
             sensor_msgs/TimeReference        [fded64a0265108ba86c3d38fb11c0c16]
             tf2_msgs/TFMessage               [94810edda583a504dfda3829e70d7eec]
             velodyne_msgs/VelodyneScan       [50804fc9533a0e579e6322c04ae70566]
topics:      /diagnostics                      140 msgs    : diagnostic_msgs/DiagnosticArray 
             /diagnostics_agg                   40 msgs    : diagnostic_msgs/DiagnosticArray 
             /diagnostics_toplevel_state        40 msgs    : diagnostic_msgs/DiagnosticStatus
             /gps/fix                          146 msgs    : sensor_msgs/NavSatFix           
             /gps/rtkfix                       200 msgs    : nav_msgs/Odometry               
             /gps/time                         192 msgs    : sensor_msgs/TimeReference       
             /image_raw                        600 msgs    : sensor_msgs/Image               
             /obs1/gps/fix                      30 msgs    : sensor_msgs/NavSatFix           
             /obs1/gps/rtkfix                  200 msgs    : nav_msgs/Odometry               
             /obs1/gps/time                    136 msgs    : sensor_msgs/TimeReference       
             /radar/points                     400 msgs    : sensor_msgs/PointCloud2         
             /radar/range                      400 msgs    : sensor_msgs/Range               
             /radar/tracks                     400 msgs    : radar_driver/RadarTracks        
             /tf                              1986 msgs    : tf2_msgs/TFMessage              
             /velodyne_nodelet_manager/bond     80 msgs    : bond/Status                     
             /velodyne_packets                 200 msgs    : velodyne_msgs/VelodyneScan      
             /velodyne_points                  200 msgs    : sensor_msgs/PointCloud2

real    0m1.003s
user    0m0.620s
sys 0m0.283s
```

```shell
ros_readbagfile 2021-07-12-19-04-56.bag /turtle1/cmd_vel | tee topics.yaml
```

## 关于roslaunch

```xml
<!-- 关于环境变量 -->
<launch>
  <group name="wg">
    <!-- 使用 env 置换符来使用ROBOT变量的值。例如，在roslaunch指令前执行：export ROBOT=pre将会使得pre.machine 被引用 -->
    <!-- 使用 env 置换符可以使得launch文件的一部分依赖于环境变量的值。 -->
    <include file="$(find pr2_alpha)/$(env ROBOT).machine" />
    <include file="$(find 2dnav_pr2)/config/new_amcl_node.xml" />
    <include file="$(find 2dnav_pr2)/config/base_odom_teleop.xml" />
    <include file="$(find 2dnav_pr2)/config/lasers_and_filters.xml" />
    <include file="$(find 2dnav_pr2)/config/map_server.xml" />
    <include file="$(find 2dnav_pr2)/config/ground_plane.xml" />

    <!-- The navigation stack and associated parameters -->
    <include file="$(find 2dnav_pr2)/move_base/move_base.xml" />
  </group>
</launch>
```

```xml
<!-- 关于机器标签machine tag，比如pre.machine文件 -->
<launch>
  <machine name="c1" address="pre1" ros-root="$(env ROS_ROOT)" ros-package-path="$(env ROS_PACKAGE_PATH)" default="true" />
  <machine name="c2" address="pre2" ros-root="$(env ROS_ROOT)" ros-package-path="$(env ROS_PACKAGE_PATH)" />
</launch>
<!-- 这个文件对本地机器名进行了一个映射，例如"c1" 和 "c2"分别对应于机器名"pre1"和"pre2"。 -->
<!-- 
	一旦这个映射建立好了之后，就可以用于控制节点的启动。比如，2dnav_pr2 package里所引用的config/new_amcl_node.xml文件包含这样的语句：
	<node pkg="amcl" type="amcl" name="amcl" machine="c1">
	这可以控制 amcl节点在机器名为c1的机器上运行。
-->
```

```xml
<!-- 关于parameters -->
<node pkg="move_base" type="move_base" name="move_base" machine="c2">
  <remap from="odom" to="pr2_base_odometry/odom" />
  <param name="controller_frequency" value="10.0" />
  <param name="footprint_padding" value="0.015" />
  <param name="controller_patience" value="15.0" />
  <param name="clearing_radius" value="0.59" />
  <rosparam file="$(find 2dnav_pr2)/config/costmap_common_params.yaml" command="load" ns="global_costmap" />
  <rosparam file="$(find 2dnav_pr2)/config/costmap_common_params.yaml" command="load" ns="local_costmap" /> <!-- 与上一行归属于不同的域名 -->
  <rosparam file="$(find 2dnav_pr2)/move_base/local_costmap_params.yaml" command="load" />
  <rosparam file="$(find 2dnav_pr2)/move_base/global_costmap_params.yaml" command="load" />
  <rosparam file="$(find 2dnav_pr2)/move_base/navfn_params.yaml" command="load" />
  <rosparam file="$(find 2dnav_pr2)/move_base/base_local_planner_params.yaml" command="load" />
</node>
<!--
	这一小段代码负责启动move_base节点。 第一个引用元素是 remapping. 设计Move_base时是希望它从 "odom" topic 接收里程计信息的。在这个pr2案例里，里程计信息是发布在pr2_base_odometry topic上,所以我们要重新映射一下。
	当一个给定类型的信息在不同的情况下发布在不同的topic上，我们可以使用topic remapping
	<node>内有好几个<param>标签。这些参数是节点的内部元素（因为它们都写在</node>之前），因此它们是节点的私有参数私有参数。比如，第一个参数将move_base/controller_frequency设置为10.0。
	在<param>元素之后,还有一些<rosparam>元素，它们将从yaml文件中读取多个参数。yaml是一种易于人类读取的文件格式，支持复杂数据的结构。这是第一个<rosparam>所加载的costmap_common_params.yaml文件一部分:
-->
```

```xml
<launch>
<include file="$(find 2dnav_pr2)/move_base/2dnav_pr2.launch" />
<!-- 重载roslaunch修改参数 -->
<param name="move_base/local_costmap/resolution" value="0.5"/>
</launch>
```

# Gazebo Tutorials

## 关于物理引擎

http://gazebosim.org/tutorials?tut=physics_params&cat=physics

## 模型制作

```shell
gazebo velodyne.world -u # -u 以暂停仿真的形式运行
```

## Spawning Object In Simulation

### Create a Simple Box URDF

```xml
<robot name="simple_box">
  <link name="my_box">
    <inertial>
      <origin xyz="2 0 0" />
      <mass value="1.0" />
      <inertia  ixx="1.0" ixy="0.0"  ixz="0.0"  iyy="100.0"  iyz="0.0"  izz="1.0" />
    </inertial>
    <visual>
      <origin xyz="2 0 1"/>
      <geometry>
        <box size="1 1 2" />
      </geometry>
    </visual>
    <collision>
      <origin xyz="2 0 1"/>
      <geometry>
        <box size="1 1 2" />
      </geometry>
    </collision>
  </link>
  <gazebo reference="my_box">
    <material>Gazebo/Blue</material>
  </gazebo>
</robot>
```

### Spawn Model in Simulation

```shell
rosrun gazebo spawn_model -file `pwd`/object.urdf -urdf -z 1 -model my_object
```

### Spawn Additional Example Objects in Simulation

```xml
<launch>
  <!-- send table urdf to param server -->
  <param name="table_description" command="$(find xacro)/xacro.py $(find gazebo_worlds)/objects/table.urdf.xacro" />

  <!-- push table_description to factory and spawn robot in gazebo -->
  <node name="spawn_table" pkg="gazebo" type="spawn_model" args="-urdf -param table_description -z 0.01 -model table_model" respawn="false" output="screen" />
</launch>
```

## 关于xacro

http://wiki.ros.org/xacro

## 统一机器人描述格式

1. <link>标签用于描述机器人某个刚体部分的外观和物理属性，包括尺寸（size）、颜色（color）、形状（shape）、惯性矩阵（inertial matrix）、碰撞参数（collision properties）等。<visual>标签用于描述机器人link部分的外观参数，<inertial>标签用于描述link的惯性参数，而<collision>标签用于描述link的碰撞属性。<origin>标签定义了joint的起点
2. <joint>标签用于描述机器人关节的运动学和动力学属性，包括关节运动的位置和速度限制。连接两个刚体<link>。<calibration>：关节的参考位置，用来校准关节的绝对位置。<dynamics>：用于描述关节的物理属性，例如阻尼值、物理静摩擦力等，经常在动力学仿真中用到。<limit>：用于描述运动的一些极限值，包括关节运动的上下限位置、速度限制、力矩限制等。<mimic>：用于描述该关节与已有关节的关系。<safety_controller>：用于描述安全控制器参数。
   1. continuous  旋转关节，可以绕单轴无限旋转
   2. revolute  旋转关节，但有角度极限
   3. prismatic  滑动关节，有位置极限
   4. planar  平面关节，允许在平面正交方向上平移或旋转
   5. floating  浮动关节，允许进行平移、旋转运动
   6. fixed  固定关节，不允许运动
3. <robot>是完整机器人模型的最顶层标签，<link>和<joint>标签都必须包含在<robot>标签内。
4. <gazebo>标签用于描述机器人模型在Gazebo中仿真所需要的参数，包括机器人材料的属性、Gazebo插件等。该标签不是机器人模型必需的部分，只有在Gazebo仿真时才需加入。

## 创建机器人描述功能包

```shell
$ catkin_create_pkg mrobot_description urdf xacro
·urdf：用于存放机器人模型的URDF或xacro文件。
·meshes：用于放置URDF中引用的模型渲染文件。
·launch：用于保存相关启动文件。
·config：用于保存rviz的配置文件。

URDF提供了一些命令行工具，可以帮助我们检查、梳理模型文件
$ check_urdf mrobot_chassis.urdf
还可以使用urdf_to_graphiz命令查看URDF模型的整体结构：
$ urdf_to_graphiz mrobot_chassis.urdf
```

## 使用xacro优化URDF

```xml
<xacro:property name="M_PI" value="3.14159"/> <!-- 常量定义 -->
<origin xyz="0 ${(motor_length+wheel_length)/2} 0" rpy="0 0 0"/> <!-- 调用数学公式 -->

<!-- 宏定义 -->
<xacro:macro name="mrobot_standoff_2in" params="parent number x_loc y_loc z_loc">
	<joint name="standoff_2in_${number}_joint" type="fixed">
		<origin xyz="${x_loc} ${y_loc} ${z_loc}" rpy="0 0 0" />
		<parent link="${parent}"/>
		<child link="standoff_2in_${number}_link" />
	</joint>
	<link name="standoff_2in_${number}_link">
		<inertial>
			<mass value="0.001" />
			<origin xyz="0 0 0" />
			<inertia ixx="0.0001" ixy="0.0" ixz="0.0"
					 iyy="0.0001" iyz="0.0"
					 izz="0.0001" />
		</inertial>
		<visual>
			<origin xyz=" 0 0 0 " rpy="0 0 0" />
			<geometry>
				<box size="0.01 0.01 0.07" />
			</geometry>
			<material name="black">
				<color rgba="0.16 0.17 0.15 0.9"/>
			</material>
		</visual>
		<collision>
			<origin xyz="0.0 0.0 0.0" rpy="0 0 0" />
			<geometry>
				<box size="0.01 0.01 0.07" />
			</geometry>
		</collision>
	</link>
</xacro:macro>
```



## Others

1. urdf是ROS原生支持格式，Gazebo使用sdf较好

# mavros



# Others

## 关于ROS的CMakeLists.txt

[ROS学习之catkin CMakeList.txt - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/62344573)

```cmake
cmake_minimum_required(VERSION 3.0.2) 
#Cmake版本说明
project(learning_cplus) 
#功能包名称，可以使用变量 ${PROJECT_NAME} 在后面引用项目名称
find_package(catkin REQUIRED COMPONENTS 
roscpp 
rospy 
std_msgs 
message_generation 
) 
#找寻依赖的CMake包 
generate_messages( 
DEPENDENCIES 
std_msgs 
) 
#调用已生成的消息，我们的头文件有用到 
catkin_package( 
# INCLUDE_DIRS include 
# LIBRARIES learning_cplus 
CATKIN_DEPENDS roscpp rospy std_msgs message_runtime
# DEPENDS system_lib 
) 
#编译依赖配置
include_directories(include ${catkin_INCLUDE_DIRS} src/)  # 库文件
# add_library(src/Parameter.h)   # 具体头文件
add_executable(publisher src/publisher.cpp) 
target_link_libraries(publisher ${catkin_LIBRARIES}) 
add_dependencies(publisher learning_cplus_generate_messages_cpp) 
add_executable(listener src/listener.cpp) 
target_link_libraries(listener ${catkin_LIBRARIES}) 
add_dependencies(listener learning_cplus_generate_messages_cpp)
```



```shell
启动gazebo仿真
make px4_sitl_default gazebo
启动MAVROS,链接到本地ROS
roslaunch mavros px4.launch fcu_url:="udp://:14540@127.0.0.1:14557"
运行外部控制程序
rosrun offboard offboard_node
```



一份教程：XTDrone (https://github.com/robin-shaun/XTDrone)

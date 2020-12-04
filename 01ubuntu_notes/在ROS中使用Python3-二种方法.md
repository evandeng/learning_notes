# 在ROS中使用Python3-二种方法

当前ROS是只支持Python2.7的。Python3的支持在ROS的计划中，详细的可以看[这里](https://github.com/ros-infrastructure/rep/blob/rep151/rep-0151.rst)。简单说来就是要到2019年ROS的N版本才能完全支持Python3。

首先要了解为什么ROS不能支持Python3.对于纯的Python代码同时支持Python3和Python2.7是比较容易的，基本上ROS的代码也都是支持的。问题在于包含了C++或者C的那部分Python代码。Python2.7和Python3的c module代码相差很大。一次只能编译其中的一种版本。而且很多module没有做好Python3的支持。在Python3环境下也无法编译。这就是ROS无法支持Python3的原因。目前ROS的核心包都是支持用Python3从源码编译的。但是官方并没有发布Python3的软件包。所以想要使用的话要自己编译。下面介绍两种使用Python3的方法。

1. 使用Python3和Python2.7的混合环境
   原理：使用virtualenv创建一个Python3的环境。然后在这个环境中编译安装自己需要的软件包。在引用软件包的时候，如果没有对应的Python3的软件包，会自动的去Python2.7的环境里面找。这样很多软件包都是可以通用的。当然对于没有做好Python3支持的软件包也是没法用的。

下面以geometry2为例子

```bash
sudo apt-get install python3-dev
mkdir catkin_ws # 创建工作空间
cd catkin_ws
mkdir src
cd src 
# 下载geometry和geometry2的源代码
git clone https://github.com/ros/geometry
git clone https://github.com/ros/geometry2
cd ..
# 创建虚拟环境
virtualenv -p /usr/bin/python3 venv
source venv/bin/activate
pip install catkin_pkg pyyaml empy rospkg numpy
catkin_make
source devel/setup.bash
123456789101112131415
```

这样就能够成功在Python3下使用tf的相关函数了。

运行Python3程序

```
Python 3.5.2 (default, Nov 23 2017, 16:37:01) 
[GCC 5.4.0 20160609] on linux
Type "help", "copyright", "credits" or "license" for more information.
>>> import tf
>>> tf.__file__
'/home/xiaoqiang/Desktop/catkin_ws/devel/lib/python3/dist-packages/tf/__init__.py'
>>> import tf2_py
>>> tf2_py.__file__
'/home/xiaoqiang/Desktop/catkin_ws/devel/lib/python3/dist-packages/tf2_py/__init__.py'
>>> 
12345678910
```

可以看到我们的tf已经是在Python3的路径下了。

1. 从源码安装ROS
   首先把ROS都卸载干净

```
sudo apt-get purge ros-*
sudo apt-get autoremove
12
```

创建ROS工作空间

```
mkdir ros
1
```

创建Python3环境

```
cd ros
# 把系统默认Python替换成Python3
sudo rm -rf /usr/bin/python
sudo ln -s /usr/bin/python3.5 /usr/bin/python
sudo apt install python3-pip
12345
```

安装ros的编译基础软件包

```bash
sudo apt-get install python3-rosdep python3-rosinstall-generator python3-wstool python3-rosinstall build-essential
sudo pip3 install catkin_pkg
12
```

开始下载相关软件包

```bash
rosinstall_generator desktop --rosdistro kinetic --deps --tar > kinetic-desktop.rosinstall
wstool init -j8 src kinetic-desktop.rosinstall
# 如果上一步有失败的，执行
wstool update -j 4 -t src
1234
```

这里安装的是kinetic版本。其他版本需要调整对应的参数。

安装软件包依赖

```bash
rosdep install --from-paths src --ignore-src --rosdistro kinetic -y
sudo apt-get install libtbb-dev python3-pyqt5
sudo pip3 install empy numpy defusedxml netifaces
# 修复 16.04 libboost_python3找不到的问题
sudo ln -s /usr/lib/x86_64-linux-gnu/libboost_python-py35.so /usr/lib/x86_64-linux-gnu/libboost_python3.so
12345
```

编译工作空间

```
./src/catkin/bin/catkin_make_isolated --install -DCMAKE_BUILD_TYPE=Release
1
```

如果这个过程中编译有错误，一般是缺少软件包之类的。需要下载安装对应的软件包然后再次编译。直到全部编译成功。

添加环境变量

在~/.bashrc里面添加

```bash
source /home/xiaoqiang/Documents/ros/install_isolated/setup.bash
1
```

这里是小强的路径，你要根据自己的工作空间的位置进行修改。

之后再重新打开一个终端就可以了。

```
In [2]: import tf2_ros

In [3]: tf2_ros.__file__
Out[3]: '/home/xiaoqiang/Documents/ros/install_isolated/lib/python3/dist-packages/tf2_ros/__init__.py'

In [4]: 
123456
```

可以看到现在的ROS使用的Python已经是Python3了。

总结虽然以上两种方法都可以使用Python3,但是推荐还是使用第一种方法。第二种方法使用起来比较费事，而且一旦使用了Python3就没办法使用Pyhon2.7了。有些软件包并没有做好Pyhon3的支持。这样会在使用中产生不少问题。
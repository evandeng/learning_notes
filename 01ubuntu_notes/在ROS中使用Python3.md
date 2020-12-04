# [在ROS中使用Python3](https://www.cnblogs.com/h46incon/p/6207145.html)

Use Python3 in ROS.
以下内容在**Ubuntu 16.04 x64**和**ROS kinetic**中测试通过
事实上，只要在.py文件加上python3的shebang，rosrun的时候就会自动调用相应的解释器了：

```
#!/usr/bin/env python3
```

但是，在安装`ros-kinetic-desktop-full`的时候，也并没有安装python3相关的package，所以需要手动安装。

------

#### 1. 创建virtualenv环境（非必须）。

这一步不是必要的，在这只是为了保险起见。

```
sudo pip install virtualenv
# 我在这使用的是Python3.6rc2，指向系统已安装的python3也可以。
virtualenv rosenv -p /opt/python36rc/bin/python3
source rosenv/bin/activate
python -V
# Python 3.6.0rc2
```

#### 2. 安装相应的Package

需要安装`catkin-tools`和`rospkg`。

```
# 没有创建virtualenv的话，注意需要使用pip3
pip install catkin-tools 
pip install rospkg
# pip install catkin_pkg rospkg pyyaml empy numpy
```

#### 3. 按正常方式创建ROS Packgae

创建Packge和Node的时候，无需source virtualenv。
但是，**记得**要在python node的文件头部加上shebang，**否则rosrun仍会调用python2**：

```
#!/usr/bin/env python3
```

#### 4. rosrun

如果使用virtualenv，`rosrun`前记得source virtualenv就好了。

```
source ros_test/devel/setup.zsh
# 如没有使用virtualenv，下一语句则跳过
source rosenv/bin/activate
rosrun py3test pytest.py
```

# Note

需要注意的是，即使使用的virtualenv中，python指向的是python3，但是只要不在.py文件中添加python3的shebang，`rosrun`还是会调用python2的。这样可以很方便地兼容原来Python2的Package。
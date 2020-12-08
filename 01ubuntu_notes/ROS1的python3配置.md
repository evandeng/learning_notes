# ROS1+python3配置

ubuntu18.04+ROS1+python3+cv2+cv_bridge

#### 一、创建虚拟环境

为了尽量在装包的时候不损坏外部环境，建议用户使用virtualenv。基本流程：

```
# 安装pip3
sudo apt-get install python3-pip
# 安装虚拟命令 
pip3 install virtualenv
pip3 install virtualenvwrapper
# 配置虚拟环境 
mkdir $HOME/.virtualenvs
# 打开 ~/.bashrc 文件，并添加如下：
    export WORKON_HOME=$HOME/.virtualenvs
    source /usr/local/bin/virtualenvwrapper.sh
# 更新bashrc
source ~/.bashrc
# 系统报错提示，bash: /usr/local/bin/virtualenvwrapper.sh: 没有那个文件或目录
# 修改~/.bashrc 中的内容为： 
	export WORKON_HOME=$HOME/.virtualenvs
	source ~/.local/bin/virtualenvwrapper.sh
# 创建虚拟环境
mkvirtualenv --python=/usr/local/python3.6 venv

# 其他命令。激活/退出/删除虚拟环境
workon venv
deactivate
rmvirtualenv venv
```

#### 二、在虚拟环境中安装cv2

```
# 进入虚拟环境
workon py3
# 安装cv2
pip3 install opencv-python
pip3 install catkin-tools 
pip3 install rospkg
# 退出虚拟环境
deactivate
```

#### 三、安装ROS1

最新安装网址`http://wiki.ros.org/noetic/Installation`。选择自己系统对应的版本18.04是 [ROS Melodic installation](http://wiki.ros.org/melodic/Installation)

**安装ROS后，先不要进入ROS tutorials**.

#### 四、配置ros环境

```
# 激活ros
source /opt/ros/melodic/setup.bash
# 安装工具
sudo apt-get install python-catkin-tools python3-dev python3-catkin-pkg-modules python3-numpy python3-yaml ros-melodic-cv-bridge
# 创建环境文件
cd
mkdir catkin_ws
catkin_make -DPYTHON_EXECUTABLE=/usr/bin/python3 -DPYTHON_INCLUDE_DIR=/usr/include/python3.6m -DPYTHON_LIBRARY=/usr/lib/x86_64-linux-gnu/libpython3.6m.so
# 克隆cv_bridge src
git clone https://github.com/ros-perception/vision_opencv.git src/vision_opencv
# 查询对应安装版本
apt-cache show ros-melodic-cv-bridge | grep Version
	例如：Version: 1.12.8
# 设置安装版本
cd src/vision_opencv/
git checkout 1.12.8
cd ../../
# 安装
catkin_make
# 进入ros环境
source devel/setup.bash
```

#### 五、测试是否可用

```
# 新建command
# 进入虚拟环境
workon py3
# 激活ROS环境
source catkin_ws/devel/setup.bash
# 启动python
python
# 导入测试命令，不报错，则成功。
import cv2
import cv_bridge
from cv_bridge.boost.cv_bridge_boost import getCvType
```

#### 六、按正常方式创建ROS Packgae

创建Packge和Node的时候，无需source virtualenv。
但是，**记得**要在python node的文件头部加上shebang，**否则rosrun仍会调用python2**：

```
#!/usr/bin/env python3
```

#### 七、rosrun

如果使用virtualenv，`rosrun`前记得source virtualenv就好了。

```
source ros_test/devel/setup.zsh
# 如没有使用virtualenv，下一语句则跳过
source rosenv/bin/activate
rosrun py3test pytest.py
```

Ros2 play record ros1 的rosbag

```
# Ros2 play record ros1 的rosbag
https://index.ros.org/doc/ros2/Tutorials/Rosbag-with-ROS1-Bridge/
```

# 遇到问题总结：

#### 1. ModuleNotFoundError: No module named 'em'

在安装编译ROS的时候是链接python3，但报错是2.7package无法找到包。在实际安装em相关的包时，只需装到python3就行。解决命令：

`sudo apt-get install python3-empy`





# Note

需要注意的是，即使使用的virtualenv中，python指向的是python3，但是只要不在.py文件中添加python3的shebang，`rosrun`还是会调用python2的。这样可以很方便地兼容原来Python2的Package。



##### reference

cv_bridge: https://www.it1352.com/1701211.html


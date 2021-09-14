# 配置虚拟环境virtualenv

#### 1. 方法一

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
mkvirtualenv --python=/usr/local/python3.5.3/bin/python venv
virtualenv -p /usr/bin/python2 --no-site-packages venv

# 其他命令。激活/退出/删除虚拟环境
workon venv
deactivate
rmvirtualenv venv
```

#### 2. 方法二

##### 1）用pip安装virtualenv

```
sudo apt-get install python-virtualenv
```

##### 2）创建python2的虚拟环境，进入要创建虚拟环境的目录下，我是放在/home/xyy/下

```
root@xyy-Lenovo-G450:/home/xyy# virtualenv -p /usr/bin/python2 py2env
```

创建完可以进入py2env/bin/下查看

```
root@xyy-Lenovo-G450:/home/xyy/py2env/bin# ls
activate      activate.fish     easy_install      pip   pip2.7  python2
activate.csh  activate_this.py  easy_install-2.7  pip2  python  python2.7
```

##### 3）创建python3的虚拟环境，进入要创建虚拟环境的目录下，我是放在/home/xyy/下

```
root@xyy-Lenovo-G450:/home/xyy# virtualenv -p /usr/bin/python3 py3env
```

创建完可以进入py3env/bin/下查看

```
root@xyy-Lenovo-G450:/home/xyy/py3env/bin# ls
activate      activate.fish     easy_install      pip   pip3.4  python3
activate.csh  activate_this.py  easy_install-3.4  pip3  python  python3.4
```

##### 4）激活虚拟环境：

```
root@xyy-Lenovo-G450:~# source /home/xyy/py2env/bin/activate
(py2env)root@xyy-Lenovo-G450:~# 
```

可以看到第二行，在命令行前面多了一个括号（py2env），这样就进入虚拟环境了。
进入python3虚拟环境基本一样

```
root@xyy-Lenovo-G450:~# source /home/xyy/py3env/bin/activate
(py3env)root@xyy-Lenovo-G450:~# 
```

##### 5）退出虚拟环境

```
(py2env)root@xyy-Lenovo-G450:~# deactivate 
root@xyy-Lenovo-G450:~#
```

#### 3. 只安装virtualenv

当安装virtualenvwrapper出现问题时候，只是用virtualenv创建虚拟环境，使用时：

```
virtualenv -p /usr/bin/python2.7 venv
virtualenv --system-site-packages venv
virtualenv --no-site-packages venv
virtualenv -p /usr/bin/python2 --no-site-packages venv
```

激活和退出虚拟环境

```
source ~/.virtualenv/py35/bin/activate
deactivate
```



### 问题汇总

##### 1. 注意：pip创建的过程中报错,无法使用使用python3环境

Running virtualenv with interpreter /usr/bin/python3.6
Traceback (most recent call last):
File "/usr/local/lib/python2.7/dist-packages/virtualenv.py", line 22, in
import distutils.spawn
ModuleNotFoundError: No module named 'distutils.spawn'

```
# 解决方式(缺少distutils.spawn)，安装distutils
sudo apt-get install python-distutils
```

重新创建,创建成功

##### 2. could not import hook_loader

这里不要慌 ，经过多方面测试，只要执行一条语句便解决
sudo apt install --reinstall python3-pkg-resources python3-setuptools

##### 3. virtualenv打开出错，dist:Optional[distribution]=none

版本问题。

```
pip3 install --upgrade virtualenv==16.7.9
```


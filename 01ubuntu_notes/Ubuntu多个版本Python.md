# Ubuntu多个版本Python

### 方法一：

1. ##### 管理工具virtualenv

virtualenv 是一个命令行的 Python 多环境管理工具，它的实现方式就是为每个项目创建一个独立的 Python 版本解释器并安装独立的依赖库。

2. ##### 管理工具[pipenv](https://pipenv.kennethreitz.org/en/latest/)

Pipenv 是一个新的命令行开发流程管理工具。它类似于 JS 的 npm，yarn 等工具，提供了开发流程的一系列工具，环境管理也是其中重要的一环。

Pipenv 根据配置文件进行管理，而配置文件可以由 git 等版本控制软件管理，所以非常便于项目的协作。

使用 Pipenv，你可以完全替换掉 virtualenv，pip 等工具（虽然它还是间接使用他们），只需要写好配置文件，一个 pipenv install 命令就可以了。其他人协作时也不需要额外的配置，用 pipenv install 安装就可以，非常方便。

3. ##### 管理工具pyenv

系统环境：ubuntu14.04LTS，系统默认的python版本为2.7，我这里想要再安装一个3.4.3版本。再安装python之前，我们首先要安装这个管理工具pyenv。https://www.cnblogs.com/baicailong/p/9751019.html

4. ##### Anaconda 

用过，不知道怎么用。

### 方法二：

在bashrc中修改python的别名

```
# 查看可使用的python版本
ls /usr/bin/python*

# 打开该用户的 ~/.bashrc文件，添加新的别名信息来修改默认使用的 Python 版本。
alias python='/usr/bin/python3.4'
. ~/.bashrc
```

或使用 update-alternatives `https://www.cnblogs.com/yifugui/p/8649864.html`

以 root 身份登录，首先罗列出所有可用的 python 替代版本信息：

```
# update-alternatives --list python``update-alternatives: error: no alternatives ``for` `python
```

如果出现以上所示的错误信息，则表示 Python 的替代版本尚未被 update-alternatives 命令识别。想解决这个问题，我们需要更新一下替代列表，将 python2.7 和 python3.4 放入其中。

```
# update-alternatives --install /usr/bin/python python /usr/bin/python2.7 1``update-alternatives: using ``/usr/bin/python2``.7 to provide ``/usr/bin/python` `(python) ``in` `auto mode``# update-alternatives --install /usr/bin/python python /usr/bin/python3.4 2``update-alternatives: using ``/usr/bin/python3``.4 to provide ``/usr/bin/python` `(python) ``in` `auto mode
```

--install 选项使用了多个参数用于创建符号链接。最后一个参数指定了此选项的优先级，如果我们没有手动来设置替代选项，那么具有最高优先级的选项就会被选中。这个例子中，我们为 /usr/bin/python3.4 设置的优先级为2，所以update-alternatives 命令会自动将它设置为默认 Python 版本。

```
# python --version``Python 3.4.2
```

接下来，我们再次列出可用的 Python 替代版本。

```
# update-alternatives --list python``/usr/bin/python2``.7``/usr/bin/python3``.4
```

现在开始，我们就可以使用下方的命令随时在列出的 Python 替代版本中任意切换了。

```
# update-alternatives --config python
```

3、移除替代版本

一旦我们的系统中不再存在某个 Python 的替代版本时，我们可以将其从 update-alternatives 列表中删除掉。例如，我们可以将列表中的 python2.7 版本移除掉。

```
# update-alternatives --remove python /usr/bin/python2.7` `update-alternatives: removing manually selected alternative - switching python to auto mode``update-alternatives: using ``/usr/bin/python3``.4 to provide ``/usr/bin/python` `(python) ``in` `auto mode
```

方法2、移除软连接

```
rm` `-rf ``/data/logs` `ln` `-s ``/temp/logs` `/data/logs
```

### 方法三：

1. 在*.py文件中可以用 ＃!/usr/bin/python 来指定程序的解释器版本

2. 修改python快捷方式指向

```
sudo rm /usr/bin/python
sudo ln -s /usr/include/python2.5 /usr/bin/python**
```

* 下载python 

  ```
  https://www.python.org/ftp/python/3.8.0/
  wget https://www.python.org/ftp/python/3.8.0/Python-3.8.0a1.tgz
  ```

  

* 解压。`tar -zxvf Python-3.8.0.tgz`

* 安装

```
cd Python-3.8.0
./configure --prefix=/usr/local/python3
make && make install
```

* 建立软连接

```
ln -s /usr/local/python3/bin/python3.8 /usr/bin/python3
ln -s /usr/local/python3/bin/pip3.8 /usr/bin/pip3
```

* 测试是否可用？

```
python3
pip3
```



## 问题汇总

1. 安装python3.8过程中，make出现permission deny的问题。原因用户没有目录权限。（试过，可行）

`sudo chmod o+w /usr/local/`

2. 启动超级权限 （没试）

```
su root 
make clean
make && make install 
```


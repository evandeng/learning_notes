## [ubuntu升级显卡驱动](https://www.cnblogs.com/luozeng/p/10448657.html)

参考：http://blog.csdn.net/Zafir_410/article/details/73188228

2.1 卸掉已安装的驱动

```
sudo apt-get purge nvidia*
```

 卸载驱动sudo apt-get purge nvidia*，这个可以卸载，但有时候也会卸载不了，如果执行这句话之后再输入nvidia-smi，仍然有Nvidia的驱动信息，则说明没有卸载成功，此时用这句话

```
sh ./nvidia.run --uninstall 
```

当然runfile安装的时候会卸载掉之前的驱动，所以可以不用手动去卸载。　

或可以ctrl+alt+f1 进入控制台，同时进行登录，输入sudo service lightdm stop

执行完后，输入sudo service lightdm start，然后输入sudo reboot，如果有出现



2.2  禁用 nouveau

参考：http://www.cnblogs.com/Qwells/p/6086773.html#undefined

新建blacklist-nouveau.conf文件，输入命令：

```
sudo gedit /etc/modprobe.d/blacklist-nouveau.conf
```

往文中写入参数：

```
blacklist nouveau
blacklist lbm-nouveau
options nouveau modeset=0
alias nouveau off
alias lbm-nouveau off
```

　　保存并退出。这一步是为了禁掉Ubuntu自带开源驱动nouveau。之后sudo reboot重启系统。在终端执行命令：

```
lsmod | grep nouveau
```

　　查看nouveau模块是否被加载。如果什么都没输出，则执行下一步。 

```
#如果nouveau禁用不了，输入下面命令，并reboot
sudo update-initramfs -u
```

2.3  给驱动run文件赋予执行权限

```
sudo chmod a+x NVIDIA-Linux-x86_64-375.66.run
```

2.4 安装 驱动

```
sudo /etc/init.d/lightdm stop   ``//关闭图形界面``sudo ./NVIDIA-Linux-x86_64-375.20.run --no-opengl-files
```

　　必须加上 --no-opengl-files ,否则会出现题主要是三种，（1）循环登录，也就是登录之后在退出来到登录界面; （2）界面变得很大，看着心慌，哈哈;   （3）登录进去了，显示正常了，结果只有桌面背景和鼠标等等

2.4.1 安装提示 “sign  the kernel module”  

   安装过程中提示是否sign the kernel module（为内核模块签名），这里要选择是。接下来会继续问使用已有的密钥给模块签名还是重新生成。

这里选在重新生成密钥。继续下去之后，密钥会在/usr/share/nvidia/下生成一个nvidia*.der（私钥）文件和nvidia*.key（公钥）文件，当提示是否删除已生成的密钥文件时，选择否，

因为之后要用到这两个文件。继续安装程序到最后会提示无法启动模块。这是因为生成的密钥还没有添加到内核的信任库中

2.5.1.安装提示 更新什么都选择NO  ，好像三个NO

 在安装驱动的时候，有一布问你”Would you like to run the nvidia-xconfig utility to automatically update your X configuration file…”什么的，选择 No。

2.6  待安装结束后，添加密钥到内核的信任列表中：

```
sudo /etc/init.d/lightdm start   ``//打开图形界面
 sudo mokutil --import /usr/share/nvidia/nvida*.der  ``//必须加上 sudo
```

　　接下来提示输入密码，再次输入密码

   然后  sudo  reboot

```
重启进入bootloader之后，由于进行了添加密钥操作，会触发shim，这时不能直接进入 boot,选择第二个 “ reroll the mok ” 会要求输入上一步输入的密码来导入密钥。按步骤操作， 如果不小心进入boot,
再次执行 sudo mokutil ...
```

启动进入系统后，就可以正常使用了。需要注意的是，由于生成的密钥添加到了内核的信任列表中，所以对生成的私钥和公钥一定要妥善保管。



--以下为备份

#### 安装nvidia驱动

##### **1. 使用 Ubuntu 软件仓库中的稳定版本安装**

查看显卡硬件型号；

在终端输入：`ubuntu-drivers devices`，可以看到显卡型号和推荐安装的版本。

- 如果同意安装推荐版本，那我们只需要终端输入：`sudo ubuntu-drivers autoinstall` 就可以自动安装了。
- 当然我们也可以使用 apt 命令安装自己想要安装的版本，比如我想安装 `340` 这个版本号的版本，终端输入：`sudo apt install nvidia-340` 就自动安装了。
- 安装过程中按照提示操作，除非你知道每个提示的真实含义，否则所有的提示都选择默认就可以了，安装完成后重启系统，NVIDIA 显卡就可以正常工作了。安装完成后你可以参照 `https://linuxconfig.org/benchmark-your-graphics-card-on-linux` 上的介绍测试你的显卡。

##### 2. 在software&update里安装

显卡驱动最简单的安装方法就是在 `software & updates` 软件中，`Additional Drivers` 选项下，系统会自动检测你电脑的显卡驱动，点击选择你显卡的驱动应用就可以了。

##### **3. 使用 PPA 第三方软件仓库安装最新版本**

- 添加 PPA 软件仓库：`sudo add-apt-repository ppa:graphics-drivers/ppa`，需要输入用户密码，按照提示还需要按下 Enter 键。
- 更新软件索引：`sudo apt update`
- 接下来的步骤同方法一，只是这样我们就可以选择安装最新版本的驱动程序了。

##### 4. **从 NVIDIA 官网下载最新版驱动手动安装**

打开终端，输入：`lshw -numeric -C display`，确认自己电脑显卡的型号。

确认显卡型号后，到 NVIDIA 的官网下载相应型号的驱动，官网地址是：`https://www.nvidia.com/Download/index.aspx`

选好后点击 SEARCH 按钮搜索相应的驱动。

接着需要先安装一些 NVIDIA 显卡依赖的软件，在终端依次执行如下命令：

- `sudo dpkg --add-architecture i386`
- `sudo apt update`
- `sudo apt install build-essential libc6:i386`

Ubuntu 系统默认安装好是使用的一个开源的驱动：`nouveau`，我们要安装官方的驱动需要先禁用这个开源驱动，方法如下，依次执行：

- `sudo bash -c "echo blacklist nouveau > /etc/modprobe.d/blacklist-nvidia-nouveau.conf"`
- `sudo bash -c "echo options nouveau modeset=0 >> /etc/modprobe.d/blacklist-nvidia-nouveau.conf"`

执行完上面两条指令后，我们使用如下命令看看是否成功禁用了开源驱动：`cat /etc/modprobe.d/blacklist-nvidia-nouveau.conf`。如果和下面一样，表示成功了。

```text
$ cat /etc/modprobe.d/blacklist-nvidia-nouveau.conf
blacklist nouveau
options nouveau modeset=0
```

这个时候我们需要先重启一下系统，重启吧。

重启成功后打开终端，输入如下命令：`sudo telinit 3`。然后按快捷键：`CTRL+ALT+F1` 进入字符界面，输入用户名和密码，然后登录系统，进入我们保存下载 NVIDIA 驱动的目录，默认是：`Downloads/` 目录，`cd Downloads/`，然后执行：`bash NVIDIA-Linux-x86_64-418.43.bin`。注意：`NVIDIA-Linux-x86_64-418.43.bin` 要根据你下载的驱动文件的名字相应改动。

安装过程中都点同意即可，如果你遇到下面的提示，安装下面我的输入输入后回车继续安装：

The distribution-provided pre-install script failed!
Are you sure you want to continue? -> **CONTINUE INSTALLATION**
Would you like to run the nvidia-xconfig utility? -> **YES**

安装完成后重启系统就可以点击软件列表中的 NVIDIA 的配置软件配置显卡驱动了，如果你遇到如下报错，请依次在终端输入如下命令解决：

- 报错：WARNING: Unable to find suitable destination to install 32-bit compatibility libraries

- 解决办法：

- - sudo dpkg --add-architecture i386
  - sudo apt update
  - sudo apt install libc6:i386
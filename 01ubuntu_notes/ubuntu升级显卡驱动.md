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
blacklist nouveau blacklist lbm-nouveau options nouveau modeset=0 alias nouveau off alias lbm-nouveau off
```

　　保存并退出。这一步是为了禁掉Ubuntu自带开源驱动nouveau。之后sudo reboot重启系统。在终端执行命令：

```
lsmod | grep nouveau
```

　　查看nouveau模块是否被加载。如果什么都没输出，则执行下一步。 
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
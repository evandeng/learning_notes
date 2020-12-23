# Linux异常解决：/usr/bin/env python\r no such file or directory

### **1.异常发生背景：**

我有两台电脑，一台win10系统，一台Liunx系统。今天在win10上面写了一个ROS程序，拷贝到Linux上面，执行rosrun命令出现异常信息'/usr/bin/env python\r no such file or directory'。

### **2.异常原因分析：**

一开始以为是sublime的格式设置问题，以为win10和ubuntu里面的缩进格式设置的不一样（TAB和空格），这个问题也曾经困扰过我。后来发现不是这个问题。在百度了该异常代码后发现是DOS系统下和Linux系统下对于换行键的表示不同。

在windows下，用连续的'\r'和'\n'两个字符进行换行。'\r'为回车符，'\n'为换行符，比如原来的'aaabbb'更改为'aaa \n bbb'后输出的结果为：aaa 换行 bbb。

```bash
#!/usr/bin/env python\r\n
```

在Linux下，用'\n'进行换行。

```bash
#!/usr/bin/env python\n
```

所以windows下的程序会认为**`#!/usr/bin/env python`**是一行，而linux会认为**`#!/usr/bin/env python\r`**是一行。

### 3.异常解决

看了很多网上的教程，说用VIM编辑器进行修改，由于我不是很常用VIM编辑器，尝试了几次并没有成功。后来发现可以用dos2unix这个包进行转换。

首先通过apt-get命令安装dos2unix的包，然后通过dos2unix这个命令即可完成转换。

```
sudo apt-get install dos2unix


dos2unix <filename>
```
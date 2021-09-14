# python中glob的用途

 glob是python自带的一个操作文件的相关模块，由于模块功能比较少，所以很容易掌握。用它可以查找符合特定规则的文件路径名。使用该模块查找文件，只需要用到： **“\**\**\*”, “\**?\**”, “\**[]\**”这**三个匹配符;

```
”*”匹配0个或多个字符；
”?”匹配单个字符；?at(Cat,cat,Bat)
”[]”匹配指定范围内的字符，如：[0-9]匹配数字。[CB]at(Cat,Bat),[!C](Bat,bat),Letter[0-9](Letter0,Letter2,Letter9),
"**" 表示匹配任意中间目录，比如 a/**/z 可以匹配 a/z , a/b/z 或 a/b/c/z 等。
```

## 一、glob.glob：

```
返回所有匹配的文件路径列表。它只有一个参数pathname，定义了文件路径匹配规则，这里可以是绝对路径，也可以是相对路径。下面是使用glob.glob的例子：

for xmlPath in glob.glob('/media/ai1/DATAPART11/LIDC-IDRI' +"/*"):
#解释：遍历指定文件夹下所有文件或文件夹


for xmlPath in glob.glob(xmlPath + "/*/*"):
#解释：遍历指定文件夹下的所有文件夹里的所有文件，/*/*可以根据文件夹层数自主设定


img_path = sorted(glob.glob(os.path.join(images, '*.npy')))
#解释：遍历文件夹下所有npy文件

 

import glob
#获取指定目录下的所有图片
print glob.glob(r"E:/Picture/*/*.jpg")
#获取上级目录的所有.py文件
print glob.glob(r'../*.py') #相对路径
```

### 总结：python的glob模块可以对文件夹下所有文件进行遍历，并保存为一个list列表



## 二、glob.iglob：

获取一个可编历对象，使用它可以逐个获取匹配的文件路径名。与glob.glob()的区别是：glob.glob同时获取所有的匹配路径，而glob.iglob一次只获取一个匹配路径。这有点类似于.NET中操作数据库用到的DataSet与DataReader。下面是一个简单的例子：

```
import glob  

#父目录中的.py文件  
f = glob.iglob(r'../*.py')  

print f #<generator object iglob at 0x00B9FF80>  

for py in f:  
    print py  
import glob #父目录中的.py文件 f = glob.iglob(r'../*.py') print f #<generator object iglob at 0x00B9FF80> for py in f: print py
```

### 总结：iglob与glob类似，只是这里返回值为迭代器，对于大量文件时更为省内存
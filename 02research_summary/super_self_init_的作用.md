# super(A,self).__init__()的作用：

一句话总结就是，强继承。

```

class Root():
    y = 'zhege'
    def __init__(self):
        self.x = '这是属性'
    def fun(self):
        # print(self.x)
        self.z = '这是fun下的属性'
        print('这是方法')
 
class A(Root):
    def __init__(self):
        # super(A,self).__init__()
        print('实例化时显示')
 
test = A()
test.fun()
print(test.y)     #初始化里面的self.x属性 是不能被继承的。而属性y是可以被继承的
# print(test.x)     #加了super（）语句之后就可以继承去方法了
print(test.z)       #普通方法下的属性可以任意调用

```


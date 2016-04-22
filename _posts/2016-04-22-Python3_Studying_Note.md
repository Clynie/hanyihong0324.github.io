---
layout: post
title:  "Python3学习笔记"
date:   2016-04-22 15:03:26 +0800
categories: Python3
---

### 列表
**创建列表**  

    a=["has", "sh", "sihd"] #Python中不区分单双引号

**访问列表数据项**

    a[0]

**求列表数据项数**

    len(a)

- **更改列表数据项
 + 在末尾增加数据项**

        a.append(“sdi”) #a是列表名

 + **从末尾删除数据项**

        a.pop()

 + **在末尾增加数据项集合**

        a.extend([“shadj”, “askjdi”])

 + **删除一个特定数据项**

        a.remove(“has”)

 + **在特定位置前加一个数据项**

        a.insert(0, “salh”)

**列表内迭代**  
例：打印不定长列表中的所有数据项

for循环的实现

    a=[“ajdk”, “lkshd”, “lksahd”, “727”, “287j”]
      for i in a:		#再忘记冒号就剁手
        print(i)  

while循环的实现

    count = 0
    	while count < len(a):
    		print(a[count]) #注意区别和for循环中print()里的数据项表示方式的不同
    		count += 1

***for循环***

**基本框架**

    for i in range(10):
      print(i)

或者

    for i in range(0, 10):

**设定步长**

    for i in range(10, 0, -1):

***while循环***

**基本框架**

    j = 0
    while j < 10:
    	print(j)
    	j = j + 1

**无限循环**

  	while True:	#大写

**列表嵌套**

    a=['ash', 'sd', ['12', ['h32u', '2h3', '2736', 'h3h']]]

**嵌套列表中数据项的特殊打开方式**

    a[2][1][3]		#这个就是h3h

**多层嵌套列表中的迭代**

如果用原来的代码

    a=['ash', 'sd', ['12', ['h32u', '2h3', '2736', 'h3h']]]
    for j in a:
      print(j)		#注意区别和while循环中print()里的数据项表示方式的不同

那么运行结果是：

    ash
    sd
    ['12', ['h32u', '2h3', '2736', 'h3h']] 	#for循环并不会深究内列表的事情

真·解决方案：

    a=['ash', 'sd', ['12', ['h32u', '2h3', '2736', 'h3h']]]

              #Python代码规范，定义函数前空两行
    def func(thelist):
      for i in thelist:
        if isinstance(i, list):
          func(i)		#递归加成技术，内存毁灭大法(滑稽)
        else:
      print(i)
    func(a)

**在列表中查找列表**  
核心科技：使用if...else...模式和isinstance()函数

isinstance()函数

    a=['klasj', '27']
    b=27
    print(isinstance(a,list))
    print(isinstance(b,list))

运行结果：
		True
		False

**isinstance()函数功能：允许检查某个特定标识符是否包含某个特定类型的数据**

**函数说明**

		isinstance(xxx, yyy)

xxx：操作对象  
yyy：一个Python类型  
返回值：布尔值

PS：我觉得这里可以理解为就是个判断功能吧……isinstance(a,list)就是判断a是不是列表

### 函数

**定义方式（顺便介绍递归）**

    def func(the_list): #the_list是参数；貌似不加_就不符合PEP8；定义函数前要空两行
		  for i in the_list:
			  if isinstance(i, list):
				  func(i)	#这里是函数递归，在Python默认设置下，递归上限为100
			  else:
          print(i)

**可选参数（默认参数）**

	 def func(the_list, level = 0)	#当只传递一个the_list参数时，默认传递一个level=0（话说这个可以用来做函数重载？？？）

***if...else...模式***

**基本框架**

    if pwd == 'apple':
      print('y')
    else:
      print('n')

**if/elif语句**

    a=int(input())
		if a <= 2:
			print('free')
		elif 2 < a < 13:		#elif语句块的数量不限
			print('child')
		else:
			print('adult')

### 注释

**单行注释**

    #这就是单行注释

**多行注释**

用三引号（单双不限）括起一段文本，形成一个没有被赋值的字符串

### 模块

**导入模块**

    import xxx

**调用模块中的函数**

    xxx.func()	#需要说明func函数从哪里来，因为可能存在不同来源的多个名为func的函数（涉及到命名空间的知识）

**将特定函数加入当前命名空间**

	 from xxx import func		#这会覆盖掉当前命名空间中已定义的func函数

### 文件操作

**os模块**

    os.getcwd()			#获得当前工作目录
    os.chdir('~/Python')	#切换目录

**文件读取**

    data = open('x.txt')	#打开文件x.txt并将其赋至名为data的文件对象
    print(data.readline())	#打印data的第一行
    data.seek(n)			#光标指向data的第n个字符前，例data.seek(0)会使光标回到文件开头
    data.close()			#关闭文件

***

## つづく

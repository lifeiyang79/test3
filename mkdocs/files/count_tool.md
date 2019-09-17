## python统计微信推送中数字出现的次数

1. 实现过程

首先我只是在jupyter里面调试（不得不说加了插件的jupyter notebook作为开发环境简直不要太爽，只要打开浏览器就可以），导入必要的包，读取文件的sys库，以及正则库re

```python
import sys
import re
```

然后就是读取文件，将文本进行处理，包括去除换行符，空格等

```python
result = []
with open('test.txt', 'r') as f:    
    for line in f:
 		result.append(list(line.strip('\n').split(',')))
#print(result)
m = [n for a in result for n in a]
n = "".join(m)
o = n.replace(' ', '')
print("原始文本", o)
```

考虑到日期时间不能算进去，首先得去除日期时间，利用正则语法匹配常见的日期格式，再去除即可

```python
reg = re.compile('(20\d{2}([\.\-/|年月\s]{1,3}\d{1,2}){2}日?(\s?\d{2}:\d{2}(:\d{2})?)?)|(\d{1,2}\s?(分钟|小时|天)前)')
```

剩下来的就好了，根据统计出来的与数字同时出现的词语，包括"个"、"元"等量词和"超过"、"超"等前置词以及"~"、"至"等连接词来进行统计即可。

```python
a = re.findall('([0-9.]+%)(~|\-|到|至)([0-9.]+%)', N)
b = re.findall('([0-9.]+)(%|百|美|城|克|-|版|估|手|\|合|世|逾|英|来|票|港|关|房|笔|市|折|涨|平|套|板|毫|微|起|派|区|的|两|附|宗|一|千|[A-Za-z]|毛|动态|指数|台|条|辆|元|城市|高|转|（|袋|股|行|万|处|人|、|型|“|左右|等|分|件|字|是|米|号|位|届|名|后|次|欧|℃|斤|公里|点|场|英里|位|周|架|座|度|M|成|寸|秒|P|k|像素|分钟|K|G|省|小时|公斤|项|户|吨|大|百万|金|种|份|岁|年|款|只|千万|亿|倍|余|多|天|以上|左右元|块|支|美元|家|个|亩|平米|平方米|平方千米|基点|关口)', O)
c=re.findall('(超过|超|近|名|或|比|现|高|及|入|逾|值|强|数|到|的|看|涨|自|是|至|以||上|冲|从|探|点|报|达|大|在|约|共计|为|[A-Za-z]|于|总计|分|出|了|前|第|合计|达到|增加|增长|上升|下降|跌破|击穿|产值|持股)([0-9.]+)',NN)
```

最后将这些统计加起来就可以了，但是在实际使用时，总是有你想不到的量词，前置词甚至连接词（这时总让人感叹中华文化博大精深），但这种误差又不能忽视，因此又添加了一行检测遗漏的代码（这里是不是和机器学习一个意思🐶）

```python
ERROR=re.findall('(.{2}[0-9.]+.{2})',NNN)
```

2. 图形化

python自带的tkinter库可以初步满足UI的需求，因此做了个图形化工具，涉及窗口、文本、按钮、标签、菜单、菜单树、滚动条、文件处理、界面布局等

* 新建父窗口（可理解为界面的最底层）

可以对其进行背景色，大小等条件设置。

```python
from tkinter import *

def gui_start():
    init_window = Tk()        #实例化出一个父窗口
    init_window.mainloop()    #父窗口进入事件循环，可以理解为保持窗口运行，否则界面不展示

gui_start()
```

运行结果：

![count_tool_pic_1.jpg](https://github.com/lifeiyang79/Test2/blob/master/files/pics/count_tool_pic_1.jpg?raw=true)



整理下代码：

```python
from tkinter import *
import hashlib

class MY_GUI():
    def __init__(self,init_window_name):
        self.init_window_name = init_window_name


    #设置窗口
    def set_init_window(self):
        self.init_window_name.title("文本处理工具_v1.2   by: 飞扬)      #窗口名
        self.init_window_name.geometry('1068x680+10+10')                 #290 160为窗口大小，+10 +10 定义窗口弹出时的默认展示位置
        self.init_window_name["bg"] = "pink"                            #窗口背景色，其他背景色见：blog.csdn.net/chl0000/article/details/7657887
        self.init_window_name.attributes("-alpha",0.9)                  #虚化，值越小虚化程度越高


def gui_start():
    init_window = Tk()              #实例化出一个父窗口
    ZMJ_PORTAL = MY_GUI(init_window)
    # 设置根窗口默认属性
    ZMJ_PORTAL.set_init_window()
                                    
    init_window.mainloop()          #父窗口进入事件循环，可以理解为保持窗口运行，否则界面不展示


gui_start()
```

* 基于父窗口创建其他组件

以数字统计为例，介绍数据读取、按钮、布局、事件触发、后台转换处理、界面展示结果。

1. 标签

标签可以使用tkinter里的Label()方法，生成标签，括号内可带入其他属性参数；
生成标签后需使用grid()或者pack()方法使其展示；

```python
self.init_data_label = Label(self.init_window_name, text="待处理数据")
self.init_data_label.grid(row=0, column=0)
self.result_data_label = Label(self.init_window_name, text="输出结果")
self.result_data_label.grid(row=0, column=12)
```

2. 文本框

tkinter库中的Text()方法，可以创建一个文本框，用于数据的录入和结果展示；

Text()方法可以传入其他参数值，用于定义如背景颜色，前景色，字体，字号、字色等

```python
self.init_data_Text = Text(self.init_window_name, width=67, height=35)  #原始数据录入框
self.init_data_Text.grid(row=1, column=0, rowspan=10, columnspan=10)
self.result_data_Text = Text(self.init_window_name, width=70, height=49)  #处理结果展示
self.result_data_Text.grid(row=1, column=12, rowspan=15, columnspan=10)
```

3. 按钮

按钮的创建可以使用Button()方法实现，可自定义按钮大小，宽度，按钮文本，背景色，以及需要触发的事件；

```python
self.str_trans_to_md5_button = Button(self.init_window_name, text="字符串转MD5", bg="lightblue", width=10,command=self.str_trans_to_md5)  # 调用内部方法  加()为直接调用
self.str_trans_to_md5_button.grid(row=1, column=11)
```

4. 事件代码：后台处理

step3中在定义按钮时，会选择触发一个事件（或称为函数），函数的实现涉及：从界面文本框中读取待处理数据---逻辑处理---界面结果文本框展示，这里的功能函数即就是前面的数字统计函数。

5. 日志打印

和上面没啥区别，直接贴代码了.

```python
    #获取当前时间
    def get_current_time(self):
        current_time = time.strftime('%Y-%m-%d %H:%M:%S',time.localtime(time.time()))
        return current_time


    #日志动态打印
    def write_log_to_Text(self,logmsg):
        global LOG_LINE_NUM
        current_time = self.get_current_time()
        logmsg_in = str(current_time) +" " + str(logmsg) + "\n"      #换行
        if LOG_LINE_NUM <= 7:
            self.log_data_Text.insert(END, logmsg_in)
            LOG_LINE_NUM = LOG_LINE_NUM + 1
        else:
            self.log_data_Text.delete(1.0,2.0)
            self.log_data_Text.insert(END, logmsg_in)
```

[整体代码](https://github.com/lifeiyang79/Test2/blob/master/files/count_tool_1.1.py)

运行结果为：

![count_tool_pic_2.jpg](https://github.com/lifeiyang79/Test2/blob/master/files/pics/count_tool_pic_2.jpg?raw=true)




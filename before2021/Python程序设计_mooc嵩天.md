# **学习 Python语言程序设计-mooc&emsp;北理 嵩天**

## 1. Python基本语法元素：

- **程序设计IPO**：I：Input&emsp;&emsp;P：Process&emsp;&emsp;O：Output
- **eval()函数**：计算字符表达式的值

## 2.Python基本图形绘制：

### 不同编程语言的特性:
- C语言：主要学习指针、内存，需要理解计算机系统结构，解决性能问题
- Java语言：面向对象，解决跨平台和交互问题
- Python语言：使用第三方库，目的在于各类问题的求解

### turtle库(单位为像素)：
- 窗体布局：
    - turtle.setup(width,height,startx,starty)：
        - width和height整数为像素，小数为占屏幕的比例。
        - startx和starty为窗体左上角与屏幕左上角的距离，单位像素。
    - turtle.goto(x,y)：使用绝对坐标
- 运动控制
    - turtle.foward(d)：前进d个像素
    - turtle.bk(d)：后退d个像素
    - turtle.circle(r,angle)：绕圈
    - turtle.setheading(absolure_angle)：设定海龟朝向，绝对角度
    - turtle.left\right(angle)：设定海龟改变朝向，相对角度
- 色彩模式
    - rgb： red&emsp;green&emsp;blue&emsp;取值为[0,255]或[0,1]
    - 默认使用小数值，可切换为整数：turtle.mode(mode) &emsp;1.0为小数，255为整数
- 画笔控制
    - turtle.penup()：画笔抬起
    - turtle.pendown()：画笔放下
    - turtle.pensize(width)：画笔宽度
    - turtle.pencolor(color)：color为(r,g,b)或者常见颜色字符串'blue'

## 3.基本数据类型：

### 函数：
- round(x,d)：对x进行四舍五入，截取前d位小数
- pow(x,y,[z])：计算x的y次幂，并对z取余。
- max()\min()：求最大最小值
- int(x,base=i)：将x变为整数，**采用i进制**
- 复数类型：z.real z.imag
- 操作符：
    - 加减乘除：+&emsp;-&emsp;*&emsp;/&emsp;
    - 整数除：//
    - 幂运算：**

### 字符串
- 切片：s=str[x;y;step]，从x到y，不包括y，步长为step。**如果步长为负数，则表示从后往前取。例如step=-1，则是对字符串进行逆序。**
- 操作符：
    - x + y：连接两个字符串
    - x * n：复制n次字符串x
    - x in s：判断x是否是s的字串
- 处理函数：
    - len(s)：计算字符串长度
    - str(x)：任意类型对应的字符串类型**可以看作与eval()函数互逆**
    - 转换进制：hex()&emsp;&emsp;oct()&emsp;&emsp;bin()
    - ord()与chr()：unicode码与int之间的转换
- 处理方法：
    - s.lower()/s.upper()：转换大小写
    - **s.split(sep)：**根据sep切分s，并返回一个列表
    - s.count(sub)：返回字串sub在s中出现的次数
    - s.replace(old,new)：替换子串
    - **s.strip(chars)：**去除在s左侧和右侧的chars中列出的字符
    - s.join(iter)：s加入到iter中每个元素后，除了最后一个元素
- **格式化：**
    - {i:<格式控制标记>}对应format中的第i个参数
    - 控制标记：
        - **例：'{0:=^20,.4f}'.format(1111.23265)**

:|<填充>|<对齐>|<宽度>|<.精度>|<类型>
:-:|:-:|:-:|:-:|:-:|:-:
引导符号|填充的字符<br>如例中的'='|<左对齐<br>>右对齐<br>^居中|输出总宽度|浮点数的精度或者<br>字符串的最大输出长度|整数类型：各进制b,d,o,x<br>浮点数类型：e,f,%<br>c：以unicode编码输出

### time 库：处理时间的标准库
- 获取时间函数：
    - **time()：**获取当前时间戳
    - ctime()：获取当前时间并返回一个易读的字符串。&emsp;例：'Mon Jul 29 17:51:16 2019'
    - **gmtime()：**返回一个标准时间类型&emsp;&emsp;time.struct_time(tm_year=2019, tm_mon=7, tm_mday=29, tm_hour=9, tm_min=53, tm_sec=3, tm_wday=0, tm_yday=210, tm_isdst=0)
- 时间格式化：
    - 显示：strftime(tpl,ts)：例：time.strftime('%Y-%m-%d %H:%M:%S',time.gmtime())
- 程序计时
    - 测量时间：perf_counter()：类似于time()
    - 程序休眠：sleep(s)

## 3.基本数据类型
- 单行动态刷新
    - 本质：用后打印的字符覆盖之前的字符
    - **不能换行，要能回退\r,end=''**

## 4.程序的控制结构

### 程序的分支结构
- 单分支 if
- 二分支 if-else：简洁形式：&emsp;<表达式1>&emsp;if&emsp;<条件>&emsp;else&emsp;<表达式2>
- 多分支 if-elif-else

### 程序的异常处理
- try except else finally：可在except后面加指定异常类型，不加则会对所有异常进行响应。else是在不发生异常时执行，finally必执行

### 程序的循环结构
- for循环
- while循环
- else：正常完成循环时执行。**break退出时不执行**

### random库：梅森旋转算法
- seed()：设置随机数种子
- random()：生成一个[0.0,1.0]的随机数，与随机数种子有关
- **扩展**
    - randint(a,b)：生成一个[a,b]之间的整数
    - randrange(m,n[,k])：生成一个[m,n]之间的以k为步长的随机整数
    - uniform(a,b)：生成一个[a,b]之间的随机小数
    - choice（seq)：从序列seq中随机选择一个元素
    - shuffle(seq)：将序列seq中元素随机排列，返回打乱后的序列
- 蒙特卡罗方法：统计模拟方法，构建一个和系统性能相似的概率模型，利用概率统计通过随机试验进行数值模拟

## 5.函数和代码复用

### 函数的定义与使用
- 函数的参数
    - **可选参数**，即设置默认参数，放在后面
    - **可变参数**，即不确定参数总数量，用 * ，组装成一个list传入函数
    - **关键字参数**，用 ** ，组装成一个dict传入函数
- 局部变量和全局变量
    - 规则1：局部变量和全局变量是不同变量
        - 可以使用 global 在函数内部使用全局变量
    - 规则2：局部变量为组合数据类型且未创建，等同于全局变量
- lambda函数
    - 匿名函数：适用于简单、能够在一行内表示的函数
    - 用法：函数名=lambda <参数> : <表达式>
    - **谨慎使用，能不用尽量不用**

### 代码复用和函数递归
- 函数与分支
- 递归的基例：即递归的出口
- 递归的链条：即进行递归的规则

### 可执行文件：PyInstaller库
- 常用参数
    - -h：常看帮助
    - --clean：清理临时文件
    - -F：生成独立的可执行文件
    - -i <图标文件名.ico>：指定使用的图标文件
- 用法：**无需进入python环境，在命令行中&emsp;&emsp; pyinstaller -i 图标名 -F python文件名**

## 6.组合数据类型

### 集合类型及操作
- **无序，唯一，集合中元素不可更改，必须为不可变数据类型**
- 用{}表示，用{}或set()建立
- 集合间操作符：|&emsp;-&emsp;&&emsp;^&emsp;>=&emsp;<=
- 集合的方法：
    - add(x)：增加x元素
    - discard(x)和remove(x)：移除x元素。如果没有x元素，remove会报KeyErro错，discard不会
    - clear()：清除所有元素
    - pop()：随机取一个，直至异常
- **数据去重**

### 序列类型及操作
- 有序，可重，**类型可以不同**，有序号的体系
- 分为：字符串类型，元组类型，列表类型
- 操作符
    - x in s：x是否在s中
    - s + t：连接两个序列s和t
    - s * n：将s复制n次
    - s[i]：返回s中第i个元素
    - s[i:j:k]:对i切片，返回s中i到j以k为步长的元素子序列。**步长可以为负数，表示逆向**
- 常用函数：
    - len(s):返回长度
    - max和min：返回最大最小值，要求是序列中元素可以比较
    - s.index(x,i,j)：s中从i到j位置中第一次出现元素x的位置
    - s.count(x)：返回序列中x出现的次数
- 元组：
    - **元组一旦创建就不可修改**
    - 使用小括号或者tuple来创建
- 列表
    - 创建后可以随意修改
    - **使用方括号或list来创建时才创建了新的列表**，赋值时仅仅是传递引用
    - **del ls[i,j,k]：删除列表ls中i到j以k为步长的元素**，注意与pop和remove区分
    - ls.remove(x)：将列表ls中出现的第一个元素删除

### 字典类型及操作
- 键(索引)与值(数据)的对应关系，可以将序列看成以整数作为索引
- 键值对的集合，键值对之间无序
- 采用大括号或dict创建，键值对用冒号表示
- 处理函数和方法：
    - del
    - d.keys：返回d中所有键的信息
    - d.values：返回d中所有值的信息
    - d.items：返回d中所有键值对的信息
    - d.get(k,< default >)：返回键k对应的值，若不存在则返回default的值
    - d.pop(k,< default >)：取出键k对应的值，若不存在则返回default的值
    - d.popitem()：随机取出一个键值对，以元组形式返回

### jieba库的使用
- 优秀的中文分词第三方库
- 原理：汉字间概率大的组成词组，形成分词结果，用户也可以新增自定义词组
- 精确模式
    - 精确切分开，不存在冗余
- 全模式
    - 把文本中所有可能的词语都扫描出来，有冗余
- 搜索引擎模式
    - 在精确模式基础上，对长词进一步切分，有冗余
- 常用函数
    - jieba.lcut(s)：以精确模式将s进行分词，返回一个词语的列表**cut_all=True则是全模式**
    - jieba.add_word(w)：向分词词典中增加新词w

## 7.文件与数据格式化

### 文件：表现为文本文件和二进制文件
- 文件处理步骤：打开-操作-关闭
- 文件打开：<变量名>=open(<文件名>,<打开模式>)
    - 打开模式：'r'只读(默认值)&emsp;  &emsp;'w'覆盖写&emsp;&emsp;'x'创建写&emsp;&emsp;'a'追加写&emsp;
    - 打开方式：'b'二进制文件模式&emsp;&emsp;'t'文本文件模式(默认值)&emsp;&emsp;'+'增加同时读写功能
- 文件关闭：<变量名>.close()
- 文件读取:
    - < f >.read(size=-1)：默认读入全部内容，如果给定参数，则读入前size长度的内容
    - < f >.readline(size=-1)：默认读入一行内容，如果给定参数，则读入该行前size的内容
    - < f >.readlines(hint=-1)：默认读入全部行，每行形成一个元素形成列表，若给出参数则读入前hint行
- 文件写入
    - < f >.write(s)：向文件中写入字符串s或字节流
    - < f >.writeline(s)：将一个元素全为字符串的列表拼接后写入文件，无换行无空格
    - **< f >.seek(offset)：改变当前文件操作指针的位置。0 -开头 1 -当前位置 2 -文件结尾**
- 数据绘图
    - 定义数据文件格式（接口）
    - 根据接口解析参数绘制图形
- 一维数据
    - 由对等关系的有序或者无序数据构成，采用限行方式组织，对应列表、数组、集合等概念
- 二维数据
    - csv数据存储格式(Comma-Separated Values)：每行一个一维数据，采用逗号分隔，无空行
- wordcloud库
    - 优秀的二词云展示第三方库
    - 以词语为基本单位，更加直观和艺术的展示文本
    - 例：w =wordcloud.WordCloud()&emsp;&emsp;w.generate(txt)&emsp;&emsp;w.to_file(filename)

## 8.程序设计方法学

### 自顶向下和自底向上
- 自顶向下（设计）：分而治之，系统的思维的体现
    - 将总问题表达成若干个小问题，并使用同样的方法进一步分解，直到分解到可以用计算机简单明了的解决
    - 是解决复杂问题的有效方法，思想是分解
- 自底向上（执行）：模块化集成
    - 按照自顶向下相反的路径操作，分单元测试，逐步组装，直至各部分以组装的思路都经过测试和验证
    - 是有效逐步组建复杂系统的有效测试方法

### python程序设计思维
- 人类思维特征
    - 逻辑思维：推理和演绎，数学为代表
    - 实证思维：实验和验证，物理为代表
    - 计算思维：设计和构造，计算机为代表：**抽象问题的计算过程，利用计算机自动化求解**
- 提高用户体验的方法：程序到产品的关键
    - 进度条展示
    - 异常处理
    - 打印程序运行的过程信息
    - 写日志文件
    - 给予帮助信息

### 基本的程序设计模式
- IPO：INPUT&emsp;PROCESS&emsp;OUTPUT
- 模块化设计：模块内部紧耦合，模块之间松耦合
- 配置化设计：程序引擎 + 配置文件。程序执行和配置分离，将可选参数配置化。关键在于接口设计

### os库：提供通用的基本的操作系统交互功能
- 路径操作：os.path子库，处理文件路径及信息
    - import os.path as op
    - op.abspath(path)：返回path在当前系统中的绝对路径
    - op.relpath(path)：返回当前程序与文件之间的相对路径
    - op.normpath(path)：归一化path的表示形式，统一用\\分隔路径
    - op.dirname(path)：返回path中的目录名称
    - op.basename(path)：返回path中的文件名称
    - op.join(path,* paths):组合path和paths，返回一个路径字符串
    - op.exists(path)：判断path是否存在，返回True或False
    - op.isfile(path)和op.isdir(path)：判断path对应的是否是文件或者目录
- 进程管理：启动系统中其他程序
    - os.sysytem(command)：执行程序或命令command
- 环境参数：获得系统软硬件信息等环境参数
    - os.chdir(path)：修改当前程序操作的路径
    - os.getcwd()：返回程序的当前路径

## 9.Python计算生态

### 从数据处理到人工智能
- 数据处理：
    1.数据表示：采用合适的方式用程序表达数据
    2.数据清洗：数据归一化、数据转换、异常值处理
    3.数据统计：数据的该要理解，数量，分布，中位数等
    4.数据可视化：直观展示数据内涵的方式
    5.数据挖掘：从数据分析获得知识，产生数据外的价值
    - 计算生态：
        - numpy：表示n维数组最基础的库，用c语言编写，提供python接口，运行效率高
        - Pandas：数据分析高层次应用库，用索引，基于numpy开发
        - Scipy：数学、科学和工程计算功能库，基于numpy开发
        ---
        - Matplotlib库：高质量的二维数据可视化功能库
        - Seaborn：统计类数据可视化功能库
        - Mayavi：三维科学数据可视化功能库
        ---
        - PyPDF2：用来处理pdf文件的工具集
        - NLTK：自然语言文本处理第三方库
        ---
        - Python-docx：创建或更新word文件的第三方库
- 机器学习
    - 计算生态
        - Scikit-learn：机器学习方法工具集
        - TensorFlow：机器学习计算框架
        - MXNet：基于神经网络的深度学习计算框架

### 从web解析到网络空间
- 网络爬虫
    - 计算生态
        - Requests：最友好的网络爬虫功能库
        - Scrapy：优秀的网络爬虫框架
        - pyspider：强大的Web页面爬取库
        ---
        - Beautiful Soup：Html和Xml的解析库
        - Re：正则表达式解析和处理功能库
        - Python-Goose：提取文章类型Web页面的功能库
- web网站开发
    - 后端框架
        - Django：最流行的Web应用框架
        - Pyramid：规模适中的Web应用框架
        - Flask：Web应用开发微框架
- 网络应用开发
    - 计算生态
        - WeRoBot：微信公众号开发框架
        - aip：百度AI开放平台接口
        - MyQR：二维码生成第三方库

### 从人机交互到艺术设计
- 界面设计
    - PyQt5：Qt开发框架的Python接口
    - wxPython：跨平台GUI开发框架
    - PyGObject：使用GTK+开发GUI的功能库
- 游戏开发
    - Pygame：简单的游戏开发功能库，基于SDL
    - Panda3D：开源、跨平台的3D渲染和游戏开发库
    - cocos2d：构建2D游戏和图形界面交互式应用的框架，支持GPU加速，适用于2D专业级游戏开发
- 虚拟现实
    - VR Zero：在树莓派上开发VR应用的库
    - pyovr：Pculus Rift的Python开发接口
    - Vizard：基于Python的通用VR开发引擎
- 图形艺术
    - Quads：迭代的艺术，像素风
    - ascii_art：AScii艺术库
    - turtle：海龟绘图体系

# <center>重要的是思想</center>

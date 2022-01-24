# <center> 开始学习Python数据分析与展示 </center>

## 课程内容导学

### 课程学习路线
- Numpy:数据表示和处理
- Matpllotlib:数据绘图可视化
- Pandas:数据分析

## 数据分析之表示

### Numpy库：关注数据的结构表达
- 数据的维度：一组数据的组织形式
    - 一维数据：对等关系的数据构成，采用线性方式组织。可采用列表和集合类型表示
    - 二维数据：由多个一维数据构成，是一维数据的组合形式。可以采用列表的列表表示
    - 多维数据：由一维数据和二维数据在新维度上扩展形成。可以采用列表的列表表示
    - 高维数据：仅利用最基本的二元关系展示数据间的复杂结构。可以采用字典类型表示键值对
- Numpy：科学计算基础库
    - 包含一个强大的N维数组对象ndarray
    - 广播功能函数
    - 整合C/C++/Fortan代码的工具
    - 线性代数、傅里叶表换、随机数生成等功能
    - 是Scipy、Pandas等数据处理或科学计算库的基础
- ndarray
    - 数组对象可以去掉元素间运算所需要的循环，使一维向量更像单个数据
    - 是一个多维数据对象，由实际数据和描述这些数据的元数据构成
- ndarray数组的创建方法
    1. 从python中列表、元组类型创建ndarray数组：x=np.arrat(list/turple，dtype=int32)
    2. 使用Numpy中函数创建ndarray数组。
        - **np.arange(n)：**类似range()函数，返回ndarray类型，元素从0到n-1
        - np.ones(shape)：根据shapr生成一个全1的数组，shape是元组类型
        - np.zeros(shape)：根据shapr生成一个全0的数组，shape是元组类型
        - np.full(shape,val)：根据shape生成一个数组，每个元素值都是val
        - **np.eye(n)：**创建一个正方的n*n单位矩阵，对角线为1.其余为0
        - np.ones_like(a)：根据数组a的形状生成一个全1的数组
        - np.zeros_like(a)：根据数组a的形状生成一个全0的数组
        - np.full_like(a,val)：根据数组a的形状生成一个数据，每个元素值都是val
        - **np.linspace(start,end,number,endpoint=True)：**根据起止数据等间距的填充数据，形成数组。number是生成的元素个数，endpoint=Flase则end不包含在生成的数组中。
        - np.concatenate()：将两个或者多个数组合并成一个新的数组
- ndarray数组的类型属性：
    - .ndim：数组的维度
    - .shape：数组的形状
    - .size：数组元素的个数
    - .dtype：数组元素的类型
    - .itersize：数组元素的占用存储空间的大小
- ndarray数组的维度变换
    - a.reshape(shape)：不改变数组元素，返回一个shape形状的数组，原数组不便
    - a.resize(shape):与reshape()功能一致，但会对原数组进行修改
    - a.swapaxes(ax1,ax2)：将数组n个维度中的两个维度进行调换
    - a.flatten()：对数组进行降维，返回折叠后的一维数组，原数组不变
    - **a.astype(new_type)：**对数组中的元素进行类型变换，返回新数组
    - a.tolist()：数组向列表的转换
- ndarray数组的操作
    - 一维数组的索引和切片：与列表类似
    - 多维数据的索引和切片：a[:,1:3,:]：表示第二个维度取第1到3（不包含）个元素，以逗号分隔维度：即以逗号分隔后采用与列表切片类似的操作

- ndarray数组的运算
    - np.abs(x)：计算各元素的绝对值
    - np.sqrt(x)：计算各元素的平方根
    - np.square(x)：计算数组各元素的平方
    - ······
    - **np.modf(a)：**将a中每个元素的小数和整数部分分开成两个数组
    - + - * /：两数组各元素进行对应运算
    - < > == !=：进行算数比较，返回布尔型的数组
- ndarray数组的存取
    - 一维、二维：**csv文件**
        - **np.savetxt(frame,array,fmt='%.18e',delimiter=None):** &emsp;frame:文件、字符串&emsp;&emsp;array：存入文件的数组&emsp;&emsp;fmt：写入文件的格式&emsp;delimiter：分割字符串，默认是空格&emsp;&emsp;例：np.savetxt('a.csv',array_a,fmt='%.1f',delimiter=',')
        - **np.loadtxt(frame,dtype=np.float,delimiter=None,unpack=Flase):**
    - 多维：
        - a.tofile(frame,sep='',format='%s'):sep默认为空的话，以二进制方式写入
        - np.fromfile(frame,dtype=float,count=-1,sep='')
        ---
        - np.save(frame,array)与np.load(frame)：Numpy专用文件存储，扩展名为.npy
- Numpy的随机数函数子库mp.random
    - .rand(shape)：根据shape创建随机数数据，浮点数，[0,1]均匀分布
    - .randn(shape)：根据shape创建随机数数据，标准正态分布
    - .randint(low,high[,shape])：根据shape创建随机整数数组，范围是[low,high]
    - .seed(s)：设置随机数种子
- Numpy的统计函数
    - sum(a,axis=None)：和
    - mean(a,axis=None)：平均值
    - average(a,axis=None,wights=None)：计算加权平均值
    - std(a,axis=None)：标准差
    - var(a,axis=None)：计算方差
    - min(a),max(a)：最大最小值
    - argmin/max(a)：a中最小最大元素降成一维后的下标
    - unravel_index(index,shape)：将一维下标index转换成多维下标
    - ptp(a)：计算数组a中元素最大值与最小值的差
    - median(a)：计算数组a中元素的中位数
- np.random的梯度函数
    - np.gardient(f)：计算数组f中元素的梯度

## 数据分析之展示

### Matplotlib库
- matplotlib.pylot：绘制各类可视化图形的命令子库。import matplotlib.pyplot as plt
    - plt.plot(x,y，format_string,** kwargs):给定两个列表，分别对应x和y轴。若只有一个，则对应y轴。foramt_string为控制曲线的字符串，可选。** kwargs则表示可以有多组(x,y,format_string)
    - **format_string:** 颜色字符（曲线颜色）+风格字符（实线、虚线等）+标记字符（数据点标记）
    - plt.axis(x_start,x_end,y_start,y_end)：设置x、y轴起止的值
    - plt.show()：展示所绘的图
    - plot.grid(True/False)：是否有网格
    ---
    - 更改全局字体：rcParams
    - 更改字体：增加属性fontproperties、fontsize等
    ---
    - plt.x/ylabel():设置x/y轴标签
    - plt.title():设置图形标题
    - plt.text()：在任意位置设置文本
    - plt.annotate()：设置带箭头的注释
    ---
    - plt.subplot(nrows,ncols,plot_number)：划分子区域绘图
    - plt.subplot2grid(Gridspec,Curspec,colspan=1,rowspan=1)：设定网格，选中网格画图
    - plr.gridspec.GridSpec()和plt.subplot()配合。类似于plt.subplot2grid
    ---
    - 饼图：plt.pie(0
    - 直方图：plt.pie()
    - 极坐标图：plt.polar()
    - 面向对象绘图

# 数据分析之概要

## Pandas库：关注数据的应用表达：数据与索引之间建立关系
- import pandas as pd
- 提供了高效的数据类型：Series和DataFrame
- 提供了各类操作方法

### Series
- 由一组数据及与之相关的数据索引组成，索引与数据一一对应
- 可以用单个数，列表，字典，ndarray,其他函数如range()等创建
- a=pd.Series(list,index=i)
- 实质是一个一维带’标签‘的数组

### DataFrame
- 由共用相同索引的一组列组成，即索引+多列数据，是一个表格型的数据类型
- 既有行索引，也有列索引
- 是一个二维带'标签'的数组

### 索引的操作
- 重新索引：.reindex()：能够改变或者重排索引
- 连接索引：.append()
- 计算差：.diff()
- 计算并：.intersecton()
- 计算并:.union()
- 删除元素：.delete()
- 增加元素：.insert()
---
- 可以通过对索引的操作，进行对数据的操作

### 运算法则
- 算术运算根据行列索引，补齐后运算，缺项填充NaN
- 维度不匹配时为广播运算，默认在1轴进行运算
- 采用+ - * /符号进行的二元运算产生新的对象：可采用.add()等方法，可设置一些参数
---
- 比较运算根据索引进行比较，不进行补齐，若维度一致，则要求尺寸一致
- 维度不匹配时为广播运算，默认在1轴
- 采用<=>=<>==!=等符号进行的二元运算产生的布尔对象

### Pandas的数据排序
- .sort_index(axis=0,ascending=True);按索引进行排序，默认升序
- .sort_values(by,axis=0,ascenging=True):按值进行排序。NaN统一放到排序末尾

### Pandas的基本统计分析
- .decribe()：返回一个包含基本统计值的Series，可以通过索引得到想要的统计值
- .cumsum()/.cumprod()：依次给出**前1、2、...、n个数**的和、积等
- 滚动计算:.rolling(w).sum()\mean(0：依次计算**相邻w个元素**的和、算数平均值等

### 数据的相关分析
- 协方差：.cov()：计算协方差矩阵
- 相关系数：.corr()：计算相关系数矩阵

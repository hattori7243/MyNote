# Python网络爬虫与信息提取

## 基本介绍
- Request库：自动爬取HTML页面，自动网络请求提交
- robot.txt：网络爬虫排除标准
- Beautiful Soup库：解析HTML页面
- Re库：正则表达式

## 网络爬虫之获取

### request库的方法
- .request(method,url,** kw)：构造一个请求，支撑以下各方法的基础方法。kw为控制访问的参数，均为可选项
- **.get()：**获取HTML网页的主要方法，对应于http的get
- **.head()：**获取HTML网页头信息的方法，对应于http的head
- .post()：向HTML网页提交post请求的方法，对应于http的post
- .put()：向HTML网页提交put请求的方法，对应于http的put
- .patch()：向HTML网页提交局部修改请求的方法，对应于http的patch
- .delete()：向HTML网页提交删除请求的方法，对应于http的delete
- 以上方法均有很多可选参数，调用完均返回一个Response对象
---
- Response对象的属性
    - .status_code：http请求的返回状态，200表示连接成功，404表示连接失败
    - .text：http相应内容的字符串形式，即url对应的页面内容
    - .encoding：从http header中猜测的相应内容的编码方式
    - .apprent_encoding：从内容中分析出的响应内容编码方式（备选编码方式）
    - .content：http响应内容的二进制形式

### HTTP协议
- Hypertext Transfer Protocol：超文本传输协议
- 基于 请求与响应 无状态 的应用层协议
- 采用 URL 作为定位网络资源的标识：格式：http://host[:port][path]

### 网络爬虫的尺寸
- 小型：爬取网页，一般用**requests**库
- 中型：爬取网站，一般用**scrapy**库
- 大规模：搜索引擎的关键，爬取全网，只能定制开发

## 网络爬虫之提取

### BeautifulSoup4库
- 对HTML页面等进行解析，返回一个BeautifulSoup：soup=BeautifulSoup(demo,'HTML.parser')
- 可以认为Beautiful Soup类和HTML文档和标签树均等价
- Beautiful Soup类的基本元素：
    - tag：标签，最基本的信息组织单元，分别用<>和</>标明开头和结尾
    - Name：标签的名字。<p>...</p>的名字是p。 tag.name
    - Attributee：标签的属性，以字典形式组织。 tag.attrs
    - NavigableString：标签内非属性字符串，即<>...</>中字符串。tag.string
    - Comment：标签内字符串的注释部分,即<!...>内容。
- 标签的遍历：
    - 下行遍历：列表：.contents 迭代类型：.children &emsp;.descendants
    - 上行遍历：返回父亲节点：.parent 迭代类型：.parents
    - 平行遍历：返回下一个平行节点标签 .next/previous sibling&emsp;迭代类型：加s
- HTML格式化输出：.pretify()

### 信息的组织和提取
- HTML通过预定义的<>...</>标签形式组织不同类型的信息
- 信息标记的种类：
    - XML(Extensible Markup Language)：采用标签。可拓展性好，但较繁琐。
    - JSON(JavaScript Object Notation)：采用有类型的键值对。信息有类型，适合程序处理，较XML简洁。
    - YAML(YAML Ain't a Markup Language):采用无类型的键值对。文本信息比例最高，可读性好。
- 信息提取的一般方法
    1. 完整解析标记形式，再提取关键信息。准确但繁琐速度慢。需要标记解析器
    2. 无视标记形式，直接搜索关键信息。准确性与内容相关但简洁速度快。需要文本查找函数
- 基于bs4库的HTML内容查找方法：.find_all()
    - < BeautifulSoup >.find_all(name,attrs,recursive,string,**kwargs)
    - 返回一个列表类型，存储查找的结果
    - name：对标签名称的检索字符串。若有多个标签，可用一个列表作为参数。若参数为True，则返回所有标签。
    - attrs：对标签属性值的检索字符串，可标注属性检索
    - recursive：是否对子孙全部检索，默认为True
    - string：字符串区域的检索字符串
    ---
    - < tag >.find_all()可以写成< tag >.find_all()，soup.find_all()可以写成soup()

## 网络爬虫之实战

### 正则表达式：用来简洁表达一组字符串的表达式
- 正则表达式的编译：re.compile():将正则表达式的字符串形式编译成正则表达式对象，可以方便调用各方法
- 原生字符串类型：不包含转义符的类型

### Re库
- Re库的常用方法
    - .search()：在字符串中搜索正则表达式的第一个位置，返回match对象
    - .match()：仅在字符串的开始位置匹配正则表达式，返回match对象
    - .finall()：搜索字符串，以列表类型返回全部能匹配的字串
    - .split()：将一个字符串按正则表达式匹配结果进行分割，返回列表类型
    - .finditer()：搜索字符串，返回一个匹配结果的迭代类型，每个迭代元素是match对象
    - .sub()：在一个字符串中替换所有匹配正则表达式的字串，返回替换后的字符串
- match对象
    - 属性：
        - .string：待匹配的文本
        - .re：匹配时使用的pattern对象（正则表达式）
        - .pos：正则表达式搜索文本的开始位置
        - .endpos：正则表达式搜索文本的结束位置
    - 方法：
        - .group()：分组
        - .start()：匹配字符串在原始字符串的开始位置
        - .end():匹配字符串在原始字符串中的结束位置
        - .span()：返回（.start(),.end()）
- Re库默认采用贪婪匹配。若采用最小匹配，在* + ? {m,n}后加?

## Scrapy框架

- 框架是实现功能的软件结构和功能组件集合

### Scrapy爬虫框架结构
- 5 + 2 结构
---
- Enigine：控制所有模块的数据流，并根据条件触发事件（已完成）
- Downloader：根据请求下载网页（已完成）
- Scheduler：对所有爬取请求进行调度管理（已完成）
- Spider：产生爬取请求，解析返回的响应（需要用户编写配置代码）
- Item Pipeline：以流水线的方式处理返回的爬取项（需要用户编写配置代码）
---
- Downloader Middleware：进行Engine、Scheduler和Downloader之间用户可配置的控制
- Spider Middleware：在Spider与Engine之间对请求和爬取项进行再处理，由用户编写代码控制
---

### Scrapy命令
- 命令行 scrapy -h 查看帮助

### Scrapy爬虫使用
1. 创建一个工程和Spider模板
2. 编写Spider
3. 编写Item Pipeline
4. 优化配置策略

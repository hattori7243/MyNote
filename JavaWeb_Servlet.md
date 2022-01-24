# javaweb三大组件:Servlet、Filter、Listener

## 1.Filter: 过滤器

- 当访问服务器的资源时，Filter可以将请求拦截下来，完成一些特殊的功能
- 功能作用：
    - 一般用于完成通用的操作：例如登陆验证、统一编码处理、敏感字符过滤。。。
    
- 使用：
    1. 定义一个类，实现接口Filter
    2. 覆写方法
    3. 配置拦截路径
        - web.xml
        - 注解

### 执行流程

- 过滤的时候，来和去并不会调用两次Filter的service方法，而是在体现在放行前后

### 生命周期

- init：服务器启动后，会自动创建Filter对象，调用init方法。并不是向Servlet那样，默认要第一次被访问才创建
- destroy：服务器正常关闭的时候，调用destroy方法。和Servlet一样

### 拦截路径的配置

- 直接改注解的urlPatterns的值

### 拦截方式的配置

- 改注解的dispatcherTypes的值
    1. REQUEST:默认值，浏览器直接请求资源
    2. FORWARD:转发访问资源
    3. INCLUDE
    4. ERROR
    5. ASYNC
- 改web.xml的< dispatcher > < /dispatcher >标签

### 过滤器链(多个过滤器)

- 过滤器拦截顺序顺序：由大到小
- 过滤器执行顺序:先放行的后返回，类似压栈入栈

### 注意的点

- 在进行登陆验证的时候，要记得排除掉登陆相关的资源，直接放行。否则会陷入循环
- 在需要通过Filter进行一些处理的时候，可以用代理模式

## 2.Listener：监听器

- 事件监听机制
    - 事件
    - 事件源
    - 监听器
    - 注册监听器：将事件、事件源、监听器绑定在一起。当事件源上发生某个事件后，执行监听器代码



## 3.Servlet = server + applet

### 概念：运行在服务器段的小程序
- Servlet就是一个接口，定义了java类能被浏览器访问（例如能被tomcat识别）的规则。
  
- Servlet接口：
![](https://raw.githubusercontent.com/hattori7243/img/master/20210302110403.png)
![](https://raw.githubusercontent.com/hattori7243/img/master/20210302110416.png)

- 快速入门：创建项目+实现Servlet接口+实现接口中的抽象方法（service是提供服务的方法）+配置Servlet(在web.xml中配置)

### 执行原理

1. 通过url确定访问的Servlet资源路径
- 查找wen.xml文件,查看< url-pattern >标签体内容，并找到对应的全类名
- 通过全类名确定java类，然后通过class.forName()来加载类进内存，进而xxx.newInstance()创建对象,再通过调用xxx.service()方法。能调用的原因是因为实现了Servlet接口，一定实现了service方法。

### 方法与servlet的生命周期

- 方法

    1. **init**方法：在servlet被创建（默认第一次访问servlet第一次被访问的时候创建）的时候执行，只会执行一次。
    2. **service**方法：在servlet被访问的时候执行，能够执行多次
    3. **destroy**方法：销毁方法，在服务器**正常关闭**时候执行，只会执行一次
    4. getServletConfig：获取ServletConfig对象
    5. getServletInfo：获取Servlet信息：版本、作者等等

- 生命周期

    1. 被创建：执行**init**方法，只执行一次
        - 默认情况下，第一次被访问时候，Servlet被创建
        - 或者在服务器启动的时候创建，在web.xml中配置
        ![](https://raw.githubusercontent.com/hattori7243/img/master/20210302145916.png)
        - Servlet的init方法只会执行一次，说明其在内存中只存在一个对象，是单例模式。
            - 所以多个用户同时访问的是，可能存在线程安全问题
            - 解决方案：尽量不要在Servlet中定义成员变量，只使用方法。如果要的话，不要去对成员变量进行修改，可以取值。
    2. 提供服务：执行**service**方法，执行多次。被刺访问servlet是，都会被调用一次
    3. 被销毁：执行**destroy**方法，只执行一次。服务器关闭时，servlet被销毁。很多时候非正常关闭，不会执行destroy方法。在servlet销毁之前被执行，用于释放资源。

### 体系结构

- Servlet是接口
    - \
    - GenericServlet -- 抽象类:将Servlet接口中其他方法做了默认空实现，只将service方法进行抽象
    -  \
    - **HttpServlet** -- 抽象类:对http协议的封装，减少了对请求类型判断的代码部分。只要覆写doPost、doGet等方法即可
    
### 相关配置

- urlpattern:
    - servlet3.0之后可以使用注解配置@WebServlet("/demo3")或者@WebServlet(urlpatterns="/demo3")
    - 可以配置多个
    - 路径定义规则：
        - /xx
        - /xx/yy
        - *.do
        ![](https://raw.githubusercontent.com/hattori7243/img/master/20210302160847.png)



# Http: Hyper Text Tranfer Protocol 超文本传输协议

- 定义了客户端和服务器段通信是发送数据的格式
- 是基于TCP/IP的高级协议，默认端口号为80
- 基于请求/响应模型，一次请求对应一次响应
- 标准的http协议（不包括cookies、seddion、application）是无状态的协议：每次请求之间相互独立，不能通信数据

## 请求消息

### 请求消息数据格式

1. 请求行
    - 请求方式 请求url 请求协议/版本
    - GET /login.html HTTP/1.1
    - 共有7种请求方式，常用的有2种：
        - **GET**:请求参数在**请求行中**，即体现在网址中?username=zhangsan，请求的url长度**有限度**
        - POST:请求参数在**请求体**中username=zhangsan，请求的url长度**没有限度**
2. 请求头
    - 键值对的形式 请求头名称:请求头值
    - 请求头和请求行之间没有任何分隔。区别是一个用空格，一个用:
    - 若有多个值用,分隔
    - 常见的请求头:
        - User-Agent：浏览器告知服务器，访问时使用的浏览器信息。可在服务器段获取用来解决浏览器的兼容性信息
        - Referer:告诉服务器我从哪里来？直接访问网页是没有的，从一个网页访问另一个url才有
            - 防盗链
            - 做统计
        - Connection:keep-alive表示链接是活着的可以被复用
3. 请求空行
    - 空行分割
4. 请求体（正文）
    - GET请求没有请求体。POST具体传输的是一些参数数据。参数名=值
![](https://raw.githubusercontent.com/hattori7243/img/master/20210303095148.png)

### Servlet中的Request对象

- Request对象的继承体系结构：
    - ServletRequest -- 接口
    - \
    - HttpRequest -- 接口
    - \
    - org.apache.catalina.connector.RequestFacade -- 实现类（实现了HttpRequest接口）
- 功能
    1. 获取请求消息数据 HttpRequest
    
        1. 请求行
            - 请求行： Get /day14/demo1?name=zhangsan HTTP/1.1
            - 获取请求方式GET：String getMethod();
            - **获取虚拟目录/day14**：String getContextPath();
            - 获取Servlet路径/demo1：String getServletPath();
            - 获取请求参数name=zhangsan：String getQueryString();
            - **获取请求URI/URL**：
                - String getRequestURI(); /day/demo1
                - StringBuffer getRequestURL(); http://localhost/day14/demo1
            - 获取协议及版本HTTP/1.1：String getProtocol();
            - 获取客户机的IP地址：String getRemoteAddr();
            
        2. 请求头数据
            - Enumeration< String > getHeaderNames();返回所有请求头名称
            - String **getHeader**(String var1);通过键值对选择的方式得到返回值
            
        3. 请求体数据:只有POST请求方式才有请求体，在请求体中封装了POST请求的参数
            1. 获取流对象
                - 获取字符输入流：BufferedReader getReader() throws IOException;只能操作字符数据
                - 获取字节输入流：ServletInputStream getInputStream() throws IOException;可以操作所有类型的数据
            2. 从流对象中拿数据
        
    2. 其他功能
    
        - 获取请求参数通用方式：GET、POST方式都适用
            - 根据参数名称获取参数值 -- String **getParameter**(String var1); -- name=zhangsan
            - 根据参数名称获取参数值的数组 -- String[] getParameterValues(String var1); -- xxx=a&xxx=b
            - 获取所有参数名称的枚举：Enumeration< String > getParameterNames();
            - 获取键值对的map：Map<String, String[]> **getParameterMap**();
            
        - 请求转发：在服务器内部的资源跳转方式
            - 获取转发器RequestDispatcher getRequestDispatcher(String var1);
            - 调用转发器中的void forward(ServletRequest var1, ServletResponse var2) throws ServletException, IOException;
            - 浏览器地址栏路径不发生变化
            - 只能转发到当前服务器内部资源中
            - 只用一次http请求访问多个资源
            
        - 共享数据
            - 请求转发中可以使用Request对象来共享数据，其作用范围是一次http请求，而请求转发是一次http请求访问多个资源
        
        - 获取ServletContext
    
- BeanUtils：简化数据的封装
    - 用于封装javaBean：BeanUtils.populate(Object obj, Map map)将map集合的键值对封装到对应的java.Bean中
    - javaBean：标准的java类
        - 类必须被public修饰
        - 必须提供空参的构造器
        - 成员变量都用private修饰
        - 必须提供公共的setter和getter方法
        - 功能：用来封装数据

## 响应消息

### 响应消息数据格式

1. 响应行
    - 协议/版本 响应的状态码 状态码的描述 -- HTTP1.1 200 OK
    - 响应状态码:服务器告诉客户端本次请求和响应的一个状态，都是3位数字
        - 1xx:服务器接受客户端消息但没有接受完成，等待一段时间后询问客户端的状态码
        - 2xx:此次响应成功。代表值200
        - 3xx:代表值302(重定向,设置响应头中location的值)、304(访问缓存)
        - 4xx:请求错误。代表值404(请求路径没有对应的资源)、405(请求方式没有对应的doXXX方法，例如GET请求但是服务器端没有doGET方法)
        - 5xx:服务器端错误。代表500(服务器内部异常)
    
2. 响应头
    - 键值对的形式 响应头名称:响应头值
    - 常见的响应头:
        - **Content-Type**：服务器告诉客户端本次响应体数据格式以及编码格式
        - Content-Length:响应长度
        - Content-disposition:服务器告诉客户端以什么格式打开响应体数据
            - 默认值in-line，即在当前页面打开
            - 可设置attachment：以附件形式打开响应体，例如文件下载
3. 响应空行
    - 空行分割
4. 响应体（正文）
    - 真实传输的数据
![](https://raw.githubusercontent.com/hattori7243/img/master/20210303095111.png)
![](https://raw.githubusercontent.com/hattori7243/img/master/20210303095242.png)

### Servlet中的Response对象

- 功能：设置响应消息
    1. 设置响应行
          - 设置状态码:setstatus(int sc)
    2. 设置响应头:setHeader(String name,String value)
    3. 设置响应体
        - 获取输出流
        - 使用输出流将数据输出到浏览器
            - 字符输出流:getWriter()
            - 字节输出流:getOuputStream
            
## 重定向vs转发

- 重定向的特点:redirect
    - 重定向地址栏会发生变化
    - 重定向可以访问任意站点的资源
    - 重定向是两次请求，不能使用request对象来共享数据
    
- 转发的特点:forward
    - 转发地址栏路径不变
    - 转发只能访问服务器内部资源
    - 转发是一次请求，意味着可以使用request对象来共享数据
    
## 路径问题

- 使用绝对路径时，判断请求是给服务器还是客户端用的？
    - 客户端用的话，需要加虚拟目录。例如超链接、重定向
        - 虚拟目录可以动态获取, request.getCOntextPath()
    - 服务器用的话，就不用。例如转发
    
## ServletContext对象

- 概念代表整个web应用，可以和程序的容器(服务器)来通信
- 获取：
    1. 通过request对象获取:request.getServletContext();
    2. 通过HttpServlet对象来获取:Http.getServletContext();
- 功能：
    1. 获取MIME类型(在互联网通信过程中定义的一种文件数据类型)
        - 格式：大类型/小类型。例如text/html,image/jpeg...
        - String getMimeType(String file)
    2. 是一个域对象，可以共享数据)
        - 范围:所有用户所有请求的数据
    3. 来获取文件的真实（服务器）路径
        - String getRealPath(String path)



# 会话技术

- 会话：一次会话中包含多次请求和响应：从浏览器第一次给服务器资源发送请求，会话建立，直到有一方断开为止
- 功能：在一次会话的范围内的多次请求间，共享数据
- 技术：
    - 客户端会话技术：Cookies
    - 服务器端会话技术：Session
    
## Cookies

### 概念：客户端会话技术，将数据保存到客户端

### 使用步骤

- 创建Cookie对象，绑定Cookies
    - new Cookie(String name, String value)
- 服务器发送Cookie对象给客户端
    - void	addCookie(Cookie cookie)
- 客户端浏览器保存cookie在本地，并在下一次http请求的时候添加到请求头中
- 服务器获取客户端发来的本地的Cookie，拿到cookie数据
    - Cookie[]	getCookies()

### 原理：基于响应头中**Set-Cookie**和请求头中的**Cookie**来实现的

- 服务器在发送给客户端的响应头中，设置了set-cookie字段
![](https://raw.githubusercontent.com/hattori7243/img/master/20210303164653.png)

- 浏览器在接收到这个响应之后，将该cookie保存到浏览器中
- 之后浏览器在发送给该服务器的请求http包中，就会携带之前保存的cookie
![](https://raw.githubusercontent.com/hattori7243/img/master/20210303164908.png)


### 细节

- 一次可以发送多个Cookie，创建多个Cookie对象，调用多次response.addCookie方法发送即可。包如下：
![](https://raw.githubusercontent.com/hattori7243/img/master/20210303165450.png)

- Cookie在浏览器中保存多长时间？
    - 默认情况下，浏览器关闭时，保存在内存中的Cookie数据被消除
    - 持久化存储
        - setMaxAge(int seconds)
            - 正数：写到硬盘中，代表cookie的存活时间
            - 负数：就是默认值，存在内存中，浏览器关闭就清除
            - 0:要求客户端删除对应的cookie信息

- cookie在tomcat8之后支持中文数据
- cookie的获取范围
    - 在一个服务器中部署了多个web项目，默认cookie不能共享
    - 若要共享，则需要调用cookie.setPath(String path)设置获取范围。默认情况下设置成当前项目的虚拟目录
- cookie不能直接存储空格
- **cookie的唯一性由键、最大存活时间、可获取范围共同唯一确定**：同样名字的cookie，如果最大存活时间或者获取范围不同，是不同的cookie，会重复存储两个键相同的cookie
    - 例如:name=zhangsan ,age=17,maxage=20,path=/和name=zhangsan,age=18,maxage=19,path=/是不同的cookie。不注意的话，可能会导致多个重复cookie存在。
    - 同样的，设置maxage=0删除cookie的时候，也要设置相同的path，不然没法删除对应的cookie
    
### 特点

- cookie数据存储在客户端浏览器中

- 浏览器对于cookie的大小有限制(例如4kB)，且对于同一个域名下的cookie数量有限制(例如20个以内)

### 作用

- cookie一般用于存储少量的不太敏感的数据
- 在不登陆的情况下，完成服务器对客户端的身份识别

## Session

- 概念：服务器端会话技术，在一次会话的多次请求间共享数据，将数据保存在服务器端的对象中 -- HttpSession


### HttpSession对象

- 共享数据的方法:
    - Object	getAttribute(String name)
    - void	setAttribute(String name, Object value)
    - void	removeAttribute(String name)
    
- 原理:Session是依赖于Cookie来确保获取的session是同一个
    1. 在第一次请求，服务端没有对应session时，会在响应头中设置一个名为JSESSIONID的Cookie，这个就是新建在服务器端的session id
        - ![](https://raw.githubusercontent.com/hattori7243/img/master/20210304103548.png)
    2. 在同一次会话中，后续客户端在发送http请求给服务器的时候，就会在请求头中设置一个JSESSIONID的Cookie，这样服务器收到以后就能根据这个id找到对应的Session。
        - ![](https://raw.githubusercontent.com/hattori7243/img/master/20210304103746.png)
    - 测试过，当关闭所有标签页，浏览器并不退出以后，重新打开网页，Cookie信息还是存在的，服务器端的Session也还存在，能够根据Cookie找到Session。
    - 但是，当完全从内存中退出浏览器的时候，重新打开网页，Cookie信息默认被删除了，请求头中没有关于sessionid的cookie信息。
    
### 细节

- 当客户端关闭后，服务器不关闭，两次获取的session是同一个吗？
    - 默认情况下不是同一个，因为Cookie的默认存活时间是浏览器关闭。
    - 若希望是同一个，则可以设置JSESSIONID的Cookie的存活时间。
    - 另外，如果客户端并没有按照服务器发送Cookie的约定将Cookie删除，而是保存下来，继续请求，依然能够请求到该Session。这也就说明了，**Cookie和Session的生存时间是独立的，如果严格来说，并不能通过Cookie的保存时间来限制Session的保存时间，应该主动设置Session的有效期，其中tomcat默认的时间是30分钟**
        - ![](https://raw.githubusercontent.com/hattori7243/img/master/20210304111656.png)
        - ![](https://raw.githubusercontent.com/hattori7243/img/master/20210304111720.png)

- 客户端不关闭，服务器关闭后，两次获取的session是同一个吗？
    - 不是同一个，但是要确保数据不丢失
    - Session的钝化：在服务器正常关闭之前，将session对象序列化到硬盘上
    - Session的活化：在服务器启动之前，将Session在硬盘中的文件转化为内存中的session对象即可
    - 这两部分tomcat自动做了，即虽然session的地址肯定变了，但是数据并没有变。也就是说，对客户端来说，是感知不到钝化活化的过程的
    
- session的失效时间
    1. 服务器关闭
    2. session对象自己调用自杀方法: session.invalidate()
    3. session默认失效时间是30分钟
        - 配置tomcat/conf/web.xml
        - ![](https://raw.githubusercontent.com/hattori7243/img/master/20210304112722.png)

### 特点

- 用于存储一次会话的多次请求的数据，存在服务器端
- session可以存储任意类型、大小的数据
- Session与Cookie的区别：
    - Session存在服务器端，Cookie存在客户端
    = Session没有数据大小限制，Cookie有



# JSP: Java Server Pages

- 概念：即html和java代码混合。目的是用于简化书写


### 本质上就是servlet
- 会自动生成java代码、java class文件，然后生成一个servelet来提供服务。至于html元素则会自动调用servlet中的写html的方法。

### JSP脚本

- **用<% %>括起来**：将来会被放在**service方法**中
- 用<%! %>括起来：将来的java类中的成员变量、成员方法。用得很少
- 用<%= %>括起来：将来也是放在service方法中，里面的东西会被直接打印
- <%--  --%>：注释

### 指令

- 用于配置jsp页面，导入资源文件
- < %@ 指令名称 属性名1=属性值1 属性名2=属性值2 ...% >
    - page指令：配置JSP页面的
        - contentType：等同于response.setContenType()
        - import:用来导java包
        - errorpage:当前页面发生异常后，会自动跳转到哪个页面
        - isErrorPage：标示当前页面是不是错误页面。标示是错误页面之后，就可以用内置对象exception来对错误信息进行操作。
    - include指令：导入其他页面的资源文件
    - taglib指令：导入资源
        - 《 %@ taglib prefix="xx" uri="xxx" %>
        - prefix：定义的是前缀
        

### JSP的内置对象：在JSP页面中不需要获取，就可以直接用


- 域对象：共享数据
    - 1.pageContext：当前页面共享数据，还可以用来获取其他8个内置对象
    - 2.request：单次请求共享数据（包括转发）
    - 3.session：一次会话的多个请求间
    - 4.application：所有用户间共享数据

- 5.response
- 6.out：字符输出流，可以将数据输出到页面上。response.getWriter()会先于out输出
- 7.config:Servlet的配置对象
- 8.page:当前页面，类似于this

- 9.exception:只有在被配置成错误页面的时候才可以调用，用来处理错误信息

## EL表达式

- Expression Language 表达式语言
- 用来替换和简化jsp中java代码的编写,jsp默认支持el
- 语法 &dollar; { }
- 若要忽略el表达式，可以在&dollar;前加转义符\

### 使用

- 运算符
    - 常见运算符+ - * / 等
    - 空运算符:empty：用于判断字符串、集合、数组对象是否为null并且长度是否为0.:&dollar;{empty list}
    
- 获取值：只能从域对象中获取值
    1. &dollar;{域名城.键名称}：从指定域中获取指定键的值
    2. &dollar;{键名称}：表示以作用范围从小到大查找该键
    3. 获取对象：只能通过获取属性的方式来取得内部数据
        - &dollar;{域名城.键名.属性名}：会自动去调用该属性的getter方法
    4. 获取list集合&dollar;{域名城.键名[索引]}
    5. 获取map集合&dollar;{域名城.键名[key]}
    
    
## JSTL

- JavaServer Pages Tag Library：JSP标准标签库（Apache组织提供，开源免费）
- 用于简化和替换jsp页面上的java代码

### 使用：

1. 导入jstl相关jar包
2. 引入标签库 taglib指令
3. 使用标签
   
### 常用标签
- if：相当于if语句:< c:if test="xxx" > yyy < /c:if >。若test的值为真，则执行标签体内容
- choose：相当于switch语句
- foreach：相当于for循环
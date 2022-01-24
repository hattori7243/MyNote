# 常用软件、开发模式

### Maven

- 为什么需要？解决哪些痛点

    1. jar包难以寻找
    2. jar包依赖的问题
    3. jar包版本冲突问题
    4. jar包管理的问题
    5. 项目结构混乱
    6. 项目的生命周期控制方式不同

- 亮点

    - 统一整个项目的结构，便于阅读项目结构，利于上手
    - 给每个jar包定义了唯一的标志，在maven中叫做项目的坐标
    - 自动解决jar包依赖的问题
    - 以插件的形式提供maven命令来更好的控制开发周期
    ![](https://raw.githubusercontent.com/hattori7243/img/master/20210304203929.png)

- 约定的项目结构

    ![](https://raw.githubusercontent.com/hattori7243/img/master/20210304205629.png)

- Maven的坐标:通过groupId、artifactId、version、packaging、classifier一起定义

- Maven通过pom.xml文件中的dependencies元素来导入依赖的构件
    - 其中< groupId > < artifactId >是必须的，用来确定唯一的依赖
    - 可以通过< scope >来确定该依赖的作用范围

- Maven依赖冲突问题
    - 路径最近原则
    - 最先声明原则

- maven采用引用的方式将依赖的jar引入进来，不对真实的jar进行拷贝，但是打包的时候，运行需要用到的jar都会被拷贝到安装包中

- maven搜索构件:https://search.maven.org/ ，下载构件地址

### Tomcat

- 其他知识
    - 资源分类
        - 静态资源：所有用户访问得到的是一样的资源。如html、css、js
        - 动态资源：每个用户访问得到的是不一样的资源，如servlet、jsp、php、asp。返回给用户时是先变成静态资源，在返回给浏览器的解析引擎。

    - javaEE：java语言在企业级开发中使用的技术规范的综合，一共规定了13项大的规范。Tomcat定位为中小型的开源免费服务器软件，仅支持部分的javaEE规范
    - java动态项目的目录结构
        - 项目根目录
            - WEB-INF目录
                - web.xml：web项目的核心配置文件
                - classes：放置字节码文件的目录
                - libs：放置依赖的jar包

- 部署方式

    1. 直接部署在webapps目下即可。通过相对于webapps的目录访问
    2. 配置conf/server.xml文件配置虚拟目录访问 < Context docase="路径" path="虚拟路径">
    3. 在conf/catalina/localhost创建任意名称的xml文件，文件中填写< Context docase="路径" >，path不用写，文件名即为访问的别名。ps. **特别注意大小写！**，这种方法，没法创建在默认目录下面的项目的别名，会报warning，在log里可以看见。

- 将Tomcat集成到idea中

    - run选项卡里面的edit configuration配置tomcat
    - idea会为每一个tomcat工程创建一份配置文件，文件位置可以在启动日志里看到
    - idea会存储两份项目：一个是工作空间的项目，一份是给tomcat**实际部署**的项目（这一份才是tomcat真正访问的）。
        - 同时工作空间里的java文件，会编译成字节码文件后放在实际部署的项目中
        - WEB-INF中的文件不能被浏览器直接访问
        
### MVC开发模式

- M:Model 模型--V:View 视图--C:Controller 控制器
![](https://raw.githubusercontent.com/hattori7243/img/master/20210304153540.png)

- M：Model 模型

    - 完成具体的业务操作：如查询数据库，封装对象等
    - 可以用javaBean完成

- V：View 视图

    - 展示数据
    - 可以用jsp完成

- C：Controller 控制器

    - 获取用户输入
    - 调用模型
    - 将数据交给视图进行展示
    - 可以用Servlet完成

- 优缺点

    - 优点
        - 耦合性低，方便维护，利于分工协作
        - 重用性高
    - 缺点
        - 不适合小型项目，复杂度高


### 三层架构：软件设计架构

- 界面层（表示层/web层）
    - 用户看的界面，用户通过界面上的组件和服务器进行交互

- 业务逻辑层（service层）
    - 处理业务逻辑的。例如组合DAO层中的简单方法，形成复杂的功能、业务逻辑操作

- 数据访问层（dao层:data access object）
    - 操作数据存储文件
![](https://raw.githubusercontent.com/hattori7243/img/master/20210304164832.png)
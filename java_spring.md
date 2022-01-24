# 1. Spring

## 1.1 Spring 是什么

Spring时分层的轻量级开源框架，以**IoC**和**AOP**为内核

### 1.1.1 Ioc: 控制反转 Inverse Of Control

引入第三方（例如Spring框架）来解除对象之间的耦合关系，当对象A运行到需要对象B的时候，IOC容器会主动创建一个对象B注入到对象A需要的地方。而不是类似于之前需要对象A自己创建对象B并且管理。因此，对象A获得依赖对象B的过程由主动变成了被动，控制权发生了颠倒，也就是控制反转。也就是获得依赖对象的过程由自身管理变成了接受IoC容器的注入。**通过引入IOC容器，利用依赖关系注入的方式，实现对象之间的解耦**。

- https://blog.csdn.net/m13666368773/article/details/7802126

### 1.1.2 AOP: 面向切面编程 Aspect Oriented Programming

 - 通过预编译方式和**运行期**通过**动态代理**实现程序功能的统一维护的一种技术
   	- 动态代理：可以在不修改源码的情况下对方法进行功能增强
	- 利用AOP可以对业务逻辑的各个部分进行隔离，使业务逻辑各部分之间的耦合度降低，提高程序的可重用性，同时提高了开发的效率。
 - 相关概念
   	- Target：目标对象，代理的目标对象
   	- Proxy：代理对象，动态代理后产生的结果代理类
   	- Joinpoint：连接点，被拦截到的点，即Spring进行AOP后可以被拦截增强的方法
   	- Pointcut：切入点，就是指我们需要去对那些Joinpoint进行拦截的定义。即Joinpoint时可以进行拦截的，Pointcut是实际拦截的。
   	- Advice：通知/增强，就是指拦截到Joinpoint之后所做的事情
   	- Aspect：切面，就是切入点和通知的结合
   	- Weaving：织入，是把增强应用到目标对象来创建新的代理对象的过程，Spring采用动态代理织入
- Spring框架监控切入点方法的执行，一旦监控到切入点方法被运行，使用代理机制，同台创建目标对象的代理对象，根据通知类别，在代理对象的对应位置，将通知对应的功能织入，完成完整的代码逻辑运行。

#### 1.1.2.1 AOP的动态代理技术

- JDK代理：基于接口的动态代理技术

- cglib代码：基于父类的动态代理技术

- Spring中会根据目标类是否实现了接口来决定采用哪种动态代理的方式

  ![](https://raw.githubusercontent.com/hattori7243/img/master/20210310210506.png)

## 1.2 Spring的优势

1. 方便解耦，简化开发

   通过spring提供的Ioc容器，将依赖关系交由Spring进行控制

2. AOP编程的支持

3. 声明式事务的支持

4. 方便程序的测试

5. 方便集成各种优秀的框架

6. 降低javaEE API的使用难度

7. Java源码经典学习典范

## 1.3 Spring体系结构

<img src="https://raw.githubusercontent.com/hattori7243/img/master/20210305170330.png" style="zoom:50%;" />

# 2. Spring快速入门

## 2.1 Spring开发步骤

1. 导入Spring的包(Maven坐标)

2. 编写接口和实现类

3. 创建Spring核心配置文件xml

4. 在配置文件中配置类和序号

   ![](https://raw.githubusercontent.com/hattori7243/img/master/20210305172736.png)

5. 通过Spring的API来获得Bean实例，完成解耦

   ![](https://raw.githubusercontent.com/hattori7243/img/master/20210305172801.png)

## 2.2 Spring配置文件

### 2.2.1 Bean标签的基本配置

- id：唯一性标识

- class：对应类的全限定名，必须保证存在无参构造

- scope：指对象的作用范围，默认是singletong，单例

  - **singleton**：单例，当**加载配置文件时**就直接调用构造函数创建对象了
  - **prototype**：多例，只有当**实际执行getbean创建对象语句**的时候才调用构造函数创建对象
  - request：WEB项目中，会放一份到request域中
  - session：WEB项目中，会放一份到session域中 

- <img src="https://raw.githubusercontent.com/hattori7243/img/master/20210308211337.png" style="zoom:50%;" />

  

### 2.2.2 Bean生命周期配置

- init-method：指定init方法
- destroy-method：指定destroy方法

### 2.2.3 Bean实例化实例化三种方式

- 无参构造方法实例化
- 工厂**静态**方法实例化
- 工厂**实例**方法实例化

### 2.2.4 Bean的依赖注入

依赖注入：是Spring框架核心IoC的具体实现

- set方法注入
  -  <img src="/Users/zhangshuyu/Library/Application Support/typora-user-images/image-20210308214954749.png" alt="image-20210308214954749" style="zoom:80%;" />
  - name是指在上层的属性名，ref指的实际注入的类

- 构造方法注入
  - <img src="https://raw.githubusercontent.com/hattori7243/img/master/20210308215715.png" style="zoom:80%;" />
  - name是指在上层的属性名，ref指的实际注入的类

### 2.2.5 Bean的依赖注入的数据类型

- 普通数据类型

- 引用数据类型

- 集合数据类型 

  ![image-20210308221320737](/Users/zhangshuyu/Library/Application Support/typora-user-images/image-20210308221320737.png)

  

# 3. Spring注解开发

## 3.1 Spring旧注解

![](https://raw.githubusercontent.com/hattori7243/img/master/20210309203509.png)

- 使用注解开发时，需要在application.xml文件中配置组建扫描，作用是指定哪个包及其子包下的Bean需要进行扫描。
- ![](https://raw.githubusercontent.com/hattori7243/img/master/20210309202824.png)

## 3.2 Spring新注解

针对旧注解不能使用注解配置的情况：

- 非自定义的Bean的配置
- 加载properties文件的配置
- 组件扫描的配置
- import其他文件的配置

![](https://raw.githubusercontent.com/hattori7243/img/master/20210309203841.png)

# 4. Spring集成Junit

![](https://raw.githubusercontent.com/hattori7243/img/master/20210309210927.png)

- 第一步不是导入JUnit，而是导入spring-test的这个包。当然JUnit也要导。
- 第三步可以指定配置文件也可以指定注解开发的类，指定类的时候注解中配置classes=""

# 5. AOP配置

## 5.1 xml配置

### 5.1.1 切点表达式

![](https://raw.githubusercontent.com/hattori7243/img/master/20210312193305.png)

- 切点表达式的抽取: 先定义<aop:pointcut id=xxx expression="xxx"/>, 然后在下面pointcut-ref=id即可

### 5.1.2 通知类型

![](https://raw.githubusercontent.com/hattori7243/img/master/20210312193526.png)

## 5.2 注解方式实现

![](https://raw.githubusercontent.com/hattori7243/img/master/20210312200541.png)

- 通知类型注解:@Before @AfterReturning @Around @AfterThrowing @After

- 切面表达式的抽取：在切面内定义一个空方法，然后在上面用@Pointcut定义切点表达式，然后通过方法名作为id引用。
  - <img src="https://raw.githubusercontent.com/hattori7243/img/master/20210312201123.png" style="zoom:67%;" />
- 要点：
  - 使用@Aspect标注切面类
  - 使用 @通知注解 标注通知方法
  - **在配置文件中配置注解扫描范围和aop自动代理< aop:aspectj-autoproxy />**




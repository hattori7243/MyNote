# 1.什么是RPC？

- RPC：全称Remote Procedure Call，远程过程调用。
- 需要RPC这么一个轮子的原因：即通过网络来调用另一个服务器上能提供的方法，如果每次都需要通过自己来网络编程的话工作量大，需要考虑底层传输方式，序列化等方面。换句话说，RPC框架就是实现了网络编程的工作，让**调用远程方法像调用本地方法一样简单**。

# 2.RPC的原理

主要核心功能由以下6个部分实现：

1. **客户端（服务消费端）**：也就是主动去调用远程方法的一端
2. **客户端stub（桩）**：代理类，将调用的方法、类、方法参数等信息包装然后通过网路传递，等于是一个打包的服务点。
3. **网络传输**：就是负责将调用信息从客户端传递到服务端，以及将返回结果从服务端传到客户端。实现方式有很多种，比如Socket以及Netty。
4. **服务端Stub（桩）**：接收到客户端传来的请求后，处理后传递给服务端，也是中转性质。
5. **服务端（服务提供短）**：提供远程方法给客户端调用的一端

具体调用过程：

![](https://raw.githubusercontent.com/hattori7243/img/master/20210521142200.png)

<img src="https://raw.githubusercontent.com/hattori7243/img/master/20210521142258.png" style="zoom:50%;" />

### 常见RPC框架

- Dubbo
  - 阿里开源
  - 主要支持语言java
  - 核心能力
    - 面向接口的远程方法调用
    - 智能容错和负载均衡
    - 服务自动注册和发现

- Motan
  - 新浪开源

- gRPC
  - 谷歌开源，支持众多开发语言

- Thrift
  - Fackbook开源，支持众多编程语言，比gRPC更多（C++，Java，python，php，ruby等）

# 3. 如何自己实现一个RPC框架？

首先需要有一个注册中心，服务提供端先向注册中心注册服务，然后服务消费者从注册中心拿到服务相关的信息后，才能知道我能通过RPC框架来调用哪些服务，然后再通过网络请求调用服务端的服务。

简化图如下：

![](https://raw.githubusercontent.com/hattori7243/img/master/20210521143322.png)

**而具体一点如下：**

![image.png](https://cdn.nlark.com/yuque/0/2020/png/738439/1606120412129-a4f994c0-4d8d-434b-bbaf-bf181370514e.png)





## 1. 注册中心

- 注册中心其实就是负责保存服务地址，即提供一个目录服务。服务端将所能提供的服务名称和对应的地址（ip+port）注册到注册中心，消费端根据自己想要调用的服务名称找到对应的服务地址（可能有很多个），然后选择服务地址，消费端就可以通过该地址利用网络请求请求服务端。





![](https://raw.githubusercontent.com/hattori7243/img/master/20210521144145.png)

**顺序如下：**

1. 服务容器负责启动，加载，运行服务提供者
2. 服务提供者启动时，将自己所能提供的服务注册到注册中心
3. 客户端通启动时，向注册中订阅自己所需要的服务。
4. 客户端从注册中心拿到服务提供者的地址列表。
5. 客户端，从提供服务的地址列表中，基于负载均衡的算法，选择一个进行调用，失败的话，再选另一个调用
6. 服务提供者和消费者都会记录下自己调用或者提供服务的次数，并更新到监控中心。

## 2. 网络传输

- 因为我们要调用远程的方法，就需要发送网络请求来传递目标类和方法信息以及方法的参数等数据到服务提供者。而这就需要通过网络传输。

使用选择：

- Socket：Java中最原始、最基础的网络通信方式，阻塞式IO（BIO）
- NIO：非阻塞式IO，但是网络编程太麻烦了
- 基于NIO的网络编程框架Netty：极大简化了TCP和UDP套接字服务器网络编程，并且支持多种协议比如ftp、SMTP、http以及各种二进制和基于文本的传统协议

## 3. 序列化和反序列化

因为网络中传输的数据都是以二进制的形式存在的，java对象没办法直接在网络中传输。因此，我们需要将java对象以一种可逆的方式转换为二进制的数据，并最终再将该二进制数据还原回java对象。这就是**序列化和反序列化**

jdk自带的序列化只需要实现java.io.Serializable接口即可，不过因为不支持跨语言调用并且性能较差，不推荐

比较常见的序列化方式有**hessian、kyro、protostuff**

## 4. 动态代理

代理模式就是我们为某一个对象提供一个代理对象，并且由代理对象代替真实对象做一些事情。对于外界来说，他只需要知道有代理对象的存在即可。

而我们在RPC框架中，我们是远程调用方法时，如果能找一个代理，那我们就并不需要关心实际时怎么实现的，我们只需要找到该代理提出我们的需求即可。

- 也就是说，运用代理模式，我们可以屏蔽远程方法调用的细节，通过代理对象来传输网络请求。

## 5. 负载均衡

避免单个服务器响应大量请求，造成服务器宕机、崩溃等问题。

## 6. 传输协议

需要设计一个私有的RPC协议，协议是客户端和服务端交流的基础。

简单来说：**通过设计协议，我们定义需要传输哪些类型的数据，以及每一种类型的数据占多大。这样规定好了以后，才能正确的解析出我们需要的数据**

通常一个标准的RPC协议包含以下内容：

- **魔数**：通常是4个字节，可自定义，主要是为了筛选出来到服务器端的数据包是否是遵循我们的协议，是不是无效数据包。

- **序列化器编号**：标志序列化的方式，是采用哪一种序列化的方式，是java自带的序列化还是json、kyro序列化等方式

- **消息体长度**：运行时计算出来

- 。。。。。。

# 4. 序列化

## JDK自带的序列化

只需要实现java.io.Serializable接口即可

值得注意的是serivalVersionUID，在反序列化时会检查是否一致，用来进行版本判断。如果不设置的话，编译器会动态生成默认的序列化号。

- 不支持跨语言调用
- 性能差，序列化后字节数组体积大，传输成本加大
- 有漏洞

## kyro

高性能的序列化/反序列化工具，由于其变长存储特性并且使用了字节码生成机制，拥有较高的运行速度和较小的字节码体积

但是只支持java，也不支持跨语言调用

## hession

hession是一个轻量级的，自定义描述的二进制RPC协议。hessian是一个比较老的序列化实现了，并且同样也是跨语言的。

dubbo RPC中默认启用的序列化方式是hessian2，但是dubbo对hessian2进行了修改，不过大体结构还是差不多的

# 5. Socket网络编程实战

Socket：套接字：是ip地址和端口的组合。通过互联网进行通信，至少需要一对套接字：服务端的Server Socket和客户端的Server Socket

Java中使用Socket常用的两个类，在`java.net`包中

1. `Socket`：一般用于客户端
2. `ServerSocket`：用于服务端

**Socket网络通信过程如下：**

<img src="https://raw.githubusercontent.com/hattori7243/img/master/20210524150434.png" style="zoom:150%;" />

即简而言之就是服务端绑定自己的套接字，然后监听客户端请求。然后客户端请求建立连接。建立连接后，通过输入输出流发送接收响应消息通信。在客户端关闭后，都关闭相关资源。

# 6. Netty

**Netty是一个基于NIO的client-server框架，使用它可以快速简单的开发网络应用程序**

极大的简化了TCP和UDP套接字服务器等网络编程，并且在性能以及安全性等很多方面甚至都要更好

支持多种协议如ftp、smtp、http以及各种二进制和基于文本的传统协议

**Netty成功找到了一种在不妥协可维护性和性能的情况下实现易于开发、性能、稳定性和灵活性的方法**

## 利用netty和kryo实现序列化传输对象

自顶向下来看：

先是一个利用netty来进行传输的实体类`NettyClient`和`NettyServer`，将通过channel向服务端/客户端发送消息

Handler类：将客户端、服务端接收到的数据保存在channel中，实现io多路复用

然后是自定义的编码器、解码器，调用序列化接口将对象变为字节数组

序列化接口，利用kryo将对象转变为字节数组

#### 客户端发送/接收消息：

**发送消息**

首先对于rpcRequest对象，在发送时，由于之前在netty中注册过该对象的编码器。

会自动调用该对象的编码器，而该编码器会自动调用endoce方法（因为继承了netty中的接口），在该encode方法中会调用序列化接口。

序列化接口是用kryo实现的，该接口会将对象变为字节数组。同时会先在前4个字节中写明消息的长度，便于查验。

然后发送出去。阻塞等待回复消息

**接收消息**

在接收到返回的消息时，先通过注册好的解码器将收到的消息解码成对象

然后在通过channelHandler将该对象放到AttributeMap 上，AttributeMap 可以看作是一个Channel的共享数据源

#### 服务端接收/发送消息：

**接收/发送消息**

在接收到客户端发来的消息时，先通过注册好的解码器将收到的消息解码成对象

然后在通过channel将回复该对象放到channel 上发送出去

#### Netty中的channel

channel是一个管道,用于连接字节缓冲区Buf和另一端的实体,这个实例可以是Socket,也可以是File, 在Nio网络编程模型中, 服务端和客户端进行IO数据交互(得到彼此推送的信息)的媒介就是Channel

- `Channel`中的所有Io操作都是异步的,一经调用就马上返回,于是Netty基于Jdk原生的`Future`进行了封装, `ChannelFuture`, 读写操作会返回这个对象,实现自动通知IO操作已完成

# 7. 代理模式

代理模式的主要作用是**扩展目标对象的功能**

可以分为静态代理和动态代理

### 静态代理

在编译前就将接口、实现类、代理类这些都变成了实际的字节码文件。非常不灵活，每次增强都需要手动编码完成，应用很少

### 动态代理

不需要针对每个目标类单独创建代理类。**从jvm角度，动态代理是在运行时动态生成类的字节码并且加载到jvm中。**

可以分为两类：

#### 1. JDK动态代理（面向接口）

需要被代理的类实现了某个接口，也就是根据该接口来实现动态代理。

**重点在于定义一个继承`InvocationHandler`	的类并重写`invoke`方法**

**使用步骤：**

- 定义一个接口以及他的实现类
- 自定义继承`InvocationHandler`的类并重写`invoke`方法，在`invoke`方法中调用原生方法（被代理类的方法），并自定义一些增强的逻辑
- 通过`Proxy.newProxyInstance(ClassLoader loader, Class<?>[] interfaces, InvocationHandler h)`方法动态创建代理对象p
- 通过直接调用代理对象p实现接口中的方法即可完成对被代理对象的调用

#### 2. CGLIB动态代理

JDK动态代理实现的前提条件是只能代理实现了接口的类

CGLIB则没有这个限制，他通过继承的方式实现代理。

Spring中AOP模块，如果目标对象实现了接口，则默认采用JDK代理，否则采用CGLIB代理

**重点是自定义`MethodInterceptor`并重写`intercept`方法，`intercept`相当于JDK代理中的`invoke`方法，用于拦截并且增强被代理类的方法。**

**使用步骤：**

- 定义一个需要被代理的类
- 定义一个实现`cglib`的`MethodInterceptor`接口的类，并重写`intercept`方法，在该方法中调用`methodProxy.invokeSuper`，增强被代理类的方法
- 创建一个`cglib`中的`Enhancer`类，`setClassLoader`设置类加载器，`setSuperclass`设置被代理类，`setClallback`设置方法拦截器即之前定义过的实现了`MethodInterceptor`接口的类。
- 然后调用enhancer.create()创建一个代理对象，通过代理对象访问即可

**需要特别注意的是，`MethodInterceptor`接口重写`intercept`方法中，调用被代理类的方式必须是`methodProxy.invokeSuper`，不然会造成死循环，也就是一直代理一直代理，永远走不到父类。**

因为cglib是通过生成一个被代理类的子类来拦截方法调用，因此不能代理声明为final的类和方法，因为final声明的类不能被继承，final声明的方法不能被重写。

同时，cglib的性能也比JDK代理差，但胜在不需要接口也能实现动态代理

# 8. ZooKeeper

进入bin目录，`./zkCli.sh -server 127.0.0.1:2181`连接zookeeper

## 数据模型

ZooKeeper 数据模型采用层次化的多叉树形结构，每个节点上都可以存储数据，这些数据可以是数字、字符串或者是二级制序列。并且。每个节点还可以拥有 N 个子节点，最上层是根节点以“/”来代表。每个数据节点在 ZooKeeper 中被称为 **znode**，它是 ZooKeeper 中数据的最小单元。并且，每个 znode 都一个唯一的路径标识。

**ZooKeeper 主要是用来协调服务的，而不是用来存储业务数据的，所以不要放比较大的数据在 znode 上，ZooKeeper 给出的上限是每个结点的数据大小最大是 1M**

![](https://raw.githubusercontent.com/hattori7243/img/master/20210527143530.png)

## znode（数据节点）

**是zookeeper中数据的最小单元**

### 4种类型

- **持久节点（persistent）**：一旦创建就一直存在，哪怕宕机也不主动删除。直到操作手动删除
- **临时节点（ephemeral）**：和**客户端会话（sessoin）**绑定，**会话消失则节点消失**。因此，只能做叶子节点，不能创建子节点
- 持久顺序节点（persistent_sequential）：和持久节点一样，只不过名称具有顺序性
- 临时顺序节点（ephemeral_sequential）：和临时节点一样，只不过名称具有顺序性

### 数据结构

- 每个znode由两部分组成：**stat状态信息**和**data节点存放的数据的具体内容**
- 用get来获取data，用stat命令来获取状态信息
- 状态信息的解释:
- <img src="https://raw.githubusercontent.com/hattori7243/img/master/20210527144058.png" style="zoom:50%;" />

**状态信息中主要维护了三个版本**

- dataVersion：当前znode节点的版本号，当前内容数据变化时+1
- cversion：子节点的版本，自节点变化时+1
- aclVersion：ACL版本号，表示ACL信息变化次数
  - ACL（AccessControlLists）是用来进行权限控制的

## Watcher（事件监听器）

允许用户在指定节点上注册一些Watcher，并在一些特定事件触发的时候，Zookeeper服务端会将事件通知到感兴趣的客户端上去，**该机制是ZooKeeper实现分布式协调服务的重要特性**

## Seession（会话）

Session可以看作是ZooKeeper服务器和客户端的一个TCP长连接，通过这个链接，客户端通过心跳检测与服务器保持有效的会话，也能够向服务器发送请求并接受相应，还能够通过该链接接受来自服务器的Watcher事件通知

sessionTimeout：当掉线了，如果在一定时间内崇廉回来，会话仍然有效

sessionID：唯一标示该会话

## 集群

为了保证高可用，最好以集群形态来部署Zookeeper

- 集群最好是奇数台
  - 因为集群可用的情况是剩下的服务器个数比宕机的服务器个数多。所以2n-1和2n能容忍出现宕机的服务器数量是一样的，所以采用奇数能减少服务器成本

## ZooKeeper Java 客户端 Curator

- 连接ZooKeeper

```java
RetryPolicy retryPolicy = new ExponentialBackoffRetry(BASE_SLEEP_TIME, MAX_RETRIES);

CuratorFramework zkClient = CuratorFrameworkFactory.builder()
  // the server to connect to (can be a server list)
  .connectString("127.0.0.1:2181")
  .retryPolicy(retryPolicy)
  .build();

zkClient.start();
```

- 增加节点

```java
zkClient.create().forPath("/node1/00001");
//给定创建何种类型的节点
zkClient.create().withMode(CreateMode.PERSISTENT).forPath("/node1/00002");
//如果给定的父节点不存在，自动创建
zkClient.create().creatingParentsIfNeeded().withMode(CreateMode.PERSISTENT).forPath("/node1/00001");
//给定节点包含的数据内容
zkClient.create().creatingParentsIfNeeded().withMode(CreateMode.EPHEMERAL).forPath("/node1/00001","java".getBytes());
//检查节点是否创建成功
zkClient.checkExists().forPath("/node1/00001");//不为null的话，说明节点创建成功
```

- 删除节点

```java
zkClient.delete().forPath("/node1/00001");
//删除节点及其子节点
zkClient.delete().deletingChildrenIfNeeded().forPath("/node1");
```

- 获取/设置节点数据内容

```java
//返回值是一个bytes数组
zkClient.getData().forPath("/node1/00001");//获取节点的数据内容
zkClient.setData().forPath("/node1/00001","c++".getBytes());//更新节点数据内容
```

- 获取某个节点的所有子节点路径

```java
//只会获取一层
List<String> childrenPaths = zkClient.getChildren().forPath("/node1");
```

### 监听器

注册监听器

```java
zkClient.start();
String path = "/node1";
PathChildrenCache pathChildrenCache = new PathChildrenCache(zkClient, path, true);
PathChildrenCacheListener pathChildrenCacheListener = (curatorFramework, pathChildrenCacheEvent) -> {
  if(pathChildrenCacheEvent.getType()== PathChildrenCacheEvent.Type.CHILD_UPDATED){
    zkClient.create().forPath("/test");
  }
};
pathChildrenCache.getListenable().addListener(pathChildrenCacheListener);
pathChildrenCache.start();
```

获取子节点数据类型，以及都有哪些类型

```java
pathChildrenCacheEvent.getType();

public static enum Type {
  CHILD_ADDED,//子节点增加
  CHILD_UPDATED,//子节点更新
  CHILD_REMOVED,//子节点被删除
  CONNECTION_SUSPENDED,
  CONNECTION_RECONNECTED,
  CONNECTION_LOST,
  INITIALIZED;

  private Type() {
  }
}
```

## 在rpc框架中

服务端启动时将服务名称和对应的地址（ip+端口号）注册到注册中。客户端根据服务名称找到对应的服务地址，然后通过网络请求服务。

注册中心就是一个中转站，类似于114，提供的作用是根据名称找到地址。






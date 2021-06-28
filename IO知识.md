# 四种IO模型

## 1. 同步阻塞IO（Blocking IO）

在 Java 应用程序进程中，默认情况下，所有的 socket 连接的 IO 操作都是同步阻塞 IO（Blocking IO）。

![](https://raw.githubusercontent.com/hattori7243/img/master/20210616154842.png)

举个例子，在 Java 中发起一个 socket 的 read 读操作的系统调用，流程大致如下：

（1）从 Java 启动 IO 读的 read 系统调用开始，用户线程就进入阻塞状态。

（2）当系统内核收到 read 系统调用，就开始准备数据。一开始，数据可能还没有到达内核缓 冲区（例如，还没有收到一个完整的 socket 数据包），这个时候内核就要等待。

（3）内核一直等到完整的数据到达，就会将数据从内核缓冲区复制到用户缓冲区（用户空间 的内存），然后内核返回结果（例如返回复制到用户缓冲区中的字节数）。

（4）直到内核返回后，用户线程才会解除阻塞的状态，重新运行起来。

总之，阻塞 IO 的特点是：在内核进行 IO 执行的**两个阶段**（等待数据和复制数据），用户线程都被阻塞了。

- 优点：开发简单。在阻塞等待阶段，线程被挂起，不会占用CPU资源
- 缺点：因为线程被阻塞，所以会为每个连接配备一个独立的线程，**用一个线程来维护一个连接的IO操作。**因此，在高并发的情况下，需要大量的线程来维护大量的网络连接，这样**内存、线程切换开销会十分巨大**，因此阻塞IO模型在高并发的场景下一般不可用

## 2. 同步非阻塞IO（Non-Blocking IO）

在非阻塞IO中，在开始IO系统调用后，会出现以下两种情况：

（1）在内核缓冲区中没有数据的情况下，系统调用会立即返回，返回一个调用失败的信息。

（2）在内核缓冲区中有数据的情况下，是阻塞的，直到数据从内核缓冲复制到用户进程缓冲。 复制完成后，系统调用返回成功，应用进程开始处理用户空间的缓存数据。

![image-20210616155342524](/Users/zhangshuyu/Library/Application Support/typora-user-images/image-20210616155342524.png)

可以看到，如果系统调用时处于还在等待数据阶段，是会直接返回的。此时不会阻塞。但是在数据准备好之后，复制数据时还是会阻塞。

也就是说，和BIO相比，非阻塞指的是等待数据准备好的过程中不会阻塞，而是立即返回没准备好的消息，在复制数据时同样还是需要阻塞。

- 优点：每次发起的 IO 系统调用，在内核等待数据过程中可以立即返回。用 户线程不会阻塞，实时性较好。
- 缺点：不断地轮询内核，这将占用大量的 CPU 时间，效率低下

**特别说明**：同步非阻塞 IO，可以简称为 NIO，但是，它**不是** Java 中的 NIO，虽然它们的 英文缩写一样，希望大家不要混淆。Java 的 NIO（New IO），对应的不是NIO（None Blocking IO）模型，而是 IO 多路复用模型（ IO Multiplexing）。

## 3. IO多路复用模型（IO Multiplexing）

为了避免同步非阻塞IO模型中频繁的轮询等待的问题，引入了一种新的系统调用，来查询IO的就绪状态。

在linux中，对应的系统调用为select/epoll系统调用。该系统调用可以通过一个进程监视多个文件描述符，一旦其中的某个就绪，就能将就绪的状态返回给应用程序。

![](https://raw.githubusercontent.com/hattori7243/img/master/20210616160025.png)

举个例子来说明 流程如下：IO多路复用模型的流程。发起一个多路复用IO的read读操作的系统调用，

（1）选择器注册。在这种模式中，首先，将需要 read 操作的目标 socket 网络连接，提前注册 到 select/epoll 选择器中，Java 中对应的选择器类是 Selector 类。然后，才可以开启整个 IO 多路复 用模型的轮询流程。

（2）就绪状态的轮询。通过选择器的查询方法，查询注册过的所有 socket 连接的就绪状态。 通过查询的系统调用，内核会返回一个就绪的 socket 列表。当任何一个注册过的 socket 中的数据 准备好了，内核缓冲区有数据（就绪）了，内核就将该 socket 加入到就绪的列表中。

**当用户进程调用了select查询方法，那么整个线程会被阻塞掉。**

（3）用户线程获得了就绪状态的列表后，根据其中的 socket 连接，发起 read 系统调用，用户 线程阻塞。内核开始复制数据，将数据从内核缓冲区复制到用户缓冲区。

（4）复制完成后，内核返回结果，用户线程才会解除阻塞的状态，用户线程读取到了数据， 继续执行。

- 需要注意的是，IO多路复用中，两种系统调用select和read都会阻塞。
- 对于注册在选择器上的每一个可以查询的连接，一般都设置成为同步非阻塞模型
- **Java 语言的 NIO（New IO）技术，使用的就是 IO 多路复用模型。在 Linux 系统上，使用的是 epoll 系统调用。**
- 优点：与一个线程维护一个连接的同步阻塞 IO 模式相比，使用 select/epoll 的 最大优势在于，一个选择器查询线程可以同时处理成千上万个连接。系统不必创建大量的线程，也不必维护这些线程，从而大大减小了系统的开销。
- 缺点：本质上，select/epoll 系统调用是阻塞式的，属于同步 IO。都需要在 读写事件就绪后，由系统调用本身负责进行读写，也就是说这个读写过程是阻塞的。

## 4. 异步IO模型（Asynchronous IO）

要想彻底解决线程的阻塞，就必须使用异步IO模型

AIO 的基本流程是：用户线程通过系统调用， 向内核注册某个IO 操作。内核在整个IO 操作（包括数据准备、数据复制）完成后，**通知**用户程序，用户执行后续的业务操作。因此，也被称为信号驱动 IO。

在异步 IO 模型中，在整个内核的数据处理过程中，包括内核将数据从网络物理设备（网卡） 读取到内核缓冲区、将内核缓冲区的数据复制到用户缓冲区，用户程序都不需要阻塞。

![](https://raw.githubusercontent.com/hattori7243/img/master/20210616160503.png)

举个例子。发起一个异步 IO 的 read 读操作的系统调用，流程如下：

（1）当用户线程发起了 read 系统调用，立刻就可以开始去做其他的事，用户线程不阻塞。

（2）内核就开始了 IO 的第一个阶段：准备数据。等到数据准备好了，内核就会将数据从内 核缓冲区复制到用户缓冲区（用户空间的内存）。

（3）内核会给用户线程发送一个信号（Signal），或者回调用户线程注册的回调方法，告诉 用户线程 read 操作完成了。

（4）用户线程读取用户缓冲区的数据，完成后续的业务操作。

- 优点：在等待和复制数据的两个阶段，都不需要阻塞用户线程。用户线程只需要注册IO操作，调用立即返回后等待内核完成IO操作的信号即可。**是真正的异步输入输出，吞吐量高于IO多路复用的吞吐量。**
- 缺点：将工作都留给了操作系统，需要底层内核提供支持。

Windows 系统下通过 IOCP 实现了真正的异步 IO。而在 Linux 系统下，异步 IO 模型在 2.6 版本才引入，目前并不完善，其底层实现仍使用 epoll，与 IO 多路复用相同，因此在性 能上没有明显的优势。

大多数的高并发服务器端的程序，一般都是基于 Linux 系统的。因而，目前这类高并发网络应 用程序的开发，大多采用 IO 多路复用模型。

大名鼎鼎的 Netty 框架，使用的就是 IO 多路复用模型，而不是异步 IO 模型。

# Java New IO（NIO）

在 1.4 版本之前，Java IO 类库是阻塞 IO；从 1.4 版本开始，引进了新的异步 IO 库，被称为 Java New IO 类库，简称为 JAVA NIO。New IO 类库的目标，就是要让 Java 支持非阻塞 IO

Java NIO 为非阻塞 IO（Non-Block IO），称“老的”阻塞式 Java IO 为 OIO （Old IO）。

**OIO是面向流的**，不能随意改变读取指针的位置

**NIO是面向缓冲区的**，可以随意读取Buffer中任意位置的数据，可以不用顺序操作。

Java NIO 由以下三个核心组件组成：

- Channel（通道）
- Buffer（缓冲区）
- Selector（选择器）

## 1. Channel 通道

在 OIO 中，同一个网络连接会关联到两个流：一个输入流（Input Stream），另一个输出流（Output Stream）。通过这两个流，不断地进行输入和输出的操作。

在 NIO 中，同一个网络连接使用一个通道表示，所有的 NIO 的 IO 操作都是从通道开始的。 一个通道类似于 OIO 中的两个流的结合体，**既可以从通道读取，也可以向通道写入。**

Java中的通道Channel主要有四种实现：

- `FileChannel` 文件通道

用于文件的读写

只能为阻塞模式，不能设置为非阻塞模式

- ` SocketChannel` 套接字通道，用于Socket套接字TCP连接的数据读写

- ` ServerSocketChannel` 服务器监听通道，允许监听TCP连接请求，为每个监听到的请求，创建一个SocketChannel套接字通道

ServerSocketChannel 应用于服务器端，而 SocketChannel 同时处于服务器端和客户端。换句话 说，对应于一个连接，两端都有一个负责传输的 SocketChannel 传输通道。同时服务器端还有一个ServerSocketChannel通道。

都支持阻塞和非阻塞模式。`socketChannel.configureBlocking（false）`false非阻塞，true阻塞

`DatagramChannel` 数据包通道，用于UDP协议的数据读写

也支持阻塞和非阻塞模式

不是提前绑定连接到channel上，而是在发送的时候传参。监听的时候还是提前绑定自身端口

## 2. Selector 选择器

IO 多路复用指的是一个进程/线程可以同时监视多个文 件描述符（一个网络连接，操作系统底层使用一个文件描述符来表示），一旦其中的一个或者多个文件描述符可读或者可写，系统内核就通知该进程/线程。

而在Java中，实现对多个文件描述符的监视就是用到了**Selector选择器**

它是一个 IO 事件的查询器。通过选择器，一个线程可以查询多个通道的 IO 事件的就绪状态。

使用选择器，可以很简单地使用一个线程， 通过选 择器去管理多个通道。这是非常高效的，这种高效来自于 Java 的选择器组件 Selector，以及其背后的操作系统底层的 IO 多路复用的支持。

- 通道和选择器之间通过**register（注册）**的方式完成建立连接。
  - 调用通道的 `Channel.register （Selector sel，int ops）`方法，可以将通道实例注册到一个选择器中。
  - ops参数指定选择器要监控的IO事件类型，若要监听多种事件，可按位或。
  - ![](https://raw.githubusercontent.com/hattori7243/img/master/20210616191721.png)
  - IO 事件不是对通道的 IO 操作，而是通道的某个 IO 操作的一种就绪状态，**表示通道具备完成某个 IO 操作的条件。**
  - 被选择器监控的通道必须继承了抽象类`SelectableChannel`，FileChannel就不行
- 通过`select()`函数判断是否有就绪的IO事件，返回值是就绪的事件数目，用`selectedKeys()`并读到一个集合中，然后对应操作。
  - ![](https://raw.githubusercontent.com/hattori7243/img/master/20210616192141.png)
  - ![](https://raw.githubusercontent.com/hattori7243/img/master/20210616192150.png)

## 3. Buffer缓冲区

通道的读取， 就是将数据从通道读取到缓冲区中；通道的写入，就是将数据从缓冲区中写入到通道中。

NIO 的 Buffer（缓冲区）本质上是一个内存块，与普通的内存块（Java 数组）不同的是：NIO Buffer 对象，提供了一组更加有效的方法，用来进行写入和读取的交替访问。

**需要强调的是：Buffer 类是一个非线程安全的抽象类。**

### Buffer类的属性

实现有很多种，针对不同数据类型`ByteBuffer 、 CharBuffer 、 DoubleBuffer 、 FloatBuffer 、 IntBuffer 、 LongBuffer 、 ShortBuffer 、 MappedByteBuffer`

内部有一个 byte[]数组内存块，作为内存缓冲区。为了记录读写的状态和位置， Buffer 类提供了一些重要的属性。其中，有三个重要的成员属性：capacity（容量）、position（读写位置）、limit（读写的限制）。

还有一个标记属性：mark（标记），可以存入当前position，后面再恢复。

- capacity 容量

表示内部容量的大小。一旦写入的对象数量超过了 capacity 容量， 缓冲区就满了，不能再写入了。

大小并不是指字节的数量，而是写入的数据对象的数量。例如对于DoubleBuffer来说，capacity等于10代表能存10个double也就是80个byte

- position 读写位置

在读写模型的转变时，position会被重新赋值。通过调用flip翻转方法，变换模型。

需要注意的是，position和limit几乎是同时变化的，因为读写模式对应的limit也不一样

- limit 读写的限制

在写模式下，limit 属性值的含义为可以写入的数据最大上限

在读模式下，limit 的值含义为最多能从缓冲区中读取到多少数据

![](https://raw.githubusercontent.com/hattori7243/img/master/20210616162646.png)

### Buffer类的方法

- allocate()：创建缓冲区：调用具体实现子类的allocate方法，不是使用new。

```
Buffer intBuffer=IntBufer.allocate(20);
```

- put()：写入缓冲区，需要注意的是必须写入对应类型的数据。因为Buffer中是没有定义put方法的，所以必须强制转化类型或者在一开始就指定类型，这样就能保证写入数据类型的正确性。
- flip()：将写模式变为读模式。将limit设为position，position变为0。

![image-20210616163524979](/Users/zhangshuyu/Library/Application Support/typora-user-images/image-20210616163524979.png)

- get()：读取缓冲区，特别注意的是，读之前必须保证是在读模式，不然读不到数据。也可以指定读某个位置的数据，不带参数默认读position的数据。
- rewind()：倒带。将position重新置为0。

- mark()和reset()：保存和还原position
- clear()：清空缓冲区并切换到写入模式。其实并没有真正的删除数据，只是把指针回到原点而已，limit设为最大容量而已。

### 总结

- NIO 可以通过选择器（也可以说成：多路复用器），后续不断地轮 询选择器的选择键集合，选择新到来的连接。
- SocketChannel 传输通道的读写操作都是非阻塞的。如果没有可读写的数据， 负责 IO 通信的线程不会同步等待。这样，线程就可以处理其他连接的通道；不需要像 OIO 那样， 线程一直阻塞，等待所负责的连接可用为止。
- 一个选择器线程可以同时处理成千上万个客户端连接，性能不会随着客户端 的增加而线性下降。

# Reactor反应器模式

反应器模式由Reactor反应器线程、Handlers处理器两大角色组成

1. Reactor反应器线程的职责：负责响应IO事件，并且分发到Handlers处理器
2. Handlers处理器的职责：非阻塞的执行业务处理逻辑

处理并发连接请求时，为了避免当前请求未处理完无法处理后面的请求的情况，早期采用**一个线程处理一个连接**（Connection Per Thread）的模式。各个socket的输入输出处理都是独立的。早期版本的Tomcat就是这样实现的。

缺点是：对应于大量的连接，需要耗费大量的线程资源，对线程资源要求太高。

## 单线程Reactor反应器模式

![](https://raw.githubusercontent.com/hattori7243/img/master/20210616194559.png)

为对应的IO状态，在注册的时候用attach(Object handler)将状态与处理的函数关联起来。在实际处理该状态时，用attachment()取出对应的处理函数进行处理。

![](https://raw.githubusercontent.com/hattori7243/img/master/20210616195007.png)

![](https://raw.githubusercontent.com/hattori7243/img/master/20210616195017.png)

也就是建立一个key的连接的类型，value是处理的函数的映射，然后由reactor来根据key找value来进行处理。

但是由于reactor和handler都是在同一个线程中的，所以在处理handler时，其他handler和reactor也是被阻塞的。

## 多线程Reactor反应器模式

多线程池 Reactor 反应器的演进，分为两个方面：

（1）首先是升级 Handler 处理器。既要使用多线程，又要尽可能的高效率，则可以考虑使用线程池。

（2）其次是升级 Reactor 反应器。可以考虑引入多个 Selector 选择器，提升选择大量通道的能力。

总体来说，多线程池反应器的模式，大致如下：

（1）将负责输入输出处理的 IO **Handler 处理器的执行，放入独立的线程池中**。这样，**业务处理线程**与负责服务**监听**和 IO 事件查询的反应器线程**相隔离**，避免服务器的连接监听受到阻塞。

（2）如果服务器为多核的 CPU，可以将**反应器线程拆分为多个子反应器（SubReactor）线程**； 同时，引入多个选择器，每一个 SubReactor 子线程负责一个选择器。这样，充分释放了系统资源的能力；也**提高了反应器管理大量连接，提升选择大量通道的能力**。

## 总结

- 反应器模式和生产者消费者模式对比

生产者消费者模式是生产者将事件放到队列中，由消费者来提取进行处理。而反应器模式是基于查询的，对每一个来说都有一个Handler来处理，直接处理，不需要缓冲存储事件。

- 优点
  - 响应快，虽然同一反应线程本身是同步的，但是由于和实际处理线程解耦开了，不会被单个连接的同步IO所阻塞；
  - 最大程度避免了复杂的多线程同步，页避免了多线程切换的开销
  - 可扩展
- 缺点
  - 需要操作系统底层的IO多路复用的支持

# 异步回调模式

## Future异步回调模式

### Callable接口

为了解决之前Runnalbe接口接口没有返回值的问题，定义了Callable接口，其中业务处理的方法为call()，有返回值

![](https://raw.githubusercontent.com/hattori7243/img/master/20210617164333.png)



但是Callable接口并没有办法直接作为Thread线程实例的构造参数，需要借助FutureTask来封装一下。同时FutureTask间接继承了Runnable接口，所以可以这样就可以作为Thread实例的target执行目标了。

**FutureTask 类就像一座搭在 Callable 实例与 Thread 线程实例之间的桥。FutureTask 类的内部封 装一个 Callable 实例，然后自身又作为 Thread 线程的 target。**

需要保证FutureTask的范型参数和Callable的一致

FutureTask中的run方法去调用Callable的call()方法，同时更新完成状态和保存结果（到`private Object outcome`中）

![](https://raw.githubusercontent.com/hattori7243/img/master/20210617164656.png)

### Future接口

FutureTask 就是实现了Future接口。

Future接口提供了三大功能

- 判断并发任务是否执行完成
- 获取并发任务执行完成后的结果，也就是返回值
  - 获取异步结果时，也是阻塞的
  - ![image-20210617165554003](/Users/zhangshuyu/Library/Application Support/typora-user-images/image-20210617165554003.png)
- 取消并发执行中的任务

有以下方法

![](https://raw.githubusercontent.com/hattori7243/img/master/20210617164857.png)



## Guava的异步回调

Guava是谷歌公司提供的 Java 扩展包，提供了一种异步回调的解决方案

异步任务接口 ListenableFuture，扩展了 Java 的 Future 接口，实现了 **非阻塞获取异步结果的功能**。

Guava 对 Java 的异步回调机制，做了以下的增强：

（1）引入了一个新的接口 ListenableFuture，**继承了 Java 的 Future 接口**，使得 Java 的 Future 异步任务，在 Guava 中能被监控和获得非阻塞异步执行的结果。

（2）引入了一个新的接口 FutureCallback，这是一个独立的新接口。该接口的目的，是在异步任务执行完成后，根据异步结果，完成不同的回调处理，并且可以处理异步结果。**即根据执行结果，来进行善后**

因此，不用阻塞的等待返回值，由于在完成后的处理逻辑已经绑定到异步任务中，所以他完成后会自动进行处理逻辑。我们只需要异步的去获取即可。

Guava 异步回调和 Java 的 FutureTask 异步回调，本质的不同在于：

- Guava是非阻塞的异步回调，调用线程是不阻塞的，可以继续执行自己的业务逻辑

- FutureTask是阻塞的异步回调，调用线程是阻塞的，在获取异步结果的过程中，一直阻塞，等待异步线程返回结果。

## Netty的异步回调模式

总体来说，Netty 对 JavaFuture 异步任务的扩展如下：

（1）继承 Java 的 Future 接口，得到了一个新的属于 Netty 自己的 Future 异步任务接口；该接 口对原有的接口进行了增强，使得 Netty 异步任务能够以非阻塞的方式处理回调的结果；注意，Netty 没有修改 Future 的名称，只是调整了所在的包名，Netty 的 Future 类的包名和 Java 的 Future 接口 的包名不同。

（2）引入了一个新接口——GenericFutureListener，用于表示异步执行完成的监听器。这个接 口和 Guava 的 FutureCallbak 回调接口不同。Netty 使用了监听器的模式，异步任务的执行完成后的 回调逻辑抽象成了 Listener 监听器接口。可以将 Netty 的 GenericFutureListener 监听器接口加入 Netty 异步任务 Future 中，实现对异步任务执行状态的事件监听。

总体上说，在异步非阻塞回调的设计思路上，Netty 和 Guava 的思路是一致的。对应关系为：

- Netty的Future接口，继承 Java 的 Future 接口，得到了一个新的属于 Netty 自己的 Future 异步任务接口；该接口对原有的接口进行了增强，使得 Netty 异步任务能够以非阻塞的方式处理回调的结果
- Netty的GenericFutureListener接口，使用了**监听器的模式**，异步任务的执行完成后的回调逻辑抽象成了 Listener 监听器接口。可以将 Netty 的 GenericFutureListener 监听器接口加入 Netty 异步任务 Future 中，实现对异步任务执行状态的事件监听。

# Netty的原理与基础

Netty 是为了快速开发可维护的高性能、高可扩展、网络服务器和客户端程序而提供的异步事件驱动基础框架和工具。

换句话说，Netty 是一个 Java NIO 客户端/服务器框架，可以大大简化了网络编程流程。

是基于反应器模式实现的，对于Java NIO通信的应用场景，反应器为`NioEventLoopGroup`

**Handle处理器**

Netty 的 Handler 处理器需要处理多种 IO 事件（如可读、可写），对应于不同的 IO 事件，Netty 提供了一些基础的方法。这些方法都已经提前封装好，后面直接继承或者实现即可。

比如说，对于 处 理 入 站 的 IO 事 件 的 方 法 ， 对 应 的 接 口 为 ChannelInboundHandler 入 站 处 理 接 口 ， 而 这也是 Netty 提供的入站处理的默认实现。

### Netty中的Channel

Netty 中的每一种协议的通道，都有 NIO（异步 IO）和 OIO（阻塞式 IO）两个版本。

![](https://raw.githubusercontent.com/hattori7243/img/master/20210617183722.png)

### Netty中的Reactor反应器

Netty 中的反应器有多个实现类，与 Channel 通道类有关系。对应于 NioSocketChannel 通道， Netty 的反应器类为：NioEventLoop。

一 个 NioEventLoop 拥有一个 Thread 线程，负责一个 Java NIO Selector 选择器的 IO 事件轮询。

### Netty中的Handle处理器

Netty 的 Handler 处理器分为两大类：第一类是 ChannelInboundHandler 通道入站处理器；第二 类是 ChannelOutboundHandler 通道出站处理器。二者都继承了 ChannelHandler 处理器接口。

### Netty中的Bootstrap引导类

![](https://raw.githubusercontent.com/hattori7243/img/master/20210617184220.png)

**启动流程**

- 创建引导类

![](https://raw.githubusercontent.com/hattori7243/img/master/20210617184607.png)

- 创建反应器线程组，并和引导类绑定

![](https://raw.githubusercontent.com/hattori7243/img/master/20210617184618.png)

- 设置通道的IO类型

![](https://raw.githubusercontent.com/hattori7243/img/master/20210617184646.png)

- 设置监听端口

![](https://raw.githubusercontent.com/hattori7243/img/master/20210617184706.png)

- 设置传输通道的配置选项

![](https://raw.githubusercontent.com/hattori7243/img/master/20210617184727.png)

- 装配子通道的Pipeline流水线

![](https://raw.githubusercontent.com/hattori7243/img/master/20210617184941.png)

- 绑定服务器新连接的监听端口

![](https://raw.githubusercontent.com/hattori7243/img/master/20210617185056.png)

**关闭流程**

- 自我阻塞，直到通道关闭

![](https://raw.githubusercontent.com/hattori7243/img/master/20210617185229.png)

- 关闭EventLoopGroup

![](https://raw.githubusercontent.com/hattori7243/img/master/20210617185251.png)


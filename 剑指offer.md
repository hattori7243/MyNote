# 算法思路

## 3. Top-K问题和K-th问题

解决方法有两种：

- 第一种，利用快排的模版，每次选择以后丢弃一般的排序区间。这样时间复杂度是O(n)。这种做法会改变原数组。

- 第二种，利用大小为k的小根堆或者大根堆。java可以利用PriorityQueue来作为堆。这种做法不会改变原数组。
  - 且这种方法，在处理海量数据时有优势。因为如果数据量很大，可能无法全部读到内存中进行排序。这时就可以分批次读入判断是否加入堆即可。
  - 例如，需要前k小的元素。我们设置一个大小为k的大根堆，即堆顶是最大的元素。当新元素比堆顶小的时候，把堆顶踢掉加入新元素。这样到最后，堆中剩下的就是前k个最小的。堆顶的就是第k小的。

## 5. 数组中只出现1次的数字

在一个数组中，除了一个数字只出现一次之外，其他数字都出现了三次。请找出那个只出现一次的数字

我们如果把这个题目改成其他数字都只出现了两次，那么就很简单可以用异或来做。因此出现两次的数字对应位出现两次相同的异或为0

也就是说，是0^0==1^1==0让两次变得很简单。

因此，如果我们能找到一个操作x，三个0之间的操作x和三个1之间的x操作结果相同的话，就可以排除出来了。

因此，想到(1+1+1)%3==(0+0+0)%3。因此，我们将对应位等于所有数对应位的和加起来mod3。

这样，三次的都被消除了，只剩一个出现一次的。

这种思想还可以拓展到k次。

# 设计思想

## 单例模式的写法

首先构造函数必须是private的，避免外界能调用构造函数。将实际要保存的对象设置为static

### 1. 单线程按需创建

最常见的，提供一个公有的静态方法`getInstance`，判断单例对象`instance`不为空的时候直接返回该对象，为空的时候才创建

<img src="/Users/zhangshuyu/Library/Application Support/typora-user-images/image-20210624102104708.png" alt="image-20210624102104708" style="zoom:85%;" />

问题是，这样的情况在单线程中是很高效的，但是在多线程环境中，在判断`instance==null`可能会发生问题

### 2. 双重检查加锁，线程安全按需创建

因为1中在多线程情况下会出现问题，原因在于instance==null的判断中，因此我们加锁的方式实现

<img src="https://raw.githubusercontent.com/hattori7243/img/master/20210624102503.png" style="zoom:85%;" />

改动点有三个，第一个是将instance域改为volatile，保证每个线程改动后其他线程都能立即获取。这样第一次`instance==null`的判断结果才可靠。

同时，如果不加第一个instance==null的判断条件的话，每次进行第二个instance==null都需要获取锁，开销是很大的。因此，我们用双重检查，在第一次检查就过滤掉大部分情况，再用第二次检查来+锁的方式来实现线程安全性。

### 3. 提前创建

这种方法指的是在类中对静态域进行初始化的时候完成对单例对象的分配，正确性由虚拟机通过类的加载机制来保证。

缺点是无论该单例对象用不用得到，都会被创建出来。

![](https://raw.githubusercontent.com/hattori7243/img/master/20210624103038.png)

### 4. 静态内部类实现

3中是通过虚拟机的类加载机制来完成的，也就是说当我加载自己这个对象时，就必然导致该静态单例对象被创建。但是，如果通过不是自身的类，而是其他类，也是通过虚拟机的类加载机制来完成的话，就可以达到用的时候才创建了。

因此，使用一个静态内部类，我们在该内部类中设置一个静态域的初始化条件等于一个新的单例对象，这样当不用的时候，该静态内部类不会被加载，也就不会创建。当需要用到的时候，虚拟机会加载该类，也就会创建新的单例对象，正确性也还是由虚拟机保证的。

![](https://raw.githubusercontent.com/hattori7243/img/master/20210624103424.png)

### 破坏单例模式的方法

#### 1. 反射

因为我们避免重复创建的方式是将构造函数设为private，让获取新对象只能通过我们设置的函数来获取，进而来进行控制。

因此如果通过反射来直接获取构造函数，并通过设置setAccessible来直接调用，就可以获取一个新的不同的单例对象。

**解决方法**

既然我们无法阻止通过反射来获取并调用构造函数，那么我们就可以在构造函数中来进行控制。

也就是说，我们让构造函数来识别之前此次调用是不是第一次创建对象。

那么就可以通过判断instance是不是null或者设置一个flag标志位默认为false，当调用一次构造函数后就设置为true都可以。

#### 2. 调用clone()方法进行破坏

因为如果调用父类Object的clone()方法的话，必须实现Cloneable接口。此时确实会生成一个新的对象，判断两个也是不等的。

**解决方法**

第一，可以不实现Cloneable接口，禁止调用clone方法

第二，可以override clone方法，是其返回已经创建的实例对象

#### 3. 序列化+反序列化

和clone()方法类似，如果实现了Serializable接口的话，通过序列化转换，最后得到的对象也是不等的。

**解决方法**

第一，可以不实现Serializable接口，禁止序列化

第二，通过写readResolve方法。如果反序列化的目标类有readResolve方法，就会通过反射的方式调用该类的readResolve方法，返回一个对象。那我们在该方法中返回instance即可保证反序列化的也相同即可。

### 完整代码

```Java
import java.io.*;
import java.lang.reflect.Constructor;

public class Test implements Cloneable, Serializable {

    public static void main(String[] args) throws Exception {

        System.out.println("-----------序列化----------------------");
        Test originTest = Test.getInstance();
        ByteArrayOutputStream bos = new ByteArrayOutputStream();
        ObjectOutputStream oos = new ObjectOutputStream(bos);
        oos.writeObject(Test.getInstance());
        ByteArrayInputStream bis = new ByteArrayInputStream(bos.toByteArray());
        ObjectInputStream ois = new ObjectInputStream(bis);
        Test serializeTest = (Test) ois.readObject();
        System.out.println(originTest == serializeTest);//true or false

        System.out.println("-----------反射----------------------");
        //通过反射获取
        Constructor<Test> cons = Test.class.getDeclaredConstructor();
        ((Constructor) cons).setAccessible(true);
        Test reflextTest = cons.newInstance();
        System.out.println(reflextTest == originTest);//true or false

        System.out.println("---------------------------克隆----------------------");

        Object clone = originTest.clone();
        Test cloneTest=(Test) clone;
        System.out.println(cloneTest == originTest);//true or false
    }
    private Test() {
    }
    private static class TestHolder{
        private static volatile Test instance=new Test();
    }
    public static Test getInstance(){
        return TestHolder.instance;
    }
    private Object readResolve(){
        return TestHolder.instance;
    }
}
```


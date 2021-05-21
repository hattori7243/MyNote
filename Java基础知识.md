# Java基础

# Java容器





# Java并发

# Java虚拟机

# JavaI/O

## 序列化

必须实现 Serializable 接口，只是一个标准，并没有任何方法需要实现

### transient

- ArrayList中保存数据的数组是transient的，因为ArrayList的数组是动态扩展的，并不是所有空间都被使用，因此不需要所有内容序列化，通过重写序列化和反序列化方法，可以只序列化数组中有内容的那部分数据。
- 而LinkedList和HashMap中，均有着只保存了链的引用的情况，这部分也是transient的，也是需要重写序列化和反序列化方法，通过引用来将所有数据序列化




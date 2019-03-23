# JVM(一)

标签（空格分隔）： JVM

---

## 1.执行流程
> java源代码 ————> (编译后)java字节码文件 ————> 类加载器(classloader) ————>JVM(运行时数据区)


## 2.运行时数据区
> 
+ 堆内存
    - 线程共享
    - 存放对象实例
    - 垃圾收集器管理的主要区域，分代手机算法：
        - 新生代
            - Eden
            - From Survivor
            - To Survivor
        - 老年代
    - Xms Xms 设置堆大小；若堆没有内存可以分配，则OutOfMemoryError异常
+ 方法区
    - 线程共享
    - 存储已被虚拟机加载的类信息,常量,静态变量,即时编译器编译后的代码等
    - 也成为永久代，等本质上二者不等价。或者说使用永久代实现方法区
    - 非堆；但内存不够分配时，也OutOfMemoryError异常
    - 元空间 
+ 程序计数器
    - 线程私有
    - 当前线程所执行的字节码的行号指示器
    - 如果执行的是java方法，则计数器记录是正在执行的虚拟机字节码指令的地址
    - 如果是Native方法，则计数器则为空
    - 唯一一个不会OutOfMemoryError异常的区域
+ JVM栈
    - **线程私有**
    - 栈描述的是java方法执行的内存模型，执行一个方法的时候会产生一个栈帧，随后将其保存到栈定，方法执行完自动出栈。**顶栈的栈帧表示的是当前执行的方法**。
        - 若栈的深度过大，如递归，就会StackOverflowError异常。
        - 若虚拟机允许栈动态扩展内存不足就会则OutOfMemoryErro常异常
+ 本地方法栈
    - **线程私有**
    - 和JVM栈的作用相似，只是JVM栈为虚拟机执行Java方法服务的，而本地方法栈是为虚拟机使用到的Native方法服务的。
    - 一样存在StackOverflowError 和 OuOfMemoryError的异常
    
## 3.Java对象访问模式
> 整个的引用数据类型处理数据的时候会牵扯到：堆内存，栈内存以及方法区。Example： 
Object obj = new Object();
+ Object obj保存在栈内存中，保存的堆内存引用。严格说，这个数据会保存到本地变量表中。表中会描述有多少个对象，对象对应的是哪个栈内存，栈内存以对应某个堆内存。
+ new Object()一个真的对象保存在堆内存中。

---

> 而直观的引用操作为：
+ 1.新定义对象名称保存在本地变量表里面，然后在这块区域保存与之对应的栈内存
+ 2.通过变量表中的栈内存地址可以找堆内存
+ 3.然后利用堆内存的对象对本地方法的调用(方法区)

--- 
> 实际上对于引用数据类型的访问有两种方式，分别是：
+ 1.句柄
    本地变量表              
       int
      double
      refer  
       ……
       ……                               
       ……
    对于refer引用指向着堆内存的句柄池，句柄池里分别是
    1)对象示例数据指针——>实例池
    2)对象类型数据指针——>对象类型数据(方法区) 
+ 2.直接指针访问(Java目前使用的模式)
> 堆内存里去掉了句柄池，而直接保存了具体对象；而这个对象可以直接进行Java方法区的调用
对于refer引用指向着堆内存的具体对象，
    1)对象实例数据
    2)对象类型数据指针——>对象类型数据(方法区)
    
    
## 4.控制参数
> + 堆空间
    - Xms 最小
    - Xms 最大
+ 新生代
    - XX:NewSize 最小
    - XX:MaxNewSize 最大
    - XX:NewRatio 用来来指定新生代和整个堆的大小比例
    - XX:SurvivorRatio Eden区与Survivor区的大小比值
+ 永久代
    - XX:PermSize 最小
    - XX:MaxPermSize 最大
+ 元空间
    - XX:MetaspaceSize 最小
    - XX:MaxMetaspaceSize 最大
+ 老年代
    - 堆空间 - 年轻代空间大小
+ 每个线程堆栈大小
    -Xss
+ GC日志
    - XX:+PrintGCDetails  (打印GC详细日志)
    - XX:+PrintGCDateStamps  (打印GC的时间戳)
    - Xloggc:/mnt/yyspace/logs/gc/system_gc.log (GC输出路径)
    - XX:+HeapDumpOnOutOfMemoryError  (可以让JVM在出现内存溢出时候Dump出当前的内存转储快照)

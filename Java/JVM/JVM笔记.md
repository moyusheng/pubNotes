## JVM体系结构

JVM位置

<img src="JVM笔记.assets/20191130085629392.png" alt="image-20200916114846002"  style="float: center;" />

JVM是运行在操作系统之上的，它与硬件没有直接的交互。



![](JVM笔记.assets/20191130085643146.png)

## Java中的常量池

分为三类

- 类文件中常量池（The Constant Pool）
  - 所处区域：堆
  - 诞生时间：编译时
  - 内容概要：
    - 包含字面量和符号引用
    - ![img](JVM笔记.assets/20170329213804490.jpg)
  - class常量池是在编译的时候每个class都有的，在编译阶段，存放的是常量的符号引用。
- 运行时常量池（The Run-Time Constant Pool）
  - 所处区域：元空间
  - 诞生时间：JVM运行时
  - 内容概要：
    - class文件元信息描述，编译后的代码数据，引用类型数据，类文件常量池。 
    -  所谓的运行时常量池其实就是将编译后的类信息放入运行时的一个区域中，用来动态获取类信息。
    - 运行时常量池是在类加载完成之后，将每个class常量池中的符号引用值转存到运行时常量池中，也就是说，每个class都有一个运行时常量池，类在解析之后，将符号引用替换成直接引用，与全局常量池中的引用值保持一致。
- String常量池
  - 所处区域：堆
  - 内容概要：
    - 准备阶段之后在堆中生成字符串对象实例，然后将该字符串对象实例的引用值存到字符串常量池中
    - String常量池中存的是引用值而不是具体的实例对象，具体的实例对象是在堆中开辟的一块空间存放的。



**new对象和Class的区别**

![image-20210521213244059](JVM笔记.assets/image-20210521213244059.png)

说明

- Class只有一个，无论new多少次；实例有多个，并且地址不一样

**class和对象实例的转换**

<img src="JVM笔记.assets/image-20210521213640568.png" alt="image-20210521213640568" style="zoom:80%;" />

说明

- `Class类`转换为`实例`，是通过`new Class()`
- `实例`转换为`Class`，是通过`实例.getClass()`







类加载机制



![image-20210521164649979](JVM笔记.assets/image-20210521164649979.png)

常量池中

- 字面量
  - final修饰 文本  字符串
- 符号引用
  - 类 接口 方法 明细数据 描述



装载

类相关的信息，model信息储存在方法区中

对象，放在堆中

字节码文件变成字节流，然后类加载器寻找到它，

将字节流代表的静态存储结构（静态变量、类信息、常量、即时编译后的代码）转化为方法区的运行时数据结构，

储存在方法区的数据，需要专门的对象对它进行访问，所以java堆中生成一个代表这个类的JAVA.lang.Class对象，作为数据访问入口



链接

验证：验证格式的正确性，符号引用？字节码？保证文件不能有问题

准备：为类的静态变量分配内存 ，赋予默认值

解析：符号引用转换成直接引用，即在虚拟机中开辟一块地址，真正指向一块地址



初始化

给静态变量复制，初始化静态代码块，初始化当前类，当前类的parent



## 类加载器

- 虚拟机自带的加载器
- 启动类（根）加载器Bootstrap classLoader
  - 主要负责加载核心的类库(java.lang.*等)，构造ExtClassLoader和APPClassLoader
- 扩展类加载器 ExtClassLoader
  - 主要负责加载jre/lib/ext目录下的一些扩展的jar。
- 应用程序加载器 AppClassLoader
  - 主要负责加载应用程序的主函数类

加载顺序是自下往上找

```java
public class Car {
    public static void main(String[] args) {
        Car car = new Car();
        Class<? extends Car> aClass = car.getClass();
        ClassLoader classLoader = aClass.getClassLoader();
        System.out.println("classLoader: "+classLoader); //sun.misc.Launcher$AppClassLoader@18b4aac2
        System.out.println("classLoader.getParent(): "+classLoader.getParent());//sun.misc.Launcher$ExtClassLoader@4554617c  在jre/lib/ext拓展包下面
        System.out.println("classLoader.getParent().getParent(): "+classLoader.getParent().getParent());//null  1.不存在 2.java程序获取不到  在lib/rt.jar
    }
}
```





## 双亲委派机制

**工作原理**

- 1.如果一个类加载器收到了类加载请求，它并不会自己先去加载，而是把这个请求委托给父类的加载器去执行

- 2.如果父类加载器还存在其父类加载器，则进一步向上委托，依次递归，请求最终将到达顶层的启动类加载器

- 3.如果父类加载器可以完成类加载任务，就返回成功，倘若父类加载器无法完成此加载任务，子加载器才会尝试自己去加载，这就是双亲委派模式

  <img src="JVM笔记.assets/20210422185342282.png" alt="img" style="zoom:80%;" align="center"/>

优点：

- 避免类的重复加载
- **保护程序安全，防止核心API被随意篡改**

举例：

![img](JVM笔记.assets/20210422185648364.png)，如图，我们自定义包路径为java.lang，并在此包下定义一个String类![img](JVM笔记.assets/20210422185738689.png)，在String类中启动main方法，ide会报错![img](JVM笔记.assets/20210422185835494.png)，找不到main方法。

**原因**在于String类是java的核心类，位于java.lang包下，当我们启动main方法时，需要先获取main方法所在的类，也就是String类，经过层层向上委托直到引导类加载器，引导类加载器发现该类是java.lang包下的，所以该类由引导类加载器加载，但原生String类并没有main方法，故报错“找不到main方法”






















































































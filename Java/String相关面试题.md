## **1.判定定义为String类型的st1和st2是否相等，为什么**

```php
package string;
 
public class Demo2_String {
 
  public static void main(String[] args) {
    String st1 = "abc";
    String st2 = "abc";
    System.out.println(st1 == st2);
    System.out.println(st1.equals(st2));
  }
}

输出结果：
第一行：true
第二行：true
```

解释：

​		String类代表字符串，字符串是常量，常量需要进入到内存中的方法区的常量池(进入常量池规则：**如果常量池中没有这个常量，就创建一个，如果有就不再创建了**)。

​		所以s1首先会在常量池中创建`abc`对象，`abc`对象是有地址值的，`abc`对象将地址值赋给s1。当s2的时候，此时常量池中已经存在了`abc`对象，所以就不需要再创建了，s2的引用也指向了常量池中的`abc`对象，两个引用指向了同一个对象，s1和s2地址值一样

> ==和equals的区别
>
> ​	==比较的内容：
>
> ​		基本数据结构（`byte,short,int,long,float,double,boolean,char`）比较的是值是否相等
>
> ​		引用数据类型，比较的是堆内存地址是否相等
>
> ​	equals比较的内容：
>
> ​		引用类型，默认情况下，比较内存地址值是否相同；可以进行重写



## **2.下面这句话在内存中创建了几个对象？**

```
String s = new String("abc");
```

解答：

​		2个对象。`s`首先会在常量池创建`abc`字符串常量，当`new`的时候就会在堆内存中创建一个对象，此时会把常量池中的字符串常量拷贝一份副本到给到堆内存中的对象，堆内存中的这个对象就会把地址值赋给`s`。


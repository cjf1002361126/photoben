# 剑指JAVA面试



在Idea中看到的src和resource不是classpath, 项目被打成war包后WEB-INF中的classes,lib才是classpath，WEB-INF 是资源目录, 客户端不能直接访问，所以我们需要添加classpath访问。

<context:property-placeholder location="classpath:/properties/config.properties"/>



## 六、JVM



[**一看你就懂，超详细java中的ClassLoader详解**](https://blog.csdn.net/briblue/article/details/54973413)

[**java中JVM的原理**](https://blog.csdn.net/witsmakemen/article/details/28600127/)

### ClassLoader

![1557636962777](https://github.com/cjf1002361126/photoben/blob/master/慕课网-剑指java面试/1557636962777.png?raw=true)

![1557662735880](https://github.com/cjf1002361126/photoben/blob/master/慕课网-剑指java面试/1557662735880.png?raw=true) 

![1557664123011](https://github.com/cjf1002361126/photoben/blob/master/慕课网-剑指java面试/1557664123011.png?raw=true)

Launcher 类

```java
static class AppClassLoader extends URLClassLoader 
static class ExtClassLoader extends URLClassLoader
```

### 反射



![1557639360931](https://github.com/cjf1002361126/photoben/blob/master/慕课网-剑指java面试/1557639360931.png?raw=true)



利用反射来创建实例时，可绕过jvm的权限检查机制。

### 双亲委派

![1557675980769](https://github.com/cjf1002361126/photoben/blob/master/慕课网-剑指java面试/1557675980769.png?raw=true)



查看native代码

http://hg.openjdk.java.net/jdk8u/jdk8u/jdk/



java.ext.dirs : C:\Program Files\Java\jdk1.8.0_191\jre\lib\ext;C:\Windows\Sun\Java\lib\ext

sun.boot.class.path : C:\Program Files\Java\jdk1.8.0_191\jre\lib\resources.jar;C:\Program Files\Java\jdk1.8.0_191\jre\lib\rt.jar;C:\Program Files\Java\jdk1.8.0_191\jre\lib\sunrsasign.jar;C:\Program Files\Java\jdk1.8.0_191\jre\lib\jsse.jar;C:\Program Files\Java\jdk1.8.0_191\jre\lib\jce.jar;C:\Program Files\Java\jdk1.8.0_191\jre\lib\charsets.jar;C:\Program Files\Java\jdk1.8.0_191\jre\lib\jfr.jar;C:\Program Files\Java\jdk1.8.0_191\jre\classes



通过指定bootclasspath，看下是否会去加载某个类

能否让extClassLoader去加载自己的类

如何让bootstrapclassloader去loadclass呢

​	"D:\zssise"

​	

findLoadedClass

```java
Class cl = Class.forName("java.lang.ClassLoader");
Method method = cl.getDeclaredMethod("findLoadedClass", new Class[] { String.class });
        method.setAccessible(true);
Thread.currentThread().getContextClassLoader().loadClass("sun.security.ec.CurveDB"); // 加载
method.invoke(Thread.currentThread().getContextClassLoader().getParent(),"sun.security.ec.CurveDB") // method应该传入的这个classloader是具体的classloader对象，判断sun.security.ec.CurveDB是否被加载，可以调用ex
 
```






![1557678014878](https://github.com/cjf1002361126/photoben/blob/master/慕课网-剑指java面试/1557678014878.png?raw=true)

![1557678115986](https://github.com/cjf1002361126/photoben/blob/master/慕课网-剑指java面试/1557678115986.png?raw=true)

因Class.loaderClass得到的class还没有链接，初始化，所以Classloader具有延时加载效果，类的初始化不需要在加载的时候就进行，加快加载时间。

而，对于mysql驱动类Driver里面有段static代码，采用Class.forName("com.mysql.jdbc.Driver")，可以快速生成Driver对象。 



![1557928378138](https://github.com/cjf1002361126/photoben/blob/master/慕课网-剑指java面试/1557928378138.png?raw=true)

getMethod：返回所有public的方法，包括继承和实现的父类public方法

getDeclaredMethod：获取当前类的所有属于自己声明的方法，private,public,default,protected,**从父类中继承的不算，实现接口的方法由于有声明所以包括在内。**



### 内存模型



![1557937144429](https://github.com/cjf1002361126/photoben/blob/master/慕课网-剑指java面试/1557937144429.png?raw=true)



#### 程序计数器

每个线程对应一个程序计数器，所以是线程私有区域，不会发生内存泄露。

![1557937510243](https://github.com/cjf1002361126/photoben/blob/master/慕课网-剑指java面试/1557937510243.png?raw=true)



#### 虚拟机栈

一个线程对应一个虚拟机栈，每个方法被执行时会创建一个栈帧。

所以，多递归会出现栈溢出(StackoverflowError)；如果虚拟机栈过多会引发内存溢出（OutOfMemoryError）;

虚拟机栈过多的情况：一直创建线程，且线程一直不停止，一直占用内存。则会出现虚拟机栈内存溢出。

![1557937808495](https://github.com/cjf1002361126/photoben/blob/master/慕课网-剑指java面试/1557937808495.png?raw=true)

![1557937912269](https://github.com/cjf1002361126/photoben/blob/master/慕课网-剑指java面试/1557937912269.png?raw=true)

![1557938053132](https://github.com/cjf1002361126/photoben/blob/master/慕课网-剑指java面试/1557938053132.png?raw=true)


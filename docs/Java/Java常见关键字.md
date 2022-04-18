Java 中的关键字很多，每个关键字都代表着不同场景下的不同含义，接下来挑选几个比较重要的关键字介绍。

# 一：static关键字

### 1.1 static修饰属性

static可修饰`属性`，`方法`，`方法块`，一旦被修饰，代表所被修饰的东西属于`类本身而不属于实例`，换句话来说，这些东西在类范围内是共享的，谁都可以访问，这时候需要注意`并发读写`的问题。

- 当static修饰属性、方法时，代表该属性、方法属于类本身，与实例无关，被修饰的东西在类加载时就会初始化，而不是等到创建实例的时候
- 当static修饰代码块时，叫做静态代码块，也是在类加载时就会初始化，常用于在类启动前做一些初始化操作

注：被static修饰的属性和方法属于类本身，与实例无关，虽然破坏了面向对象的封装性，但易于访问，适合一些与实例无关的属性和方法。

 ### 1.2 static初始化时机

static修饰的对象属于类，因此加载时机也是类初始化时，由于此时实例还未初始化，因此static方法不能调用实例属性，static代码块同理。

```java
public class StaticTest1 {

    static {
        System.out.println("父类静态代码块加载！");
    }

    public static ArrayList staticAttribute = new ArrayList(){{
        System.out.println("父类静态变量加载！");
    }};

    public static void staticMethod(){
        System.out.println("父类静态方法加载！");
    }

    public StaticTest1(){
        System.out.println("父类构造方法加载！");
    }
}

public class StaticTest2 extends StaticTest1{

    public static ArrayList staticAttribute = new ArrayList(){{
        System.out.println("子类静态变量加载！");
    }};

    static {
        System.out.println("子类静态代码块加载！");
    }

    public static void staticMethod(){
        System.out.println("子类静态方法加载！");
    }

    public StaticTest2(){
        super();
        System.out.println("子类构造方法加载！");
    }

    public static void main(String[] args) {
        new StaticTest2();
    }

}
```

以上测试代码的输出结果为：

```properties
父类静态代码块加载！
父类静态变量加载！
子类静态变量加载！
子类静态代码块加载！
父类构造方法加载！
子类构造方法加载！
```

以上可以看出：

- static先加载，同被static修饰的代码按先后顺序加载
- 父类优先于子类加载
- 静态代码加载完成后再进行实例初始化，执行构造方法

# 二：final关键字

final表示不可变的，可修饰类，属性，方法：

- final修饰类时，表明该类无法被继承
- final修饰属性时，表明该属性引用不可变
- final修饰方法时，表明该方法无法被重写

注：当final修饰属性时，引用不可变不代表属性不可变，如ArrayList，Map等容器属性，虽然容器引用不可变，但容器里的元素是可以修改的。

# 三：try/catch/finally关键字

try、catch、finally是Java异常处理的关键字，try用来包括可能出现异常的代码，catch用来捕获异常，finally里是无论异常是否捕获到都会执行的代码。

- 执行顺序为try -> catch -> finally -> return
- `catch可以捕获多种异常，但只会匹配其中某一种，当匹配到后即停止匹配`，开始执行finally代码，因此捕获时，`应先捕获子类异常后捕获父类异常`

注：正常情况下，finally代码都会被执行，`除非在finally执行之前出现Jvm崩溃或者手动调用System.exit()等`，示例代码如下：

```java
public class FinallyTest {


    public static Integer FinallyTest(){
        try {
            System.out.println("try executed...");
            throw new Exception();
        }catch (Exception e){
            System.out.println("catch executed...");
            System.exit(0);
        }finally {
            System.out.println("finally executed...");
        }
        System.out.println("func return...");
        return 1;
    }

    public static void main(String[] args) {
        FinallyTest.FinallyTest();
    }

}
```
# 三：volatile关键字

- volatile介绍

volatile是Jvm提供的一个关键字，用于保证多线程编程的线程安全。

volatile只能修饰属性，一般用来修饰共享变量，`可以保证该变量在多线程之间的可见性和禁止指令重排序`。

- volatile原理

volatile是如何保证可见性以及禁止指令重排的呢？

`可见性是由于多核CPU缓存导致的`，volatile的内存语义即：`遇到volatile修饰的共享变量时，当该变量值被修改时，内存会主动通知所有CPU缓存，该值已经失效，应该从内存中的重新拉取`。

注：volatile详细原理参考[Java并发](https://github.com/Rocks526/Java-Notes/blob/master/docs/Java/Java并发编程/并发编程学习路线.md)章节。

# 四：transient关键字

- transient介绍

transient是Java提供的一个序列化相关的关键字，可以用来修饰类属性和实例属性，`表明该属性序列化时忽略`。

- transient原理

transient其实和Serializable接口类似，本身并没有意义，更多类似于一个标志，实现的逻辑在序列化的底层代码中（ObjectOutputStream/ObjectInputStream），当序列化时某个属性被transient修饰时，就将该字段忽略。

- transient应用

一般用来在`网络传输时忽略掉部分不需要的大字段或者敏感信息`。

注：在ArrayList等数组类容器中，Java为了优化性能减少内存消耗，对存储数据的数组用transient修饰，不让其序列化，自己手动实现writeObject方法重写序列化方案，只序列化已经存入的元素，忽略数组后面的空值。

# 五：default关键字

- default介绍

default 关键字一般会用在接口的方法上，意思是`对于该接口，子类是无需强制实现的，但自己必须有默认实现`。

- default应用

在Jdk8中，容器相关类新增了很多方法，为了向前兼容使用了default关键字，父类提供默认实现，子类无需修改原有代码，需要实现的子类手动重写。
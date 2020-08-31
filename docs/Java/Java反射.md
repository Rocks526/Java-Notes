- [反射介绍](#fsjs)
- [反射基本操作](#fsjbcz)
- [反射的应用](#fsdyy)

------

# <a id="fsjs">反射介绍</a>

### 反射概述

 JAVA反射机制是在运行状态中，对于任意一个类，都能够知道这个类的所有属性和方法；对于任意一个对象，都能够调用它的任意一个方法和属性；这种动态获取的信息以及动态调用对象的方法的功能称为java语言的反射机制。

> 运行时动态修改类信息的机制叫做反射

### Class对象

在Java中万物皆对象，实际上，我们创建的每一个类也都是对象，即类本身是java.lang.Class类的实例对象。这个实例对象称之为类对象，也就是Class对象。

- **Class 类的实例对象表示正在运行的 Java 应用程序中的类和接口**。也就是jvm中有很多的实例，每个类都有唯一的Class对象。
- Class 类没有公共构造方法。**Class 对象是在加载类时由 Java 虚拟机自动构造**的。也就是说我们不需要创建，JVM已经帮我们创建了。
- Class 对象用于提供类本身的信息，比如有几种构造方法， 有多少属性，有哪些普通方法

获取Class对象的三种方式：

- Class.forName("类全限定名")：根据类的全限定名动态加载类进入JVM
- 类名.class
- 类对象.getClass()

在一个JVM中，一种类，只会有一个类对象存在。所以以上三种方式取出来的类对象，都是一样。（此处准确是在同一ClassLoader下,只有一个类对象）

> 三种方式中，常用第一种，第二种需要导入类的包，依赖太强，不导包就抛编译错误。第三种对象都有了也就不需要反射了。一般都第一种，一个字符串可以传入也可写在配置文件中等多种方法。
>
> 第二种和第三种方式都属于编译时加载类，编译器会检查该类是否存在，必须存在才能编译通过。
>
> 第一种属于运行时加载，编译器不会去检查类是否存在，可以编译完成后创建，只要在运行时能搜索到该类即可。

在Java中，除了自定义的对象之外，基本类型/包装类型/数组等都有类对象。

```java
    public static void main(String[] args) {
        Class intClass = int.class;
        Class integerClass = Integer.class;
        Class arrayClass = int[].class;
        System.out.println(intClass.getName());
        System.out.println(integerClass.getName());
        System.out.println(arrayClass.getName());
    }
---------------------------------------------------
int
java.lang.Integer
[I
```

# <a id="fsjbcz">反射基本操作</a>

> 反射的本质即通过Class在运行时动态加载类，更新类的信息，因此反射的所有操作也都是基于Class对象的

- 利用反射机制创建对象
  - public Constructor[] getConstructors()：所有"公有的"构造方法
  - public Constructor[] getDeclaredConstructors()：获取所有的构造方法(包括私有、受保护、默认、公有)
  -  public Constructor getConstructor(Class… parameterTypes): 获取单个的"公有的"构造方法
  - public Constructor getDeclaredConstructor(Class…parameterTypes):获取"某个构造方法"可以是私有的，或受保护、默认、公有

实体User：

```java
package com.rocks.reflect;

public class User {

    private Integer id;

    public String name;

    public Integer age;

    public void run(){
        System.out.println("User is runing...");
    }

    private Long run(Long distance){
        System.out.println("User runed " + distance + "米...");
        return distance;
    }

    public User() {
    }

    public User(Integer id, String name, Integer age) {
        this.id = id;
        this.name = name;
        this.age = age;
    }

    public Integer getId() {
        return id;
    }

    public void setId(Integer id) {
        this.id = id;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public Integer getAge() {
        return age;
    }

    public void setAge(Integer age) {
        this.age = age;
    }

    @Override
    public String toString() {
        return "User{" +
                "id=" + id +
                ", name='" + name + '\'' +
                ", age=" + age +
                '}';
    }
}
```

反射示例：

```java
    //通过反射创建对象
    public static void main(String[] args) throws ClassNotFoundException, NoSuchMethodException, IllegalAccessException, InvocationTargetException, InstantiationException {
        //1.获取Class对象
        Class aclass = Class.forName("com.rocks.reflect.User");
        //2.获取构造器对象
        Constructor constructor = aclass.getConstructor();
        Constructor constructor1 = aclass.getConstructor(Integer.class, String.class, Integer.class);
        //3.构造示例对象 可以强转User
        Object o = constructor.newInstance();
        Object o1 = constructor1.newInstance(1,"rocks",22);
        System.out.println(o);
        System.out.println(o1);
    }
---------------------------------------------
User{id=null, name='null', age=null}
User{id=1, name='rocks', age=22}
```

- 利用反射机制获取对象属性，修改对象属性
  - getField只能获取public的，包括从父类继承来的字段。
  - getDeclaredField 可以获取本类所有的字段，包括private的，但是 不能获取继承来的字段。 (注：这里只能获取到private的字段，但并不能访问该private字段的值,除非加上setAccessible(true))
  - getFields获取所有public
  - getDeclaredFields获取本类所有字段，包括private

```java
    public static void main(String[] args) throws ClassNotFoundException, NoSuchFieldException, NoSuchMethodException, IllegalAccessException, InvocationTargetException, InstantiationException {
        //1.获取Class对象
        Class aclass = Class.forName("com.rocks.reflect.User");
        //2.获取构造器对象
        Constructor constructor = aclass.getConstructor();
        //3.构造示例对象 可以强转User
        Object o = constructor.newInstance();
        System.out.println(o);
        //4.获取对象属性并设置
        Field name = aclass.getField("name");
        Field id = aclass.getDeclaredField("id");
        name.set(o,"rocks");
        //5.设置私有属性需要暴力访问
        id.setAccessible(true);
        id.set(o,1);
        System.out.println(o);
    }
-----------------------------------------------
User{id=null, name='null', age=null}
User{id=1, name='rocks', age=null}
```

- 利用反射调用对象的方法

```java
    public static void main(String[] args) throws ClassNotFoundException, NoSuchFieldException, NoSuchMethodException, IllegalAccessException, InvocationTargetException, InstantiationException {
        //1.获取Class对象
        Class aclass = Class.forName("com.rocks.reflect.User");
        //2.获取构造器对象
        Constructor constructor = aclass.getConstructor();
        //3.构造示例对象 可以强转User
        Object o = constructor.newInstance();
        //4.获取方法
        Method run = aclass.getMethod("run");
        //5.执行方法 获得方法的返回结果 如果无返回结果 则返回null
        Object res = run.invoke(o);
        System.out.println(res);
        //6.获取私有方法
        Method run1 = aclass.getDeclaredMethod("run", Long.class);
        //7.私有方法执行需要设置暴力访问
        run1.setAccessible(true);
        Object res2 = run1.invoke(o, 1000L);
        System.out.println(res2);
    }
----------------------------------------------
User is runing...
null
User runed 1000米...
1000 
```

# <a id="fsdyy">反射的应用</a>

- 利用反射破解集合的泛型

```java
    public static void main(String[] args) throws NoSuchMethodException, InvocationTargetException, IllegalAccessException {

        //创建Integer类型的List
        ArrayList<Integer> arrayList = new ArrayList<>();
        arrayList.add(1);
        arrayList.add(2);
        //通过反射绕过泛型限制  --》  泛型只在编译期有效  编译器会将泛型消除
        Class aClass = arrayList.getClass();
        Method method = aClass.getMethod("add", Object.class);
        method.invoke(arrayList,"add str by reflect");
        System.out.println(arrayList);
        for (Integer num : arrayList){
            System.out.println(num);
        }
    }
---------------------------------------------
[1, 2, add str by reflect]
1
2
Exception in thread "main" java.lang.ClassCastException: java.lang.String cannot be cast to java.lang.Integer
```

利用反射绕过编译器的泛型检查可以将其他类型加入List，如果for遍历会抛出类型转换异常。
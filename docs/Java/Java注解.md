- [注解介绍](#zjjs)
- [注解分类](#zjfl)
- [自定义注解](#zdyzj)
- [注解应用](#zjyy)

------

# <a id="zjjs">注解介绍</a>

#### Java注解简介

Java 注解（Annotation）又称 Java 标注，是 JDK5.0 引入的一种注释机制。

Java 语言中的类、方法、变量、参数和包等都可以被标注。和 Javadoc 不同，Java 标注可以通过反射获取标注内容。在编译器生成类文件时，标注可以被嵌入到字节码中。Java 虚拟机可以保留标注内容，在运行时可以获取到标注内容 。 当然它也支持自定义 Java 标注。

> 参考:https://www.runoob.com/w3cnote/java-annotation.html

#### 常见的注解

- @Override - 检查该方法是否是重写方法。如果发现其父类，或者是引用的接口中并没有该方法时，会报编译错误。
- @Deprecated - 标记过时方法。如果使用该方法，会报编译警告。
- @SuppressWarnings - 指示编译器去忽略注解中声明的警告。

- @Autowired：Spring自动注入Bean注解

# <a id="zjfl">注解分类</a>

#### 按照运行机制分类

- 源码注解：注解只在源码中存在，编译成class文件就不存在了
- 编译时注解：注解在源码和class文件都存在，JDK自带的@Override，@Deprecated，@SuppressWarnings都属于此类
- 在运行时可以通过反射获取，影响程序逻辑

#### 按照来源分类

- Jdk自带注解

  - @Override，@Deprecated，@SuppressWarnings

- 第三方注解

  - @Autowired，@Service，@Repository，@RequestBody等

  > 第三方注解也是自定义注解的一种

- 自定义注解

- 元注解

  - @Retention - 注解的生命周期。
  - @Documented - 生成Javadoc时包含注解。
  - @Target - 注解可以修饰的目标。
  - @Inherited - 允许子类继承注解。

  > 注解继承：只会继承class，不会继承interface的注解，而且只会继承类上的注解，不会继承方法和属性上的注解

# <a id="zdyzj">自定义注解</a>

#### 自定义注解

- 使用@Interface关键字标识这是一个注解

- 成员以无参无异常的方式声明
- 可以使用default关键字给成员指定一个默认值
- 成员类型是受限的，可以使用原始类型、String、Class、Annotation、Enumeration
- 如果注解只有一个成员，成员名应起名为value，使用时可以忽略成员名
- 注解类可以没有成员，如果没有成员，称为标识注解

#### 自定义注解示例

```java
//可以修饰类 属性 方法
@Target({ElementType.TYPE,ElementType.FIELD,ElementType.METHOD})
@Retention(RetentionPolicy.RUNTIME)  //运行时注解
@Inherited    //注解可以被子类继承
@Documented   //生成javadoc时带上注解
public @interface MyAnnotation {

    String desc() default "这是一个自定义注解";

    String author() default "Rocks526";

    int age() default 22;

}
```

ElementType可选类型：

- CONSTRUCTOR:用于描述构造器　
- FIELD:用于描述属性　　　　
- LOCAL_VARIABLE:用于描述局部变量 
- METHOD:用于描述方法 
- PACKAGE:用于描述包 
- PARAMETER:用于描述参数 
- TYPE:用于描述类、接口(包括注解类型) 或enum声明

- TYPE_PARAMETER:表示该注解能使用在自定义类型参数（参数的自定义类型可以是javaBean或者枚举等）的声明语句中

- TYPE_USE:表示该注解能使用在使用类型的任意语句中。

> 后两个类型是JDK8新增，参考：https://blog.csdn.net/bluuusea/article/details/79997107

RetentionPolicy注解生命周期：

- SOURCE：注解只存在源码中，编译后消失
- CLASS：注解存在源码和编译后的class文件中，但不能通过反射操作
- RUNTIME：注解存在源码和编译后的class文件中，可以通过反射操作

#### 使用自定义注解

```java
@MyAnnotation(age = 23)
public class Demo1 {
    
    @MyAnnotation(desc = "这是一个属性")
    private String DemoField;
    
    @MyAnnotation(author = "Rocks")
    private String DemoFunction(){ 
        return null;   
    }
    
}
```

#### 自定义注解解析器

注解解析器：通过反射获取类、函数或者属性上的**运行时**注解信息，从而实现动态控制程序运行的逻辑。

```java
    public static void main(String[] args) throws ClassNotFoundException {

        //获取Class
        Class<?> aClass = Class.forName("Demo1");
        //获取注解
//        Annotation[] annotations = aClass.getAnnotations();
        MyAnnotation annotation = aClass.getAnnotation(MyAnnotation.class);
        System.out.println(annotation.age());
        System.out.println(annotation.author());
        System.out.println(annotation.desc());
    }
-------------------------------------------------
23
Rocks526
这是一个自定义注解
```

# <a id="zjyy">注解应用</a>

- 模仿Jpa，根据实体信息自动生成SQL

```java
@Target({ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Inherited
@Documented
public @interface Table {
    //表名
    String value();
}
@Target({ElementType.FIELD})
@Retention(RetentionPolicy.RUNTIME)
@Inherited
@Documented
public @interface Column {
    //列名
    String value();
}
@Table("user")
public class User {

    @Column("id")
    private Long id;

    @Column("user_name")
    private String userName;

    @Column("password")
    private String password;
}
```

```java
public class Demo2 {

    public static void main(String[] args) throws IllegalAccessException {

        //根据用户信息生成查询SQL
        User user = new User();
        user.setId(1l);
        user.setUserName("root");
        user.setPassword("admin1234");
        String sql = query(user);
        System.out.println(sql);
    }

    private static String query(User user) throws IllegalAccessException {
        // 通过反射获取表名
        Class<? extends User> aClass = user.getClass();
        Table tableAnnotation = aClass.getAnnotation(Table.class);
        String tableName = tableAnnotation.value();
        StringBuilder sql = new StringBuilder();
        // 添加where 1=1 防止后续没有筛选条件报错
        sql.append("select * from ")
                .append(tableName)
                .append(" where 1=1 ");
        // 获取所有字段 获取列名
        Field[] declaredFields = aClass.getDeclaredFields();
        for (Field field : declaredFields){
            field.setAccessible(true);
            Object value = field.get(user);
            Column fieldAnnotation = field.getAnnotation(Column.class);
            String columnName = fieldAnnotation.value();
            if (value != null){
                sql.append(" and ").append(columnName)
                        .append(" = ").append(value);
            }
        }
        return sql.toString();
    }
}
-----------------------------------------------
select * from user where 1=1  and id = 1 and user_name = root and password = admin1234
```












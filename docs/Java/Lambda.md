- [Lambda](#lambda)

- [Stream](#stream)

# <a id="lambda">Lambda</a>

### 简介

Jdk8引入了函数式编程风格，Lambda 表达式也可称为闭包，可以简单理解为一种匿名函数的代替，Lambda 允许把函数作为一个方法的参数（函数作为参数传递进方法中）。使用 Lambda 表达式可以使代码变的更加简洁紧凑。

Lambda表达式还增强了集合库。 Java SE 8添加了2个对集合数据进行批量操作的包: java.util.function 包以及java.util.stream 包。 流(stream)就如同迭代器(iterator),但附加了许多额外的功能。 总的来说,lambda表达式和 stream 是自Java语言添加泛型(Generics)和注解(annotation)以来最大的变化。

### 语法格式

lambda 表达式的语法格式如下：

```java
(parameters) -> expression
(parameters) -> { statements; }
```

以下是lambda表达式的重要特征:

- 可选类型声明：不需要声明参数类型，编译器可以统一识别参数值。
- 可选的参数圆括号：一个参数无需定义圆括号，但多个参数需要定义圆括号。
- 可选的大括号：如果主体包含了一个语句，就不需要使用大括号。
- 可选的返回关键字：如果主体只有一个表达式返回值则编译器会自动返回值，大括号需要指定明表达式返回了一个数值。

### Lambda表达式示例

```java
// 1. 不需要参数,返回值为 5  
() -> 5  
  
// 2. 接收一个参数(数字类型),返回其2倍的值  
x -> 2 * x  
  
// 3. 接受2个参数(数字),并返回他们的差值  
(x, y) -> x – y  
  
// 4. 接收2个int型整数,返回他们的和  
(int x, int y) -> x + y  
  
// 5. 接受一个 string 对象,并在控制台打印,不返回任何值(看起来像是返回void)  
(String s) -> System.out.print(s)  
    
//6. 没有参数 逻辑复杂
() -> {
    System.out.print("first step");  
    System.out.print(" step");  
}
```

### 函数式接口

函数式接口需要满足以下要求：

- 接口中只有一个抽象方法
- Java8的函数式接口都要求添加注解：@FunctionInterface

自定义函数式接口如下：

```java
package Jdk8.lambda;

/**
 * 自定义函数式接口 文件处理类
 */
@FunctionalInterface
public interface FileConsumer {

    void fileHandler(String fileContent);

}
```

```java
package Jdk8.lambda;

import java.io.*;
import java.util.function.Consumer;

/**
 * 函数式接口测试
 */
public class FileService {

    // 使用自定义函数式接口
    public void getFile(String filePath,FileConsumer fileConsumer) throws Exception {
        // 获取一个文件
        BufferedReader bufferedReader = new BufferedReader(new InputStreamReader(new FileInputStream(new File(filePath))));
        StringBuilder fileContent = new StringBuilder();
        String line = "";
        while ((line = bufferedReader.readLine()) != null){
            fileContent.append(line + "\n");
        }

        // 对数据做处理
        fileConsumer.fileHandler(fileContent.toString());
    }


    // 使用Jdk自带的函数式接口
    public void getFile(String filePath, Consumer<String> fileConsumer) throws Exception {
        // 获取一个文件
        BufferedReader bufferedReader = new BufferedReader(new InputStreamReader(new FileInputStream(new File(filePath))));
        StringBuilder fileContent = new StringBuilder();
        String line = "";
        while ((line = bufferedReader.readLine()) != null){
            fileContent.append(line + "\n");
        }

        // 对数据做处理
        fileConsumer.accept(fileContent.toString());
    }


    public static void main(String[] args) throws Exception {

        FileService fileSevice = new FileService();
        fileSevice.getFile("E:\\git-repository\\Jdk-demo\\src\\main\\java\\Jdk8\\lambda\\Lambda.java", new FileConsumer() {
            @Override
            public void fileHandler(String fileContent) {
                System.out.println(fileContent);
            }
        });

        fileSevice.getFile("E:\\git-repository\\Jdk-demo\\src\\main\\java\\Jdk8\\lambda\\Lambda.java", new Consumer<String>() {
            @Override
            public void accept(String s) {
                System.out.println(s);
            }
        });

    }

}
```

除了自定义函数式接口外，Jdk提前定义好了一些比较抽象的接口供我们使用，主要在java.util.function包下，常用的有以下这些：

| 接口                | 参数  | 返回类型 | 描述                                                       |
| ------------------- | ----- | -------- | ---------------------------------------------------------- |
| Predicate\<T\>      | T     | boolean  | 用于判别一个对象。如求一个人是否为男性。                   |
| Consumer\<T\>       | T     | void     | 用于接收一个对象进行处理但没有返回。比如打印一个人的名字。 |
| Function\<T,R\>     | T     | R        | 将一个对象转换成不同类型的对象                             |
| Supplier\<T\>       | None  | T        | 提供一个对象                                               |
| UnaryOperator\<T\>  | T     | T        | 接收对象并返回同类型对象                                   |
| BinaryOperator\<T\> | (T,T) | T        | 接收两个同类型对象，并返回一个同类型对象                   |

### 方法引用

方法引用是Lambda调用特定方法的一种快捷写法。

```java
package Jdk8.lambda;

import java.util.function.Consumer;

/**
 * 方法引用
 */
public class MethodRefrence {

    public static void main(String[] args) {

        // 静态方法引用
        Consumer<String> consumer = (String str) -> Integer.parseInt(str);
        Consumer<String> consumer2 = Integer::parseInt;

        // 实例类型方法的引用
        Consumer<String> consumer3 = str -> str.length();
        Consumer<String> consumer4 = String::length;

        // 外部对象的实例方法引用
        StringBuilder sb = new StringBuilder();
        Consumer<String> consumer5 = str -> sb.append(str);
        Consumer<String> consumer6 = sb::append;

    }

}
```

# <a id="stream">Stream</a>

### 简介

Stream API是Jdk8引入的函数式处理集合的API，可以将基础操作连接起来，完成复杂的数据处理流水线，Stream里提供了透明的并行处理。

Stream的API可以分为构建流、中间处理操作和流的收集三大类。

### 构建流

```java
package Jdk8.stream;

import java.io.IOException;
import java.nio.file.Files;
import java.nio.file.Paths;
import java.util.ArrayList;
import java.util.Arrays;
import java.util.stream.IntStream;
import java.util.stream.Stream;

/**
 * 流的创建
 */
public class Source {

    public static void main(String[] args) throws IOException {

        // 1.通过数值直接构建流
        Stream stream = Stream.of(1,2,3,4,5);
        stream.forEach(System.out::println);

        // 2.通过数组构建流
        int[] numbers = {1,2,3,4,5};
        IntStream intStream = Arrays.stream(numbers);
        intStream.forEach(System.out::println);

        // 3.通过本地文件生成流
        String filePath = "E:\\git-repository\\Jdk-demo\\src\\main\\java\\Jdk8\\lambda\\MethodRefrence.java";
        // Paths.get(filePath)可以设置多个文件URL
        Stream<String> lines = Files.lines(Paths.get(filePath));
        lines.forEach(System.out::println);

        // 4.通过函数生成流 ==>  无限流
        // 从0开始 不断加2生成 遍历时取前一百 否则会一直打印
        Stream<Integer> iterate = Stream.iterate(0, n -> n + 2);
        iterate.limit(100).forEach(System.out::println);

        Stream<Double> generate = Stream.generate(Math::random);
        generate.limit(100).forEach(System.out::println);
        
        // 5.通过集合容器获得Stream
        ArrayList<String> strings = new ArrayList<>();
        Stream<String> stream1 = strings.stream();
    }
    
}
```

### 流的转换操作

Stream的API提供了很多流的转换处理操作，主要分为中间操作和终端操作。

- 中间操作

中间操作主要是对数据做一些处理和状态转换，包括无状态操作和有状态操作，无状态操作会让每个元素把所有处理步骤执行完再处理下一个元素，而有状态操作会在这一步暂时截断，将所有元素都执行这个步骤，之后再一个一个执行其他步骤。

- 终端操作

终端操作主要分为非短路操作和短路操作，非短路操作会对流的每个元素都进行处理，短路操作当遇到匹配的元素后，后续的元素都不会再处理。

![image-20201231151709140](http://rocks526.top/lzx/image-20201231151709140.png)

```java
package Jdk8.stream;

import java.util.*;

public class Operator {

    private static ArrayList<User> users;

    static {
        users = new ArrayList<>();
        users.add(new User().setId(1L).setAge(22).setName("Rocks").setPass("123").setSex(1));
        users.add(new User().setId(2L).setAge(32).setName("Lucy").setPass("123").setSex(1));
        users.add(new User().setId(3L).setAge(21).setName("Timi").setPass("123").setSex(0));
        users.add(new User().setId(4L).setAge(14).setName("Jack").setPass("123").setSex(1));
        users.add(new User().setId(5L).setAge(16).setName("Jim").setPass("123").setSex(0));
        users.add(new User().setId(6L).setAge(17).setName("DiDi").setPass("123").setSex(1));
        users.add(new User().setId(7L).setAge(22).setName("Body").setPass("123").setSex(1));
        users.add(new User().setId(8L).setAge(23).setName("Jam").setPass("123").setSex(0));
        users.add(new User().setId(9L).setAge(22).setName("Rocks").setPass("123").setSex(1));
    }

    public static void main(String[] args) {

        // 1.filter 过滤掉不符合断言判断的数据
        users.stream().filter(user -> user.getAge()>20).forEach(System.out::println);

        // 2.map  将一个元素转换成另一个元素
        users.stream().map(user -> user.getName()).forEach(System.out::println);

        // 3.flatMap 将一个元素转换成流
        users.stream().flatMap(user -> Arrays.stream(user.getName().split(""))).forEach(System.out::println);

        // 4.peek 对流中元素进行遍历操作，与forEach类似，但不会销毁流元素
        // 如果没有foreach的终端操作 peek的中间操作不会执行 stream懒加载
        users.stream().peek(user -> System.out.println(user.getId())).forEach(System.out::println);

        // 5.sort 对流中元素进行排序，可选则自然排序或指定排序规则。有状态操作
        users.stream().sorted(Comparator.comparing(User::getAge)).forEach(System.out::println);

        // 6.对流元素进行去重。有状态操作
        users.stream().map(User::getAge).distinct().forEach(System.out::println);

        // 7.skip 跳过前N条记录。有状态操作
        // 8.limit 截断前N条记录。有状态操作
        users.stream().sorted(Comparator.comparing(User::getAge)).skip(1).limit(3).forEach(System.out::println);

        // 9.allMatch 终端操作，短路操作。 判断是否有某个元素匹配 遇到匹配的后不再遍历后续元素
        boolean isHasMan = users.stream().peek(System.out::println).allMatch(user -> user.getSex().equals(1));
        System.out.println(isHasMan);

        // 10.anyMatch 终端操作，短路操作。 任何一个元素匹配，返回true
        boolean isHasAge = users.stream().anyMatch(user -> user.getAge() >= 20);
        System.out.println(isHasAge);

        // 11.noneMatch 任何元素都不匹配，返回true
        boolean isHasNameRocks = users.stream().noneMatch(user -> user.getName().equals("Rocks"));
        System.out.println(isHasNameRocks);

        // 12.findFirst  返回第一个元素
        Optional<User> first = users.stream().findFirst();
        System.out.println(first.get());

        // 13.findAny 返回任意一个元素 有可能返回不是第一个(并行操作 但性能比findFirst高)
        Optional<User> any = users.stream().findAny();
        System.out.println(any.get());

        // 14.count 获取数量
        long count = users.stream().count();
        System.out.println(count);

        // 15.max 获取最大
        OptionalInt max_age = users.stream().mapToInt(user -> user.getAge()).max();
        System.out.println(max_age);

        // 16.min 获取最小
        OptionalInt min_age = users.stream().mapToInt(User::getAge).min();
        System.out.println(min_age);
    }

}
```

### 流的收集

转换操作的终端操作虽然可以返回结果，但都是返回计算的结果，如果想要将转换后的数据收集变为list，则需要流的收集操作。

流的收集操作会将流中的元素累积成一个结果返回。通过collect方法进行收集。

> collect方法是流的收集方法，需要传入一个实现了Collector接口的实现类，Collections是Jdk提供的一个集合工具类，里面提供了很多实现了Collector接口的实现类供我们使用。

```java
package Jdk8.stream;

import java.util.ArrayList;
import java.util.List;
import java.util.Map;
import java.util.stream.Collectors;

/**
 * 流元素的收集
 */
public class Sink {

    private static ArrayList<User> users;

    static {
        users = new ArrayList<>();
        users.add(new User().setId(1L).setAge(22).setName("Rocks").setPass("123").setSex(1));
        users.add(new User().setId(2L).setAge(32).setName("Lucy").setPass("123").setSex(1));
        users.add(new User().setId(3L).setAge(21).setName("Timi").setPass("123").setSex(0));
        users.add(new User().setId(4L).setAge(14).setName("Jack").setPass("123").setSex(1));
        users.add(new User().setId(5L).setAge(16).setName("Jim").setPass("123").setSex(0));
        users.add(new User().setId(6L).setAge(17).setName("DiDi").setPass("123").setSex(1));
        users.add(new User().setId(7L).setAge(22).setName("Body").setPass("123").setSex(1));
        users.add(new User().setId(8L).setAge(23).setName("Jam").setPass("123").setSex(0));
        users.add(new User().setId(9L).setAge(22).setName("Rocks").setPass("123").setSex(1));
    }

    public static void main(String[] args) {
        // 收集所有sex为1的user
        List<User> sex1 = users.stream().filter(user -> user.getSex().equals(1)).collect(Collectors.toList());
        System.out.println(sex1);

        // 过滤所有年龄大于20的user 并根据性别分组
        Map<Integer, List<User>> collect = users.stream().filter(user -> user.getAge() > 20).collect(Collectors.groupingBy(User::getSex));
        System.out.println(collect);

        // 根据年龄是否大于18将user分区
        Map<Boolean, List<User>> collect1 = users.stream().collect(Collectors.partitioningBy(user -> user.getAge() > 18));
        System.out.println(collect1);
    }

}
```


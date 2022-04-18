异常指是`程序运行过程中可能出现的错误或者异常情况，通过异常机制可以在代码编写时实现对代码运行过程中可能产生的错误提前进行处理，增加程序的健壮性`。主流的编程语言，Java、Python、Go等都支持异常机制。

注：异常和中断有时候也可以作为一种线程间通知的机制。

# 一：Java的异常体系结构

![img](http://rocks526.top/lzx/upcnw9dbza.jpeg)

Java中的异常体系结构如上图所示：

- 定义一个基类Throwable作为异常和错误的超类
- 分为Error和Exception两大类
  - Error指`出现一些程序无法处理的系统错误，终止系统继续运行`
  - Exception指的是`出现一些程序可以处理的异常情况，程序可以捕获异常进行处理，系统可以恢复运行`
- 在Error和Exception下定义了许多子类，包含程序可能遇到的各种情况，如常见的SQL异常，IO异常，参数异常，堆栈异常等
- Exception还可以分为`运行期异常(RuntimeException)和检查期异常`
  - 运行期异常指的是`在程序运行时可能出现的状况，不可预知，可处理可不处理，一般是由程序运行逻辑出错导致的`
  - 检查期异常指的是`编译器可以检查的语法异常，可预知，必须进行处理，否则编译器会报错`
- 可以通过继承Exception或RuntimeException实现自定义异常

```java
public class MyException extends Exception {

    public MyException(){
        super();
    }

    public MyException(String msg){
        super(msg);
    }

}
public class Cat {
    
    private String name;

    private Integer age;

    public Cat(String name,Integer age) throws MyException {
        if (name.equals("") || age == null){
            throw new MyException("属性不可为空！");
        }else {
            this.name = name;
            this.age = age;
        }
    }


    public static void main(String[] args) {

        try {
            Cat mi = new Cat("", 2);
        } catch (MyException e) {
            System.out.println(e.getMessage());
        }
    }
    
}
```

# 二：Exception的处理

Java的异常处理有两种方案：

- 捕获异常并对已经情况进行处理，日志打印等
- 向上抛出异常，留给上层调用者处理，一直往上抛的话最后由Jvm处理

Java的异常处理涉及5个关键字，分别是：

- try：可能出现异常的代码
- catch：捕获异常进行处理，`可以多次捕获，但只能捕获一个进行处理，因此捕获顺序应该从子类到父类依次捕获`
- finally：无论是否发生异常，都会执行的逻辑，`一般用于资源释放`
- throw：手动抛出异常
- throws：定义于方法之上，表明该方法可能抛出的异常，方法调用方需要进行处理

捕获到的Throwable对象常用方法：

- getCause()：返回抛出异常的原因，如果 cause 不存在或未知，则返回 null
- getMessage()：返回异常的原因信息
- printStackTrace()：打印追踪到的堆栈信息

注：异常信息打印时，一定记得打印异常原因和和相关堆栈。

# 三：常见异常汇总

### Error

- NoClassDefFoundError：找不到class定义的错误，可能是类依赖的class文件或者jar包不存在
- StackOverflowError：深递归导致栈被耗尽而抛出的错误
- OutOfMemoryError：内存溢出异常

注：可以看到，这些错误都是运行期因为某些原因导致的，并且很严重，会导致系统崩溃，程序一般无法自行处理、恢复系统。

### RuntimeException

- NullPointerException：空指针引用异常
- ClassCastException：类型强制转换异常
- IllegalArgumentException：传递非法参数异常
- IndexOutOfBoundsException：数组下标越界异常
- NumberFormatException：数字格式异常

注：可以看到，这些运行时异常大多是由于程序编码错误导致的，因此一般不做捕获处理，应尽量减少这种bug。

### Checked Exception

- IOException：IO操作异常
- SQLException：SQL语句异常
- ClassNotFoundException：找不到指定class异常

注：可以看到，检查期异常大多是由于第三方引起的，程序无法自行恢复，但这些异常又不会引起严重的系统崩溃，因此需要捕获，由程序进行处理。
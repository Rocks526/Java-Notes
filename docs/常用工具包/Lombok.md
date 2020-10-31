- [Lombok简介](#jj)
- [Lombok实战](#sz)
- [Lombok原理](#yl)
- [Lombok优缺点](#yqd)
- [Lombok导致的Bug](#bug)

# <a id="jj">Lombok简介</a>

> Lombok官网：https://projectlombok.org/

Project Lombok is a java library that automatically plugs into your editor and build tools, spicing up your java.Never write another getter or equals method again, with one annotation your class has a fully featured builder, Automate your logging variables, and much more.

以上是Lombok官网的描述，Lombok是一个Java工具，可以通过注解简化get/set/equals等重复代码，优化Java。

Lombok有以下常用注解：

- @Data注解：在JavaBean中使用，这个注解包含范围最广，它包含getter/setter/equals/hashcode/tostring注解，即当使用当前注解时，会自动生成包含的所有方法
- @getter注解：在JavaBean中使用，使用此注解会生成对应的getter方法
- @setter注解：在JavaBean中使用，使用此注解会生成对应的setter方法
- @NoArgsConstructor注解：在JavaBean中使用，使用此注解会生成对应的无参构造方法
- @AllArgsConstructor注解：在JavaBean中使用，使用此注解会生成对应的全参构造方法
- @ToString注解：在JavaBean中使用，使用此注解会自动重写对应的toStirng方法
- @EqualsAndHashCode注解：在JavaBean中使用，使用此注解会自动重写对应的equals方法和hashCode方法
- @NonNull：在属性，方法，参数上使用，自动添加空值校验
- @Cleanup：在局部变量上使用，自动调用变量的close方法(方法名可指定)
- @Builder：在JavaBean中使用，自动生成构造者模式
- @Accessors(chain = true)：在JavaBean中使用，可以让set方法链式调用
- @Synchronized：在方法上使用，自动生成Synchronized锁
- @SneakyThrows：在方法和构造器上使用，自动生成try/catch捕捉异常
- @Slf4j：在需要打印日志的类中使用，当项目中使用了slf4j打印日志框架时使用该注解，会简化日志的打印流程，自动注入一个log对象
- @Log4j：在需要打印日志的类中使用，当项目中使用了log4j打印日志框架时使用该注解，会简化日志的打印流程，自动注入一个log对象

# <a id="sz">Lombok实战</a>

> 准备测试环境，Jdk8，Idea，Lombok插件，引入Maven依赖。
>
> ```
> <dependency>
>     <groupId>org.projectlombok</groupId>
>     <artifactId>lombok</artifactId>
>     <version>1.16.10</version>
>     <scope>compile</scope>
> </dependency>
> ```

- @Data

```java
@Data
public class User {

    private Long id;

    private String userName;

    private String password;

}
============================= 编译后 =======================================
public class User {
    private Long id;
    private String userName;
    private String password;

    public User() {
    }

    public Long getId() {
        return this.id;
    }

    public String getUserName() {
        return this.userName;
    }

    public String getPassword() {
        return this.password;
    }

    public void setId(Long id) {
        this.id = id;
    }

    public void setUserName(String userName) {
        this.userName = userName;
    }

    public void setPassword(String password) {
        this.password = password;
    }

    public boolean equals(Object o) {
        if (o == this) {
            return true;
        } else if (!(o instanceof User)) {
            return false;
        } else {
            User other = (User)o;
            if (!other.canEqual(this)) {
                return false;
            } else {
                label47: {
                    Object this$id = this.getId();
                    Object other$id = other.getId();
                    if (this$id == null) {
                        if (other$id == null) {
                            break label47;
                        }
                    } else if (this$id.equals(other$id)) {
                        break label47;
                    }

                    return false;
                }

                Object this$userName = this.getUserName();
                Object other$userName = other.getUserName();
                if (this$userName == null) {
                    if (other$userName != null) {
                        return false;
                    }
                } else if (!this$userName.equals(other$userName)) {
                    return false;
                }

                Object this$password = this.getPassword();
                Object other$password = other.getPassword();
                if (this$password == null) {
                    if (other$password != null) {
                        return false;
                    }
                } else if (!this$password.equals(other$password)) {
                    return false;
                }

                return true;
            }
        }
    }

    protected boolean canEqual(Object other) {
        return other instanceof User;
    }

    public int hashCode() {
        int PRIME = true;
        int result = 1;
        Object $id = this.getId();
        int result = result * 59 + ($id == null ? 43 : $id.hashCode());
        Object $userName = this.getUserName();
        result = result * 59 + ($userName == null ? 43 : $userName.hashCode());
        Object $password = this.getPassword();
        result = result * 59 + ($password == null ? 43 : $password.hashCode());
        return result;
    }

    public String toString() {
        return "User(id=" + this.getId() + ", userName=" + this.getUserName() + ", password=" + this.getPassword() + ")";
    }
}
```

-  @AllArgsConstructor

```java
@AllArgsConstructor
public class User {

    private Long id;

    private String userName;

    private String password;

}
============================= 编译后 =========================================
public class User {
    private Long id;
    private String userName;
    private String password;

    @ConstructorProperties({"id", "userName", "password"})
    public User(Long id, String userName, String password) {
        this.id = id;
        this.userName = userName;
        this.password = password;
    }
}
```

-  @Builder

```java
@Builder
public class User {

    private Long id;

    private String userName;

    private String password;

}
=========================== 编译后 ===============================================
public class User {
    private Long id;
    private String userName;
    private String password;

    User(Long id, String userName, String password) {
        this.id = id;
        this.userName = userName;
        this.password = password;
    }

    public static User.UserBuilder builder() {
        return new User.UserBuilder();
    }

    public static class UserBuilder {
        private Long id;
        private String userName;
        private String password;

        UserBuilder() {
        }

        public User.UserBuilder id(Long id) {
            this.id = id;
            return this;
        }

        public User.UserBuilder userName(String userName) {
            this.userName = userName;
            return this;
        }

        public User.UserBuilder password(String password) {
            this.password = password;
            return this;
        }

        public User build() {
            return new User(this.id, this.userName, this.password);
        }

        public String toString() {
            return "User.UserBuilder(id=" + this.id + ", userName=" + this.userName + ", password=" + this.password + ")";
        }
    }
}
```

- @Accessors(chain = true)

```java
@Data
@Accessors(chain = true)
public class User {
    
    private Long id;
    private String userName;
    private String password;
    
}
------------------------------------- 编译后 ----------------------------------------
public class User {
    private Long id;
    private String userName;
    private String password;

    public User() {
    }

    public Long getId() {
        return this.id;
    }

    public String getUserName() {
        return this.userName;
    }

    public String getPassword() {
        return this.password;
    }

    public User setId(final Long id) {
        this.id = id;
        return this;
    }

    public User setUserName(final String userName) {
        this.userName = userName;
        return this;
    }

    public User setPassword(final String password) {
        this.password = password;
        return this;
    }

    public boolean equals(final Object o) {
        if (o == this) {
            return true;
        } else if (!(o instanceof User)) {
            return false;
        } else {
            User other = (User)o;
            if (!other.canEqual(this)) {
                return false;
            } else {
                label47: {
                    Object this$id = this.getId();
                    Object other$id = other.getId();
                    if (this$id == null) {
                        if (other$id == null) {
                            break label47;
                        }
                    } else if (this$id.equals(other$id)) {
                        break label47;
                    }

                    return false;
                }

                Object this$userName = this.getUserName();
                Object other$userName = other.getUserName();
                if (this$userName == null) {
                    if (other$userName != null) {
                        return false;
                    }
                } else if (!this$userName.equals(other$userName)) {
                    return false;
                }

                Object this$password = this.getPassword();
                Object other$password = other.getPassword();
                if (this$password == null) {
                    if (other$password != null) {
                        return false;
                    }
                } else if (!this$password.equals(other$password)) {
                    return false;
                }

                return true;
            }
        }
    }

    protected boolean canEqual(final Object other) {
        return other instanceof User;
    }

    public int hashCode() {
        int PRIME = true;
        int result = 1;
        Object $id = this.getId();
        int result = result * 59 + ($id == null ? 43 : $id.hashCode());
        Object $userName = this.getUserName();
        result = result * 59 + ($userName == null ? 43 : $userName.hashCode());
        Object $password = this.getPassword();
        result = result * 59 + ($password == null ? 43 : $password.hashCode());
        return result;
    }

    public String toString() {
        return "User(id=" + this.getId() + ", userName=" + this.getUserName() + ", password=" + this.getPassword() + ")";
    }
}  
-------------------------------------- Demo  -------------------------------------------
public static void main(String[] args) {

        User user = new User()
                .setId(11L)
                .setUserName("Rocks")
                .setPassword("123456");

}
```

-  @NonNull

```java
@Data
public class User {

    private Long id;

    @NonNull
    private String userName;

    private String password;

    public void demo(@NonNull String msg,String desc){
        return;
    }
}
======================== 编译后 =============================================
public class User {
    private Long id;
    @NonNull
    private String userName;
    private String password;

    public void demo(@NonNull String msg, String desc) {
        if (msg == null) {
            throw new NullPointerException("msg");
        }
    }

    @ConstructorProperties({"userName"})
    public User(@NonNull String userName) {
        if (userName == null) {
            throw new NullPointerException("userName");
        } else {
            this.userName = userName;
        }
    }

    public Long getId() {
        return this.id;
    }

    @NonNull
    public String getUserName() {
        return this.userName;
    }

    public String getPassword() {
        return this.password;
    }

    public void setId(Long id) {
        this.id = id;
    }

    public void setUserName(@NonNull String userName) {
        if (userName == null) {
            throw new NullPointerException("userName");
        } else {
            this.userName = userName;
        }
    }

    public void setPassword(String password) {
        this.password = password;
    }

    public boolean equals(Object o) {
        if (o == this) {
            return true;
        } else if (!(o instanceof User)) {
            return false;
        } else {
            User other = (User)o;
            if (!other.canEqual(this)) {
                return false;
            } else {
                label47: {
                    Object this$id = this.getId();
                    Object other$id = other.getId();
                    if (this$id == null) {
                        if (other$id == null) {
                            break label47;
                        }
                    } else if (this$id.equals(other$id)) {
                        break label47;
                    }

                    return false;
                }

                Object this$userName = this.getUserName();
                Object other$userName = other.getUserName();
                if (this$userName == null) {
                    if (other$userName != null) {
                        return false;
                    }
                } else if (!this$userName.equals(other$userName)) {
                    return false;
                }

                Object this$password = this.getPassword();
                Object other$password = other.getPassword();
                if (this$password == null) {
                    if (other$password != null) {
                        return false;
                    }
                } else if (!this$password.equals(other$password)) {
                    return false;
                }

                return true;
            }
        }
    }

    protected boolean canEqual(Object other) {
        return other instanceof User;
    }

    public int hashCode() {
        int PRIME = true;
        int result = 1;
        Object $id = this.getId();
        int result = result * 59 + ($id == null ? 43 : $id.hashCode());
        Object $userName = this.getUserName();
        result = result * 59 + ($userName == null ? 43 : $userName.hashCode());
        Object $password = this.getPassword();
        result = result * 59 + ($password == null ? 43 : $password.hashCode());
        return result;
    }

    public String toString() {
        return "User(id=" + this.getId() + ", userName=" + this.getUserName() + ", password=" + this.getPassword() + ")";
    }
}
```

> 注意给某个属性添加@NonNull之后，@Data不再生成无参构造方法。

- @Cleanup：通过value参数可以指定调用的方法，默认close

```java
    public void test() throws IOException {
        @Cleanup
        FileInputStream fileInputStream = new FileInputStream(new File("../../resources/log4j.properties"));
        int byteRes = fileInputStream.read();
        System.out.println(byteRes);
    }
============================= 编译后 ==============================================
    public void test() throws IOException {
        FileInputStream fileInputStream = new FileInputStream(new File("../../resources/log4j.properties"));

        try {
            int byteRes = fileInputStream.read();
            System.out.println(byteRes);
        } finally {
            if (Collections.singletonList(fileInputStream).get(0) != null) {
                fileInputStream.close();
            }

        }

    }
```

- @SneakyThrows

```java
    @SneakyThrows
    public void test() {
        @Cleanup
        FileInputStream fileInputStream = new FileInputStream(new File("../../resources/log4j.properties"));
        int byteRes = fileInputStream.read();
        System.out.println(byteRes);
    }

    @SneakyThrows
    public void test2(){
        System.out.println("test2");
    }
==================== 编译后 ====================================================
    public void test() {
        try {
            FileInputStream fileInputStream = new FileInputStream(new File("../../resources/log4j.properties"));

            try {
                int byteRes = fileInputStream.read();
                System.out.println(byteRes);
            } finally {
                if (Collections.singletonList(fileInputStream).get(0) != null) {
                    fileInputStream.close();
                }

            }

        } catch (Throwable var7) {
            throw var7;
        }
    }

    public void test2() {
        try {
            System.out.println("test2");
        } catch (Throwable var2) {
            throw var2;
        }
    }
```

> 无法判断异常的具体类型，统一用Throwable去捕获。

# <a id="yl">Lombok原理</a>

Lombok的核心原理在于Jdk1.5之后引入的注解机制。Jdk为注解提供了两种解析方式：

- 运行时解析

> 运行时能够解析的注解，必须将@Retention设置为RUNTIME，这样就可以通过反射拿到该注解。java.lang.reflect反射包中提供了一个接口AnnotatedElement，该接口定义了获取注解信息的几个方法，Class、Constructor、Field、Method、Package等都实现了该接口。

- 编译时解析

编译时解析有两种机制，分别简单描述下：

1. Annotation Processing Tool

apt自JDK5产生，JDK7已标记为过期，不推荐使用，JDK8中已彻底删除，自JDK6开始，可以使用Pluggable Annotation Processing API来替换它，apt被替换主要有2点原因：

- api都在com.sun.mirror非标准包下
- 没有集成到javac中，需要额外运行

2. Pluggable Annotation Processing API

[JSR 269](https://jcp.org/en/jsr/detail?id=269)自JDK6加入，作为apt的替代方案，它解决了apt的两个问题，javac在执行的时候会调用实现了该API的程序，这样我们就可以对编译器做一些增强，javac执行的过程如下：

![image-20201013145837685](http://rocks526.top/lzx/image-20201013145837685.png)

Lombok本质上就是一个实现了“[JSR 269 API](https://www.jcp.org/en/jsr/detail?id=269)”的程序。在使用javac的过程中，它产生作用的具体流程如下：

1. javac对源代码进行分析，生成了一棵抽象语法树（AST）
2. 运行过程中调用实现了“JSR 269 API”的Lombok程序
3. 此时Lombok就对第一步骤得到的AST进行处理，找到@Data注解所在类对应的语法树（AST），然后修改该语法树（AST），增加getter和setter方法定义的相应树节点
4. javac使用修改后的抽象语法树（AST）生成字节码文件，即给class增加新的节点（代码块）

# <a id="yqd">Lombok优缺点</a>

优点

- 简化代码，删除繁琐的get/set/toString代码
- 自动生成样板代码，提高开发效率
- Bean修改后，自动生成新属性的get/set

缺点

- 降低代码可读性，难以调试
- 代码侵入性度，强迫IDE安装插件

# <a id="bug">Lombok导致的Bug</a>




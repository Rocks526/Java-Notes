### 一：代理设计模式介绍

代理是一种常用的设计模式，其目的就是为其他对象提供一个代理以控制对某个对象的访问。

代理类负责为委托类预处理消息，过滤消息并转发消息，以及进行消息被委托类执行后的后续处理。

![img](http://rocks526.top/lzx/20160807170246461)

简单结构示意图：

![img](http://rocks526.top/lzx/20160810080241992)

为了保持行为的一致性，代理类和委托类通常会实现相同的接口，所以在访问者看来两者没有丝毫的区别。

通过代理类这中间一层，能有效控制对委托类对象的直接访问，也可以很好地隐藏和保护委托类对象，同时也为实施不同控制策略预留了空间，从而在设计上获得了更大的灵活性。

> 代理模式：代理类持有委托类的一个引用，两者实现共同的接口，面向接口编程，代理类可以动态替换实现。方法调用时，代理类可以实现自己的逻辑，也可以调用委托类实现原方法。
>
> 静态代理：代理类需要自己实现，通过编译器生成class文件；
>
> 动态代理：代理类通过字节码技术自动生成，例如Jdk的动态代理和Cglib动态代理，开发者只需要编写代理方法逻辑即可。

### 二：Jdk动态代理介绍

Jdk动态代理类位于java.lang.reflect包下，一般主要涉及到以下两个类：

- InvocationHandler：该接口中仅定义了一个方法

```java
public object invoke(Object obj,Method method, Object[] args)
```

此方法就是代理类方法被调用时的方法，开发者可自行定义处理逻辑。三个参数分别代表：代理对象实例，被调用的方法，方法参数列表。

> 通过这三个参数，可以实现对委托对象原方法的调用。

- Proxy：该类即为动态代理类，其中主要包含以下方法

```java
// 构造函数，持有一个InvocationHandler对象，InvocationHandler对象会持有委托类
protected Proxy(InvocationHandler h) 
// 获得一个代理类，其中loader是类装载器，interfaces是真实类所拥有的全部接口的数组。
static Class getProxyClass (ClassLoaderloader, Class[] interfaces)
// 返回代理类的一个实例，返回后的代理类可以当作委托类使用
static Object newProxyInstance(ClassLoaderloader, Class[] interfaces, InvocationHandler h)
```

### 三：Jdk动态代理示例

Jdk动态代理步骤：

- 创建一个实现接口InvocationHandler的逻辑处理器，在invoke方法里实现代理类方法被调用时的逻辑处理
- 创建需要被代理的类以及接口
- 通过Proxy的静态方法`newProxyInstance(ClassLoaderloader, Class[] interfaces, InvocationHandler h)`创建一个代理
- 通过代理调用方法

代码示例：

- 接口

```java
package com.lizhaoxuan.jdk.proxy;

/**
 * 接口
 * @author lizhaoxuan
 */
public interface UserMapper {

    boolean addUser(String name,String pass);

    boolean deleteUser(Long id);

    boolean updateUser(Long id,String name);

    String queryUser(Long id);

}
```

- 实现类

```java
package com.lizhaoxuan.jdk.proxy;

import lombok.SneakyThrows;
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.ResultSet;
import java.sql.Statement;

/**
 * 真正的实现类，委托类
 * @author lizhaoxuan
 */
public class UserMapperImpl implements UserMapper{

    private Connection connection;
    private Statement statement;

    @SneakyThrows
    public UserMapperImpl(){
        // 加载驱动 Jdbc3之后通过SPI机制加载类，可无须手动加载
        Class.forName("org.postgresql.Driver");
        // 获取连接器
        connection = DriverManager.getConnection("jdbc:postgresql://127.0.0.1:9999/public", "postgres", "dsadsad");
        // 获取SQL执行器
        statement = connection.createStatement();
    }

    @SneakyThrows
    @Override
    public boolean addUser(String name, String pass) {
        int effectRows = statement.executeUpdate(String.format("insert into \"public\".\"user\" (name,pass) values ('%s','%s')", name, pass));
        return effectRows > 0;
    }

    @SneakyThrows
    @Override
    public boolean deleteUser(Long id) {
        int effectRows = statement.executeUpdate(String.format("delete from \"public\".\"user\" where id = %d", id));
        return effectRows > 0;
    }

    @SneakyThrows
    @Override
    public boolean updateUser(Long id, String name) {
        int effectRows = statement.executeUpdate(String.format("update \"public\".\"user\" set name = '%s' where id = %d", name, id));
        return effectRows > 0;
    }

    @SneakyThrows
    @Override
    public String queryUser(Long id) {
        StringBuilder builder = new StringBuilder();
        ResultSet resultSet = statement.executeQuery(String.format("select * from \"public\".\"user\" where id = %d",id));
        while (resultSet.next()){
            long l = resultSet.getLong("id");
            String name = resultSet.getString("name");
            String pass = resultSet.getString("pass");
            builder.append(String.format("User: id=%d,name=%s,pass=%s!\n",l,name,pass));
        }
        return builder.toString();
    }
}
```

- 静态代理类

```java
package com.lizhaoxuan.jdk.proxy;

import lombok.extern.slf4j.Slf4j;
import org.apache.commons.lang3.StringUtils;
import java.util.Objects;

/**
 * 静态代理，需要自己实现代理类
 * UserMapper代理类，对原类进行增强，添加日志打印
 */
@Slf4j
public class UserMapperProxy implements UserMapper{

    private UserMapper implementation;

    public UserMapperProxy(UserMapper userMapper){
        Objects.requireNonNull(userMapper);
        implementation = userMapper;
    }

    @Override
    public boolean addUser(String name, String pass) {
        log.info("[UserMapperProxy] start add User[name={},pass={}]!",name,pass);
        boolean addUser = implementation.addUser(name, pass);
        log.info("[UserMapperProxy] add User result:{}!",addUser);
        return addUser;
    }

    @Override
    public boolean deleteUser(Long id) {
        log.info("[UserMapperProxy] start delete User[id={}]!",id);
        boolean deleteUser = implementation.deleteUser(id);
        log.info("[UserMapperProxy] delete User result:{}!",deleteUser);
        return deleteUser;
    }

    @Override
    public boolean updateUser(Long id, String name) {
        log.info("[UserMapperProxy] start update User[name={},id={}]!",name,id);
        boolean updateUser = implementation.updateUser(id, name);
        log.info("[UserMapperProxy] update User result:{}!",updateUser);
        return updateUser;
    }

    @Override
    public String queryUser(Long id) {
        log.info("[UserMapperProxy] start query User[id={}]!",id);
        String queryUser = implementation.queryUser(id);
        log.info("[UserMapperProxy] query User result:{}!", !StringUtils.isBlank(queryUser));
        return queryUser;
    }
}
```

- 动态代理，代理类处理器

```java
package com.lizhaoxuan.jdk.proxy;

import com.alibaba.fastjson.JSON;
import lombok.extern.slf4j.Slf4j;

import java.lang.reflect.InvocationHandler;
import java.lang.reflect.Method;
import java.util.Objects;

/**
 * UserMapper代理处理器
 */
@Slf4j
public class UserMapperHandler implements InvocationHandler {

    private UserMapper implementation;

    public UserMapperHandler(UserMapper userMapper){
        Objects.requireNonNull(userMapper);
        implementation = userMapper;
    }

    /**
     *  代理类的所有方法调用都会转发给handler处理器，调用此方法
     * @param proxy 代理实例对象（多层代理时有用）
     * @param method    调用的方法
     * @param args  方法参数
     * @return
     * @throws Throwable
     */
    @Override
    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
        log.info("[DynamicProxy] method start,name={},param={}!",method.getName(), JSON.toJSONString(args));
        Object result = method.invoke(implementation, args);
        log.info("[DynamicProxy] method execute finished,name={},returnValue={}!",method.getName(), JSON.toJSONString(result));
        return result;
    }

}
```

- 测试

```java
package com.lizhaoxuan.jdk.proxy;

import lombok.extern.slf4j.Slf4j;
import java.lang.reflect.Proxy;

/**
 * 测试类
 * @author lizhaoxuan
 *
 */
@Slf4j
public class Test {

    public static void main(String[] args) {
        staticProxyTest();
        dynamicProxyTest();
    }

    /**
     * 动态代理测试
     */
    private static void dynamicProxyTest() {
        // 实现类
        UserMapper userMapper = new UserMapperImpl();
        // 代理处理器
        UserMapperHandler userMapperHandler = new UserMapperHandler(userMapper);
        // 代理类
        UserMapper proxy = (UserMapper) Proxy.newProxyInstance(userMapper.getClass().getClassLoader(), userMapper.getClass().getInterfaces(), userMapperHandler);
        // 方法测试
        boolean addUser = proxy.addUser("Rocks", "admin1234");
        boolean updateUser = proxy.updateUser(2L, "Rocks526");
        String queryUser = proxy.queryUser(2L);
        boolean deleteUser = proxy.deleteUser(2L);
    }

    /**
     * 静态代理测试
     */
    private static void staticProxyTest() {
        // 实现类
        UserMapper userMapper = new UserMapperImpl();
        // 代理类
        UserMapper proxy = new UserMapperProxy(userMapper);
        // 方法测试
        boolean addUser = proxy.addUser("Rocks", "admin1234");
        boolean updateUser = proxy.updateUser(1L, "Rocks526");
        String queryUser = proxy.queryUser(1L);
        boolean deleteUser = proxy.deleteUser(1L);
    }

}

```

- 静态代理输出

![image-20210817145035971](http://rocks526.top/lzx/image-20210817145035971.png)

- 动态代理输出

![image-20210817172323565](C:\Users\lizhaoxuan\AppData\Roaming\Typora\typora-user-images\image-20210817172323565.png)

> - invokeHandler处理器里的第一个proxy参数是代理对象的实例，在多层代理时会使用到。
> - 接口可以不需要实现类，实现逻辑放在invokeHandler里，类似于Mybatis的Mapper接口增强。

### 四：Jdk动态代理的实现

- Proxy.newProxyInstance方法

```java
   public static Object newProxyInstance(ClassLoader loader,
                                          Class<?>[] interfaces,
                                          InvocationHandler h)
        throws IllegalArgumentException
    {
        // 非空校验
        Objects.requireNonNull(h);

        // 拷贝所有接口
        final Class<?>[] intfs = interfaces.clone();

        // 安全性检查
        final SecurityManager sm = System.getSecurityManager();
        if (sm != null) {
            checkProxyAccess(Reflection.getCallerClass(), loader, intfs);
        }

        // 从缓存获取或生成代理类
        Class<?> cl = getProxyClass0(loader, intfs);

        // 初始化代理类，传入逻辑处理器InvocationHandler
        try {

            // 权限校验
            if (sm != null) {
                checkNewProxyPermission(Reflection.getCallerClass(), cl);
            }

            // 获取入参为InvocationHandler的构造函数
            final Constructor<?> cons = cl.getConstructor(constructorParams);
            final InvocationHandler ih = h;
            // 访问权限检查
            if (!Modifier.isPublic(cl.getModifiers())) {
                AccessController.doPrivileged(new PrivilegedAction<Void>() {
                    public Void run() {
                        cons.setAccessible(true);
                        return null;
                    }
                });
            }
            // 初始化
            return cons.newInstance(new Object[]{h});
        } catch (IllegalAccessException|InstantiationException e) {
            throw new InternalError(e.toString(), e);
        } catch (InvocationTargetException e) {
            Throwable t = e.getCause();
            if (t instanceof RuntimeException) {
                throw (RuntimeException) t;
            } else {
                throw new InternalError(t.toString(), t);
            }
        } catch (NoSuchMethodException e) {
            throw new InternalError(e.toString(), e);
        }
    }
```

在此方法中，除了一些安全性校验之外，主要逻辑有两点：

1. 从缓存获取或创建代理类
2. 将InvocationHandler交给代理类，返回实例化对象

-  getProxyClass0

```java
    /**
     * 生成的代理类的缓存Cache
     */
    private static final WeakCache<ClassLoader, Class<?>[], Class<?>>
        proxyClassCache = new WeakCache<>(new KeyFactory(), new ProxyClassFactory());

/**
     * 生成动态代理类，调用之前需要完成权限检查
     */
    private static Class<?> getProxyClass0(ClassLoader loader,
                                           Class<?>... interfaces) {
        // 检查实现接口数量
        if (interfaces.length > 65535) {
            throw new IllegalArgumentException("interface limit exceeded");
        }

        // 缓存中获取，不存在则通过ProxyClassFactory创建
        return proxyClassCache.get(loader, interfaces);
    }
```

当缓存中不存在时，会通过ProxyClassFactory生成代理类。

- ProxyClassFactory

```java
    /**
     * 代理类生成工厂，根据给定的类加载器和需要实现的接口，动态生成代理类
     */
    private static final class ProxyClassFactory
        implements BiFunction<ClassLoader, Class<?>[], Class<?>>
    {
        // 所有代理类的类名前缀
        private static final String proxyClassNamePrefix = "$Proxy";

        // 用于生成类名的唯一标识，递增数字
        private static final AtomicLong nextUniqueNumber = new AtomicLong();

        @Override
        public Class<?> apply(ClassLoader loader, Class<?>[] interfaces) {

            Map<Class<?>, Boolean> interfaceSet = new IdentityHashMap<>(interfaces.length);
            for (Class<?> intf : interfaces) {
                // 校验类加载器是否可识别这些接口
                Class<?> interfaceClass = null;
                try {
                    interfaceClass = Class.forName(intf.getName(), false, loader);
                } catch (ClassNotFoundException e) {
                }
                if (interfaceClass != intf) {
                    throw new IllegalArgumentException(
                        intf + " is not visible from class loader");
                }
                // 校验是否是一个接口
                if (!interfaceClass.isInterface()) {
                    throw new IllegalArgumentException(
                        interfaceClass.getName() + " is not an interface");
                }
                // 校验接口是否重复
                if (interfaceSet.put(interfaceClass, Boolean.TRUE) != null) {
                    throw new IllegalArgumentException(
                        "repeated interface: " + interfaceClass.getName());
                }
            }

            // 代理类包名
            String proxyPkg = null;
            // 访问权限public && final ，不可被重写
            int accessFlags = Modifier.PUBLIC | Modifier.FINAL;

            // 校验所有接口的包权限控制 ==> 非public的接口必须在同一个包中
            for (Class<?> intf : interfaces) {
                int flags = intf.getModifiers();
                // 校验public权限
                if (!Modifier.isPublic(flags)) {
                    accessFlags = Modifier.FINAL;
                    String name = intf.getName();
                    int n = name.lastIndexOf('.');
                    String pkg = ((n == -1) ? "" : name.substring(0, n + 1));
                    if (proxyPkg == null) {
                        proxyPkg = pkg;
                    } else if (!pkg.equals(proxyPkg)) {
                        throw new IllegalArgumentException(
                            "non-public interfaces from different packages");
                    }
                }
            }

            // 使用默认包名
            if (proxyPkg == null) {
                // if no non-public proxy interfaces, use com.sun.proxy package
                proxyPkg = ReflectUtil.PROXY_PACKAGE + ".";
            }

            // 生成代理类名称
            long num = nextUniqueNumber.getAndIncrement();
            String proxyName = proxyPkg + proxyClassNamePrefix + num;

            // 生成代理类字节码
            byte[] proxyClassFile = ProxyGenerator.generateProxyClass(
                proxyName, interfaces, accessFlags);

            try {
                // 加载代理类
                return defineClass0(loader, proxyName,
                                    proxyClassFile, 0, proxyClassFile.length);
            } catch (ClassFormatError e) {
                // 生成的字节码有误
                throw new IllegalArgumentException(e.toString());
            }
        }
    }
```

通过ProxyGenerator.generateProxyClass生成代理类，然后defineClass0进行加载。

- 生成代理类

```java
    private static void saveProxyCode(){
            byte[] proxyClass = ProxyGenerator.generateProxyClass("ProxyUserMapper", new Class[]{UserMapper.class}, Modifier.PUBLIC | Modifier.FINAL);
            FileOutputStream out =null;
            try {
                out = new FileOutputStream("ProxyUserMapper.class");
                out.write(proxyClass);
            } catch (FileNotFoundException e) {
                e.printStackTrace();
            } catch (IOException e) {
                e.printStackTrace();
            }finally {
                if(null!=out) try {
                    out.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
    }
```

- 生成代码如下

```java
//
// Source code recreated from a .class file by IntelliJ IDEA
// (powered by FernFlower decompiler)
//

import com.lizhaoxuan.jdk.proxy.UserMapper;
import java.lang.reflect.InvocationHandler;
import java.lang.reflect.Method;
import java.lang.reflect.Proxy;
import java.lang.reflect.UndeclaredThrowableException;

public final class ProxyUserMapper extends Proxy implements UserMapper {
    private static Method m1;
    private static Method m5;
    private static Method m2;
    private static Method m3;
    private static Method m4;
    private static Method m6;
    private static Method m0;

    public ProxyUserMapper(InvocationHandler var1) throws  {
        super(var1);
    }

    public final boolean equals(Object var1) throws  {
        try {
            return (Boolean)super.h.invoke(this, m1, new Object[]{var1});
        } catch (RuntimeException | Error var3) {
            throw var3;
        } catch (Throwable var4) {
            throw new UndeclaredThrowableException(var4);
        }
    }

    public final boolean addUser(String var1, String var2) throws  {
        try {
            return (Boolean)super.h.invoke(this, m5, new Object[]{var1, var2});
        } catch (RuntimeException | Error var4) {
            throw var4;
        } catch (Throwable var5) {
            throw new UndeclaredThrowableException(var5);
        }
    }

    public final String toString() throws  {
        try {
            return (String)super.h.invoke(this, m2, (Object[])null);
        } catch (RuntimeException | Error var2) {
            throw var2;
        } catch (Throwable var3) {
            throw new UndeclaredThrowableException(var3);
        }
    }

    public final boolean updateUser(Long var1, String var2) throws  {
        try {
            return (Boolean)super.h.invoke(this, m3, new Object[]{var1, var2});
        } catch (RuntimeException | Error var4) {
            throw var4;
        } catch (Throwable var5) {
            throw new UndeclaredThrowableException(var5);
        }
    }

    public final boolean deleteUser(Long var1) throws  {
        try {
            return (Boolean)super.h.invoke(this, m4, new Object[]{var1});
        } catch (RuntimeException | Error var3) {
            throw var3;
        } catch (Throwable var4) {
            throw new UndeclaredThrowableException(var4);
        }
    }

    public final String queryUser(Long var1) throws  {
        try {
            return (String)super.h.invoke(this, m6, new Object[]{var1});
        } catch (RuntimeException | Error var3) {
            throw var3;
        } catch (Throwable var4) {
            throw new UndeclaredThrowableException(var4);
        }
    }

    public final int hashCode() throws  {
        try {
            return (Integer)super.h.invoke(this, m0, (Object[])null);
        } catch (RuntimeException | Error var2) {
            throw var2;
        } catch (Throwable var3) {
            throw new UndeclaredThrowableException(var3);
        }
    }

    static {
        try {
            m1 = Class.forName("java.lang.Object").getMethod("equals", Class.forName("java.lang.Object"));
            m5 = Class.forName("com.lizhaoxuan.jdk.proxy.UserMapper").getMethod("addUser", Class.forName("java.lang.String"), Class.forName("java.lang.String"));
            m2 = Class.forName("java.lang.Object").getMethod("toString");
            m3 = Class.forName("com.lizhaoxuan.jdk.proxy.UserMapper").getMethod("updateUser", Class.forName("java.lang.Long"), Class.forName("java.lang.String"));
            m4 = Class.forName("com.lizhaoxuan.jdk.proxy.UserMapper").getMethod("deleteUser", Class.forName("java.lang.Long"));
            m6 = Class.forName("com.lizhaoxuan.jdk.proxy.UserMapper").getMethod("queryUser", Class.forName("java.lang.Long"));
            m0 = Class.forName("java.lang.Object").getMethod("hashCode");
        } catch (NoSuchMethodException var2) {
            throw new NoSuchMethodError(var2.getMessage());
        } catch (ClassNotFoundException var3) {
            throw new NoClassDefFoundError(var3.getMessage());
        }
    }
}
```

- 总结

Jdk动态代理实现逻辑如下：

1. 通过实现 InvocationHandler 接口创建自己的调用处理器；
2. 通过为 Proxy 类指定 ClassLoader 对象和一组 interface 来创建动态代理类；
3. 通过ProxyGenerator.generateProxyClass生成代理类字节码
4. 加载字节码，通过反射调用代理类的构造函数，将InvocationHandler传递给代理类
5. 所有代理方法的调用全部转发给InvocationHandler的invoke方法






























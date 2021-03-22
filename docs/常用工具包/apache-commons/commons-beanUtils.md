# 一：Apache Commons beanUtils介绍

### 1.1 Commons beanUtils介绍

在介绍Commons beanUtils之前，需要先介绍下Java的反射和内省机制。

Java语言习惯于在多个业务逻辑之间通过JavaBean来传递数据，一般通过JavaBean的get/set方法实现对JavaBean属性的获取和修改。除了get/set之外，Java提供了内省机制实现对JavaBean的操作，包括获取JavaBean的所有属性、get/set方法等信息，获取属性和修改信息需要借助反射实现。

反射是在运行状态把Java类中的各种成分映射成相应的Java类，可以动态的获取所有的属性以及动态调用任意一个方法，强调的是`运行状态`。

> 关于Java反射和内省的更多信息参考Java语言基础篇。

由于Java反射和内省的API十分繁琐，操作不易，因此Apache Commons beanUtils包就是为了简化这些操作而生，对这些操作进行封装，提供简易API。

### 1.2 Maven依赖

```xml
        <dependency>
            <groupId>commons-beanutils</groupId>
            <artifactId>commons-beanutils</artifactId>
            <version>1.9.4</version>
        </dependency>
```

# 二：Apache Commons beanUtils常用API

### 2.1 常用API介绍

Commons beanUtils包主要提供以下几种工具：`MethodUtils`、`ConstructorUtils`、`PropertyUtils`、`BeanUtils`、`ConvertUtils`等。

- MethodUtils：通过反射对对象的方法做各种各样的操作

| 方法                                                         | 返回值   | 解释                                     |
| ------------------------------------------------------------ | -------- | ---------------------------------------- |
| setCacheMethods(final boolean cacheMethods)                  | void     | 设置是否缓存方法(缓存可以提高性能)       |
| clearCache()                                                 | int      | 清空方法缓存                             |
| invokeMethod(final Object object,final String methodName,Object[] args,Class<?>[] parameterTypes) | Object   | 执行方法                                 |
| invokeStaticMethod(final Object object,final String methodName,Object[] args,Class<?>[] parameterTypes) | Object   | 执行静态方法                             |
| getAccessibleMethod(final Class\<?> clazz,final String methodName,final Class<?> parameterType) | Method   | 返回一个可访问的方法                     |
| getMatchingAccessibleMethod(final Class\<?> clazz,final String methodName,final Class<?>[] parameterTypes) | Method   | 查找与方法名及参数匹配的可访问方法       |
| isAssignmentCompatible(final Class\<?> parameterType, final Class<?> parameterization) | boolean  | 确定是否可以使用一个类型作为方法调用参数 |
| getPrimitiveWrapper(final Class<?> primitiveType)            | Class<?> | 获得基本数据类型的包装类型               |
| getPrimitiveType(final Class<?> wrapperType)                 | Class<?> | 获得包装类的基本数据类型                 |

- ConstructorUtils：通过反射对对象的构造方法做各种操作

| 方法                                                         | 返回值      | 解释                           |
| ------------------------------------------------------------ | ----------- | ------------------------------ |
| invokeConstructor(final Class klass,Object[] args,Class<?>[] parameterTypes) | T           | 执行构造方法                   |
| getAccessibleConstructor(final Class klass,final Class<?> parameterType) | Constructor | 获得含有一个形参的构造方法     |
| getAccessibleConstructor(final Class klass,final Class<?>[] parameterTypes) | Constructor | 获得含有指定类型形参的构造方法 |
| getAccessibleConstructor(final Constructor ctor)             | Constructor | 获得可访问构造方法             |

- PropertyUtils：通过反射对对象的属性做各种操作

| 方法                                                         | 返回值              | 解释                                               |
| ------------------------------------------------------------ | ------------------- | -------------------------------------------------- |
| clearDescriptors()                                           | void                | 清空所有属性描述信息                               |
| resetBeanIntrospectors()                                     | void                | 重置BeanIntrospector                               |
| addBeanIntrospector(final BeanIntrospector introspector)     | void                | 添加一个BeanIntrospector                           |
| removeBeanIntrospector(final BeanIntrospector introspector)  | boolean             | 移除BeanIntrospector                               |
| copyProperties(final Object dest, final Object orig)         | void                | 复制属性                                           |
| describe(final Object bean)                                  | Map<String, Object> | 属性描述，key属性名，value属性值                   |
| getMappedProperty(final Object bean,final String name, final String key) | Object              | 获得Map属性中指定键对应的值，适用于属性是Map的情况 |
| getIndexedProperty(final Object bean,final String name, final int index) | Object              | 指定索引属性值，适用于属性是list或者array的情况    |
| getNestedProperty(final Object bean, final String name)      | Object              | 获得嵌套属性，属性是对象的情况                     |
| getProperty(final Object bean, final String name)            | Object              | 获得属性                                           |
| getPropertyDescriptor(final Object bean,final String name)   | PropertyDescriptor  | 获取属性描述                                       |
| getPropertyEditorClass(final Object bean, final String name) | Class<?>            | 获得已为此属性注册的任何显式 PropertyEditor Class  |
| getPropertyType(final Object bean, final String name)        | Class<?>            | 获得属性类型                                       |
| getReadMethod(final PropertyDescriptor descriptor)           | Method              | 返回一个可访问的属性的getter方法                   |
| getWriteMethod(final PropertyDescriptor descriptor)          | Method              | 返回一个可访问的属性的setter方法                   |
| isReadable(final Object bean, final String name)             | boolean             | 判断是否为可读属性                                 |
| isWriteable(final Object bean, final String name)            | boolean             | 判断是否为可写属性                                 |

-  BeanUtils：通过反射提供了Bean对象的一些便捷操作方法

| 方法                                                         | 返回值              | 解释                                  |
| ------------------------------------------------------------ | ------------------- | ------------------------------------- |
| cloneBean(final Object bean)                                 | Object              | 克隆对象                              |
| copyProperties(final Object dest, final Object orig)         | void                | 复制属性                              |
| copyProperty(final Object bean, final String name, final Object value) | void                | 复制属性，相当于设置属性              |
| describe(final Object bean)                                  | Map<String, String> | 描述                                  |
| getArrayProperty(final Object bean, final String name)       | String[]            | 返回指定属性的值，作为字符串数组返回  |
| getMappedProperty(final Object bean, final String name)      | String              | 获得Map属性值作为字符串返回           |
| getMappedProperty(final Object bean, final String name, final String key) | String              | 获得Map属性中指定键的值作为字符串返回 |
| populate(final Object bean, final Map<String, ? extends Object> properties) | void                | 将Map转换成对象                       |

- ConvertUtils：提供了数据类型相互转换的一些方法

| 方法名                                                    | 返回值    | 解释                           |
| --------------------------------------------------------- | --------- | ------------------------------ |
| convert(final Object value)                               | String    | 将对象转换为字符串             |
| convert(final String value, final Class<?> clazz)         | Object    | 将字符串转换为指定数据类型对象 |
| convert(final String[] values, final Class<?> clazz)      | Object    | 将数组转换为指定数据类型对象   |
| convert(final Object value, final Class<?> targetType)    | Object    | 将对象转换为指定数据类型对象   |
| deregister()                                              | void      | 移除所有已经注册的转换器       |
| deregister(final Class<?> clazz)                          | void      | 移除指定类型的转换器           |
| lookup(final Class<?> clazz)                              | Converter | 查找指定类型的转换器           |
| register(final Converter converter, final Class<?> clazz) | void      | 注册转换器                     |

### 2.2 代码示例

> ​	经过测试，对象属性拷贝和克隆均存在问题，且Alibaba Java Guide不建议使用。

```java
package com.rocks.commons.beanutils;

import lombok.SneakyThrows;
import org.apache.commons.beanutils.BeanUtils;
import org.apache.commons.beanutils.ConvertUtils;
import org.apache.commons.beanutils.Converter;

import java.math.BigDecimal;
import java.text.SimpleDateFormat;
import java.util.Arrays;
import java.util.Date;
import java.util.HashMap;
import java.util.Map;

/**
 * BeanUtils工具包示例代码 (不建议使用 推荐使用Spring的BeanUtils)
 * @author lizhaoxuan
 */
public class Demo {

    public static void main(String[] args) {

        User user = new User()
                .setId(1L)
                .setUsername("Rocks")
                .setPassword("123456")
                .setAge(22)
                .setCreateTime(new Date())
                .setMoney(new BigDecimal("39999.0"))
                .setProduct(new Product().setName("car").setDesc("a beautiful car"))
                .setExtendInfos(Arrays.asList("extend1","extend2"));

        convertUtilsDemo(user);
        beanUtilsDemo(user);
    }

    @SneakyThrows
    private static void beanUtilsDemo(User user) {

        // 属性复制  ==>  检查对象类型 是否是DynaBean或者Map  如果不是 则遍历所有属性进行复制 前提是可读可写 由于不可写 复制全部为null
        UserVo userVo = new UserVo();
        BeanUtils.copyProperties(userVo,user);
        System.out.println(userVo);

        // 获取对象描述
        Map<String, String> describe = BeanUtils.describe(user);
        System.out.println(describe);

        // 将Map转换为对象
        HashMap<String, Object> stringObjectHashMap = new HashMap<>();
        stringObjectHashMap.put("desc","A person builder");
        stringObjectHashMap.put("notExistProperty","Test");
        BeanUtils.populate(userVo,stringObjectHashMap);
        System.out.println(userVo);

        // 对象克隆 new出新的对象 然后copyProperties
        User cloneUser = (User) BeanUtils.cloneBean(user);
        System.out.println(cloneUser);
        user.getProduct().setName("car2");
        System.out.println(cloneUser.getProduct());
        user.getExtendInfos().add("extend3");
        System.out.println(cloneUser.getExtendInfos());
    }

    private static void convertUtilsDemo(User user) {
        // 对象转字符串
        String userStr = ConvertUtils.convert(user);
        System.out.println(userStr);

        // 注册转换器
        ConvertUtils.register(new Converter() {
            @SneakyThrows
            @Override
            public <T> T convert(Class<T> aClass, Object value) {
                return (T) new SimpleDateFormat("yyyy/MM/dd HH:mm:ss").parse(value.toString());
            }
        },Date.class);

        // 字符串转指定类型对象 (会先去查找class的转换器 如果没有注册则报错)
        Object convertDate = ConvertUtils.convert("2021/02/01 20:30:20", Date.class);
        System.out.println(convertDate);
    }

}
```


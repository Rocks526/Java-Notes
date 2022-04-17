- [fastjson](#fastjson)
- [Jackson](#jackson)
- [Gson](#gson)

-----------------------------

# <a id="fastjson">fastjson</a>

### fastjson介绍

#### fastjson简介

fastjson是阿里巴巴的开源JSON解析库，它可以解析JSON格式的字符串，支持将Java Bean序列化为JSON字符串，也可以从JSON字符串反序列化到JavaBean。Fastjson可以与任意Java对象一起使用，包括您没有源代码的现有对象。

#### fastjson优点

- 速度快

fastjson相对其他JSON库的特点是快，从2011年fastjson发布1.1.x版本之后，其性能从未被其他Java实现的JSON库超越。

- 使用广泛

fastjson在阿里巴巴大规模使用，在数万台服务器上部署，fastjson在业界被广泛接受。在2012年被开源中国评选为最受欢迎的国产开源软件之一。

- 测试完备

fastjson有非常多的testcase，在1.2.11版本中，testcase超过3321个。每次发布都会进行回归测试，保证质量稳定。

- 使用简单

fastjson的API十分简洁。

```java
String text = JSON.toJSONString(obj); //序列化
VO vo = JSON.parseObject("{...}", VO.class); //反序列化
```

- 功能完备

支持泛型，支持流处理超大文本，支持枚举，支持序列化和反序列化扩展。

#### fastjson目标

- 在服务器端和android客户端上提供最佳性能
- 提供简单的toJSONString（）和parseObject（）方法，将Java对象转换为JSON，反之亦然
- 允许将已有的不可修改的对象与JSON相互转换
- Java泛型的广泛支持
- 允许对象的自定义表示
- 支持任意复杂的对象（具有深层继承层次结构和泛型类型的广泛使用）

> 以上是fastjson的官网对其的介绍，fastjson的github地址：https://github.com/alibaba/fastjson

### fastjson使用

#### 引入maven依赖/jar包

```xml
<dependency>
    <groupId>com.alibaba</groupId>
    <artifactId>fastjson</artifactId>
    <version>1.2.73</version>
</dependency>
```

#### 序列化和反序列化API

```java
package fastjson;

import com.alibaba.fastjson.JSONArray;
import com.alibaba.fastjson.JSONObject;
import entity.Product;
import entity.User;
import java.math.BigDecimal;
import java.util.*;

public class Demo {
    public static void main(String[] args) {
        List<User> users = buildData();
        String json = fromObjectToJson(users);
        System.out.println(json);
        List<User> users2 = fromJsontoUser(json);
        System.out.println(users2);
    }

    // JSON转对象
    private static List<User> fromJsontoUser(String json) {
        // JSON字符串转对象 没有设置对象class类型 返回Object
        Object o = JSONObject.parse(json);
        // JSON字符串转对象 没有设置对象class类型 返回JSONObject 可以通过JSONObject操作对象
        JSONObject jsonObject = JSONObject.parseObject(json);
        // JSON字符串转对象 设置对象类型 返回对应类型对象
        User user = JSONObject.parseObject(json, User.class);
        // JSON字符串转对象数组 没有设置对象class类型 返回JSONArray 可以通过遍历获取JSONObject操作对象
        JSONArray jsonArray = JSONObject.parseArray(json);
        // JSON字符串转对象数组 设置对象class类型 返回对象List
        List<User> users = JSONObject.parseArray(json,User.class);
        return JSONObject.parseArray(json, User.class);
    }

    // 对象转换JSON
    private static String fromObjectToJson(List<User> users) {
        // 对象转Json字符串
        String json = JSONObject.toJSONString(users);
        // 对象转Json字符串并格式化
        String json2 = JSONObject.toJSONString(users, true);
        System.out.println(json2);
        return JSONObject.toJSONString(users);
    }

    private static List<User> buildData() {
        return new ArrayList<User>(){
            {
                add(new User().setId(1L).setUserName("Rocks").setPassword("123456")
                            .setPrice(new BigDecimal(19.88)).setBirthday(new Date())
                            .setProducts(Arrays.asList(new Product(1L,"mysql"), new Product(2L, "pg"))));
                add(new User().setId(1L).setUserName("admin").setPassword("admin1234")
                        .setPrice(new BigDecimal(98.88)).setBirthday(new Date())
                        .setProducts(Arrays.asList(new Product(3L,"mongodb"), new Product(4L, "redis"))));
            }
        };
    }

}
```

fastjson的操作十分简单，通过JSONObject对象可以完成对象和JSON的相互转换，常用方法有以下几种：

- toJSONString：将一个对象转换成JSON字符串，可以通过设置true实现字符串格式化
- parse：将一个JSON字符串转换成对象，由于没有设置泛型，因此返回Object对象
- parseObject：将一个JSON字符串转换成对象，不设置对象class时，返回JSONObject，设置class则返回对应类型对象
- parseArray：将一个JSON字符串转换成对象数组，不设置对象class时，返回JSONArray，设置class则返回对应类型对象的List

> 序列化和反序列化时，除了可以接收对象和字符串之外，还可以接收输入流，字节数组等。
>
> 除了可以将JSON和对象进行转换之外，还可以和Map进行转换，也可以通过Map构建JSONObject。

#### 利用JSONObject和JSONArray构造Json字符串

```java
    //根据JSONObject和JSONArray构造Json字符串
    private static void buildJsonStrAndObjectByfastJson(){
        JSONArray jsonArray = new JSONArray();
        for (int i = 0; i < 2; i++) {
            JSONObject jsonObject = new JSONObject();
            jsonObject.put("userId", i);
            jsonObject.put("userName", "Rocks" + i);
            jsonObject.put("birthday", "2016/12/12");
            jsonArray.add(jsonObject);
        }
        String jsonOutput = jsonArray.toJSONString();
        System.out.println(jsonOutput);
    }
```

JSONObject对象的常用操作方法：

- put：添加属性
- remove：移除属性
- getXXX：根据key获取value，可以获取不同类型

#### 定制序列化

> 在上面的例子中，针对Date，BigDecimal等类型，转换的形式可能不是想要的结果，需要通过一些配置实现定制。

- 方式一：@JSONField

```java
package entity;

import com.alibaba.fastjson.annotation.JSONField;
import lombok.Data;
import lombok.experimental.Accessors;
import java.math.BigDecimal;
import java.util.Date;
import java.util.List;
import java.util.Set;

@Data
@Accessors(chain = true)
public class User {
    //fastjson 设置序列化后的id名称 key为userId的key可反序列化 key为id的该值会被忽略
    //fastjson ordinal用来控制排序字段 默认为0 越大排序越靠后 为负值时，默认取绝对值
    @JSONField(name = "userId", ordinal = 1)
    private Long id;

    @JSONField(ordinal = 2)
    private String userName;

    //fastjson deserialize和serialize用来控制字段是否序列化和反序列化
    @JSONField(deserialize = false, serialize = false)
    private String password;

    //fastjson 格式化Date格式
    @JSONField(format = "yyyy/MM/dd", ordinal = 10)
    private Date birthday;

    //fastjson defaultValue用来设置默认值
    @JSONField(defaultValue = "0")
    private BigDecimal price;

    private Set<User> friends;

    private List<Product> products;

}
```

常用参数如下：

 format控制格式化。

 ordinal控制排序，越小越靠前。

 deserialize和serialize用来控制是否进行序列化和反序列化。

 name用来控制序列化后的key的名字，反序列化时也会使用该名字，如果使    用原名，会被忽略掉。

 defaultValue用来设置默认值。

> @JSONField可以添加在字段上或get，set方法上，反序列化时，如果字段为private，必须提供set方法，而且反序列化时，必须要有空参构造方法。

- 方式二：使用SerializerFeature的WriteDateUseDateFormat







### fastjson原理





### SpringBoot里使用fastjson







### fastjson的漏洞







# <a id="jackson">Jackson</a>







# <a id="gson">Gson</a>








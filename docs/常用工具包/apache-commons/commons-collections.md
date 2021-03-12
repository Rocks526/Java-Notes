# 一：Commons Collections介绍

### 1.1 Commons Collections简介

`Commons Collections`增强了Java集合框架，主要提供一下功能：

- 新增的一些容器类：包括TreeList/MultiMap/HashMultiSet等
- 提供的工具类：包括CollectionUtils/MapUtils等

> 对于一些容器增强类平时使用不多，主要使用CollectionUtils等工具类。

### 1.2 Commons Collections的Maven依赖

```xml
<dependency>
  <groupId>org.apache.commons</groupId>
  <artifactId>commons-collections4</artifactId>
  <version>4.4</version>
</dependency>
```

# 二：Commons Collections常用API

### 2.1 CollectionUtils代码示例

```java
package com.rocks.commons.collections;

import org.apache.commons.collections4.CollectionUtils;
import java.util.Arrays;
import java.util.List;

/**
 * CollectionUtils代码示例
 * @author lizhaoxuan
 */
public class CollectionUtilsDemo {

    public static void main(String[] args) {

        List<String> list = Arrays.asList("demo", "demo2", "demo3");
        List<String> list2 = Arrays.asList("demo", "demo3", "demo2");
        List<String> list3 = Arrays.asList("demo", "test2", "test3");
        List<String> list4 = Arrays.asList("demo", "test2");

        // 判断集合是否为空
        System.out.println(CollectionUtils.isNotEmpty(list));
        System.out.println(CollectionUtils.isEmpty(list));

        // 判断两个集合值是否相等 不考虑顺序
        System.out.println(CollectionUtils.isEqualCollection(list,list2));

        // 两个集合并集
        System.out.println(CollectionUtils.union(list,list3));

        // 两个集合交集 
        System.out.println(CollectionUtils.intersection(list,list3));

        // 两个集合差集 
        System.out.println(CollectionUtils.subtract(list,list3));

        // 两个集合交集的补集
        System.out.println(CollectionUtils.disjunction(list,list3));

        // 判断是否包含任意一个相同元素
        System.out.println(CollectionUtils.containsAny(list,list3));
        // 判断是否包含全部元素
        System.out.println(CollectionUtils.containsAll(list,list3));

        // 某元素在集合中出现的次数
        System.out.println(CollectionUtils.cardinality("demo",list));

        // 删除子集合
        System.out.println(CollectionUtils.removeAll(list,list4));

    }

}
```

### 2.2 MapUtils代码示例

```java
package com.rocks.commons.collections;

import org.apache.commons.collections4.MapUtils;

import java.util.HashMap;
import java.util.Map;

/**
 * MapUtils代码示例
 * @author lizhaoxuan
 */
public class MapUtilsDemo {

    public static void main(String[] args) {
        HashMap<String, Object> hashMap = new HashMap<>();
        hashMap.put("demo","true");
        hashMap.put("demo2","value2");
        hashMap.put("demo3",true);
        hashMap.put("demo4",12);
        hashMap.put("demo5",12.3f);

        // 判断Map是否为空
        System.out.println(MapUtils.isEmpty(hashMap));
        System.out.println(MapUtils.isNotEmpty(hashMap));

        // 从Map获取指定类型值 支持默认值
        System.out.println(MapUtils.getBoolean(hashMap,"demo1",false));
        System.out.println(MapUtils.getBoolean(hashMap,"demo3",false));
        System.out.println(MapUtils.getFloat(hashMap,"demo5",0f));
        System.out.println(MapUtils.getString(hashMap,"demo2",""));
        System.out.println(MapUtils.getInteger(hashMap,"demo2",0));
    }

}
```






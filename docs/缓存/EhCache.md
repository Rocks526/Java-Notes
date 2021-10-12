# 一：EhCache介绍

### 1.1 EhCache简介

EhCache是一个`纯Java的进程内缓存框架`，具有快速、精干等特点，是Hibernate中默认CacheProvider。Ehcache是一种广泛使用的开源Java分布式缓存。主要面向通用缓存、Java EE和轻量级容器。它具有内存和磁盘存储、缓存加载器、缓存扩展、缓存异常处理程序、一个gzip缓存servlet过滤器、支持REST和SOAP Api等特点。

EhCache3.x和2.x差距很大，API完全不兼容，3.x主要改动如下：

- 通过建造者模式和泛型优化Java API
- 支持多级存储模型：堆、堆外、磁盘
- 支持集群模式

官网：https://github.com/ehcache/ehcache3

文档：https://www.ehcache.org

### 1.2 EhCache特性

- 性能高
- API简单
- 轻量级，核心程序仅仅依赖slf4j

- 多种缓存策略
- 3.x之后支持多级存储
- 缓存数据会在虚拟机重启的过程中写入磁盘
- 可以通过RMI、可插入API等方式进行分布式缓存
- 具有缓存和缓存管理器的侦听接口
- 支持多缓存管理器实例，以及一个实例的多个缓存区域
- 提供Hibernate的缓存实现

### 1.3 EhCache存储介绍

EhCache在3.x之后支持四种模式：

- heap
  - 堆缓存，受到jvm的管控，可以不序列化，速度最快
  - 默认使用的是引用传递，也可以使用复制器来进行值传递
  - 可以设置缓存条数或者缓存的大小来进行堆缓存大小的设置
  - 尽量不要设置的过大，否则容易引起GC
- off-heap
  - 堆外缓存，不受jvm的管控，受到RAM的限制，在ehcache中配置，至少1M
  - 通过-XX:MaxDirectMemorySize限制ehcache可分配的最大堆外内存，但是实际使用发现，不配置也能使用，如果不配置，使用堆外缓存时，ehcache将会使用jvm的内存，最大值为堆内存
- disk
  - 磁盘存储，尽量使用高性能的SSD
  - 这一层的存储，不能在不同的CacheManager之间共享
- clustered
  - 集群模式

多种模式可以混合使用，当混合使用时，规则如下：

- 必须有heap层
- disk和clusterd不能同时存在
- 层的大小应该采用金字塔的方式，即金字塔上的层比下的层使用更少的内存

对于多层存储模型，get和put的规则如下：

- 当将一个值放入缓存时，它直接最低层。比如heap + offheap + disk，直接会存储在disk层
- 当获取一个值，从最高层获取，如果没有继续向下一层获取，一旦获取到，会向上层推送，同时上层存储该值

# 二：EhCache使用示例

### 2.1 EhCache2.x使用示例

- 引入Maven依赖

```xml
   <dependency>
      <groupId>net.sf.ehcache</groupId>
      <artifactId>ehcache</artifactId>
      <version>2.10.3</version>
    </dependency>      
```

- 添加配置文件ehcache.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<ehcache xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:noNamespaceSchemaLocation="http://ehcache.org/ehcache.xsd"
         updateCheck="false">
    <!-- EhCache在每次启动的时候都要连接到 ehcache 网站上去检查新版本 使用如上的 updateCheck="false" 来禁止这个检查新版本 -->

    <!-- 磁盘缓存位置 -->
    <diskStore path="E:\source-code\Java\store\ehcache"/>

    <!-- 默认缓存 -->
    <!--
        name:cache唯一标识
        eternal：缓存是否永久有效
        maxElementsInMemory：内存中最大缓存对象数
        overflowToDisk(true,false)：缓存对象达到最大数后，将缓存写到硬盘中
        diskPersistent：硬盘持久化
        timeToIdleSeconds：缓存清除时间
        timeToLiveSeconds：缓存存活时间
        diskExpiryThreadIntervalSeconds：磁盘缓存的清理线程运行间隔
        memoryStoreEvictionPolicy：缓存清空策略
        1.FIFO：first in first out 先讲先出
        2.LFU： Less Frequently Used 一直以来最少被使用的
        3.LRU：Least Recently Used  最近最少使用的
    -->
    <defaultCache
            maxElementsInMemory="10"
            eternal="false"
            timeToIdleSeconds="120"
            timeToLiveSeconds="120"
            overflowToDisk="true"
            maxElementsOnDisk="1000"
            diskPersistent="false"
            diskExpiryThreadIntervalSeconds="120"
            memoryStoreEvictionPolicy="FIFO" />

    <!-- 自定义缓存配置 -->
    <cache name="WorkOrderCache"
           maxElementsInMemory="100"
           eternal="false"
           timeToIdleSeconds="5"
           timeToLiveSeconds="5"
           overflowToDisk="false"
           memoryStoreEvictionPolicy="LRU"/>

</ehcache>
```

- 测试代码

```java
package com.lizhaoxuan;

import net.sf.ehcache.Cache;
import net.sf.ehcache.CacheManager;
import net.sf.ehcache.Element;
import net.sf.ehcache.config.CacheConfiguration;
import org.junit.jupiter.api.BeforeAll;
import org.junit.jupiter.api.Test;

/**
 * EhCache快速入门
 * @author lizhaoxuan
 * @date 2021/10/9
 */
public class QuickStart {

    private static CacheManager cacheManager;

    @BeforeAll
    public static void init(){
        // 初始化缓存管理器
        cacheManager = CacheManager.create("E:\\source-code\\Java\\EhCache\\src\\main\\resources\\ehcache.xml");
    }

    @Test
    public void getTest(){
        // 获取缓存
        Cache cache = cacheManager.getCache("WorkOrderCache");
        // 设置配置 ==> 如果不设置，使用xml文件里的配置
        CacheConfiguration cacheConfiguration = cache.getCacheConfiguration();
        cacheConfiguration.setMaxElementsInMemory(3);
        cacheConfiguration.setOverflowToDisk(false);
        // 添加元素
        cache.put(new Element("key1", "StrValue"));
        Object value2 = new Object();
        cache.put(new Element("key2", value2));
        // 获取元素
        Element element1 = cache.get("key1");
        System.out.println("element1 ==> " + element1.getObjectValue());
        Element element2 = cache.get("key2");
        System.out.println("引用是否相等:" + value2 == element2.getObjectValue());
        // 刷新缓存
        cache.flush();
        // 再次获取
        Element element3 = cache.get("key1");
        System.out.println("element1 ==> " + element3);
        // 关闭manager
        cacheManager.shutdown();
    }

}
```

![image-20211009191449286](http://rocks526.top/lzx/image-20211009191449286.png)

### 2.2 EhCache3.x使用示例

- 引入Maven依赖

```xml
 <dependency>
      <groupId>org.ehcache</groupId>
      <artifactId>ehcache</artifactId>
      <version>3.9.6</version>
    </dependency>      
```

- 测试代码

```java
package com.lizhaoxuan;

import org.ehcache.Cache;
import org.ehcache.CacheManager;
import org.ehcache.config.builders.CacheConfigurationBuilder;
import org.ehcache.config.builders.CacheManagerBuilder;
import org.ehcache.config.builders.ResourcePoolsBuilder;
import org.ehcache.config.units.EntryUnit;
import org.ehcache.config.units.MemoryUnit;
import org.junit.jupiter.api.BeforeAll;
import org.junit.jupiter.api.Test;

/**
 * EhCache 3.x 快速入门
 * @author lizhaoxuan
 * @date 2021/10/9
 */
public class EhCache3 {

    private static CacheManager cacheManager;

    @BeforeAll
    public static void init(){
        // 初始化缓存管理器
        cacheManager = CacheManagerBuilder.newCacheManagerBuilder()
                // 磁盘存储路径
                .with(CacheManagerBuilder.persistence("E:\\source-code\\Java\\ehcache"))
                // 初始化缓存
                .withCache("WorkOrderCache", CacheConfigurationBuilder.newCacheConfigurationBuilder(String.class,Long.class,ResourcePoolsBuilder.newResourcePoolsBuilder().heap(10, EntryUnit.ENTRIES).disk(10, MemoryUnit.MB,true)).build())
                .build(true);
    }

    @Test
    public void test(){
        // 获取之前初始化的Cache
        Cache<String, Long> workOrderCache = cacheManager.getCache("WorkOrderCache", String.class, Long.class);
        // 创建Cache
        Cache<String, Long> userCache = cacheManager.createCache("UserCache", CacheConfigurationBuilder.newCacheConfigurationBuilder(
                String.class, Long.class, ResourcePoolsBuilder.heap(100)
        ).build());
        // Cache使用
        userCache.put("Rocks",22L);
        System.out.println(userCache.get("Rocks"));
        // 关闭
        cacheManager.close();
    }

}
```

![image-20211012220425532](http://rocks526.top/lzx/image-20211012220425532.png)

# 三：EhCache配置









# 四：EhCache集群









# 五：EhCache原理


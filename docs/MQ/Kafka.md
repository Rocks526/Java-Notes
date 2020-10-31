# Kafka介绍

Kafka起初是由LinkedIn公司采用Scala语言开发的一个多分区、多副本且基于ZooKeeper协调的分布式消息系统，现已被捐献给Apache基金会。目前Kafka已经定位为一个分布式流式处理平台，它以高吞吐、可持久化、可水平扩展、支持流数据处理等多种特性而被广泛使用。目前越来越多的开源分布式处理系统如Cloudera、Storm、Spark、Flink等都支持与Kafka集成。

## Kafka应用

- 消息系统：Kafka 和传统的消息系统（也称作消息中间件）都具备系统解耦、冗余存储、流量削峰、缓冲、异步通信、扩展性、可恢复性等功能。与此同时，Kafka 还提供了大多数消息系统难以实现的消息顺序性保障及回溯消费的功能。

- 存储系统：Kafka 把消息持久化到磁盘，相比于其他基于内存存储的系统而言，有效地降低了数据丢失的风险。也正是得益于Kafka 的消息持久化功能和多副本机制，我们可以把Kafka作为长期的数据存储系统来使用，只需要把对应的数据保留策略设置为“永久”或启用主题的日志压缩功能即可。

- 流式处理平台：Kafka 不仅为每个流行的流式处理框架提供了可靠的数据来源，还提供了一个完整的流式处理类库，比如窗口、连接、变换和聚合等各类操作。

# Kafka安装

## Windows

1. 下载kafka压缩包

![image-20200106120611116](http://rocks526.top/lzx/image-20200106120611116.png)

2. 解压
3. 由于kafka默认日志路径为/tmp/kafka/logs，Windows下无该目录，因此需要修改conf/server.properties文件的日志目录，如果使用kafka自带的zk，则zk的日志目录页需要修改

4. 启动zk
5. 启动kafka

## Kafka集群搭建

1. 复制多分配置文件，修改broker.id和zookeeper地址，集群每个broker的id必须唯一，zookeeper必须使用同一个或者同一个zk集群，zk会用来保存一些broker的主题，分区等元信息
2. 如果在单机部署，还需要修改监听的端口号
3. 启动多个broker
4. 以上是必须修改的配置信息，还有一些涉及集群间信息复制的配置可选

# Kafka生产者

## Kafka生产者介绍

Kafka提供了内置的客户端，生产者相关的是KafkaProducer和ProducerRecord，除了内置的客户端之外，Kafka还提供了二进制连接协议，可以根据协议构造请求直接发往Kafka网络端口。

由于存在多种场景需求：消息允许丢失部分，允许出现重复，不允许丢失，延迟要求，吞吐量要求等多种场景，因此Kafka生产者提供了多种配置选项来达到目的。

## Kafka发送消息过程

1. KafkaProducer创建ProducerRecord对象，里面封装主题，分区，key，value等信息，key和分区可选，key默认为null
2. KafakProducer调用send方法发送ProducerRecord对象
3. 序列化器将ProducerRecord对象里的key和value序列化
4. 分区器根据ProducerRecord里的主题信息和分区信息确定要发送的位置，如果没有指定分区，则根据key的值来选择一个分区
5. 接下来并不是将该条记录直接发送，kafka为了提高吞吐量，会将记录添加到一个记录批次里，该批次存储发送到同一个主题和分区上的信息
6. 当某个批次达到设置的最大值时或者指定时间时，一个独立的线程负责将这些记录批次发送到相应的broker上
7. 服务器接收到消息后会返回一个响应，如果写入成功，返回一个RecordMetaData对象，里面包含主题，分区和偏移量信息。如果写入失败，则返回一个错误信息，生产者接收到错误信息后会自动进行重试，达到指定的最大重试次数之后返回错误信息

## Kafka生产者配置

详细配置参考：http://kafka.apachecn.org/documentation.html#configuration

### 必须配置

1. bootstrap.servers：broker地址 如果是集群 填写一个或多个均可 会自动检索其他broker信息
2. key.serializer：key序列化器 Kafka提供一些基础类型 也可以自己实现kafka的Serializer接口定义序列化器，需要注意的是即便发送消息不设置key也必须设置key序列化器
3. value.serializer：value序列化器，同上

### 可选配置

1. acks：指定几个分区副本收到消息，生产者认为写入成功，对消息的丢失可能性有很大影响，有以下可选值：

acks=0：生产者不等待服务端响应，将消息发出即完成。此种情况最为不可靠，由于网络异常消息未发送到服务端或接收到消息的broker还未复制给其他副本就宕机都可能导致消息丢失，但吞吐量很高

acks=1：只要集群的leader节点收到消息，生产者会收到服务端返回的一个成功响应，即认为写入成功，如果leader没有收到消息，返回错误信息，则生产者会自动重发。此种情况，如果broker还未复制给其他副本就宕机可能导致消息丢失，一般比较可靠，吞吐量取决于消息发送方式和最大消息限制

acks=all：必须所有参与复制的副本节点都收到消息，才认为写入成功。最安全的一种配置，可以保证消息不会丢失

2. buffer.memory

生产者缓冲区大小，生产者将消息发送到缓冲区，独立线程从缓冲区读取消息进行发送，当生产速度大于发送速度时，缓冲区会被堆满，此时send方法的调用要么被阻塞，要么抛出异常

3. compression.type

消息发送给broker前使用何种算法压缩，默认不会压缩，可选snappy，gzip，lz4

4. retries

最大消息重发次数，重试时间间隔由retry.backoff.ms参数设置

5. batch.size

每个批次的最大值，单位是字节数而不是消息个数

6. linger.ms

每个批次等待消息的时间，当某个批次达到最大值或者达到该时间时，该批次会被发送，默认为0

7. client.id

标识消息来源

8. max.in.flight.requests.per.connection

该参数指定生产者在收到服务端响应之前可以最多发送多少条消息。设置为1可以保证同一个生产者的所有消息的顺序性

9. timeout.ms  request.timeout.ms  metadata.fetch.timeout.ms

request.timeout.ms是生产者发送数据时等待服务端响应的超时时间

metadata.fetch.timeout.ms是生产者获取元数据时超时时间

timeout.ms是broker等待同步副本返回消息确认的时间

10. max.block.ms

调用send方法或者partitionsFor方法时，如果缓冲区已满则阻塞，此参数是最大阻塞时间，之后抛出异常

11. max.requests.size

单次请求发送的消息的最大值

12. receive.buffer.bytes   send.buffer.bytes

TCP的发送和接受缓冲区大小，设置为-1则使用操作系统默认值

## Kafka生产者发送方式

Kakfa生产者的调用send方法发送消息，send方法有发送不关注结果、同步、异步三种方式

### 发送不关注结果

直接调用send方法发送一个消息，不关心发送结果

该消息会被发送到缓冲区，是否到达服务端不可知

### 同步发送

调用send方法发送一个消息，返回一个Future对象，利用Future对象的get方法阻塞获得返回结果。

### 异步发送

调用send方法发送一个消息，传入一个CallBack回调函数，发送成功后会执行回调函数。

### 示例

需要注意的是，第一种和第三种都是将数据发送到缓冲区即返回，因此可能会存在延迟，缓冲区没有发送给broker，可以调用producer.flush方法刷新一下缓冲区

```java
package producer;

import org.apache.kafka.clients.producer.Callback;
import org.apache.kafka.clients.producer.KafkaProducer;
import org.apache.kafka.clients.producer.ProducerRecord;
import org.apache.kafka.clients.producer.RecordMetadata;

import javax.swing.*;
import java.util.Properties;
import java.util.concurrent.ExecutionException;
import java.util.concurrent.Future;

/**
 * Kafka Producer Test
 */
public class Producer {


    private static final String BROKER_ADDRESS = "localhost:9092";

    public static void main(String[] args) {

        //创建Properties对象 用于存储KafkaProducer配置信息 创建KafkaProducer
        Properties properties = new Properties();

        // ----------  必须属性  ------------

        //broker地址 如果是集群 填写一个或多个均可 会自动检索其他broker信息
        properties.setProperty("bootstrap.servers",BROKER_ADDRESS);
        //key序列化器 Kafka提供一些基础类型 可以自己实现kafka的Serializer接口定义序列化器
        properties.setProperty("key.serializer","org.apache.kafka.common.serialization.IntegerSerializer");
        //value序列化器 Kafka提供一些基础类型 可以自己实现kafka的Serializer接口定义序列化器
        properties.setProperty("value.serializer","org.apache.kafka.common.serialization.StringSerializer");

        //创建一个生产者
        KafkaProducer producer = new KafkaProducer<Integer,String>(properties);

        //创建一条消息
        final ProducerRecord<Integer, String> record = new ProducerRecord<Integer, String>("demo",2,"This is Second Message!");

        //发送不关注结果
//        producer.send(record);

        //同步发送
        Future future = producer.send(record);
        try {
            Object o = future.get();
            System.out.println(o);
        } catch (InterruptedException e) {
            e.printStackTrace();
        } catch (ExecutionException e) {
            e.printStackTrace();
        }

        //异步发送
//        producer.send(record, new Callback() {
//            public void onCompletion(RecordMetadata recordMetadata, Exception e) {
//                if (e == null){
//                    System.out.println("消息发送成功，Topic： " + recordMetadata.topic() + " , partitions: "
//                            + recordMetadata.partition() + " , offset : " + recordMetadata.offset());
//                }else {
//                    System.out.println(e.getMessage());
//                }
//
//            }
//        });

    }
    
    
        //刷新缓冲区
        producer.flush();

        //关闭
        producer.close();


}


```

## 序列化器

Kafka内置提供了Sring，Integer，Byte数组三种类型的序列化器，可以满足基本需求，如果需要传输某个对象，可以通过json，Thrift等序列化框架将该对象序列化后进行传输，也可以通过实现org.apache.kafka.common.serialization.Serializer接口自定义序列化器。

### 自定义序列化器

需要传输的实体

```java
package serializer;


import lombok.Data;

@Data
public class User {

    private Long userId;

    private String userName;
}

```

自定义序列化器

```java
package serializer;


import org.apache.kafka.common.serialization.Serializer;

import java.io.UnsupportedEncodingException;
import java.util.Map;

public class UserSerializer implements Serializer<User> {


    @Override
    public void configure(Map<String, ?> map, boolean b) {

    }

    @Override
    public byte[] serialize(String topic, User user) {
        try {
            return user.toString().getBytes("UTF-8");
        } catch (UnsupportedEncodingException e) {
            e.printStackTrace();
            return null;
        }
    }

    @Override
    public void close() {

    }
}

```

```java
package producer;

import org.apache.kafka.clients.producer.KafkaProducer;
import org.apache.kafka.clients.producer.ProducerRecord;
import serializer.User;

import java.util.Properties;

/**
 * User Producer 自定义序列化器
 */
public class UserProducer {


    private static final String BROKER_ADDRESS = "localhost:9092";

    public static void main(String[] args) {

        //创建Properties对象 用于存储KafkaProducer配置信息 创建KafkaProducer
        Properties properties = new Properties();

        // ----------  必须属性  ------------

        //broker地址 如果是集群 填写一个或多个均可 会自动检索其他broker信息
        properties.setProperty("bootstrap.servers",BROKER_ADDRESS);
        //key序列化器 Kafka提供一些基础类型 可以自己实现kafka的Serializer接口定义序列化器
        properties.setProperty("key.serializer","org.apache.kafka.common.serialization.IntegerSerializer");
        //value序列化器 Kafka提供一些基础类型 可以自己实现kafka的Serializer接口定义序列化器
        properties.setProperty("value.serializer","serializer.UserSerializer");

        //-----------  非必须属性 -------------

        //acks
        properties.put("acks","all");
        //重试次数
        properties.put("retries",3);

        //创建一个生产者
        KafkaProducer producer = new KafkaProducer<Integer, User>(properties);

        //创建一条消息
        User user = new User();
        user.setUserId(3l);
        user.setUserName("lzx");
        ProducerRecord<Integer, User> record = new ProducerRecord<Integer, User>("demo",5,user);

        //发送不关注结果
        producer.send(record);

        //同步发送
//        Future future = producer.send(record);
//        try {
//            Object o = future.get();
//            System.out.println(o);
//        } catch (InterruptedException e) {
//            e.printStackTrace();
//        } catch (ExecutionException e) {
//            e.printStackTrace();
//        }

        //异步发送
//        producer.send(record, new Callback() {
//            public void onCompletion(RecordMetadata recordMetadata, Exception e) {
//                if (e == null){
//                    System.out.println("消息发送成功，Topic： " + recordMetadata.topic() + " , partitions: "
//                            + recordMetadata.partition() + " , offset : " + recordMetadata.offset());
//                }else {
//                    System.out.println(e.getMessage());
//                }
//
//            }
//        });

        //刷新缓冲区
        producer.flush();

        //关闭
        producer.close();

    }



}

```

![image-20200108114427035](http://rocks526.top/lzx/image-20200108114427035.png)



由于自定义序列化器一般性能较弱，而且兼容性较弱，因此一般建议采用成熟的第三方序列化框架，如fastjson，jackson，Protobuf，Thrift，Avro等。

## 分区器

ProducerRecord对象里有一个key属性可选，该属性可用来进行分区选择，当key属性未添加时，默认为null，Kafka默认分区器会对key未null的消息采取轮询算法选择分区。如果key不为null，Kafka会采用Hash算法进行分区选择，相同key的消息总是会落到同一个分区，但是需要注意的是，当分区数量改变时，消息可能会被路由到其他分区，因此建议设置好分区数量后不要再进行改变。

Kafka除了提供的默认分区器外，还提供了org.apache.kafka.clients.producer.Partitioner接口，可以通过实现该接口实现自定义分区算法。

### 自定义分区器

```java
package consumer.partitioner;

import org.apache.kafka.clients.producer.Partitioner;
import org.apache.kafka.common.Cluster;
import org.apache.kafka.common.PartitionInfo;
import org.apache.kafka.common.record.InvalidRecordException;

import java.util.List;
import java.util.Map;

/**
 * 自定义分区算法
 */
public class MyPartitioner implements Partitioner {

    @Override
    public int partition(String topic, Object key, byte[] keyBytes, Object value, byte[] valueBytes, Cluster cluster) {

        //获取所有分区信息
        List<PartitionInfo> partitionInfos = cluster.partitionsForTopic(topic);
        //分区数
        int partitionsNumber = partitionInfos.size();

        //指定自己的路由规则
        if (keyBytes == null || !(key instanceof String)){
            throw new InvalidRecordException("The key of Message should be a String!");
        }
        //partitionsOne的key全部放到第一个分区
        if (((String)key).equals("partitionsOne")){
            return 0;
        }
        //其他key全部放到最后一个分区
        return partitionsNumber;
    }

    @Override
    public void close() {

    }

    @Override
    public void configure(Map<String, ?> map) {

    }
}

```

KafkaProducer通过partitioner.class属性配置使用何种分区器

```java
package producer;

import org.apache.kafka.clients.producer.Callback;
import org.apache.kafka.clients.producer.KafkaProducer;
import org.apache.kafka.clients.producer.ProducerRecord;
import org.apache.kafka.clients.producer.RecordMetadata;

import java.util.Properties;

/**
 * Partitions Producer 自定义分区策略
 */
public class PartitionsProducer {

    private static final String BROKER_ADDRESS = "localhost:9092";

    public static void main(String[] args) {

        //创建Properties对象 用于存储KafkaProducer配置信息 创建KafkaProducer
        Properties properties = new Properties();

        // ----------  必须属性  ------------

        //broker地址 如果是集群 填写一个或多个均可 会自动检索其他broker信息
        properties.setProperty("bootstrap.servers",BROKER_ADDRESS);
        //key序列化器 Kafka提供一些基础类型 可以自己实现kafka的Serializer接口定义序列化器
        properties.setProperty("key.serializer","org.apache.kafka.common.serialization.StringSerializer");
        //value序列化器 Kafka提供一些基础类型 可以自己实现kafka的Serializer接口定义序列化器
        properties.setProperty("value.serializer","org.apache.kafka.common.serialization.StringSerializer");

        //-----------  非必须属性 -------------

        //acks
        properties.put("acks","all");
        //重试次数
        properties.put("retries",3);
        //设置自定义分区器
        properties.put("partitioner.class","partitioner.MyPartitioner");

        //创建一个生产者
        KafkaProducer producer = new KafkaProducer<String,String>(properties);

        //创建一条消息
//        ProducerRecord<String, String> record = new ProducerRecord<String, String>("demo","partitionsOne","This is partitionsOne Message!");
//        ProducerRecord<String, String> record = new ProducerRecord<String, String>("demo","This is null Message!");
        ProducerRecord<String, String> record = new ProducerRecord<String, String>("demo","other","This is other Message!");


        //发送不关注结果
//        producer.send(record);

        //同步发送
//        Future future = producer.send(record);
//        try {
//            Object o = future.get();
//            System.out.println(o);
//        } catch (InterruptedException e) {
//            e.printStackTrace();
//        } catch (ExecutionException e) {
//            e.printStackTrace();
//        }

        //异步发送
        producer.send(record, new Callback() {
            public void onCompletion(RecordMetadata recordMetadata, Exception e) {
                if (e == null){
                    System.out.println("消息发送成功，Topic： " + recordMetadata.topic() + " , partitions: "
                            + recordMetadata.partition() + " , offset : " + recordMetadata.offset());
                }else {
                    System.out.println(e.getMessage());
                }

            }
        });

        //刷新缓冲区
        producer.flush();

        //关闭
        producer.close();

    }


}

```

# Kafka消费者

## Kafka消费者介绍

kafka和其他MQ一样，都实现了基于队列和发布订阅两种模式的消息发布与消费，与其他MQ不同的是，kafka除了producer，consumer，broker这些概念之外，还提供了消费者群组，分区等独有机制。

## 消费者群组与分区机制

一个消费者群组包括多个消费者，当多个消费者订阅同一个主题时，这些消费者会分别消费该主题的部分分区，多个消费者可以并行工作，加快消费速度，提高吞吐量。

将分区机制和消费者群组机制的结合，kafka通过同一个消费者群组多个消费者订阅同一个主题实现了队列模型，同时提高了消费吞吐量，不同的消费者群组间实现了发布订阅模型。

需要注意的是，同一个群组多个消费者消费同一个主题时，消费者是以分区为单位消费的，同一个分区只能一个消费者消费，因此分区的数量应该大于等于消费者数量，如果消费者数量大于分区数，则多出来的消费者会闲置。

### 消费者群组与分区再均衡

当订阅了同一个主题的新消费者加入群组时，会从其他消费者那里接管一些分区过来，而当群组里的消费者离开时，原本由他读取的分区也会分配给其他消费者，或者当管理员添加了新的分区时，以上这些情况都会导致分区重新分配，分配的这个过程称为消费者群组与分区再均衡，这个过程期间，数据是无法消费的，应该尽量减短或避免。

### 分配分区过程

每个群组有一个协调器，当消费者要加入分区时，它会向群组协调器发送一个JoinGroup请求，第一个加入群组的消费者将称为群主，群主负责从协调器那里获得群组成员列表，并负责给每个成员分配分区，通过一个实现了PartitionAssignor接口的类来决定分配方案。分配完成之后，群主把分配情况发送给群组协调器，协调器再转发给各个消费者。

kafka内置了两种分区分配策略，由partition.assignment.strategy属性指定：

Range：把主题的若干个连续分区分配给消费者。如消费者C1和C2，主题T1和T2各有三个分区，那么消费者C1可能分到两个主题的分区0和分区1，消费者C2可能分到两个主题的分区3，分配并不均衡，只是以主题为单位的均衡

RoundRbin：把主题的所有分区逐个分配给消费者。如果是刚才情景，此策略下，消费者C1可能分到主题T1的分区0,2和主题T2的分区1，而消费者C2可能分到主题T1的分区1和主题T2的分区0,2，分配比较均衡，最多相差一个分区

默认情况下，partition.assignment.strategy属性的值是org.apache.kafka.clients.consumer.RangeAssignor，这个类实现了Range策略，也可以配置org.apache.kafka.clients.consumer.RoundRobinAssignor，这个类实现了RoundRobin策略，或者自定义一个实现了PartitionAssignor接口的类

## 消费者代码示例

```java
package consumer;

import org.apache.kafka.clients.consumer.ConsumerRecord;
import org.apache.kafka.clients.consumer.ConsumerRecords;
import org.apache.kafka.clients.consumer.KafkaConsumer;
import org.apache.kafka.common.PartitionInfo;
import org.apache.kafka.common.TopicPartition;

import java.util.Arrays;
import java.util.Collections;
import java.util.List;
import java.util.Properties;

/**
 *  Kafka Consumer Test
 */
public class Consumer {


    //broker地址
    private static final String BROKER_ADDRESS = "localhost:9092";


    public static void main(String[] args) {

        //创建Properties对象 用于存储KafkaConsumer配置信息 创建KafkaConsumer
        Properties properties = new Properties();

        // ----------  必须属性  ------------

        //broker地址 如果是集群 填写一个或多个均可 会自动检索其他broker信息
        properties.setProperty("bootstrap.servers",BROKER_ADDRESS);
        //key反序列化器 Kafka提供一些基础类型 可以自己实现kafka的Serializer接口定义反序列化器
        properties.setProperty("key.deserializer","org.apache.kafka.common.serialization.IntegerDeserializer");
        //value反序列化器 Kafka提供一些基础类型 可以自己实现kafka的Serializer接口定义反序列化器
        properties.setProperty("value.deserializer","org.apache.kafka.common.serialization.StringDeserializer");

        //-----------  非必须属性 -------------

        //消费者群组
        properties.put("group.id","demo-group1");
        //自动提交偏移量
        properties.put("enable.auto.commit","true");
        //当offset丢失时从哪开始读取
        properties.put("auto.offset.reset","earliest");

        //创建一个消费者
        KafkaConsumer<Integer,String> consumer = new KafkaConsumer<Integer,String>(properties);

        //订阅主题集合  可传入正则表达式
        consumer.subscribe(Collections.singletonList("demo"));
        //获取demo主题的所有分区
        List<PartitionInfo> partitions = consumer.partitionsFor("demo");
        //订阅分区
        //consumer.assign(Arrays.asList(new TopicPartition("demo",0)));

        //死循环不断拉取消息
        try{
            while (true){
                //100是超时时间 当消费者缓冲区没有数据时的阻塞时间
                ConsumerRecords<Integer,String> records = consumer.poll(100);
                for(ConsumerRecord record : records){
                    System.out.println("Topic : " + record.topic() + " , partition : " + record.partition()
                    + " , offset : " + record.offset() + " , key : " + record.key() + " , value : " + record.value());
                }
            }
        }catch (Exception e){
            System.out.println(e.getMessage());
        }finally {
            consumer.close();
        }


    }




}

```

KafkaConsumer通过一个消息轮询，不断从broker拉取消息，轮询会将群组协调，分区再均衡，发送心跳，获取数据等所有细节全部处理好。

需要注意的是，消费者订阅主题和分区只能选择其中一个

## 消费者提交和偏移量

每次调用poll方法会返回已经写入broker但还未被消费过的消息，那么消费者是怎么判断消息是否被消费过？其他MQ大多采用消费者确认的方式，而Kafka的消费者可以通过broker来追踪消息消费位置，消费之后更新broker里存储的最新消费位置

消费者通过向一个叫做_consumer_offset的特殊主题里发送消息，该消息包含了每个分区的偏移量，broker将所有分区的最新偏移量存储于该主题。当某个消费者退出或者加入集群，触发再均衡时，如果某个分区被别的消费者接管，则该消费者会从broker的_consumer_offset主题获取最新偏移量

此种消息更新方式，如果提交的偏移量小于消费者处理的最后一个消息偏移量，那么就会重复消费，反之，则会丢失消息

为了实现不同的需求，Kafka实现了多种提交方式：

### 自动提交

如果enable.auto.commit属性设置为true，那么每过5s，消费者会自动把从poll方法接收到的最大偏移量提交上去，提交时间间隔由auto.commit.interval.ms属性指定，默认5s。

此种方式虽然简便，但存在丢失消息的可能性，如poll之后3s发生再均衡，刚刚poll下来的消息消费掉了，但还未更新偏移量，当该分区再均衡之后交给其他消费者，则会出现这3s的消息重复。

而且poll下来的消息可能其中某次没有处理成功，但只要后续的处理成功，提交了最新的偏移量，之前没有处理成功的消息也会被消费，从而导致丢失消息。

### 同步提交

由于自动提交可能导致消息重复或丢失，因此可以选择手动提交消息，通过commitSync()方法同步提交，该方法会提交poll方法获取的最新的偏移量，调用之后成功则返回，失败则抛出异常，由于该方法同步阻塞，很影响吞吐量，我们可以通过降低提交频率来提升吞吐量，但又增大了再均衡时消息重复的概率。

同步提交同样不能poll消息之后后续逻辑处理失败的问题，如果之后的消息处理成功，会提交最新的偏移量，导致消息丢失。

### 异步提交

由于同步提交是阻塞的，影响吞吐量，因此kafka也提供了异步提交方式，可以通过调用commitAsync方法来异步提交，该方法立即返回，不等待broker响应。

与commitSync不同的是，同步提交会自动重试，而异步提交不会，这是因为由于异步的原因，可能先提交2000失败，后提交3000成功，如果再次重试2000可能导致3000被覆盖，从而导致重复消费。

异步提交支持一个回调函数。

### 同步与异步组合

一般情况下，针对偶尔出现的提交失败，不需要进行重试，一般是临时问题导致的，后续会提交成功，而如果是发生在再均衡或者关闭消费者之前的最后一次提交，就要保证提交成功。

因此可以采用同步与异步组合的方式，poll循环里异步提交加快速度，consumer关闭之前同步提交确保成功。

示例：

```java
package consumer;


import org.apache.kafka.clients.consumer.ConsumerRecord;
import org.apache.kafka.clients.consumer.ConsumerRecords;
import org.apache.kafka.clients.consumer.KafkaConsumer;
import org.apache.kafka.common.PartitionInfo;

import java.util.Collections;
import java.util.List;
import java.util.Properties;

/**
 * Consumer CommitSync & CommitAsync
 */
public class CommitConsumer {


    //broker地址
    private static final String BROKER_ADDRESS = "localhost:9092";


    public static void main(String[] args) {

        //创建Properties对象 用于存储KafkaConsumer配置信息 创建KafkaConsumer
        Properties properties = new Properties();

        // ----------  必须属性  ------------

        //broker地址 如果是集群 填写一个或多个均可 会自动检索其他broker信息
        properties.setProperty("bootstrap.servers",BROKER_ADDRESS);
        //key反序列化器 Kafka提供一些基础类型 可以自己实现kafka的Serializer接口定义反序列化器
        properties.setProperty("key.deserializer","org.apache.kafka.common.serialization.IntegerDeserializer");
        //value反序列化器 Kafka提供一些基础类型 可以自己实现kafka的Serializer接口定义反序列化器
        properties.setProperty("value.deserializer","org.apache.kafka.common.serialization.StringDeserializer");

        //-----------  非必须属性 -------------

        //消费者群组
        properties.put("group.id","demo-group1");
        //自动提交偏移量
        properties.put("enable.auto.commit","false");
        //当offset丢失时从哪开始读取
        properties.put("auto.offset.reset","earliest");

        //创建一个消费者
        KafkaConsumer<Integer,String> consumer = new KafkaConsumer<Integer,String>(properties);

        //订阅主题集合  可传入正则表达式
        consumer.subscribe(Collections.singletonList("demo"));
        //获取demo主题的所有分区
        List<PartitionInfo> partitions = consumer.partitionsFor("demo");
        //订阅分区
        //consumer.assign(Arrays.asList(new TopicPartition("demo",0)));

        //死循环不断拉取消息
        try{
            while (true){
                //100是超时时间 当消费者缓冲区没有数据时的阻塞时间
                ConsumerRecords<Integer,String> records = consumer.poll(100);
                for(ConsumerRecord record : records){
                    System.out.println("Topic : " + record.topic() + " , partition : " + record.partition()
                            + " , offset : " + record.offset() + " , key : " + record.key() + " , value : " + record.value());
                }
                consumer.commitAsync();
            }
        }catch (Exception e){
            System.out.println(e.getMessage());
        }finally {
            try{
                consumer.commitSync();
            }catch (Exception e){
                //TODO
            }
            consumer.close();
        }


    }
    
    
}

```

### 提交特定的偏移量

以上方式提交时会自动提交poll下来的消息的最大偏移量，那么如果每次poll消息量很大，为了减少损失，想要处理一部分之后就提交，而不是每次poll提交，可以采用下面的办法。

consumer的手动提交方法允许提交时传入一个map，存储分区和最新偏移量，可以通过此种方式实现一次poll多次提交偏移量。

### 再均衡监听器

kafka除了以上提交方式之外，还提供了一个再均衡监听器，可以通过实现org.apache.kafka.clients.consumer.ConsumerRebalanceListener接口实现自定义再均衡监听器，完成在再均衡之前提交最新偏移量，监听器可以在订阅主题时传入。

监听器：

```java
package consumer;

import lombok.Data;
import org.apache.kafka.clients.consumer.ConsumerRebalanceListener;
import org.apache.kafka.clients.consumer.KafkaConsumer;
import org.apache.kafka.clients.consumer.OffsetAndMetadata;
import org.apache.kafka.common.TopicPartition;

import java.util.Collection;
import java.util.HashMap;

/**
 * 再均衡监听器
 */
public class HandlerBeforeRebalance implements ConsumerRebalanceListener {


    public HandlerBeforeRebalance(KafkaConsumer consumer){
        this.consumer = consumer;
    }

    private KafkaConsumer consumer;

    //存储每个分区和最新偏移量
    public HashMap<TopicPartition, OffsetAndMetadata> currentOffset = new HashMap<>();

    //再均衡开始之前，消费者停止读取消息之后调用
    @Override
    public void onPartitionsRevoked(Collection<TopicPartition> collection) {
        //提交最新偏移量 最新偏移量的更新由consumer的poll中完成
        consumer.commitSync(currentOffset);
    }

    //再均衡分区分配完成之后，新的消费者读取之前执行
    @Override
    public void onPartitionsAssigned(Collection<TopicPartition> collection) {

    }
}

```

示例代码：

```java
package consumer;

import org.apache.kafka.clients.consumer.ConsumerRecord;
import org.apache.kafka.clients.consumer.ConsumerRecords;
import org.apache.kafka.clients.consumer.KafkaConsumer;
import org.apache.kafka.clients.consumer.OffsetAndMetadata;
import org.apache.kafka.common.PartitionInfo;
import org.apache.kafka.common.TopicPartition;

import java.util.Collections;
import java.util.List;
import java.util.Properties;

/**
 * 通过再均衡监听器提交偏移量
 */
public class ListenerConsumer {


    //broker地址
    private static final String BROKER_ADDRESS = "localhost:9092";


    public static void main(String[] args) {

        //创建Properties对象 用于存储KafkaConsumer配置信息 创建KafkaConsumer
        Properties properties = new Properties();

        // ----------  必须属性  ------------

        //broker地址 如果是集群 填写一个或多个均可 会自动检索其他broker信息
        properties.setProperty("bootstrap.servers",BROKER_ADDRESS);
        //key反序列化器 Kafka提供一些基础类型 可以自己实现kafka的Serializer接口定义反序列化器
        properties.setProperty("key.deserializer","org.apache.kafka.common.serialization.IntegerDeserializer");
        //value反序列化器 Kafka提供一些基础类型 可以自己实现kafka的Serializer接口定义反序列化器
        properties.setProperty("value.deserializer","org.apache.kafka.common.serialization.StringDeserializer");

        //-----------  非必须属性 -------------

        //消费者群组
        properties.put("group.id","demo-group1");
        //自动提交偏移量
        properties.put("enable.auto.commit","false");
        //当offset丢失时从哪开始读取
        properties.put("auto.offset.reset","earliest");

        //创建一个消费者
        KafkaConsumer<Integer,String> consumer = new KafkaConsumer<Integer,String>(properties);

        //订阅主题集合并传入再均衡监听器
        HandlerBeforeRebalance handlerBeforeRebalance = new HandlerBeforeRebalance(consumer);
        consumer.subscribe(Collections.singletonList("demo"),handlerBeforeRebalance);
        //获取demo主题的所有分区
        //List<PartitionInfo> partitions = consumer.partitionsFor("demo");
        //订阅分区
        //consumer.assign(Arrays.asList(new TopicPartition("demo",0)));

        //死循环不断拉取消息
        try{
            while (true){
                //100是超时时间 当消费者缓冲区没有数据时的阻塞时间
                ConsumerRecords<Integer,String> records = consumer.poll(100);
                for(ConsumerRecord record : records){
                    System.out.println("Topic : " + record.topic() + " , partition : " + record.partition()
                            + " , offset : " + record.offset() + " , key : " + record.key() + " , value : " + record.value());
                    //更新分区和偏移量信息
                    handlerBeforeRebalance.currentOffset.put(new TopicPartition(record.topic(),record.partition())
                            ,new OffsetAndMetadata(record.offset()+1,"no metadata"));
                }
                consumer.commitAsync(handlerBeforeRebalance.currentOffset,null);
            }
        }catch (Exception e){
            System.out.println(e.getMessage());
        }finally {
            try{
                consumer.commitSync(handlerBeforeRebalance.currentOffset);
            }catch (Exception e){
                //TODO
            }
            consumer.close();
        }


    }



}

```

### 确保消息不会丢失且不会重复

通过再均衡监听器看起来好像可以实现消息不会重复消费，但仍然存在例外，如果消息处理完之后，偏移量提交之前程序崩溃，仍然会导致重复消息。

因此需要一个原子操作，将消息的处理和偏移量的提交一起执行。可以借助第三方的数据库的事务操作来完成消息的严格性，通过将偏移量记录在数据库里，在一个事务里提交偏移量和处理数据。

此时最新偏移量被保存在数据库，而不是broker上，那么再均衡时可以通过再均衡监听器去数据库获取最新偏移量，然后利用seek方法跳到指定偏移位置消费数据，如此即可实现消息不会丢失且不会重复

## 优雅退出

消费者通过poll循环不断拉取消息，可以通过另一个线程调用consumer.wakeup方法来打断消费者，抛出WakeupException异常来打断死循环，进而退出消费。

## 消费者配置

### 必须配置

1. bootstrap.servers：broker地址 如果是集群 填写一个或多个均可 会自动检索其他broker信息
2. key.deserializer：key反序列化器 Kafka提供一些基础类型 也可以自己实现kafka的Serializer接口定义序列化器，需要注意的是即便发送消息不设置key也必须设置key反序列化器
3. value.deserializer：value序列化器，同上

### 可选配置

1. fetch.min.bytes

该属性指定了消费者从broker获取记录的最小字节数，用于较少网络IO

2. fetch.max.wait.ms

该属性用于配合上面的fetch.min.bytes属性，用于指定最长等待broker准备数据时间，避免长时间等待

3. max.partitio.fetch.bytes

该属性指定了服务器从每个分区里返回给消费者的最大字节数

4. session.timeout.ms

消费者被认为死亡之前可以与服务器断开连接的时间

5. auto.offset.reset

当消费者读取一个没有偏移量的分区或者偏移量无效时采取的方案，earliest从头读取，latest从之后最新的读取，默认latest

6. enable.auto.commit

自动提交偏移量

7. partition.assignment.strategy

分区分配策略

8. client.id

客户端标识

9. max.poll.records

每次call方法调用返回的最大记录数

10. recevice.buffer.bytes和send.buffer.bytes

TCP缓冲器大小，-1根据操作系统默认设置

## 反序列化器

```java
package serializer;

import org.apache.kafka.common.serialization.Deserializer;

import java.util.Map;

/**
 * User DeSerializer
 */
public class UserDeSerializer implements Deserializer {
    @Override
    public void configure(Map map, boolean b) {
        
    }

    @Override
    public Object deserialize(String topic, byte[] bytes) {
        return new String(bytes);
    }

    @Override
    public void close() {

    }
}

```

# Kafka存储

## kafka存储结构

- Kafka中的消息以主题为基本单位进行归类，各个主题在逻辑上相互独立，每个主题又可以指定分区，通过分区将消息分散到多个节点上，实现水平扩展，又引入副本机制保证数据的冗余。

- 物理存储上，主题只是逻辑结构，存储以分区为单位，每个分区对应一个文件夹，命名为主题名-分区名，该文件夹包含一个日志文件和两个索引文件，以及其他可能的文件（比如以“.txnindex”为后缀的事务索引文件），由于每个分区文件太大难以维护，因此kafka引入日志分段的概念，将单个log切分为多个logSegment。
- 向Log 中追加消息时是顺序写入的，只有最后一个 LogSegment 才能执行写入操作，在此之前所有的 LogSegment 都不能写入数据。
- 为了便于消息的检索，每个LogSegment中的日志文件（以“.log”为文件后缀）都有对应的两个索引文件：偏移量索引文件（以“.index”为文件后缀）和时间戳索引文件（以“.timeindex”为文件后缀）

## kafka消息格式

对一个成熟的消息中间件而言，消息格式（或者称为“日志格式”）不仅关系功能维度的扩展，还牵涉性能维度的优化。随着 Kafka 的迅猛发展，其消息格式也在不断升级改进，从0.8.x版本开始到现在的2.0.0版本，Kafka的消息格式也经历了3个版本：v0版本、v1版本和v2版本。

### V0版本

Kafka消息格式的第一个版本通常称为v0版本，在Kafka 0.10.0之前都采用的这个消息格式（在0.8.x版本之前，Kafka还使用过一个更古老的消息格式，不过对目前的Kafka而言，我们也不需要了解这个版本的消息格式）。

![image-20200112194414970](http://rocks526.top/lzx/image-20200112194414970.png)

- crc32（4B）：crc32校验值。校验范围为magic至value之间
- magic（1B）：消息格式版本号，此版本的magic值为0
- attributes（1B）：消息的属性。总共占1个字节，低3位表示压缩类型：0表示NONE、1表示GZIP、2表示SNAPPY、3表示LZ4（LZ4自Kafka 0.9.x引入），其余位保留
- key length（4B）：表示消息的key的长度。如果为-1，则表示没有设置key，即key=null
- key：可选，如果没有key则无此字段
- value length（4B）：实际消息体的长度。如果为-1，则表示消息为空。
- value：消息体。可以为空，比如墓碑（tombstone）消息。

v0版本中一个消息的最小长度（RECORD_OVERHEAD_V0）为crc32+magic+attributes+keylength+value length=4B+1B+1B+4B+4B=14B。也就是说，v0版本中一条消息的最小长度为14B，如果小于这个值，那么这就是一条破损的消息而不被接收。

### V1版本

Kafka从0.10.0版本开始到0.11.0版本之前所使用的消息格式版本为v1，比v0版本就多了一个timestamp字段，表示消息的时间戳。v1版本的消息结构如图所示。

![image-20200112194709267](http://rocks526.top/lzx/image-20200112194709267.png)

v1版本的magic字段的值为1。v1版本的attributes字段中的低3位和v0版本的一样，还是表示压缩类型，而第4个位（bit）也被利用了起来：0表示timestamp类型为CreateTime，而1表示timestamp类型为LogAppendTime，其他位保留。

v1 版本的消息的最小长度（RECORD_OVERHEAD_V1）要比 v0 版本的大 8 个字节，即22B。

### V2版本

Kafka从0.11.0版本开始所使用的消息格式版本为v2，这个版本的消息相比v0和v1的版本而言改动很大，同时还参考了Protocol Buffer而引入了变长整型（Varints）和ZigZag编码。

v2版本中消息集称为Record Batch，而不是先前的Message Set，其内部也包含了一条或多条消息，消息的格式参见图5-7的中部和右部。在消息压缩的情形下，Record Batch Header部分（参见图5-7左部，从first offset到records count字段）是不被压缩的，而被压缩的是records字段中的所有内容。生产者客户端中的ProducerBatch对应这里的RecordBatch，而ProducerRecord对应这里的Record。

![image-20200112195720639](http://rocks526.top/lzx/image-20200112195720639.png)

先讲述消息格式Record的关键字段，可以看到内部字段大量采用了Varints，这样Kafka可以根据具体的值来确定需要几个字节来保存。v2版本的消息格式去掉了crc字段，另外增加了length（消息总长度）、timestampdelta（时间戳增量）、offset delta（位移增量）和headers信息，并且attributes字段被弃用了

- length：消息总长度。
- attributes：弃用，但还是在消息格式中占据1B的大小，以备未来的格式扩展。
- timestamp delta：时间戳增量。通常一个timestamp需要占用8个字节，如果像这里一样保存与RecordBatch的起始时间戳的差值，则可以进一步节省占用的字节数。
- offset delta：位移增量。保存与 RecordBatch起始位移的差值，可以节省占用的字节数。
- headers：这个字段用来支持应用级别的扩展，而不需要像v0和v1版本一样不得不将一些应用级别的属性值嵌入消息体。Header的格式如图最右部分所示，包含key和value，一个Record里面可以包含0至多个Header。

对于 v1 版本的消息，如果用户指定的 timestamp 类型是 LogAppendTime 而不是CreateTime，那么消息从生产者进入 broker 后，timestamp 字段会被更新，此时消息的 crc值将被重新计算，而此值在生产者中已经被计算过一次。再者，broker 端在进行消息格式转换时（比如v1版转成v0版的消息格式）也会重新计算crc的值。在这些类似的情况下，消息从生产者到消费者之间流动时，crc的值是变动的，需要计算两次crc的值，所以这个字段的设计在 v0 和 v1 版本中显得比较“鸡肋”。在 v2 版本中将 crc 的字段从 Record 中转移到了RecordBatch中。

v2版本对消息集（RecordBatch）做了彻底的修改，参考图5-7最左部分，除了刚刚提及的crc字段，还多了如下字段。

- first offset：表示当前RecordBatch的起始位移。
- length：计算从partition leader epoch字段开始到末尾的长度。
- partition leader epoch：分区leader纪元，可以看作分区leader的版本号或更新次数
- magic：消息格式的版本号，对v2版本而言，magic等于2。
- attributes：消息属性，注意这里占用了两个字节。低3位表示压缩格式，可以参考v0和v1；第4位表示时间戳类型；第5位表示此RecordBatch是否处于事务中，0表示非事务，1表示事务。第6位表示是否是控制消息（ControlBatch），0表示非控制消息，而1表示是控制消息，控制消息用来支持事务功能
- last offset delta：RecordBatch中最后一个Record的offset与first offset的差值。主要被broker用来确保RecordBatch中Record组装的正确性。
- first timestamp：RecordBatch中第一条Record的时间戳。
- max timestamp：RecordBatch 中最大的时间戳，一般情况下是指最后一个 Record的时间戳，和last offsetdelta的作用一样，用来确保消息组装的正确性。
- producer id：PID，用来支持幂等和事务，详细内容请参考7.4节。
- producer epoch：和producer id一样，用来支持幂等和事务
- first sequence：和 producer id、producer epoch 一样，用来支持幂等和事务
- records count：RecordBatch中Record的个数。

v2版本的消息不仅提供了更多的功能，比如事务、幂等性等，某些情况下还减少了消息的空间占用，总体性能提升很大。

## 消息压缩

- 常见的压缩算法是数据量越大压缩效果越好，一条消息通常不会太大，这就导致压缩效果并不是太好。而Kafka实现的压缩方式是将多条消息一起进行压缩，这样可以保证较好的压缩效果。在一般情况下，生产者发送的压缩数据在broker中也是保持压缩状态进行存储的，消费者从服务端获取的也是压缩的消息，消费者在处理消息之前才会解压消息，这样保持了端到端的压缩。

- Kafka 日志中使用哪种压缩方式是通过参数 compression.type 来配置的，默认值为“producer”，表示保留生产者使用的压缩方式。这个参数还可以配置为“gzip”“snappy”“lz4”，分别对应 GZIP、SNAPPY、LZ4 这3 种压缩算法。如果参数 compression.type 配置为“uncompressed”，则表示不压缩。

以上都是针对消息未压缩的情况，而当消息压缩时是将整个消息集进行压缩作为内层消息（inner message），内层消息整体作为外层（wrapper message）的 value，其结构如图所示。

![image-20200112195017361](http://rocks526.top/lzx/image-20200112195017361.png)

![image-20200112195139035](http://rocks526.top/lzx/image-20200112195139035.png)

- 压缩后的外层消息（wrapper message）中的key为null，所以图5-5左半部分没有画出key字段，value字段中保存的是多条压缩消息（inner message，内层消息），其中Record表示的是从 crc32 到 value 的消息格式。当生产者创建压缩消息的时候，对内部压缩消息设置的offset从0开始为每个内部消息分配offset
- 其实每个从生产者发出的消息集中的消息offset都是从0开始的，当然这个offset不能直接存储在日志文件中，对offset 的转换是在服务端进行的，客户端不需要做这个工作。外层消息保存了内层消息中最后一条消息的绝对位移（absolute offset），绝对位移是相对于整个分区而言的

## 日志索引

每个日志分段文件对应了两个索引文件，主要用来提高查找消息的效率。偏移量索引文件用来建立消息偏移量（offset）到物理地址之间的映射关系，方便快速定位消息所在的物理文件位置；时间戳索引文件则根据指定的时间戳（timestamp）来查找对应的偏移量信息。

Kafka 中的索引文件以稀疏索引（sparse index）的方式构造消息的索引，它并不保证每个消息在索引文件中都有对应的索引项。每当写入一定量（由 broker 端参数 log.index.interval.bytes指定，默认值为4096，即4KB）的消息时，偏移量索引文件和时间戳索引文件分别增加一个偏移量索引项和时间戳索引项，增大或减小log.index.interval.bytes的值，对应地可以增加或缩小索引项的密度。

偏移量索引文件中的偏移量是单调递增的，查询指定偏移量时，使用二分查找法来快速定位偏移量的位置，如果指定的偏移量不在索引文件中，则会返回小于指定偏移量的最大偏移量。时间戳索引文件中的时间戳也保持严格的单调递增，查询指定时间戳时，也根据二分查找法来查找不大于该时间戳的最大偏移量，至于要找到对应的物理文件位置还需要根据偏移量索引文件来进行再次定位。稀疏索引的方式是在磁盘空间、内存空间、查找时间等多方面之间的一个折中。

日志分段文件达到一定的条件时需要进行切分，那么其对应的索引文件也需要进行切分。日志分段文件切分包含以下几个条件，满足其一即可：

1. 当前日志分段文件的大小超过了 broker 端参数 log.segment.bytes 配置的值。log.segment.bytes参数的默认值为1073741824，即1GB。

2. 当前日志分段中消息的最大时间戳与当前系统的时间戳的差值大于 log.roll.ms或log.roll.hours参数配置的值。如果同时配置了log.roll.ms和log.roll.hours参数，那么log.roll.ms的优先级高。默认情况下，只配置了log.roll.hours参数，其值为168，即7天。

3. 偏移量索引文件或时间戳索引文件的大小达到broker端参数log.index.size.max.bytes配置的值。log.index.size.max.bytes的默认值为10485760，即10MB。

4. 追加的消息的偏移量与当前日志分段的偏移量之间的差值大于Integer.MAX_VALUE，即要追加的消息的偏移量不能转变为相对偏移量（offset-baseOffset＞Integer.MAX_VALUE）。

对非当前活跃的日志分段而言，其对应的索引文件内容已经固定而不需要再写入索引项，所以会被设定为只读。而对当前活跃的日志分段（activeSegment）而言，索引文件还会追加更多的索引项，所以被设定为可读写。在索引文件切分的时候，Kafka 会关闭当前正在写入的索引文件并置为只读模式，同时以可读写的模式创建新的索引文件，索引文件的大小由broker端参数 log.index.size.max.bytes 配置。Kafka 在创建索引文件的时候会为其预分配log.index.size.max.bytes 大小的空间

### 偏移量索引

偏移量索引项的格式如图5-8所示。每个索引项占用8个字节，分为两个部分。

1. relativeOffset：相对偏移量，表示消息相对于baseOffset 的偏移量，占用4 个字节，当前索引文件的文件名即为baseOffset的值。

2. position：物理地址，也就是消息在日志分段文件中对应的物理位置，占用4个字节。

![image-20200112202316806](http://rocks526.top/lzx/image-20200112202316806.png)

消息的偏移量（offset）占用8个字节，也可以称为绝对偏移量。索引项中没有直接使用绝对偏移量而改为只占用4个字节的相对偏移量（relativeOffset=offset-baseOffset），这样可以减小索引文件占用的空间。

索引查找过程：利用跳表根据文件名查询所属分段-》二分法查询前一个偏移量-》顺序扫描查找文件物理地址

需要注意的是，Kafka 强制要求索引文件大小必须是索引项大小的整数倍，对偏移量索引文件而言，必须为8的整数倍。如果broker端参数log.index.size.max.bytes配置为67，那么Kafka在内部会将其转换为64，即不大于67，并且满足为8的整数倍的条件。

### 时间戳索引

时间戳索引项的格式如图5-11所示。

![image-20200112202837886](http://rocks526.top/lzx/image-20200112202837886.png)

每个索引项占用12个字节，分为两个部分。

1. timestamp：当前日志分段最大的时间戳。
2. relativeOffset：时间戳所对应的消息的相对偏移量。

时间戳索引文件中包含若干时间戳索引项，每个追加的时间戳索引项中的 timestamp 必须大于之前追加的索引项的 timestamp，否则不予追加。

与偏移量索引文件相似，时间戳索引文件大小必须是索引项大小（12B）的整数倍，如果不满足条件也会进行裁剪。

我们已经知道每当写入一定量的消息时，就会在偏移量索引文件和时间戳索引文件中分别增加一个偏移量索引项和时间戳索引项。两个文件增加索引项的操作是同时进行的，但并不意味着偏移量索引中的relativeOffset和时间戳索引项中的relativeOffset是同一个值。

时间戳索引查询过程：

1. 将targetTimeStamp和每个日志分段中的最大时间戳largestTimeStamp逐一对比，直到找到不小于targetTimeStamp 的 largestTimeStamp 所对应的日志分段。日志分段中的largestTimeStamp的计算是先查询该日志分段所对应的时间戳索引文件，找到最后一条索引项，若最后一条索引项的时间戳字段值大于0，则取其值，否则取该日志分段的最近修改时间。
2. 找到相应的日志分段之后，在时间戳索引文件中使用二分查找算法查找到不大于targetTimeStamp的最大索引项，即[1526384718283，28]，如此便找到了一个相对偏移量28。
3. 在偏移量索引文件中使用二分算法查找到不大于28的最大索引项，即[26，838]。
4. 从步骤1中找到日志分段文件中的838的物理位置开始查找不小于targetTimeStamp的消息。

## 日志清理

Kafka 将消息存储在磁盘中，为了控制磁盘占用空间的不断增加就需要对消息做一定的清理操作。Kafka 中每一个分区副本都对应一个 Log，而 Log 又可以分为多个日志分段，这样也便于日志的清理操作。Kafka提供了两种日志清理策略。

- 日志删除（Log Retention）：按照一定的保留策略直接删除不符合条件的日志分段。
- 日志压缩（Log Compaction）：针对每个消息的key进行整合，对于有相同key的不同value值，只保留最后一个版本。

我们可以通过broker端参数log.cleanup.policy来设置日志清理策略，此参数的默认值为“delete”，即采用日志删除的清理策略。如果要采用日志压缩的清理策略，就需要将log.cleanup.policy设置为“compact”，并且还需要将log.cleaner.enable（默认值为true）设定为true。通过将log.cleanup.policy参数设置为“delete，compact”，还可以同时支持日志删除和日志压缩两种策略。日志清理的粒度可以控制到主题级别，比如与log.cleanup.policy 对应的主题级别的参数为 cleanup.policy

### 日志删除

#### 基于时间的删除

日志删除任务会检查当前日志文件中是否有保留时间超过设定的阈值（retentionMs）来寻找可删除的日志分段文件集合（deletableSegments），如图5-13所示。retentionMs可以通过broker端参数log.retention.hours、log.retention.minutes和log.retention.ms来配置，其中 log.retention.ms 的优先级最高，log.retention.minutes 次之，log.retention.hours最低。默认情况下只配置了log.retention.hours参数，其值为168，故默认情况下日志分段文件的保留时间为7天。

查找过期的日志分段文件，并不是简单地根据日志分段的最近修改时间 lastModifiedTime来计算的，而是根据日志分段中最大的时间戳 largestTimeStamp 来计算的。因为日志分段的lastModifiedTime可以被有意或无意地修改，比如执行了touch操作，或者分区副本进行了重新分配，lastModifiedTime并不能真实地反映出日志分段在磁盘的保留时间。要获取日志分段中的最大时间戳 largestTimeStamp 的值，首先要查询该日志分段所对应的时间戳索引文件，查找时间戳索引文件中最后一条索引项，若最后一条索引项的时间戳字段值大于 0，则取其值，否则才设置为最近修改时间lastModifiedTime。

若待删除的日志分段的总数等于该日志文件中所有的日志分段的数量，那么说明所有的日志分段都已过期，但该日志文件中还要有一个日志分段用于接收消息的写入，即必须要保证有一个活跃的日志分段 activeSegment，在此种情况下，会先切分出一个新的日志分段作为activeSegment，然后执行删除操作。

删除日志分段时，首先会从Log对象中所维护日志分段的跳跃表中移除待删除的日志分段，以保证没有线程对这些日志分段进行读取操作。然后将日志分段所对应的所有文件添加上“.deleted”的后缀（当然也包括对应的索引文件）。最后交由一个以“delete-file”命名的延迟任务来删除这些以“.deleted”为后缀的文件，这个任务的延迟执行时间可以通过file.delete.delay.ms参数来调配，此参数的默认值为60000，即1分钟。

#### 基于大小的删除

日志删除任务会检查当前日志的大小是否超过设定的阈值（retentionSize）来寻找可删除的日志分段的文件集合（deletableSegments），如图5-14所示。retentionSize可以通过broker端参数log.retention.bytes来配置，默认值为-1，表示无穷大。注意log.retention.bytes配置的是Log中所有日志文件的总大小，而不是单个日志分段（确切地说应该为.log日志文件）的大小。单个日志分段的大小由 broker 端参数 log.segment.bytes 来限制，默认值为1073741824，即1GB。

基于日志大小的保留策略与基于时间的保留策略类似，首先计算日志文件的总大小size和retentionSize的差值diff，即计算需要删除的日志总大小，然后从日志文件中的第一个日志分段开始进行查找可删除的日志分段的文件集合 deletableSegments。查找出 deletableSegments 之后就执行删除操作，这个删除操作和基于时间的保留策略的删除操作相同

#### 基于日志起始偏移量

一般情况下，日志文件的起始偏移量 logStartOffset 等于第一个日志分段的 baseOffset，但这并不是绝对的，logStartOffset 的值可以通过 DeleteRecordsRequest 请求（比如使用KafkaAdminClient的deleteRecords（）方法、使用kafka-delete-records.sh脚本，具体用法参考9.1.3节）、日志的清理和截断等操作进行修改。

基于日志起始偏移量的保留策略的判断依据是某日志分段的下一个日志分段的起始偏移量baseOffset 是否小于等于logStartOffset，若是，则可以删除此日志分段。

### 日志压缩

Kafka中的Log Compaction是指在默认的日志删除（Log Retention）规则之外提供的一种清理过时数据的方式。如图5-16所示，Log Compaction对于有相同key的不同value值，只保留最后一个版本。如果应用只关心key对应的最新value值，则可以开启Kafka的日志清理功能，Kafka会定期将相同key的消息进行合并，只保留最新的value值。

Kafka中的Log Compaction可以类比于Redis中的RDB的持久化模式。

## 磁盘存储

Kafka 依赖于文件系统（更底层地来说就是磁盘）来存储和缓存消息。在我们的印象中，对于各个存储介质的速度认知大体同图5-20所示的相同，层级越高代表速度越快。很显然，磁盘处于一个比较尴尬的位置，这不禁让我们怀疑Kafka 采用这种持久化形式能否提供有竞争力的性能。在传统的消息中间件 RabbitMQ 中，就使用内存作为默认的存储介质，而磁盘作为备选介质，以此实现高吞吐和低延迟的特性。然而，事实上磁盘可以比我们预想的要快，也可能比我们预想的要慢，这完全取决于我们如何使用它。

![image-20200112204405217](http://rocks526.top/lzx/image-20200112204405217.png)

有关测试结果表明，一个由6块7200r/min的RAID-5阵列组成的磁盘簇的线性（顺序）写入速度可以达到600MB/s，而随机写入速度只有100KB/s，两者性能相差6000倍。操作系统可以针对线性读写做深层次的优化，比如预读（read-ahead，提前将一个比较大的磁盘块读入内存）和后写（write-behind，将很多小的逻辑写操作合并起来组成一个大的物理写操作）技术。顺序写盘的速度不仅比随机写盘的速度快，而且也比随机写内存的速度快，如图5-21[2]所示。

![image-20200112204418957](http://rocks526.top/lzx/image-20200112204418957.png)

Kafka 在设计时采用了文件追加的方式来写入消息，即只能在日志文件的尾部追加新的消息，并且也不允许修改已写入的消息，这种方式属于典型的顺序写盘的操作，所以就算 Kafka使用磁盘作为存储介质，它所能承载的吞吐量也不容小觑。但这并不是让Kafka在性能上具备足够竞争力的唯一因素，我们不妨继续分析。

### 页缓存

页缓存是操作系统实现的一种主要的磁盘缓存，以此用来减少对磁盘 I/O 的操作。具体来说，就是把磁盘中的数据缓存到内存中，把对磁盘的访问变为对内存的访问。为了弥补性能上的差异，现代操作系统越来越“激进地”将内存作为磁盘缓存，甚至会非常乐意将所有可用的内存用作磁盘缓存，这样当内存回收时也几乎没有性能损失，所有对于磁盘的读写也将经由统一的缓存。

当一个进程准备读取磁盘上的文件内容时，操作系统会先查看待读取的数据所在的页（page）是否在页缓存（pagecache）中，如果存在（命中）则直接返回数据，从而避免了对物理磁盘的 I/O 操作；如果没有命中，则操作系统会向磁盘发起读取请求并将读取的数据页存入页缓存，之后再将数据返回给进程。

对一个进程而言，它会在进程内部缓存处理所需的数据，然而这些数据有可能还缓存在操作系统的页缓存中，因此同一份数据有可能被缓存了两次。并且，除非使用Direct I/O的方式，否则页缓存很难被禁止。此外，用过Java的人一般都知道两点事实：对象的内存开销非常大，通常会是真实数据大小的几倍甚至更多，空间使用率低下；Java的垃圾回收会随着堆内数据的增多而变得越来越慢。基于这些因素，使用文件系统并依赖于页缓存的做法明显要优于维护一个进程内缓存或其他结构，至少我们可以省去了一份进程内部的缓存消耗，同时还可以通过结构紧凑的字节码来替代使用对象的方式以节省更多的空间。如此，我们可以在 32GB 的机器上使用28GB至30GB的内存而不用担心GC所带来的性能问题。此外，即使Kafka服务重启，页缓存还是会保持有效，然而进程内的缓存却需要重建。这样也极大地简化了代码逻辑，因为维护页缓存和文件之间的一致性交由操作系统来负责，这样会比进程内维护更加安全有效。

Kafka 中大量使用了页缓存，这是 Kafka 实现高吞吐的重要因素之一。虽然消息都是先被写入页缓存，然后由操作系统负责具体的刷盘任务的，但在Kafka中同样提供了同步刷盘及间断性强制刷盘（fsync）的功能，这些功能可以通过 log.flush.interval.messages、log.flush.interval.ms 等参数来控制。同步刷盘可以提高消息的可靠性，防止由于机器掉电等异常造成处于页缓存而没有及时写入磁盘的消息丢失。不过笔者并不建议这么做，刷盘任务就应交由操作系统去调配，消息的可靠性应该由多副本机制来保障，而不是由同步刷盘这种严重影响性能的行为来保障。

### 零拷贝

除了消息顺序追加、页缓存等技术，Kafka还使用零拷贝（Zero-Copy）技术来进一步提升性能。所谓的零拷贝是指将数据直接从磁盘文件复制到网卡设备中，而不需要经由应用程序之手。零拷贝大大提高了应用程序的性能，减少了内核和用户模式之间的上下文切换。对 Linux操作系统而言，零拷贝技术依赖于底层的 sendfile（）方法实现。对应于 Java 语言，FileChannal.transferTo（）方法的底层实现就是sendfile（）方法。

## 总结

### Kafka高性能原因？

- 顺序读写磁盘
- 批量写入
- 页缓存
- 零拷贝

# Kafka服务端设计














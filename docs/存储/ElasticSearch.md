- [ES入门](#rm)
  - [ES介绍](#js)
  - [ES安装](#az)
  - [ES相关概念](#gn)
  - [ES基本操作](#jbcz)
  
- [ES进阶](#jj)
  
  - [ES数据类型](#sjlx)
  
  - [ES检索](#js)
  - [ES聚合统计](#jh)
  - [ES数据建模](#jm)
  
- [ES集群](#jq)
  - [ES集群安全](#aq)
  - [ES集群架构](#jg)
  - [ES生产环境配置](#schj)
  - [ES索引生命周期管理](#sysmzq)
  
- [ES的Java客户端](#java)
  - [ES原生TCP客户端](#tcp)
  - [ES原生HTTP客户端](#http)
  - [RestTemplate](#restTemplate)
  - [ESTemplate](#esTemplate)
  - [ESRestTemplate](#EsrestTemplate)
  - [Spring Data ES](#dataes)
  
- [ES相关生态](#st)
  - [Logstash](#logstash)
  - [Beats](#beats)
  - [Kibana](#kibana)
  - [X-Pack](#x-pack)
  
- [ES源码](#ym)

- [ES配置文件解析](#pzwj)

# <a id="rm">ES入门</a>

### <a id="js">ES介绍</a>

- ES简介

Elaticsearch简称为es， es是一个开源的高扩展的分布式全文检索引擎，它可以近乎实时的存储、检索数据；本身扩展性很好，可以扩展到上百台服务器，处理PB级别的数据。

es使用Java开发并使用Lucene作为其核心来实现所有索引和搜索的功能，但是它的目的是通过简单的RESTful API来隐藏Lucene的复杂性，从而让全文搜索变得简单。

- ES特点
  - 数据存储
  - 数据检索
  - 数据统计分析
  - 近实时
  - Restful接口

- ES版本
  - 0.4：2020年第一次发布

  - 1.0：2014年1月

  - 2.0：2015年10月

  - 5.0：2016年10月

  > - 底层Lucene升级到6.x版本
  >
  > - 打分机制从TF-IDF改为BM25
  > - 支持Ingest节点，Painless脚本，Completion suggested，原生的Java Rest客户端
  > - Type标记为过时，支持Keyword类型
  > - 性能优化
  >   - 内部引擎移除了避免同一文档并发更新的竞争锁，带来15——20%的性能提升
  >   - instant aggregation，支持分片上聚合的缓存
  >   - 新增Profile API

  - 6.0：2017年10月

  > - 底层Lucene升级到7.x版本
  > - 新功能
  >   - 跨集群复制（CCR）
  >   - 索引生命周期管理
  >   - SQL支持
  > - 更友好的升级和数据迁移
  > - 性能优化
  >   - 有效存储稀疏索引的方案，降低存储成本
  >   - 在索引时进行排序，加快排序的查询性能

  - 7.0：2019年4月

  > - 底层Lucene升级到7.x版本
  > - 正式废除一个Index的多Type支持，默认type为_doc
  > - 7.1开始Security功能免费使用
  > - ECK —— ElasticSearch Operator on Kubernetes
  > - 新功能
  >   - 完成High Level REST Client
  >   - Script Score Query
  > - 性能优化
  >   - 默认分片数从5改为1，避免Over Sharding问题
  >   - 性能优化，更快的TOP K

- Elatic Stack生态圈

> Elatic Stack生态可以作为搜索、日志分析、指标分析、安全分析等问题的解决方案。包括以下组件：

数据抓取：Logstash，Beat

数据存储/计算：ElasticSearch

可视化：Kibana

X-Pack：提供安全、告警、监控、图查询、机器学习能力，部分开源

### <a id="az">ELK安装</a>

> ELK组件需要采用统一版本，ElasticSearch是使用java开发的，因此es需要的jdk环境，ES7.0之后内置了JDK

- ES安装

  - 官网下载压缩包：https://www.elastic.co/cn/downloads/elasticsearch
  - 修改config/elasticsearch.yml配置文件，加上以下内容，开启跨域，让head可以连接ES

  > http.cors.enabled: true 
  >
  > http.cors.allow‐origin: "*"

  - bin/elasticsearch启动es，启动成功后开放9300为TCP端口，9200为HTTP端口，访问localhost:9200可以查看ES信息
  - 查看安装的插件：elasticsearch-plugin list
  - 安装插件：elasticsearch-plugin install analysis-icu

- ES-head安装

  - ES-head官网：https://github.com/mobz/elasticsearch-head
  - 支持内置服务器、Docker、Chrome扩展多种方式部署，推荐Chrome扩展

- Kibana安装

  - 官网下载压缩包：https://www.elastic.co/cn/downloads/
  - 修改config/kibana.yml配置文件，设置ES的访问地址

  > elasticsearch.hosts: ["http://localhost:9200"]

  - /bin/kinaba，启动Kibana，默认端口5601，访问地址localhost:5601/

- Logstash安装

  - 官网下载压缩包：https://www.elastic.co/cn/downloads/
  - 修改config/logstash.yml配置文件
  - /bin/logstash，启动Logstash
  
- Docker安装ES

  - 拉取镜像：docker pull elasticsearch:7.9.0
  - 运行容器，设置端口映射：docker run -di -p 9200:9200 -p9300:9300 --name=es790 elasticsearch:7.9.0


### <a id="gn">ES相关概念</a>

Elasticsearch是面向文档(document oriented)的，这意味着它可以存储整个对象或文档(document)。然而它不仅 仅是存储，还会索引(index)每个文档的内容使之可以被搜索。在Elasticsearch中，你可以对文档（而非成行成列的 数据）进行索引、搜索、排序、过滤。Elasticsearch比传统关系型数据库如下：

| RelationalDB(关系型数据库) | Elasticsearch(搜索引擎) |
| -------------------------- | ----------------------- |
| Database                   | Index                   |
| Table                      | Type(已废弃)            |
| Row                        | Document                |
| Column                     | Field                   |
| Schema                     | mappings                |
| SQL                        | DSL                     |

- 节点(Node)

一个节点是集群中的一个服务器，作为集群的一部分，它存储数据，参与集群的索引和搜索功能。

一个节点可以通过配置集群名称的方式来加入一个指定的集群。默认情况下，每个节点都会被安排加入到一个叫做“elasticsearch”的集群中，这意味着，如果你在你的网络中启动了若干个节点，并假定它们能够相互发现彼此，它们将会自动地形成并加入到一个叫做“elasticsearch”的集群中。
在一个集群里，只要你想，可以拥有任意多个节点。而且，如果当前你的网络中没有运行任何Elasticsearch节点， 这时启动一个节点，会默认创建并加入一个叫做“elasticsearch”的集群。 

- 分片(Shards)

通过将一个索引进行分片，切分出多个分片，多个分片可以自动路由到多个节点上，利用多台机器的性能。分片数量在创建索引库时指定，一旦指定不可修改。当集群容量不足时，可以通过添加新的节点来扩容，多余的分片会自动转移到新的节点，即分片数应大于节点数。

- 副本(replicas)

副本是对分片的备份，通过对所有分片设置一个或多个副本达到容错高可用的目的，同时也能降低分片的压力，提升查询性能。

> ES通过分片和副本机制，实现水平扩展和高可用的特性，与Kakfa和Mongodb的分片副本机制类似。

- 倒排索引

倒排索引与正排列索引相对应，为了提高文档检索的效率，通过将文档分词，再以每个词为队列，将所有的文档连接起来，查询的时候直接根据词查文档。

- 分词

ES提供跟多分词器，包括英文分词，中文分词等。ES通过分词器将文档切分成一个个小的词项，再基于词项去构建索引。

### <a id="jbcz">ES基本操作</a>

- 索引操作

**添加无mappings的索引**

> `PUT` localhost:9200/rocks
>
> {}
>
> 添加一个名称为rocks的索引库，暂不设置mappings

**给未设置mappings的索引库添加mappings**

> `POST` localhost:9200/rocks/rocks_type/_mappings
>
> {
>
> ​	"rocks_type":{
>
> ​		"properties":{
>
> ​			"id":{
>
> ​				"type":"long",
>
> ​				"store":true,
>
> ​				"index":true,
>
> ​				"analyzer":"standard"
>
> ​			},
>
> ​			"name":{
>
> ​				"type":"text",
>
> ​				"index":true,
>
> ​				"analyzer":"smart_ik"
>
> ​			}
>
> ​		}
>
> ​	}
>
> }
>
> 给rocks索引库的名称为rocks_type的type添加mappings，包括id和name两个字段，id为long类型，需要存储/索引，采用标准分词器，name采用text类型，需要存储/索引，采用IK分词器

**添加索引库并设置mappings**

> `PUT` localhost:9200/rocks2
>
> {
>
>   "settings": {
>
> ​    "index": {
>
> ​      "number_of_replicas":0,
>
> ​      "number_of_shards":1
>
> ​    }
>
>   },
>
>   "mappings": {
>
> ​    "rocks_type": {
>
> ​      "properties": {
>
> ​        "id": {
>
> ​          "type": "long",
>
> ​          "index": "true"
>
> ​        }
>
> ​      }
>
> ​    }
>
>   }
>
> }
>
> 创建名称为rocks2的索引库，并设置mappings，与上面两个请求之和效果一致，同时设置分片和副本数量

**删除索引库**

> `DELETE` localhost:9200/rocks2
>
> {}
>
> 删除名称为rocks2的索引库

ES的每个分片都是一个Lucence，由于Lucence的不可变性，因此索引一旦创建，分片数和字段类型不可修改，除非重建索引，但索引副本数可以修改，可以新增新的字段类型。

**新增mappings字段**

> `POST` localhost:9200/rocks2/_mappings/rocks_type
>
> {
>
> ​    "rocks_type": {
>
> ​      "properties": {
>
> ​        "name": {
>
> ​          "type": "text",
>
> ​          "index": "true"
>
> ​        }
>
> ​      }
>
> ​    }
>
> }
>
> 给rocks2索引库的rocks_type类型新增name字段

**修改索引副本数**

> `PUT` localhost:9202/rocks2/_settings
>
> {
>
>   "number_of_replicas":1
>
> }
>
> 更新rocks2索引库的副本个数为1

- 文档操作







- Bucket批量操作









- 分词器相关API







- 文档简单查询









- 索引模板	







- ES监控相关API







# <a id="jj">ES进阶</a>

### <a id="sjlx">ES数据类型</a>



### <a id="js">ES检索</a>



### <a id="fbs">ES分布式特性</a>





### <a id="jh">ES聚合统计</a>



### <a id="jm">ES数据建模</a>









# <a id="jq">ES集群</a>

### <a id="aq">ES集群安全</a>



### <a id="jg">ES集群部署架构</a>



### <a id="schj">ES生产环境配置</a>



### <a id="sysmzq">ES索引生命周期</a>



# <a id="java">ES的Java客户端</a>

> ES的Java客户端非常多，主要分为TCP和HTTP两类:
>
> - TCP
>   - TransportClient：ES官方早期提供的基于TCP的客户端，7.0之后标记为过时，8.0之后移除此客户端
>   - ElasticSearchTemplate：Spring提供的ES客户端，基于TransportClient封装而成
> - HTTP
>   -  ElasticsearchRestClient：ES官方提供的基于HTTP的客户端，<推荐使用>
>   - ElasticSearchRestTemplate：Spring提供的ES客户端，基于ElasticsearchRestClient封装而成
>   - RestTemplate：Spring提供的HTTP工具包，可用于操作ES
>   - JestClient：第三方的ES客户端，基于HTTP协议
> - Spring Data ES
>   - Spring data系列 简化CRUD 复杂DSL使用Template

对于比较旧版本的ES，Spirng Data系列和ES的版本号对应有些不兼容，推荐采用ES原生的HTTP客户端，高版本推荐采用Spring Data系列。

- 版本对照

| Spring Data Release Train                                    | Spring Data Elasticsearch                                    | Elasticsearch | Spring Boot                                                  |
| ------------------------------------------------------------ | ------------------------------------------------------------ | ------------- | ------------------------------------------------------------ |
| 2020.0.0[[1](https://github.com/spring-projects/spring-data-elasticsearch/blob/master/src/main/asciidoc/preface.adoc#_footnotedef_1)] | 4.1.x[[1](https://github.com/spring-projects/spring-data-elasticsearch/blob/master/src/main/asciidoc/preface.adoc#_footnotedef_1)] | 7.9.0         | 2.3.x[[1](https://github.com/spring-projects/spring-data-elasticsearch/blob/master/src/main/asciidoc/preface.adoc#_footnotedef_1)] |
| Neumann                                                      | 4.0.x                                                        | 7.6.2         | 2.3.x                                                        |
| Moore                                                        | 3.2.x                                                        | 6.8.12        | 2.2.x                                                        |
| Lovelace                                                     | 3.1.x                                                        | 6.2.2         | 2.1.x                                                        |
| Kay[[2](https://github.com/spring-projects/spring-data-elasticsearch/blob/master/src/main/asciidoc/preface.adoc#_footnotedef_2)] | 3.0.x[[2](https://github.com/spring-projects/spring-data-elasticsearch/blob/master/src/main/asciidoc/preface.adoc#_footnotedef_2)] | 5.5.0         | 2.0.x[[2](https://github.com/spring-projects/spring-data-elasticsearch/blob/master/src/main/asciidoc/preface.adoc#_footnotedef_2)] |
| Ingalls[[2](https://github.com/spring-projects/spring-data-elasticsearch/blob/master/src/main/asciidoc/preface.adoc#_footnotedef_2)] | 2.1.x[[2](https://github.com/spring-projects/spring-data-elasticsearch/blob/master/src/main/asciidoc/preface.adoc#_footnotedef_2)] | 2.4.0         | 1.5.x[[2](https://github.com/spring-projects/spring-data-elasticsearch/blob/master/src/main/asciidoc/preface.adoc#_footnotedef_2)] |

Spring Data ES：https://github.com/spring-projects/spring-data-elasticsearch/blob/master/src/main/asciidoc/preface.adoc

Maven：https://mvnrepository.com/artifact/org.springframework.data/spring-data-elasticsearch

Spring官方文档：https://spring.io/projects/spring-data-elasticsearch#learn

### <a id="tcp">ES原生TCP客户端</a>

- 环境介绍
  - Jdk8
  - ES版本号5.1.1
  - ES客户端版本号5.6.8
  - SpringBoot版本2.3.0

- 引入依赖

```xml
    <properties>
        <java.version>1.8</java.version>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <project.reporting.outputEncoding>UTF-8</project.reporting.outputEncoding>
        <spring-boot.version>2.3.0.RELEASE</spring-boot.version>
    </properties> 
<dependency>
            <groupId>org.elasticsearch</groupId>
            <artifactId>elasticsearch</artifactId>
            <version>5.6.8</version>
        </dependency>
        <dependency>
            <groupId>org.elasticsearch.client</groupId>
            <artifactId>transport</artifactId>
            <version>5.6.8</version>
            <exclusions>
                <exclusion>
                    <artifactId>elasticsearch</artifactId>
                    <groupId>org.elasticsearch</groupId>
                </exclusion>
                <exclusion>
                    <artifactId>transport-netty4-client</artifactId>
                    <groupId>org.elasticsearch.plugin</groupId>
                </exclusion>
            </exclusions>
        </dependency>

        <dependency>
            <groupId>org.elasticsearch.plugin</groupId>
            <artifactId>transport-netty4-client</artifactId>
            <version>5.6.8</version>
            <exclusions>
                <exclusion>
                    <artifactId>elasticsearch</artifactId>
                    <groupId>org.elasticsearch</groupId>
                </exclusion>
            </exclusions>
        </dependency>

        <dependency>
            <groupId>com.google.code.gson</groupId>
            <artifactId>gson</artifactId>
        </dependency>
```

> SpringBoot2.3.0引入ES的5.6.8时，启动会抛出Netty异常，提示缺少netty中的某个类，原因是依赖冲突，netty5.6.8被高版本覆盖，单独加入netty5.6.8依赖之后ok
>
> <dependency>
>  <groupId>org.elasticsearch.plugin</groupId>
>  <artifactId>transport-netty4-client</artifactId>
>  <version>5.6.8</version>
> </dependency>

- 配置文件

```properties
spring.application.name=springboot-es-client

# 应用服务 WEB 访问端口
server.port=6780

# ES
es.cluster.name=es
es.cluster.nodes=127.0.0.1:9302
```

> ES的TCP默认端口9300，HTTP默认端口9200，由于Docker启动，9300映射到9302端口

- 构建ES客户端
  - 构建Settings配置信息类
  - 通过Settings构建TransportClient客户端
  - ES开启嗅探模式，可以自动发现集群其他节点，因此构建Settings时可以只配置一个节点，只要启动成功，之后会连接其他节点

```java
package com.rocks.springbootesclient.manager;

import lombok.extern.slf4j.Slf4j;
import org.elasticsearch.client.transport.TransportClient;
import org.elasticsearch.common.settings.Settings;
import org.elasticsearch.common.transport.InetSocketTransportAddress;
import org.elasticsearch.transport.client.PreBuiltTransportClient;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.stereotype.Component;

import javax.annotation.PostConstruct;
import java.net.InetAddress;
import java.net.UnknownHostException;

/**
 * ES工具类
 */
@Slf4j
@Component
public class ESManager {

    //参数配置
    private Settings settings;

    //es客户端
    private TransportClient client;

    //ES集群名字
    @Value("${es.cluster.name}")
    private String clusterName;

    //ES集群节点
    @Value("${es.cluster.nodes}")
    private String clusterNodes;

    @PostConstruct
    public void init(){
        try{
            checkProperties();
            settings = Settings.builder().put("cluster.name",clusterName).build();
            client = new PreBuiltTransportClient(settings);
            String[] nodes = clusterNodes.split(",");
            for (String node : nodes){
                String[] nodeInfo = node.split(":");
                //settings开启嗅探配置 可只添加一个节点 自动发现集群其他节点
                client.addTransportAddress(new InetSocketTransportAddress(InetAddress.getByName(nodeInfo[0]),Integer.valueOf(nodeInfo[1])));
            }
        } catch (UnknownHostException e) {
            log.error("elasticSearch init error,reason:{}",e);
            e.printStackTrace();
        }
    }

    private void checkProperties() {
        if (clusterName == null || clusterName.equals("")){
            throw new RuntimeException("ES集群名称不可为空...");
        }
        String[] nodes = clusterNodes.split(",");
        if (nodes.length == 0){
            throw new RuntimeException("ES集群至少需要一个节点...");
        }
        for (String node : nodes){
            String[] nodeInfo = node.split(":");
            if (nodeInfo.length != 2){
                throw new RuntimeException("ES节点信息配置错误...");
            }
        }
    }

    public TransportClient getInstance(){
        return client;
    }

    public void close(){
        client.close();
    }

}
```

- Index操作

```java
package com.rocks.springbootesclient.controller;

import com.rocks.springbootesclient.manager.ESManager;
import com.rocks.springbootesclient.util.ResponseUtils;
import com.rocks.springbootesclient.vo.DeleteIndexResVo;
import com.rocks.springbootesclient.vo.Response;
import lombok.extern.slf4j.Slf4j;
import org.elasticsearch.action.ActionFuture;
import org.elasticsearch.action.admin.indices.create.CreateIndexRequest;
import org.elasticsearch.action.admin.indices.create.CreateIndexResponse;
import org.elasticsearch.action.admin.indices.delete.DeleteIndexRequest;
import org.elasticsearch.action.admin.indices.delete.DeleteIndexResponse;
import org.elasticsearch.action.admin.indices.get.GetIndexRequest;
import org.elasticsearch.action.admin.indices.get.GetIndexRequestBuilder;
import org.elasticsearch.action.admin.indices.get.GetIndexResponse;
import org.elasticsearch.action.admin.indices.mapping.put.PutMappingRequest;
import org.elasticsearch.action.admin.indices.mapping.put.PutMappingResponse;
import org.elasticsearch.action.delete.DeleteRequest;
import org.elasticsearch.client.Requests;
import org.elasticsearch.client.transport.TransportClient;
import org.elasticsearch.common.settings.Settings;
import org.elasticsearch.common.transport.InetSocketTransportAddress;
import org.elasticsearch.common.xcontent.XContentBuilder;
import org.elasticsearch.common.xcontent.XContentFactory;
import org.elasticsearch.transport.client.PreBuiltTransportClient;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.*;

import java.io.IOException;
import java.net.InetAddress;
import java.net.UnknownHostException;
import java.util.ArrayList;
import java.util.HashMap;
import java.util.Set;

@RestController
@RequestMapping("/index")
@Slf4j
public class IndexController {

    @Autowired
    private ESManager esManager;

    /**
     * 获取索引
     * @return
     */
    @GetMapping("/get")
    public Response getIndexs(){
        try {
            TransportClient client = esManager.getInstance();
            GetIndexResponse getIndexResponse = client.admin().indices().getIndex(new GetIndexRequest().indices("demo_lzx")).get();
            String[] indices = getIndexResponse.indices();
            return ResponseUtils.success(indices);
        } catch (Exception e) {
            log.error("索引:{}获取失败，Reason:{}","demo_lzx",e);
            return ResponseUtils.fail();
        }
    }

    /**
     * 创建索引同时设置mappings
     * @return
     */
    @PostMapping("/create")
    public Response createIndex(@RequestParam(required = true)String indexName){
        //以User实体为mappings
        /**
         * mappings格式如下：
         *  "mappings": {
         *      "user": {               type名称
         *          "dynamic": "false", 索引严格程度 false true strict
         *          "properties": {     属性
         *              "id" :{
         *                  "type": "long",
         *                  "store": "true",
         *                  "analyzer": "smart",
         *                  "index": "true"
         *              },
         *              "name": {
         *                  "type": "text",
         *                  "store": "true",
         *                  "analyzer": "ik_smart",
         *                  "index": "false"
         *              }
         *          }
         *      }
         *  }
         *  方法1：根据实体生成JSON 但需要拼接上mappings/dynamic等属性
         *  方法2：ElasticSearchClient提供XContentFactory的JosnBuilder来拼接属性，之后自动转JSON，代码同样繁琐
         */
        try {
            TransportClient client = esManager.getInstance();
            XContentBuilder xContentBuilder = XContentFactory.jsonBuilder()
                    //startObject类似于{ }
                    .startObject()
                        .startObject("user")
                            .startObject("properties")
                                .startObject("id")
                                    .field("type","long").field("store","true")
                                .endObject()
                                .startObject("name")
                                    .field("type","text").field("store","true").field("index","true")
                                .endObject()
                            .endObject()
                        .endObject()
                    .endObject();
            HashMap<String, Object> settingParams = new HashMap<>();
            //设置分片和副本数
            settingParams.put("number_of_replicas",1);
            settingParams.put("number_of_shards",1);
            log.info("生成的mappings为:{}",xContentBuilder.string());
            CreateIndexResponse createIndexResponse = client.admin().indices().prepareCreate(indexName)
                                                            .addMapping("user", xContentBuilder.string())
                                                            .setSettings(settingParams)
                                                            .get();
            boolean acknowledged = createIndexResponse.isAcknowledged();
            return acknowledged ? ResponseUtils.success() : ResponseUtils.fail();
        } catch (IOException e) {
            log.error("索引:{}创建失败，Reason:{}",indexName,e);
            return ResponseUtils.fail();
        }
    }

    /**
     * 创建索引不设置mappings
     * @return
     */
    @PostMapping("/create/nomappings")
    public Response createIndexWithoutMappings(@RequestParam(required = true)String indexName){
        try {
            TransportClient client = esManager.getInstance();
            CreateIndexResponse createIndexResponse = client.admin().indices().prepareCreate(indexName).get();
            boolean acknowledged = createIndexResponse.isAcknowledged();
            return acknowledged ? ResponseUtils.success() : ResponseUtils.fail();
        } catch (Exception e) {
            log.error("索引:{}创建失败，Reason:{}",indexName,e);
            return ResponseUtils.fail();
        }
    }

    /**
     * 给索引添加Mappings
     * @return
     */
    @PutMapping("/create/mappings")
    public Response addMappingsByIndexName(@RequestParam(required = true)String indexName){
        try {
            TransportClient client = esManager.getInstance();
            XContentBuilder xContentBuilder = XContentFactory.jsonBuilder()
                    //startObject类似于{ }
                    .startObject()
                    .startObject("user")
                    .startObject("properties")
                    .startObject("id")
                    .field("type","long").field("store","true").field("analyzer","ik_smart")
                    .endObject()
                    .startObject("name")
                    .field("type","text").field("store","true").field("index","false")
                    .endObject()
                    .endObject()
                    .endObject()
                    .endObject();
            log.info("生成的mappings为:{}",xContentBuilder.string());
            PutMappingRequest mapping = Requests.putMappingRequest(indexName).type("user").source(xContentBuilder);
            PutMappingResponse putMappingResponse = client.admin().indices().putMapping(mapping).get();
            boolean acknowledged = putMappingResponse.isAcknowledged();
            return acknowledged ? ResponseUtils.success() : ResponseUtils.fail();
        } catch (Exception e) {
            log.error("给索引:{}添加Mappings失败，Reason:{}",indexName,e);
            return ResponseUtils.fail();
        }
    }

    /**
     * 删除索引
     * @return
     */
    @DeleteMapping("/delete")
    public Response deleteIndexs(@RequestParam(required = true) Set<String> indexNames){
        try {
            ArrayList<DeleteIndexResVo> deleteIndexResVos = new ArrayList<DeleteIndexResVo>();
            TransportClient client = esManager.getInstance();
            //client.admin().indices().prepareDelete(indexName)可以批量添加索引删除 但是Set没办法拿出所有索引名字赋值进去
            for (String indexName : indexNames){
                DeleteIndexResponse deleteIndexResponse = client.admin().indices().prepareDelete(indexName).get();
                boolean acknowledged = deleteIndexResponse.isAcknowledged();
                DeleteIndexResVo deleteIndexResVo = new DeleteIndexResVo();
                deleteIndexResVo.setIndexName(indexName);
                deleteIndexResVo.setIsSuccess(acknowledged);
                deleteIndexResVos.add(deleteIndexResVo);
                if (!acknowledged){
                    log.error("删除索引:{}失败，Reason:{}",indexName);
                }
            }
            return ResponseUtils.success(deleteIndexResVos);
        } catch (Exception e) {
            log.error("删除索引:{}失败，Reason:{}",indexNames,e);
            return ResponseUtils.fail();
        }
    }

}
```

- Document操作

```java
package com.rocks.springbootesclient.controller;

import com.google.gson.Gson;
import com.rocks.springbootesclient.entity.User;
import com.rocks.springbootesclient.manager.ESManager;
import com.rocks.springbootesclient.util.ResponseUtils;
import com.rocks.springbootesclient.vo.Response;
import lombok.extern.slf4j.Slf4j;
import org.elasticsearch.action.DocWriteResponse;
import org.elasticsearch.action.delete.DeleteResponse;
import org.elasticsearch.action.get.GetResponse;
import org.elasticsearch.action.index.IndexRequest;
import org.elasticsearch.action.index.IndexResponse;
import org.elasticsearch.action.update.UpdateRequest;
import org.elasticsearch.action.update.UpdateResponse;
import org.elasticsearch.client.transport.TransportClient;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.*;

@RestController
@RequestMapping("/document")
@Slf4j
public class DocumentController {

    @Autowired
    private ESManager esManager;

    @PostMapping("/create")
    public Response createDocument(@RequestBody User user,
                                   @RequestParam String indexName,
                                   @RequestParam String typeName) {
        try {
            if (user == null || user.getId().equals("") || user.getId() == null) {
                return ResponseUtils.fail("用户ID不可为NULL!");
            }
            TransportClient client = esManager.getInstance();
            /**
             * Document文档格式如下:
             * {
             *     "id" : 1L,
             *     "name": "Rocks"
             * }
             * 方法1：根据实体生成JSON
             * 方法2：ElasticSearchClient提供XContentFactory的JosnBuilder来拼接属性，之后自动转JSON
             */
            Gson gson = new Gson();
            String json = gson.toJson(user, User.class);
            //三个参数分别是索引名称，类型名称，文档ID 文档ID可不指定
            IndexResponse indexResponse = client.prepareIndex(indexName, typeName, user.getId() + "").setSource(json).get();
            return (indexResponse.status().getStatus() == 200 || indexResponse.status().getStatus() == 201) ? ResponseUtils.success() : ResponseUtils.fail();
        } catch (Exception e) {
            log.error("文档添加失败,Reason:{}", e);
            return ResponseUtils.fail();
        }
    }

    @PutMapping("/update")
    public Response updateDocument(@RequestBody User user,
                                   @RequestParam String indexName,
                                   @RequestParam String typeName) {
        try {
            if (user == null || user.getId().equals("") || user.getId() == null) {
                return ResponseUtils.fail("用户ID不可为NULL!");
            }
            TransportClient client = esManager.getInstance();
            /**
             * Document文档格式如下:
             * {
             *     "id" : 1L,
             *     "name": "Rocks"
             * }
             * 方法1：根据实体生成JSON
             * 方法2：ElasticSearchClient提供XContentFactory的JosnBuilder来拼接属性，之后自动转JSON
             */
            Gson gson = new Gson();
            String json = gson.toJson(user, User.class);
            UpdateRequest updateRequest = new UpdateRequest();
            updateRequest.index(indexName).type(typeName).id(user.getId() + "").doc(json);
            UpdateResponse updateResponse = client.update(updateRequest).get();
            return (updateResponse.status().getStatus() == 200 || updateResponse.status().getStatus() == 201) ? ResponseUtils.success() : ResponseUtils.fail();
        } catch (Exception e) {
            log.error("文档更新失败,Reason:{}", e);
            return ResponseUtils.fail();
        }
    }

    @PutMapping("/upsert")
    public Response upsertDocument(@RequestBody User user,
                                   @RequestParam String indexName,
                                   @RequestParam String typeName) {
        try {
            if (user == null || user.getId().equals("") || user.getId() == null) {
                return ResponseUtils.fail("用户ID不可为NULL!");
            }
            TransportClient client = esManager.getInstance();
            /**
             * Document文档格式如下:
             * {
             *     "id" : 1L,
             *     "name": "Rocks"
             * }
             * 方法1：根据实体生成JSON
             * 方法2：ElasticSearchClient提供XContentFactory的JosnBuilder来拼接属性，之后自动转JSON
             */
            Gson gson = new Gson();
            String json = gson.toJson(user, User.class);
            UpdateRequest updateRequest = new UpdateRequest();
            updateRequest.index(indexName).type(typeName).id(user.getId() + "").doc(json).upsert(json);
            UpdateResponse updateResponse = client.update(updateRequest).get();
            return (updateResponse.status().getStatus() == 200 || updateResponse.status().getStatus() == 201) ? ResponseUtils.success() : ResponseUtils.fail();
        } catch (Exception e) {
            log.error("文档更新失败,Reason:{}", e);
            return ResponseUtils.fail();
        }
    }

    @DeleteMapping("/delete")
    public Response deleteDocument(@RequestParam(required = false) Long id,
                                   @RequestParam String indexName,
                                   @RequestParam String typeName) {
        try {

            if (id == null || id.equals("")) {
                return ResponseUtils.fail("文档ID不可为NULL!");
            }
            TransportClient client = esManager.getInstance();
            DeleteResponse deleteResponse = client.prepareDelete(indexName, typeName, id + "").get();
            return deleteResponse.status().getStatus() == 200 ? ResponseUtils.success() : ResponseUtils.fail();
        } catch (Exception e) {
            log.error("文档删除失败,Reason:{}", e);
            return ResponseUtils.fail();
        }
    }


    @GetMapping("/get/id")
    public Response getDocumentById(@RequestParam(required = false) Long id,
                                    @RequestParam String indexName,
                                    @RequestParam String typeName) {
        try {

            if (id == null || id.equals("")) {
                return ResponseUtils.fail("文档ID不可为NULL!");
            }
            TransportClient client = esManager.getInstance();
            GetResponse getResponse = client.prepareGet(indexName, typeName, id + "").get();
            return ResponseUtils.success(getResponse.getSourceAsString());
        } catch (Exception e) {
            log.error("文档删除失败,Reason:{}", e);
            return ResponseUtils.fail();
        }
    }

}
```

- 高级操作

```java
package com.rocks.springbootesclient.controller;

import com.google.gson.Gson;
import com.rocks.springbootesclient.entity.User;
import com.rocks.springbootesclient.manager.ESManager;
import com.rocks.springbootesclient.util.ResponseUtils;
import com.rocks.springbootesclient.vo.Response;
import lombok.extern.slf4j.Slf4j;
import org.elasticsearch.action.DocWriteResponse;
import org.elasticsearch.action.ListenableActionFuture;
import org.elasticsearch.action.bulk.BulkItemRequest;
import org.elasticsearch.action.bulk.BulkItemResponse;
import org.elasticsearch.action.bulk.BulkRequestBuilder;
import org.elasticsearch.action.bulk.BulkResponse;
import org.elasticsearch.action.delete.DeleteRequest;
import org.elasticsearch.action.get.GetResponse;
import org.elasticsearch.action.get.MultiGetItemResponse;
import org.elasticsearch.action.get.MultiGetRequest;
import org.elasticsearch.action.get.MultiGetResponse;
import org.elasticsearch.action.index.IndexRequest;
import org.elasticsearch.action.search.SearchRequestBuilder;
import org.elasticsearch.action.search.SearchResponse;
import org.elasticsearch.action.update.UpdateRequest;
import org.elasticsearch.client.transport.TransportClient;
import org.elasticsearch.common.xcontent.XContentFactory;
import org.elasticsearch.index.query.*;
import org.elasticsearch.search.SearchHit;
import org.elasticsearch.search.SearchHits;
import org.elasticsearch.search.aggregations.Aggregation;
import org.elasticsearch.search.aggregations.AggregationBuilders;
import org.elasticsearch.search.aggregations.Aggregations;
import org.elasticsearch.search.aggregations.bucket.terms.StringTerms;
import org.elasticsearch.search.aggregations.metrics.max.InternalMax;
import org.elasticsearch.search.aggregations.metrics.max.MaxAggregationBuilder;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.*;

import java.util.ArrayList;
import java.util.Set;

@RestController
@RequestMapping("/search")
@Slf4j
public class SearchController {

    @Autowired
    private ESManager esManager;

    //批量查询  multiGet
    @GetMapping("/multiGet")
    public Response multiGet(@RequestParam(required = false) Set<Long> ids,
                                      @RequestParam String indexName,
                                      @RequestParam String typeName) {
        try {
            if (ids == null || ids.size() == 0) {
                return ResponseUtils.fail("ID不可为NULL!");
            }
            TransportClient client = esManager.getInstance();
            MultiGetRequest multiGetRequest = new MultiGetRequest();
            for (Long id : ids){
                multiGetRequest.add(indexName,typeName,String.valueOf(id));
            }
            MultiGetResponse multiGetItemResponses = client.multiGet(multiGetRequest).get();
            ArrayList<User> users = new ArrayList<>();
            Gson gson = new Gson();
            for (MultiGetItemResponse response : multiGetItemResponses){
                if (!response.isFailed()){
                    if (response.getResponse().isExists()){
                        User user = gson.fromJson(response.getResponse().getSourceAsString(), User.class);
                        users.add(user);
                    }else {
                        log.error("ID为[{}]的用户查询失败,用户为Null...",response.getId());
                    }
                }else {
                    log.error("ID为[{}]的用户查询失败...",response.getId());
                }
            }
            return ResponseUtils.success(users);
        } catch (Exception e) {
            log.error("文档批量查询失败,Reason:{}", e.toString());
            return ResponseUtils.fail();
        }
    }

    //bulk实现批量增删改操作
    @PutMapping("/bulk")
    public Response bulkOperator() {
        try{
            TransportClient client = esManager.getInstance();
            BulkRequestBuilder bulkRequestBuilder = client.prepareBulk();
            IndexRequest addRequest = new IndexRequest();
            addRequest.index("rocks-demo1")
                    .type("user")
                    .id("3")
                    .source(XContentFactory.jsonBuilder()
                            .startObject()
                            .field("id","3")
                            .field("name","Rocks3")
                            .field("desc","This is three User...")
                            .endObject());
            IndexRequest addRequest2 = new IndexRequest();
            addRequest2.index("rocks-demo1")
                    .type("user")
                    .id("4")
                    .source(XContentFactory.jsonBuilder()
                            .startObject()
                            .field("id","4")
                            .field("name","Rocks4")
                            .field("desc","This is fifth User...")
                            .endObject());
            UpdateRequest updateRequest = new UpdateRequest();
            updateRequest.index("rocks-demo1")
                    .type("user")
                    .id("4")
                    .doc(XContentFactory.jsonBuilder()
                            .startObject()
                            .field("id","4")
                            .field("name","Rocks4")
                            .field("desc","This is fourth User...")
                            .endObject());
            DeleteRequest deleteRequest = new DeleteRequest();
            deleteRequest.index("rocks-demo1")
                    .type("user")
                    .id("3");
            bulkRequestBuilder.add(addRequest);
            bulkRequestBuilder.add(addRequest2);
            bulkRequestBuilder.add(updateRequest);
            bulkRequestBuilder.add(deleteRequest);
            //异步执行
//            ListenableActionFuture<BulkResponse> execute =
//                    bulkRequestBuilder.execute();
            BulkResponse bulkItemResponses = bulkRequestBuilder.get();
            for (BulkItemResponse bulkItemResponse : bulkItemResponses){
                if (!bulkItemResponse.isFailed()){
                    DocWriteResponse.Result result = bulkItemResponse.getResponse().getResult();
                    log.info("ID为[{}]的操作执行结果为:[{}]",bulkItemResponse.getId(),result.toString());
                }else {
                    log.error("ID为[{}]的操作执行失败...Reason:{}",bulkItemResponse.getId(),bulkItemResponse.getFailureMessage());
                }
            }
            return ResponseUtils.success();
        }catch (Exception e){
            log.error("批量操作失败,Reason:{}", e.toString());
            return ResponseUtils.fail();
        }
    }

    //使用query查询  match_all 查询所有字段
    @GetMapping("/match_all")
    public Response matchAllOperator() {
        try{
            TransportClient client = esManager.getInstance();
            MatchAllQueryBuilder matchAllQueryBuilder = QueryBuilders.matchAllQuery();
            //查询Rocks 会分词
            matchAllQueryBuilder.queryName("Rocks");
            String[] includes = new String[]{"id", "name"};
            String[] excludes = new String[]{"desc"};
            //可以查询多个索引库 如果索引库不存在会抛出异常 IndexNotFoundException[no such index]
            SearchRequestBuilder searchRequestBuilder = client.prepareSearch("rocks-demo1", "rocks-demo2")
                    .setQuery(matchAllQueryBuilder)
                    //返回id,name字段 不返回desc
                    .setFetchSource(includes, excludes)
                    //返回5条
                    .setSize(5);
            SearchResponse searchResponse = searchRequestBuilder.get();
            System.out.println(searchResponse.toString());
            SearchHits hits = searchResponse.getHits();
            for (SearchHit searchHit : hits){
                System.out.println(searchHit.getSourceAsString());
            }
            return ResponseUtils.success();
        }catch (Exception e){
            log.error("match_all查询失败,Reason:{}", e.toString());
            return ResponseUtils.fail();
        }
    }

    //使用query查询  match 查询某个字段 精确匹配 Rocks不会匹配到Rocks1 Rocks2等
    @GetMapping("/match")
    public Response matchOperator() {
        try{
            TransportClient client = esManager.getInstance();
            MatchQueryBuilder matchQueryBuilder = QueryBuilders.matchQuery("name", "Rocks");
            //查询Rocks
            String[] includes = new String[]{"id", "name", "desc"};
            //可以查询多个索引库 如果索引库不存在会抛出异常 IndexNotFoundException[no such index]
            SearchRequestBuilder searchRequestBuilder = client.prepareSearch("rocks-demo1", "rocks-demo2")
                    .setQuery(matchQueryBuilder)
                    //返回id,name字段 不返回desc
                    .setFetchSource(includes, null)
                    //返回5条
                    .setSize(5);
            SearchResponse searchResponse = searchRequestBuilder.get();
            System.out.println(searchResponse.toString());
            SearchHits hits = searchResponse.getHits();
            for (SearchHit searchHit : hits){
                System.out.println(searchHit.getSourceAsString());
            }
            return ResponseUtils.success();
        }catch (Exception e){
            log.error("match查询失败,Reason:{}", e.toString());
            return ResponseUtils.fail();
        }
    }

    //使用query查询  multiMatch 多字段查询
    @GetMapping("/multiMatch")
    public Response multiMatchOperator() {
        try{
            TransportClient client = esManager.getInstance();
            //在name和desc字段上查询Rocks
            MultiMatchQueryBuilder multiMatchQueryBuilder = QueryBuilders.multiMatchQuery("Rocks", "name","desc");
            SearchRequestBuilder searchRequestBuilder = client.prepareSearch("rocks-demo1", "rocks-demo2")
                    .setQuery(multiMatchQueryBuilder)
                    //返回5条
                    .setSize(5);
            SearchResponse searchResponse = searchRequestBuilder.get();
            System.out.println(searchResponse.toString());
            SearchHits hits = searchResponse.getHits();
            for (SearchHit searchHit : hits){
                System.out.println(searchHit.getSourceAsString());
            }
            return ResponseUtils.success();
        }catch (Exception e){
            log.error("mutilMatch查询失败,Reason:{}", e.toString());
            return ResponseUtils.fail();
        }
    }

    //使用query查询 term查询
    @GetMapping("/term")
    public Response termOperator() {
        try{
            TransportClient client = esManager.getInstance();
            //注意rocks是小写的r  term查询的value不会进行分词 因此Rocks不会被匹配到
            TermQueryBuilder termQueryBuilder = QueryBuilders.termQuery("name", "rocks");
            SearchRequestBuilder searchRequestBuilder = client.prepareSearch("rocks-demo1", "rocks-demo2")
                    .setQuery(termQueryBuilder)
                    .setSize(3);
            SearchResponse searchResponse = searchRequestBuilder.get();
            System.out.println(searchResponse.toString());
            SearchHits hits = searchResponse.getHits();
            for (SearchHit searchHit : hits){
                System.out.println(searchHit.getSourceAsString());
            }
            return ResponseUtils.success();
        }catch (Exception e){
            log.error("term查询失败,Reason:{}", e.toString());
            return ResponseUtils.fail();
        }
    }

    //使用query查询 terms查询
    @GetMapping("/terms")
    public Response termsSearch() {
        try{
            TransportClient client = esManager.getInstance();
            //查询desc字段里包含first || second || fifth单词的文档
            TermsQueryBuilder termsQueryBuilder = QueryBuilders.termsQuery("desc", "first", "second", "fifth");
            SearchRequestBuilder searchRequestBuilder = client.prepareSearch("rocks-demo1", "rocks-demo2")
                    .setQuery(termsQueryBuilder)
                    .setSize(10);
            SearchResponse searchResponse = searchRequestBuilder.get();
            System.out.println(searchResponse.toString());
            SearchHits hits = searchResponse.getHits();
            for (SearchHit searchHit : hits){
                System.out.println(searchHit.getSourceAsString());
            }
            return ResponseUtils.success();
        }catch (Exception e){
            log.error("terms查询失败,Reason:{}", e.toString());
            return ResponseUtils.fail();
        }
    }

    //使用query查询 range范围查询
    @GetMapping("/range")
    public Response rangeSearch() {
        try{
            TransportClient client = esManager.getInstance();
            //查询ID为1-10的文档
            RangeQueryBuilder rangeQueryBuilder = QueryBuilders.rangeQuery("id").from(1).to(10);
            SearchRequestBuilder searchRequestBuilder = client.prepareSearch("rocks-demo1", "rocks-demo2")
                    .setQuery(rangeQueryBuilder)
                    .setSize(15);
            SearchResponse searchResponse = searchRequestBuilder.get();
            System.out.println(searchResponse.toString());
            SearchHits hits = searchResponse.getHits();
            for (SearchHit searchHit : hits){
                System.out.println(searchHit.getSourceAsString());
            }
            return ResponseUtils.success();
        }catch (Exception e){
            log.error("range查询失败,Reason:{}", e.toString());
            return ResponseUtils.fail();
        }
    }

    //使用query查询 wildcard通配符查询
    @GetMapping("/wildcard")
    public Response wildcardSearch() {
        try{
            TransportClient client = esManager.getInstance();
            //查询name为rocks开头的文档
            WildcardQueryBuilder wildcardQueryBuilder = QueryBuilders.wildcardQuery("name","rocks*");
            SearchRequestBuilder searchRequestBuilder = client.prepareSearch("rocks-demo1", "rocks-demo2")
                    .setQuery(wildcardQueryBuilder);
            SearchResponse searchResponse = searchRequestBuilder.get();
            System.out.println(searchResponse.toString());
            SearchHits hits = searchResponse.getHits();
            for (SearchHit searchHit : hits){
                System.out.println(searchHit.getSourceAsString());
            }
            return ResponseUtils.success();
        }catch (Exception e){
            log.error("wildcard查询失败,Reason:{}", e.toString());
            return ResponseUtils.fail();
        }
    }

    //使用query查询 prefix前缀查询
    @GetMapping("/prefix")
    public Response prefixSearch() {
        try{
            TransportClient client = esManager.getInstance();
            //查询name为roc开头的文档
            PrefixQueryBuilder prefixQueryBuilder = QueryBuilders.prefixQuery("name", "roc");
            SearchRequestBuilder searchRequestBuilder = client.prepareSearch("rocks-demo1", "rocks-demo2")
                    .setQuery(prefixQueryBuilder)
                    .setSize(8);
            SearchResponse searchResponse = searchRequestBuilder.get();
            System.out.println(searchResponse.toString());
            SearchHits hits = searchResponse.getHits();
            for (SearchHit searchHit : hits){
                System.out.println(searchHit.getSourceAsString());
            }
            return ResponseUtils.success();
        }catch (Exception e){
            log.error("prefix查询失败,Reason:{}", e.toString());
            return ResponseUtils.fail();
        }
    }

    //使用query查询 fuzzy模糊匹配查询 => 只有后模糊 => rocks%
    @GetMapping("/fuzzy")
    public Response fuzzySearch() {
        try{
            TransportClient client = esManager.getInstance();
            //查询name为包含ock的文档
            FuzzyQueryBuilder fuzzyQueryBuilder = QueryBuilders.fuzzyQuery("name", "rock");
            SearchRequestBuilder searchRequestBuilder = client.prepareSearch("rocks-demo1", "rocks-demo2")
                    .setQuery(fuzzyQueryBuilder)
                    .setSize(6);
            SearchResponse searchResponse = searchRequestBuilder.get();
            System.out.println(searchResponse.toString());
            SearchHits hits = searchResponse.getHits();
            for (SearchHit searchHit : hits){
                System.out.println(searchHit.getSourceAsString());
            }
            return ResponseUtils.success();
        }catch (Exception e){
            log.error("fuzzy查询失败,Reason:{}", e.toString());
            return ResponseUtils.fail();
        }
    }

    //使用query查询 ids批量查询
    @GetMapping("/ids")
    public Response idsSearch() {
        try{
            TransportClient client = esManager.getInstance();
            //查询id为1-5的文档
            IdsQueryBuilder idsQueryBuilder = QueryBuilders.idsQuery().addIds("1", "2", "3", "4", "5");
            SearchRequestBuilder searchRequestBuilder = client.prepareSearch("rocks-demo1", "rocks-demo2")
                    .setQuery(idsQueryBuilder)
                    .setSize(20);
            SearchResponse searchResponse = searchRequestBuilder.get();
            System.out.println(searchResponse.toString());
            SearchHits hits = searchResponse.getHits();
            for (SearchHit searchHit : hits){
                System.out.println(searchHit.getSourceAsString());
            }
            return ResponseUtils.success();
        }catch (Exception e){
            log.error("ids查询失败,Reason:{}", e.toString());
            return ResponseUtils.fail();
        }
    }

    //使用query查询 queryString查询
    @GetMapping("/queryString")
    public Response queryStringSearch() {
        try{
            TransportClient client = esManager.getInstance();
            //查询name包含user 不包含5和3的文档 (不会分词)
            QueryStringQueryBuilder queryStringQueryBuilder = QueryBuilders.queryStringQuery("+user -3 -5");
            SearchRequestBuilder searchRequestBuilder = client.prepareSearch("rocks-demo1", "rocks-demo2")
                    .setQuery(queryStringQueryBuilder)
                    .setSize(13);
            SearchResponse searchResponse = searchRequestBuilder.get();
            System.out.println(searchResponse.toString());
            SearchHits hits = searchResponse.getHits();
            for (SearchHit searchHit : hits){
                System.out.println(searchHit.getSourceAsString());
            }
            return ResponseUtils.success();
        }catch (Exception e){
            log.error("queryString查询失败,Reason:{}", e.toString());
            return ResponseUtils.fail();
        }
    }

    //使用query查询 aggregation聚合查询
    @GetMapping("/aggregation")
    public Response aggregationSearch() {
        try{
            TransportClient client = esManager.getInstance();
            //获取最大ID数 聚合结果起名max_id
            MaxAggregationBuilder maxAggregationBuilder = AggregationBuilders.max("max_id").field("id");
            SearchRequestBuilder searchRequestBuilder = client.prepareSearch("rocks-demo1", "rocks-demo2")
                    .addAggregation(maxAggregationBuilder)
                    .setSize(3);
            SearchResponse searchResponse = searchRequestBuilder.get();
            System.out.println(searchResponse.toString());
//            SearchHits hits = searchResponse.getHits();
//            for (SearchHit searchHit : hits){
//                System.out.println(searchHit.getSourceAsString());
//            }
            //根据不同的聚合结果 需要找到对应的实现类来接收 Ctrl Alt B 查看接口实现类
            Aggregations aggregations = searchResponse.getAggregations();
            InternalMax max_id = (InternalMax)aggregations.get("max_id");
            System.out.println(max_id.getValue());
            return ResponseUtils.success();
        }catch (Exception e){
            log.error("aggregation查询失败,Reason:{}", e.toString());
            return ResponseUtils.fail();
        }
    }

    //使用query查询 组合查询 -> 包含多种 演示bool类型
    @GetMapping("/bool")
    public Response boolSearch() {
        try{
            TransportClient client = esManager.getInstance();
            //多条件组合查询
            BoolQueryBuilder boolQueryBuilder = QueryBuilders.boolQuery()
                    //name必须匹配Rocks
                    .must(QueryBuilders.matchAllQuery().queryName("Rocks"))
                    //desc不能包含fourth
                    .mustNot(QueryBuilders.matchQuery("desc","fourth"))
                    //desc应该包含user 非必须满足
                    .should(QueryBuilders.matchQuery("desc","user"))
                    //desc应该包含this 非必须满足
                    .should(QueryBuilders.matchQuery("desc","this"))
                    //查询至少需要满足几个should 1 => 上面两个should至少要满足一个
                    .minimumShouldMatch(1)
                    //过滤 返回ID大于1的文档
                    .filter(QueryBuilders.rangeQuery("id").gt(1));
            SearchRequestBuilder searchRequestBuilder = client.prepareSearch("rocks-demo1", "rocks-demo2")
                    .setQuery(boolQueryBuilder)
                    .setSize(16);
            SearchResponse searchResponse = searchRequestBuilder.get();
            System.out.println(searchResponse.toString());
            SearchHits hits = searchResponse.getHits();
            for (SearchHit searchHit : hits){
                System.out.println(searchHit.getSourceAsString());
            }
            return ResponseUtils.success();
        }catch (Exception e){
            log.error("bool查询失败,Reason:{}", e.toString());
            return ResponseUtils.fail();
        }
    }

}
```

> ES原生客户端总结：
>
> - 首先需要通过settings对象配置es的整体信息
> - 通过settings对象生成TransportClient客户端连接对象
> - 通过TransportClient操作index和文档
> - index操作在client.admin().indices()方法下
> - document操作在client.xxx方法下
> - 索引和文档的操作都有两种方式
>   - 第一种是prepareXXX：此方式入参是索引名，类型名，ID等
>   - 第二种是XXX方式：此方式入参是XXXRequest对象，将索引名，类型名，ID等封装在其中，该对象通过Requests生成
> - 索引和文档的操作存在同步和异步两种方式
>   - get同步
>   - excute异步，可以设置监听器或者同步阻塞等待
> - 除了基本的索引和文档的CRUD之外，还提供了复杂DSL的API
>   - 通过QueryBuilders生成对应的查询请求条件，利用client发送请求，获取响应
> - 聚合查询通过AggregationBuilders构造查询条件
>   - 需要注意返回结果，为了保证接口兼容，返回结果是个接口Aggregation，为了拿到返回结果，需要根据不同的聚合条件利用不同的实现类来接收
>   - Ctrl + Alt + B查看Aggregation的实现类

### <a id="esTemplate">ESTemplate</a>

> ES的原生客户端在添加和更新文档时，无论是通过ElasticSearchClient提供XContentFactory的JosnBuilder来拼接属性还是通过Json工具包反序列化实体，整体代码都有些繁琐，因此Spring基于原生的客户端封装形成了ElasticSearchTemplate模板

- 环境介绍 : 整体环境与上例的ES原生客户端保持一致
  - Jdk8
  - ES版本号5.1.1
  - ES客户端版本号5.6.8
  - SpringBoot版本2.3.0
- 引入依赖

```xml
        <!-- https://mvnrepository.com/artifact/org.springframework.data/spring-data-elasticsearch -->
        <dependency>
            <groupId>org.springframework.data</groupId>
            <artifactId>spring-data-elasticsearch</artifactId>
            <version>3.1.8.RELEASE</version>
        </dependency>
```

> 删除之前ES官方的TCP依赖，引入spring-data-es依赖，由于Springboot版本号为2.3.0，导致SpringDataES跟着SpringBoot版本号走，引入了es7.6.2的依赖，将SpringBoot版本降到2.0.0之后，引入es5.6.8依赖
>
> ES的部分版本不向前兼容，es7.6.2的TCP客户端删除了部分es5.6.8客户端的类

![image-20200914165901072](http://rocks526.top/lzx/image-20200914165901072.png)

![image-20200914165948003](http://rocks526.top/lzx/image-20200914165948003.png)

> 由于之前测试版本兼容性，spring-data-es采用了3.1.8，没有采用与springboot2.0.0匹配的spring-data-es3.0.0，出现实体继承Repository接口后，启动项目时，由于要创建索引库，抛出异常，原因是未找到创建索引库的方法，猜测是版本不一致导致的，修改版本为spring-data-es3.0.0后项目顺利启动

![image-20200914172532162](http://rocks526.top/lzx/image-20200914172532162.png)

> 目前存在问题：
>
> - 实体主键类型映射异常，部分参数无法解析
>
> 实体的主键ID设置@Field(type = FieldType.Long,fielddata = true)，但es最终生成类型为text，且其他字段生成类型也有异常，fielddata参数提示无法将其解析，是无效参数
>
> 猜测是版本兼容问题，springboot2.0.0和spring-data-es3.0.0引入的es依赖是5.6.8版本，而目前使用的es是5.1.1版本，通过docker拉取es5.6.8的镜像，启动一个5.6.8的容器，结果9200端口可访问，通过tcp客户端连接9300端口失败，网上解释原因是版本不兼容导致的。

- 实体

```java
package com.rocks.springbootesclient.entity;

import lombok.Data;
import org.springframework.data.annotation.Id;
import org.springframework.data.elasticsearch.annotations.Document;
import org.springframework.data.elasticsearch.annotations.Field;
import org.springframework.data.elasticsearch.annotations.FieldType;

import java.io.Serializable;

/**
 * 实体User
 */
@Data
@Document(indexName = "rocks-demo3",type = "user",shards = 1,replicas = 1)
public class User implements Serializable {

    private static final long serialVersionUID = 1809041336715990704L;

    @Id
    @Field(type = FieldType.Long)
    private Long id;

    //keyword类型不分词
    @Field(type = FieldType.keyword)
    private String name;

    @Field(type = FieldType.text)
    private String desc;

}
```

- Repository

```java
package com.rocks.springbootesclient.dao;

import com.rocks.springbootesclient.entity.User;
import org.springframework.data.elasticsearch.repository.ElasticsearchRepository;
import org.springframework.stereotype.Repository;

@Repository
public interface UserRepository extends ElasticsearchRepository<User,Long> {
}
```

- 配置ElasticSearchTemplate
  - Repository可以完成基本的CRUD和分页排序等
  - 复杂的DSL需要ElasticSearchTemplate辅助完成
  - ElasticSearchTemplate需要注入es原生客户端的连接对象的Bean

```java
package com.rocks.springbootesclient.config;

import com.rocks.springbootesclient.manager.ESManager;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.data.elasticsearch.core.ElasticsearchTemplate;

@Configuration
public class ESTemplateConfig {

    @Autowired
    private ESManager esManager;

    @Bean("elasticsearchTemplate")
    public ElasticsearchTemplate elasticsearchTemplate(){
        return new ElasticsearchTemplate(esManager.getInstance());
    }

}
```

- 文档CRUD & 搜索

```java
package com.rocks.springbootesclient.controller;

import com.google.gson.Gson;
import com.rocks.springbootesclient.dao.UserRepository;
import com.rocks.springbootesclient.entity.User;
import com.rocks.springbootesclient.util.ResponseUtils;
import com.rocks.springbootesclient.vo.Response;
import lombok.extern.slf4j.Slf4j;
import org.elasticsearch.action.search.SearchResponse;
import org.elasticsearch.index.query.MatchAllQueryBuilder;
import org.elasticsearch.index.query.QueryBuilders;
import org.elasticsearch.index.query.RangeQueryBuilder;
import org.elasticsearch.search.sort.FieldSortBuilder;
import org.elasticsearch.search.sort.SortBuilders;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.data.domain.Page;
import org.springframework.data.domain.PageRequest;
import org.springframework.data.domain.Sort;
import org.springframework.data.elasticsearch.core.ElasticsearchTemplate;
import org.springframework.data.elasticsearch.core.ResultsExtractor;
import org.springframework.data.elasticsearch.core.aggregation.AggregatedPage;
import org.springframework.data.elasticsearch.core.query.GetQuery;
import org.springframework.data.elasticsearch.core.query.NativeSearchQuery;
import org.springframework.web.bind.annotation.*;

import java.util.ArrayList;
import java.util.Arrays;
import java.util.Set;

@RestController
@RequestMapping("/spring")
@Slf4j
public class ElasticSearchTemplateController {


    @Autowired
    private ElasticsearchTemplate elasticsearchTemplate;

    @Autowired
    private UserRepository userRepository;


    @PostMapping("/create")
    public Response createDocument(@RequestBody User user) {
        try {
            User save = userRepository.save(user);
            return ResponseUtils.success(save);
        } catch (Exception e) {
            log.error("文档添加失败,Reason:{}", e);
            return ResponseUtils.fail();
        }
    }

    @DeleteMapping("/delete")
    public Response deleteDocumentById(@RequestParam(required = false) Set<Long> ids) {
        try {
            if (ids == null || ids.size() == 0) {
                return ResponseUtils.fail("文档ID不可为NULL!");
            }
            //全部删除
//            userRepository.deleteAll();
            //根据用户ID指定删除
//            userRepository.delete(user);
            //根据用户ids批量删除
            ArrayList<User> users = new ArrayList<>();
            for (Long id : ids){
                User user = new User();
                user.setId(id);
                users.add(user);
            }
            userRepository.deleteAll(users);
            return ResponseUtils.success(ids);
        } catch (Exception e) {
            log.error("文档删除失败,Reason:{}", e.toString());
            return ResponseUtils.fail();
        }
    }

    @GetMapping("/get")
    public Response getDocumentById(@RequestParam(required = false) Set<Long> ids) {
        try {
            if (ids == null || ids.size() == 0) {
                return ResponseUtils.fail("文档ID不可为NULL!");
            }
            Iterable<User> allById = userRepository.findAllById(ids);
            for(User user : allById){
                System.out.println(user);
            }
            return ResponseUtils.success(allById);
        } catch (Exception e) {
            log.error("文档获取失败,Reason:{}", e.toString());
            return ResponseUtils.fail();
        }
    }

    @PutMapping("/update")
    public Response updateDocumentById(@RequestBody User user) {
        try {
            if (user == null || user.getId() == null) {
                return ResponseUtils.fail("文档ID不可为NULL!");
            }
            User save = userRepository.save(user);
            return ResponseUtils.success(save);
        } catch (Exception e) {
            log.error("文档更新失败,Reason:{}", e.toString());
            return ResponseUtils.fail();
        }
    }

    @GetMapping("/get2")
    public Response getDocument(@RequestParam(required = false,defaultValue = "1") Integer page,
                                @RequestParam(required = false,defaultValue = "0") Integer size) {
        try {
            //根据ID升序
            Sort sort = new Sort(Sort.Direction.DESC,"id");
            //分页 页数从0开始
            PageRequest pageRequest = new PageRequest(page,size,sort);
            Page<User> all = userRepository.findAll(pageRequest);
            for(User user : all){
                System.out.println(user);
            }
            return ResponseUtils.success(all);
        } catch (Exception e) {
            log.error("文档获取失败,Reason:{}", e.toString());
            return ResponseUtils.fail();
        }
    }

    @GetMapping("/search")
    public Response searchDocument(@RequestParam Integer page,
                                @RequestParam Integer size,
                                @RequestParam String keyword) {
        try {

            /**
             *  查询需要NativeSearchQuery查询对象 主要包含过滤器QueryBuilder对象 排序SortBuilder对象 分页Pageable对象
             *  QueryBuilder包括BoolQueryBuilder/GeoDistanceQueryBuilder/QeuryStringQueryBuilder等多种
             *  SortBuilder包括FieldSortBuilder/GeoDistanceSortBuilder/ScoreSortBuilder
             *  通过QueryBuilders和SortBuilders构造需要的过滤对象和排序对象
             */
            MatchAllQueryBuilder matchAllQueryBuilder = QueryBuilders.matchAllQuery();
            matchAllQueryBuilder.queryName(keyword);
            RangeQueryBuilder rangeQueryBuilder = QueryBuilders.rangeQuery("id").from(2).to(5);
            FieldSortBuilder fieldSortBuilder = SortBuilders.fieldSort("id");
            //1.通过构建NativeSearchQuery对象进行查询 可实现查询 过滤 排序 但不可实现分页
//            //三个参数分别是查询 过滤 排序
//            NativeSearchQuery nativeSearchQuery = new NativeSearchQuery(matchAllQueryBuilder, rangeQueryBuilder, Arrays.asList(fieldSortBuilder));
//            Page<User> searchResponse = userRepository.search(nativeSearchQuery);
//            searchResponse.stream().forEach(u -> System.out.println(u));
            //2.通过构建QueryBuilder和Pageable对象进行查询 可实现查询 排序 分页
            Sort sort = new Sort(Sort.Direction.DESC,"id");
            PageRequest pageRequest = new PageRequest(page,size,sort);
            Page<User> searchResponse = userRepository.search(matchAllQueryBuilder, pageRequest);
            searchResponse.stream().forEach(u -> System.out.println(u));
            return ResponseUtils.success();
        } catch (Exception e) {
            log.error("文档获取失败,Reason:{}", e.toString());
            return ResponseUtils.fail();
        }
    }

    @GetMapping("/search2")
    public Response search2Document(@RequestParam Integer page,
                                   @RequestParam Integer size,
                                   @RequestParam String keyword) {
        try {
            //通过ElasticSearchTemplate实现搜索
            MatchAllQueryBuilder matchAllQueryBuilder = QueryBuilders.matchAllQuery();
            matchAllQueryBuilder.queryName(keyword);
            RangeQueryBuilder rangeQueryBuilder = QueryBuilders.rangeQuery("id").from(2).to(5);
            FieldSortBuilder fieldSortBuilder = SortBuilders.fieldSort("id");
            NativeSearchQuery nativeSearchQuery = new NativeSearchQuery(matchAllQueryBuilder, rangeQueryBuilder, Arrays.asList(fieldSortBuilder));
            Sort sort = new Sort(Sort.Direction.DESC,"id");
            PageRequest pageRequest = new PageRequest(page,size,sort);
            //1.query方法 => 通过nativeSearchQuery进行查询 通过ResultsExtractor将响应结果转换为需要的实体
//            User user = elasticsearchTemplate.query(nativeSearchQuery, new ResultsExtractor<User>() {
//                @Override
//                public User extract(SearchResponse searchResponse) {
//                    return null;
//                }
//            });
//            2.queryForObject和queryForList方法 => 接收一个GetQuery参数和返回实体class 根据id查询
            GetQuery getQuery = new GetQuery();
            elasticsearchTemplate.queryForObject(getQuery,User.class);
            return ResponseUtils.success();
        } catch (Exception e) {
            log.error("文档获取失败,Reason:{}", e.toString());
            return ResponseUtils.fail();
        }
    }

}
```

> elasticsearchTemplate的query/queryForObject/queryForList和SpringDataES的search方法类似，都是需要SearchQuery接口，可以构造不同的查询类型。
>
> 参考文档：https://blog.csdn.net/qq_16436555/article/details/94433850

### <a id="http">ES原生HTTP客户端</a>











### <a id="EsrestTemplate">ESRestTemplate</a>









# <a id="st">ES相关生态</a>

### <a id="logstash">Logstash</a>



### <a id="beats">Beats</a>



### <a id="kibana">Kibana</a>



### <a id="x-pack">X-Pack</a>



# <a id="ym">ES源码</a>









# <a id="pzwj">ES配置文件解析</a>








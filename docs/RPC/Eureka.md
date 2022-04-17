# 一：Spring Cloud Eureka介绍

### 1.1 Spring Cloud Eureka介绍

Spring Cloud Eureka是Spring Cloud Netflix微服务套件中的一部分，它基于Netflix Eureka做了二次封装，主要负责完成微服务架构中的服务治理功能。Spring Cloud Eureka通过为Eureka增加了SpringBoot风格的自动化配置，只需要简单引入依赖和配置注解就能让SpringBoot构建的微服务应用轻松与Eureka服务治理体系进行整合。

### 1.2 服务治理介绍

服务治理可以说是微服务架构中最为核心和基础的模块，它主要用来实现各个微服务实例的自动注册与发现。

- 服务注册：在服务治理框架中，通常都会构建一个注册中心，每个服务单元向注册中心登记自己提供的服务，将主机、端口、版本号、通信协议等一些附加信息告诉注册中心，注册中心维护所有的服务清单。
- 服务发现：在服务治理框架帮助下，服务间的调用不再通过指定具体的实例地址来实现，而是通过向服务名发起请求实现。当需要服务提供方信息时，从注册中心拉取即可。

### 1.3 Netflix Eureka介绍

Netflix Eureka作为注册中心，既`包含了服务端组件，也包含了客户端组件`，并且服务端与客户端均采用Java编写，所以Eureka主要适用于通过Java实现的分布式系统，或是运行于Jvm之上的其他语言的系统。

Eureka除了Java客户端API之外，还`提供了RestFul接口`，因此也支持其他语言通过RestFul接口接入服务治理中，其他语言的客户端实现官方并没有提供，需要自己开发。

Eureka服务端也称为注册中心，和其他注册中心一样，`支持高可用配置`。它依托于强一致性提供良好的服务实例可用性，可以应对多种不同的故障场景。如果Eureka以集群方式模式部署，当集群分片中出现故障时，Eureka会转入自我保护模式。它允许在分片故障期间继续提供服务的发现和注册，当故障分片恢复时，集群中的其他分片会把它们的状态再次同步回来。

Eureka客户端主要用来处理服务的注册和发现，客户端通过注解和参数配置的方式，嵌入应用程序代码中，并在程序运行时，`向注册中心注册自身提供的服务并周期性的发送心跳来更新服务的租约`。同时，`能从注册中心查找所有服务的注册信息，并缓存到本地，周期性的更新服务状态`。

# 二：Spring Cloud Eureka实战

### 2.1 搭建注册中心

- 创建Maven聚合项目，引入依赖

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.5.6</version>
        <relativePath/> <!-- lookup parent from repository -->
    </parent>

    <groupId>org.example</groupId>
    <artifactId>SpringCloud</artifactId>
    <packaging>pom</packaging>
    <version>1.0-SNAPSHOT</version>
    <modules>
        <module>Eureka-Server</module>
    </modules>

    <properties>
        <maven.compiler.source>8</maven.compiler.source>
        <maven.compiler.target>8</maven.compiler.target>
        <java.version>1.8</java.version>
        <spring-cloud.version>2020.0.4</spring-cloud.version>
    </properties>

    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>${spring-cloud.version}</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>

    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>org.apache.commons</groupId>
            <artifactId>commons-lang3</artifactId>
            <version>3.9</version>
        </dependency>
        <dependency>
            <groupId>commons-io</groupId>
            <artifactId>commons-io</artifactId>
            <version>2.6</version>
        </dependency>
        <dependency>
            <groupId>org.apache.commons</groupId>
            <artifactId>commons-collections4</artifactId>
            <version>4.4</version>
        </dependency>
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <version>1.18.10</version>
        </dependency>
        <!-- 日志处理 -->
        <dependency>
            <groupId>log4j</groupId>
            <artifactId>log4j</artifactId>
            <version>1.2.17</version>
        </dependency>
        <dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-log4j12</artifactId>
            <version>1.7.26</version>
        </dependency>
        <dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-api</artifactId>
            <version>1.7.26</version>
        </dependency>
        <dependency>
            <groupId>com.alibaba</groupId>
            <artifactId>fastjson</artifactId>
            <version>1.2.73</version>
        </dependency>
    </dependencies>

    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
                <configuration>
                    <excludes>
                        <exclude>
                            <groupId>org.projectlombok</groupId>
                            <artifactId>lombok</artifactId>
                        </exclude>
                    </excludes>
                </configuration>
            </plugin>
        </plugins>
    </build>

</project>
```

- 创建EurekaServer工程，引入依赖

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <parent>
        <artifactId>SpringCloud</artifactId>
        <groupId>org.example</groupId>
        <version>1.0-SNAPSHOT</version>
    </parent>
    <modelVersion>4.0.0</modelVersion>

    <artifactId>Eureka-Server</artifactId>

    <properties>
        <maven.compiler.source>8</maven.compiler.source>
        <maven.compiler.target>8</maven.compiler.target>
    </properties>

    <dependencies>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-eureka-server</artifactId>
        </dependency>
    </dependencies>

</project>
```

- 启动类添加注解

```java
/**
 * Eureka注册中心
 * @author lizhaoxuan
 * @date 2021/10/30
 */
@EnableEurekaServer
@SpringBootApplication
public class EurekaServerApplication {

    public static void main(String[] args) {
        SpringApplication.run(EurekaServerApplication.class, args);
    }

}
```

- 添加配置文件

```yml
server:
  port: 9001

# eureka
eureka:
  instance:
    # eureka实例名称
    hostname: eureka-server
  client:
    # 是否注册自身到eureka
    register-with-eureka: false
    # 是否从eureka server拉取注册服务信息
    fetch-registry: false
    serviceUrl:
      # eureka服务注册地址
      defaultZone: http://${eureka.instance.hostname}:${server.port}/eureka/
```

- 启动服务，访问9001端口

![image-20211030172740651](http://rocks526.top/lzx/image-20211030172740651.png)

可以看到，eureka server已经搭建成功，由于还没有服务注册，因此`Instances currently registered with Eureka`面板是空的。

### 2.2 注册服务

- 添加一个用户服务，引入Maven依赖

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <parent>
        <artifactId>SpringCloud</artifactId>
        <groupId>org.example</groupId>
        <version>1.0-SNAPSHOT</version>
    </parent>
    <modelVersion>4.0.0</modelVersion>

    <artifactId>user-service</artifactId>

    <properties>
        <maven.compiler.source>8</maven.compiler.source>
        <maven.compiler.target>8</maven.compiler.target>
    </properties>

    <dependencies>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
        </dependency>
    </dependencies>

</project>
```

- 添加配置文件

```yml
server:
  port: 9002
spring:
  application:
    name: user-service

# eureka
eureka:
  client:
    serviceUrl:
      # eureka服务注册地址
      defaultZone: http://localhost:9001/eureka/
```

- 添加注解，打开Eureka注册

```java
/**
 * 用户服务
 * @author lizhaoxuan
 * @date 2021/10/30
 */
@EnableEurekaClient
@SpringBootApplication
public class UserServiceApplication {

    public static void main(String[] args) {
        SpringApplication.run(UserServiceApplication.class, args);
    }

}
```

- 添加提供服务的接口

```java
package com.lizhaoxuan.controller;

import com.lizhaoxuan.entity.User;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

/**
 * UserController
 * @author lizhaoxuan
 * @date 2021/10/30
 */
@RestController
@RequestMapping("/user")
public class UserController {

    @GetMapping("/{id}")
    public User getUserInfo(@PathVariable Long id){
        return User.mockUser(id);
    }

}
```

```java
package com.lizhaoxuan.entity;

import lombok.AllArgsConstructor;
import lombok.Builder;
import lombok.Data;
import lombok.NoArgsConstructor;
import lombok.experimental.Accessors;
import org.apache.commons.lang3.RandomUtils;

import java.time.LocalDateTime;
import java.util.UUID;

@Data
@NoArgsConstructor
@AllArgsConstructor
@Builder
public class User {

    private Long id;
    private String name;
    private String pass;
    private LocalDateTime createTime;
    private Boolean enable;

    public static User mockUser(Long id){
        return new UserBuilder()
                .id(id)
                .name("Demo-" + UUID.randomUUID())
                .pass("<Pass>-" + UUID.randomUUID())
                .createTime(LocalDateTime.now().minusDays(RandomUtils.nextInt(0,10)))
                .enable(RandomUtils.nextBoolean()).build();
    }

}
```

- 启动服务

![image-20211030180332466](http://rocks526.top/lzx/image-20211030180332466.png)

![image-20211030180152974](http://rocks526.top/lzx/image-20211030180152974.png)

可以看到，服务已经成功注册到eureka server。

### 2.3 服务发现与消费











### 2.4 高可用注册中心













# 三：Spring Cloud Eureka原理













# 四：Spring Cloud Eureka源码


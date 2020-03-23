- [Neo4j介绍](#js)

- [Neo4j安装](#az)
- [Neo4j基础](#mx)

- [CQL](#CQL)

- [SpringBoot整合Neo4j](#sb)

# <a id="js">Neo4j介绍</a>

Neo4j是一个Java实现的高性能的NoSQL数据库，将结构化数据存储在拓扑图上而不是表中，从而轻松地管理巨量的数据。

**Neo4j特点**

- 图形存储数据，解决了SQL数据库查询复杂关系时的"多表连接爆炸"问题，查找相邻节点和关系性能很强
- 提供声明式查询语言CQL，比命令式查询语言SQL更易于理解
- 支持完整的ACID，这一点区别于Redis，MongoDB等NoSQL数据库

**Neo4j适用场景**

- 社交网络
- 智能推荐引擎
- 恶意软件检测
- 软件开发依赖分析

# <a id="az">Neo4j安装</a>

**Windows安装Neo4j**

- 官网下载安装包，目前最新版本是4.0.2
  - https://neo4j.com/download-center/#community
- 解压压缩包
- 配置环境变量
- 安装neo4j服务
  - neo4j install-service
- 进入bin目录启动neo4j，访问地址http://IP:7474/，出现Neo4j的可视化操作界面即安装成功
  - bin/neo4j.bat start

****

**Linux安装Neo4j**

- 官网下载安装包，目前最新版本是4.0.2
  - https://neo4j.com/download-center/#community
- 上传Linux并解压压缩包
  - tar -xzvf neo4j-community-4.0.2-unix.tar.gz 
- 进入conf目录修改配置文件，开放远程访问
  - dbms.connectors.default_listen_address=0.0.0.0注释去掉
- 进入bin目录，运行./neo4j start命令启动neo4j，访问地址http://IP:7474/，出现Neo4j的可视化操作界面即安装成功
  - 其他命令有./neo4j console|start|stop|restart|status

> Neo4j需要依赖Java环境，目前最新版的Neo4j4.0依赖jdk11

# <a id="mx">Neo4j基础</a>

**Neo4j数据模型**

Neo4j遵循图模型来存储和管理其数据，图模型的主要构建块是：

- 节点：通常用于存储实体信息
- 关系：用来将节点连接起来构建实体，它们相当于在关系数据库管理系统中显式存储和预先计算的连接查询
- 属性：节点和关系都是属性的容器，它们实际上是键值对组成的
- 标签：使用标签能够快速高效地对节点分类并创建子图，节点可设置多个标签，关系只能设置一个标签

他们之间具备如下关系：

- 节点和关系都包含属性，属性中存储kv数据
- 关系连接节点
- 节点用圆圈表示，关系用方向键表示
- 关系具有方向：单向和双向
- 每个关系包含“开始节点”或“从节点”和“到节点”或“结束节点”
- 标签将一个公共名称与一组节点或关系相关联

------

**Neo4j中的数据类型**

Neo4j的数据类型与Java语言类似， 它们用于定义节点或关系的属性

| S.No. | CQL数据类型 | 用法                            |
| ----- | ----------- | ------------------------------- |
| 1.    | boolean     | 用于表示布尔文字：true，false。 |
| 2.    | byte        | 用于表示8位整数。               |
| 3.    | short       | 用于表示16位整数。              |
| 4.    | int         | 用于表示32位整数。              |
| 5.    | long        | 用于表示64位整数。              |
| 6.    | float       | I用于表示32位浮点数。           |
| 7.    | double      | 用于表示64位浮点数。            |
| 8.    | char        | 用于表示16位字符。              |
| 9.    | String      | 用于表示字符串。                |

# <a id="CQL">CQL</a>

**CQL介绍**

CQL代表Cypher查询语言， 像关系型数据库的查询语言SQL，Neo4j具有CQL作为查询语言。

- 它是Neo4j图形数据库的查询语言。
- 它是一种声明性模式匹配语言
- 它遵循SQL语法。
- 它的语法是非常简单且人性化、可读的格式。

****

**CQL特点**

- 声明式语言
  - 告诉db需要什么数据，由db自行优化选择查询方案
- 具有表现力
  - 通过()，->，[]等符号表示，易于理解
- 模式匹配的
- 幂等的

****

**语法约定**

- 节点别名用小写驼峰
- 标签用大写驼峰
- 关系用大写加下划线
- 属性名用小写驼峰
- 关键词全部大写

****

**常用CQL命令**

| S.No. | CQL命令         | 用法                         |
| ----- | --------------- | ---------------------------- |
| 1.    | CREATE 创建     | 创建节点，关系和属性         |
| 2.    | MATCH 匹配      | 检索有关节点，关系和属性数据 |
| 3.    | RETURN 返回     | 返回查询结果                 |
| 4.    | WHERE 哪里      | 提供条件过滤检索数据         |
| 5.    | DELETE 删除     | 删除节点和关系               |
| 6.    | REMOVE 移除     | 删除节点和关系的属性         |
| 7.    | ORDER BY以…排序 | 排序检索数据                 |
| 8.    | SET 组          | 添加或更新操作               |
| 9.    | SKIP,LIMIT分页  | 对查询出的数据分页           |

# <a id="sb">SpringBoot整合Neo4j</a>

- 创建pringboot项目，引入相关依赖

```java
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-data-neo4j</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>

        <dependency>
            <groupId>org.neo4j</groupId>
            <artifactId>neo4j-ogm-embedded-driver</artifactId>
        </dependency>

        <!-- add this dependency if you want to use the HTTP driver -->
        <dependency>
            <groupId>org.neo4j</groupId>
            <artifactId>neo4j-ogm-http-driver</artifactId>
        </dependency>

        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <optional>true</optional>
        </dependency>
```

- 添加@EnableNeo4jRepositories注解开启Neo4jRepositories

```java
@SpringBootApplication
@EnableNeo4jRepositories
public class SpringbootNeo4jApplication {

    public static void main(String[] args) {
        SpringApplication.run(SpringbootNeo4jApplication.class, args);
    }

}
```

- 编写实体类

```java
@NodeEntity(label = "User")
@Data
public class UserNode {

    //图形id
    @Id
    @GeneratedValue
    private Long nodeId;

    @Property
    private String userName;

    @Property
    private int age;

}

@Data
@RelationshipEntity(type = "UserRelation")
public class UserRelation {

    @Id
    @GeneratedValue
    private Long id;

    //开始节点
    @StartNode
    private UserNode startNode;

    //结束节点
    @EndNode
    private UserNode endNode;

}
```

- 编写Neo4jRepositories

```java
@Repository
public interface UserRepository extends Neo4jRepository<UserNode, Long> {

    @Query("MATCH (n: User) RETURN n")
    List<UserNode> getUserNodeList();

    @Query("MATCH (n:User) where n.userName = {userName} return n")
    UserNode findByName(@Param("userName") String userName);

}

@Repository
public interface UserRelationRepository extends Neo4jRepository<UserRelation,Long> {

    @Query("MATCH p = (n:User) <-[r: UserRelation]-> (n1:User) where n.userName = {0} and n1.userName = {1} return p")
    List<UserRelation> findUserRelationByUserName (@Param(" firstUserName") String firstUserName, @Param ("secondUserName") String secondUserName) ;

}
```

- 编写controller测试

```java
@RestController
@RequestMapping("/user")
public class UserController {

    @Autowired
    private UserRepository userRepository;

    @RequestMapping("/add")
    public String addUserNode(String userName,Integer age){
        UserNode userNode = new UserNode();
        userNode.setAge(age);
        userNode.setUserName(userName);
        userRepository.save(userNode);
        return "add success ";
    }

    @RequestMapping("/get")
    public List<UserNode> getUserList(){
        return userRepository.getUserNodeList();
    }
}

@RestController
@RequestMapping("/userRelation")
public class UserRelationController {

    @Autowired
    private UserRelationRepository userRelationRepository;

    @Autowired
    private UserRepository userRepository;

    @RequestMapping("/add")
    public String addUserRelation(String firstName,String secondName){
        UserRelation userRelation = new UserRelation();
        UserNode firstNode = userRepository.findByName(firstName);
        UserNode secondNode = userRepository.findByName(secondName);
        userRelation.setStartNode(firstNode);
        userRelation.setEndNode(secondNode);
        userRelationRepository.save(userRelation);
        return "add success!";
    }

    @RequestMapping("/get")
    public List<UserRelation> findUserRelationByUserName(String firstName, String secondName){
        System.out.println(firstName + "   " + secondName);
        return userRelationRepository.findUserRelationByUserName(firstName,secondName);
    }
}
```














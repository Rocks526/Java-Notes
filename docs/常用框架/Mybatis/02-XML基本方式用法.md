# 一：环境准备

### 1.1 需求介绍

这一章通过设定一个简单的权限控制需求，采用 RBAC 模型来熟悉myabtis的xml方式开发。需求如下：

- 用户的CRUD
- 角色的CRUD
- 用户、角色、权限的关联

### 1.2 初始化表

```sql

-- ----------------------------
-- Table structure for privilege
-- ----------------------------
DROP TABLE IF EXISTS "public"."privilege";
CREATE TABLE "public"."privilege" (
                                      "id" serial8 NOT NULL,
                                      "name" varchar(255) COLLATE "pg_catalog"."default" NOT NULL,
                                      "url" varchar(255) COLLATE "pg_catalog"."default" NOT NULL
)
;

-- ----------------------------
-- Table structure for role
-- ----------------------------
DROP TABLE IF EXISTS "public"."role";
CREATE TABLE "public"."role" (
                                 "id" serial8 NOT NULL,
                                 "name" varchar(255) COLLATE "pg_catalog"."default" NOT NULL,
                                 "create_time" timestamp(6) NOT NULL,
                                 "update_time" timestamp(6) NOT NULL
)
;

-- ----------------------------
-- Table structure for role_privilege
-- ----------------------------
DROP TABLE IF EXISTS "public"."role_privilege";
CREATE TABLE "public"."role_privilege" (
                                           "id" serial8 NOT NULL,
                                           "role_id" int8 NOT NULL,
                                           "privilege_id" int8 NOT NULL
)
;

-- ----------------------------
-- Table structure for user
-- ----------------------------
DROP TABLE IF EXISTS "public"."user";
CREATE TABLE "public"."user" (
                                 "id" serial8 NOT NULL,
                                 "name" varchar(255) COLLATE "pg_catalog"."default" NOT NULL,
                                 "pass" varchar(255) COLLATE "pg_catalog"."default" NOT NULL,
                                 "create_time" timestamp(6) NOT NULL,
                                 "update_time" timestamp(6) NOT NULL
)
;

-- ----------------------------
-- Table structure for user_role
-- ----------------------------
DROP TABLE IF EXISTS "public"."user_role";
CREATE TABLE "public"."user_role" (
                                      "id" serial8 NOT NULL,
                                      "user_id" int8 NOT NULL,
                                      "role_id" int8 NOT NULL
)
;

-- ----------------------------
-- Primary Key structure for table privilege
-- ----------------------------
ALTER TABLE "public"."privilege" ADD CONSTRAINT "privilege_pkey" PRIMARY KEY ("id");

-- ----------------------------
-- Primary Key structure for table role
-- ----------------------------
ALTER TABLE "public"."role" ADD CONSTRAINT "role_pkey" PRIMARY KEY ("id");

-- ----------------------------
-- Primary Key structure for table role_privilege
-- ----------------------------
ALTER TABLE "public"."role_privilege" ADD CONSTRAINT "role_privilege_pkey" PRIMARY KEY ("id");

-- ----------------------------
-- Primary Key structure for table user
-- ----------------------------
ALTER TABLE "public"."user" ADD CONSTRAINT "user_pkey" PRIMARY KEY ("id");

-- ----------------------------
-- Primary Key structure for table user_role
-- ----------------------------
ALTER TABLE "public"."user_role" ADD CONSTRAINT "user_role_pkey" PRIMARY KEY ("id");


-- 初始化数据
INSERT INTO "public"."user"("name", "pass", "create_time", "update_time") VALUES ('超级管理员', 'admin1234', '2021-09-10 16:35:55', '2021-09-10 16:35:55');
INSERT INTO "public"."role"("name","create_time", "update_time") VALUES ('admin', '2021-09-10 16:35:55', '2021-09-10 16:35:55');
INSERT INTO "public"."privilege"("name","url") VALUES ('系统管理', '/systems');
INSERT INTO "public"."privilege"("name","url") VALUES ('用户管理', '/accounts');
INSERT INTO "public"."privilege"("name","url") VALUES ('角色管理', '/roles');
INSERT INTO "public"."privilege"("name","url") VALUES ('审计日志', '/auditLogs');
INSERT INTO "public"."user_role"("user_id","role_id") VALUES (1, 1);
INSERT INTO "public"."role_privilege"("role_id","privilege_id") VALUES (1, 1);
INSERT INTO "public"."role_privilege"("role_id","privilege_id") VALUES (1, 2);
INSERT INTO "public"."role_privilege"("role_id","privilege_id") VALUES (1, 3);
INSERT INTO "public"."role_privilege"("role_id","privilege_id") VALUES (1, 4);
```

### 1.3 创建实体

```java
@Data
@NoArgsConstructor
@Accessors(chain = true)
public class User {

    private Long id;
    private String name;
    private String pass;
    private Date createTime;
    private Date updateTime;

}

@Data
@NoArgsConstructor
@Accessors(chain = true)
public class Role {

    private Long id;
    private String name;
    private Date createTime;
    private Date updateTime;

}

@Data
@NoArgsConstructor
@Accessors(chain = true)
public class Privilege {

    private Long id;
    private String name;
    private String url;

}

@Data
@NoArgsConstructor
@Accessors(chain = true)
public class UserRole {

    private Long id;
    private Long userId;
    private Long roleId;

}

@Data
@NoArgsConstructor
@Accessors(chain = true)
public class RolePrivilege {

    private Long id;
    private Long roleId;
    private Long privilegeId;

}
```

> mybatis对表名和类名的映射支持下划线转驼峰，因此类名命名规范即可自动转换，无需配置映射，字段名称不一致的需要配置映射。
>
> 在创建实体类时，推荐采用包装类型，因为Java的基本类型都有值，不可能为null，因此在动态查询时，如果通过null判断，会出现很多隐藏问题。

# 二：XML方式

### 2.1 XML方式介绍

MyBatis 真正强大之处在于它的映射语句，这也是它的魔力所在。由于它的映射语句异常强大，映射器的 XML 文件就显得相对简单。如果将其与具有相同功能的 JDBC 代码进行对比，立刻就会发现，使用这种方法节省了将近 95% 的代码量。

MyBatis 3.0 相比 2.0 版本的有个最大变化，就是`支持使用接口来调用方法`。

以前使用 SqlSession 通过命名空间调用 MyBatis 方法时，首先需要用到命名空间和方法 id 组成的字符串来调用相应的方法，当参数多于1个的时候，需要将所有参数放到 Map 对象，通过 Map 传递多个参数，使用起来很不方便，而且还无法避免很多重复的代码。

使用接口调用方式就会方便很多， MyBatis 使用 Java 的动态代理可以直接通过接口来调用方法，不需要提供接口的实现类，更不需要在实现类中使用 SqlSession 以通过命名空间间接调用。另外，当有多个参数的时候，通过参数注解＠Param 设置参数的名字省 去了手动构造 Map 参数的过程，尤其在 Spring 中使用的时候，可以配置为自动扫描所有的接口类 ，直接将接口注入需要用到的地方。

下面的都以接口方式作为开发示例。

### 2.2 接口创建

```java
package com.lizhaoxuan.xml.mapper;
public interface UserMapper {
}

package com.lizhaoxuan.xml.mapper;
public interface RoleMapper {
}

package com.lizhaoxuan.xml.mapper;
public interface PrivilegeMapper {
}

package com.lizhaoxuan.xml.mapper;
public interface UserRoleMapper {
}

package com.lizhaoxuan.xml.mapper;
public interface RolePrivilegeMapper {
}
```

### 2.3 XML文件创建

- 全局配置文件

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration PUBLIC "-//mybatis.org//DTD Config 3.0//EN" "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>

    <settings>
        <!-- 打印查询语句 -->
        <setting name="logImpl" value="LOG4J" />
    </settings>

    <!-- 别名 -->
    <typeAliases>
        <package name="com.lizhaoxuan.xml.entity"/>
    </typeAliases>

    <!-- 环境配置 -->
    <environments default="dev">
        <environment id="dev">
            <transactionManager type="JDBC"/>
            <dataSource type="UNPOOLED">
                <property name="driver" value="org.postgresql.Driver"/>
                <property name="url" value="jdbc:postgresql://127.0.0.1:5432/postgres"/>
                <property name="username" value="postgres"/>
                <property name="password" value="admin"/>
            </dataSource>
        </environment>
    </environments>

    <!-- mapper映射文件 -->
    <mappers>
        <mapper resource="mapper/xml/UserMapper.xml"/>
        <mapper resource="mapper/xml/RoleMapper.xml"/>
        <mapper resource="mapper/xml/UserRoleMapper.xml"/>
        <mapper resource="mapper/xml/PrivilegeMapper.xml"/>
        <mapper resource="mapper/xml/RolePrivilegeMapper.xml"/>
        <!-- 当xml较多时可以直接扫描接口所在的包名，mybatis自动根据接口的全限定名去找对应的mapper.xml文件，但由于此项目的xml文件和接口的全路径名不一致，因此无法扫描到 -->
<!--        <package name="com.lizhaoxuan.xml.mapper"/>-->
    </mappers>

</configuration>
```

- UserMapper.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<mapper namespace="com.lizhaoxuan.xml.mapper.UserMapper">
</mapper>
```

> 其他xml文件与User类似，不再一一展示。

# 三：select用法

### 3.1 select简单查询

在权限系统中，存在查询用户、角色和权限等需求，对于Jdbc代码，需要查询数据库后手动解析结果集，将数据映射到Java对象中。在mybatis中，只需要xml中添加一个`select元素`，写一个查询的SQL，再做一些简单的配置，就可以将查询结果自动映射到对象中。

- 根据id查询用户详情

```xml
    <!-- 对象属性映射 -->
    <resultMap id="UserMap" type="com.lizhaoxuan.xml.entity.User">
        <id property="id" column="id"/>
        <result property="name" column="name"/>
        <result property="pass" column="pass"/>
        <result property="createTime" column="create_time" jdbcType="TIMESTAMP"/>
        <result property="updateTime" column="update_time" jdbcType="TIMESTAMP"/>
    </resultMap>

    <select id="selectById" resultMap="UserMap">
        select * from public.user where id = #{id}
    </select>
```

```java
public interface UserMapper {
    // 根据id查询用户
    User selectById(Long id);
}
```

- 测试代码

```java
package com.lizhaoxuan.xml;

import com.lizhaoxuan.xml.entity.User;
import com.lizhaoxuan.xml.mapper.UserMapper;
import lombok.SneakyThrows;
import lombok.extern.slf4j.Slf4j;
import org.apache.ibatis.io.Resources;
import org.apache.ibatis.session.SqlSession;
import org.apache.ibatis.session.SqlSessionFactory;
import org.apache.ibatis.session.SqlSessionFactoryBuilder;
import org.junit.jupiter.api.BeforeAll;
import org.junit.jupiter.api.Test;

import java.io.Reader;

/**
 * Select元素测试
 */
@Slf4j
public class SelectTest {

    public static SqlSessionFactory sqlSessionFactory;

    @SneakyThrows
    @BeforeAll
    public static void init(){
        // 加载配置文件
        Reader reader = Resources.getResourceAsReader("mybatis-config.xml");
        // 创建SqlSession工厂
        sqlSessionFactory = new SqlSessionFactoryBuilder().build(reader);
        reader.close();
    }

    @Test
    public void userTest(){
        // 开启sqlSession
        SqlSession sqlSession = sqlSessionFactory.openSession();
        // 直接获取xml元素执行sql
        User user = sqlSession.selectOne("com.lizhaoxuan.xml.mapper.UserMapper.selectById", 1L);
        log.info("select by NameSpaceAndElement result:{}",user);
        // 根据接口执行sql
        UserMapper userMapper = sqlSession.getMapper(UserMapper.class);
        User user2 = userMapper.selectById(1L);
        log.info("select by mapper result:{}", user2);
    }

}
```

- 测试结果

![image-20210923152157056](http://rocks526.top/lzx/image-20210923152157056.png)

输出结果如图所示，查出了id为1的用户信息，除此以外还能看到第二次查询时，没有打印SQL语句，这个是因为sqlSession缓存了上次的查询结果，第二次是直接走了缓存。

### 3.2 接口查询方式原理介绍

之前介绍过，全局配置文件加载mapper文件时，既可以通过指定每个xml文件的位置，也可以通过指定mapper接口的包名实现批量加载，那么他们是如何关联起来的？

- 指定包名时，mybatis会自动扫描包下所有接口，根据接口的全限定名去加载对应路径的xml文件
- 指定xml文件时，会通过NameSapce的值将xml和接口进行关联

解释了接口和xml文件的关联方式后，那么方法又是怎么和xml的元素关联起来的？

- mybatis是通过方法名称去查询xml里id重名的元素的，查询到后，将方法参数封装传递给xml的sql

### 3.3 select标签介绍

介绍完通过接口执行SQL后，接下来介绍刚才xml里涉及的一些标签：

- \<select>：映射查询语句使用的标签。
- id：命名空间的唯一标识，可以用来代表一条SQL语句。
- resultMap：用于配置返回值的类型和映射的关系。
- select标签的内容是SQL查询语句。
- #{id}：mybatis SQL中使用预编译参数的一种方式，大括号中的id是传入的参数名。

在上面示例的select中，使用resultMap设置返回值类型，这里的userMap就是上面\<resultMap>中的id的属性值，通过id引用\<resultMap>。

-----------------

\<resultMap>标签用于配置结果集和对象映射，有如下属性：

- id：必填，唯一标识，用于引用。
- type：必填，用于配置查询结果映射到的Java对象。
- extends：选填，可以配置当前resultMap继承自其他的resultMap，属性值为被继承的resultMap的id。
- autoMapping：选填，是否开启自动映射（未配置result标签的字段是否进行映射），可选值为true或false。该值会覆盖全局配置的autoMappingBehavior配置。

以上是\<resultMap>的属性，\<resultMap>可以包含以下子标签：

- constructor ： 配置使用构造方法注入结果，包含以下两个子标签（默认属性的set方法注入）
  - idArg: id 参数，标记结果作为id （唯一值），可以帮助提高整体性能
  - arg ：注入到构造方法的一个普通结果
- id ： 一个id 结果，标记结果作为id （主键、唯一值），可以帮助提高整体性能。
- result ： 注入到Java 对象属性的普通结果。
- association ： 一个复杂的类型关联，许多结果将包成这种类型。
- collection ： 复杂类型的集合。
- discriminator ：根据结果值来决定使用哪个结果映射。
- case ： 基于某些值的结果映射。

本章中会介绍常用标签constructor 、id 、result。而association 、collection和discriminator 标签会在后面的章节中介绍。

-------------

首先来了解一下这些标签属性之间的关系：

- constructor ： 通过构造方法注入属性的结果值。构造方法中的idArg 、arg 参数分别对应着resultMap 中的id 、result 标签，它们的含义相同，只是注入方式不同。
- resultMap 中的id 和result 标签包含的属性相同，不同的地方在于， id 代表的是主键（或唯一值）的字段（可以有多个），它们的属性值是通过setter方法注入的。

接着来看一下id 和result 标签包含的属性：

- column ：从数据库中得到的列名， 或者是列的别名。
- property ：映射到列结果的属性。可以映射简单的如“ username ”这样的属性，也可以映射一些复杂对象中的属性， 例如“ address.street.number ”，这会通过“ ．”方式的属性嵌套赋值。
- javaType ： 一个Java类的完全限定名，或一个类型别名（通过typeAlias 配置或者默认的类型）。如果映射到一个JavaBean，MyBatis 通常可以自动判断属性的类型。`如果映射到HashMap ，则需要明确地指定javaType 属性。`

- jdbcType ：列对应的数据库类型。JDBC类型仅仅需要对插入、更新、删除操作可能为空的列进行处理。这是JDBC对jdbcType 的需要，而不是MyBatis 的需要。`（JDBC对于不同类型的数据库字段，当值为null时，需要调用不同的方法设置）`
- typeHandler ：使用这个属性可以覆盖默认的类型处理器。这个属性值是类的完全限定名或类型别名。

-------------------

### 3.4 返回值

下面来看一下接口方法的返回值要如何定义：

- 接口中定义的返回值类型必须和XML中配置的resultType 类型一致，否则就会因为类型不一致而抛出异常。
- 返回值类型是由XML 中的resultType （或resultMap中的type ）决定的，不是由接口中写的返回值类型决定的。

UserMapper 接口中的selectByid 方法，通过主键id 查询，最多会有一条记录，所以这里定义的返回值为User。

当返回结果是List时，resultType可以指定为List里元素的类型，例如查询全部用户：

```java
public interface UserMapper {

    // 根据id查询用户
    User selectById(Long id);

    // 查询全部用户
    List<User> selectAll();

}
```

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<mapper namespace="com.lizhaoxuan.xml.mapper.UserMapper">

    <!-- 对象属性映射 -->
    <resultMap id="UserMap" type="com.lizhaoxuan.xml.entity.User">
        <id property="id" column="id"/>
        <result property="name" column="name"/>
        <result property="pass" column="pass"/>
        <result property="createTime" column="create_time" jdbcType="TIMESTAMP"/>
        <result property="updateTime" column="update_time" jdbcType="TIMESTAMP"/>
    </resultMap>
    
    <select id="selectAll" resultType="User">
        select
            id,
            name,
            pass,
            create_time as "createTime",
            update_time as "updateTime"
        from public.user
    </select>

    <select id="selectById" resultMap="UserMap">
        select * from public.user where id = #{id}
    </select>

</mapper>
```

> 当SQL的返回值最多只有1个结果的时候（可以0 个），可以将接口返回值定义为User ，而不是List\<User>。当然，如果将返回值改为List\<User>或User[] ，也没有问题，只是不建议这么做。
>
> 当执行的SQL 返回多个结果时，必须使用List\<User>或User[]作为返回值，如果使用User ，就会抛出TooManyResultsException 异常。

在上面的selectAll方法中，由于没有使用resultMap自动映射字段，因此采用as别名的方式完成映射。在mybatis中，字段属性映射方式有三种：

- mybatis-config.xml配置下划线自动转驼峰映射（不符合标准的还是需要另外两种）

```xml
    <settings>
        <!-- 打印查询语句 -->
        <setting name="logImpl" value="LOG4J" />
        <!-- 开启下划线自动转驼峰 -->
        <setting name="mapUnderscoreToCamelCase" value="true" />
    </settings>
```

- resultMap标签：通过resultMap标签描述返回值的解析
- as别名：通过SQL的as别名将列名转换为属性名

> mybatis在字段映射的时候不区分大小写，都会全部转成大写进行匹配。

### 3.5 多表查询

上面两个SELECT 查询仅仅是简单的单表查询，这里列举的是两种常见的情况，在实际业务中还需要多表关联查询，关联查询结果的类型也会有多种情况，下面来列举一些复杂的用法。

- 多表查询，返回结果为单表

根据用户id 获取用户拥有的所有角色，返回的结果为角色集合，结果只有角色的信息，不包含额外的其他字段信息。

```java
    // 查询用户拥有的所有角色
    List<Role> selectRoleByUserId(Long userId);
```

```xml
    <select id="selectRoleByUserId" resultType="Role">
        select
            r.*
        from public.user u,public.user_role ur,public.role r
        where u.id = ur.user_id and r.id = ur.role_id and u.id = #{userId}
    </select>
```

```java
    @Test
    public void selectRoleByUserIdTest(){
        // 开启sqlSession
        SqlSession sqlSession = sqlSessionFactory.openSession();
        // 根据接口执行sql
        UserMapper userMapper = sqlSession.getMapper(UserMapper.class);
        List<Role> roles = userMapper.selectRoleByUserId(1L);
        roles.forEach(System.out::println);
    }
```

![image-20210923162440570](http://rocks526.top/lzx/image-20210923162440570.png)

- 多表查询，返回结果为多表

根据用户id 获取用户拥有的所有角色，返回的结果为角色集合，但角色需要包含一些用户名称字段。

实现方式一：新增一个Vo，继承于Role，新增需要的多余属性。

```java
@Data
public class RoleVo extends Role {
    private String userName;
}

    // 查询用户拥有的所有角色，富化用户信息
    List<RoleVo> selectRoleAndUserNameByUserId(Long userId);
```

```xml
    <select id="selectRoleAndUserNameByUserId" resultType="com.lizhaoxuan.xml.vo.RoleVo">
        select
            r.*,
            u.name as "userName"
        from public.user u,public.user_role ur,public.role r
        where u.id = ur.user_id and r.id = ur.role_id and u.id = #{userId}
    </select>
```

实现方式二：Role实体类新增关联的对象属性，将SQL的结果集解析注入关联对象中

```java
@Data
@NoArgsConstructor
@Accessors(chain = true)
public class Role {

    private Long id;
    private String name;
    private Date createTime;
    private Date updateTime;

    // ============== 富化属性 =========================
    private User user;

}

    // 查询用户拥有的所有角色，富化用户信息
    List<Role> selectRoleAndUserNameByUserId2(Long userId);
```

```xml
    <select id="selectRoleAndUserNameByUserId2" resultType="Role">
        select
            r.*,
            u.name as "user.name"
        from public.user u,public.user_role ur,public.role r
        where u.id = ur.user_id and r.id = ur.role_id and u.id = #{userId}
    </select>
```

```java
    @Test
    public void selectRoleAndUserNameByUserIdTest(){
        // 开启sqlSession
        SqlSession sqlSession = sqlSessionFactory.openSession();
        // 根据接口执行sql
        UserMapper userMapper = sqlSession.getMapper(UserMapper.class);
        List<RoleVo> roles = userMapper.selectRoleAndUserNameByUserId(1L);
        roles.forEach(System.out::println);

        List<Role> roles2 = userMapper.selectRoleAndUserNameByUserId2(1L);
        roles2.forEach(System.out::println);
    }
```

![image-20210923191038799](http://rocks526.top/lzx/image-20210923191038799.png)

> 以上只是两种简单的映射方式，后续的复杂查询章节会介绍mybatis的级联操作。

# 四：insert用法

和select相比， insert 要简单很多。只有让它返回主键值时，由于不同数据库的主键生成方式不同，这种情况下会有一些复杂。

### 4.1 简单insert

```java
    // 新增用户
    int save(User user);
```

```xml
    <!-- jdbcType字段存在自动映射，非必须，此处只为展示 -->
    <insert id="save" >
        insert into public.user (name,pass,create_time,update_time)
            values (#{name}, #{pass}, #{createTime, jdbcType=TIMESTAMP}, #{updateTime, jdbcType=TIMESTAMP})
    </insert>
```

```java
    @Test
    public void saveTest(){
        // 开启sqlSession
        SqlSession sqlSession = sqlSessionFactory.openSession();
        // 根据接口执行sql
        UserMapper userMapper = sqlSession.getMapper(UserMapper.class);
        int effectRows = userMapper.save(new User().setName("lizhaoxuan").setPass("admin1234").setCreateTime(new Date()).setUpdateTime(new Date()));
        log.info("saveResult={}!", effectRows == 1);
        // 提交事务
        sqlSession.commit();
        sqlSession.close();
    }
```

![image-20210923192849053](http://rocks526.top/lzx/image-20210923192849053.png)

### 4.2 获取自增主键

insert标签返回的int是影响的行数，而不是新增记录的主键。想要获取生成的自增主键，有两种方式：

- 使用JDBC方式返回主键自增的值（通过JDBC的getGeneratedKeys方法获取，需要数据库支持）
- 使用selectKey返回主键的值（通过一个新的查询，使用数据库内置函数获取）

```java
    // 新增用户 & 通过JDBC方式获取自增主键
    int save2(User user);

    // 新增用户 & 通过selectKey方式获取自增主键
    int save3(User user);
```

```xml
    <!-- JDBC方式获取自增主键 -->
    <insert id="save2" useGeneratedKeys="true" keyProperty="id" keyColumn="id">
        insert into public.user (name,pass,create_time,update_time)
        values (#{name}, #{pass}, #{createTime, jdbcType=TIMESTAMP}, #{updateTime, jdbcType=TIMESTAMP})
    </insert>

    <!-- selectKey方式获取自增主键 -->
    <insert id="save3" >
        insert into public.user (name,pass,create_time,update_time)
        values (#{name}, #{pass}, #{createTime, jdbcType=TIMESTAMP}, #{updateTime, jdbcType=TIMESTAMP})
        <!-- 在insert之后执行这个sql，获取自增主键并设置回去 -->
        <selectKey keyColumn="id" keyProperty="id" resultType="long" order="AFTER">
            select currval('user_id_seq')
        </selectKey>
    </insert>
```

```java
    @Test
    public void save2Test(){
        // 开启sqlSession
        SqlSession sqlSession = sqlSessionFactory.openSession();
        // 根据接口执行sql
        UserMapper userMapper = sqlSession.getMapper(UserMapper.class);
        User user = new User().setName("lizhaoxuan2").setPass("admin1234").setCreateTime(new Date()).setUpdateTime(new Date());
        int effectRows = userMapper.save2(user);
        log.info("saveResult={},newId={}!", effectRows == 1, user.getId());
        // 提交事务
        sqlSession.commit();
        sqlSession.close();
    }

    @Test
    public void save3Test(){
        // 开启sqlSession
        SqlSession sqlSession = sqlSessionFactory.openSession();
        // 根据接口执行sql
        UserMapper userMapper = sqlSession.getMapper(UserMapper.class);
        User user = new User().setName("lizhaoxuan3").setPass("admin1234").setCreateTime(new Date()).setUpdateTime(new Date());
        int effectRows = userMapper.save3(user);
        log.info("saveResult={},newId={}!", effectRows == 1, user.getId());
        // 提交事务
        sqlSession.commit();
        sqlSession.close();
    }
```

![image-20210923194900941](http://rocks526.top/lzx/image-20210923194900941.png)

> JDBC方式的keyProperty属性是获取id后回填的属性，keyColumn是获取数据库的列名，如果有多个自动生成的列，用逗号隔开即可。
>
> selectKey方式的keyColumn 、keyProperty 和上面useGeneratedKeys 的用法含义相同，这里的resultType 用于设置返回值类型。order 属性的设置和使用的数据库有关，在MySQL和Pg数据库中， order属性设置的值是AFTER ，因为当前记录的主键值在insert语句执行成功后才能获取到。而在Oracle 数据库中， order 的值要设置为BEFORE ，这是因为Oracle中需要先从序列获取值，然后将值作为主键插入到数据库中。

# 五：update用法

以一个根据ID更新用户信息为例：

```java
    // 根据id更新用户信息
    int updateById(User user);
```

```xml
    <!-- 简单更新，动态SQL后续章节会单独介绍 -->
    <update id="updateById">
        update public.user
            set name = #{name},pass=#{pass},update_time=#{updateTime}
        where id = #{id}
    </update>
```

```java
    @Test
    public void updateByIdTest(){
        // 开启sqlSession
        SqlSession sqlSession = sqlSessionFactory.openSession();
        // 根据接口执行sql
        UserMapper userMapper = sqlSession.getMapper(UserMapper.class);
        int effectRows = userMapper.updateById(new User().setId(1L).setName("SuperUser").setPass("admin").setUpdateTime(new Date()));
        log.info("updateResult={}!", effectRows == 1);
        // 提交事务
        sqlSession.commit();
        sqlSession.close();
    }
```

![image-20210924094107022](http://rocks526.top/lzx/image-20210924094107022.png)

# 六：delete用法

以一个根据ID删除用户为例：

```java
    // 根据ID删除用户
    int deleteById(Long userId);
```

```xml
    <delete id="deleteById">
        delete from public.user where id = #{userId}
    </delete>
```

```java
    @Test
    public void deleteByIdTest(){
        // 开启sqlSession
        SqlSession sqlSession = sqlSessionFactory.openSession();
        // 根据接口执行sql
        UserMapper userMapper = sqlSession.getMapper(UserMapper.class);
        int effectRows = userMapper.deleteById(18L);
        log.info("deleteResult={}!", effectRows == 1);
        // 提交事务
        sqlSession.commit();
        sqlSession.close();
    }
```

![image-20210924094442677](http://rocks526.top/lzx/image-20210924094442677.png)

# 七：接口多个参数用法

在前面的代码示例中，我们的接口都只有一个参数，如果存在多个参数，则使用了JavaBean的方式，此方式并不适用所有情况，例如只有2、3个参数时，新建一个JavaBean并不太合适。因此除了这个这种方式之外，还有其他两种方式：

- Map传递参数：key作为参数的名字，value就是参数的值，这种方式也需要构造Map，不是很方便
- @Param注解：通过这个注解，可以设置每个参数的名字

> 如果使用多个参数，不加@Param注解，每个参数的名字会被解析为0、1、param1、param2这种形式。

```java
    // 根据用户名称和创建时间查询
    List<User> selectByNameAndCreateTime(Map<String,Object> params);

    // 根据用户名称和创建时间查询
    List<User> selectByNameAndCreateTime2(@Param("name") String userName, @Param("createTime") Date createTime);
```

```xml
    <!-- &lt; ==>  小于号，xml里特殊字符需要转义 -->
    <select id="selectByNameAndCreateTime" resultType="User">
        select * from public.user where name like #{userName} and create_time &lt; #{createTime}
    </select>

    <select id="selectByNameAndCreateTime2" resultType="User">
        select * from public.user where name like #{name} and create_time &lt; #{createTime}
    </select>
```

```java
    @Test
    public void test(){
        // 开启sqlSession
        SqlSession sqlSession = sqlSessionFactory.openSession();
        // 根据接口执行sql
        UserMapper userMapper = sqlSession.getMapper(UserMapper.class);
        HashMap<String, Object> params = new HashMap<>();
        params.put("userName","%lizhaoxuan%");
        params.put("createTime",new Date());
        List<User> users = userMapper.selectByNameAndCreateTime(params);
        log.info("queryResult1={}!", users);
        List<User> users2 = userMapper.selectByNameAndCreateTime2("%lizhaoxuan%",new Date());
        log.info("queryResult2={}!", users2);
    }
```

![image-20210924102816248](http://rocks526.top/lzx/image-20210924102816248.png)

> XML中一些特殊字符需要转义，包括<、>等，具体如下：

![image-20210924113053804](C:\Users\lizhaoxuan\AppData\Roaming\Typora\typora-user-images\image-20210924113053804.png)

# 八：Mapper接口动态代理实现原理

```java
package com.lizhaoxuan.xml.proxy;

import org.apache.ibatis.session.SqlSession;
import java.lang.reflect.InvocationHandler;
import java.lang.reflect.Method;
import java.util.List;

/**
 * 动态代理示例
 * @author lizhaoxuan
 * @date 2021/9/24
 */
public class MapperProxy<T> implements InvocationHandler {

    private Class<T> mapperInterface;
    private SqlSession sqlSession;

    public MapperProxy(Class<T> mapperInterface, SqlSession sqlSession) {
        this.mapperInterface = mapperInterface;
        this.sqlSession = sqlSession;
    }

    @Override
    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
        // 只做简单示例，真实情况需要根据不同类型的method调用sqlSession不同的方法，返回值也需要做处理
        String flag = mapperInterface.getCanonicalName() + "." + method.getName();
        List<Object> list = sqlSession.selectList(flag);
        return list;
    }
}
```

```java
package com.lizhaoxuan.xml;

import com.lizhaoxuan.xml.entity.User;
import com.lizhaoxuan.xml.mapper.UserMapper;
import com.lizhaoxuan.xml.proxy.MapperProxy;
import lombok.SneakyThrows;
import lombok.extern.slf4j.Slf4j;
import org.apache.ibatis.io.Resources;
import org.apache.ibatis.session.SqlSession;
import org.apache.ibatis.session.SqlSessionFactory;
import org.apache.ibatis.session.SqlSessionFactoryBuilder;
import org.junit.jupiter.api.BeforeAll;
import org.junit.jupiter.api.Test;

import java.io.Reader;
import java.lang.reflect.Proxy;
import java.util.Date;
import java.util.HashMap;
import java.util.List;

/**
 * 动态代理测试
 */
@Slf4j
public class ProxyTest {

    public static SqlSessionFactory sqlSessionFactory;

    @SneakyThrows
    @BeforeAll
    public static void init(){
        // 加载配置文件
        Reader reader = Resources.getResourceAsReader("mybatis-config.xml");
        // 创建SqlSession工厂
        sqlSessionFactory = new SqlSessionFactoryBuilder().build(reader);
        reader.close();
    }

    @Test
    public void proxy(){
        // 开启sqlSession
        SqlSession sqlSession = sqlSessionFactory.openSession();
        // 获取处理器
        MapperProxy<UserMapper> handler = new MapperProxy<>(UserMapper.class, sqlSession);
        // 获取代理
        UserMapper userMapperProxy = (UserMapper) Proxy.newProxyInstance(Thread.currentThread().getContextClassLoader(), new Class[]{UserMapper.class}, handler);
        List<User> users = userMapperProxy.selectAll();
        log.info("result:{}",users);
    }

}
```

![image-20210924111951890](http://rocks526.top/lzx/image-20210924111951890.png)

根据上面示例，动态代理大概流程如下：

- 通过mapper接口的全限定名和方法名关联到xml文件的sql
- 根据sql的不同类型，调用不同的SqlSession方法
- 处理输入参数
- 执行SQL，获取结果
- 处理输出参数

> 这种代理方式和常规代理的不同之处在于，这里没有对某个具体类进行代理，而是通过代理转化成了对其他代码的调用。
>
> 由于方法参数和返回值存在很多种情况，因此MyBatis 的内部实现会比上面的逻辑复杂得多。













# 一：动态SQL介绍

MyBatis的强大特性之一便是它的动态SQL 。在业务开发中，经常存在根据用户输入条件进行检索之类的需求，当使用JDBC时，就需要根据前端入参拼接SQL，需要处理各种连接条件和逗号。MyBatis 的动态SQL则能让我们摆脱这种痛苦。

在MyBatis3 之前的版本中，使用动态SQL需要学习和了解非常多的标签，现在MyBatis采用了功能强大的OGNL ( Object-Graph Navigation Language ）表达式语言消除了许多其他标签，以下是MyBatis 的动态SQL 在XML中支持的几种标签：

- if
- choose (when 、oterwise)
- trim (where 、set)
- foreach
- bind

本章除了讲解这几种标签的用法外，还会介绍如何在一个XML中针对不同的数据库编写不同的SQL语句，另外会对这5种标签中必须要用到的OGNL表达式进行一个简单的介绍。

# 二：if用法

if 标签通常用于WHERE语句中，通过判断参数值来决定是否使用某个查询条件，它也经常用于UPDATE语句中判断是否更新某一个字段， 还可以在INSERT语句中用来判断是否插入某个字段的值。

### 2.1 where中使用if标签

- 需求：

根据用户输入的筛选条件进行查询：当输入ID时精确查询；当输入名称时模糊查询；当输入密码时模糊查询；当什么都不输入时，返回所有用户列表。

- if标签使用

```java
    /**
     * 根据条件检索
     *  当输入ID时精确查询；当输入名称时模糊查询；当输入密码时模糊查询；当什么都不输入时，返回所有用户列表。
     * @param user
     * @return
     */
    List<User> selectByCondition(User user);
```

```xml
    <select id="selectByCondition" resultType="User">
        select * from public.user
            where 1=1
        <if test="id != null and id > 0">
            and id = #{id}
        </if>
        <if test="name != null and name != ''">
            and name like concat('%', #{name}, '%')
        </if>
        <if test="pass != null and pass != ''">
            and pass like concat('%', #{pass}, '%')
        </if>
    </select>
```

```java
    @Test
    public void selectAllTest(){
        SqlSession sqlSession = sqlSessionFactory.openSession();
        UserMapper userMapper = sqlSession.getMapper(UserMapper.class);
        User condition = new User();
        // 查询全部
        List<User> users = userMapper.selectByCondition(condition);
        users.forEach(System.out::println);
        // 查询ID为1的
        List<User> users2 = userMapper.selectByCondition(condition.setId(1L));
        users2.forEach(System.out::println);
        // 查询name包含super的
        List<User> user3 = userMapper.selectByCondition(condition.setId(null).setName("super"));
        user3.forEach(System.out::println);
        // 查询name包含super、密码包含admin的
        List<User> user4 = userMapper.selectByCondition(condition.setPass("admin"));
        user4.forEach(System.out::println);
    }
```

![image-20210926181345856](http://rocks526.top/lzx/image-20210926181345856.png)

- if标签属性

if 标签有一个必填的属性test，test的属性值是一个符合OGNL要求的判断表达式，表达式的结果可以是true或false，除此之外所有的非0值都为true，只有0为false。为了方便理解，在表达式中，建议只用true或false作为结果。

1. 针对null的判断：`property!=null` 或 `property==null`，适用于任何类型的字段
2. 空串判断：`property!=''` 或 `property==''`，仅适用于字符串类型
3. 连接条件：当有多个判断条件时，使用and或or进行连接，嵌套的判断可以使用小括号分组
4. 注意and连接符，多个条件之间，if标签不会自动拼接and或者or
5. 注意where之后的1=1，当条件都不存在时，单独的where会报错，因此拼接1=1，这个可以通过where标签优化

### 2.2 update中使用if标签

- 需求

前端只传入更新的字段，未更新的字段不传值，后端需要判断非空，不能将未变化的字段更新成null。

- if标签的使用

```java
    // 根据字段更新
    int updateByCondition(User user);
```

```xml
    <update id="updateByCondition">
        update public.user set
        <if test="name != null and name != ''">
            name = #{name},
        </if>
        <if test="pass != null and pass != ''">
            pass = #{pass},
        </if>
        id = #{id}
        where id = #{id}
    </update>
```

```java
    @Test
    public void updateTest(){
        SqlSession sqlSession = sqlSessionFactory.openSession();
        UserMapper userMapper = sqlSession.getMapper(UserMapper.class);
        User condition = new User().setId(1L).setName("Super");
        int effectRows = userMapper.updateByCondition(condition);
        log.info("result={}!", effectRows == 1);
        condition.setPass("admin12345");
        int effectRows2 = userMapper.updateByCondition(condition);
        log.info("result={}!", effectRows2 == 1);
        sqlSession.commit();
    }
```

![image-20210926192300067](http://rocks526.top/lzx/image-20210926192300067.png)

- 需要注意每个set后面的逗号，后续可以通过set标签优化
- 需要注意最后拼接的id=#{id}，当所有条件都为null时，此语句保证SQL语法不会出错

### 2.3 insert中使用if标签

- 需求

插入的时候，如果前端传入参数则使用传入参数，没有传参数则不赋值，使用数据库默认值。

- if标签使用

```java
    // 根据字段保存
    int saveByCondition(User user);
```

```xml
    <insert id="saveByCondition">
        insert into public.user (name,pass
        <if test="createTime != null">
            ,create_time
        </if>
        <if test="updateTime != null">
            ,update_time
        </if>
        )  values (#{name}, #{pass}
        <if test="createTime != null">
            , #{createTime}
        </if>
        <if test="updateTime != null">
            , #{updateTime}
        </if>
        )
    </insert>
```

```java
    @Test
    public void saveTest(){
        SqlSession sqlSession = sqlSessionFactory.openSession();
        UserMapper userMapper = sqlSession.getMapper(UserMapper.class);
        User condition = new User().setName("Super").setPass("admin12345").setUpdateTime(new Date(1630425600000L));
        int effectRows = userMapper.saveByCondition(condition);
        log.info("result={}!", effectRows == 1);
        sqlSession.commit();
    }
```

![image-20210926193750155](http://rocks526.top/lzx/image-20210926193750155.png)

# 三：choose用法

if标签提供了基本的条件判断，但是它无法实现if...else if...else if....else....的逻辑，要想实现这样的逻辑，就需要用到choose、when、otherwise标签。`choose元素中包含when和otherwise两个标签，一个choose中至少有一个when ，有0个或者1个otherwise。`

- 需求

根据唯一标识查询用户：当输入ID时根据ID查询；没有ID时根据用户名查询；都没有时返回null。

```java
    // 根据id和name查询用户
    User selectByIdAndName(@Param("id")Long id, @Param("name") String name);
```

```xml
    <select id="selectByIdAndName" resultType="User">
        select * from public.user where
        <choose>
            <when test="id != null">
                id = #{id}
            </when>
            <when test="name != null and name != ''">
                name = #{name}
            </when>
            <otherwise>
                1=2
            </otherwise>
        </choose>
    </select>
```

```java
    @Test
    public void chooseTest(){
        SqlSession sqlSession = sqlSessionFactory.openSession();
        UserMapper userMapper = sqlSession.getMapper(UserMapper.class);
        User user = userMapper.selectByIdAndName(1L, "Super");
        log.info("result1={}",user);
        User user2 = userMapper.selectByIdAndName(null, "Super");
        log.info("result1={}",user2);
        User user3 = userMapper.selectByIdAndName(null, null);
        log.info("result1={}",user3);
    }
```

![image-20210926195104055](http://rocks526.top/lzx/image-20210926195104055.png)

# 四：where、set、trim用法

这3个标签解决了类似的问题，并且where和set都属于trim的一种具体用法。下面分别来看这3个标签。

### 4.1 where标签

where标签的作用：如果该标签包含的元素中有返回值，就插入一个where；如果where后面的字符串是以AND和OR开头的，就将它们剔除。

```xml
    <select id="selectByCondition" resultType="User">
        select * from public.user
            where 1=1
        <if test="id != null and id > 0">
            and id = #{id}
        </if>
        <if test="name != null and name != ''">
            and name like concat('%', #{name}, '%')
        </if>
        <if test="pass != null and pass != ''">
            and pass like concat('%', #{pass}, '%')
        </if>
    </select>
<!-- 使用where标签干掉1=1的逻辑 -->
    <select id="selectByCondition2" resultType="User">
        select * from public.user
        <where>
            <if test="id != null and id > 0">
                and id = #{id}
            </if>
            <if test="name != null and name != ''">
                and name like concat('%', #{name}, '%')
            </if>
            <if test="pass != null and pass != ''">
                and pass like concat('%', #{pass}, '%')
            </if>
        </where>
    </select>
```

- 当if条件都不满足的时候， where元素中没有内容，所以在SQL中不会出现where ，效果和1=1一样，返回所有用户。
- 当if条件满足，where元素的内容就是以and开头的条件，where会自动去掉开头的and，这也能保证where条件正确。
- 和手动拼接1=1避免SQL错误的情况，where标签更简洁。

### 4.2 set标签

set标签的作用：如果该标签包含的元素中有返回值，就插入一个set；如果set后面的字符串是以逗号结尾的，就将这个逗号剔除。

```xml
    <update id="updateByCondition">
        update public.user set
        <if test="name != null and name != ''">
            name = #{name},
        </if>
        <if test="pass != null and pass != ''">
            pass = #{pass},
        </if>
        id = #{id}
        where id = #{id}
    </update>
<!-- 使用set标签干掉逗号拼接 -->
<update id="updateByCondition2">
        update public.user
        <set>
            <if test="name != null and name != ''">
                name = #{name},
            </if>
            <if test="pass != null and pass != ''">
                pass = #{pass},
            </if>
            id = #{id},
        </set>
        where id = #{id}
    </update>
```

- 当set里的条件存在时会去掉最后一个逗号，因此每个拼接条件都可以加上逗号，会自动去掉
- 当set里的条件不存在时，会不加set导致SQL错误，因此还是需要id=#{id}的逻辑保证SQL不会出错
- 相比where标签，set并不是很完美，还是需要手动拼接id=#{id}这种无业务意义的语句

### 4.3 trim标签

where和set标签的功能都可以用trim标签来实现，并且在底层就是通过`TrimSqlNode`实现的。

```xml
<!-- where标签的trim实现如下 -->
<trim prefix="WHERE" prefixOverrides="AND | OR " > 
     <!-- AND和OR后面的空格不能省略，避免匹配到order之类的单词-->
    ......
</ trim>
<!-- set标签的trim实现如下 -->
<trim prefix="SET" suffixOverrides="," >
    ............
</ trim>
```

trim标签有如下属性：

- prefix：当trim元素内包含内容时，会给内容增加prefix指定的前缀。
- prefixOverrides：当trim元素内包含内容时，会把内容中匹配的前缀字符串去掉。
- suffix：当trim元素内包含内容时，会给内容增加suffix指定的后缀。
- suffixOverrides：当trim元素内包含内容时，会把内容中匹配的后缀字符串去掉。

# 五：foreach用法

SQL语句中有时会使用IN关键字，例如id in (1,2,3）。可以使用${ids}方式直接获取值，但这种写法不能防止SQL注入，想避免SQL 注入就需要用#{}的方式，这时就要配合使用foreach标签来满足需求。

foreach可以对数组、Map或实现了Iterable接口（如List、Set）的对象进行遍历。数组在处理时会转换为List对象，因此foreach遍历的对象可以分为两大类：Iterable类型和Map类型。

### 5.1 foreach实现in集合

foreach 实现in集合（或数组）是最简单和常用的一种情况，下面介绍如何根据传入的用户id集合查询出所有符合条件的用户。

```java
    // 根据用户ID列表查询
    List<User> selectByIds(List<Long> userIds);
```

```xml
    <select id="selectByIds" resultType="User">
        select * from public.user where id in 
        <foreach collection="list" open="(" close=")" separator="," item="id" index="i">
            #{id}
        </foreach>
    </select>
```

```java
    @Test
    public void foreachTest(){
        SqlSession sqlSession = sqlSessionFactory.openSession();
        UserMapper userMapper = sqlSession.getMapper(UserMapper.class);
        List<User> users = userMapper.selectByIds(Arrays.asList(1L, 2L, 3L));
        users.forEach(System.out::println);
    }
```

![image-20210926202255467](http://rocks526.top/lzx/image-20210926202255467.png)

foreach包含以下属性：

- collection：必填，值为要迭代循环的属性名，这个属性值的情况有很多。
- item：变量名，值为从迭代对象中取出的每一个值。
- index：索引的属性名，在集合数组情况下值为当前索引值，当选代循环的对象是Map类型时，这个值为Map的key。
- open：整个循环内容开头的字符串。
- close： 整个循环内容结尾的字符串。
- separator：每次循环的分隔符。

collection的属性要如何设置？ 需要区分以下几种情况：

- 当只有一个参数时
  - 当参数为Array数组时，collection的value为array
  - 当参数为List时，collection的value为list或collection
  - 当参数为其他实现了Collection的接口类时，collection的value为collection
- 当有多个参数时
  - 使用@Param注解设置集合对象的参数名，collection的值为注解的value
- 当参数是Map类型时
  - collection的值为_paramter，如果使用@Param注解，可以设置为注解的值
- 当参数是一个对象，集合是对象的一个属性时
  - 使用对象.属性名直接取值即可，如果多层嵌套，多层取值即可
  - 针对集合和数组类型，可以通过下标取值

### 5.2 foreach实现批量插入

```java
    // 批量插入
    int saveBatch(List<User> users);
```

```xml
    <insert id="saveBatch">
        insert into public.user (name,pass) values
        <foreach collection="list" item="user" separator=",">
            (#{user.name}, #{user.pass})
        </foreach>
    </insert>
```

```java
    @Test
    public void saveBatchTest(){
        SqlSession sqlSession = sqlSessionFactory.openSession();
        UserMapper userMapper = sqlSession.getMapper(UserMapper.class);
        userMapper.saveBatch(Arrays.asList(
                new User().setName("aaa1").setPass("aaa1"),
                new User().setName("aaa2").setPass("aaa2"),
                new User().setName("aaa3").setPass("aaa3"),
                new User().setName("aaa4").setPass("aaa4")
                ));
        sqlSession.commit();
    }
```

![image-20210926203757343](http://rocks526.top/lzx/image-20210926203757343.png)

- 获取批量插入的生成的主键ID

从MyBatis3.3.1版本开始，MyBatis开始支持批量新增回写主键值的功能，这个功能首先要求数据库主键值为自增类型，同时还要求该数据库提供的JDBC驱动可以支持返回批量插入的主键值，但并不是所有数据库都完美实现了该接口。

```xml
    <insert id="saveBatch" useGeneratedKeys="true" keyProperty="id,createTime,updateTime" keyColumn="id,create_time,update_time">
        insert into public.user (name,pass) values
        <foreach collection="list" item="user" separator=",">
            (#{user.name}, #{user.pass})
        </foreach>
    </insert>
```

```java
    @Test
    public void saveBatchTest(){
        SqlSession sqlSession = sqlSessionFactory.openSession();
        UserMapper userMapper = sqlSession.getMapper(UserMapper.class);
        List<User> users = Arrays.asList(
                new User().setName("aaa1").setPass("aaa1"),
                new User().setName("aaa2").setPass("aaa2"),
                new User().setName("aaa3").setPass("aaa3"),
                new User().setName("aaa4").setPass("aaa4")
        );
        userMapper.saveBatch(users);
        users.forEach(System.out::println);
        sqlSession.commit();
    }
```

![image-20210926204307137](http://rocks526.top/lzx/image-20210926204307137.png)

### 5.3 foreach实现动态更新

```java
    // 批量更新
    int updateBatch(@Param("fields") Map<String,Object> updateFields);
```

```xml
    <update id="updateBatch">
        update public.user set
        <foreach collection="fields" item="value" index="field" separator=",">
            ${field} = #{value}
        </foreach>
        where id = #{fields.id}
    </update>
```

```java
    @Test
    public void updateBatchTest(){
        SqlSession sqlSession = sqlSessionFactory.openSession();
        UserMapper userMapper = sqlSession.getMapper(UserMapper.class);
        HashMap<String, Object> params = new HashMap<>();
        // id既作为查询条件，又作为更新条件，必须存在
        params.put("id",1L);
        params.put("name","SUPER");
        params.put("update_time",new Date());
        int effectRows = userMapper.updateBatch(params);
        sqlSession.commit();
    }
```

![image-20210926205546296](http://rocks526.top/lzx/image-20210926205546296.png)

# 六：bind用法

bind标签可以使用OGNL表达式创建一个变量井将其绑定到上下文中。

```java
    // bind标签
    List<User> selectByCondition3(User user);
```

```xml
    <select id="selectByCondition3" resultType="User">
        select * from public.user
        where 1=1
        <if test="id != null and id > 0">
            and id = #{id}
        </if>
        <if test="name != null and name != ''">
            <bind name="nameLike" value="'%' + name + '%'"></bind>
            and name like #{nameLike}
        </if>
        <if test="pass != null and pass != ''">
            <bind name="passLike" value="'%' + pass + '%'"></bind>
            and pass like #{passLike}
        </if>
    </select>
```

```java
    @Test
    public void bindTest(){
        SqlSession sqlSession = sqlSessionFactory.openSession();
        UserMapper userMapper = sqlSession.getMapper(UserMapper.class);
        User condition = new User();
        // 查询全部
        List<User> users = userMapper.selectByCondition3(condition);
        users.forEach(System.out::println);
        // 查询ID为1的
        List<User> users2 = userMapper.selectByCondition3(condition.setId(1L));
        users2.forEach(System.out::println);
        // 查询name包含super的
        List<User> user3 = userMapper.selectByCondition3(condition.setId(null).setName("super"));
        user3.forEach(System.out::println);
        // 查询name包含super、密码包含admin的
        List<User> user4 = userMapper.selectByCondition3(condition.setPass("admin"));
        user4.forEach(System.out::println);
    }
```

![image-20210926210222863](http://rocks526.top/lzx/image-20210926210222863.png)

之前拼接百分号是通过数据库函数concat拼接，由于并非所有数据库都有concat函数，因此可以采用bind标签实现变量拼接，提升数据库兼容性。

# 七：多数据库支持

bind标签并不能解决更换数据库带来的所有问题，那么还可以通过什么方式支持不同的数据库呢？这需要用到if标签以及由MyBatis提供的databaseIdProvider数据库厂商标识配置。

MyBatis可以根据不同的数据库厂商执行不同的语句，这种多厂商的支持是基于映射语句中的databaseld属性的。MyBatis会加载不带databaseId属性和带有匹配当前数据库databaseld属性的所有语句。如果同时找到带有databaseId和不带databaseId的相同语句，则后者会被舍弃。为支持多厂商特性，只要像下面这样在mybatis-config.xml文件中加入databaseidProvider配置即可。

```xml
    <!-- 多数据库支持 -->
    <databaseIdProvider type="DB_VENDOR"/>
```

> 注意：此配置需要放在mapper标签之上，environments标签之下，否则会提示错误。

这里的DB_VENDOR会通过`DatabaseMetaData#getDatabaseProductName()`返回的字符串进行设置。由于通常情况下这个字符串都非常长而且相同产品的不同版本会返回不同的值，所以通常会通过设置属性别名来使其变短：

```xml
    <!-- 多数据库支持 -->
    <databaseIdProvider type="DB_VENDOR">
        <property name="SQL Server" value="sqlserver"/>
        <property name="DB2" value="db2"/>
        <property name="Oracle" value="oracle"/>
        <property name="MySQL" value="mysql"/>
        <property name="PostgreSQL" value="postgresql"/>
        <property name="Derby" value="derby"/>
        <property name="HSQL" value="hsqldb"/>
        <property name="H2" value="h2"/>
    </databaseIdProvider>
```

上面列举了常见的数据库产品名称，在有property配置时，databaseId将被设置为第一个能匹配数据库产品名称的属性键对应的值， 如果没有匹配的属性则会被设置为null。在这个例子中，如果getDatabaseProductName()返回`Microsoft SQL Server`，databaseId将被设置为sqlserver。

> DB_VENDOR的匹配策略为， DatabaseMetaData#getDatabaseProductName()返回的字符串包含property中name部分的值即可匹配，所以虽然SQL Server 的产品全名一般为Microsoft SQL Server，但这里只要设置为SQL Server就可以匹配。

除了增加上面的配置外，映射文件也是要变化的，关键在于以下几个映射文件的标签中含有的databaseId属性：

- select
- insert
- delete
- update
- selectKey
- sql

以SelectKey为例来介绍databaseId属性的使用：

```java
    // 新增
    int saveAndReturnId(User user);
```

```xml
    <insert id="saveAndReturnId"  databaseId="postgresql">
        insert into public.user (name,pass) values (#{name},#{pass})
        <selectKey keyColumn="id" keyProperty="id" resultType="long" order="AFTER">
            select currval('user_id_seq')
        </selectKey>
    </insert>

    <insert id="saveAndReturnId"  databaseId="mysql">
        insert into public.user (name,pass) values (#{name},#{pass})
        <selectKey keyColumn="id" keyProperty="id" resultType="long" order="AFTER">
            SELECT LAST_INSERT_ID()
        </selectKey>
    </insert>
```

```java
    @Test
    public void chooseTest(){
        SqlSession sqlSession = sqlSessionFactory.openSession();
        UserMapper userMapper = sqlSession.getMapper(UserMapper.class);
        User user = new User().setPass("admin222").setName("222333");
        int effectRows = userMapper.saveAndReturnId(user);
        System.out.println(user);
        sqlSession.commit();
    }
```

![image-20210926212239849](http://rocks526.top/lzx/image-20210926212239849.png)

> databaseId可以写多个SQL，也可以写在一个SQL的判断里。

# 八：OGNL用法

在MyBatis的动态SQL和${}形式的参数中都用到了OGNL表达式， 所以有必要了解一下OGNL的简单用法。MyBatis常用的OGNL表达式如下：

- e1 or e2
- e1 and e2
- e1 == e2 或e1 eq e2
- e1 != e2 或e1 neq e2
- e1 lt e2 ：小于
- e1 lte e2：小于等于，其他表示为gt（大于）、gte（大于等于）
- e1 + e2 、e1 * e2 、e1/e2 、e1 - e2 、e1%e2
- !e 或 not e：非，取反
- e.method(args）：调用对象方法
- e.property：对象属性值
- e1[e2]：按索引取值（List、数组和Map)
- @class@method(args）：调用类的静态方法
- @class@field：调用类的静态字段值

示例：

```xml
<if test="list ! =null and list.size() ＞0" ></if>
<if test="list ! =null and list[0] != ''" ></if>
<if test="map ! =null and map[name] != ''" ></if>
<if test="@org.apache.commons.lang3.StringUtils@isNotEmpty(name)" ></if>
```


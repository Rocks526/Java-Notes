1. 创建springboot项目

2. 引入mybatis和mysql依赖

```xml
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>org.mybatis.spring.boot</groupId>
            <artifactId>mybatis-spring-boot-starter</artifactId>
            <version>2.1.2</version>
        </dependency>

        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <scope>runtime</scope>
        </dependency>
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <optional>true</optional>
        </dependency>
```

3. 配置DataSource信息

```properties
# app
server.port=8080

# db
spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver
spring.datasource.url=jdbc:mysql://localhost:3306/msi?useUnicode=true&characterEncoding=utf-8&useSSL=false&serverTimezone=UTC
spring.datasource.username=root
spring.datasource.password=123
```

4. 启动类添加MapperScan注解，扫描mapper接口

```java
@SpringBootApplication
@MapperScan("com.rocks.springboot.springbootmybatismysql.dao")
public class SpringbootMybatisMysqlApplication {

    public static void main(String[] args) {
        SpringApplication.run(SpringbootMybatisMysqlApplication.class, args);
    }

}
```

5. 编写实体类

```java
package com.rocks.springboot.springbootmybatismysql.po;

import lombok.Data;

import java.util.Date;

/**
 * @Author Rocks526
 * @ClassName User
 * @Description 用户实体
 * @Date 2020/5/28 15:45
 **/
@Data
public class User {

    //ID
    private Long id;

    //用户名
    private String userName = "";

    //密码
    private String passWord = "";

    //电话
    private String telephone = "";

    //头像URL
    private String photo = "";

    //角色
    private Integer role = 1;

    private Date createTime = new Date();

    private Date updateTime = new Date();

    //昵称
    private String nickName = "";

    //用户状态 0正常 1冻结 2删除
    private Integer status = 0;

}

```

6. 编写dao接口，利用注解完成sql映射

```java
package com.rocks.springboot.springbootmybatismysql.dao;

import com.rocks.springboot.springbootmybatismysql.po.User;
import org.apache.ibatis.annotations.*;
import org.springframework.stereotype.Repository;

import java.util.List;

/**
 * @Author Rocks526
 * @ClassName UserDao
 * @Description UserDao
 * @Date 2020/5/28 15:43
 **/
@Repository
public interface UserDao {

    @Select("select * from user")
    @Results({
            @Result(id = true,property = "id",column = "id"),
            @Result(property = "userName",column = "user_name"),
            @Result(property = "passWord",column = "pass_word"),
            @Result(property = "createTime",column = "create_time"),
            @Result(property = "updateTime",column = "update_time"),
            @Result(property = "nickName",column = "nick_name")
    }
    )
    List<User> getUserList();

    @Select("select * from user where id = #{userId}")
    @Results({
            @Result(id = true,property = "id",column = "id"),
            @Result(property = "userName",column = "user_name"),
            @Result(property = "passWord",column = "pass_word"),
            @Result(property = "createTime",column = "create_time"),
            @Result(property = "updateTime",column = "update_time"),
            @Result(property = "nickName",column = "nick_name")
    }
    )
    User getUserInfoById(Long userId);

    @Select("select * from user where user_name = #{userName}")
    @Results({
            @Result(id = true,property = "id",column = "id"),
            @Result(property = "userName",column = "user_name"),
            @Result(property = "passWord",column = "pass_word"),
            @Result(property = "createTime",column = "create_time"),
            @Result(property = "updateTime",column = "update_time"),
            @Result(property = "nickName",column = "nick_name")
    }
    )
    User getUserInfoByUserName(Long userName);

    @Insert("insert into user (user_name,pass_word,telephone,photo,role,create_time,update_time,nick_name,status)values(#{userName},#{passWord},#{telephone},#{photo},#{role},#{createTime},#{updateTime},#{nickName},#{status})")
    void insert(User user);

    @Delete("delete from user where user_name = #{userName}")
    void deleteByUserName(String userName);

    @Delete("delete from user where id = #{userId}")
    void deleteByUserId(Long userId);

    @Update("update user set pass_word = #{passWord} where user_name = #{userName}")
    void updatePassByUserName(String passWord,String userName);

    @Update("update user set pass_word = #{passWord} where id = #{userId}")
    void updatePassByUserId(String passWord,Long userId);

    @Update("update user set status = #{status} where id = #{userId}")
    void updateUserStatusByUserId(Integer status,Long userId);

}

```

> 如果不使用注解模式，可编写xml文件，放在resource目录下，application配置文件里配置xml文件位置

7. 编写Service接口

```java
package com.rocks.springboot.springbootmybatismysql.service;

import com.rocks.springboot.springbootmybatismysql.po.User;

import java.util.List;

/**
 * @Author Rocks526
 * @ClassName UserService
 * @Description 用户服务接口
 * @Date 2020/5/28 15:57
 **/

public interface UserService {

    //获取所有用户
    List<User> getAllUser();

    //根据ID获取用户信息
    User getUserInfoById(Long userId);

    //根据用户名获取用户信息
    User getUserInfoByUserName(Long userName);

    //修改密码
    void updatePassWordByUserId(Long userId,String password);

    //修改密码
    void updatePassWordByUserName(String userName,String password);

    //新增用户
    void saveUser(User user);

    //删除用户
    void deleteUser(Long userId);

    //修改用户状态
    void updateUserStatus(Integer status,Long userId);


}
```

8. 编写Service实现类

```java
package com.rocks.springboot.springbootmybatismysql.service.Impl;

import com.rocks.springboot.springbootmybatismysql.dao.UserDao;
import com.rocks.springboot.springbootmybatismysql.po.User;
import com.rocks.springboot.springbootmybatismysql.service.UserService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

import java.util.List;

/**
 * @Author Rocks526
 * @ClassName UserServiceImpl
 * @Description 用户服务实现类
 * @Date 2020/5/28 16:01
 **/
@Service
public class UserServiceImpl implements UserService {

    @Autowired
    private UserDao userDao;

    @Override
    public List<User> getAllUser() {
        return userDao.getUserList();
    }

    @Override
    public User getUserInfoById(Long userId) {
        return userDao.getUserInfoById(userId);
    }

    @Override
    public User getUserInfoByUserName(Long userName) {
        return userDao.getUserInfoByUserName(userName);
    }

    @Override
    public void updatePassWordByUserId(Long userId, String password) {
        userDao.updatePassByUserId(password,userId);
    }

    @Override
    public void updatePassWordByUserName(String userName, String password) {
        userDao.updatePassByUserName(password,userName);
    }


    @Override
    public void saveUser(User user) {
        userDao.insert(user);
    }

    @Override
    public void deleteUser(Long userId) {
        userDao.deleteByUserId(userId);
    }

    @Override
    public void updateUserStatus(Integer status, Long userId) {
        userDao.updateUserStatusByUserId(status,userId);
    }
}

```

9. 编写全局响应Response

```java
package com.rocks.springboot.springbootmybatismysql.until;

import lombok.Data;

/**
 * @Author Rocks526
 * @ClassName Response
 * @Description 响应结果
 * @Date 2020/5/28 16:12
 **/
@Data
public class Response {

    private Integer code;

    private String msg;

    private Object data;

    public Response(Integer code,String msg,Object data){
        this.code = code;
        this.msg = msg;
        this.data = data;
    }

    public static Response success(){
        return new Response(200,"请求成功!",null);
    }

    public static Response success(Object data){
        return new Response(200,"请求成功!",data);
    }

    public static Response success(String msg){
        return new Response(200,msg,null);
    }

    public static Response success(Integer code,String msg,Object data){
        return new Response(code,msg,data);
    }

    public static Response success(String msg,Object data){
        return new Response(200,msg,data);
    }

    public static Response error(){
        return new Response(400,"请求失败!",null);
    }

    public static Response error(Object data){
        return new Response(400,"请求失败!",data);
    }

    public static Response error(String msg){
        return new Response(400,msg,null);
    }

    public static Response error(Integer code,String msg,Object data){
        return new Response(code,msg,data);
    }

    public static Response error(String msg,Object data){
        return new Response(400,msg,data);
    }



}
```

10. 编写Controller

```java
package com.rocks.springboot.springbootmybatismysql.controller;

import com.rocks.springboot.springbootmybatismysql.po.User;
import com.rocks.springboot.springbootmybatismysql.service.UserService;
import com.rocks.springboot.springbootmybatismysql.until.Response;
import lombok.extern.slf4j.Slf4j;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.*;

import java.util.List;

/**
 * @Author Rocks526
 * @ClassName UserController
 * @Description 用户控制器
 * @Date 2020/5/28 16:07
 **/
@Slf4j
@RestController
@RequestMapping("/user")
public class UserController {

    @Autowired
    private UserService userService;

    @RequestMapping(value = "",method = RequestMethod.GET)
    public Response getAllUser(){
        List<User> allUser = userService.getAllUser();
        return Response.success(allUser);
    }

    @RequestMapping(value = "/{id}",method = RequestMethod.GET)
    public Response getUserById(@PathVariable(value = "id",required = true) Long id){
        User userInfo = userService.getUserInfoById(id);
        return Response.success(userInfo);
    }

    @RequestMapping(value = "/{id}",method = RequestMethod.DELETE)
    public Response deleteUserById(@PathVariable(value = "id",required = true) Long id){
        userService.deleteUser(id);
        return Response.success("删除成功!");
    }

    @RequestMapping(value = "",method = RequestMethod.PUT)
    public Response saveUser(@RequestBody User user){
        userService.saveUser(user);
        return Response.success("用户添加成功!");
    }

    @RequestMapping(value = "",method = RequestMethod.POST)
    public Response updateUserStatus(@RequestParam(value = "id",required = true) Long userId,
                                     @RequestParam(value = "status",required = true)Integer status){
        userService.updateUserStatus(status,userId);
        return Response.success("用户状态更新成功!");
    }

}
```

11. 编写全局异常处理器

```java
package com.rocks.springboot.springbootmybatismysql.config;

import com.rocks.springboot.springbootmybatismysql.until.Response;
import lombok.extern.slf4j.Slf4j;
import org.springframework.web.bind.annotation.ControllerAdvice;
import org.springframework.web.bind.annotation.ExceptionHandler;
import org.springframework.web.bind.annotation.ResponseBody;

import javax.servlet.http.HttpServletRequest;

/**
 * @Author Rocks526
 * @ClassName GlobalExceptionHandler
 * @Description 异常处理器
 * @Date 2020/5/28 16:26
 **/
@Slf4j
@ControllerAdvice
public class GlobalExceptionHandler {

    @ExceptionHandler(Exception.class)
    @ResponseBody
    public Response exceptionHandler(HttpServletRequest request, Exception e){
        log.error("请求[{}]发生异常:{}",request.getRequestURL(),e.toString());
        return Response.error();
    }

}
```
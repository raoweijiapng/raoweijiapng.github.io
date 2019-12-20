---
title: SpringBoot项目(2)之用户注册开发和测试的使用
tags: java SpringBoot
categories: java
---

* TOC
{:toc}

## 用户注册
  
  > /user/register (post) --> UserController --> UserService --> UserMapper --> DB（user表）--> 返回json结果

  - 在子工程ydma-user的src/main/resources里添加application.properties文件

    ```
    server.port=7001

    spring.datasource.username=root
    spring.datasource.password=123456
    spring.datasource.url=jdbc:mysql://localhost:3306/ydma?useUnicode=true&characterEncoding=utf8
    spring.datasource.driverClassName=com.mysql.jdbc.Driver
    ```

  - 在子工程ydma-user的src/main/java的cn.xdl.ydma里添加启动类

    ```java
    package cn.xdl.ydma;

    import org.mybatis.spring.annotation.MapperScan;
    import org.springframework.boot.SpringApplication;
    import org.springframework.boot.autoconfigure.SpringBootApplication;

    @SpringBootApplication
    @MapperScan(basePackages="cn.xdl.ydma.dao")
    public class RunUserBoot {
      public static void main(String[] args) {
        SpringApplication.run(RunUserBoot.class, args);
      }
    }
    ```

  - 在子工程ydma-user的src/main/java的cn.xdl.ydma.entity里添加YdmaResult类

    ```java
    package cn.xdl.ydma.entity;

    import java.io.Serializable;

    public class YdmaResult implements Serializable{
      private int code;
      private String msg;
      private Object data;
      
      public int getCode() {
        return code;
      }
      public void setCode(int code) {
        this.code = code;
      }
      public String getMsg() {
        return msg;
      }
      public void setMsg(String msg) {
        this.msg = msg;
      }
      public Object getData() {
        return data;
      }
      public void setData(Object data) {
        this.data = data;
      }      
    }
    ```

  - 在子工程ydma-user的src/main/java的cn.xdl.ydma里添加service目录,并在此目录下添加UserService

    ```java
    package cn.xdl.ydma.service;

    import cn.xdl.ydma.entity.YdmaResult;

    public interface UserService {      
      public YdmaResult addUser(String name, String password);      
    }
    ```

  - 在子工程ydma-user的src/main/java的cn.xdl.ydma.service下添加UserServiceImpl

    ```java
    package cn.xdl.ydma.service;

    import java.util.Date;

    import org.springframework.beans.factory.annotation.Autowired;
    import org.springframework.stereotype.Service;

    import cn.xdl.ydma.dao.UserMapper;
    import cn.xdl.ydma.entity.User;
    import cn.xdl.ydma.entity.YdmaResult;

    @Service
    public class UserServiceImpl implements UserService {

      @Autowired
      private UserMapper userDao;
      
      public YdmaResult addUser(String name, String password) {
        YdmaResult result = new YdmaResult();
        //检查用户名是否被占用
        User user = userDao.selectByName(name);
        if(user != null) {//名字存在
          result.setCode(1);
          result.setMsg("用户名已存在");
          return result;
        }
        //添加用户
        user = new User();
        user.setName(name);
        user.setPassword(password);
        user.setRegtime(new Date());
        userDao.insertSelective(user);
        //返回结果
        result.setCode(0);
        result.setMsg("注册成功");
        return result;
      }
    }
    ```

  - 在子工程ydma-user的src/main/java的cn.xdl.ydma里添加web目录,并在此目录下添加Controller

    ```java
    package cn.xdl.ydma.web;

    import org.springframework.beans.factory.annotation.Autowired;
    import org.springframework.web.bind.annotation.PostMapping;
    import org.springframework.web.bind.annotation.RestController;

    import cn.xdl.ydma.entity.YdmaResult;
    import cn.xdl.ydma.service.UserService;

    @RestController
    public class UserController {
      
      @Autowired
      private UserService userService;

      @PostMapping("/user/register")
      public YdmaResult register(String name,String password) {
        return userService.addUser(name, password);
      }      
    }
    ```

  > 注意:此时要在MyBatis Generator生成的mapper上加上通用层的标注

## MVC流程测试

  - 在父工程下的pom.xml文件添加test依赖

    ```
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-test</artifactId>
        <version>2.1.2.RELEASE</version>
    </dependency>
    ```

  > MockMVC：发送请求，获取响应结果

  > MockMvcBuilders ： 创建MockMVC对象器

  > RequestBuilder ： 请求对象，可以模拟get、post、put、delete等

  > MockMvcRequestBuilders ： 创建请求对象器

  > MvcResult ： 返回结果对象

  - 在子工程ydma-user的src/test/java里添加TestUserController类

    ```java
    package test;

    import cn.xdl.ydma.RunUserBoot;
    import cn.xdl.ydma.web.UserController;
    import org.junit.Assert;
    import org.junit.Before;
    import org.junit.Test;
    import org.junit.runner.RunWith;
    import org.springframework.beans.factory.annotation.Autowired;
    import org.springframework.boot.test.context.SpringBootTest;
    import org.springframework.test.context.junit4.SpringRunner;
    import org.springframework.test.web.servlet.MockMvc;
    import org.springframework.test.web.servlet.MvcResult;
    import org.springframework.test.web.servlet.RequestBuilder;
    import org.springframework.test.web.servlet.request.MockMvcRequestBuilders;
    import org.springframework.test.web.servlet.setup.MockMvcBuilders;

    @RunWith(SpringRunner.class)//加载配置文件，创建Spring容器，进行对象注入
    @SpringBootTest(classes= {RunUserBoot.class})//指定RunUserBoot.class启动类
    public class TestUserController {
      
      @Autowired
      private UserController controller;
      
      MockMvc mock;
      
      @Before //在执行@Test方法前调用@Before方法，之后调用@Test，最后调用@After
      public void before() {
        //使用controller创建一个MockMvc对象
        mock = MockMvcBuilders.standaloneSetup(controller).build();
      }

      @Test
      public void test1() throws Exception {
        //使用mock对象发送请求，获取返回的结果
        RequestBuilder request = 
          MockMvcRequestBuilders.post("/user/register")
          .param("name", "xdl")
          .param("password", "123");
        MvcResult result = mock.perform(request).andReturn();
        //打印返回结果
        String content = result.getResponse().getContentAsString();
        System.out.println(content);
      }
      
      @Test
      public void test4() throws Exception {
        
        //使用mock对象发送请求，获取返回的结果
        RequestBuilder request = 
          MockMvcRequestBuilders.post("/user/register")
          .param("name", "xdl1")
          .param("password", "123");
        MvcResult result = mock.perform(request).andReturn();
        //打印返回结果
        String content = result.getResponse().getContentAsString();
        System.out.println(content);
      }
      
      @Test
      public void test2() {
        System.out.println("---test2----");
        String s = null;
        s.length();
      }

      @Test
      public void test3() {
        System.out.println("---test3----");
        String expected = "abc";
        Assert.assertEquals(expected, "abcd");
      }
    }
    ```



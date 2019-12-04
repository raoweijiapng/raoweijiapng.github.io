---
title: Java_SpringBoot(1)之分布式和微服务、SpringBoot的含义和初步使用
tags: java SpringBoot
categories: java
---

* TOC
{:toc}

## 分布式和微服务

  单体应用：系统界面、静态资源、业务组件等都会在一个工程里，将来发布需要全部发布到一台tomcat服务器上。

  分布式：将一个系统的界面、静态资源、业务处理进行拆分，发布到不同的服务器上，然后用户请求过来，调用多个服务器资源处理，给用户响应。

  微服务：属于开发分布式应用的架构思想，将大型系统的业务处理部分，进行微型化拆分，拆分出的业务可以独立部署，这些业务之间可以通过协议进行调用，从而实现一个完整业务处理。优点：提升系统的性能、扩展性。

  微服务架构技术主流有以下两种：

  1. SpringBoot+SpringCloud (国外，Pivotal)

  2. Dubbo+Zokeerper（国内，阿里）

![dubbo与cloud比较](https://raoweijiapng.github.io/static/img/java/dubbocloud.png)

## SpringBoot

  SpringBoot是对Spring框架及其相关技术进行的封装，可以简化Spring应用搭建和开发过程。（敏捷开发）

  SpringBoot优点：

  1. 去除了XML配置方式，完全采用Java配置方式

  2. 内置tomcat服务器，自动发布应用（main方法启动）

  3. 基于jar包方式发布程序

  4. 提供一系列的启动器（jar包集合），便于导入

  5. 采用properties或yml文件做配置

  6. 提供了自动配置机制，自动创建一些常用对象，例如JdbcTemplate、DataSource、DispatcherServlet等
  
##MAVEN

  MAVEN是一个项目构建工具，可以对创建、编译、打包、生成文档等过程进行管理。maven工程都有一个pom.xml文件，通过在pom.xml追加定义，实现jar包和工具的导入。

  MAVEN优点：一种标准化的方式构建项目，一个清晰的方式定义项目的组成，一个容易的方式发布项目的信息，以及一种简单的方式在多个项目中共享JARs。

![maven](https://raoweijiapng.github.io/static/img/java/maven.png)

## SpringBoot程序结构

  1. pom.xml导入boot启动器

    spring-boot-starter-parent //启动器父项，统一控制版本、jdk版本、编码等

    spring-boot-starter  //spring核心、boot自动配置、yaml解析等

    spring-boot-starter-jdbc //spring-jdbc、hikari

    spring-boot-starter-web  //springmvc、jackson、restful、tomcat

    spring-boot-starter-test //spring-test、junit

    spring-boot-starter-data-redis //spring-data-redis

    spring-boot-starter-data-jpa   //spring-data-jpa

    spring-boot-devtools //热启动，代码修改后自动重启

  ```xml
  <project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <groupId>cn.xdl</groupId>
    <artifactId>boot01</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <dependencies>
      <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
        <version>2.1.2.RELEASE</version>
      </dependency>
    </dependencies>
  </project>  
  ```

  2. 在src/main/resources里添加application.properties或者application.yml

    properties格式

      server.port=8888

    yml格式

      server:
        port: 8888

  提示：配置文件的key可以参考《SpringBoot2.x配置参数大全.docx》

  3. 在src/main/java下创建cn.xdl目录,并向里添加启动类RunXxxx

  ```java
    package cn.xdl;
    import org.springframework.boot.SpringApplication;
    import org.springframework.boot.autoconfigure.SpringBootApplication;
    import org.springframework.context.ApplicationContext;
    @SpringBootApplication
    public class RunBoot {
      public static void main(String[] args) {
        ApplicationContext ac = SpringApplication.run(RunBoot.class, args);
      }
    }
  ```

  4. 业务处理组件（Controller、Service、Dao等）

  注意：业务组件一定要放在启动类包下或子包下。

  > 在src/main/java/cn/xdl下创建web目录,并在此目录下创建HelloController.class

  ```java
    package cn.xdl.web;
    import org.springframework.web.bind.annotation.RequestMapping;
    import org.springframework.web.bind.annotation.RestController;
    //@Controller
    @RestController //等价于@Controller+@ResponseBody
    public class HelloController {
      @RequestMapping("/hello")
    //  @ResponseBody
      public String say() {
        return "Hello SpringBoot";
      }      
    }
  ```

## SpringBoot程序启动过程

  1. Boot程序启动入口为SpringApplication.run(RunBoot.class, args);

  2. 静态run方法内部创建了一个SpringApplication对象，并且执行该对象的run方法

  3. 创建SpringApplication对象时，做了一些初始化工作加载Initializers和Listeners组件（根据spring.factories定义加载）

  4. run方法内部会创建一个Spring容器对象ApplicationContext（根据底层Env环境不同创建不同的ApplicationContext）

  5. run方法内部还会加载启动Logo Banner信息

  6. run方法内部对ApplicationContext容器进行各项信息设置，例如bean对象(refreshContext)，环境参数(prepareContext)，启动Banner等

  7. 如果有tomcat等服务器启动服务器，发布程序执行
    


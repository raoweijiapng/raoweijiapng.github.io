---
title: eclipse使用SpringBoot+mybatis创建Maven项目
tags: Springboot eclipse mybatis
categories: eclipse
---

* TOC
{:toc}

## 准备工作

> 下载对应版本的jdk，tomact和maven，然后下载eclipse软件

> 打开eclipse，点击工具栏Windows-Preferences.

![eclipse配置1](https:\\raoweijiapng.github.io\static\img\eclipse\eclipse配置1.png)

> Java-Installed JREs-Add,然后Directory选择自己下载的jdk文件夹.

![eclipse配置2](https:\\raoweijiapng.github.io\static\img\eclipse\eclipse配置2.png)

> Server-Runtime Environments-Add，然后选择对应版本的tomact，Next，最后Browse，找到电脑里安装的tomact，然后Finish。

![eclipse配置3](https:\\raoweijiapng.github.io\static\img\eclipse\eclipse配置3.png)

> Maven-Installations-Add，然后Directory选择自己下载的maven文件夹。

![eclipse配置4](https:\\raoweijiapng.github.io\static\img\eclipse\eclipse配置4.png)

> Maven-User Settings，然后Browse选择自己下载的maven的setting文件,最好两个都选自己下载的。

![eclipse配置5](https:\\raoweijiapng.github.io\static\img\eclipse\eclipse配置5.png)

## 创建项目

> 打开eclipse，File-New-Maven Project,然后Next,选择选择webapp，Next,输入Group Id和Artifact Id，最后Finish

![maven配置1](https:\\raoweijiapng.github.io\static\img\eclipse\maven配置1.png)

> 新建的项目右键-Build Path-Configure Build Path,然后Libaries,如果如下图JRE环境不是自己的，最好也换成自己的,选择JRE System Library，Remove，然后Add Library，加上自己环境的jdk.

![maven配置2](https:\\raoweijiapng.github.io\static\img\eclipse\maven配置2.png)
![maven配置3](https:\\raoweijiapng.github.io\static\img\eclipse\maven配置3.png)
![maven配置4](https:\\raoweijiapng.github.io\static\img\eclipse\maven配置4.png)

> 在上述的Libaries里,点击add Library.选择Server Runtime,选择Apache Tomcat.

![maven配置5](https:\\raoweijiapng.github.io\static\img\eclipse\maven配置5.png)

> 最后Libaries如下图,然后Apply and Close.

![maven配置6](https:\\raoweijiapng.github.io\static\img\eclipse\maven配置6.png)

## 项目的配置

> 在项目的pom.xml文件中添加依赖

  ```
	<!-- Spring Boot的核心依赖,包括auto-configuration,logging和YAML -->
	<dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter</artifactId>
        <version>2.1.2.RELEASE</version>
    </dependency>
    <!-- web模块开发需要的相关jar包,springmvc、jackson、restful、tomcat -->
	<dependency>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-web</artifactId>
		<version>2.1.2.RELEASE</version>
	</dependency>
	<!-- Spring Boot热部署,代码修改后自启动 -->
	<dependency>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-devtools</artifactId>
		<version>2.1.2.RELEASE</version>
	</dependency>
    <!-- mysql数据库连接 -->
    <dependency>
        <groupId>mysql</groupId>
        <artifactId>mysql-connector-java</artifactId>
        <version>5.1.47</version>
    </dependency>
    <!-- mybatis访问数据库 -->
    <dependency>
      <groupId>org.mybatis.spring.boot</groupId>
      <artifactId>mybatis-spring-boot-starter</artifactId>
      <version>1.3.2</version>
  	</dependency>
  	<!-- 访问JSP页面必须配置 -->
  	<dependency>
        <groupId>org.apache.tomcat.embed</groupId>
        <artifactId>tomcat-embed-jasper</artifactId>
        <version>9.0.14</version>
    </dependency>
    <!-- 阿里的json数据依赖 -->
    <dependency>
	     <groupId>com.alibaba</groupId>
	     <artifactId>fastjson</artifactId>
	     <version>1.2.47</version>
	</dependency>
	<!-- 使用jstl标签库的依赖 -->
	<dependency>
        <groupId>jstl</groupId>
        <artifactId>jstl</artifactId>
        <version>1.2</version>
    </dependency>

    <!-- 支持jdbc连接 -->
	<dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-jdbc</artifactId>
        <version>2.1.2.RELEASE</version>
    </dependency>
	<!-- 支持Hibernate的Spring JPA -->
	<dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-jpa</artifactId>
        <version>2.1.2.RELEASE</version>
    </dependency>
	<!-- 支持mongodb -->
	<dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-mongodb</artifactId>
        <version>2.1.2.RELEASE</version>
    </dependency>
    <!-- 支持redis缓存 -->
	<dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-redis</artifactId>
        <version>2.1.2.RELEASE</version>
    </dependency>		
  ```

> 在src/main/resources下新建配置文件application.properties

  ```
  	spring.datasource.username=root
	spring.datasource.password=123456
	spring.datasource.url=jdbc:mysql://localhost:3306/easyui?useSSL=true&characterEncoding=UTF-8
	spring.datasource.driverClassName=com.mysql.jdbc.Driver

	#src/main/webapp
	spring.mvc.view.prefix=/WEB-INF/
	spring.mvc.view.suffix=.jsp

	mybatis.mapperLocations=classpath:sql/*.xml
  ```

> 在src/main/java创建com.mst包,在包内创建启动类

  ```
  	package com.mst;

	import org.mybatis.spring.annotation.MapperScan;
	import org.springframework.boot.SpringApplication;
	import org.springframework.boot.autoconfigure.SpringBootApplication;

	@MapperScan("com.mst.dao")
	@SpringBootApplication
	public class RunBoot{
	   public static void main(String[] args) throws Exception {
	      SpringApplication.run(RunBoot.class, args);
	   }
	}
  ```

> 在src/main/java创建com.mst.dao包,在包内创建Dao接口

> 在src/main/java创建com.mst.entity包,在包内创建实体类

> 在src/main/java创建com.mst.service包,在包内创建service接口和接口的实现类

> 在src/main/java创建com.mst.web包,在包内创建controller类

> 在src/main/resources创建sql文件夹,在文件夹内创建mapper的xml文件

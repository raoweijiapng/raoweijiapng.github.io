---
title: SpringBoot项目(1)之项目的相关结构和maven的使用
tags: java SpringBoot
categories: java
---

* TOC
{:toc}

## 项目内容

  - 在线学习平台，[模板](www.ydma.cn)

    > 主要功能：课程浏览、课程视频列表、查看学习视频、笔记、评价、登录、注册、个人信息管理、问答。

  - 项目结构

    > 单体应用：将业务逻辑和界面等做成一个project，一个war包发布。

    > 微服架构：将一个系统拆分成若干个小系统，每个小系统都可以独立部署，他们之间还可以通过HTTP协议交互。

  - 主要技术

    > SpringBoot ： 快速开发rest服务

    > MyBatis：数据库访问操作

    > SpringCloud : 服务管理

    > Redis : 缓存

    > Thymeleaf ： 页面模板

    > Ajax : 页面和服务交互

    > Red5 ： 流媒体服务器

    > 其他第三方服务：支付、短信、二维码等等

## Maven使用

### Maven作用域

  > jar包在pom.xml引入时，通过scope指定jar包使用范围。

  ```
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-devtools</artifactId>
        <version>2.1.2.RELEASE</version>
        <scope>compile</scope>
    </dependency>
  ```

  - compile(默认)

    > 编码、编译、测试、运行、发布时jar包都有效

  - provided

    > 编码、编译、测试、运行时jar包都有效，例如servlet等

  - runtime

    > 运行时jar包有效

  - test

    > 测试时jar包有效，例如junit、xxx-test

### Maven依赖传递

  > A包--》依赖B包  C包--》依赖A包

  > 引入c包时,也会引入A包和B包

###Maven依赖排除
  
  > 上述C想引入A包，但是不想引入B包，需要使用依赖排除。

  ```
    <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-jdbc</artifactId>
      <version>2.1.2.RELEASE</version>
      <!-- 
      <exclusions>
        <exclusion>
          <groupId>com.zaxxer</groupId>
          <artifactId>HikariCP</artifactId>
        </exclusion>
      </exclusions>
       -->
    </dependency>
  ```

### Maven继承

  > 继承parent指定的pom.xml定义

  ```
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.1.2.RELEASE</version>
    </parent>
  ```

### Maven聚合

  > 用一个聚合工程管理其他多个project，对聚合工程编译、打包会依次对包含的多个project做编译和打包。

  ```
    <modules>
      <module>boot03-jdbc</module>
      <module>boot03-mybatis</module>
      <module>boot03-jpa</module>
    </modules>
  ```

### Maven常用命令

  > mvn archetype:generate 创建Maven项目

  > mvn compile 编译源代码

  > mvn deploy 发布项目

  > mvn test 运行应用程序中的单元测试

  > mvn clean 清除项目目录中的生成结果

  > mvn package 根据项目生成的jar

  > mvn install 在本地Repository中安装jar

  > mvntomcat:run 启动tomcat服务

## 搭建ydma项目结构

  > ydma : 聚合工程、父工程（project,pom类型）

  >> ydma-user : 用户服务模块(moudle,jar类型)

  >> ydma-course : 课程服务模块(moudle,jar类型)

  >> ydma-video : 视频服务模块(moudle,jar类型)

  >> ydma-note : 笔记服务模块(moudle,jar类型)

  >> ydma-evaluation : 评价服务模块(moudle,jar类型)

  >> ydma-issue : 问题服务模块(moudle,jar类型)

  >> ydma-direction : 目录服务模块(moudle,jar类型)

  >> ydma-web : 页面工程

  >> ydma-cloud-eureka : SpringCloud服务管理系统

  >> yamd-cloud-zuul : SpringCloud网关系统

## MyBatis Generator工具

  - 在ydma的pom.xml引入mybatis-generator-core包

    ```java
      <dependency>
        <groupId>org.mybatis.generator</groupId>
        <artifactId>mybatis-generator-core</artifactId>
        <version>1.3.7</version>
      </dependency>
    ```

  - 在子工程下的src/main/resources定义mbg.xml配置文件

    ```
      <?xml version="1.0" encoding="UTF-8"?>
      <!DOCTYPE generatorConfiguration
        PUBLIC "-//mybatis.org//DTD MyBatis Generator Configuration 1.0//EN"
        "http://mybatis.org/dtd/mybatis-generator-config_1_0.dtd">

      <generatorConfiguration>

      <!-- 指定数据库驱动包 -->
        <classPathEntry location="C:\Users\raowe\.m2\repository\mysql\mysql-connector-java\5.1.47\mysql-connector-java-5.1.47.jar" />

        <context id="DB2Tables" targetRuntime="MyBatis3">
        
       <!-- 指定数据库连接参数 -->
          <jdbcConnection driverClass="com.mysql.jdbc.Driver"
              connectionURL="jdbc:mysql://localhost:3306/ydma?useSSL=false"
              userId="root"
              password="123456">
          </jdbcConnection>

          <javaTypeResolver >
            <property name="forceBigDecimals" value="false" />
          </javaTypeResolver>

      <!-- 指定实体类 -->
          <javaModelGenerator targetPackage="cn.xdl.ydma.entity" targetProject="C:\Users\raowe\IdeaProjects\ydma-test\ydma\ydma-user\src\main\java\">
            <property name="enableSubPackages" value="true" />
            <property name="trimStrings" value="true" />
          </javaModelGenerator>

      <!-- 指定SQL定义文件XML -->
          <sqlMapGenerator targetPackage="test.xml"  targetProject="C:\Users\raowe\IdeaProjects\ydma-test\ydma\ydma-user\src\main\resources\">
            <property name="enableSubPackages" value="true" />
          </sqlMapGenerator>

      <!-- 指定Mapper映射接口 -->
          <javaClientGenerator type="ANNOTATEDMAPPER" targetPackage="cn.xdl.ydma.dao"  targetProject="C:\Users\raowe\IdeaProjects\ydma-test\ydma\ydma-user\src\main\java\">
            <property name="enableSubPackages" value="true" />
          </javaClientGenerator>

      <!-- 指定根据哪些表生成实体类、SQL定义、Mapper映射器接口 -->
          <table tableName="user" domainObjectName="User" enableSelectByExample="false"
            enableDeleteByExample="false" enableCountByExample="false"
            enableUpdateByExample="false" >
          </table>

        </context>
      </generatorConfiguration>
    ```

  - 在在子工程下的src/main/java定义RunMBG启动类

    ```java
      public class RunMBG {
        public static void main(String[] args) throws Exception{
          List<String> warnings = new ArrayList<String>();
          boolean overwrite = true;
          File configFile = new File("ydma/ydma-user/src/main/resources/mbg.xml");
          ConfigurationParser cp = new ConfigurationParser(warnings);
          Configuration config = cp.parseConfiguration(configFile);
          DefaultShellCallback callback = new DefaultShellCallback(overwrite);
          MyBatisGenerator myBatisGenerator = new MyBatisGenerator(config, callback, warnings);
          myBatisGenerator.generate(null);
        }      
      }
    ```

## 项目的数据库结构

![猿代码项目的数据库结构](https://raoweijiapng.github.io/static/img/java/er.png)



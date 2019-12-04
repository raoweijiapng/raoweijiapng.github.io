---
title: Java_SpringBoot(3)之JdbcTemplate和Mabtis的初步使用
tags: java SpringBoot
categories: java
---

* TOC
{:toc}

## SpringBoot JdbcTemplate

  Spring DAO：JdbcTemplate、JdbcDaoSupport、JDBC事务（AOP）、异常处理DataAccessException

  SpringBoot中提供JdbcTemplateAutoConfiguration自动配置组件，自动创建一个JdbcTemplate对象。

  - 利用自动配置创建连接池对象(参考以前过程)

    > 创建Maven项目,在pom.xml文件中导入dependecy

    ```xml
      <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter</artifactId>
            <version>2.1.2.RELEASE</version>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-jdbc</artifactId>
            <version>2.1.2.RELEASE</version>
        </dependency>

        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>5.1.47</version>
        </dependency>

        <dependency>
            <groupId>com.alibaba</groupId>
            <artifactId>druid</artifactId>
            <version>1.0.9</version>
        </dependency>

      </dependencies>
    ```

    > 在src/main/resources下创建application.properties文件并输入

    ```
      spring.datasource.username=root
      spring.datasource.password=123456
      spring.datasource.url=jdbc:mysql://localhost:3306/mysql
      spring.datasource.driverClassName=com.mysql.jdbc.Driver
      spring.datasource.type=com.alibaba.druid.pool.DruidDataSource
    ```
  
  - 根据表编写实体类

    > 在src/main/java下创建com.xdl.entity目录,然后创建实体类News

    ```java
      package cn.xdl.entity;
      import java.io.Serializable;
      import java.sql.Timestamp;
      public class News implements Serializable{        
        private int id;
        private String title;
        private String content;
        private Timestamp release_time;
        private String sticky;
        public int getId() {
          return id;
        }
        public void setId(int id) {
          this.id = id;
        }
        public String getTitle() {
          return title;
        }
        public void setTitle(String title) {
          this.title = title;
        }
        public String getContent() {
          return content;
        }
        public void setContent(String content) {
          this.content = content;
        }
        public Timestamp getRelease_time() {
          return release_time;
        }
        public void setRelease_time(Timestamp release_time) {
          this.release_time = release_time;
        }
        public String getSticky() {
          return sticky;
        }
        public void setSticky(String sticky) {
          this.sticky = sticky;
        }  
      }
    ```

  - 定义Dao接口

    > 在src/main/java下创建com.xdl.dao目录,然后创建接口NewDao

    ```java
      package cn.xdl.dao;
      import cn.xdl.entity.News;
      import java.util.List;
      public interface NewDao {        
        public void save(News news);
        
        public List<News> findAll();
        
        public List<News> findPage(int page, int size);
      }
    ```

  - 定义Dao的实现类，直接注入JdbcTemplate对象使用

    ```java
      package cn.xdl.dao;
      import cn.xdl.entity.News;
      import org.springframework.beans.factory.annotation.Autowired;
      import org.springframework.jdbc.core.BeanPropertyRowMapper;
      import org.springframework.jdbc.core.JdbcTemplate;
      import org.springframework.jdbc.core.RowMapper;
      import org.springframework.stereotype.Repository;
      import java.util.List;
      @Repository
      public class JdbcNewDao implements NewDao{
        @Autowired
        private JdbcTemplate template;
        
        public void save(News news) {
          String sql = "insert into news (title,content,release_time,sticky) values (?,?,?,?)";
          Object[] args = {
            news.getTitle(),
            news.getContent(),
            news.getRelease_time(),
            news.getSticky()
          };
          template.update(sql, args);
        }

        public List<News> findAll() {
          String sql = "select * from news";
          //记录字段名和实体对象属性名一致才能使用BeanPropertyRowMapper
          RowMapper<News> rowMapper = new BeanPropertyRowMapper<News>(News.class);
          return template.query(sql, rowMapper);
        }

        public List<News> findPage(int page, int size) {
          String sql = "select * from news limit ?,?";
          int begin = (page-1)*size;
          Object[] args = {begin,size};
          RowMapper<News> rowMapper = new BeanPropertyRowMapper<News>(News.class);
          return template.query(sql,args,rowMapper);
        }
      }
    ```

  - 创建启动类,并进行测试

  ```java
    package cn.xdl;
    import cn.xdl.dao.NewDao;
    import cn.xdl.entity.News;
    import com.alibaba.druid.pool.DruidDataSource;
    import org.springframework.boot.SpringApplication;
    import org.springframework.boot.autoconfigure.SpringBootApplication;
    import org.springframework.boot.context.properties.ConfigurationProperties;
    import org.springframework.context.ApplicationContext;
    import org.springframework.context.annotation.Bean;
    import org.springframework.context.annotation.Primary;
    import javax.sql.DataSource;
    import java.util.List;
    @SpringBootApplication
    public class RunBoot {
      public static void main(String[] args) {
        ApplicationContext ac = SpringApplication.run(RunBoot.class, args);
        DataSource ds = ac.getBean("druid",DataSource.class);
        System.out.println(ds);
        NewDao newDao = ac.getBean(NewDao.class);
        List<News> list = newDao.findPage(1, 3);
        for(News n : list) {
          System.out.println(n.getId()+" "+n.getTitle());
        }
      }

      @Bean()
      @Primary
      @ConfigurationProperties(prefix="spring.datasource")
      public DataSource druid() {
        DruidDataSource druid = new DruidDataSource();
        return druid;
      }

      @Bean()
      @ConfigurationProperties(prefix="spring.datasource1")
      public DataSource druid2() {
        DruidDataSource druid = new DruidDataSource();
        return druid;
      }
    }
  ```

## SpringBoot MyBatis

  SpringBoot整合MyBatis，提供一些自动配置组件，例如MybatisAutoConfiguration,可以自动创建出SqlSessionFactoryBean对象。（mybatis-spring-boot-starter启动器引入）。

  - 利用自动配置创建连接池对象（参考以前过程）

    > 在上述SpringBoot JdbcTemplate的pom.xml文件的基础上加入

    ```xml
      <dependency>
          <groupId>org.mybatis.spring.boot</groupId>
          <artifactId>mybatis-spring-boot-starter</artifactId>
          <version>1.3.2</version>
      </dependency>
    ```
  
  - 根据表编写实体类,同上

  - 定义SQL语句（XML文件，配置mybatis.mapperLocations参数）

    > 在上述的application.properties文件中输入

    ```
      mybatis.mapperLocations=classpath:sql/*.xml
    ```

    > 在resources目录下创建sql目录,并在此目录下创建NewsMapper.xml,然后输入以下

    ```xml
      <?xml version="1.0" encoding="UTF-8" ?>  
      <!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" 
      "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
      <mapper namespace="cn.xdl.dao.NewDao">

        <select id="findAll" resultType="cn.xdl.entity.News">
        select * from news
        </select>
        
        <select id="findPage" resultType="cn.xdl.entity.News">
        select * from news limit #{begin},#{size}
        </select>

      </mapper>
    ```

  - 定义Mapper映射器接口（与XML文件SQL映射，配置@MapperScan）

    > 把上述的接口NewDao修改为以下内容

    ```java
      import java.util.List;
      import org.apache.ibatis.annotations.Param;
      import cn.xdl.entity.News;
      public interface NewDao {
        public List<News> findAll();  
        //#{begin} #{size}
        public List<News> findPage(@Param("begin")int begin,@Param("size")int size);        
      }
    ```
  
    > 把上述的启动类修改为以下内容

    ```java
      package cn.xdl;
      import cn.xdl.dao.NewDao;
      import cn.xdl.entity.News;
      import org.mybatis.spring.annotation.MapperScan;
      import org.springframework.boot.SpringApplication;
      import org.springframework.boot.autoconfigure.SpringBootApplication;
      import org.springframework.context.ApplicationContext;
      import java.util.List;
      @SpringBootApplication
      @MapperScan(basePackages="cn.xdl.dao")//扫描Mapper接口
      public class RunBoot {
        public static void main(String[] args) {
          ApplicationContext ac = SpringApplication.run(RunBoot.class, args);
          NewDao newDao = ac.getBean(NewDao.class);
          //基于sql分页
          int page = 1;
          int begin = (page-1)*3;
          List<News> list = newDao.findPage(begin, 3);
          for(News n:list) {
            System.out.println(n.getId()+" "+n.getTitle());
          }
        }
      }
    ```



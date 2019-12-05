---
title: Java_SpringBoot(5)之JPA的使用以及扩展
tags: java SpringBoot
categories: java
---

* TOC
{:toc}

## SpringBoot JPA

  Java Persistent API (Java持久化API)，Java标准，Hibernate框架是该标准典型实现。

  JPA是用于对对象操作完成对DB操作。底层都是对JDBC技术的封装。

  Spring Data模块功能提供了对JPA支持。在SpringBoot导入spring-boot-starter-data-jpa启动器。

  - 利用自动配置创建连接池对象（在JdbcTemplate过程的pom文件上导入)

    ```xml
      <dependency>
          <groupId>org.springframework.boot</groupId>
          <artifactId>spring-boot-starter-data-jpa</artifactId>
          <version>2.1.2.RELEASE</version>
      </dependency>
    ```

  - 根据表定义实体类（需要定义与表的映射关系）

    ```java
      package cn.xdl.entity;
      import javax.persistence.Column;
      import javax.persistence.Entity;
      import javax.persistence.Id;
      import javax.persistence.Table;
      import java.io.Serializable;
      @Entity
      @Table(name="knowledge")
      public class Knowledge implements Serializable{
          @Id
          @Column(name="id")
          private Integer id;

          @Column(name="name")
          private String name;

          @Column(name="subject_id")
          private Integer subject_id;

          @Column(name="chapter_id")
          private Integer chapter_id;

          public String getName() {
              return name;
          }
          public void setName(String name) {
              this.name = name;
          }
          public Integer getId() {
              return id;
          }
          public void setId(Integer id) {
              this.id = id;
          }
          public Integer getSubject_id() {
              return subject_id;
          }
          public void setSubject_id(Integer subject_id) {
              this.subject_id = subject_id;
          }
          public Integer getChapter_id() {
              return chapter_id;
          }
          public void setChapter_id(Integer chapter_id) {
              this.chapter_id = chapter_id;
          }
      }
    ```

  - 定义JPA Respository(Dao)接口,继承JpaRepository、CrudRepository等

  ```java
    package cn.xdl.dao;
    import cn.xdl.entity.Knowledge;
    import org.springframework.data.jpa.repository.JpaRepository;
    public interface KnowledgeDao extends JpaRepository<Knowledge, Integer>{

    }
  ```

  - 定义启动类（@SpringBootApplication）

    ```java
      package cn.xdl;
      import cn.xdl.dao.KnowledgeDao;
      import cn.xdl.entity.Knowledge;
      import org.springframework.boot.SpringApplication;
      import org.springframework.boot.autoconfigure.SpringBootApplication;
      import org.springframework.context.ApplicationContext;
      import org.springframework.data.domain.Page;
      import org.springframework.data.domain.PageRequest;
      import org.springframework.data.domain.Pageable;
      import org.springframework.data.domain.Sort;
      import java.util.List;
      @SpringBootApplication
      public class RunBoot {
        public static void main(String[] args) {
          ApplicationContext ac = SpringApplication.run(RunBoot.class, args);
          KnowledgeDao dao1 = ac.getBean(KnowledgeDao.class);
          List<Knowledge> list = dao1.findAll();
          list.forEach(know -> System.out.println(know.getId()+" "+know.getName()));

          //分页
          Sort sort = new Sort(Sort.Direction.ASC, "id");
          Pageable pageable = PageRequest.of(0, 3, sort);//page用索引从0开始
          Page<Knowledge> page = dao1.findAll(pageable);
          System.out.println("总页数:"+page.getTotalPages());
          List<Knowledge> list1 = page.getContent();
          list1.forEach(know -> System.out.println(know.getId()+" "+know.getName()));
        }
      }
    ```

## SpringBoot JPA扩展

![jpa-method](https://raoweijiapng.github.io/static/img/java/jpa-method.png)

  - 按规则定义方法名

    > 在上述的KnowledgeDao添加定义方法

    ```java
      import java.util.List;
      public interface KnowledgeDao extends JpaRepository<Knowledge, Integer>{

        public List<Knowledge> findByIdGreaterThan(int id);

        public List<Knowledge> findByNameLike(String name);

        public List<Knowledge> findBySubjectId(int subject_id);
        
      }
    ```

    > 在上述的启动类进行修改

    ```java
      package cn.xdl;
      import cn.xdl.dao.KnowledgeDao;
      import cn.xdl.entity.Knowledge;
      import org.springframework.boot.SpringApplication;
      import org.springframework.boot.autoconfigure.SpringBootApplication;
      import org.springframework.context.ApplicationContext;
      import java.util.List;
      @SpringBootApplication
      public class RunBoot {
        public static void main(String[] args) {
          ApplicationContext ac = SpringApplication.run(RunBoot.class, args);
          KnowledgeDao dao1 = ac.getBean(KnowledgeDao.class);
          //扩展操作
          List<Knowledge> list = dao1.findByIdGreaterThan(2);
          List<Knowledge> list1 = dao1.findByNameLike("%什么%");
          List<Knowledge> list2 = dao1.findBySubjectId(1);
          list.forEach(know -> System.out.println(know.getId()+" "+know.getName()));
          list1.forEach(know -> System.out.println(know.getId()+" "+know.getName()));
          list2.forEach(know -> System.out.println(know.getId()+" "+know.getName()));
        }
      }
    ```

  - 定义SQL语句

    > 在上述的KnowledgeDao添加定义方法

    ```java
      package cn.xdl.dao;
      import cn.xdl.entity.Knowledge;
      import org.springframework.data.jpa.repository.JpaRepository;
      import org.springframework.data.jpa.repository.Query;
      import org.springframework.data.repository.query.Param;
      import java.util.List;
      public interface KnowledgeDao extends JpaRepository<Knowledge, Integer>{
        @Query(nativeQuery=true,value="select * from knowledge where id>=:id")
        public List<Knowledge> findBySQL(@Param("id")int no);        
      }
    ```

    > 在上述的启动类进行修改

    ```java
      package cn.xdl;
      import cn.xdl.dao.KnowledgeDao;
      import cn.xdl.entity.Knowledge;
      import org.springframework.boot.SpringApplication;
      import org.springframework.boot.autoconfigure.SpringBootApplication;
      import org.springframework.context.ApplicationContext;
      import java.util.List;
      @SpringBootApplication
      public class RunBoot {
        public static void main(String[] args) {
          ApplicationContext ac = SpringApplication.run(RunBoot.class, args);
          KnowledgeDao dao1 = ac.getBean(KnowledgeDao.class);
          //扩展操作
          List<Knowledge> list = dao1.findBySQL(2);
          list.forEach(know -> System.out.println(know.getId()+" "+know.getName()));
        }
      }
    ```

  - 定义JPQL面向对象语句

    > 在上述的KnowledgeDao添加定义方法

    ```java
      package cn.xdl.dao;
      import cn.xdl.entity.Knowledge;
      import org.springframework.data.jpa.repository.JpaRepository;
      import org.springframework.data.jpa.repository.Query;
      import org.springframework.data.repository.query.Param;
      import java.util.List;
      public interface KnowledgeDao extends JpaRepository<Knowledge, Integer>{
        //其中的Knowledge是类名
        @Query("from Knowledge where id>=:id")
        public List<Knowledge> findByJPQL(@Param("id")int no);      
      }
    ```

    > 在上述的启动类进行修改

    ```java
      package cn.xdl;
      import cn.xdl.dao.KnowledgeDao;
      import cn.xdl.entity.Knowledge;
      import org.springframework.boot.SpringApplication;
      import org.springframework.boot.autoconfigure.SpringBootApplication;
      import org.springframework.context.ApplicationContext;
      import java.util.List;
      @SpringBootApplication
      public class RunBoot {
        public static void main(String[] args) {
          ApplicationContext ac = SpringApplication.run(RunBoot.class, args);
          KnowledgeDao dao1 = ac.getBean(KnowledgeDao.class);
          //扩展操作
          List<Knowledge> list = dao1.findByJPQL(3);
          list.forEach(know -> System.out.println(know.getId()+" "+know.getName()));
        }
      }
    ```
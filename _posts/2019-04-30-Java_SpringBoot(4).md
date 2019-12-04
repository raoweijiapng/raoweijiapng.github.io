---
title: Java_SpringBoot(4)之Mabtis的扩展使用
tags: java SpringBoot
categories: java
---

* TOC
{:toc}

## 扩展1：Mybatis分页

  > 使用pageHelper组件进行优化、简化操作。

  - 在上述mybatis的pom文件上引入pagehelper-spring-boot-starter启动器

    ```xml
      <dependency>
          <groupId>com.github.pagehelper</groupId>
          <artifactId>pagehelper-spring-boot-starter</artifactId>
          <version>1.2.5</version>
      </dependency>
    ```

  - 在调用mapper接口方法查询之前使用PageHelper.startPage

    ```java
      package cn.xdl;
      import java.util.List;
      import org.mybatis.spring.annotation.MapperScan;
      import org.springframework.boot.SpringApplication;
      import org.springframework.boot.autoconfigure.SpringBootApplication;
      import org.springframework.context.ApplicationContext;
      import com.github.pagehelper.PageHelper;
      import cn.xdl.dao.NewDao;
      import cn.xdl.entity.News;
      @SpringBootApplication
      @MapperScan(basePackages="cn.xdl.dao")//扫描Mapper接口
      public class RunBoot {
        public static void main(String[] args) {
          ApplicationContext ac = SpringApplication.run(RunBoot.class, args);
          NewDao newDao = ac.getBean(NewDao.class);
          //基于pageHelper分页
          PageHelper.startPage(1, 3);//设置分页参数
          List<News> list = newDao.findAll();//pageHelper先将sql封装成分页sql，再执行查询
          for(News n:list) {
            System.out.println(n.getId()+" "+n.getTitle());
          }
        }
      }
    ```

## 扩展2：注解SQL

  - 利用自动配置创建连接池对象（参考以前过程）,同mybatis一样

  - 根据表编写实体类

    ```java
      package cn.xdl.entity;
      import java.io.Serializable;
      public class Category implements Serializable{        
        private Integer id;
        private String name;
        private Integer turn;
        private String description;
        private Integer parent_id;
        
        public Integer getId() {
          return id;
        }
        public void setId(Integer id) {
          this.id = id;
        }
        public String getName() {
          return name;
        }
        public void setName(String name) {
          this.name = name;
        }
        public Integer getTurn() {
          return turn;
        }
        public void setTurn(Integer turn) {
          this.turn = turn;
        }
        public String getDescription() {
          return description;
        }
        public void setDescription(String description) {
          this.description = description;
        }
        public Integer getParent_id() {
          return parent_id;
        }
        public void setParent_id(Integer parent_id) {
          this.parent_id = parent_id;
        }   
      }
    ```

  - 定义Mapper映射器接口（注解SQL，配置@MapperScan),利用@Select、@Insert、@Update、@Delete定义SQL语句

  ```java
    package cn.xdl.dao;
    import java.util.List;
    import org.apache.ibatis.annotations.Result;
    import org.apache.ibatis.annotations.Results;
    import org.apache.ibatis.annotations.Select;
    import cn.xdl.entity.Category;
    public interface CategoryDao {      
      @Select("select * from category")
      @Results({
        @Result(property="id",column="category_id"),
        @Result(property="name",column="name"),
        @Result(property="turn",column="turn"),
        @Result(property="description",column="description"),
        @Result(property="parent_id",column="parent_id")
      })
      public List<Category> selectAll();      
      
      @Select("select category_id id,name,description,turn,parent_id from category")
      public List<Category> selectAll1();      
    }
  ```

  - 定义启动类（@SpringBootApplication+@MapperScan）

    ```java
      package cn.xdl;
      import cn.xdl.dao.CategoryDao;
      import cn.xdl.entity.Category;
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
          //测试categoryDao
          CategoryDao categoryDao = ac.getBean(CategoryDao.class);
          List<Category> list = categoryDao.selectAll1();
          for(Category c:list) {
            System.out.println(c.getId()+" "+c.getName());
          }
        }
      }
    ```

## 扩展3：动态SQL

  - XML方式定义SQL

    > 在NewsMapper.xml文件中输入

    ```xml
      <update id="dynamicUpdate" parameterType="cn.xdl.entity.News">
      update knowledge
          <set>
              <if test="title != null">
                  name=#{name},
              </if>
              <if test="content != null">
                  content=#{content},
              </if>
              <if test="sticky != null">
                  sticky=#{sticky}
              </if>
          </set>
          <where>
              id=#{id}
          </where>
      </update>
    ```

    > 定义Mapper映射器接口（与XML文件SQL映射，配置@MapperScan）

    ```java
      package cn.xdl.dao;
      import java.util.List;
      import cn.xdl.entity.News;
      public interface NewsDao {  
        public int dynamicUpdate(News nw);
      }
    ```

    > 定义启动类（@SpringBootApplication+@MapperScan）

    ```java
      @SpringBootApplication
      @MapperScan(basePackages="cn.xdl.dao")//扫描Mapper接口
      public class RunBoot {
        public static void main(String[] args) {
          ApplicationContext ac = SpringApplication.run(RunBoot.class, args);
          Knowledge know = new Knowledge();
          know.setId(1);
          know.setChapter_id(10);
          know.setSubject_id(1);
          dao.dynamicUpdate(know);
        }
      }
    ```

  - 注解方式定义SQL

    > 新建一个实体类Question

    ```java
      package cn.xdl.entity;
      import java.io.Serializable;
      import java.util.Date;
      public class Question implements Serializable{        
        private Integer id;
        private Integer author;
        private Date add_date;
        private String body;
        private Integer degree;
        private String answer;
        private String analysis;
        private Integer type_id;
        private Integer knowledge_id;
        private Integer subject_id;
        private Integer chapter_id;
        
        public Integer getId() {
          return id;
        }
        public void setId(Integer id) {
          this.id = id;
        }
        public Integer getAuthor() {
          return author;
        }
        public void setAuthor(Integer author) {
          this.author = author;
        }
        public Date getAdd_date() {
          return add_date;
        }
        public void setAdd_date(Date add_date) {
          this.add_date = add_date;
        }
        public String getBody() {
          return body;
        }
        public void setBody(String body) {
          this.body = body;
        }
        public Integer getDegree() {
          return degree;
        }
        public void setDegree(Integer degree) {
          this.degree = degree;
        }
        public String getAnswer() {
          return answer;
        }
        public void setAnswer(String answer) {
          this.answer = answer;
        }
        public String getAnalysis() {
          return analysis;
        }
        public void setAnalysis(String analysis) {
          this.analysis = analysis;
        }
        public Integer getType_id() {
          return type_id;
        }
        public void setType_id(Integer type_id) {
          this.type_id = type_id;
        }
        public Integer getKnowledge_id() {
          return knowledge_id;
        }
        public void setKnowledge_id(Integer knowledge_id) {
          this.knowledge_id = knowledge_id;
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

    > 在NewsDao.java接口增加定义方法

    ```java
      package cn.xdl.dao;
      public interface QuestionDao {
        @UpdateProvider(type=QuestionProvider.class,method="updateSelective")
        public int dymamicUpdate(Question question);
      }
    ```

    > QuestionProvider.java定义SQL拼凑

    ```java
      package cn.xdl.entity;
      import org.apache.ibatis.jdbc.SQL;
      public class QuestionProvider {
          //返回一个拼凑好的update SQL语句
          public String updateSelective(Question question) {
              SQL sql = new SQL();
              sql.UPDATE("question");//update question
              if(question.getAuthor() != null) {
                  sql.SET("author=#{author}");//set ...
              }
              if(question.getAdd_date() != null) {
                  sql.SET("add_date=#{add_date}");
              }
              if(question.getBody() != null) {
                  sql.SET("body=#{body}");
              }
              if(question.getSubject_id() != null) {
                  sql.SET("subject_id=#{subject_id}");
              }
              if(question.getChapter_id() != null) {
                  sql.SET("chapter_id=#{chapter_id}");
              }
              if(question.getKnowledge_id() != null) {
                  sql.SET("knowledge_id=#{knowledge_id}");
              }
              sql.WHERE("id=#{id}"); //where ...
              return sql.toString();
          }

      }
    ```

    > 定义启动类（@SpringBootApplication+@MapperScan）

    ```java
      @SpringBootApplication
      @MapperScan(basePackages="cn.xdl.dao")//扫描Mapper接口
      public class RunBoot {
        public static void main(String[] args) {
          ApplicationContext ac = SpringApplication.run(RunBoot.class, args);
          Question question = new Question();
          question.setId(2);
          question.setAdd_date(new Date());
          question.setBody("MyBatis动态SQL");
          dao2.dymamicUpdate(question);
        }
      }
    ```


---
title: Java_SpringBoot(6)之MVC的使用
tags: java SpringBoot
categories: java
---

* TOC
{:toc}

## SpringBoot MVC

  - 应用一：开发Restful服务(推荐),(请求-->SpringMVC-->返回JSON结果)

    > 创建Maven项目,在pom.xml文件中导入dependecy

    ```xml
      <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
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
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-data-jpa</artifactId>
            <version>2.1.2.RELEASE</version>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-devtools</artifactId>
            <version>2.1.2.RELEASE</version>
        </dependency>

      </dependencies>
    ```

    > 在src/main/resources下创建application.properties文件并输入

    ```java
      server.port=8888
      spring.datasource.username=root
      spring.datasource.password=123456
      spring.datasource.url=jdbc:mysql://localhost:3306/mysql
      spring.datasource.driverClassName=com.mysql.jdbc.Driver
    ```

    > 在src/main/java下创建com.xdl.entity目录,然后创建实体类Knowledge

    > 在src/main/java下创建com.xdl.dao目录,然后创建接口KnowledgeDao

    ```java
      package cn.xdl.dao;
      import cn.xdl.entity.Knowledge;
      import org.springframework.data.jpa.repository.JpaRepository;
      public interface KnowledgeDao extends JpaRepository<Knowledge, Integer>{
        
      }
    ```

    > 在src/main/java下创建com.xdl.web目录,然后创建KnowledgeController类

    ```java
      package cn.xdl.web;
      import cn.xdl.dao.KnowledgeDao;
      import cn.xdl.entity.Knowledge;
      import org.springframework.beans.factory.annotation.Autowired;
      import org.springframework.data.domain.PageRequest;
      import org.springframework.data.domain.Pageable;
      import org.springframework.web.bind.annotation.GetMapping;
      import org.springframework.web.bind.annotation.PathVariable;
      import org.springframework.web.bind.annotation.RequestParam;
      import org.springframework.web.bind.annotation.RestController;
      import java.util.List;
      @RestController
      public class KnowledgeController {
        
        @Autowired
        private KnowledgeDao knowledgeDao;

        //GET  按id查询某个知识点
        @GetMapping("/knowledge/{id}")
        public Knowledge load(@PathVariable("id")int id) {
          return knowledgeDao.findById(id).get();
        }

        //GET 分页查询知识点
        @GetMapping("/knowledge/list")
        public List<Knowledge> list(
          @RequestParam(name="page",required=false,defaultValue="1")int page,
          @RequestParam(name="size",required=false,defaultValue="3")int size){
          Pageable pageable = PageRequest.of(page-1, size);
          return knowledgeDao.findAll(pageable).getContent();
        }  
      }
    ```

    > 在src/main/java下创建启动类

    ```java
      package cn.xdl;
      import org.springframework.boot.SpringApplication;
      import org.springframework.boot.autoconfigure.SpringBootApplication;
      @SpringBootApplication
      public class RunBoot {
        public static void main(String[] args) {
          SpringApplication.run(RunBoot.class, args);
        }
      }
    ```

  - 应用二：开发JSP应用 (请求-->SpringMVC-->JSP-->返回HTML响应结果)

    > 创建Maven项目,在pom.xml文件中导入dependecy

    ```xml
      <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
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
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-data-jpa</artifactId>
            <version>2.1.2.RELEASE</version>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-devtools</artifactId>
            <version>2.1.2.RELEASE</version>
        </dependency>

        <dependency>
            <groupId>org.apache.tomcat.embed</groupId>
            <artifactId>tomcat-embed-jasper</artifactId>
            <version>9.0.14</version>
        </dependency>

        <dependency>
            <groupId>jstl</groupId>
            <artifactId>jstl</artifactId>
            <version>1.2</version>
        </dependency>

      </dependencies>
    ```

    > 在src/main/resources下创建application.properties文件并输入

    ```java
      server.port=8888
      spring.datasource.username=root
      spring.datasource.password=123456
      spring.datasource.url=jdbc:mysql://localhost:3306/mysql
      spring.datasource.driverClassName=com.mysql.jdbc.Driver

      #src/main/webapp
      spring.mvc.view.prefix=/
      spring.mvc.view.suffix=.jsp
    ```

    > 在src/main/java下创建com.xdl.entity目录,然后创建实体类News

    > 在src/main/java下创建com.xdl.dao目录,然后创建接口NewsDao

    ```java
      package cn.xdl.dao;
      import org.springframework.data.jpa.repository.JpaRepository;
      import cn.xdl.entity.News;
      public interface NewsDao extends JpaRepository<News, Integer> {

      }
    ```

    > 在src/main/java下创建com.xdl.controller目录,然后创建NewsController类

    ```java
      package cn.xdl.controller;
      import cn.xdl.dao.NewsDao;
      import cn.xdl.entity.News;
      import org.springframework.beans.factory.annotation.Autowired;
      import org.springframework.data.domain.Page;
      import org.springframework.data.domain.PageRequest;
      import org.springframework.data.domain.Pageable;
      import org.springframework.stereotype.Controller;
      import org.springframework.web.bind.annotation.RequestMapping;
      import org.springframework.web.bind.annotation.RequestParam;
      import org.springframework.web.servlet.ModelAndView;
      import java.util.List;
      @Controller
      public class NewsController {
        
        @Autowired
        private NewsDao newsDao;
        
        @RequestMapping("/news/list")
        public ModelAndView list(
          @RequestParam(name="page",required=false,defaultValue="1")int pageIndex,
          @RequestParam(name="size",required=false,defaultValue="3")int size) {
          pageIndex--;//页数从0开始
          Pageable page = PageRequest.of(pageIndex, size);
          Page<News> p = newsDao.findAll(page);
          List<News> list = p.getContent();
          //ModelAndView
          ModelAndView mav = new ModelAndView();
          mav.setViewName("list");// /list.jsp
          mav.getModel().put("news", list);
          return mav;//将mav交给ViewResolver组件解析处理
        }  
      }
    ```

    > 在src/main/下创建webapp目录,然后创建list.jsp

    ```jsp
      <%@ page language="java" contentType="text/html; charset=UTF-8"
      pageEncoding="UTF-8"%>
      <%@ taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c" %>
      <!DOCTYPE html>
      <html>
        <head>
          <meta charset="UTF-8">
          <title>Insert title here</title>
        </head>
        <body>
          <h1>新闻列表</h1>
          <table>
            <c:forEach items="${news}" var="n" varStatus="vs">
              <tr>
                <td>${vs.count}</td>
                <td>${n.title }</td>
                <td>${n.release_time}</td>
              </tr>
            </c:forEach>
          </table>
        </body>
      </html>
    ```

    > 在src/main/java下创建启动类，同上应用一

  - 应用三：开发Thymeleaf模板应用(推荐) (请求-->SpringMVC-->thymeleaf模板-->返回HTML响应结果)

    模板技术：velocity、freemarker、thymeleaf

    模板技术是对JSP技术的替代，由模板文件和模型数据构成。

    ```
      velocity : *.vm + VTL
      freemarker : *.ftl + FTL
      thymeleaf : *.html + THTL
    ```

    模板技术优点: 模板技术应用简单，JSP复杂; 模板技术效率高（模板(缓存)+模型=HTML结果），JSP(JSP-->Servlet-->HTML结果)

    > 创建Maven项目,在pom.xml文件中导入dependecy

    ```xml
      <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
            <version>2.1.2.RELEASE</version>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-thymeleaf</artifactId>
            <version>2.1.2.RELEASE</version>
        </dependency>
    </dependencies>
    ```

    > 在src/main/resources下创建application.properties文件并输入

    ```java
      server.port=8888
    ```

    > 在src/main/java下创建com.xdl.controller目录,然后创建HelloController类

    ```java
      package cn.xdl.web;
      import org.springframework.stereotype.Controller;
      import org.springframework.web.bind.annotation.RequestMapping;
      import org.springframework.web.servlet.ModelAndView;
      @Controller
      public class HelloController {
        @RequestMapping("/hello")
        public ModelAndView  say() {
          ModelAndView mav = new ModelAndView();
          mav.setViewName("hello");//src/main/resources/templates/hello.html
          mav.getModel().put("msg", "XDL学习SpringBoot");
          return mav;
        }  
      }
    ```

    > 在src/main/resorces下创建templates目录,然后在此目录下创建hello.html

    ```html
      <!DOCTYPE html>
      <html xmlns:th="http://www.thymeleaf.org">
        <head>
          <meta charset="UTF-8">
          <title>Insert title here</title>
        </head>
        <body>
          <h1 th:text="${msg}"></h1>
        </body>
      </html>
    ```

    > 在src/main/java下创建启动类，同上应用一

  - 开发Thymeleaf模板应用(推荐)的扩展

    > 在上述应用三的pom.xml文件中增加dependecy

    ```xml
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
          <groupId>org.springframework.boot</groupId>
          <artifactId>spring-boot-starter-data-jpa</artifactId>
          <version>2.1.2.RELEASE</version>
      </dependency>
      <dependency>
          <groupId>org.springframework.boot</groupId>
          <artifactId>spring-boot-devtools</artifactId>
          <version>2.1.2.RELEASE</version>
      </dependency>
    ```

    > 在上述应用三的application.properties增加以下内容

    ```
      spring.datasource.username=root
      spring.datasource.password=123456
      spring.datasource.url=jdbc:mysql://localhost:3306/mysql
      spring.datasource.driverClassName=com.mysql.jdbc.Driver

      spring.thymeleaf.cache=false
    ```

    > 在src/main/java下创建com.xdl.entity目录,然后创建实体类Knowledge

    > 在src/main/java下创建com.xdl.dao目录,然后创建接口KnowledgeDao

    ```java
      package cn.xdl.dao;
      import cn.xdl.entity.Knowledge;
      import org.springframework.data.jpa.repository.JpaRepository;
      public interface KnowledgeDao extends JpaRepository<Knowledge, Integer>{
        
      }
    ```

    > 在上述应用三的src/main/java/controller目录下,创建KnowledgeController类

    ```java
      package cn.xdl.web;
      import org.springframework.beans.factory.annotation.Autowired;
      import org.springframework.data.domain.Page;
      import org.springframework.data.domain.PageRequest;
      import org.springframework.data.domain.Pageable;
      import org.springframework.stereotype.Controller;
      import org.springframework.web.bind.annotation.RequestMapping;
      import org.springframework.web.bind.annotation.RequestParam;
      import org.springframework.web.servlet.ModelAndView;
      import cn.xdl.dao.KnowledgeDao;
      import cn.xdl.entity.Knowledge;
      @Controller
      public class KnowledgeController {
        @Autowired
        private KnowledgeDao knowledgeDao;
        
        @RequestMapping("/knowledge/list")
        public ModelAndView list(
          @RequestParam(name="page",required=false,defaultValue="1")int page,
          @RequestParam(name="size",required=false,defaultValue="3")int size) {
          Pageable pageable = PageRequest.of(page-1, size);
          Page<Knowledge> pageContent = knowledgeDao.findAll(pageable);
          ModelAndView mav = new ModelAndView();
          mav.setViewName("list");//list.html
          mav.getModel().put("knows", pageContent.getContent());
          mav.getModel().put("current",pageContent.getNumber()+1);
          mav.getModel().put("total", pageContent.getTotalPages());
          return mav;
        }  
      }
    ```

    > 在上述应用三的src/main/resorces/templates目录下创建list.html

    ```html
      <!DOCTYPE html>
      <html xmlns:th="http://www.thymeleaf.org">
        <head>
          <meta charset="UTF-8">
          <title>Insert title here</title>
        </head>
        <body>
          <h1>列表显示</h1>
          <span th:text="${current}"></span>
          /
          <span th:text="${total}"></span>
          <table>
            <tr th:each="know : ${knows}">
              <td th:text="${know.id}"></td>
              <td th:text="${know.name}"></td>
            </tr>
          </table>
          <a href="/knowledge/list?page=1">第一页</a>
          <a th:if="${current>1}" th:href="@{/knowledge/list(page=${current-1})}">上一页</a>
          <a th:if="${current<total}" th:href="@{/knowledge/list(page=${current+1})}">下一页</a>
          <a th:href="@{/knowledge/list(page=${total})}">最后一页</a>

          <select>
            <option th:each="know : ${knows}" th:text="${know.name}" th:value="${know.id}"></option>
          </select>
        </body>
      </html>
    ```

    > 在src/main/java下创建启动类，同上应用一

---
title: Java_SpringBoot(2)之启动类的@SpringBootApplication和用连接池访问数据库
tags: java SpringBoot
categories: java
---

* TOC
{:toc}

## @SpringBootApplication

  - @SpringBootApplication是boot类中一个多功能标记，由以下几个标记组成。

    @SpringBootConfiguration  //开启Bean定义支持

    @EnableAutoConfiguration  //开启自动配置

    @ComponentScan  //开启组件扫描

### @SpringBootConfiguration

  用于支持@Bean组件定义，等价于之前XML中的< bean>.常用于jar包中、特殊bean采用此方法定义，纳入spring容器中。

  ```java
    //@SpringBootConfiguration
    @Configuration
    public class RunBoot {    
      public static void main(String[] args) {
        ApplicationContext ac = 
          SpringApplication.run(RunBoot.class, args);
        UserDao dao1 = ac.getBean("dao1",UserDao.class);
        System.out.println(dao1);
        UserDao dao2 = ac.getBean("userdao",UserDao.class);
        System.out.println(dao2);
        UserDao dao3 = ac.getBean(UserDao.class);
        System.out.println(dao3);
      }
      
      //<bean id="userdao" class="cn.xdl.bean.JdbcUserDao">
      @Bean//将返回的对象放入ApplicationContext容器对象中,默认id为方法名
      @Primary //解决类型冲突，默认使用该对象
      public UserDao userdao() {
        return new JdbcUserDao();
      }
      
      @Bean(name="dao1")//将返回的对象放入ApplicationContext容器对象中,指定id为dao1
      public UserDao userdao1() {
        return new JdbcUserDao();
      }    
    }
  ```

### @ComponentScan

  - 开启组件扫描功能，等价于XML配置< context:component-scan base-package=""/>。

    ```java
      @ComponentScan //默认扫描当前包及子包下组件
      //@ComponentScan(basePackages= {"cn.xdl.service"})
    ```

    @ComponentScan如果扫描其他包下组件，需要指定basePackages属性，如果扫描当前包和子包组件可以省略basePackages属性。

  - 使用includeFilters属性加载没有@Service等标记的组件

    ```java
      @ComponentScan(basePackages= {"cn.xdl.service"},includeFilters= {
        @Filter(type=FilterType.ASSIGNABLE_TYPE,classes= {UserServiceImpl.class})
      }) 
    ```
  
    扫描cn.xdl.service下的UserServiceImpl组件，即使不加@Service标记也能纳入Spring容器

  - 使用excludeFilters属性排除指定包的组件

    ```java
      @ComponentScan(basePackages= {"cn.xdl.service"},excludeFilters= {
        @Filter(type=FilterType.ASSIGNABLE_TYPE,classes= {UserServiceImpl.class})
      }) 
    ```

    扫描cn.xdl.service下的组件，按类型排除UserServiceImpl组件
    
    ```java
      @ComponentScan(basePackages= {"cn.xdl.service"},excludeFilters= {
        @Filter(type=FilterType.ANNOTATION,value=Service.class)
      })
    ```

    扫描cn.xdl.service下的组件，按注解标记排除组件。

### @EnableAutoConfiguration

  开启Boot自动配置功能，Boot底层提供了很多配置组件，通过自动配置标记加载到Spring容器。

  - 自动配置原理
    
    Boot底层在xxx-autoconfigure.jar自动配置包中有一个META-INF/spring.factories文件。spring.factories定义很多的XxxAutoConfiguration,例如DataSourceAutoConfiguration、JdbcTemplateAutoConfiguration、DispatcherServletAutoConfiguration等。这些配置类内部采用@Configuration+@Bean+@Primary+@Conditional等标记完成组件对象创建并加载到Spring容器中。

    启动Boot程序时，如果追加@EnableAutoConfiguration标记，它会引入AutoConfigurationImportSelector组件，内部借助SpringFactoriesLoader组件加载xxx-autoconfigure.jar中META-INF/spring.factories定义的配置类信息，利用反射实例化加载其中的Bean组件对象，例如dataSource、jdbcTemplate等。

  - 自动配置示例

    > 首先在pom.xml添加jar包引入

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
      </dependencies>
    ```

    > application.properties定义db连接参数

    ```
      spring.datasource.username=root
      spring.datasource.password=123456
      spring.datasource.url=jdbc:mysql://localhost:3306/jd
      spring.datasource.driverClassName=com.mysql.jdbc.Driver
    ```

    > 开启@EnableAutoConfiguration，通过springboot容器获取dataSource和jdbcTemplate对象

    ```java
      @EnableAutoConfiguration
      public class RunBoot {
        public static void main(String[] args) {
          ApplicationContext ac = SpringApplication.run(RunBoot.class, args);
          //加载DataSourceAutoConfiguration
          Object obj1 = ac.getBean("dataSource");
          System.out.println(obj1);
          //加载JdbcTemplateAutoConfiguration
          Object obj2 = ac.getBean("jdbcTemplate");
          System.out.println(obj2);
        }
      }
    ```

  - 自动配置示例（参数注入）

    > 可以将application.properties或yml配置文件中的参数给Bean对象属性注入。

    > 在application.properties定义参数

    ```
      my.name=scott
      my.age=20
    ```

    > 定义实体类My.java,属性名字与name和age相同

    ```java
      public class My {    
        private String name; //对应my.name
        private int age; //对应my.age
        
        public String getName() {
          return name;
        }
        public void setName(String name) {
          this.name = name;
        }
        public int getAge() {
          return age;
        }
        public void setAge(int age) {
          this.age = age;
        }
      }
    ```

    > 方法一：在My类定义前使用@ConfigurationProperties

    ```java
      @Component
      @ConfigurationProperties(prefix="my")//由ConfigurationPropertiesAutoConfiguration自动配置组件支持
      public class My {
        
        private String name; //对应my.name
        private int age; //对应my.age
        
        public String getName() {
          return name;
        }
        public void setName(String name) {
          this.name = name;
        }
        public int getAge() {
          return age;
        }
        public void setAge(int age) {
          this.age = age;
        }
      }
    ```
    
    > 方法二：在@Bean组件定义方法前使用@ConfigurationProperties

    ```java
      @Bean
      @ConfigurationProperties(prefix="my")//将配置参数给返回对象的属性注入
      public My my() {
        return new My();
      }
    ```

    提示：@ConfigurationProperties标记需要开启自动配置才能生效，跟底层自动配置组件ConfigurationPropertiesAutoConfiguration有关。或者使用@EnableConfigurationProperties开启ConfigurationPropertiesAutoConfiguration的支持也可以。如果key和Bean对象属性不一致的，可以采用@Value("${key}")注入参数。

    ```java
      public class RunBoot {
        public static void main(String[] args) {
          ApplicationContext ac = SpringApplication.run(RunBoot.class, args);
          My my = ac.getBean("my",My.class);
          System.out.println(my.getName()+" "+my.getAge());
        }
      }
    ```

##SpringBoot访问数据库

###DataSource连接池

  种类：druid、dbcp、hikari、dbcp2、c3p0、proxool、tomcat-jdbc

  连接池优点：避免频繁创建和释放连接，共享连接对象；控制连接对象数量，保障数据库稳定性。

  SpringBoot2提供了自动配置组件DataSourceAutoConfiguration，该配置组件有以下机制：

  - 提供hikari、dbcp2、tomcat类型的连接池对象创建方法，创建优先级顺序为hikari、tomcat、dbcp2

  - 引入spring-boot-starter-jdbc默认引入的是hikari连接池,即便引入dbcp或者Druid,也还是引入hikari,所以要exclusion hikari

  ```xml
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-jdbc</artifactId>
        <version>2.1.2.RELEASE</version>
        <exclusions>
            <exclusion>
                <groupId>com.zaxxer</groupId>
                <artifactId>HikariCP</artifactId>
            </exclusion>
        </exclusions>
    </dependency>
  ```

  - 使用其他类型连接池可以通过spring.datasource.type定义,在.properties文件中加入spring.datasource.type=com.alibaba.druid.pool.DruidDataSource

  ```
    spring.datasource.username=root
    spring.datasource.password=123456
    spring.datasource.url=jdbc:mysql://localhost:3306/jd
    spring.datasource.driverClassName=com.mysql.jdbc.Driver
    spring.datasource.type=com.alibaba.druid.pool.DruidDataSource
  ```

  - 如果不想通过spring.datasource.type定义,可以在boot类中

  ```java
    @Configuration
    @EnableAutoConfiguration
    public class RunBoot {      
      public static void main(String[] args) {
          ApplicationContext ac = SpringApplication.run(RunBoot.class, args);
          //加载DataSourceAutoConfiguration
          Object obj1 = ac.getBean("ds");
          System.out.println(obj1);
        }

      @Bean
      @ConfigurationProperties(prefix="spring.datasource")
      @Primary
      public DataSource ds() {
        DruidDataSource ds1 = new DruidDataSource();
        return ds1;
      }
    }
  ```

  - 如果需要多库操作，只能自定义dataSource，别再走自动配置了

  > application.properties参数配置:

  ```
    spring.datasource.username=root
    spring.datasource.password=123456
    spring.datasource.url=jdbc:mysql://localhost:3306/jd
    spring.datasource.driverClassName=com.mysql.jdbc.Driver
    
    spring.datasource1.username=root
    spring.datasource1.password=123456
    spring.datasource1.url=jdbc:mysql://localhost:3306/ydma
    spring.datasource1.driverClassName=com.mysql.jdbc.Driver
  ```

  > 自定义dataSource代码:

  ```java
    @Bean
    @Primary
    public DataSource ds() {
      DruidDataSource ds1 = new DruidDataSource();
      return ds1;
    }
    
    @Bean
    public DataSource ds1() {
      DruidDataSource ds1 = new DruidDataSource();
      return ds1;
    }
  ```

  提示：一旦自定义了dataSource对象，自动配置组件将失效。多库事务完整性，需要采用JTA分布式事务控制。

    


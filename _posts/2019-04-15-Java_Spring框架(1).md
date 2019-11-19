---
title: Java_Spring框架(1)之Spring框架、IOC的使用
tags: java Spring IOC
categories: java
---

* TOC
{:toc}

## Spring

  Spring是一个开源框架，为JavaEE应用提供多方面的解决方案，用于简化企业级应用的开发。

![Spring的组成](http://raoweijiapng.github.io/static/img/java/Spring的组成.png)

## Spring框架的构成

<table>
    <tr>
        <td>简称</td> 
        <td>含义</td>
        <td>说明</td>
    </tr>
    <tr>
        <td>IOC</td> 
        <td>控制反转</td>
        <td>它是Spring框架的核心</td>
    </tr>
    <tr>
        <td>DAO</td> 
        <td>数据访问对象模块</td>
        <td>它是对JDBC的封装和简化</td>
    </tr>
    <tr>
        <td>WebMVC</td>
        <td colspan="2">它是Spring对java web的支持以及MVC设计模式的支持</td>  
    </tr>
    <tr>
        <td>AOP</td> 
        <td>面向切面编程</td>
        <td>它是对面向对象的扩展</td>
    </tr>
    <tr>
        <td>ORM</td>
        <td colspan="2">对象关系映射(是以面向对象的思想访问数据库) Mybatis</td>    
    </tr>
    <tr>
        <td>JEE</td>
        <td colspan="2">与JavaEE服务整合</td>    
    </tr>
    
</table>

## 什么是IOC

  Inverstion Of Control(控制反转),程序中需要某个对象时,由原来的new的方式改成了由容器来进行创建和管理以及维护组件关系。

  这样做的好处是可以大大降低组件之间的耦合度。 

## Spring容器

  任何的java类都可以在Spring容器中创建对象并由容器进行管理以及维护组件关系.

  Spring容器实现了IOC和AOP机制.

  Spring容器的类型BeanFactory或者是ApplicationContext类型.

## 使用Spring容器完成IOC

> 建立一个项目,导入jar包(ioc相关jar包)拷贝Spring配置文件applicationContext.xml到src下

  jar包有spring-expression-4.1.6.RELEASE.jar、spring-core-4.1.6.RELEASE.jar、spring-context-4.1.6.RELEASE.jar、spring-beans-4.1.6.RELEASE.jar、commons-logging.jar

> 在SPring配置文件中配置对象创建的说明 

  ```
    格式:<bean id="对象的引用名" class="包名.类名"> </bean>
  ```

> 创建Spring容器对象,创建对象时需要指定配置文件使用对应的API获取对象  

- 从容器中获取系统中的类对象，如日期对象和List类型的对象 

  首先导入IOC相关的包,然后再拷贝配置文件applicationContext.xml,其中的代码如下

  ```xml
  <?xml version="1.0" encoding="UTF-8"?>
  <beans xmlns="http://www.springframework.org/schema/beans"
      xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
        xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean  id="date" class="java.util.Date">
    </bean>
    <!--  创建List 类型的对象 -->
    <bean  id="list" class="java.util.ArrayList">
    </bean>
  </beans>
  ```
  
  创建ApplicationContextTest.class,并输入

  ```java
    import org.springframework.context.ApplicationContext;
    import org.springframework.context.support.ClassPathXmlApplicationContext;
    import java.util.Date;
    import java.util.List;
    public class ApplicationContextTest {
          new ClassPathXmlApplicationContext("applicationContext.xml");
        // 从容器中获取对象
        Date  date  = (Date)app.getBean("date");
        //输出对象时,调用的是toString方法
        System.out.println(date);
        // Date  date  = new  Date();
        Date  date2 = app.getBean("date", Date.class);
        System.out.println(date2);
        List  list = app.getBean("list", List.class);
        System.out.println(list);
      }
    }
  ```

- 获取自定义的类对象，建立一个员工类Emp    

  成员变量有int  id,String  name,double salary,提供get、set方法，提供无参构造，全参构造，toString

  ```java
    public class Emp {
      private  int  id;
      private  String name;
      private  double salary;
      /** 初始化方法 */
      public  void  init() {
        System.out.println("初始化Emp");
      }
      /** 销毁方法 */
      public  void  destroy() {
        System.out.println("即将销毁Emp");
      }
        public int getId() {
        return id;
      }
      public void setId(int id) {
        this.id = id;
      }
      public String getName() {
        return name;
      }
      public void setName(String name) {
        this.name = name;
      }
      public double getSalary() {
        return salary;
      }
      public void setSalary(double salary) {
        this.salary = salary;
      }
      @Override
      public String toString() {
        return "Emp [id=" + id + ", name=" + name + ", salary=" + salary + "]";
      }
      public Emp(int id, String name, double salary) {
        super();
        this.id = id;
        this.name = name;
        this.salary = salary;
        System.out.println("Emp(int id, String name, double salary) ");
      }
      public Emp() {
        super();
        System.out.println("Emp()");
      }
    }
  ```

  在applicationContext.xml中Spring容器创建Emp类型的对象

  ```xml
    <!--  创建Emp 类型的对象  -->
    <bean  id="emp"  class="bean.Emp">
    </bean>
  ```

  创建EmpTest.class,并输入

  ```java
    import org.springframework.context.ApplicationContext;
    import org.springframework.context.support.ClassPathXmlApplicationContext;
    import bean.Emp;
    public class ApplicationContextTest {
        // 创建Spring 容器对象
        ApplicationContext app = new ClassPathXmlApplicationContext("applicationContext.xml");
        Emp  emp  =  app.getBean("emp", Emp.class);
        System.out.println(emp);
        //输出对象时调用的是类的toString方法
      }
    }
  ```

## Spring容器创建对象的三种方式
 
> 构造器方式实例化

    <bean  id="标识符"  class="包名.类名"></bean>
    此时，程序一运行就会默认调用容器里定义的所有类型对应的无参构造方法
    上述的几个实例就是构造器方式的实例化

> 静态工厂方法实例化 

    本质上是类型调用对应的静态方法 来获取一个对象 类型名.静态方法名()
    <bean id="标识符"  class="包名.工厂类名"  factory-method="静态方法名"> </bean>

  在applicationContext.xml中Spring容器创建对象

  ```xml
    <!--  静态工厂方法实例化   创建一个日历类型的对象 -->
    <bean id="cal"  class="java.util.Calendar" factory-method="getInstance">
    </bean>
    <bean id="emp2"  class="factory.EmpFactory" factory-method="getEmp">
    </bean>
  ```
  
  创建EmpFactory.class,并输入

  ```java
    import bean.Emp;
    public class EmpFactory {
        public static  Emp   getEmp() {
          return   new  Emp(1,"liweijie",1);
          //返回值是new对象,所以要调用类的构造方法
        }
    }
  ```

  在之前的EmpTest.class,输入以下

  ```java
    //Calendar  cal =  Calendar.getInstance();
    Calendar cal = app.getBean("cal", Calendar.class);
    System.out.println(cal);
    Emp  emp2 = app.getBean("emp2", Emp.class);
    System.out.println(emp2);
  ```

> 实例工厂方法实例化 

    本质上是利用一个已经存在的对象来创建出另外一个类型的对象 
    <bean id="标识符"    factory-method="实例方法名"  factory-bean="工厂对象的id">
    </bean>  

  在applicationContext.xml中Spring容器创建对象

  ```xml
    <!--  实例工厂方法实例化  -->
    <bean id="date2"  factory-method="getTime" factory-bean="cal">
    </bean>
  ```

  在之前的EmpTest.class,输入以下

  ```java
    Date  date2 = app.getBean("date2", Date.class);
    System.out.println(date2);
  ```

## Spring中对象的作用域  

  Spring中对象默认的作用域是单例的,如果希望每次请求都获取不同的对象,则可以使用bean标记中的scope属性指定作用域。

  scope默认的取值是singleton,设置成prototype则为非单例的。
   
  当然这里也有request session等作用域,这到web部分才会涉及。

![bean对象的作用域](https://raoweijiapng.github.io/static/img/java/bean对象的作用域.png)

## Spring容器中的对象的初始化和销毁 

- 初始化 

  一个对象被容器创建之后可以通过beans标记中的default-init-method性来指定初始化方法(xml文件的最外面的beans标签),由于这样影响的范围比较大(影响配置文件中所有的对象)所以对象对应的类型中如果没有对应的初始化方法程序也不会报错。

  也可以通过bean标记中的init-method属性来指定初始化方法,这种方式只会影响到一个对象,所以对象对应的类型中如果没有对应的初始化方法程序就会报错。

- 销毁 

  当一个对象即将消亡时可以通过beans标记中的default-destroy-method属性来指定销毁方法(xml文件的最外面的beans标签),由于这样影响的范围比较大(影响配置文件中所有的对象)所以对象对应的类型中如果没有对应的销毁方法程序也不会报错。

  也可以通过bean标记中的destroy-method属性来指定销毁方法,这种方式只会影响到一个对象,所以对象对应的类型中如果没有对应的销毁方法程序就会报错,但是销毁方法只针对单例模式的对象(如果不是单例模式,这时程序不报错)。

  创建applicationContext.xml,并输入

  ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <beans xmlns="http://www.springframework.org/schema/beans"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://www.springframework.org/schema/beans
          http://www.springframework.org/schema/beans/spring-beans.xsd">
      <!--  创建Emp 类型的对象  -->
      <bean  id="emp"  class="bean.Emp" scope="prototype"  init-method="init"  destroy-method="destroy">
      </bean>      
    </beans>
  ```

  创建EmpTest2.class,输入以下

  ```java
    import bean.Emp;
    import org.springframework.context.support.AbstractApplicationContext;
    import org.springframework.context.support.ClassPathXmlApplicationContext;
    public class EmpTest2 {
      public static void main(String[] args) {
        // 创建Spring 容器对象
        //ApplicationContext app = 
        AbstractApplicationContext  app = 
          new ClassPathXmlApplicationContext("applicationContext2.xml");
            Emp  emp  =  app.getBean("emp", Emp.class);
            Emp  emp2  =  app.getBean("emp", Emp.class);
            System.out.println(emp == emp2);
            emp.destroy();
            emp2.destroy();
            app.close();
      }
    }
  ```

## 容器对象的延迟实例化

  在Spring容器中对象默认的作用域是单例的，Spring容器对单例对象默认是容器。

  启动时创建可以通过bean标记的lazy-init="true"让其使用到的时候创建，容器启动时不创建。



---
title: Java_Spring框架(8)之
tags: java Spring 
categories: java
---

* TOC
{:toc}

## 什么是AOP

  Aspect  Oriented Programming(面向切面编程或者面向方面编程)
    
  它是对面向对象的一个扩展,可以不修改原有代码的情况下,给原有的逻辑增加功能,降低了共通业务逻辑和原有逻辑的耦合度 

  因为共同业务逻辑可以通过配置手段加入到原有逻辑中

## AOP中涉及到概念 

  切面          Aspect      封装共通业务逻辑的 

  连接点       JoinPoint    共通业务逻辑所要嵌入的位置   一般封装了方法的信息

  切点         Pointcut     它是一堆连接点   可以看成连接点的集合 (切点表达式)

  目标         Target       要嵌入共通业务逻辑的对象  

  代理         Proxy        被增强之后的目标对象 

  通知         Advice       时机  目标方法调用之前  目标方法调用之后   目标方法调用前后  目标方法最终  目标方法出现异常 

## 实现AOP的步骤(使用配置文件bean标签)

  - 建立一个项目 导入jar包(ioc aop)拷贝配置文件到src下 

  - 编写一个银行账户的服务类,有登录和注册两个方法

  ```java
    package com.xdl.service;
	public class XdlBankAccountService {
	    public  void  login() {
	    	System.out.println("登录中...");
	    }
	    public  void  register() {
	    	System.out.println("注册中...");
	    }
	}
  ``` 
        
  - 在配置文件中配置这个服务器类,然后通过容器获取服务类对应的对象,测试方法调用

  ```
  	<!--  目标对象  -->
	<bean  id="bankService"  class="com.xdl.service.XdlBankAccountService">	
	</bean>
  ```

  - 在不修改服务类代码的情况下,让服务类对应的方法调用前输出 

    a.定义一个切面类,里面定义输出******的方法 

    ```java
      package com.xdl.aspect;
	  public class XdlLogAspect {
	    public  void   printSixStarts() {
	        System.out.println("******");	
	    }	    
	  }
    ```

    b.在配置文件中,配置切面类型的对象 

    ```
      <!--  切面对象 -->
	  <bean  id="logAspect"  class="com.xdl.aspect.XdlLogAspect"></bean>
    ```

    c.在Spring配置文件中写AOP的配置  
               
    ```
      <!--  AOP配置 bean()指的是配置文件bean标签,括号里的是id名-->
      <aop:config>
        <aop:aspect ref="logAspect">
            <aop:before method="printSixStarts"  pointcut="bean(bankService)"/> 
        </aop:aspect>
      </aop:config> 
    ```          

  ```java
	package com.xdl.test;
	import org.springframework.context.ApplicationContext;
	import org.springframework.context.support.ClassPathXmlApplicationContext;
	import com.xdl.service.XdlBankAccountService;
	public class XdlBankAccountServiceTest {
		public static void main(String[] args) {
			ApplicationContext  app  = 
				new  ClassPathXmlApplicationContext("applicationContext.xml");
	        XdlBankAccountService  bankService = app.getBean("bankService",
	        	XdlBankAccountService.class);
	        bankService.register();
	        bankService.login();
		}
	}
  ```

## 切点表达式的写法

  - bean 限定表达式 

   bean(spring容器中id表达式)     支持统配,如bean(\*Account)
   
  - 类型限定表达式 

    within(表达式)   表达式的写法,是最后一部分必须是类型 

    com.xdl.dao.XdlBankAccountDAOOracleImp   对XdlBankAccountDAOOracleImp这个类型对应的所有方法都切入共通业务逻辑。

    com.xdl.dao.*      dao包下所有的类型,对应的方法都将被切入共通业务逻辑

    com..*        com包下所有的类型以及com的子包下所有的类型对应的方法都将被切入共通业务逻辑

    ```
      <aop:before method="printSysTime"   pointcut="within(com..*)"/>
    ```

  - 方法限定表达式

    execution(方法限定)   具体如下:

      execution(权限修饰  返回值类型  方法名(参数列表) throws 异常)

    其中返回值类型,方法名()是必须的  

  ```
    <aop:before method="printSysTime"   pointcut="execution(*  *())"/>
  ```  

## AOP中的五种通知类型 

   <aop:before   前置通知     目标方法调用之前调用

   <aop:after    最终通知     目标方法调用后,一定会调用 

   <aop:after-returning   后置通知    目标方法调用之后调用 

   <aop:after-throwing    异常通知    目标方法调用出现异常 采用调用 

   <aop:around    环绕通知      目标方法调用前后都调用  

## 实现AOP的步骤(使用组件扫描)

  - 建立项目,导入jar包(ioc aop)拷贝配置文件到src

  - 开启组件扫描,在applicationContext.xml里输入

  ```xml
    <context:component-scan base-package="com.xdl"></context:component-scan>
  ```
  
  - 编写DAO接口

  ```java
	package com.xdl.dao;
	public interface XdlBankAccountDAO {
	    void   insertAccount();
	    void   deleteAccount();
	}
  ```

  - 编写实现类

  ```java
    package com.xdl.dao;
	import org.springframework.stereotype.Repository;
	@Repository("accountDao")
	public class XdlBankAccountDAOOracleImp implements XdlBankAccountDAO {
		@Override
		public void insertAccount() {
			System.out.println("增加银行账户");
			//throw  new RuntimeException("抛出运行时异常");
		}

		@Override
		public void deleteAccount() {
			System.out.println("删除银行账户");
		}
	}
  ```

  - 开启标注形式的aop

  ```
    <aop:aspectj-autoproxy   proxy-target-class="true" />
  ```

  - 编写一个切面类,打上@Component和@Aspect,然后定义并标注切面方法打印######

  ```java
    package com.xdl.aspect;
	import org.aspectj.lang.annotation.Aspect;
	import org.aspectj.lang.annotation.Before;
	import org.springframework.stereotype.Component;
	@Component
	@Aspect
	public class XdlLogAspect {
		@Before("bean(accountDao)")
	    public  void   printSixStarts() {
	        System.out.println("######");	
	    }	    
	}
  ```

  - 编写测试类

  ```java
    package com.xdl.test;
	import org.springframework.context.ApplicationContext;
	import org.springframework.context.support.ClassPathXmlApplicationContext;
	import com.xdl.dao.XdlBankAccountDAO;
	import com.xdl.dao.XdlBankAccountDAOOracleImp;
	public class XdlBankAccountDAOTest {
		public static void main(String[] args) {
			ApplicationContext  app  = 
				new  ClassPathXmlApplicationContext("applicationContext.xml");
	        XdlBankAccountDAO  accountDao = app.getBean("accountDao",
	        	XdlBankAccountDAO.class);
	        accountDao.insertAccount();
	        accountDao.deleteAccount();
		}
	}
  ```

## AOP中五种通知对应的标注 

  前置通知      @Before

  后置通知      @AfterReturning 

  最终通知      @After

  异常通知      @AfterThrowing

  环绕通知      @Around

  - 重新编写Aspect类,显示执行的时间, 什么时间什么方法出了什么异常,统计一下方法的执行时间   

  ```java
    package com.xdl.aspect;
	import org.aspectj.lang.JoinPoint;
	import org.aspectj.lang.ProceedingJoinPoint;
	import org.aspectj.lang.annotation.AfterReturning;
	import org.aspectj.lang.annotation.AfterThrowing;
	import org.aspectj.lang.annotation.Around;
	import org.aspectj.lang.annotation.Aspect;
	import org.springframework.stereotype.Component;
	import java.text.SimpleDateFormat;
	import java.util.Date;
	@Component
	@Aspect
    
    public class XdlSysTimeAspect {
		@AfterReturning("within(com.xdl..*)")
	    public  void   printSysTime() {
	    	Date  date  = new Date();
	    	SimpleDateFormat  sdf = new SimpleDateFormat("yyyy-MM-dd hh:mm:ss");
	    	String dateStr  = sdf.format(date);
	    	System.out.println(dateStr + "@" + date.getTime());
	    }
		
		/** 异常通知 */
		@AfterThrowing(pointcut="within(com.xdl..*)",throwing="e")
		public  void    processException(JoinPoint  jp,Exception  e) {
			Date  date  = new Date();
	    	SimpleDateFormat  sdf = new SimpleDateFormat("yyyy-MM-dd hh:mm:ss");
	    	String dateStr  = sdf.format(date);
	    	System.out.println(dateStr + "||" +jp.getSignature()+"||" +e);
			System.out.println("出大事了");
		}
		
		/** 环绕通知 
		 * @throws Throwable */
		@Around("within(com.xdl..*)")
		public  Object  callMethodTimes(ProceedingJoinPoint  pjp) throws Throwable {
			long  startTime = System.currentTimeMillis();
			// 调用目标方法
			Object obj = pjp.proceed();
			long  endTime = System.currentTimeMillis();
			System.out.println(pjp.getSignature() +":用了" + (endTime-startTime) + "毫秒" );		    	
			return  obj;
		}
	}
  ```

  
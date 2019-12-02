---
title: Java_Spring框架(5)之MVC的使用、Controller组件的接收和发送数据
tags: java Spring MVC
categories: java
---

* TOC
{:toc}

## 什么是MVC

  Spring提供了一个Web MVC框架，便于开发MVC结构的Java Web程序。

  Spring MVC框架控制器为DispatcherServlet，DispatcherServlet负责接收请求，然后将请求分发到不同的处理器进行业务处理，最后由控制器完成转发动作。

  Spring Web MVC提供了M、V和C相关的实现，主要实现组件如下

  DispatcherServlet （控制器, 请求入口）

  HandlerMapping （控制器, 请求派发）
  
  Controller （控制器, 请求处理流程）
  
  ModelAndView （模型, 封装处理结果和视图）
  
  ViewResolver（视图, 视图显示处理器）

## MVC的应用

  - 创建web项目工程

  - 添加ioc和webmvc开发包(也可以加AOP开发包)

  - 在web.xml中配置DispatcherServlet并通过初始化参数contextConfigLocation来指定spring配置文件的位置

  ```xml
    <!-- 配置DispatcherServlet  -->
    <servlet>
       <servlet-name>SpringMVC</servlet-name>
       <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
       <init-param>
           <param-name>contextConfigLocation</param-name> 
           <param-value>classpath:applicationContext.xml</param-value>
       </init-param>
    </servlet>  
    <servlet-mapping>
       <servlet-name>SpringMVC</servlet-name>
       <url-pattern>*.do</url-pattern>
    </servlet-mapping>
  ```

  - 设计请求到响应处理流程

  - 编写Controller组件和JSP页面

  > 不使用AOP

  ```java
    import javax.servlet.http.HttpServletRequest;
    import javax.servlet.http.HttpServletResponse;
    import org.springframework.web.servlet.ModelAndView;
    import org.springframework.web.servlet.mvc.Controller;
	public class XdlLoginController implements Controller {
		@Override
		public ModelAndView handleRequest(HttpServletRequest request,
			HttpServletResponse response) throws Exception {
			ModelAndView  mav  = new ModelAndView();
			mav.setViewName("login");
			return mav;
		}
	}
  ```

  > 使用AOP

  ```java
    import org.springframework.stereotype.Controller;
    import org.springframework.web.bind.annotation.RequestMapping;

	@Controller
	public class XdlLoginController {
	    @RequestMapping("/toLogin.do")
		public String  toLogin() {
			return  "login";
		}
	}
  ```

  - 添加Spring配置文件applicationContext.xml,在applicationContext.xml配置Controller组件,HandlerMapping组件,ViewResolver组件

  > 不使用AOP

  ```xml
    <!--  配置HandlerMapping的实现类   通过mappings 属性 建立请求和控制器的对应关系 -->	
	<bean  id="handlerMapping" 
	    class="org.springframework.web.servlet.handler.SimpleUrlHandlerMapping">
	    <property name="mappings">
	        <props>
	            <prop key="/toLoging.do">loginController</prop>
	        </props>  
	    </property>     
	</bean>
    <!--  配置Controller 对象  -->
    <bean  id="loginController"  class="com.xdl.controller.XdlLoginController">
    
    </bean>	
	<!--  配置ViewResolver --> 
    <bean  id="viewResolver"  class="org.springframework.web.servlet.view.InternalResourceViewResolver">
        <property name="prefix"  value="/WEB-INF/"></property>
        <property name="suffix"  value=".jsp"></property>
    </bean>
  ```

  > 使用AOP

  ```xml
    <!-- 开启组件扫描 -->
	<context:component-scan base-package="com.xdl"></context:component-scan>
	<!-- 开启标注形式的mvc -->
	<mvc:annotation-driven />

	<!-- 配置ViewResolver -->
	<bean id="viewResolver"
		class="org.springframework.web.servlet.view.InternalResourceViewResolver">
		<property name="prefix" value="/WEB-INF/"></property>
		<property name="suffix" value=".jsp"></property>
	</bean>
  ``` 
 
![springMVC注解配置](https://raoweijiapng.github.io/static/img/java/springMVC注解配置.png)

## Controller组件接收请求参数

  在Controller组件处理中，如果需要接收请求参数，可以使用下面的方法

  使用HttpServletRequest获取

  使用@RequestParam注解

  使用自动机制封装成Bean对象

  ```java
	public class XdlBankAccount {		
	    public XdlBankAccount(int id, String acc_no, String acc_password, double acc_money) {
			super();
			this.id = id;
			this.acc_no = acc_no;
			this.acc_password = acc_password;
			this.acc_money = acc_money;
		}
		public XdlBankAccount() {
			super();
			// TODO Auto-generated constructor stub
		}
		@Override
		public String toString() {
			return "XdlBankAccount [id=" + id + ", acc_no=" + acc_no + ", acc_password=" + acc_password + ", acc_money="
					+ acc_money + "]";
		}
		public int getId() {
			return id;
		}
		public void setId(int id) {
			this.id = id;
		}
		public String getAcc_no() {
			return acc_no;
		}
		public void setAcc_no(String acc_no) {
			this.acc_no = acc_no;
		}
		public String getAcc_password() {
			return acc_password;
		}
		public void setAcc_password(String acc_password) {
			this.acc_password = acc_password;
		}
		public double getAcc_money() {
			return acc_money;
		}
		public void setAcc_money(double acc_money) {
			this.acc_money = acc_money;
		}
		private  int   id;   
	    private  String acc_no;
	    private  String acc_password;
	    private  double  acc_money;
	}
  ```

  ```java
	import com.xdl.bean.XdlBankAccount;
	import org.springframework.stereotype.Controller;
	import org.springframework.web.bind.annotation.RequestMapping;
	import org.springframework.web.bind.annotation.RequestParam;
	import javax.servlet.http.HttpServletRequest;
	@Controller
	public class XdlLoginController {
	    @RequestMapping("/toLogin.do")
		public String  toLogin() {
			return  "login";
		}
		@RequestMapping("/login.do")
		public String  login(HttpServletRequest request) {
			String  acc_no = request.getParameter("acc_no");
			String  acc_password = request.getParameter("acc_password");
			System.out.println(acc_no + ":" + acc_password);
			return  "main";
		}
		@RequestMapping("/login2.do")
		public String  login2(String acc_no,String acc_password) {
			System.out.println(acc_no + ":" + acc_password);
			return  "main";
		}
		@RequestMapping("/login3.do")
		public String  login3(@RequestParam("acc_no")String accNo,
							  String acc_password) {
			System.out.println(accNo + "@:" + acc_password);
			return  "main";
		}
		@RequestMapping("/login4.do")
		public String  login4(XdlBankAccount account) {
			System.out.println(account);
			return  "main";
		}	
	}
  ```

  ```jsp
	<%@ page language="java" contentType="text/html; charset=utf-8"
	    pageEncoding="utf-8"%>
	<!DOCTYPE html>
	<html>
	<head>
	<meta charset="utf-8">
	<title>登录</title>
	</head>
	<body>
		<form action="login4.do">
			账号:<input type="text" name="acc_no" /> <br>
			 密码: <input type="password" name="acc_password" /> <br> 
			<input  type="submit" value="登录">
		</form>
	</body>
	</html>
  ```

## 把Controller组件的数据传递给jsp页面

  - 使用域对象传递数据(request,session)

  Controller组件

  ```java
    @RequestMapping("/register1.do")
    public  String   register(String acc_no,String acc_password,HttpServletRequest request) {
		if(!"abc".equals(acc_no) && "123".equals(acc_password)) {
		    //request.setAttribute("acc_no", acc_no);
			request.getSession().setAttribute("acc_no", acc_no);
			return  "login";
		}else {
			return  "register";
		}    	
    }
  ```

  jsp页面

  ```
    request acc_no : ${requestScope.acc_no}
	session acc_no : ${sessionScope.acc_no }
  ```

  - 使用ModelAndView传递数据

  Controller组件

  ```java
    @RequestMapping("/register2.do")
    public  ModelAndView   register2(String acc_no,String acc_password,ModelAndView  mav) {
		if(!"abc".equals(acc_no) && "123".equals(acc_password)) {
		    //mav.getModel().put("acc_no", acc_no);
		    //mav.getModelMap().put(key, value)
		    mav.getModelMap().addAttribute("acc_no", acc_no);
		    mav.setViewName("login");
		}else {
		    mav.setViewName("register");
		}
    	return  mav;
    }
  ```

  jsp页面

  ```
    request acc_no : ${requestScope.acc_no}
  ```

  - 使用Model传递数据

  Controller组件

  ```java
	@RequestMapping("/register3.do")
    public  String   register3(String acc_no,String acc_password,
    	Model  m) {
		if(!"abc".equals(acc_no) && "123".equals(acc_password)) {
		    //request.setAttribute("acc_no", acc_no);
			m.addAttribute("acc_no", acc_no);
			return  "login";
		}else {
			return  "register";
		}    	
    }
  ```

  jsp页面

  ```
    request acc_no : ${requestScope.acc_no}
  ```

  - 使用ModelMap传递数据  

  Controller组件

  ```java
	@RequestMapping("/register4.do")
    public  String   register4(String acc_no,String acc_password,
    	ModelMap  mm) {
		if(!"abc".equals(acc_no) && "123".equals(acc_password)) {
		    //mm.addAttribute("acc_no", acc_no);
			mm.put("acc_no", acc_no);
			return  "login";
		}else {
			return  "register";
		}    	
    }
  ```

  jsp页面

  ```
    request acc_no : ${requestScope.acc_no}
  ```

  - 使用自定义类型的控制器参数直接进行传递

  ```java
   	@RequestMapping("/register5.do")
    public  String   register5(@ModelAttribute("account") XdlBankAccount  account) {	
    	return  "login";
    }
  ```

  ```
	request obj  acc_no :${requestScope.account.acc_no } 
	session obj  acc_no :${sessionScope.account.acc_no } 
	如果没有ModelAttribute,则名字为默认的首字母小写
	request obj  acc_no :${requestScope.xdlBankAccount.acc_no } 
	session obj  acc_no :${sessionScope.xdlBankAccount.acc_no }
  ```



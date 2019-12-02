---
title: Java_Spring框架(6)之Controller的重定向、post的中文乱码、拦截器和异常处理
tags: java Spring MVC
categories: java
---

* TOC
{:toc}

## Controller组件实现重定向

  - 控制器方法返回String

  ```java
  	@RequestMapping("/register6.do")
    public  String   register6(
    	@ModelAttribute("account") XdlBankAccount  account) {
    	//不能重定向jsp,只能通过RequestMapping跳转到jsp上
		//return  "redirect:toLogin.do";
		//转发,account能传递过去
		return  "toLogin.do";
		//默认为return  "forward:toLogin.do";
    }
  ```
         
  - 控制器方法返回ModelAndView

  ```java
  	@RequestMapping("/register7.do")
    public  ModelAndView   register7(
    	@ModelAttribute("account") XdlBankAccount  account,ModelAndView  mav) {	
    	RedirectView  rv  = new RedirectView("toLogin.do");
		mav.setView(rv);
		return  mav;
    }
  ```

## 中文参数乱码问题

  - tomcat8的get方式没有乱码

  - tomcat8的post方式,通过RequestMapping的请求必须是post方式

  > 之前的解决方案依然可用,但必须完全遵守之前的方式 

  ```java
  	@RequestMapping("/register8.do")
    public   String   register8(HttpServletRequest  request) {
		try {
			request.setCharacterEncoding("utf-8");
		} catch (UnsupportedEncodingException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		}
		String  acc_no = request.getParameter("acc_no");
		System.out.println("acc_no:"+acc_no);
		return "login";
	}
  ```

  > 在webxml配置编码过滤器,把Post请求\*.do的编码方式设为utf-8

  ```
    <!--  配置一个编码过滤器  -->
    <filter>
       <filter-name>CharacterEncoding</filter-name>
       <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
       <init-param>
           <param-name>encoding</param-name>
           <param-value>utf-8</param-value>
       </init-param>
    </filter>
    <filter-mapping>
       <filter-name>CharacterEncoding</filter-name>
       <url-pattern>*.do</url-pattern>
    </filter-mapping>
  ```
    
  ```java  
	@RequestMapping("/register9.do")
    public  String  register9(String acc_no,String acc_password,HttpServletRequest  request) {
		acc_no = request.getParameter("acc_no");
		System.out.println("acc_no:"+acc_no);
		return "login";
	}   
  ```

## 拦截器

  - 拦截器接口的三个方法,调用时间请参考[MVC注解配置图](https://raoweijiapng.github.io/java/Java_Spring框架(5)/)

    preHandle()   

    在HandlerMapping之后,Controller之前调用,这个方法返回false,则代表终止后续调用;如果返回true,则代表继续后续调用。
    
    postHandle()   

    在控制器之后,视图处理器之前

    afterCompletion()   

    在视图处理器之后,响应之前 

  - 拦截器的使用步骤 

  a.搭建一个基于标注的spring MVC(让用户发起登录,如果账号不是abc,密码是123,则登录成功;登录成功就把账号放入session中,重定向到 main.jsp。如果登录失败则提示用户登录失败。)

  ```java
    @RequestMapping("/login.do")
    public   String   login(String acc_no,String  acc_password,
	   HttpServletRequest  request) {
	   if(!"abc".equals(acc_no)  && "123".equals(acc_password)) {
		   request.getSession().setAttribute("acc_no", acc_no);   
		   return  "redirect:toMain.do";
	   }else {
		   request.setAttribute("msg", "登录失败");
		   return  "login";
	   }
    }

    @RequestMapping("/toMain.do")
    public  String   toMain() {
   	   return  "main";
    }
  ```

  b.写一个拦截器,实现HandlerInterceptor接口

  ```java
	package com.xdl.interceptor;
	import javax.servlet.http.HttpServletRequest;
	import javax.servlet.http.HttpServletResponse;
	import org.springframework.web.servlet.HandlerInterceptor;
	import org.springframework.web.servlet.ModelAndView;
	public class MyCheckLoginInterceptor implements HandlerInterceptor {
		@Override
		public void afterCompletion(HttpServletRequest arg0, HttpServletResponse arg1, Object arg2, Exception arg3)
				throws Exception {
			System.out.println("afterCompletion");
		}

		@Override
		public void postHandle(HttpServletRequest arg0, HttpServletResponse arg1, 
				Object arg2, ModelAndView arg3)
				throws Exception {
			System.out.println("postHandle");
		}

		@Override
		public boolean preHandle(HttpServletRequest request, HttpServletResponse response, 
			Object method) throws Exception {
			//System.out.println("preHandle:"+method);
			// 如果session 中有 acc_no  就放行
			Object  acc_no = request.getSession().getAttribute("acc_no");
			if(acc_no != null) {
			    return true;
			}else {
				response.sendRedirect("toLogin.do");
				return false;
			}    
		}
	}
  ```

  c.在Spring配置文件中配置拦截器  

  ```
    <!--  配置拦截器  -->
	<mvc:interceptors>
	     <mvc:interceptor>
	          //拦截的路径  比如 /*  拦截所有的一级请求   /** 拦截所有请求
 	          <mvc:mapping path="/**"/>
 	          //不拦截的请求 可以有多个
	          <mvc:exclude-mapping path="/toRegister.do"/>
	          <mvc:exclude-mapping path="/regist.do" /
	          //拦截器对象
	          <bean class="com.xdl.interceptor.MyCheckLoginInterceptor"></bean>
	     </mvc:interceptor>
	</mvc:interceptors>
  ```

## 异常处理

  - 配置一个系统提供的异常处理器 

  ```
    <!--  配置系统提供的异常处理器  -->
	<bean  id="exceptionResolver"  
	    class="org.springframework.web.servlet.handler.SimpleMappingExceptionResolver">
	    <property name="exceptionMappings">
	        <props>
	            <prop key="java.lang.Exception">error2</prop> 
	            <prop key="java.lang.RuntimeException">error</prop> 
	        </props>
	    </property>
	</bean>
  ```

  ```java
    @RequestMapping("/toLogin.do")
    public  String   toLogin() throws Exception {
		if(1==1) {
			//throw  new  RuntimeException("出大事了 --- 运行时异常");
			throw  new  Exception("出大事了---检查异常");
		}
    	return  "login";
    }

    //访问toLogin.do时,发生异常,出现什么异常就跳转到处理器定义的error.jsp(web-inf下的)
  ```
	
  - 全局处理 --- 自定义异常处理器

  > 实现HandlerExceptionResolver,让对应的异常跳转到对应的页面 
  
  ```java
    package com.xdl.resolver;
	import org.springframework.stereotype.Controller;
	import org.springframework.web.servlet.HandlerExceptionResolver;
	import org.springframework.web.servlet.ModelAndView;
	import javax.servlet.http.HttpServletRequest;
	import javax.servlet.http.HttpServletResponse;
	@Controller
	public class MyHandlerExceptionResolver implements HandlerExceptionResolver {
		@Override
		public ModelAndView resolveException(HttpServletRequest arg0, 
			HttpServletResponse arg1, Object arg2,
				Exception e) {
			ModelAndView  mav  = new ModelAndView();
			// 出现什么样的异常  就跳转到对应的页面 
			if(e instanceof RuntimeException) {
				mav.setViewName("error");
			}else if(e instanceof Exception) {
				mav.setViewName("error2");
			}
			return mav;
		}
	}
  ```

  - 局部异常处理 --- 只针对一个具体的控制器起作用(异常方法中Exception的参数必须有)

  > 在Controller组件里加入一个方法

  ```
    @RequestMapping("/toLogin.do")
    public  String   toLogin() throws Exception {
		if(1==1) {
			//throw  new  RuntimeException("出大事了 --- 运行时异常");
			throw  new  Exception("出大事了---检查异常");
		}
    	return  "login";
    }

    //此方法只针对本类中发生的Exception
    @ExceptionHandler
	public  String  foo(Exception  e) {
		System.out.println(e.getMessage());
		//getMessage是上述抛出异常时()里的字符串
		return  "error3";
	}   
  ```


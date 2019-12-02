---
title: Java_Spring框架(7)之文件上传、Controlle返回json、MVC对rest架构的支持
tags: java Spring MVC Rest
categories: java
---

* TOC
{:toc}

## 文件上传
  - 写一个文件上传的页面    

  > 第一必须以post提交,enctype="multipart/form-data",type="file"

  ```
	<%@ page language="java" contentType="text/html; charset=utf-8"
	    pageEncoding="utf-8"%>
	<!DOCTYPE html>
	<html>
	<head>
	<meta charset="utf-8">
	<title>Insert title here</title>
	</head>
	<body>
	     <form action="upload.do"   method="post"  enctype="multipart/form-data">
	                      账号:<input type="text"   name="acc_no" /> <br>
	                     头像:<input  type="file"   name="head_img"> <br>
	        <input  type="submit"  value="上传">                
	     </form>
	</body>
	</html>
  ```
    
  - 编写一个文件上传的控制器接收文件数据和非文件数据 

  > 这个控制器需要依赖于文件解析器,文件解析器依赖于文件上传的jar 

  ```xml
    在applicationContext.xml文件中输入
    <!--  文件解析器  -->
	<bean  id="multipartResolver" 
	    class="org.springframework.web.multipart.commons.CommonsMultipartResolver">
	    <property name="maxUploadSize"  value="10240"></property>
	    <property name="resolveLazily"  value="true"></property>
	</bean>
  ```  

  ```java
	import org.springframework.stereotype.Controller;
	import org.springframework.web.bind.annotation.ExceptionHandler;
	import org.springframework.web.bind.annotation.RequestMapping;
	import org.springframework.web.multipart.MultipartFile;
	import java.io.File;
	import java.io.IOException;
	import java.util.UUID;
	@Controller
	public class XdlFileUploadController {
	     @RequestMapping("/toUpload.do")
		 public   String  toUpload() {
			 return  "upload";
		 }
	     @RequestMapping("/upload.do")
		 public   String  upload(String acc_no,MultipartFile  head_img) {
	    	 System.out.println(acc_no);
	    	 System.out.println(head_img.getName() + ":" + head_img.getOriginalFilename()
	    	 +":" + head_img.getSize()+"gg");
	    	 // 如果超过 10k  就抛出文件过大的异常
	    	 if(head_img.getSize() > 10240) {
	    		 throw  new RuntimeException("文件过大");
	    	 }	    	 
	    	 String  oriFileName = head_img.getOriginalFilename();
	    	 String  fileSuffix = oriFileName.substring(oriFileName.lastIndexOf("."));
	    	 String  fileName = UUID.randomUUID().toString();
	    	 String  filePath = "D:/datas/" + fileName + fileSuffix;
	    	 File  file  = new File(filePath);
	    	 try {
	    		 // 把文件写入到磁盘
				head_img.transferTo(file);
			} catch (IllegalStateException e) {
				e.printStackTrace();
			} catch (IOException e) {
				e.printStackTrace();
			}
			 return  "main";
		 }
	     @ExceptionHandler
	     public   String  processError(Exception e) {
	    	 return  "error4";
	     }     
	}
  ```

## MVC的控制器方法返回JSON

  - 搭建一个基于标注的MVC

  - 在控制器中,编写一个控制器方法 

  ```
    @RequestMapping("/请求的路径")
    public  要转换成JSON的数据类型  方法名任意(参数任意){
  
    }
  ```

  - 为了处理返回的数据为JSON,需要加一个@ResponseBody,此标注加载类上是代表所有方法返回的都是json

  > 把数据转换成JSON同时告知mvc返回值不要经过视图处理器,这个标注要依赖于JSON的转换包

  ```java
  @RequestMapping("/login.do")
	@ResponseBody
	public  boolean   login(String acc_no,String acc_password) {
		if("luqi".equals(acc_no) && "123".equals(acc_password)) {
			return  false;
		}
		return  true;
	}

	@RequestMapping("/login2.do")
	@ResponseBody
	public  XdlBankAccount   login2(String acc_no,String acc_password) {
		if("abc".equals(acc_no) && "123".equals(acc_password)) {
			return  new  XdlBankAccount(0, acc_no, acc_password, 1);
		}
		return  null;
	}

	@RequestMapping("/login3.do")
	@ResponseBody
	public  Map<String,Object>  login3(String acc_no,String acc_password) {
		Map<String,Object>  res = new HashMap<String, Object>();
		if("abc".equals(acc_no) && "123".equals(acc_password)) {
			XdlBankAccount account = new  XdlBankAccount(0, acc_no, acc_password, 1);
			res.put("account", account);
			return  res;
		}
		res.put("account", null);
		return  res;
	}
  ```

## 什么是rest

  REST即表述性状态传递（英文：Representational State Transfer，简称REST）

  它是Roy Fielding博士在2000年他的博士论文中提出来的一种软件架构风格。

  这个软件架构基于http协议,它可以提高系统的可伸缩性,降低应用之间的耦合度,便于分布式应用程序的开发。

## rest的两个核心规范 

  对定位资源的URL做了限定,把原来基于操作的设计,改成了基于资源的设计 
  
  对资源的操作的做了限定,Http协议的请求方式上,Get用来查询,Post增加,Put更新,Delete删除

## 什么是RESTful

  符合REST约束风格和原则的应用程序或设计就是RESTful

  /blog/1   HTTP GET => 查询id=1的blog

  /blog/1   HTTP DELETE => 删除id=1的blog

  /blog/1   HTTP PUT => 更新blog

  /blog/new HTTP POST => 新增blog

## MVC对rest的支持

  - 搭建一个基于标注的 MVC

  - 在控制器方法上,支持对应的请求方式

  @RequestMapping(value="/account/{变量名}",method=RequestMethod.GET)

  - 获取路径变量上的值 

  @PathVariable("路径变量名")加在控制器参数上 

  - rest请求的路径是没有后缀的.所以需要把web,xml里的DispatcherServlet中的url-parttern改成支持所有请求的/

  - 如果改成/,则所有的静态资源被拦截,所以放行静态资源,在applicationContext.xml里加入

  ```
    <mvc:default-servlet-handler/>
  ```

  ```java
    /** 基于rest 做一个根据id  查询银行账户 */
	@RequestMapping(value="/account/{id}",method=RequestMethod.GET)
	@ResponseBody
	public  XdlBankAccount  findAccountById(@PathVariable("id") int  id) {
		// 使用Service  从数据库中获取一个银行账户 
		XdlBankAccount  account  = new XdlBankAccount(id, "test", "123", 1);
		return  account;
	}

    /** 基于rest 做一个根据id  删除银行账户 */
	@RequestMapping(value="/account/{id}",method=RequestMethod.DELETE)
	@ResponseBody
    public  boolean   accountRemoveById(@PathVariable("id") int  id) {
    	if(id > 10) {
    		return  false;
    	}
    	return true;
    }	
	
	/** 基于rest 做一个根据id  增加银行账户 */
	@RequestMapping(value="/account/{id}",method=RequestMethod.POST)
	@ResponseBody
    public  boolean   accountRemoveById(XdlBankAccount  account) {
    	System.out.println(account);
    	return  true;
    }	
	
	/** 基于rest 做一个根据id  更新银行账户 */
	@RequestMapping(value="/account/{id}",method=RequestMethod.PUT)
	@ResponseBody
    public  boolean   accountUpdate(@RequestBody XdlBankAccount  account) {
    	System.out.println(account);
    	return  true;
    }	
  ```

  
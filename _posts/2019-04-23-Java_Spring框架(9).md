---
title: Java_Spring框架(9)之Mybatis的定义和使用
tags: java Spring Mybatis
categories: java
---

* TOC
{:toc}

## Mybatis的作用

  它支持普通的SQL操作以及存储过程的调用 

  它是一个高级的ORM框架 (以面向对象的思想操作数据库)

  它封装了几乎的jdbc操作以及参数的手工设置 

  自动检索结果集(自动把结果集转换成对象甚至关联的对象) 

## MyBatis框架的构成

  实体bean     封装数据信息 
  
  SQL定义文件  封装SQL语句的XML
  
  主配置文件   定义连接数据库的信息的加载sql定义文件等
  
  框架的API    涉及到SqlSession对象的创建,还有SqlSession对应的API,主要完成增删改查

## 以根据id查询银行账户为例,编写Mybatis程序

  - 建立一个项目,导入jar包(mybatis.jar ojdbc14.jar)

  - 根据表,建立对应的实体类

  ```java
	package com.xdl.bean;
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

  - 编写SQL定义文件 (拷贝sql定义的模板到一个包中)

  ```xml
    <?xml version="1.0" encoding="UTF-8" ?>  
	<!DOCTYPE mapper PUBLIC "-//ibatis.apache.org//DTD Mapper 3.0//EN"      
	 "http://ibatis.apache.org/dtd/ibatis-3-mapper.dtd">
	 <!-- namespace指定和哪个Mapper映射器接口对应 -->
	<mapper namespace="abc">
		<!-- 定义SQL语句 -->	
	    <select id="findAccountById" parameterType="int" 
	      resultType="com.xdl.bean.XdlBankAccount">
	         select * from xdl_bank_account_30 where  id = #{id}
	    </select>
	</mapper>
  ```

  - 拷贝主配置文件模板到src下,修改对应的信息

  ```xml
	<?xml version="1.0" encoding="UTF-8" ?> 
	<!DOCTYPE configuration  PUBLIC "-//ibatis.apache.org//DTD Config 3.0//EN" "http://ibatis.apache.org/dtd/ibatis-3-config.dtd">
	<configuration>
		<environments default="environment">
			<environment id="environment">
				<transactionManager type="JDBC" />
				<dataSource type="POOLED">
					<property name="driver" 
						value="oracle.jdbc.OracleDriver" />
					<property name="url"
						value="jdbc:oracle:thin:@localhost:1521:XE"/>
					<property name="username" value="system" />
					<property name="password" value="123456" />
				</dataSource>
			</environment>
		</environments>
		<mappers>
			<mapper resource="com/xdl/mapper/XdlBankAccountMapper.xml" />
		</mappers>
	</configuration> 
  ```
  - 使用Mybatis的API获取SqlSession对象,使用这个对象完成对应的sql操作

  ```java
    package com.xdl.test;
	import java.io.InputStream;
	import org.apache.ibatis.session.SqlSession;
	import org.apache.ibatis.session.SqlSessionFactory;
	import org.apache.ibatis.session.SqlSessionFactoryBuilder;
	import com.xdl.bean.XdlBankAccount;
	public class XdlBankAccountTest {
		public static void main(String[] args) {
			// 先获取SqlSession
			//  sqlSession 工厂构建器对象
			SqlSessionFactoryBuilder  ssfb = new SqlSessionFactoryBuilder();
			// 得到一个SqlSessionFactory 类型的对象 
			InputStream  inputStream = XdlBankAccountTest.class
				.getClassLoader().getResourceAsStream("sqlmap-config.xml");
			SqlSessionFactory  ssf = ssfb.build(inputStream);
			//  获取SqlSession
			SqlSession  ss = ssf.openSession();
			// 执行对应的sql
			XdlBankAccount  account = ss.selectOne("findAccountById", 11);
	        System.out.println(account); 
	        ss.close();
		}
	}
  ```
 
  - 根据账号查询银行账户,根据id查询银行账号,根据账号和密码查询银行账户

  > 封装获取SqlSession对象的方法

  ```java
	package com.xdl.util;
	import java.io.InputStream;
	import org.apache.ibatis.session.SqlSession;
	import org.apache.ibatis.session.SqlSessionFactory;
	import org.apache.ibatis.session.SqlSessionFactoryBuilder;
	public class SqlSessionUtil {
	    private  static  SqlSessionFactory ssf;
		static {
			// sqlSession 工厂构建器对象
			SqlSessionFactoryBuilder ssfb = new SqlSessionFactoryBuilder();
			// 得到一个SqlSessionFactory 类型的对象
			InputStream inputStream = SqlSessionUtil.class
				.getClassLoader().getResourceAsStream("sqlmap-config.xml");
		    ssf = ssfb.build(inputStream);
		}
		/** 提供静态方法 可以获取SqlSession */
		public static  SqlSession  getSqlSession() {
			try {
				return  ssf.openSession();
			} catch (Exception e) {
				e.printStackTrace();
			}
			return  null;
		}
	}
  ``` 
        
  > 修改SQL定义的xml文件

  ```xml
  	<?xml version="1.0" encoding="UTF-8" ?>  
	<!DOCTYPE mapper PUBLIC "-//ibatis.apache.org//DTD Mapper 3.0//EN"      
	 "http://ibatis.apache.org/dtd/ibatis-3-mapper.dtd">
	 <!-- namespace指定和哪个Mapper映射器接口对应 -->
	<mapper namespace="abc">
		<!--  根据id查询银行账户  -->
	    <select id="findAccountById" parameterType="int" 
	      resultType="com.xdl.bean.XdlBankAccount">
	         select * from xdl_bank_account_30 where  id = #{id}
	    </select>

	    <!--  根据账号查询银行账户  -->
	    <select id="findAccountByAccNo" parameterType="string" 
	      resultType="com.xdl.bean.XdlBankAccount">
	         select * from xdl_bank_account_30 where  acc_no = #{acc_no}
	    </select>
	    
	    <!--  根据id查询银行账户的账号  -->
	    <select id="findAccountNoById" parameterType="int" 
	      resultType="string">
	         select acc_no from xdl_bank_account_30 where  id = #{id}
	    </select>

	    <!--  根据账号和密码查询银行账户  -->
	    <!--  查询参数为MAP集合  -->
	    <select id="findAccountByAccNoAndAccPassword"  parameterType="map"
	        resultType="com.xdl.bean.XdlBankAccount">
	        select * from  xdl_bank_account_30 where acc_no = #{acc_no}  
	            and  acc_password = #{acc_password}
	    </select> 
	    <!--  查询参数为XdlBankAccount对象  -->
	    <select id="findAccountByAccNoAndAccPassword2"  
	        parameterType="com.xdl.bean.XdlBankAccount"
	        resultType="com.xdl.bean.XdlBankAccount">
	        select * from  xdl_bank_account_30 where acc_no = #{acc_no}  
	            and  acc_password = #{acc_password}
	    </select>  
	</mapper>
  ```

  > 重新编写测试类

    ```java
      package com.xdl.test;
	  import java.util.HashMap;
	  import java.util.Map;
	  import org.apache.ibatis.session.SqlSession;
	  import org.junit.Test;
	  import com.xdl.bean.XdlBankAccount;
	  import com.xdl.util.SqlSessionUtil;
	  public class XdlBankAccountTest {
		  @Test
	      public  void  test1() {
	    	SqlSession  ss = SqlSessionUtil.getSqlSession();

	    	XdlBankAccount  account = ss.selectOne("findAccountByAccNo",
	    		"liweijie");
	    	System.out.println(account);

	    	String  acc_no = ss.selectOne("findAccountNoById", 1);
	    	System.out.println(acc_no);

	    	Map<String,Object>  params = new HashMap<String, Object>();
	    	params.put("acc_no", "liweijie");
	    	params.put("acc_password", "123");
	    	XdlBankAccount  account2 = ss.selectOne("findAccountByAccNoAndAccPassword",params);
	    	System.out.println(account2);

	    	XdlBankAccount  accountParams = new XdlBankAccount(0, "liweijie", "123", 0);
	    	XdlBankAccount  res = ss.selectOne("findAccountByAccNoAndAccPassword2",accountParams);
	    	System.out.println(res);
	      }
	  }
    ```

  - 查询所有的银行账户,增加银行账户,根据id删除银行账户

  > 修改SQL定义的xml文件

  ```xml
    <!--  查询所有的银行账户  -->
    <select id="findAll"  resultType="com.xdl.bean.XdlBankAccount">
         select * from xdl_bank_account_30
    </select> 
    <!--  增加逻辑  -->
    <insert id="insertAccount"  parameterType="com.xdl.bean.XdlBankAccount" >
         insert into  xdl_bank_account_30 values(xdl_bank_account_30_id_seq.nextval,
             #{acc_no},#{acc_password},#{acc_money})
    </insert> 
    <!--  删除逻辑 -->
    <delete id="deleteAccountById"  parameterType="int">
         delete from  xdl_bank_account_30  where id = #{id}
    </delete> 
  ```
  
  > 重新编写测试类

  ```java
  	List<XdlBankAccount> datas = ss.selectList("findAll");
    System.out.println(datas);
    System.out.println("---------------------");

    XdlBankAccount  account3 = new XdlBankAccount(0,"gg", "123", 18000);
    System.out.println(ss.insert("insertAccount", account3));
    ss.commit();
    
    System.out.println(ss.delete("deleteAccountById", 24));
    ss.commit();
  ```

## SqlSession对象的操作方法

  zupdate(..) 更新操作
  
  delete(..) 删除操作
  
  selectOne(..) 单行查询操作
  
  selectList(..) 多行查询操作

## Mapper映射器 

  根据规则设计DAO接口可以自动产生实现类 

  - 创建DAO接口

  > DAO接口的方法名必须和SQL定义文件中SQL语句的id保持一致 

  > 接口方法的返回值类型一般和resultType 保持一致

  > 查询语句如果返回单值,那使用resultType 
  
  > 查询语句如果可能返回多个值,则使用List<resultType对应的类型>
  
  > 对DML(insert delete update)可以是返回void,也可以返回int,推荐返回int

  > 接口方法的参数类型和parameterType保持一致,如果没有parameterType,则参数可以任意

  ```java
	package com.xdl.mapper;
	import java.util.List;
	import com.xdl.bean.XdlBankAccount;
	public interface XdlBankAccountDAO {
	     XdlBankAccount    findAccountById(int  id);
	     XdlBankAccount    findAccountByAccNo(String acc_no);
	     List<XdlBankAccount> findAll();
	     int   deleteAccountById(int  id);
	}
  ```

  > SQL定义文件中的namespace必须是包名.接口名

  ```
  <mapper namespace="com.xdl.mapper.XdlBankAccountDAO">
    ...
  </mapper>
  ```
    
  > 重新编写测试类

  ```java
    package com.xdl.test;
	import org.apache.ibatis.session.SqlSession;
	import com.xdl.mapper.XdlBankAccountDAO;
	import com.xdl.util.SqlSessionUtil;	 
	public class XdlBankAccountMapperTest {
		public static void main(String[] args) {
			// 使用SqlSession产生DAO的实现类
			SqlSession sqlSession =  SqlSessionUtil.getSqlSession();
			XdlBankAccountDAO  dao = 
				sqlSession.getMapper(XdlBankAccountDAO.class);
	        System.out.println(dao.findAccountById(1));
	        System.out.println(dao.deleteAccountById(1));
	        sqlSession.commit();
	        sqlSession.close();
		}
	}
  ```
      
   
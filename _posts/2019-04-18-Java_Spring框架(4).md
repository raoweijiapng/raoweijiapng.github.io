---
title: Java_Spring框架(4)之采用不继承JdbcDaoSupport的方式完成对数据库的操作、Spring事务
tags: java Spring AOP DAO
categories: java
---

* TOC
{:toc}

## 采用不继承JdbcDaoSupport的方式完成对数据库的操作

  需要自己定义一个JdbcTemplate类型的对象(在applicationContext.xml文件中定义组件对象),然后注入给DAO的实现类.使用自定义的模板对象完成对应的数据库操作。当然自定义模板对象也需要注入dataSource。

- 查询银行账户表中的账户的数量

  > 建立一张银行账户表,插入几条测试数据,提交

  ``` 
    create table  xdl_bank_account_30(
       id     number   constraint xdl_bank_account_30_id_pk 
          primary key,
       acc_no  varchar(30)  constraint xdl_bank_account_30_acc_no_uk
          unique,
       acc_password   varchar(30),
       acc_money      number   
    );
    insert into  xdl_bank_account_30  values(1,'liweijie','123',1234567);
    insert into  xdl_bank_account_30  values(2,'canglaoshi','123',123);
    commit;
  ```

  > 建立一个项目导入jar包(ioc、aop、dao、连接池、数据库驱动)，拷贝配置文件applicationContext.xml到src

  ```xml
	<?xml version="1.0" encoding="UTF-8"?>
	<beans xmlns="http://www.springframework.org/schema/beans"
		   xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
		   xmlns:context="http://www.springframework.org/schema/context"
		   xsi:schemaLocation="http://www.springframework.org/schema/beans
	          http://www.springframework.org/schema/beans/spring-beans.xsd
			  http://www.springframework.org/schema/context
			  http://www.springframework.org/schema/context/spring-context-4.1.xsd">

		<!--  开启组件扫描 -->
		<context:component-scan base-package="com.xdl"/>
		<!--  连接池对象的配置  -->
		<bean  id="dataSource" class="org.apache.commons.dbcp.BasicDataSource">
			<property name="driverClassName" >
				<value>oracle.jdbc.OracleDriver</value>
			</property>
			<property name="url" value="jdbc:oracle:thin:@localhost:1521:orcl">
			</property>
			<property name="username"  value="raoweijia">
			</property>
			<property name="password"  value="123">
			</property>
		</bean>
		<!--  创建一个JdbcTemplate 对象 -->
		<bean  id="jdbcTemplate"  class="org.springframework.jdbc.core.JdbcTemplate">
		    <constructor-arg  index="0"  ref="dataSource"></constructor-arg>
		</bean>
	</beans>
  ```

  > 编写DAO接口,查询银行账户表中的账户的数量

  ```java
	public interface XdlBankAccountDAO {
	    int    getAccountCount();
	}
  ```

  > 编写DAO的实现类,实现DAO接口,使用自己定义一个JdbcTemplate类型的对象提供的模板结合sql语句完成查询

  > 开启组件,在DAO实现类上打持久层标注,同时要给JdbcDaoSupport注入一个dataSource对象

  ```java
	import org.springframework.beans.factory.annotation.Autowired;
	import org.springframework.jdbc.core.JdbcTemplate;
	import org.springframework.stereotype.Repository;
	import com.xdl.dao.XdlBankAccountDAO;
	@Repository("accountDao")
	public class XdlBankAccountDAOOracleImp implements XdlBankAccountDAO {
		@Autowired
	    private  JdbcTemplate   jdbcTemplate;
		@Override
		public int getAccountCount() {
			String  sql = "select count(*) from xdl_bank_account_30";
			return  jdbcTemplate.queryForObject(sql, Integer.class);
		}
	}
  ```  
   
  > 创建Spring容器,获取DAO并进行测试 

  ```java
	import org.springframework.context.ApplicationContext;
	import org.springframework.context.support.ClassPathXmlApplicationContext;
	import com.xdl.bean.XdlBankAccount;
	import com.xdl.dao.XdlBankAccountDAO;
	public class XdlBankAccountDAOTest {
		public static void main(String[] args) {
			ApplicationContext  app  = 
				new ClassPathXmlApplicationContext("applicationContext.xml");
	        XdlBankAccountDAO dao = app.getBean("accountDao",XdlBankAccountDAO.class);
	        System.out.println(dao.getAccountCount());
	    }
	}
  ```
    
- 根据账号查询银行账户

  > 建立一张银行账户表,插入几条测试数据,提交    同上 

  > 建立一个项目,导入jar包(ioc、aop、dao、连接池、数据库驱动),拷贝配置文件到src  同上

  > 编写一个名字XdlBankAccount的bean文件下的类

  > 编写DAO接口,加入根据账号查询银行账户的方法

  ```java
  	public interface XdlBankAccountDAO {
      XdlBankAccount   getAccountByAccNo(String acc_no);
    }
  ```

  > 编写XdlBankAccountMapper类实现RowMapper接口,把得到的结果集转换成对象

  ```java
	import com.xdl.bean.XdlBankAccount;
	import org.springframework.jdbc.core.RowMapper;
	import java.sql.ResultSet;
	import java.sql.SQLException;
	// 把结果集转换成对象 
	public class XdlBankAccountMapper implements RowMapper<XdlBankAccount> {
		@Override
		public XdlBankAccount mapRow(ResultSet rs, int index) throws SQLException {
			XdlBankAccount  account  = new  XdlBankAccount(rs.getInt("id"),
				rs.getString("acc_no"), rs.getString("acc_password"),
				rs.getDouble("acc_money"));
			return account;
		}
	}
  ```

  > 编写DAO的实现类继承JdbcDaoSupport实现DAO接口,使用父类提供的模板结合sql语句完成查询 同上

  > 开启组件扫描在DAO实现类上打持久层标注,同时要给JdbcDaoSupport注入一个dataSource对象

  ```java
    @Repository("accountDao")
	public class XdlBankAccountDAOOracleImp extends JdbcDaoSupport implements XdlBankAccountDAO {
	    @Autowired
	    private  JdbcTemplate   jdbcTemplate;
	    @Override
		public XdlBankAccount getAccountByAccNo(String acc_no) {
			String  sql = "select * from xdl_bank_account_30 where acc_no = ?";
			try {
				return jdbcTemplate.query(sql, new XdlBankAccountMapper(),
					acc_no);
			} catch (DataAccessException e) {
				// TODO Auto-generated catch block
				e.printStackTrace();
			}
			return  null;
		}
    }
  ```

  > 创建Spring容器,获取DAO并进行测试 

  ```java
	  import com.xdl.dao.XdlBankAccountDAO;
	  import org.springframework.context.ApplicationContext;
	  import org.springframework.context.support.ClassPathXmlApplicationContext;
	  public class XdlBankAccountDAOTest {
		public static void main(String[] args) {
			ApplicationContext app =
				new ClassPathXmlApplicationContext("applicationContext.xml");
	        XdlBankAccountDAO dao  = app.getBean("accountDao",XdlBankAccountDAO.class);
            XdlBankAccount  account  = dao.getAccountByAccNo("liweijie");
            System.out.println(account);
        }
      }
  ```
     
- 根据账号给某个账户增加指定的钱数 

  > 建立一张银行账户表,插入几条测试数据,提交    同上 

  > 建立一个项目,导入jar包(ioc、aop、dao、连接池、数据库驱动),拷贝配置文件到src  同上

  > 编写一个名字XdlBankAccount的bean文件下的类

  > 编写DAO接口,加入根据账号查询银行账户的方法

  ```java
  	public interface XdlBankAccountDAO {
       int  updateAccountInMoney(XdlBankAccount  to,double money);
    }
  ```

  > 编写DAO的实现类继承JdbcDaoSupport实现DAO接口,使用父类提供的模板结合sql语句完成查询 同上

  > 开启组件扫描在DAO实现类上打持久层标注,同时要给JdbcDaoSupport注入一个dataSource对象

  ```java
    @Repository("accountDao")
	public class XdlBankAccountDAOOracleImp extends JdbcDaoSupport implements XdlBankAccountDAO {
	    @Autowired
	    private  JdbcTemplate   jdbcTemplate;
	    @Override
		public int updateAccountInMoney(XdlBankAccount to, double money) {
			String sql = "update xdl_bank_account_30 set acc_money = acc_money + ?  "
					+ "where  acc_no = ?";
			try {
				return  jdbcTemplate.update(sql, money,to.getAcc_no());
			} catch (DataAccessException e) {
				// TODO Auto-generated catch block
				e.printStackTrace();
			}
			return  0;
		}
    }
  ```

  > 创建Spring容器,获取DAO并进行测试 

  ```java
	  import com.xdl.dao.XdlBankAccountDAO;
	  import org.springframework.context.ApplicationContext;
	  import org.springframework.context.support.ClassPathXmlApplicationContext;
	  public class XdlBankAccountDAOTest {
		public static void main(String[] args) {
			ApplicationContext app =
				new ClassPathXmlApplicationContext("applicationContext.xml");
	        XdlBankAccountDAO dao  = app.getBean("accountDao",XdlBankAccountDAO.class);
	        XdlBankAccount  account  = dao.getAccountByAccNo("liweijie");
            int  f1 = dao.updateAccountInMoney(account, 1);
	        account  = dao.getAccountByAccNo("liweijie");
	        System.out.println(account);
        }
      }
  ```

## Spring事务

  提供了两种种事务管理方式:编程式事务管理和声明式事务管理

## Spring编程式事务管理

```
	public class SimpleService implements Service {
	  private final TransactionTemplate transactionTemplate;
	  public SimpleService(PlatformTransactionManager manager) {
	    Assert.notNull(manager,"the 'manager' argument must not be null");
	    this.transactionTemplate = new TransactionTemplate(manager);
	  }

	  public Object method1() {
	    return transactionTemplate.execute(new TransactionCallback() {
	      public Object doInTransaction(TransactionStatus status) {
	        updateOper1();
	        return updateOper2();
	      }
	    });
	  }
	}

	<bean id="sharedTransactionTemplate" 
	  class="org.springframework.transaction.support.TransactionTemplate">
	  <property name="isolationLevelName" value="ISOLATION_READ_UNCOMMITTED"/>
	  <property name="timeout" value="30"/>
	</bean>
```

## Spring的声明式事务

  Spring声明式事务管理，是使用Spring的AOP方式实现的。
  
  通过Spring配置将操作纳入到事务管理中,解除了事务管理和代码的耦合;不需要事务管理时，可直接从Spring配置文件中移除.

- 在Spring的配置文件中开启声明式事务

  ```
    <tx:annotation-driven    transaction-manager="事务管理器id" proxy-target-class="false" />
  ```
  proxy-target-class默认使用SUN公司的代理机制,如果产生不了,则启用CGLIB;如果proxy-target-class="true"则直接使用CGLIB的代理机制 

- 创建事务管理器组件  -- 注意这个组件需要依赖于dataSource 
  
  ``` 
    <!-- 创建事务管理器  -->
	<bean  id="transactionManager"class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
	    <property name="dataSource" ref="dataSource"></property>
	</bean>
  ```

- 在类上或者在业务方法加事务控制标注@Transactional

  加在类上就是所有的方法都有事务控制

  加在业务方法上,就要方法内的所有语句都要同时成立.

## 转账逻辑的实现

> 在src下的applicationContext.xml配置文件开启声明事务和创建事务管理器

```xml
  <!--  开启声明式事务  -->
  <tx:annotation-driven  transaction-manager="transactionManager"
      proxy-target-class="true" />
  <!-- 创建事务管理器  -->
  <bean  id="transactionManager"  
    class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
    <property name="dataSource"  ref="dataSource"></property>
  </bean>
```

> 在之前的DAO接口,加入根据账号查询银行账户的方法

```java
  public interface XdlBankAccountDAO {
	  /** 根据账号 给某个账户减少指定的钱数 */
	  int    updateAccountOutMoney(XdlBankAccount  from,double money);
  }
```

> 编写DAO的实现类继承JdbcDaoSupport实现DAO接口,使用父类提供的模板结合sql语句完成查询 同上

> 开启组件扫描在DAO实现类上打持久层标注,同时要给JdbcDaoSupport注入一个dataSource对象

  ```java
    @Repository("accountDao")
	public class XdlBankAccountDAOOracleImp extends JdbcDaoSupport implements XdlBankAccountDAO {
	    @Autowired
	    private  JdbcTemplate   jdbcTemplate;
	     @Override
		public int updateAccountOutMoney(XdlBankAccount from, double money) {
		   String sql = "update xdl_bank_account_30 set acc_money = acc_money - ?"
					+ "where  acc_no = ?";
			try {
				return  jdbcTemplate.update(sql, money,from.getAcc_no());
			} catch (DataAccessException e) {
				// TODO Auto-generated catch block
				e.printStackTrace();
			}
			return  0;
		}
    }
  ```

> 创建Service类,输入业务方法

```java
	import org.springframework.beans.factory.annotation.Autowired;
	import org.springframework.stereotype.Service;
	import org.springframework.transaction.annotation.Transactional;
	import com.xdl.bean.XdlBankAccount;
	import com.xdl.dao.XdlBankAccountDAO;

	@Service("accountService")

	public class XdlBankAccountService {
		@Autowired
	    private  XdlBankAccountDAO   accountDao;
		/** 根据账号 获取账户  */
		public  XdlBankAccount  accountByAccNo(String acc_no) {
			return  accountDao.getAccountByAccNo(acc_no);
		}
		/** 转账方法 */
		@Transactional
		public  void   transfer(XdlBankAccount from,XdlBankAccount to,double  money) {
			int   ff = accountDao.updateAccountOutMoney(from, money);
			int   tf = accountDao.updateAccountInMoney(to, money);
			if(ff==0 || tf == 0) {
				throw new RuntimeException("转账失败");
			}
		}
	}
```

> 创建Spring容器,获取DAO并进行测试 

```java
	import org.springframework.context.ApplicationContext;
	import org.springframework.context.support.ClassPathXmlApplicationContext;

	import com.xdl.bean.XdlBankAccount;
	import com.xdl.dao.XdlBankAccountDAO;
	import com.xdl.service.XdlBankAccountService;

	public class XdlBankAccountServiceTest {
		public static void main(String[] args) {
			ApplicationContext  app  = 
				new ClassPathXmlApplicationContext("applicationContext.xml");
	        XdlBankAccountService accountService = app.getBean("accountService",XdlBankAccountService.class);
	       
	        XdlBankAccount from = accountService.accountByAccNo("liweijie");
	        XdlBankAccount to = accountService.accountByAccNo("canglaoshi");
	        System.out.println(from);
	        System.out.println(to);
	        System.out.println("正在转账....");
	        // 改变账号  代表程序出了特殊问题
	        to.setAcc_no("canglaosh");  //改变的账号不存在,报错后,钱还是少了
	        try {
				accountService.transfer(from, to, 10000);
			} catch (Exception e) {
				// TODO Auto-generated catch block
				e.printStackTrace();
			}
	        from = accountService.accountByAccNo("liweijie");
	        to = accountService.accountByAccNo("canglaoshi");
	        System.out.println(from);
	        System.out.println(to);
	        
		}
	}
```

## @Transactional的属性

在使用时可以根据需要做特殊设定。声明式事务默认对检查异常不回滚,运行异常回滚

  propagation： 设置事务传播

    一个方法调用事务方法时,事务应该如何表现

```
    如:@Transactional(propagation=Propagation.REQUIRED)
    如果当前方法中不存在事务,则会掉用事务方法时开启新事务,如果当前方法中存在事务,则把掉用事务方法中的事务加入当前事务中

    如:@Transactional(propagation=Propagation.SUPPORTS)
    支持当前事务,如果没有当前事务,就以非事务方法执行

    如:@Transactional(propagation=Propagation.MANDATORY)
    使用当前事务,如果没有当前事务,就抛出异常

    如:@Transactional(propagation=Propagation.REQUIRED_NEW) 
    新建事务,如果存在当前事务,就把当前事务挂起

    如:@Transactional(propagation=Propagation.NOT_SUPPORTED)   
    以非事务发方式执行操作,如果存在当前事务,就把当前事务挂起

    如:@Transactional(propagation=Propagation.NEVER)  
    以非事务发方式执行操作,如果存在当前事务,就把抛出异常

    如:@Transactional(propagation=Propagation.NESTED)
    如果存在当前事务,则在嵌套事务内执行,如果当前没有事务,则执行与Propagation.REQUIRED相同的操作
```    

  isolation ： 设置事务隔离级别,系统默认级别读提交

    如:@Transactional(isolation=Isolation.SERIALIZABLE)

```
	读未提交：读到未提交的数据、读提交：读取提交的数据、可重复读:读取事务保护数据不改变、序列化:此时在事务完成之前不允许其他事务对数据的读取和修改

	用来解决数据库的三大问题：

	读未提交->脏读：一个事务读取到了另外一个事务没有提交的数据  解决:提升级别到读提交

	不可重复读：一个事务在开始时候读取了一份数据,另一个事务修改了这份数据并进行了提交;当第一个事务再次读取数据发现数据发生了变化,这叫不可重复读.  解决:提升级别可重复读

	幻读:保护可重复读时统计了表中的数据,另一个事务增加了数据,当再次统计数据时发生了改变 解决:提升级别序列化
```

  readOnly ： 设置为只读，还是可读写

     当事务只有查询语句时,可以把readOnly=true,代表只读事务

  rollbackFor ： 设置遇到哪些检查异常必须回滚

    如:@Transactional(rollbackFor={Exception.class})

  noRollbackFor ： 设置指定的运行异常不回滚

    如:@Transactional(norollbackFor={异常.class})


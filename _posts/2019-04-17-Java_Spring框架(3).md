---
title: Java_Spring框架(3)之组件扫描和采用继承JdbcDaoSupport的方式完成对数据库的操作
tags: java Spring AOP DAO
categories: java
---

* TOC
{:toc}

## 组件扫描

  它是Spring提供的一套基于标注(注解)的技术，目的是为了简化XML的配置 
 
## Spring中实现组件扫描的步骤

- 建立一个项目导入jar包(ioc aop),拷贝spring 配置文件到src下

- 在spring配置文件(applicationContext.xml)中开启组件扫描

  ```xml
    <context:component-scan base-package="包名" />
    如:
    <context:component-scan base-package="com.xdl"/>
  ```

- 编写java类,在类上加对应的标注


  @Component(通用层标注),一般用在bean文件下的自定义的对象类;其它的类也可以用.实例代码如下

  ```java
    package com.xdl.bean;
    import java.io.Serializable;
    import org.springframework.stereotype.Component;
    //@Component  默认id值为类名首字母小写,也可以自定义id
    //@Component(value="cd")或
    @Component("cd")
    public class Card  implements  Serializable{	
	    /**
		 * 
		 */
		private static final long serialVersionUID = 1L;
		public Card() {
			super();
			// TODO Auto-generated constructor stub
			System.out.println("Card()...");
		}
		public Card(String suit, String point) {
			super();
			this.suit = suit;
			this.point = point;
		}
		@Override
		public String toString() {
			return "Card [suit=" + suit + ", point=" + point + "]";
		}
		public String getSuit() {
			return suit;
		}
		public void setSuit(String suit) {
			this.suit = suit;
		}
		public String getPoint() {
			return point;
		}
		public void setPoint(String point) {
			this.point = point;
		}
		private  String  suit;
	    private  String  point;
    }
  ```

  ```java
    test类获取定义的容器组件

    ApplicationContext app = 
			new ClassPathXmlApplicationContext("applicationContext.xml");
    Card  card = app.getBean("cd",Card.class);
    System.out.println(card);
  ```

  @Repository(持久层标注),一般用在Dao接口的实现类上,用法和@Component一样

  @Controller(控制层标注),一般用在SpringMVC的Controller对象,用法和@Component一样

  @Service(服务层标注), 一般用在Service文件下的Service对象,用法和@Component一样

- 从容器中获取对应的组件  
 
## 和容器组件扫描相关的标注

  @Scope("singleton\|prototype")   控制组件的作用域(单例或多例,从容器中获取相同组件的地址和内容是否相同) 

  ```
    import org.springframework.context.annotation.Scope;
    @component(“empDao”)
    @Scope(“prototype”)
    public class Card{
    	...
    }
    //在上述三个标注下直接编写
  ```

  @@PostConstruct  和初始化相关的标注

    从容器中获取对应的组件时,构造方法调用后再调用初始化方法,此init方法本来是不调用的.和new对象无关

  @PreDestroy   和销毁相关的标注(得到的容器组件.destroy();然后执行销毁方法)

  ```java
	@Component
	public class DemoBean {
		 @PostConstruct
		 public void init() {
		 //初始化回调方法
		 }
		 @PreDestroy
		 public void destroy() {
		 //销毁回调方法
		}
	}
  ```

  ```java
	ApplicationContext app  =
			new ClassPathXmlApplicationContext("applicationContext.xml");        
    XdlBankAccount acc = app.getBean("xdlBankAccount",XdlBankAccount.class);
    System.out.println(acc);
    acc.destroy();
  ```

## 和DI相关的标注 

  @Value

    这个标注可以用在成员变量和set方法上,一般用来解决简单值的注入问题,如果要注入复杂值 则需要结合EL表达式
 
  ```
    import org.springframework.beans.factory.annotation.Value;    
	@value("方片")
	public String getSuit() {
		return suit;
	}
	public void setSuit(String suit) {
		this.suit = suit;
	}
	public String getPoint() {
		return point;
	}
	public void setPoint(String point) {
		this.point = point;
	}
	public String getPlayer() {
		return player;
	}
	public void setPlayer(Player  player) {
		this.player = player;
	}
	//@value("方片")
	private  String  suit;
    private  String  point;
    @value("#{player}") //id为player的组件,即@Component定义的组件
    private  Player  player;
  ```
  
  @Autowired

    这个标注可以用在成员变量,set方法,构造方法上,用来解决复杂值的注入问题
    这个标注优先按照类型进行匹配,如果类型对应有多个对象,则会启用对象的名字进行匹配,如果不存在,则程序崩溃 
  
  ```
    import org.springframework.beans.factory.annotation.Autowired;
    @Autowired
    public Card(String suit, String point) {
			super();
			this.suit = suit;
			this.point = point;
    }
    public String getSuit() {
		return suit;
	}
	public void setSuit(String suit) {
		this.suit = suit;
	}
	public String getPoint() {
		return point;
	}
	@Autowired
	public void setPoint(String point) {
		this.point = point;
	}
	public String getPlayer() {
		return player;
	}
	public void setPlayer(Player  player) {
		this.player = player;
	}
	//@value("方片")
	private  String  suit;
    private  String  point;
    @Autowired //id为player的组件,即@Component定义的组件
    private  Player  player;
  ```

  @Qualifier("名字")  

    可以加载指定组件,但是这个标注不能用在构造方法上 
  
  @Autowired中结合@Qualifier("名字")  
    
    对组件的依赖属于强依赖(依赖的组件必须存在,如果不存在程序崩溃)    
    可以使用@Autowired的requried属性设置成false,则代表尽量去查找组件,找不到也不报错。

  ```
    @Autowired(require=false)
    @Qualifier("player") 
    private  Player  player;
    @Autowired(require=false)
    @Qualifier("player")
	public void setPlayer(Player player) {
		this.player = player;
	}
  ```

  @Resource

    这个标注可以用在成员变量和set方法上,用来解决复杂值的注入问题的
    这个标注优先按照名字(首先set后的名字,其次是参数名)进行匹配,如果没有同名的组件,则启用类型进行匹配 
    这个标注是JDK中的标注,这个标注不能解除强依赖的问题
 
```java
	@Resource
	private  Player  player;
	@Resource
	public void setPlayer(Player player) {
		this.player = player;
	}
``` 
 
## Spring_DAO

   Spring DAO封装了JDBC简化了DAO实现的类编写

   Spring DAO提供了基于AOP的事务管理   
   
   Spring DAO对JDBC中的异常做了封装,把原来检查异常封装成了继承自RuntimeException的一个DataAccessException
 
## Spring_DAO中的核心类 
   
  JdbcTemplate(jdbc模板类)
   
    可以自动加载驱动,获取连接,执行环境的获取,结果集遍历以及资源的释放

  JdbcDaoSupport(jdbc DAO的支持类)

   这个类可以提供JdbcTemplate模板对象

## 采用继承JdbcDaoSupport的方式完成对数据库的操作

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
	</beans>
  ```

  > 编写DAO接口,查询银行账户表中的账户的数量

  ```java
    import java.util.List;
    public interface XdlBankAccountDAO {
        int   getAccountCount();
	}
  ```

  > 编写DAO的实现类继承JdbcDaoSupport,实现DAO接口,使用父类提供的模板结合sql语句完成查询

  ```
    execute方法：可以用于执行任何SQL语句，一般用于执行DDL语句；
    update方法及batchUpdate方法：update方法用于执行新增、修改、删除等语句；batchUpdate方法用于执行批处理相关语句；
    query方法及queryForXXX方法：用于执行查询相关语句；
    call方法：用于执行存储过程、函数相关语句。
  ```

  > 开启组件,在DAO实现类上打持久层标注,同时要给JdbcDaoSupport注入一个dataSource对象

  ```java
	@Repository("accountDao")
	public class XdlBankAccountDAOOracleImp extends JdbcDaoSupport implements XdlBankAccountDAO {
	    // @Autowired
	    //private   DataSource  dataSource;
		@Autowired
		public  XdlBankAccountDAOOracleImp(DataSource  dataSource) {
			super.setDataSource(dataSource);
		}

        @Override
		public int getAccountCount() {
			String sql = "select count(*) from xdl_bank_account_30";
			//return super.getJdbcTemplate().queryForInt(sql, id);
			return  super.getJdbcTemplate().queryForInt(sql);
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
        System.out.println(dao.getAccountCount());
    }
  }
  ```
    
- 查询id大于某个值的银行账户的数量

  > 建立一张银行账户表,插入几条测试数据,提交    同上 

  > 建立一个项目,导入jar包(ioc、aop、dao、连接池、数据库驱动),拷贝配置文件到src  同上

  > 编写DAO接口查询银行账户表中的id大于某个值的账户的数量

  ```java
  	public interface XdlBankAccountDAO {
      int   getAccountCountGtId(int id);
    }
  ```

  > 编写DAO的实现类继承JdbcDaoSupport实现DAO接口,使用父类提供的模板结合sql语句完成查询 同上

  > 开启组件扫描在DAO实现类上打持久层标注,同时要给JdbcDaoSupport注入一个dataSource对象

  ```java
    @Repository("accountDao")
	public class XdlBankAccountDAOOracleImp extends JdbcDaoSupport implements XdlBankAccountDAO {
	    @Autowired
		public  XdlBankAccountDAOOracleImp(DataSource  dataSource) {
			super.setDataSource(dataSource);
		}
	    @Override
		public int getAccountCountGtId(int id) {
			String sql = "select count(*) from xdl_bank_account_30 where  id > ?";
			//return super.getJdbcTemplate().queryForInt(sql, id);
			return  super.getJdbcTemplate().queryForObject(sql, Integer.class, id);
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
            System.out.println(dao.getAccountCountGtId(1));
        }
      }
  ```
     
- 根据id查询银行账号

  > 建立一张银行账户表,插入几条测试数据,提交  同上

  > 建立一个项目,导入jar包(ioc、aop、dao、连接池、数据库驱动),拷贝配置文件到src  同上

  > 编写DAO接口,加一个查询方法

  ```java
	public interface XdlBankAccountDAO {
   		XdlBankAccount   getAccountById(int id);
   	}
  ```

  > 编写一个XdlBankAccount实体类

  ```java
	import org.springframework.stereotype.Component;
	@Component
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
			System.out.println("构造方法方法");

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

  > 编写DAO的实现类继承JdbcDaoSupport实现DAO接口,使用父类提供的模板结合sql语句完成查询

  > 开启组件扫描,在DAO实现类上打持久层标注,同时要给JdbcDaoSupport注入一个dataSource对象

  ```java
    @Repository("accountDao")
    public class XdlBankAccountDAOOracleImp extends JdbcDaoSupport implements XdlBankAccountDAO {
    	@Autowired
		public  XdlBankAccountDAOOracleImp(DataSource  dataSource) {
			super.setDataSource(dataSource);
		}
		@Override
	    public XdlBankAccount getAccountById(int id) {
			String sql = "select * from xdl_bank_account_30 where id = ?";
	        // Spring 要求大家自己完成 结果集到对象的转换 		
			try {
				return super.getJdbcTemplate().queryForObject(sql,
					new XdlBankAccountMapper(), id);
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
            System.out.println(dao.getAccountById(1));
        }
    }
  ```
  
## 查询所有的银行账户
   
  > 建立一张银行账户表,插入几条测试数据,提交  同上

  > 建立一个项目,导入jar包(ioc、aop、dao、连接池、数据库驱动),拷贝配置文件到src  同上

  > 编写DAO接口,加一个查询方法

```java
	public interface XdlBankAccountDAO {
		List<XdlBankAccount>  getAll();
	}
```
   
  > 编写一个XdlBankAccount实体类  同上

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

  > 编写DAO的实现类继承JdbcDaoSupport实现DAO接口,使用父类提供的模板结合sql语句完成查询

  > 开启组件扫描,在DAO实现类上打持久层标注,同时要给JdbcDaoSupport注入一个dataSource对象

  ```java
    @Repository("accountDao")
    public class XdlBankAccountDAOOracleImp extends JdbcDaoSupport implements XdlBankAccountDAO {
    	@Autowired
		public  XdlBankAccountDAOOracleImp(DataSource  dataSource) {
			super.setDataSource(dataSource);
		}
		@Override
		public List<XdlBankAccount> getAll() {
			String sql = "select * from xdl_bank_account_30";
			return super.getJdbcTemplate().query(sql, new XdlBankAccountMapper());
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
            System.out.println(dao.getAll());
        }
    }
  ```

## 增加银行账户

  > 建立一张银行账户表,插入几条测试数据,提交  同上
   
  > 建立项目,导入jar包(ioc、aop、dao、数据库连接池、驱动)，拷贝配置文件到src下 同上

  > 编写一个XdlBankAccount实体类  同上

  > 编写DAO接口,加一个插入方法

  ```java
    import com.xdl.bean.XdlBankAccount;
	public interface XdlBankAccountDAO {
	    int  insertXdlBankAccount(XdlBankAccount  account);
	}
  ```

  > 编写DAO的实现类继承JdbcDaoSupport实现DAO接口,使用父类模板,根据sql完成对应的操作 

  > 开启组件扫描,在DAO实现类上打持久层标注,同时要给JdbcDaoSupport注入一个dataSource对象

  ```java
	import javax.annotation.Resource;
	import javax.sql.DataSource;
	import org.springframework.dao.DataAccessException;
	import org.springframework.jdbc.core.support.JdbcDaoSupport;
	import org.springframework.stereotype.Repository;
	import com.xdl.bean.XdlBankAccount;
	import com.xdl.dao.XdlBankAccountDAO;
	@Repository("accountDao")
	public class XdlBankAccountDAOOracleImp extends JdbcDaoSupport implements XdlBankAccountDAO {
	    // 注入一个dataSource 给 JdbcDaoSupport 
		@Resource
		public  void  setMyDataSource(DataSource dataSource) {
			super.setDataSource(dataSource);
		}
		@Override
		public int insertXdlBankAccount(XdlBankAccount account) {
			String sql = "insert into  xdl_bank_account_30 values("
					+ "xdl_bank_account_30_id_seq.nextval,?,?,?)";
			try {
				return  super.getJdbcTemplate().update(sql, account.getAcc_no(),
					account.getAcc_password(),account.getAcc_money());
			} catch (DataAccessException e) {
				// TODO Auto-generated catch block
				e.printStackTrace();
			}
			return  0;
		}
	}
  ```
   
  > 封装一个Service,注入DAO,封装业务方法,测试

  ```
	import org.springframework.beans.factory.annotation.Autowired;
	import org.springframework.stereotype.Service;
	import com.xdl.bean.XdlBankAccount;
	import com.xdl.dao.XdlBankAccountDAO;
	@Service("accountService")
	public class XdlBankAccountService {
		@Autowired
	    private XdlBankAccountDAO accountDao;
		public boolean register(XdlBankAccount account) {
			return  accountDao.insertXdlBankAccount(account) == 1 ? true :false;
		}
	}
  ```

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
	        XdlBankAccountService accountService =  app.getBean("accountService",XdlBankAccountService.class);
	        XdlBankAccount account = new XdlBankAccount(0,"abc", "123", 1);
	        System.out.println(accountService.register(account));
		}
	}
  ```

## 通过id更新银行账户

  > 建立一张银行账户表,插入几条测试数据,提交  同上
   
  > 建立项目,导入jar包(ioc、aop、dao、数据库连接池、驱动)，拷贝配置文件到src下 同上

  > 编写一个XdlBankAccount实体类  同上

  > 编写DAO接口,加一个删除方法

  ```java
    import com.xdl.bean.XdlBankAccount;
	public interface XdlBankAccountDAO {
	    int  updateXdlBankAccount(String acc_no,int id);
	}
  ```

  > 编写DAO的实现类继承JdbcDaoSupport实现DAO接口,使用父类模板,根据sql完成对应的操作 

  > 开启组件扫描,在DAO实现类上打持久层标注,同时要给JdbcDaoSupport注入一个dataSource对象

  ```java
	import javax.annotation.Resource;
	import javax.sql.DataSource;
	import org.springframework.dao.DataAccessException;
	import org.springframework.jdbc.core.support.JdbcDaoSupport;
	import org.springframework.stereotype.Repository;
	import com.xdl.bean.XdlBankAccount;
	import com.xdl.dao.XdlBankAccountDAO;
	@Repository("accountDao")
	public class XdlBankAccountDAOOracleImp extends JdbcDaoSupport implements XdlBankAccountDAO {
	    // 注入一个dataSource 给 JdbcDaoSupport 
		@Resource
		public  void  setMyDataSource(DataSource dataSource) {
			super.setDataSource(dataSource);
		}
		@Override
		public int updateXdlBankAccount(String acc_no,int id) {
			String sql = "update xdl_bank_account_30 set acc_no = ? where id = ?";
			try {
				return  super.getJdbcTemplate().update(sql,acc_no,id);
			} catch (DataAccessException e) {
				// TODO Auto-generated catch block
				e.printStackTrace();
			}
			return  0;
		}
	}
  ```
   
  > 封装一个Service,注入DAO,封装业务方法,测试

  ```
	import org.springframework.beans.factory.annotation.Autowired;
	import org.springframework.stereotype.Service;
	import com.xdl.bean.XdlBankAccount;
	import com.xdl.dao.XdlBankAccountDAO;
	@Service("accountService")
	public class XdlBankAccountService {
		@Autowired
	    private XdlBankAccountDAO accountDao;
		public boolean update(String acc_no,int id) {
			return  accountDao.updateXdlBankAccount(acc_no,id) == 1 ? true :false;
		}
	}
  ```

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
	        XdlBankAccountService accountService =  app.getBean("accountService",XdlBankAccountService.class);
	        System.out.println(accountService.update("rao",3));
		}
	}
  ```

## 通过id删除银行账户

  > 建立一张银行账户表,插入几条测试数据,提交  同上
   
  > 建立项目,导入jar包(ioc、aop、dao、数据库连接池、驱动)，拷贝配置文件到src下 同上

  > 编写一个XdlBankAccount实体类  同上

  > 编写DAO接口,加一个删除方法

  ```java
    import com.xdl.bean.XdlBankAccount;
	public interface XdlBankAccountDAO {
	    int  deleteXdlBankAccount(int id);
	}
  ```

  > 编写DAO的实现类继承JdbcDaoSupport实现DAO接口,使用父类模板,根据sql完成对应的操作 

  > 开启组件扫描,在DAO实现类上打持久层标注,同时要给JdbcDaoSupport注入一个dataSource对象

  ```java
	import javax.annotation.Resource;
	import javax.sql.DataSource;
	import org.springframework.dao.DataAccessException;
	import org.springframework.jdbc.core.support.JdbcDaoSupport;
	import org.springframework.stereotype.Repository;
	import com.xdl.bean.XdlBankAccount;
	import com.xdl.dao.XdlBankAccountDAO;
	@Repository("accountDao")
	public class XdlBankAccountDAOOracleImp extends JdbcDaoSupport implements XdlBankAccountDAO {
	    // 注入一个dataSource 给 JdbcDaoSupport 
		@Resource
		public  void  setMyDataSource(DataSource dataSource) {
			super.setDataSource(dataSource);
		}
		@Override
		public int deleteXdlBankAccount(int id) {
			String sql = "delete from xdl_bank_account_30 where id = ?";
			try {
				return  super.getJdbcTemplate().update(sql,id);
			} catch (DataAccessException e) {
				// TODO Auto-generated catch block
				e.printStackTrace();
			}
			return  0;
		}
	}
  ```
   
  > 封装一个Service,注入DAO,封装业务方法,测试

  ```
	import org.springframework.beans.factory.annotation.Autowired;
	import org.springframework.stereotype.Service;
	import com.xdl.bean.XdlBankAccount;
	import com.xdl.dao.XdlBankAccountDAO;
	@Service("accountService")
	public class XdlBankAccountService {
		@Autowired
	    private XdlBankAccountDAO accountDao;
		public boolean delete(int id) {
			return  accountDao.deleteXdlBankAccount(id) == 1 ? true :false;
		}
	}
  ```

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
	        XdlBankAccountService accountService =  app.getBean("accountService",XdlBankAccountService.class);
	        System.out.println(accountService.delete(3));
		}
	}
  ```

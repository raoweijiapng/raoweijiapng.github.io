---
title: Java_SpringBoot(8)之MVC的servlet、过滤器filter、Task任务调度
tags: java SpringBoot
categories: java
---

* TOC
{:toc}

## SpringBoot Servlet
  
  > 在SpringBoot中使用Servlet、Filter、Listener组件方法。

  - 定义Servlet组件，继承HttpServlet

    > 在上述应用三的src/main/java/com/xdl下创建servlet目录,然后在此目录下创建SomeServlet2.class

    ```java
      public class SomeServlet2 extends HttpServlet{    
        public void service(HttpServletRequest request,HttpServletResponse response) throws IOException {
          response.setContentType("text/html;charset=UTF-8");
          PrintWriter out = response.getWriter();
          out.println("Hello SpringBoot Servlet2");
          out.flush();
          out.close();
        }      
      }
    ```

  - 配置Servlet组件

    > 方法一,使用@ServletComponentScan、@WebServlet注解标记

    > 在上述应用三的src/main/java/com/xdl下创建servlet目录,然后在此目录下创建SomeServlet.class

    ```java
      @WebServlet(name="someservlet",urlPatterns= {"/some.do","/some.action"},loadOnStartup=1)
      public class SomeServlet extends HttpServlet{
        response.setContentType("text/html;charset=UTF-8");
        PrintWriter out = response.getWriter();
        out.println("Hello SpringBoot Servlet");
        out.flush();
        out.close();
      }
    ```

    > 在启动类前追加@ServletComponentScan

    ```java
      @SpringBootApplication
      @ServletComponentScan//扫描@WebServlet、@WebFilter、@WebListener定义的组件
      public class RunBoot {      
        public static void main(String[] args) {
          SpringApplication.run(RunBoot.class, args);
        }
      }
    ```

    > 方法二,使用ServletRegistrationBean+@Bean注册Servlet,在启动类增加以下代码

    ```java
      @SpringBootApplication
      @ServletComponentScan//扫描@WebServlet、@WebFilter、@WebListener定义的组件
      public class RunBoot {
        public static void main(String[] args) {
          SpringApplication.run(RunBoot.class, args);
        }

        @Bean
        public ServletRegistrationBean<SomeServlet2> someServlet2(){
          ServletRegistrationBean<SomeServlet2> bean = 
              new ServletRegistrationBean<SomeServlet2>();
          bean.setServlet(new SomeServlet2());
          bean.setName("someservlet2");
          bean.setLoadOnStartup(1);
          List<String> urls = new ArrayList<String>();
          urls.add("/some2.do");
          urls.add("/some2.action");
          bean.setUrlMappings(urls);
          return bean;
        }
      }
    ```

## SpringBoot Filter

  > 编写Filter过滤器组件，实现Filter接口

  > Filter组件配置,启动类前追加@ServletComponentScan

  - 使用@ServletComponentScan和@WebFilter注解标记配置,多个Filter执行顺序，由类型名字典顺序决定。

    ```java
      @WebFilter(filterName="somefilter",urlPatterns= {"/some.do","/some.action"})
      @WebFilter(filterName="somefilter",servletNames= {"someservlet","someservlet2"})
      public class ASomeFilter implements Filter{
      
        public void init(FilterConfig filterConfig) throws ServletException {
          System.out.println("---ASomeFilter------init");
        }
      
        public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain)
            throws IOException, ServletException {
          System.out.println("---ASomeFilter------doFilter");
          chain.doFilter(request, response);//执行后续处理
        }
      
        public void destroy() {
          System.out.println("---ASomeFilter------destroy");
        }
      
      }
    ```

  - 使用FilterRegistrationBean和@Bean注解配置,多个Filter执行顺序，由FilterRegistrationBean.setOrder()决定。在启动类增加以下代码

    ```java
      @SpringBootApplication
      @ServletComponentScan//扫描@WebServlet、@WebFilter、@WebListener定义的组件
      public class RunBoot {
        public static void main(String[] args) {
          SpringApplication.run(RunBoot.class, args);
        }

        @Bean
        public FilterRegistrationBean<BSomeFilter2> someFilter2(){
          FilterRegistrationBean<BSomeFilter2> bean = new FilterRegistrationBean<BSomeFilter2>();
          bean.setFilter(new BSomeFilter2());
          bean.setName("somefilter2");
          List<String> servletNames = new ArrayList<String>();
          servletNames.add("someservlet");
          servletNames.add("someservlet2");
          bean.setServletNames(servletNames);
          bean.setOrder(1);
          return bean;
        }
        @Bean
        public FilterRegistrationBean<ASomeFilter> someFilter(){
          FilterRegistrationBean<ASomeFilter> bean = new FilterRegistrationBean<ASomeFilter>();
          bean.setFilter(new ASomeFilter());
          bean.setName("somefilter");
          List<String> servletNames = new ArrayList<String>();
          servletNames.add("someservlet");
          servletNames.add("someservlet2");
          bean.setServletNames(servletNames);
          bean.setOrder(2);
          return bean;
        }
      }
    ```

## SpringBoot Task任务调度

  - 服务器启动自动执行Task任务

    > SpringBoot提供了ApplicationRunner和CommandLineRunner两个接口，用于实现启动任务。

    > ApplicationRunner,在src/main/java/com/xdl下创建task目录,然后在此目录下创建MyTask1.class

    ```java
      @Component
      @Order(2)
      public class MyTask1 implements ApplicationRunner{      
        public void run(ApplicationArguments args) throws Exception {
          System.out.println("启动服务器后自动执行任务MyTask1"+new Date());
        }      
      }
    ```

    > CommandLineRunner,在src/main/java/com/xdl下创建task目录,然后在此目录下创建MyTask2.class

    ```java
      @Component
      @Order(1)
      public class MyTask2 implements CommandLineRunner{      
        public void run(String... args) throws Exception {
          System.out.println("启动服务器后自动执行任务MyTask2"+new Date());
          Thread.sleep(5000);
        }
            }
    ```

    > 提示：如果有多个task任务，可以使用@Order注解按1、2、3顺序执行。多个任务采用单线程顺序执行。

  - 服务器启动后定时执行Task任务

    > 在Spring中提供了一个Spring Schedule模块，可以按计划进行任务调度。之前一直都采用quartz工具完成。

    > 在src/main/java/com/xdl下创建task目录,然后在此目录下创建MyTask3.class

    > 在SpringBoot中对Spring Schedule做了封装，使用方法如下

    ```java
      @Component
      @EnableScheduling //启用Spring schedule模块
      public class MyTask3 {

        @Scheduled(initialDelay=2000,fixedRate=3000)//每隔3秒调用一次
        public void execute1() {
          System.out.println("调用MyTask3任务1"+new Date());
        }
        
        @Scheduled(fixedDelay=3000)//第一次调用逻辑完成再等3秒调用第二次
        public void execute3() throws InterruptedException {
          System.out.println("调用MyTask3任务3"+new Date());
          Thread.sleep(2000);
        }
      
        @Scheduled(cron="0/5 * * * * ?")//指定执行计划,每隔5秒调用一次
        public void exec() throws Exception {
          System.out.println("服务器启动后调用MyTask3任务1"+new Date());
          Thread.sleep(3000);
        }
      }
    ```

    > 提示：使用@EnableScheduling打开模块支持，使用@Scheduled制定计划。

    > 多个@Scheduled任务默认采用单线程机制调度，如果需要多任务并发执行，可以使用@EnableAsync和@Async注解定义。

    > 在src/main/java/com/xdl下创建task目录,然后在此目录下创建MyTask4.class

    ```java
      @Component
      @EnableScheduling //启用Spring schedule模块
      @EnableAsync
      public class MyTask4 {
      
        @Scheduled(cron="0/5 * * * * ?")//指定执行计划
        @Async
        public void exec() throws Exception {
          System.out.println("服务器启动后调用MyTask3任务1"+new Date());
          Thread.sleep(3000);
        }
        
        @Scheduled(cron="0/1 * * * * ?")//指定执行计划
        @Async
        public void exec1() {
          System.out.println("服务器启动后调用MyTask3任务2"+new Date());
        }
        
      }
    ```

    > 使用Spring Schedule还需要指定cron表达式，表达式具体规则：

    ```
      秒   分    时    日   月   星期   年（可省略）

      0    0     10    1   10    ？
      
      秒： 0-59
      分： 0-59
      时： 0-23
      日： 1-31
      月： 1-12
      星期：1-7，1表示星期日，7表示星期六

      * ： 表示每一分、每一秒、每一天，任何一个可能值
      ? ： 只用在日和星期部分，如果指定日，星期用？;如果指定星期，日用?，避免日和星期冲突 
      / ： 表示增量，0/1表示0\1\2\3\4递增加1；0/5表示0\5\10\15；1/5表示1\6\11\16\21
      L ： 只用在日和星期部分，表示最后一天、周六
    ```

  ![cron使用案例](https://raoweijiapng.github.io/static/img/java/cron.png)


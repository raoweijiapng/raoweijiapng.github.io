---
title: Java_SpringBoot(7)之MVC的Test测试、拦截器、静态资源访问和异常处理
tags: java SpringBoot
categories: java
---

* TOC
{:toc}

## SpringBoot Test测试

  - 包含junit工具

    单元测试工具，用于测试组件方法。每个@Test方法都是独立线程。

  - Spring Test工具

    集成了junit，在junit基础之上进行组件及MVC流程测试。

    测试组件：直接注入组件对象，在@Test方法调用组件方法测试即可。

    测试MVC流程：需要注入controller，基于controller创建MockMvc对象，使用MockMvc发送请求接收响应结果。

    ```
      MockMvc ： 发送请求，接收响应结果
      MockMvcBuilders ： 用于创建MockMvc对象
      RequestBuilder：请求对象
      MockMvcRequestBuilders：用于创建RequestBuilder对象
      MvcResult：响应结果对象
    ```

    > 测试类实例

    ```java
      @RunWith(SpringRunner.class)//创建spring容器，注入对象，启动junit方法
      @SpringBootTest(classes= {RunBoot.class})
      public class TestKnowledgeController {
        
        @Autowired
        private KnowledgeController controller;
        
        @Test
        public void test1() throws Exception {
          //加载controller，创建一个mockMvc对象
          MockMvc mock = MockMvcBuilders.standaloneSetup(controller).build();
          //mockMvc可以发送请求，接收响应结果
          RequestBuilder request = 
            MockMvcRequestBuilders.post("/knowledge")
            .param("name", "SSM")
            .param("subjectId", "2")
            .param("chapterId", "11");
          MvcResult result = mock.perform(request).andReturn();
          //将返回结果显示
          String content = result.getResponse().getContentAsString();
          System.out.println(content);
        }
      }
    ```

## 拦截器

  - 编写一个拦截器组件，实现HandlerInterceptor

    > 在[MVC的使用](https://raoweijiapng.github.io/java/Java_SpringBoot(6)/)的应用三扩展上写拦截器组件

    > 在应用三上的pom.xml文件中增加dependcy

    ```xml
      <dependencies>
          <dependency>
              <groupId>org.springframework.boot</groupId>
              <artifactId>spring-boot-starter-web</artifactId>
              <version>2.1.2.RELEASE</version>
          </dependency>
          <dependency>
              <groupId>org.springframework.boot</groupId>
              <artifactId>spring-boot-starter-thymeleaf</artifactId>
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
          <dependency>
              <groupId>org.springframework.boot</groupId>
              <artifactId>spring-boot-starter-data-jpa</artifactId>
              <version>2.1.2.RELEASE</version>
          </dependency>
          <dependency>
              <groupId>org.springframework.boot</groupId>
              <artifactId>spring-boot-devtools</artifactId>
              <version>2.1.2.RELEASE</version>
          </dependency>
      </dependencies>
    ```

    > 在src/main/java/com/xdl下创建interceptor目录,然后在此目录下创建SomeInterceptor.class

    ```java
      @Component
      public class SomeInterceptor implements HandlerInterceptor{    
        public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler)
            throws Exception {
          System.out.println("-----preHandle------");
          return true;
        }
        
        public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler,
            @Nullable ModelAndView modelAndView) throws Exception {
          modelAndView.getModel().put("msg", "XDL学习拦截器");
          System.out.println("-----preHandle------");        
        }
        
        public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler,
            @Nullable Exception ex) throws Exception {        
          System.out.println("-----afterCompletion------");        
        }      
      }
    ```

  - 在Spring配置拦截器

    > 在src/main/java/com/xdl下创建interceptor目录,然后在此目录下创建MyInterceptorConfiguration.class

    ```java

      @Component
      public class MyInterceptorConfiguration implements WebMvcConfigurer{        
        @Autowired
        private SomeInterceptor some;
        
        public void addInterceptors(InterceptorRegistry registry) {          
          registry.addInterceptor(some).addPathPatterns("/hello");
        }        
      }
    ```

## 静态资源访问

  - 在SpringBoot里，静态资源css、js、img等资源，可以放在src\main\resources约定的几个目录下：

    META-INF/resources （优先级最高）
    
    resources
    
    static
    
    public (优先级最低)

  - 自定义，方法如下：

    > 在src/main/java/com/xdl下创建resources目录,然后在此目录下创建SomeResourcesConfiguration.class

    ```java
      @Component
      public class SomeResourcesConfiguration implements WebMvcConfigurer{  
        public void addResourceHandlers(ResourceHandlerRegistry registry) {
          registry.addResourceHandler("/**").addResourceLocations(
            "classpath:images/","classpath:resources/",
            "classpath:static/","classpath:public/");
        }    
      }
    ```

## 异常处理

  - 统一异常处理

    在SpringBoot中遇到错误，就会显示"Whitelabel Error Page"白板界面，属于统一异常处理（全局异常处理）.

    异常处理机制：底层有个ErrorMvcAutoConfiguration自动配置组件，创建了一个BasicErrorController对象，该controller提供了两个/error请求处理，一个返回HTML结果，另一个返回JSON结果。当boot程序发生异常抛出后，boot底层会自动转发/error请求处理。

    也可以自定义ErrorController替代底层的BasicErrorController，方法如下

    > 在controller的目录下创建MyErrorController.class

    ```
      @Controller
      public class MyErrorController implements ErrorController{
      
        //固定返回/error请求
        public String getErrorPath() {
          return "/error";
        }
      
        //定义error请求处理
        @RequestMapping("/error")
        public ModelAndView errorHtml() {
          ModelAndView mav = new ModelAndView();
          mav.setViewName("myerror");//myerror.html
          return mav;
        }        
      }
    ```

    > 提示：需要自己定义myerror错误页面。

    > 错误的页面放在src/main/resources下detemplates目录下,如myerror.html

    ```
      <!DOCTYPE html>
      <html xmlns:th="http://www.thymeleaf.org">
        <head>
          <meta charset="UTF-8">
          <title>Insert title here</title>
        </head>
        <body>
          <h1>发生异常了，您请求资源可能不在地球上</h1>
          <hr/>
          <img alt="" src="404.jpg" width="400px" height="250px">
        </body>
      </html>
    ```

  - 局部异常处理

    > 处理某一个Controller请求错误。可以采用@ExceptionHanlder方法

    > 在controller的目录下创建SomeController3.class

    ```java
      @Controller
      public class SomeController3{
        @RequestMapping("/some3")
        @ResponseBody
        public String some() {
          String s = null;
          s.length();
          return "Hello JSON";
        }

        @ExceptionHandler//返回html界面
        public ModelAndView handle(Exception e) {
          ModelAndView mav = new ModelAndView();
          mav.setViewName("someerror");
          return mav;
        }

        @ExceptionHandler//返回json结果
        @ResponseBody
        public Map<String, Object> handle(Exception e) {
          Map<String, Object> data = new HashMap<String, Object>();
          data.put("msg", "发生异常");
          data.put("type", e.getClass());
          return data;
        }
      }
    ```

    > 提示：方法定义需要exception参数。

    > 可以将@ExceptionHandler抽取出来写成父类BasicController以继承方式重用，也可以使用@CtrollerAdvice定义，所有Controller重用。

    > 在controller的目录下创建BasicController.class

    ```java
      @ControllerAdvice //等价于其他所有Controller都extends BasicController
      public class BasicController {
        @ExceptionHandler
        public ModelAndView handle(Exception e) {
          ModelAndView mav = new ModelAndView();
          mav.setViewName("someerror");
          return mav;
        }
      }
    ```

    > 错误的页面放在src/main/resources下detemplates目录下,如msomeerror.html

    ```
      <!DOCTYPE html>
      <html xmlns:th="http://www.thymeleaf.org">
        <head>
          <meta charset="UTF-8">
          <title>Insert title here</title>
        </head>
        <body>
          <h1>SomeController发生了异常</h1>
        </body>
      </html>
    ```


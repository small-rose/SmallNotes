---
layout: default
title: SpringBoot Request
parent: SpringBoot
nav_order: 300
---


Here are Servlet request experience .
{: .fs-6 .fw-300 }


## request 本身和 springboot 没有什么直接关系，但是我们在开发接口的过程中难免会使用。尤其是拦截器、过滤器等。 springboot 本身内置实现了 Servlet 规范的Tomcat, 当然也可以自己引其他产品。
{: .no_toc .text-delta }

1. TOC
{:toc}



# Servlet 规范


## 什么是Servlet

Servlet是为了处理JavaWeb的HTTP请求、响应的程序，遵循Servlet规范，运行在服务器。

例如Tomcat是JavaWeb的服务器，本质是一个Servlet容器，负责处理客户端

请求，将请求转发给Servlet，将Servlet响应转发给客户端。

> Servlet是一套JavaWeb的开发规范，负责处理HTTP的请求与响应

目前实现了Servlet规范的Web服务器又Tomcat、WebLogic、JBoss、WebSphere等产品.

## Servlet 依赖


Servlet 4.0.1之前旧版:
```xml
<dependency>
    <groupId>javax.servlet</groupId>
    <artifactId>javax.servlet-api</artifactId>
    <version>4.0.1</version>
    <scope>provided</scope>
</dependency>
```

Servlet 4.0.1 之后后 使用新依赖
```xml
<dependency>
    <groupId>jakarta.servlet</groupId>
    <artifactId>jakarta.servlet-api</artifactId>
    <version>6.1.0</version>
    <scope>provided</scope>
</dependency>

```

> 使用 springboot 一般不需要单独引入，因为内置Tomcat已经实现相关规范，接口肯定是有的。 
>
> 传统项目依然还是会引入，一般会配置对应Tomcat 目录的lib 包下的servlet-api.jar, 使用 jsp 的项目还会引jsp相关包。


## 实现方式

实现Servlet的三种方式

- 实现javax.servlet.Servlet接口
- 继承javax.servlet.GenericServlet类
- 继承javax.servlet.http.HttpServlet类

Servlet 接口:
```java
/**
 * 抽象接口，定义Servlet生命周期
 */
public interface Servlet {
    init();
    service();
    destroy();
    // 获取Servlet的配置对象，对应的是web.xml中的servlet>标签下的<init-param>
    getServletConfig();
}
```

GenericServlet 接口:
```java
/**
 * 扩展Servlet，是与协议无关的Servlet
 */
public abstract class GenericServlet implements Servlet, ServletConfig, Serializable {
    init();
    service();
    destroy();
    getServletConfig();
    // 扩展方法
    getInitParameter();
    getServletConfig();
    getInitParameterNames();
    getInitParameter();
}
```

HttpServlet 接口:
```java
/**
 * HTTP协议的Servlet
 */
public abstract class HttpServlet extends GenericServlet {
    init();
    service();
    destroy();
    getInitParameter();
    getServletConfig();
    getInitParameterNames();
    getInitParameter();
    // 扩展方法
    doGet();
    doPost();
    doHead();
    doPut();
    //......
}
```

> 关系为：Servlet->GenericServlet->HttpServlet->自定义Servlet

搜索这HttpServlet 就可以在 `tomcat-embed-core-9.0.29.jar` 包中找到。

## Servlet的核心技术

生命周期

- init()负责初始化，只执行一次；在父类中只是一个空方法，需要自定义Servlet自己重写
- service()负责处理HTTP请求
- destroy()在Servlet销毁时执行，例如Tomcat关闭时


HttpServletRequest对象相关类与接口

```java
public interface ServletRequest{

}
public interface HttpServletRequest extends ServletRequest {

}
public class ServletRequestWrapper implements ServletRequest{

}
public class HttpServletRequestWrapper extends ServletRequestWrapper implements HttpServletRequest{

}
```

HttpServletResponse对象相关类与接口

```java
public interface ServletResponse {
}
public interface HttpServletResponse extends ServletResponse {
}

public class ServletRequestWrapper implements ServletRequest {
}
public class HttpServletRequestWrapper extends ServletRequestWrapper implements HttpServletRequest {
}
```

> HttpServletRequest对象、HttpServletResponse对象都使用了装饰模式。方便对请参数进行逐层过滤。


servlet 九大对象（ 也称 Http 对象）

 - **apllication**：服务器启动后就产生了这个对象，所有客户共享这个内置的application对象
 - **request**：封装了用户请求信息，请求可以转发给其他request（url不变）
 - **response**：处理后的数据返回客户端，请求可以重定向（url该变）
 - **session**：存储客户端请求的信息，因此它是有状态交互式的
 - out：用于发送输出流到客户端
 - config：包含了当前JSP/Servlet所在的WEB应用的配置信息
 - pagecontext：提供访问JSP页面的命名空间。它也提供用来访问其他的JSP隐含对象
 - page：来源于当前被访问JSP页面的实例化。它实际使用的是JSP转换成的Servlet
 - exception：用于捕获JSP抛出的异常。它只有在JSP页面属性isErrorPage=true时才可用

> 以springboot发展为主流之后，前后端流行，jsp使用减少，所以也有说 4大对象 的说法。

servlet 四大作用域（ 也称 Http 作用域）


 - apllication：全局作用范围，整个应用程序共享，就是在部署文件中的同一个webApp共享，生命周期为：应用程序启动到停止
 - session：会话作用域，当用户首次访问时，产生一个新的会话，以后服务器就可以记住这个会话状态。生命周期：会话超时，或者服务器端强制使会话失效。
 - request：请求作用域，就是客户端的一次请求
 - page：一个JSP页面。
 
> 以springboot发展为主流之后，前后端流行，jsp使用减少，所以也有说 3大作用域 的说法。

域对象的公共方法：

```
// 存储数据的方法
setAttribute(name,value);
// 根据name获取对应数据值
getAttribute(name);
// 删除数据
removeAttribute(name)；
```

> page 相关的就暂不记录。

ServletContext对象在Web应用创建时被创建，当Web应用被停止时停止，所有的Servlet共享一个ServletContext对象，因此可以通过ServletContext实现Servlet之间的通信

Session对象是服务器给每个用户的浏览器创建的独有的Session对象，用于存储用户私有的数据。

ServletRequest对象在Servlet的Service方法传入，当请求结束时，被销毁。


## Filter 过滤器技术

Filter被称为过滤器，它的作用也如同它的名字一样，就是要过滤HTTP请求，也可以说Web资源，可以分为这么几类

 - JSP页面
 - Servlet
 - 静态文件（HTML、CSS、图片）
 - 参数的安全过滤
 - 权限控制
 - 等等
 
Filter也是一个链式调用的结构，最开始的filterConfig.getFilter()方法获取的是web.xml中定义的Filter，然后进行链式调用。
 
Filter如何定义？
 
**步骤一**：

定义Java类，实现Filter接口，并实现它的init、doFilter、destroy方法

**步骤二**：

传统方式是在web.xml中使用<filter>和<filter-mapping>元素对编写的filter类进行注册，并设置它所能拦截的资源。

springboot 方式可以使用 @Bean 注解,借助 FilterRegistrationBean 实例化进行过滤器的注册，并设置它所能拦截的资源。

如：
```java
public class CustomFilter implements Filter {
    private String param ;

    public CustomFilter(){
    }

    public CustomFilter(String param ){
        this.param = param;
    }
    @override
    public void init(FilterConfig filterconfig){
        String value1 = filterconfig.getInitParameter("key1");
        String value2 = filterconfig.getInitParameter("key2");
    }
    
    @override 
    public void doFilter(ServletRequest servletRequest, ServletResponse response, FilterChain filterChain) throws IOException, ServletException {
        HttpServletRequest request = (HttpServletRequest) servletRequest;
        // do customer filter
                
        // 校验不通过
        if(checkReturnFalse()){
            // 可以直接使用 response 响应输出， 或者使用 request 重定向
            return ;
        }
        // 校验通过就放行，继续下一个过滤
        filterchain.doFilter(xssRequest, response);
    }

    @Override 
    public void destroy() {
    }
}
```

注册

```xml
<filter>
    <filter-name>CustomFilter</filter-name>
    <filter-class>org.example.CustomFilter</filter-class>
</filter>
<filter-mapping>
    <filter-name>CustomFilter</filter-name>
    <url-pattern>/*</url-pattern>
    <servlet-name>login</servlet-name>
    <dispatcher>REQUEST</dispatcher>
</filter-mapping>
```

```java
@Configuration
public class FilterConf{
    @Bean("customFilter")
    public FilterRegistrationBeann customFilter(){
        FilterRegistrationBean registration = new FilterRegistrationBean();
        registration.setFilter(new CustomFilter(param));
        registration.addurlPatterns("/*");
        registration.setInitParameters(myProperties.properties());
        registration.setName("customFilter");
        registration.setEnabled(true);
        registration.setOrder(Integer.MAX_VALUE - 1);
        return registration;
    }
}
```

## HttpServletRequest

HttpServletRequest 常用方法

###  获取 HttpServletRequest 对象

（1）静态方法获取
```
ServletRequestAttributes servletRequestAttributes =  (ServletRequestAttributes)RequestContextHolder.getRequestAttributes();
HttpServletRequest request = servletRequestAttributes.getRequest();
HttpServletResponse response = servletRequestAttributes.getResponse();
```

（2）静态方法获取
```
@GetMapping(value = "")
public String test01(HttpServletRequest request,HttpServletResponse response) {
     
}
```

（3）直接注入

```
@Autowired
private HttpServletRequest request;
@Autowired
private HttpServletResponse response;
```

### request常用API


（1）取request 相关路径
```
// 获取请求方式: GET
String getMethod()
 
// 获取虚拟目录(上下文目录)：虚拟路径通过在application配置当中可以配置
// 配置：server.servlet.context-path=/zxc
String getContextPath()

// 获取Servlet路径：就是获取的controller当中配置的路径
String getServletPath()

/// 获取请求参数： id=3
// http://localhost:8080/aaa/test?id=3
String getQueryString()

// 获取请求URI：/servletDemo/demo1
// 上下文路径+controller当中配置的路径
String getRequestURI()

// 获取完整请求路径：http://localhost:8080/aaa/test
String getRequestURL()

// 获取协议及版本：HTTP/1.1
String getProtocol()

// 获取客户机的IP地址：192.168.0.0
String getRemoteAddr()
```

（2）取请求头
```
// 获取请求头数据,通过请求头名称获取值
String getHeader(String name)
// 获取所有请求头信息
Enumeration<String> getHeaderNames()
```


（3）取请求体
```
// 获取流对象-获取字符输入流，只能操作字符数据
BufferedReader getReader()
// 获取流对象-获取字节输入流，既能操作字节也能操作字符
ServletInputStream getInputStream()
```

（4）取请求参数

```
// 获取请求参数通用方式(根据参数名称获取参数值) username=zhangsan
String getParameter(String name)
// 获取请求参数名称获取参数值的数组 hobby=xx&hobby=game
String[] getParameters(String name)
// 获取请求参数名称(与获取请求头方法类似)
Enumeration<String> getParameterNames()
// 获取所有参数的Map集合
Map<String,String> getParameterMap()
```

（5）中文乱码

中文乱码问题，Tomcat8 已经将GET 请求方式的乱码问题解决了，但是POST方式的中文乱码问题 依旧存在，需要自行配置

在获取参数之前配置
```
request.setCharacterEncoding("UTF-8");
```

（6）转发

浏览器像服务器发起一次请求，服务器内部实现转发，相当于执行了两个资源之间的操作。
```
// 通过request对象获取请求转发服务器：
RequestDispatcher getRequestDispatcher(String path);
// 使用==RequestDispatcher对象来进行转发
forward(ServlertRequest request,ServletResponse response);
```

（7）共享数据

域对象：一个有作用范围的对象，可以在范围内共享数据。

request域：代表一次请求的范围，一般用于请求转发的多个资源中共享数据。
```
// 存储数据
setAttribute(String name,Object obj)
// 通过键来获取值
Object getAttribute(String name);
// 通过键移除键对应的值
removeAttribute(String name)
```


## HttpServletResponse


### response常用API

```
//设置状态码
response.setStatus(304);
 
// 设置指定名称响应头的值，下面是导出的时候我们经常要设置的响应头，响应头当中还有文件的名称，也就是通过流下载文件的时候那个文件名称
response.setHeader("Content-Disposition", "attachment;filename=" + zipName);
// 1.字符输出流
PrintWriter getWriter()
// 2.字节输出流（一般导出就是将文件写到字节流，然后响应response）
ServletOutputStream getOutputStream()
// 注意：在同一个Servlet，二种输出流不能同时使用，产生互斥
// response提供了专门负责重定向的方法
response.sendRedirect("/项目地址/资源地址");
// 指定服务器响应中文的编码方式
response.setCharacterEncoding("GBK");
// 统一服务器和客户端的编码方式
response.setContentType("text/html;charset=utf-8");
// 导出的时候如果需要直接通过response流导出Excel，需要和前端设置上下文类型，如下：
response.setContentType("application/vnd.openxmlformats-officedocument.spreadsheetml.sheet");
```

### response 常用方法示例

```java
public class SevletUtil{

    public static writJson(Object json, HttpServletResponse response){
        response.setcontentType("application/json; charset=UTF-8");
        response.setHeader( "Cache-Control",  "no-store, max-age=0, no-cache, must-revalidate");
        response.addHeader( "Cache-Control",  "post-check=0, pre-check=0");
        
        /**设置响应头允许ajax跨域访问**/
        //response.setHeader("Access-Control-Allow-Origin",“*"）/*星号表示所有的异域请求都可以接受，慎用*/
        //response.setHeader("Access-Control-ALlow-Methods""GET,PoST"); 
        
        response.setHeader(  "Pragma", "no-cache");
        response.getWriter().write(JSON.toJSONString(jsonObject));
        response.getWriter().flush();
        response.getWriter().close();
    }

    public static writHtml(String html, HttpServletResponse response){
        response.setContentType("text/html; charset=UTF-8");
        response.setHeader("Cache-Control",  "no-store, max-age=0, no-cache, must-revalidate");
        response.addHeader("Cache-Control", "post-check=0, pre-check=0");
        response.setHeader("Pragma", "no-cache");
        response.getWriter().write(html);
        response.getWriter().flush();
        response.getWriter().close();
    }

}
```

### 工具类
 
Hutool工具类是目前最常用的工具类，他的官网写的非常好，并且API也非常好，并且全是中文的，这是重点哈哈。

[ServletUtil在线API文档](https://apidoc.gitee.com/dromara/hutool)

API当中包含了：

 - cookie相关
 - 获取请求体
 - header相关
 - 获得MultiPart表单内容
 - 获得请求参数、将参数转换为Bean
 - 以及response相关write操作
 
 
## Servlet 补充
 
Servlet是单例模式吗？
 
Servlet是单例模式，服务器一般情况下只会创建一个Servlet对象，所以ServletContext是作用于整个Web程序
 
Servlet每收到一个HTTP请求便会产生一个新的线程去处理，但每次的HttpServletRequest对象都是新的。
 
Servlet是否线程安全? 什么情况下会产生线程安全问题？
 
 - 存在多线程同时访问一个资源
 - 访问非方法内部的局部变量
 
> (1) 线程安全问题都是由全局变量及静态变量
>
> (2) 引起的，所以使用doGet、doPost方法内部的变量不会出现线程安全问题


Servlet中如何处理多线程安全问题？

有三种方法解决线程安全问题

 - (1) 实现 SingleThreadModel 接口
 - (2) 使用关键字synchronized (this){XXXX}
 - (3) 避免使用实例变量

如果一个Servlet实现了SingleThreadModel接口，Servlet引擎将为每个新的请求创建一个单独的Servlet实例，这将引起大量的系统开销

如果使用synchronized关键字，该代码块同一时刻只能有一个线程执行，其它线程处于等待状态，这将严重影响系统性能和客户体验。而且这个方法也无法解决前面线程安全中的代码案例的问题

所以最好的方法是尽量不使用实例变量，方法内部的变量是每个线程的私有变量，线程之间互不影响，所以它们是线程安全的。

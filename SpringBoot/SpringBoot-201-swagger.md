---
layout: default
title: SpringBoot swagger
parent: SpringBoot
nav_order: 300
---


Here are SpringBoot swagger experience .
{: .fs-6 .fw-300 }


##   Here are swagger using demo experience .
{: .no_toc .text-delta }

1. TOC
{:toc}



# swagger


## swagger 是什么

Swagger是一个规范和完整的框架，用于生成、描述、调用和可视化 RESTful 风格的 Web 服务。总体目标是使客户端和文件系统作为服务器以同样的速度来更新。文件的方法，参数和模型紧密集成到服务器端的代码，允许API来始终保持同步。

用处:

- 使后端REST ful接口自动生成在线文档
- 可以在线功能测试
- 方便前后端接口调用过程的请求响应沟通

##  swagger 发展


在Spring中集成Swagger会使用到springfox-swagger，它对Spring和Swagger的使用进行了整合

```xml
    <dependencies>
        <!-- swagger2 -->
        <dependency>
            <groupId>io.springfox</groupId>
            <artifactId>springfox-swagger2</artifactId>
            <version>2.9.2</version>
        </dependency>
        <!-- 默认 UI -->
        <!-- http://ip:port/conext/swagger-ui.html  -->
        <dependency>
            <groupId>io.springfox</groupId>
            <artifactId>springfox-swagger-ui</artifactId>
            <version>2.9.2</version>
        </dependency>
    </dependencies>
```

也可以不使用默认的UI

`layer-UI`

```xml
        <!--  layer-UI  http://ip:port/conext/api-docs.html   -->
        <dependency>
            <groupId>com.github.ohcomeyes</groupId>
            <artifactId>swagger-ui-layer</artifactId>
            <version>1.2</version>
        </dependency>
```

`bootstrap-ui`

```xml
        <!-- bootstrap-ui  http://localhost:port/context/doc.html   -->
        <dependency>
            <groupId>com.github.xiaoymin</groupId>
            <artifactId>swagger-bootstrap-ui</artifactId>
            <version>1.9.6</version>
        </dependency>
```

`swagger-bootstrap-ui`皮肤增加更多的服务端代码来满足开发者的需求, 然后就诞生了 `Knife4j`

`Knife4j` 将 swagger 使用标准规范化 OpenAPI2 和 OpenAPI3, 并 swagger 功能进行spirngboot 2.x 和 3.x 进行适配和增强。

[Knife4j 演变和历史](https://doc.xiaominfo.com/docs/quick-start/start-knife4j-version#1%E5%89%8D%E4%B8%96%E4%BB%8A%E7%94%9F)

[Knife4j V2 官网](https://doc.xiaominfo.com/v2/documentation/)

[Knife4j V3 官网](https://doc.xiaominfo.com/docs/quick-start)



## 使用依赖和配置化

1、引入依赖

```xml
    <dependencies>
        <!-- swagger2 -->
        <dependency>
            <groupId>io.springfox</groupId>
            <artifactId>springfox-swagger2</artifactId>
            <version>2.9.2</version>
        </dependency>
        <!-- 默认 UI -->
        <!-- http://ip:port/conext/swagger-ui.html  -->
        <dependency>
            <groupId>io.springfox</groupId>
            <artifactId>springfox-swagger-ui</artifactId>
            <version>2.9.2</version>
        </dependency>
        <!--  http://ip:port/conext/api-docs.html   -->
        <!--
        <dependency>
            <groupId>com.github.ohcomeyes</groupId>
            <artifactId>swagger-ui-layer</artifactId>
            <version>1.2</version>
        </dependency>
        -->
        <!--  http://localhost:port/context/doc.html   -->
        <dependency>
            <groupId>com.github.xiaoymin</groupId>
            <artifactId>swagger-bootstrap-ui</artifactId>
            <version>1.9.6</version>
        </dependency>
    </dependencies>
```

2、 初始化配置

Spring中配置Swagger

```java
package com.xiaocai.demo.config;


import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.core.env.Environment;
import springfox.documentation.builders.ApiInfoBuilder;
import springfox.documentation.builders.PathSelectors;
import springfox.documentation.builders.RequestHandlerSelectors;
import springfox.documentation.service.ApiInfo;
import springfox.documentation.spi.DocumentationType;
import springfox.documentation.spring.web.plugins.Docket;

import java.net.InetAddress;
import java.net.UnknownHostException;

/**
 * @description: TODO 功能角色说明：
 * TODO 描述：
 * @author: 张小菜
 * @date: 2021/12/14 22:43
 * @version: v1.0
 */
@Configuration
public class SwaggerConfig {

    @Autowired
    Environment environment;

    @Bean
    public Docket createRestApi() throws UnknownHostException {
        return new Docket(DocumentationType.SWAGGER_2)
                .apiInfo(apiInfo())
                .select()
                .apis(RequestHandlerSelectors.basePackage("com.xiaocai"))
                //.apis(RequestHandlerSelectors.withClassAnnotation(ApiOperation.class))
                //.paths(Predicates.not(PathSelectors.regex("/admin/.*")))
                //.paths(Predicates.not(PathSelectors.regex("/error.*")))
                .paths(PathSelectors.any())
                .build();
    }
    public ApiInfo apiInfo() throws UnknownHostException {
        String ip = InetAddress.getLocalHost().getHostAddress();
        String contextPath = environment.getProperty("server.servlet.context-path");
        String port = environment.getProperty("server.port");
        return new ApiInfoBuilder()
                .title("bp-excel api文档")
                .description("bp-excel api文档")
                .termsOfServiceUrl("http://"+ip+":"+port+""+contextPath+"/swagger-ui.html, http://"+ip+":"+port+""+contextPath+"/doc.html")
                .version("version 1.0")
                .build();
    }
}
```

辅助类(可选)，启动应用之后可以打印地址。

```java
package com.xiaocai.demo.config;

import lombok.SneakyThrows;
import lombok.extern.slf4j.Slf4j;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.web.context.WebServerApplicationContext;
import org.springframework.boot.web.context.WebServerInitializedEvent;
import org.springframework.boot.web.server.WebServer;
import org.springframework.context.ApplicationListener;
import org.springframework.core.env.Environment;
import org.springframework.stereotype.Component;

import java.net.InetAddress;

/**
 * @description: TODO 功能角色说明：
 * TODO 描述：
 * @author: 张小菜
 * @date: 2021/12/14 22:45
 * @version: v1.0
 */
@Slf4j
@Component
public class AppServerInfo implements ApplicationListener<WebServerInitializedEvent> {


    @Autowired
    SwaggerConfig swaggerConfig;

    @SneakyThrows
    @Override
    public void onApplicationEvent(WebServerInitializedEvent event) {
        WebServer server = event.getWebServer();
        WebServerApplicationContext context = event.getApplicationContext();
        Environment env = context.getEnvironment();
        String ip = InetAddress.getLocalHost().getHostAddress();
        int port = server.getPort();
        String contextPath = env.getProperty("server.servlet.context-path");
        if (contextPath == null) {
            contextPath = "";
        }

        String termsOfServiceUrl = swaggerConfig.apiInfo().getTermsOfServiceUrl();
        log.info("\n------------------------------------------------------------\n" +
                        "\tApplication is running! Access address:\n" +
                        "\tLocal:\t\thttp://localhost:{}{}" +
                        "\n\tExternal:\thttp://{}:{}{}" +
                        "\n\tApiInfo:\t{}" +
                        "\n------------------------------------------------------------\n",
                port, contextPath,
                ip, port, contextPath,
                termsOfServiceUrl);

    }
}
```

激活注解

```java
@SpringBootApplication
@EnableSwagger2  // swagger 的激活
@EnableSwaggerBootstrapUI  // bootstrap-ui 的激活
public class SwaggerApplication {

    public static void main(String[] args) {
        SpringApplication.run(SwaggerApplication.class, args);
    }

}
```


## swagger 常用注解


API注解

@Api 用在类上，该注解将一个Controller（Class）标注为一个swagger资源（API）。在默认情况下，Swagger-Core只会扫描解析具有@Api注解的类，而会自动忽略其他类别资源（JAX-RS endpoints，Servlets等等）的注解。

该注解包含以下几个重要属性

- tags : API分组标签。具有相同标签的API将会被归并在一组内展示。
- value : 如果tags没有定义，value将作为Api的tags使用
- description : API的详细描述，在1.5.X版本之后不再使用，但实际发现在2.0.0版本中仍然可以使用

@ApiOperation

在指定的（路由）路径上，对一个操作或HTTP方法进行描述。具有相同路径的不同操作会被归组为同一个操作对象。不同的HTTP请求方法及路径组合构成一个唯一操作。

注解的属性有：

- value : 对操作的简单说明，长度为120个字母，60个汉字。
- notes : 对操作的详细说明。
- httpMethod : HTTP请求的动作名，可选值有："GET", "HEAD", "POST", "PUT", "DELETE", "OPTIONS" and "PATCH"。
- code : 默认为200，有效值必须符合标准的HTTP Status Code Definitions。


@ApiImplicitParams 

用在方法上，注解ApiImplicitParam的容器类，以数组方式存储。

@ApiImplicitParam 

对API的单一参数进行注解。虽然注解@ApiParam同JAX-RS参数相绑定，但这个@ApiImplicitParam注解可以以统一的方式定义参数列表，也是在Servelet及非JAX-RS环境下，唯一的方式参数定义方式。

> 注意这个注解@ApiImplicitParam必须被包含在注解@ApiImplicitParams之内。

可以设置以下重要参数属性：

- name : 参数名称
- value : 参数的简短描述
- required : 是否为必传参数
- dataType : 参数类型，可以为类名，也可以为基本类型（String，int、boolean等）
- paramType : 参数的传入（请求）类型，可选的值有path, query, body, header or form。

@ApiParam

增加对参数的元信息说明。这个注解只能被使用在JAX-RS 1.x/2.x的综合环境下。其主要的属性有

- required : 是否为必传参数，默认为false
- value : 参数简短说明

@ApiResponses

注解@ApiResponse的包装类，数组结构。即使需要使用一个@ApiResponse注解，也需要将@ApiResponse注解包含在注解@ApiResponses内。

@ApiResponse

描述一个操作可能的返回结果。当REST API请求发生时，这个注解可用于描述所有可能的成功与错误码。可以用，也可以不用这个注解去描述操作的返回类型，但成功操作的返回类型必须在@ApiOperation中定义。如果API具有不同的返回类型，那么需要分别定义返回值，并将返回类型进行关联。但Swagger不支持同一返回码，多种返回类型的注解。

> 注意：**这个@ApiResponse注解必须被包含在@ApiResponses注解中。**

- code : HTTP请求返回码。有效值必须符合标准的HTTP Status Code Definitions。
- message : 更加易于理解的文本消息
- response : 返回类型信息，必须使用完全限定类名，比如“com.xyz.cc.Person.class”。
- responseContainer : 如果返回类型为容器类型，可以设置相应的值。有效值为 "List", "Set" or "Map"，其他任何无效的值都会被忽略。

Model注解

对于Model的注解，Swagger提供了两个：@ApiModel及@ApiModelProperty，分别用以描述Model及Model内的属性。

@ApiModel

描述一个Model的信息（一般用在请求参数无法使用@ApiImplicitParam注解进行描述的时候）
提供对Swagger model额外信息的描述。在标注@ApiOperation注解的操作内，所有的类将自动被内省（introspected），但利用这个注解可以做一些更加详细的model结构说明。主要属性有：

- value : model的别名，默认为类名
- description : model的详细描述

@ApiModelProperty

描述一个model的属性，对model属性的注解，主要的属性值有：

- value : 属性简短描述
- example : 属性的示例值
- required : 是否为必须值


接口开发

```java
package com.xiaocai.demo.controller;

import com.xiaocai.demo.vo.UserVO;
import io.swagger.annotations.Api;
import io.swagger.annotations.ApiImplicitParam;
import io.swagger.annotations.ApiImplicitParams;
import io.swagger.annotations.ApiOperation;
import lombok.extern.slf4j.Slf4j;
import org.springframework.web.bind.annotation.*;

import java.util.HashMap;
import java.util.Map;

/**
 * @Project : springboot-demo
 * @Author : Small-Rose / zhangxaiocai
 * @Description : [ TestController ] 说明：无
 * @Function :  功能说明：无
 * @Date ：2022/2/15 15:28
 * @Version ： 1.0
 **/

@Slf4j
@RestController
@RequestMapping(value = "/test")
@Api(value = "查询测试", description="查询测试", tags="查询测试")
public class TestController {

    @GetMapping(value = "/test01")
    public String test01(){
        return "SUCCESS";
    }


    @ApiOperation(value = "执行入口-解析Excel",response = Map.class)
    @GetMapping("/test02")
    @ApiImplicitParams({
            @ApiImplicitParam(name = "userName", value = "用户名", required = true, dataType="string",
                    defaultValue = "admin", example = "admin"),
            @ApiImplicitParam(name = "password", value = "密码", required = true, dataType="string",
                    defaultValue = "123456", example = "123456")
    })
    public Map test02(String userName, String password){

        Map map = new HashMap();
        map.put("userName" , userName);
        map.put("password" , password);
        return map;
    }


    @ApiOperation(value = "测试传值-JSOn解析",response = Map.class)
    @PostMapping("/testJson01")
    public Map testJson01(@RequestBody UserVO userVO) {

        Map map = new HashMap();
        map.put("data" , userVO);
        return map;
    }
}
```

启动之后访问

```
http://localhost:2215/swagger-demo/swagger-ui.html
http://localhost:2215/swagger-demo/doc.html
```

理论上springboot 2之后默认放行 `webjars/**`、`classpath:/META-INF/resources/`

如果是旧版，需要放行资源

```java
@Configuration
@EnabLeWebMvc
@ComponentScan
public class WebConfig extends WebMvcConfigurerAdapter {  // boot 1.x
//public class WebConfig implements WebMvcConfigurer { boot 2.x
    @Autowired
    private WorkInterceptor workInterceptor;

    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        //拦截规则：除了login，其他部拦截判断
        registry.addInterceptor(workInterceptor).addPathPatterns("/**")
            .excludePathPatterns("/UserTcController/login","/UserTcController/defaultKaptcha");
        super.addInterceptors(registry);
    }
    @Override
    public void addResourceHandlers(ResourceHandlerRegistry registry){
            registry.addResourceHandler("sWagger-ui.html", "doc.html")
                    .addResourceLocations("classpath:/META-INF/resources/");
            registry.addResourceHandler("/webjars/**")
                    .addResourceLocations("classpath: /META-INF/resources/webjars/") ;
    }
}
```

## 可选-拦截防止越权校验

> 刚好代码里有就临时放在这里了。

拦截器（可选）

```java
import com.fndsoft.cpic.configer.interceptor.interceptors.InterfaceInerceptor;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Component;
import org.springframework.util.Objectutils;
import org.springframework.web.servlet.HandlerInterceptor;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.web.servlet.ModelAndView;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

@Component  
public class WorkInterceptor implements HandlerInterceptor {
    Logger logger = LoggerFactory.getLogger(WorkInterceptor.class);
    @Autowired
    InterfaceInerceptor interfaceInerceptor;
    
    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponsee response, Object handler)throws Exception {
        logger.info("------preHandle---");
        return interfaceInerceptor.getInterfaceFlag(request.getRequestURI());
    }
    @Override
    public void postHandle(HttpServletRequest request, HttpServletResponsee response, 
            Object handler,ModelAndView modelAndView) throws Exception {
        // ToDo Auto-generated method stub 请求之后
    }
    @overridepublic void afterCompletion(HttpServletRequestrequest, HttpServletResponse response,
        Object handler, Exception ex)throws Exception {
    // ToDo Auto-generated method stub 完成之后
    }
}
    
```


```java

@Service
public class InterfaceInerceptor{
    // 校验用户是否允许访问
    public Boolean getInterfaceFlag(String requesturl){
        /**获取接口拦截器开关**/
        RedisAmsSwitchDetailTc redisAmsSwitchDetailTc = amsSwitchDetailTcRedis.findByNanmeAndValue("Inerceptors", "Interface");
        /**获取所有需要拦截的接口 （所有菜单配置过的接口清单）**/ 
        String interfaceAll= AmsUserUtil.getCurrentInterfaceAll(); 
        /**获取有权限的接口 （当前用户有权限的菜单配置的接口清单）**/
        String interfaces = AmsUserUtil.getCurrentInterfaces();
        
        if(objectUtils.isEmpty(redisAmsSwitchDetailTc) || "0".equals(redisAmsSwitchDetailTc.getValue1())|| 
            interfaces==null || interfaceAll==null){
            return true;//未取到开关视同拦截器关闭2024-08-2810
        }

        // /**不在需要拦截的接口类的请求地址不拦截**/
        if(interfaceAll.indexof(requesturl) == -1){
            return true;
        }
        
        /**在需要拦截的接口类的请求地址如无权限拦截**/
        if(interfaces.indexof(requesturl) == -1){
            throw new BusinessException("由于您没有该接口访问权限！请求已被拦截器阻断！")
        }
        return true;
    }   
}
```

登录的时候查出来放到session

```java
@Service
public class UserServiceImpl implements UserServiceI{

    
    public void login(AmsUserTc userTc){
        //查询用户拥有的菜单权限
        List<AmsMenuTc> amsMenuTcs = amsMenuDao.queryAmsMenusByUser(userTc.getOpcode());
        // 其他逻辑
        // ...
        try {
            /*******获取接口地址集合用于接口拦截器校验2--start****/
            String InterfaceALL2 ="";/**需要拦截的接口*/
            String Interfaces2="";/**有权限的接口*///获取所有莱单
            AmsInterfacesTc intertc = new AmsInterfacesTc();
            intertc.setWhiteflag("0"); //只查非白名单接口
            // 所有配置过的接口清单
            List<AmsInterfacesTc> amsInterfacesTcs = amsInterfacesDao.queryInterfaceList(intertc);
            for (AmsInterfacesTc menuTc : amsInterfacesTcs) {
                InterfaceAll +="," + menuTc.getInterurl();
                List<String> menuidList = new ArrayList<>();
            }
            for (AmsMenuTc menuTc : amsMenuTcs) {
                // 只取 二级菜单
                for(AmsMenuTc menuTcj : menuTc.getAmsMenuTc()){
                    menuidList.add(menuTcj .getMenuid()) ;
                }
            }
            List<AmsInterfacesTc> stringList = amsInterfacesDao.queryAmsInterfacesTcByMenuIds(menuidList) ;
            Interfaces = stringlist.stream().map(AmsInterfacesTc::getInterurl).collect(Collectors.joining( delimiter: ","));
            //rtnMap.put("InterfaceAll", InterfaceAll);
            //rtnMap.put("Interfaces", Interfaces);
            logger.info("InterfaceAll {}", InterfaceAll);
            logger.info("Interfaces {", Interfaces);
            /*******获取接口地址集合用于接口拦截器校验2--end****/
        }catch (Exception e){
              logger.info("查接口菜单权限出错{}", e);
        }
        rtnMap.put("InterfaceAll", InterfaceAll);
        rtnMap.put("Interfaces", Interfaces);
        
        rtnMap.put("userTc", amsUserTc1);
        rtnMap.put("amsMenuTcs", amsMenuTcs); //菜单
        rtnMap.put("amsSubCompanyTcs", amsSubCompanyTcs); // 分公司
        rtnMap.put("amsRoleTcs", amsRoleTcs); // 角色
        rtnMap.put("powerMap",powerMap); // 权限
        rtnMap.put("passwordStatus",pwStatus);
        // 放到session
        session.SetAttribute(GlobleConstant.SESSION_CODE, rtnMap);
    }
}
```
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



## 配置与使用demo

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
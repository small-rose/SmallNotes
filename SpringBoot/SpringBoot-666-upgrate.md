---
layout: default
title: SpringBoot upgrate
parent: SpringBoot
nav_order: 600
---

Here are SpringBoot upgrade experience .
{: .fs-6 .fw-300 }


## Table of contents
{: .no_toc .text-delta }



## SpringBoot 升级关注事项

> 一般为现有组件安全漏洞多，需要升级到无安全漏洞版本。

## 一、版本适配

SpringBoot 1.5.13.RELEASE 升级 SpringBoot 2.5.12

**1、主干版本适配**

主干版本主要是指以 SpringBoot 为主干框架的依赖，主要包含 SpringBoot系列starter, 依赖特征是 spring-boot-starter-xxx

常见的有:

```text
compile('org.springframework.boot:spring-boot-starter-web')
compile('org.springframework.boot:spring-boot-starter-tomcat')
compile('org.springframework.boot:spring-boot-starter-data-jpa')
compile('org.springframework.boot:spring-boot-starter-data-redis')
compile('org.springframework.boot:spring-boot-starter-validation')
compile('org.springframework.boot:spring-boot-starter-cache')
compile('org.springframework.boot:spring-boot-starter-actuator')
compile('org.springframework.boot:spring-boot-starter-redis')
compile('org.springframework.boot:spring-boot-starter-mail')
compile('org.springframework.boot:spring-boot-starter-thymeleaf')
compile('org.springframework.boot:spring-boot-starter-test')
```

想看对应的版本还是得查官方文档：

现有主干版本依赖: [spring-boot-1.5.13.RELEASE#dependency-versions](https://docs.spring.io/spring-boot/docs/1.5.13.RELEASE/reference/html/appendix-dependency-versions.html
)

升级目标版本组件依赖: [spring-boot-2.5.13#dependency-versions](https://docs.spring.io/spring-boot/docs/2.5.13/reference/html/dependency-versions.html#appendix.dependency-versions)

 
**2.依赖主干版本组件适配**

兼容主干版本组件主要是依托springboot系的组件, 或者依托spring系的组件，前者依赖特征一般是:
 
- xxx-spring-boot-starter
- xxx-spring-boot-starter-xxx

如:

```text
compile("com.alibaba:druid-spring-boot-starter:1.2.23")
compile('org.apache.cxf:cxf-spring-boot-starter-jaxws:3.5.2')
implementation("org.mybatis.spring.boot:mybatis-spring-boot-starter:3.0.3")
```


后者一般无明细特征，但组件pom中会体现出会spring系组件依赖，如 

```text
compile('io.springfox:springfox-swagger2:2.10.0')
compile('io.springfox:springfox-spring-webmvc:2.10.0')
compile('io.springfox:springfox-swagger-ui:2.l0.0')

compile('org.springframework.session:spring-session-core:2.5.5')
compile('org.springframework.session:spring-session-jdbc:2.5.5')

compile('io.springfox:springfox-swagger-ui:2.9.1')
compile('com.github.xiaoymin:swagger-bootstrap-ui:1.9.6'){
    exclude module: 'spring-web'
    exclude module: 'spring-webmvc'
}
```
如果涉及SpringCloud，通用与主干版本匹配的SpringCloud 版本。[spring-cloud](https://spring.io/projects/spring-cloud#overview)

关于SpringCloud 和 SpringBoot 的版本搭配查询[spring-cloud-alibaba](https://github.com/alibaba/spring-cloud-alibaba/wiki/%E7%89%88%E6%9C%AC%E8%AF%B4%E6%98%8E)

{: .tips }
> 另外，无论是何种组件，升级后会有可能会导致依赖包变动。如果依赖包减少，导包变红，需要对比组件，额外引入减少的依赖包。如果是依赖包增加，一般问题比较少，最好是在无影响的情况下将额外引入的依赖去除。


**3.辅助组件版本适配**

辅助组件版本一般涉及功能组件、或工具组件。

如webservice相关组件: axis、jaxrpc、caf 

如工具类组件: snakeyaml、xstream、json、fastjson、kaptcha、hutool、commons-系列


## 二、配置或API适配

相关组件更换版本后，更新maven或者gradle版本环境，刷新maven或gradle,使用升级以后组件进行代码编译。

当出现编译报错现象，一般分几大类

1、配置项不兼容（配置文件项、配置文件性质的bean、拦截器、过滤器、启动类）

  主要表现: 
  
  - 原有接口的api路径变化（重新导入）
  - 原有接口api过期 （不变动或使用替代api）
  - 原有api消失 （必须使用替代api）
  - 旧的手工配置、升级后有默认配置可能需要排除自动化配置
  - 启动配置有时参数有强验证 （根据需要调整参数）
  - 启动时配置其他错误

2、组件api不兼容

  - 原有同名api消失、
  - 原有同名api参数变化、
  - 原有同名api过期、
  - 替代api逻辑变化（如原来可以返回null,现在变成抛异常）

编译报错解决后，项目启动后功能测试可能产生报错：

   - 编译问题，见上1和2。
   - 编译正常，运行时可能会报错，如缺少某些配置
   - api与原有兼容，但是逻辑意义发生变化   
   - api重写兼容，需要调整参数，2个参数变3个参数
   
> 一般进行版本选择时没有太大差异，且无冲突时，不会出现此类情况，但不排除升级前后的配置差异。
   
## 三、全量功能测试

API等代码变动，需要全面功能覆盖测试，保证功能正常。   
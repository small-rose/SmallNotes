---
layout: default
title: SpringBoot upgrate
parent: SpringBoot
nav_order: 600
---

Here are SpringBoot upgrade experience .
{: .fs-6 .fw-300 }


## Here are SpringBoot upgrade experience .
{: .no_toc .text-delta }

1. TOC
{:toc}

## SpringBoot 升级关注事项

> 一般为现有组件安全漏洞多，需要升级到无安全漏洞版本；
> 
> 项目重构，同步技术架构升级；
>  
> 项目架构调整等等。

## 一、版本适配

SpringBoot 1.5.13.RELEASE 升级 SpringBoot 2.5.12

**1、主干版本适配**

主干版本主要是指以 SpringBoot 为主干框架的依赖，主要包含 SpringBoot系列starter, 依赖特征是 spring-boot-starter-xxx

常见的有:

```groovy
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

```groovy
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

如工具类组件: snakeyaml、[xstream](http://x-stream.github.io/) 、[json-smart](https://gitcode.com/gh_mirrors/js/json-smart-v2/overview) 、json、fastjson、kaptcha、hutool、commons-系列

其他： [druid](https://github.com/alibaba/druid/wiki)

```groovy
compile('com.thoughtworks.xstream:xstream:1.4.20')
```



{: .tips}
> xstream 1.4.17 -> 1.4.20 版本
>
> com.thoughtworks.xstream.security.ForbiddenClassException
>
> 从1.14.12版本开始，XStream包含一个安全框架，用于在反序列化时为允许的类型实现黑名单或白名单。在1.4.17版本之前，XStream保留了一个默认的黑名单，以拒绝用于各种安全攻击的所有类型的Java运行时，以确保现有用户的最佳运行时兼容性。然而，这种方法失败了。过去几个月的情况表明，仅Java运行时就包含数十种可用于攻击的类型，甚至不考虑类路径上的第三方库。
> 因此，XStream的新版本 1.4.18 现在默认使用白名单，任何遵循建议并为自己的场景初始化安全框架的人都可以轻松更新到新版本，没有任何问题。其他人现在必须进行适当的初始化，否则新版本在反序列化时肯定会失败。

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


## 四、hibernate 升级

本例 hibernate 5.0.1.Final  升级 5.4.33

Hibernate主要是通过三个组件来实现：

  -  hibernate-core：Hibernate的核心实现，提供了Hibernate所有的核心功能。 
  -  hibernate-entitymanager：Hibernate实现了标准的JPA，可以把它看成hibernate-core和JPA之间的适配器，它并不直接提供ORM的功能，而是对hibernate-core进行封装，使得Hibernate符合JPA的规范。
  -  hibernate-annotation：Hibernate支持annotation方式配置的基础，它包括了标准的JPA annotation以及Hibernate自身特殊功能的annotation。


Hibernate 5.0 开始对 JPA 2.1 特定功能的支持（存储过程支持）。

Hibernate 5.2 是使用 Java 8 JDK 构建的

Hibernate 5.2  需要 Java 1.8 and JDBC 4.2.

Hibernate 5.2  hibernate-java8 模块已合并到 hibernate-core 中，并且现在原生支持 Java 8 日期/时间类型。hibernate-entitymanager 合并到 hibernate-core

org.hibernate.SessionFactory 现在扩展了javax.persistence.EntityManagerFactory - 暂时它在技术上扩展了org.hibernate.jpa.HibernateEntityManagerFactory （反过来又扩展了javax.persistence.EntityManagerFactory ）以实现向后兼容性。 HibernateEntityManagerFactory 已弃用。

org.hibernate.Session 现在扩展了javax.persistence.EntityManager - 暂时它在技术上扩展了org.hibernate.jpa.HibernateEntityManager （它又扩展了javax.persistence.EntityManager ）以实现向后兼容性。 HibernateEntityManager已弃用。


EntityManagerFactory是应用程序域模型到数据库的映射的线程安全（且不可变）表示，充当org.hibernate.Session实例的工厂。 

EntityManagerFactory是SessionFactory的 JPA 等效项，基本上，这两者汇聚到同一个SessionFactory实现中。


org.hibernate.Query （已弃用，取而代之的是新的org.hibernate.query.Query ）现在扩展了 JPA 契约javax.persistence.Query和javax.persistence.TypedQuery 。还有ProcedureCall和StoredProcedureQuery 。


org.hibernate.HibernateException现在扩展了javax.persistence.PersistenceExceptions 。现在，“覆盖”JPA 对应方法的 Hibernate 方法也会根据 JPA 契约的要求抛出各种 JDK 定义的 RuntimeException（例如IllegalArgumentException 、 IllegalStateException等）。

持久/类型访问现在通过org.hibernate.Metamodel公开，它扩展了javax.persistence.metamodel.Metamodel 。 MetamodelImpl 现在管理类型系统的所有方面（见下文）。
缓存管理也得到了整合。 org.hibernate.Cache现在扩展了javax.persistence.Cache 。 CacheImpl 现在管理缓存区域的所有方面（见下文）。


SessionFactoryImplementor 曾经有许多与管理和访问实体和集合持久化相关的方法。由于无论如何我们都需要处理 JPA Metamodel 合约，所以我继续将所有代码移至新的org.hibernate.metamodel.spi.MetamodelImplementor中

SessionFactory 和 SessionFactoryImplementor 各自都有许多处理缓存区域的方法。自 5.0 以来，其中许多方法已被弃用，这些方法将被删除。然而，该功能已移至org.hibernate.Cache和org.hibernate.engine.spi.CacheImplementor合约中，帮助实现 JPA 的javax.persistence.Cache角色。

> 启动错误 Caused by: org.springframework.beans.factory.NoUniqueBeanDefinitionException: No qualifying bean of type'javax.persistence.EntityManagerFactory' available: expected single matching bean but found 2: sessionFactory,entityManagerFactory


Hibernate 5.3 添加了对 JPA 2.2 规范的支持


关键配置

```java
@Configuration
@EnableJpaRepositories
@EnableTransactionManagement
public class JpaConfig {

  @Bean
  public DataSource dataSource() {

    EmbeddedDatabaseBuilder builder = new EmbeddedDatabaseBuilder();
    return builder.setType(EmbeddedDatabaseType.HSQL).build();
  }

  @Bean
  public LocalContainerEntityManagerFactoryBean entityManagerFactory() {

    HibernateJpaVendorAdapter vendorAdapter = new HibernateJpaVendorAdapter();
    vendorAdapter.setGenerateDdl(true);
    vendorAdapter.setShowSql(true);
    LocalContainerEntityManagerFactoryBean factory = new LocalContainerEntityManagerFactoryBean();
    factory.setJpaVendorAdapter(vendorAdapter);
    factory.setPackagesToScan("com.acme.domain");
    factory.setDataSource(dataSource());
    return factory;
  }

  @Bean
  public PlatformTransactionManager transactionManager(EntityManagerFactory entityManagerFactory) {

    JpaTransactionManager txManager = new JpaTransactionManager();
    txManager.setEntityManagerFactory(entityManagerFactory);
    return txManager;
  }
    
  /** 兼容历史 sessionFactory
   **/
  @Bean
  public SessionFactory sessionFactory(EntityManagerFactory emf){
    logger.info(" SessionFactory init success ! ");
    return emf.unwrap(SessionFactory.class);
  }
}
```

> 创建LocalContainerEntityManagerFactoryBean而不是EntityManagerFactory ，因为前者除了创建EntityManagerFactory之外还参与异常转换机制。


Springboot 是约定大于配置的思想，因此在默认情况下，只要你的 datasource 不是 **spring.datasource.** 的配置到内置的 DataSourceProperties 对象里面，那么就有可能不符合默认机制。

SpringBoot 内置了最常见的三种数据源的自动化配置：
  -  HikariDataSource  （2.0 之后就推荐使用 Hikari）
  -  tomcat的JDBC
  -  apache的dbcp
    
默认情况下 Datasource 的 EntityManagerFactory 和 TransactionManager 加载机制：

主要自动化配置类：HibernateJpaConfiguration、JpaBaseConfiguration

SpringBoot自动化装配机制（模块化装配、条件装配）完成自动化配置：

**数据源部分**:

DatasourceAutoConfiguration 配置类默认去加载 spring.datasource 开头的相关数据源参数，创建DataSource组件。 如果使用默认的就需要自己编写JavaConfig 类是创建DataSource。

**Jpa组件部分**：
 
总体上是通过 JpaBaseConfiguration 配置类去创建spring data jpa 需要的基本组件 EntityManagerFactory、TransactionManager 组件。

通过 JpaRepositoryConfiguration 配置类中 @Import(JpaRepositoryImportSelector.class) 导入JpaRepositoryImportSelector。

JpaRepositoryImportSelector 又会注册一个ImportBeanDefinitionRegistrar，其实就 JpaRepositoryRegistrar.

JpaRepositoryRegistrar 使用的 @EnableJpaRepository 的方式导入激活JPA组件。

JpaRepositoryConfiguration 的作用包含了 @EnableJpaRepository 。


> 在多数据源或一些老系统升级时使用传统的配置方式情况下，可能会导致JPA组件的自动装配失效，可能会出现找不到 entityManager 或者 提示发现多个 entityManager 的类型的情况，就需要人工进行灵活配置。 

多数据源部分可参数网友提供的方案[https://blog.51cto.com/u_16099276/9882122](https://blog.51cto.com/u_16099276/9882122)

整体配置类似，不同数据源可以扫描不同的包，类似分包机制进行事务、session隔离，也可以将数据源换成动态数据源，只是在使用动态数据时，务必要在开启事务之前切换数据源，进入事务后是无法进行数据源切换的。


**官网spring-data-examples**

SpringData Jpa [官方spring-data-examples](https://github.com/spring-projects/spring-data-examples/tree/main/jpa)



## 五、升级主要变动

`build.gradle` 变化

```groovy
buildscript ext {
    //springBootVersion = '1.5.l3.RELEASE' 
    SpringBootVersion = '2.5.12'
    repositories {
        mavenLocal()
        maven {
           url "http://your.repository.com/artifactory/property"
        }
        dependencies{
            classpath "org.springframework:springloaded:1.2.7.RELEASE"
            classpath("org.springframework.boot:spring-boot-gradle-plugin:${springBootVersion}") {
                exclude module: 'jackson-annotations'
                exclude module: 'jackson-core'
                exclude module: 'jackson-databind'
                exclude module: 'jackson-module-parameter-names'
            }
            //classpath("io.spring.gradle:dependency-management-plugin:1.0.8.RELEASE")
            classpath("io.spring.gradle:dependency-management-plugin:1.0.11.RELEASE")
        }
    }

apply plugin: 'java'
//apply plugin:: 'eclipse'
apply plugin: 'org.springframework.boot'
apply plugin: 'war'

dependencies{

    }
}
```


**补充依赖 `spring-boot-starter-validation`**

```groovy
compile('org.springframework.boot:spring-boot-starter-data-jpa')
compile('org.springframework.boot:spring-boot-starter-validation')
```


**hibernate5.SessionHolder**

> org.springframework.orm.jpa.EntityManagerHolder cannot be cast to org.Springframework.orm.hibernate5.SessionHolder

解决思路：JPA配置和Hibernate 配置冲突，如果没有使用 jap,可以把HibernateJpaAutoConfiguration这个类的自动化配置排除。

**org.hibernate.MappingException**

> org.hibernate.MappingException: The increment size of the [xyz] sequence is set to [50] in the entity mapping while the associated database sequence increment size is [1]

解决思路：调整@SequenceGenerator注解参数 allocationSize 。

@SequenceGenerator定义

```java
@Target({TYPE, METHOD, FIELD})   
@Retention(RUNTIME)  
public @interface SequenceGenerator {  
 String name();  
 String sequenceName() default "";  
 int initialValue() default 0;  
 int allocationSize() default 50;  
}
```

- name属性表示该表主键生成策略的名称，它被引用在@GeneratedValue中设置的“generator”值中。 
- sequenceName属性表示生成策略用到的数据库序列名称。 
- initialValue表示主键初识值，默认为0。 
- allocationSize表示每次主键值增加的大小，例如设置成1，则表示每次创建新记录后自动加1，默认为50。 

需要将 allocationSize 设置为 1 。


**JPA / Hibernate API 变化**

旧版

```java
public interface CrudRepository<T, ID extends Serializable> extends Repository<T, ID> {

    <S extends T> S save(S entity);
    <S extends T> Iterable<s> save(Iterable<s> entities);
    //@return the entity with the given id or {@literal null} if none found
    T findOne(ID id) ;
    boolean exists(ID id);
    Iterable<T> findAll();
    Iterable<T> findAll(Iterable<ID> ids);
    long count();void delete(ID id) ;
    void delete(T entity);
    void delete(Iterable<? extends T> entities);
    void deleteAll();
}
```

新版

```java
public interface CrudRepository<T, ID> extends Repository<T,ID> {
    <S extends T> S save(S entity);
    <S extends T> Iterable<S> saveAll(Iterable<s> entities);
    //@return the entity with the given id or {@literal Optional#empty()l if none found.
    Optional<T> findById(ID id);
    boolean existsById(ID id);
    Iterable<T> findAll();
    Iterable<T> findAllById(Iterable<ID> ids);
    long count();
    void deleteById(ID id) ;
    void delete(T entity);
    void deleteAllById(Iterable<? extends ID> ids);
    void deleteAll(Iterable<? extends T> entities);
    void deleteAll();
}
```

旧API

`T findone (ID id)`  -参数不为空，找不到值可以返回null

新API

(1) `<S extends T> Optional<s> findone (Example<s> example);` --参数写法更换，返回optional-参数写法更换，返回optional来源 JpaSpecificationExecutor来源 QueryByExampleExecutor

(2) `Optional<T> findone(@Nullable Specification<T> spec);`

(3) `Optional<T> findById(ID id) ;`.--参数不为空，返回optional来源 CrudRepository

(4) `T getById(ID id);`（本意替换getOne方法）参数不为空，找不到值EntityNotFoundException来源 JpaRepository


旧：

`Iterable<T> findAll() ;`

`Iterable<T> findAll(Iterable<ID> ids) ;`

新：

`List<T> findAllById(Iterable<ID> ids);` 来源JpaRepository

`<S extends T> List<s> findAll (Example<s> example) ;` 来源JpaRepository

`<S extends T> List<s> findAll(Example<s> example, Sort sort) ;` 来源JpaRepository

旧：

`<S extends T> S save(S entity);` 来源 CrudRepository

`<S extends T> Iterable<s> save(Iterable<s> entities) ;` 来源 CrudRepository

新：
分离save,saveAll来源 CrudRepository

`<S extendsT> S save(S entity);`

<`S extendsT> List<s> saveAll(Iterable<S> entities) ;`

旧：

`void delete(ID id);` 来源 CrudRepository

`void delete(T entity);`

`void delete(Iterable<? extends T> entities);`

新：

`void deleteById(ID id);` 来源CrudRepository

`void deleteById(T entity);`·`

`void deleteAllById(Iterable<? extends ID> ids);`·`

分页：

旧：

```
Pageable pageable = new PageRequest(page.getPageNo()-1, page.getPageSize());
Pageable pageable = PageRequest.of (pageNo-1,pageSize, Sort.by(orders)) ;
```
新 
```
Pageable pageable = PageRequest.of(page.getPageNo()-1, page.getPageSize()) ;
Pageable pageable = PageRequest.of(pageNo-1,pageSize, Sort.by(orders)) ;
```

**SpringBoot 配置变化**

- gradle 版本 > gradle 6.9 
- Tomcat 9


spring boot 1.5.13

```
server.session.timeout=3600

logging.path=/app/logs

```

spring boot 2.5.13

```
server.servlet.session.timeout=30m
server.servlet.session.timeout=3600s

logging.file.path=/app/logs
```


对应取 session的超时时间变化

```
serverProperties.getSession().getTimeout()
Duration duration =serverProperties.getServlet().getSession().getTimeout():
```


路径变化

```
importorg.springframework.boot.web.support.SpringBootServletInitializer;
变更为
importorg.springframework.boot.web.servlet.support.SpringBootServletInitializer;
```


`org.springframework.web.servlet.config.annotation.WebMvcConfigurepAdapter` 过期
使用 `org.springframework.web.servlet.config.annotation.WebMvcConfigurer` 替代;



**druid 强制校验**

```groovy
compile('com.alibaba:druid:1.2.9')
```


> java.sql.SQLException: keepAliveBetweenTimeMillis must be grater than timeBetweenEvictionRunsMillis

调大 `spring.datasource.keepAliveBetweenTimeMillis=120000`



**适配jedis版本**

```groovy
compile('redis.clients:jedis:3.6.3')
```


**spring-session 组件变化**

原适配 spring boot 1.5.13

```groovy
compile('org.springframework.session:spring-session')
```

对应表脚本

```sql
CREATE TABLE SPRING_SESSION (
    SESSION_ID VARCHAR2 (36) NOT NULL ENABLE,
    CREATION_TIME NUMBER(19) NOT NULL ENABLE,
    LAST_ACCESS_TIME NUMBER(19) NOT NULL ENABLE,
    MAX_INACTIVE_INTERVAL NUMBER(1O) NOT NULL ENABLE,
    PRINCIPAL_NAME VARCHAR2 (100),
    CONSTRAINT SPRING_SESSION_PK PRIMARY KEY (SESSION_ID)
);

COMMENT ON TABLE SPRING_SESSION IS '会话表';

CREATE INDEX SPRING_SESSION_IX1 on SPRING_SESSION (LAST_ACCESS_TIME) GLOBAL ;

CREATE TABLE SPRING_SESSION_ATTRIBUTES (
    SESSION_ID VARCHAR2 (36) NOT NULL ENABLE,
    ATTRIBUTE_NAME VARCHAR2 (20O) NOT NULL ENABLE,
    ATTRIBUTE_BYTES BLOB,
    CONSTRAINT "SPRING_SESSION_ATTRIBUTES_PR" PRIMARY KEY (SESSION_ID, ATTRIBUTE_NAME),
    CONSTRAINT "SPRING_SESSION_ATTRIBUTES_FK" FOREIGN KEY (SESSION_ID) REFERENCES SPRING_SESSION(SESSION_ID) ON DELETE CASCADE
);

COMMENT ON TABLE SPRING_SESSION_ATTRIBUTES IS '会话参数表';

CREATE INDEX SPRING_SESSION_ATTRIBUTES_IX1 on SPRING_SESSION_ATTRIBUTESSESSION_ID)GLOBAL；
```


升级适配 spring boot 2.5.13

```groovy
compile('org.springframework.session:spring-session-core')
compile('org.springframework.session:spring-session-jdbc')
```

对应表脚本

```sql
CREATE TABLE SPRING_SESSION (
    PRIMARY_ID CHAR(36) NOT NULL,
    SESSION_ID CHAR(36) NOT NULL,
    CREATION_TIME NUMBER(19,0) NOT NULL,
    LAST_ACCESS_TIME NUMBER(19,0) NOT NULL,
    MAX_INACTIVE_INTERVAL NUMBER(1O,0) NOT NULL,
    EXPIRY_TIME NUMBER(19,0) NOT NULL,
    PRINCIPAL_NAME VARCHAR2 (100 CHAR),
    CONSTRAINT SPRING_SESSION_PK PRIMARY KEY (PRIMARY_ID)
);

CREATE UNIQUE INDEX SPRING_SESSION_IX1 ON SPRING_SESSION (SESSION_ID) ;

CREATE INDEX SPRING_SESSION_IX2 ON SPRING_SESSIONI(EXPIRY_TIME);
CREATE INDEX SPRING_SESSION_IX3 ON SPRING_SESSION (PRINCIPAL_NAME) ;

CREATE TABLE SPRING_SESSION_ATTRIBUTES (
    SESSION_PRIMARY_ID CHAR(36) NOT NULL,
    ATTRIBUTE_NAME VARCHAR2 (200 CHAR) NOT NULL,
    ATTRIBUTE_BYTES BLOB NOT NULL,
    CONSTRAINT SPRING_SESSION_ATTRIBUTES_PK PRIMARY KEY(SESSION_PRIMARY_ID, ATTRIBUTE_NAME),
    CONSTRAINT SPRING_SESSION_ATTRIBUTES_FK FOREIGN KEY(SESSION_PRIMARY_ID) REFERENCES SPRING_SESSION(PRIMARY_ID) ON DELETE CASCADE
);
```

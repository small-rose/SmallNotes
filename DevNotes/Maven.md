---
layout: default
title: Maven 
parent: DevNotes
nav_order: 3
---

Here are Maven  experience .
{: .fs-6 .fw-300 }


## Here are Maven experience .
{: .no_toc .text-delta }

1. TOC
{:toc}


# Maven

## Maven 介绍

Maven 是 Apache 软件基金会组织维护的一款自动化构建工具，专注服务于 Java 平台的项目构建和依赖管理。

Maven 这个单词的本意是：专家，内行。

为什么要使用 Maven？它能帮助我们解决什么问题？

[Maven 官网](https://maven.apache.org/)


- 简化构建过程
- 提供统一的构建系统
- 提供优质的项目信息
- 鼓励更好的开发实践


以下是 Maven 官方话术的主要功能简介：

- 遵循最佳实践的简单项目设置 - 在几秒钟内启动新项目或模块
- 在所有项目中保持一致的使用 - 意味着新开发人员无需等待时间进入项目
- 卓越的依赖项管理，包括自动更新、依赖项关闭（也称为传递依赖项）
- 能够轻松地同时处理多个项目
- 一个庞大且不断增长的库和元数据存储库 ，开箱即用，并与最大的开源项目进行了安排，以实时提供其最新版本
- 可扩展，能够轻松地 编写插件 用 Java 或脚本语言
- 即时访问新功能，只需少量或无需额外配置
- 用于在 Maven 外部进行依赖项管理和部署的 Ant 任务
- 基于模型的构建：Maven 能够根据有关项目的元数据将任意数量的项目构建为预定义的输出类型，例如 JAR、WAR 或分发，在大多数情况下无需编写任何脚本。
- 项目信息的连贯站点：使用与构建过程相同的元数据，Maven 能够生成一个网站或 PDF，包括您想要添加的任何文档，并添加到该标准报告中有关项目开发状态的报告。此信息的示例可以在本网站左侧导航的“项目信息”和“项目报告”子菜单下看到。
- 发布管理和分发发布：无需太多额外的配置，Maven 将与您的源代码控制系统（例如 Subversion 或 Git）集成，并根据特定标签管理项目的发布。它还可以将此项目发布到分发位置以供其他项目使用。Maven 能够发布单个输出，例如 JAR、包含其他依赖项和文档的存档，或作为源代码分发。
- 依赖项管理：Maven 鼓励使用 JAR 和其他依赖项的中央存储库。Maven 附带了一种机制，您的项目客户端可以使用该机制从中央 JAR 存储库下载构建项目所需的任何 JAR，这与 Perl 的 CPAN 非常相似。这允许 Maven 用户跨项目重用 JAR，并鼓励项目之间的通信以确保处理向后兼容性问题。 


说人话：

- 使用项目管理和使用走向统一标准化
- 遵循POM标准让所有开发者使用公共依赖仓库，实现各种依赖资源统一管理和使用 [MAVEN 仓库](https://mvnrepository.com/)
- 多项目、父子项目管理便捷



## Maven 环境安装配置

Apache Maven 的安装是提取存档的简单过程 并添加 `bin` 目录中的 `mvn` 命令添加到 `PATH`。

JDK环境安装。


下载Maven存档包后，提取任何目录中的

```bash
unzip apache-maven-3.3.9-bin.zip
```
或
```
tar xzvf apache-maven-3.3.9-bin.tar.gz
```

添加 `bin` 的创建目录 `apache-maven-3.9.9` 自 这 `PATH`环境变量 


1、新建系统环境变量

变量名：M2_HOME 或 MAVEN_HOME

变量值：E:\apache-maven-3.3.9

变量值就是MAVEN安装目录。

{: .tips }
>MAVEN_HOME 适用于 Maven 1， M2_HOME 适用于 Maven 2 及更高版本。
>
>Maven 2 是对 Maven 1 的完全重写，并且不向后兼容。拥有两个不同的 _HOME变量意味着可以在同一台机器上运行这两个变量。
>
>但是从 Maven 3.5.0 开始 M2_HOME 的用法被移除且不再被支持，也就是说，在 **3.5.0** 之后，只需要配置 MAVEN_HOME 即可


1、配置 `PATH` 环境变量

双击用户的环境变量（针对当前用户）或 系统环境变量（针对全部用户的） PATH

新增

```
%M2_HOME%\bin
```
或

```
%MAVEN_HOME%\bin
```

一路保存。

执行 `mvn -v` 命令

```bash
mvn -v
```

打印 Maven 版本信息,Jdk 信息，则配置成功

```
C:\Users\NOTE200001>mvn -v
Apache Maven 3.6.3 (cecedd343002696d0abb50b32b541b8a6ba2883f)
Maven home: D:\apache\apache-maven-3.6.3\bin\..
Java version: 1.8.0_212, vendor: Oracle Corporation, runtime: C:\Program Files\Java\jdk1.8.0_212\jre
Default locale: zh_CN, platform encoding: GBK
OS name: "windows 10", version: "10.0", arch: "amd64", family: "windows"
```

2、在开发工具 `idea` 中配置。

一般是：`window` — `Preferences` — `maven` — `User Settings`  更换 maven 的 setting.xml 位置配置。

有些开发工具已经内嵌了maven，通常**默认（当前）用户配置**的 setting.xml 文件位置在：

```
C:/Users/${user.home}/.m2/setting.xml
```

idea 本身也自带maven

```
D:\dev-tools\JetBrains\IntelliJ IDEA 2020.1\plugins\maven\lib\maven3\conf
```

3、全局配置 setting.xml

配置文件一般在 `E:\apache-maven-3.3.9\conf` 目录下。

（1）本地仓库路径修改：

直接放 `<settings>` 根节点下即可。

```xml
 <localRepository>E:/server/MavenRepository/maven_jar</localRepository>
```
如果不配置，一般模式使用用户默认配置路径是：`C:/Users/${user.home}/.m2/repository`

（2）修改源镜像仓库地址。默认去maven官方源仓库下载太慢了，修改为国内的源仓库。

在mirrors节点下：

```xml

 <!-- 阿里云中央仓库 -->
 <mirror>
       <id>nexus-aliyun</id>
       <mirrorOf>central</mirrorOf>
       <name>Nexus aliyun</name>
       <url>http://maven.aliyun.com/nexus/content/groups/public</url>
 </mirror>
```

常用简化版配置如下:

```xml
<settings xmlns="http://maven.apache.org/SETTINGS/1.0.0"
          xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
          xsi:schemaLocation="http://maven.apache.org/SETTINGS/1.0.0 http://maven.apache.org/xsd/settings-1.0.0.xsd">
   
   <!-- 本地仓库的位置 -->
   <localRepository>D:\apache\MavenRepository\maven_jar</localRepository>

    <!-- 镜像设置，用于指定中央仓库的镜像，以提高依赖项下载的速度 -->
    <mirrors>
        <!-- 阿里私服 -->
        <mirror>
            <id>alimaven</id>
            <mirrorOf>central</mirrorOf>
            <name>aliyun maven</name>
            <url>http://maven.aliyun.com/nexus/content/repositories/central/</url>
        </mirror>

        <!-- 中央仓库1 -->
        <mirror>
            <id>repo1</id>
            <mirrorOf>central</mirrorOf>
            <name>Human Readable Name for this Mirror.</name>
            <url>http://repo1.maven.org/maven2/</url>
        </mirror>

        <!-- 中央仓库2 -->
        <mirror>
            <id>repo2</id>
            <mirrorOf>central</mirrorOf>
            <name>Human Readable Name for this Mirror.</name>
            <url>http://repo2.maven.org/maven2/</url>
        </mirror>
    </mirrors>

</settings>
```

## Maven 配置 setting.xml

settings.xml 文件可能存在于两个位置：

- Maven安装目录：${maven.home}/conf/settings.xml
- 用户安装目录：${user.home}/.m2/settings.xml

前者的 `settings.xml` 也称为全局设置，后者的 `settings.xml` 称为用户设置。

如果两个文件都存在，它们的内容将**合并**，用户特定的 `settings.xml` 文件将具有更高的优先级。

实际应用中，经常使用的是 `<localRepository>`、`<servers>`、`<mirrors>`、`<profiles>` 有限几个节点，其他节点使用默认值足够应对大部分的应用场景。

- localRepository 本地仓库设置
    -  (1) 默认值为 ${user.home}/.m2/repository。
    -  (2) 特别适用于允许所有登录用户从共享本地仓库构建的主要构建服务器。

- Mirrors 镜像配置 
    - id、name：此镜像的唯一标识符和用户友好的名称。
    - id 用于区分镜像元素，并在连接到镜像时从 <servers> 部分中与id值相同的server选择相应的凭据。
    - url：此镜像的基本 URL。构建系统将使用此 URL 连接到仓库，而不是原始的仓库 URL。
    - mirrorOf：此镜像所镜像的仓库的ID。例如，要指向 Maven 中央仓库的镜像（https://repo.maven.apache.org/maven2/），请将此元素设置为 central。更高级的映射如 repo1、repo2 或 *、!inhouse 也是可能的。这个值不能与镜像的ID匹配。

```xml
<settings>
  <mirrors>
    <mirror>
      <id>other-mirror</id>
      <name>Other Mirror Repository</name>
      <url>https://other-mirror.repo.other-company.com/maven2</url>
      <mirrorOf>central</mirrorOf>
    </mirror>
  </mirrors>
</settings>
```

**使用单个仓库**

可以通过使其镜像所有仓库请求来强制 Maven 使用单个仓库。该仓库必须包含所有所需的构件，或者能够将请求代理到其他仓库。当使用内部公司仓库和 Maven 仓库管理器代理外部请求时，此设置最有用。

要实现这一点，需要将 mirrorOf 设置为 *。

> 注意：此功能仅适用于 Maven 2.0.5+。

```xml
<settings>
  
  <mirrors>
    <mirror>
      <id>internal-repository</id>
      <name>Maven Repository Manager running on repo.mycompany.com</name>
      <url>http://repo.mycompany.com/proxy</url>
      <mirrorOf>*</mirrorOf>
    </mirror>
  </mirrors>
  
</settings>
```

**高级镜像规范**

单个镜像可以处理多个仓库。这通常与仓库管理器一起使用，可以方便地集中配置其背后的仓库列表。

语法如下：
 - * 匹配所有仓库 ID。
 - external:* 匹配除使用 localhost 或基于文件的仓库之外的所有仓库。这在您想要排除用于集成测试的重定向仓库时使用。
 - 自 Maven 3.8.0 开始，external:http:* 匹配使用 HTTP 的所有仓库，但排除使用 localhost 的仓库。
 - 可以使用逗号作为分隔符指定多个仓库
 - 惊叹号可以与上述通配符之一结合使用，以排除一个仓库 ID

示例：

- * = 所有内容
- external:* = 不在 localhost 和基于文件的仓库上的所有内容
- repo,repo1 = repo 或 repo1
- *,!repo1 = 除了 repo1 之外的所有内容

```xml
<settings>
  
  <mirrors>
    <mirror>
      <id>internal-repository</id>
      <name>Maven Repository Manager running on repo.mycompany.com</name>
      <url>http://repo.mycompany.com/proxy</url>
      <mirrorOf>external:*,!foo</mirrorOf>
    </mirror>
    <mirror>
      <id>foo-repository</id>
      <name>Foo</name>
      <url>http://repo.mycompany.com/foo</url>
      <mirrorOf>foo</mirrorOf>
    </mirror>
  </mirrors>
  
</settings>
```

## Maven 配置 setting.xml 完整示例

完整详细的示例

```xml
<?xml version="1.0" encoding="UTF-8"?>     
<settings   xmlns="http://maven.apache.org/POM/4.0.0"    
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"    
    xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/settings-1.0.0.xsd">  
        
    <!--本地仓库。该值表示构建系统本地仓库的路径。其默认值为${user.home}/.m2/repository。  -->  
    <localRepository>usr/local/maven</localRepository> 
        
    <!--Maven是否需要和用户交互以获得输入。如果Maven需要和用户交互以获得输入，则设置成true，反之则应为false。默认为true。 -->  
    <interactiveMode>true</interactiveMode>  
        
    <!--Maven是否需要使用plugin-registry.xml文件来管理插件版本。  -->  
    <!--如果设置为true，则在{user.home}/.m2下需要有一个plugin-registry.xml来对plugin的版本进行管理  -->  
    <!--默认为false。 -->  
    <usePluginRegistry>false</usePluginRegistry>  
        
    <!--表示Maven是否需要在离线模式下运行。如果构建系统需要在离线模式下运行，则为true，默认为false。  -->  
    <!--当由于网络设置原因或者安全因素，构建服务器不能连接远程仓库的时候，该配置就十分有用。  -->  
    <offline>false</offline>  
        
    <!--当插件的组织Id（groupId）没有显式提供时，供搜寻插件组织Id（groupId）的列表。  -->  
    <!--该元素包含一个pluginGroup元素列表，每个子元素包含了一个组织Id（groupId）。  -->  
    <!--当我们使用某个插件，并且没有在命令行为其提供组织Id（groupId）的时候，Maven就会使用该列表。  -->  
    <!--默认情况下该列表包含了org.apache.maven.plugins。  -->  
    <pluginGroups>  
            
        <!--plugin的组织Id（groupId）  -->  
        <pluginGroup>org.codehaus.mojo</pluginGroup>  
        <pluginGroup>org.apache</pluginGroup>  
           
    </pluginGroups>  
        
    <!--用来配置不同的代理，多代理profiles可以应对笔记本或移动设备的工作环境：通过简单的设置profile id就可以很容易的更换整个代理配置。  -->  
    <proxies>  
            
        <!--代理元素包含配置代理时需要的信息 -->  
        <proxy>  
                
            <!--代理的唯一定义符，用来区分不同的代理元素。 -->  
            <id>myproxy</id>  
                
            <!--该代理是否是激活的那个。true则激活代理。当我们声明了一组代理，而某个时候只需要激活一个代理的时候，该元素就可以派上用处。  -->  
            <active>true</active>  
                
            <!--代理的协议。 协议://主机名:端口，分隔成离散的元素以方便配置。 -->  
            <protocol>http://…</protocol>  
                
            <!--代理的主机名。协议://主机名:端口，分隔成离散的元素以方便配置。   -->  
            <host>proxy.somewhere.com</host>  
                
            <!--代理的端口。协议://主机名:端口，分隔成离散的元素以方便配置。  -->  
            <port>8080</port>  
                
             <!--代理的用户名，用户名和密码表示代理服务器认证的登录名和密码。  -->  
            <username>proxyuser</username>  
                
            <!--代理的密码，用户名和密码表示代理服务器认证的登录名和密码。  -->  
            <password>somepassword</password>  
                
            <!--不该被代理的主机名列表。该列表的分隔符由代理服务器指定；例子中使用了竖线分隔符，使用逗号分隔也很常见。 -->  
            <nonProxyHosts>*.google.com|ibiblio.org</nonProxyHosts>  
                
        </proxy>  
           
    </proxies>  
        
    <!--配置服务端的一些设置。一些设置如安全证书不应该和pom.xml一起分发。这种类型的信息应该存在于构建服务器上的settings.xml文件中。 -->  
    <servers>  
            
        <!--服务器元素包含配置服务器时需要的信息  -->  
        <server>  
                
            <!--这是server的id（注意不是用户登陆的id），该id与distributionManagement中repository元素的id相匹配。 -->  
            <id>server001</id>  
                
            <!--鉴权用户名。鉴权用户名和鉴权密码表示服务器认证所需要的登录名和密码。  -->  
            <username>my_login</username>  
                
            <!--鉴权密码 。鉴权用户名和鉴权密码表示服务器认证所需要的登录名和密码。  -->  
            <password>my_password</password>  
                
            <!--鉴权时使用的私钥位置。和前两个元素类似，私钥位置和私钥密码指定了一个私钥的路径（默认是/home/hudson/.ssh/id_dsa）以及如果需要的话，一个密钥 -->  
            <!--将来passphrase和password元素可能会被提取到外部，但目前它们必须在settings.xml文件以纯文本的形式声明。  -->  
            <privateKey>${usr.home}/.ssh/id_dsa</privateKey>  
                
            <!--鉴权时使用的私钥密码。 使用私钥登录时，不能有 password 节点-->  
            <passphrase>some_passphrase</passphrase>  
                
            <!--文件被创建时的权限。如果在部署的时候会创建一个仓库文件或者目录，这时候就可以使用权限（permission）。-->  
            <!--这两个元素合法的值是一个三位数字，其对应了unix文件系统的权限，如664，或者775。  -->  
            <filePermissions>664</filePermissions>  
                
            <!--目录被创建时的权限。  -->  
            <directoryPermissions>775</directoryPermissions>  
                
            <!--传输层额外的配置项  -->  
            <configuration></configuration>  
                
        </server>  
           
    </servers>  
        
    <!--为仓库列表配置的下载镜像列表。  -->  
    <mirrors>  
            
        <!--给定仓库的下载镜像。  -->  
        <mirror>  
                
            <!--该镜像的唯一标识符。id用来区分不同的mirror元素。  -->  
            <id>planetmirror.com</id>  
                
            <!--镜像名称  -->  
            <name>PlanetMirror Australia</name>  
                
            <!--该镜像的URL。构建系统会优先考虑使用该URL，而非使用默认的服务器URL。  -->  
            <url>http://downloads.planetmirror.com/pub/maven2</url>  
                
            <!--被镜像的服务器的id。例如，如果我们要设置了一个Maven中央仓库（http://repo1.maven.org/maven2）的镜像，-->  
            <!--就需要将该元素设置成central。这必须和中央仓库的id central完全一致。 -->  
            <mirrorOf>central</mirrorOf>  
                
        </mirror>  
           
    </mirrors>  
        
    <!--根据环境参数来调整构建配置的列表。settings.xml中的profile元素是pom.xml中profile元素的裁剪版本。-->  
    <!--它包含了id，activation, repositories, pluginRepositories和 properties元素。-->  
    <!--这里的profile元素只包含这五个子元素是因为这里只关心构建系统这个整体（这正是settings.xml文件的角色定位），而非单独的项目对象模型设置。-->  
    <!--如果一个settings中的profile被激活，它的值会覆盖任何其它定义在POM中或者profile.xml中的带有相同id的profile。  -->  
    <profiles>  
            
        <!--根据环境参数来调整的构件的配置 -->  
        <profile>  
                
            <!--该配置的唯一标识符。  -->  
            <id>test</id>  
                
            <!--自动触发profile的条件逻辑。Activation是profile的开启钥匙。-->  
            <!--如POM中的profile一样，profile的力量来自于它能够在某些特定的环境中自动使用某些特定的值；这些环境通过activation元素指定。-->  
            <!--activation元素并不是激活profile的唯一方式。settings.xml文件中的activeProfile元素可以包含profile的id。-->  
            <!--profile也可以通过在命令行，使用-P标记和逗号分隔的列表来显式的激活（如，-P test）。 -->  
            <activation>  
                    
                <!--profile默认是否激活的标识 -->  
                <activeByDefault>false</activeByDefault>  
                    
                <!--activation有一个内建的java版本检测，如果检测到jdk版本与期待的一样，profile被激活。 -->  
                <jdk>1.7</jdk>  
                    
                <!--当匹配的操作系统属性被检测到，profile被激活。os元素可以定义一些操作系统相关的属性。 -->  
                <os>  
                        
                    <!--激活profile的操作系统的名字  -->  
                    <name>Windows XP</name>  
                        
                    <!--激活profile的操作系统所属家族(如 'windows')   -->  
                    <family>Windows</family>  
                        
                    <!--激活profile的操作系统体系结构   -->  
                    <arch>x86</arch>  
                        
                    <!--激活profile的操作系统版本 -->  
                    <version>5.1.2600</version>  
                          
                </os>  
                    
                <!--如果Maven检测到某一个属性（其值可以在POM中通过${名称}引用），其拥有对应的名称和值，Profile就会被激活。-->  
                <!--如果值字段是空的，那么存在属性名称字段就会激活profile，否则按区分大小写方式匹配属性值字段 -->  
                <property>  
                        
                    <!--激活profile的属性的名称 -->  
                    <name>mavenVersion</name>  
                        
                    <!--激活profile的属性的值  -->  
                    <value>2.0.3</value>  
                          
                </property>  
                    
                <!--提供一个文件名，通过检测该文件的存在或不存在来激活profile。missing检查文件是否存在，如果不存在则激活profile。-->  
                <!--另一方面，exists则会检查文件是否存在，如果存在则激活profile。 -->  
                <file>  
                        
                    <!--如果指定的文件存在，则激活profile。  -->  
                    <exists>/usr/local/hudson/hudson-home/jobs/maven-guide-zh-to-production/workspace/</exists>  
                        
                    <!--如果指定的文件不存在，则激活profile。 -->  
                    <missing>/usr/local/hudson/hudson-home/jobs/maven-guide-zh-to-production/workspace/</missing>  
                          
                </file>  
                     
            </activation>  
                
             <!--对应profile的扩展属性列表。Maven属性和Ant中的属性一样，可以用来存放一些值。这些值可以在POM中的任何地方使用标记${X}来使用，这里X是指属性的名称。-->  
            <!--属性有五种不同的形式，并且都能在settings.xml文件中访问。   -->  
            <!--1. env.X: 在一个变量前加上"env."的前缀，会返回一个shell环境变量。例如,"env.PATH"指代了$path环境变量（在Windows上是%PATH%）。  -->   
            <!--2. project.x：指代了POM中对应的元素值。      -->  
            <!--3. settings.x: 指代了settings.xml中对应元素的值。   -->  
            <!--4. Java System Properties: 所有可通过java.lang.System.getProperties()访问的属性都能在POM中使用该形式访问，   -->  
            <!--   如/usr/lib/jvm/java-1.6.0-openjdk-1.6.0.0/jre。      -->  
            <!--5. x: 在<properties/>元素中，或者外部文件中设置，以${someVar}的形式使用。  -->  
            <properties>  
              
                <!-- 如果这个profile被激活，那么属性${user.install}就可以被访问了 -->  
                <user.install>usr/local/winner/jobs/maven-guide</user.install>  
                     
            </properties>  
                
            <!--远程仓库列表，它是Maven用来填充构建系统本地仓库所使用的一组远程项目。  -->  
            <repositories>  
                    
                <!--包含需要连接到远程仓库的信息  -->  
                <repository>  
                        
                    <!--远程仓库唯一标识 -->  
                    <id>codehausSnapshots</id>  
                        
                    <!--远程仓库名称  -->  
                    <name>Codehaus Snapshots</name>  
                        
                    <!--如何处理远程仓库里发布版本的下载 -->  
                    <releases>  
                            
                        <!--true或者false表示该仓库是否为下载某种类型构件（发布版，快照版）开启。   -->  
                        <enabled>false</enabled>  
                            
                        <!--该元素指定更新发生的频率。Maven会比较本地POM和远程POM的时间戳。这里的选项是：-->  
                        <!--always（一直），daily（默认，每日），interval：X（这里X是以分钟为单位的时间间隔），或者never（从不）。  -->  
                        <updatePolicy>always</updatePolicy>  
                            
                        <!--当Maven验证构件校验文件失败时该怎么做:-->  
                        <!--ignore（忽略），fail（失败），或者warn（警告）。 -->  
                        <checksumPolicy>warn</checksumPolicy>  
                               
                    </releases>  
                        
                    <!--如何处理远程仓库里快照版本的下载。有了releases和snapshots这两组配置，POM就可以在每个单独的仓库中，为每种类型的构件采取不同的策略。-->  
                    <!--例如，可能有人会决定只为开发目的开启对快照版本下载的支持。参见repositories/repository/releases元素 -->  
                    <snapshots>  
                                
                        <enabled />
                        <updatePolicy />
                        <checksumPolicy />
                               
                    </snapshots>  
                        
                    <!--远程仓库URL，按protocol://hostname/path形式  -->  
                    <url>http://snapshots.maven.codehaus.org/maven2</url>  
                        
                    <!--用于定位和排序构件的仓库布局类型-可以是default（默认）或者legacy（遗留）。-->  
                    <!--Maven 2为其仓库提供了一个默认的布局；然而，Maven 1.x有一种不同的布局。我们可以使用该元素指定布局是default（默认）还是legacy（遗留）。  -->  
                    <layout>default</layout>  
                          
                </repository>  
                     
            </repositories>  
                
            <!--发现插件的远程仓库列表。仓库是两种主要构件的家。第一种构件被用作其它构件的依赖。这是中央仓库中存储的大部分构件类型。另外一种构件类型是插件。-->  
            <!--Maven插件是一种特殊类型的构件。由于这个原因，插件仓库独立于其它仓库。pluginRepositories元素的结构和repositories元素的结构类似。-->  
            <!--每个pluginRepository元素指定一个Maven可以用来寻找新插件的远程地址。 -->  
            <pluginRepositories>  
                    
                <!--包含需要连接到远程插件仓库的信息.参见profiles/profile/repositories/repository元素的说明 -->  
                <pluginRepository>  
                    <releases>  
                        <enabled />
                        <updatePolicy />
                        <checksumPolicy />
                    </releases>  
                           
                    <snapshots>  
                        <enabled /> 
                        <updatePolicy /> 
                        <checksumPolicy /> 
                    </snapshots>  
                           
                    <id />  
                    <name />  
                    <url />  
                    <layout />  
                </pluginRepository>  
                          
            </pluginRepositories>  
                
            <!--手动激活profiles的列表，按照profile被应用的顺序定义activeProfile。 该元素包含了一组activeProfile元素，每个activeProfile都含有一个profile id。-->  
            <!--任何在activeProfile中定义的profile id，不论环境设置如何，其对应的 profile都会被激活。-->  
            <!--如果没有匹配的profile，则什么都不会发生。例如，env-test是一个activeProfile，则在pom.xml（或者profile.xml）中对应id的profile会被激活。-->  
            <!--如果运行过程中找不到这样一个profile，Maven则会像往常一样运行。  -->  
            <activeProfiles>  
                      
                <activeProfile>env-test</activeProfile>  
                     
            </activeProfiles>  
                
        </profile>  
           
    </profiles>  
        
</settings>  
```

## Maven 配置 pom.xml

pom文件定于了一个maven项目的maven配置，一般pom文件的放在项目或者模块的根目录下。

### 目录结构

maven的遵循约定大于配置，约定了如下的目录结构：

- ${basedir}	存放pom.xml和所有的子目录
- ${basedir}/src/main/java	项目的java源代码
- ${basedir}/src/main/resources	项目的资源，比如说property文件，springmvc.xml
- ${basedir}/src/test/java	项目的测试类，比如说Junit代码
- ${basedir}/src/test/resources	测试用的资源
- ${basedir}/src/main/scripts	项目脚本源码的目录
- ${basedir}/src/main/webapp/WEB-INF	web应用文件目录，web项目的信息，比如存放web.xml、本地图片、jsp视图页面
- ${basedir}/target	打包输出目录
- ${basedir}/target/classes	编译输出目录
- ${basedir}/target/site	生成文档的目录，可以通过index.html查看项目的文档
- ${basedir}/target/test-classes	测试编译输出目录
- Test.java	Maven只会自动运行符合该命名规则的测试类
- ~/.m2/repository	Maven默认的本地仓库目录位置


### GAV 坐标

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"  
    xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">  

    <!--该元素描述了项目相关的所有依赖。 这些依赖自动从项目定义的仓库中下载 -->
    <dependencies>
        <dependency>
            <!--  依赖坐标  -->
            <!--依赖项目的坐标三元素：groupId + artifactId + version -->
            <groupId>org.apache.maven</groupId>
            <artifactId>maven-artifact</artifactId>
            <version>3.8.1</version>
 
            <!--  依赖类型  -->
            <!-- 依赖类型，默认是jar。通常表示依赖文件的扩展名，但有例外。一个类型可以被映射成另外一个扩展名或分类器 -->
            <!-- 类型经常和使用的打包方式对应，尽管这也有例外，一些类型的例子：jar，war，ejb-client和test-jar -->
            <!-- 如果设置extensions为true，就可以在plugin里定义新的类型 -->
            <type>jar</type>
            <!-- 依赖的分类器。分类器可以区分属于同一个POM，但不同构建方式的构件。分类器名被附加到文件名的版本号后面 -->
            <!-- 如果想将项目构建成两个单独的JAR，分别使用Java 4和6编译器，就可以使用分类器来生成两个单独的JAR构件 -->
            <classifier></classifier>
 
            <!--  依赖传递  -->
            <!--依赖排除，即告诉maven只依赖指定的项目，不依赖该项目的这些依赖。此元素主要用于解决版本冲突问题 -->
            <exclusions>
                <exclusion>
                    <artifactId>spring-core</artifactId>
                    <groupId>org.springframework</groupId>
                </exclusion>
            </exclusions>
            <!-- 可选依赖，用于阻断依赖的传递性。如果在项目B中把C依赖声明为可选，那么依赖B的项目中无法使用C依赖 -->
            <optional>true</optional>
            
            <!--  依赖范围 -->
            <!--依赖范围。在项目发布过程中，帮助决定哪些构件被包括进来
                - compile：默认范围，用于编译;  - provided：类似于编译，但支持jdk或者容器提供，类似于classpath 
                - runtime: 在执行时需要使用;    - systemPath: 仅用于范围为system。提供相应的路径 
                - test: 用于test任务时使用;    - system: 需要外在提供相应的元素。通过systemPath来取得 
                - optional: 当项目自身被依赖时，标注依赖是否传递。用于连续依赖时使用 -->
            <scope>test</scope>
            <!-- 该元素为依赖规定了文件系统上的路径。仅供scope设置system时使用。但是不推荐使用这个元素 -->
            <!-- 不推荐使用绝对路径，如果必须要用，推荐使用属性匹配绝对路径，例如${java.home} -->
            <systemPath>${basedir}/lib/a.jar</systemPath>
        </dependency>
    </dependencies>
 
    <!-- 继承自该项目的所有子项目的默认依赖信息，这部分的依赖信息不会被立即解析。 -->
    <!-- 当子项目声明一个依赖，如果group ID和artifact ID以外的一些信息没有描述，则使用这里的依赖信息 -->
    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-starter</artifactId>
                <version>3.3.3</version>
            </dependency>
        </dependencies>
    <dependencyManagement/>
<project/>
```

## Maven 配置 pom.xml 完整示例

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"  
    xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">  
    <!--父项目的坐标。如果项目中没有规定某个元素的值，那么父项目中的对应值即为项目的默认值。 坐标包括group ID，artifact ID和 version。 -->  
    <parent>  
        <!--被继承的父项目的构件标识符 -->  
        <artifactId />
        <!--被继承的父项目的全球唯一标识符 -->  
        <groupId />
        <!--被继承的父项目的版本 -->  
        <version />
        <!--父项目的pom.xml文件的相对路径。相对路径允许你选择一个不同的路径。默认值是../pom.xml。Maven首先在构建当前项目的地方寻找父项目的pom，其次在文件系统的这个位置（relativePath位置），然后在本地仓库，最后在远程仓库寻找父项目的pom。 -->  
        <relativePath />  
    </parent>  
    <!--声明项目描述符遵循哪一个POM模型版本。模型本身的版本很少改变，虽然如此，但它仍然是必不可少的，这是为了当Maven引入了新的特性或者其他模型变更的时候，确保稳定性。 -->  
    <modelVersion>4.0.0</modelVersion>  
    <!--项目的全球唯一标识符，通常使用全限定的包名区分该项目和其他项目。并且构建时生成的路径也是由此生成， 如com.mycompany.app生成的相对路径为：/com/mycompany/app -->  
    <groupId>asia.banseon</groupId>  
    <!--构件的标识符，它和group ID一起唯一标识一个构件。换句话说，你不能有两个不同的项目拥有同样的artifact ID和groupID；在某个特定的group   
        ID下，artifact ID也必须是唯一的。构件是项目产生的或使用的一个东西，Maven为项目产生的构件包括：JARs，源码，二进制发布和WARs等。 -->  
    <artifactId>banseon-maven2</artifactId>  
    <!--项目产生的构件类型，例如jar、war、ear、pom。插件可以创建他们自己的构件类型，所以前面列的不是全部构件类型 -->  
    <packaging>jar</packaging>  
    <!--项目当前版本，格式为:主版本.次版本.增量版本-限定版本号 -->  
    <version>1.0-SNAPSHOT</version>  
    <!--项目的名称, Maven产生的文档用 -->  
    <name>banseon-maven</name>  
    <!--项目主页的URL, Maven产生的文档用 -->  
    <url>http://www.baidu.com/banseon</url>  
    <!--项目的详细描述, Maven 产生的文档用。 当这个元素能够用HTML格式描述时（例如，CDATA中的文本会被解析器忽略，就可以包含HTML标签），   
        不鼓励使用纯文本描述。如果你需要修改产生的web站点的索引页面，你应该修改你自己的索引页文件，而不是调整这里的文档。 -->  
    <description>A maven project to study maven.</description>  
    <!--描述了这个项目构建环境中的前提条件。 -->  
    <prerequisites>  
        <!--构建该项目或使用该插件所需要的Maven的最低版本 -->  
        <maven />  
    </prerequisites>  
    <!--项目的问题管理系统(Bugzilla, Jira, Scarab,或任何你喜欢的问题管理系统)的名称和URL，本例为 jira -->  
    <issueManagement>  
        <!--问题管理系统（例如jira）的名字， -->  
        <system>jira</system>  
        <!--该项目使用的问题管理系统的URL -->  
        <url>http://jira.xxxx.com/xxxx</url>  
    </issueManagement>  
    <!--项目持续集成信息 -->  
    <ciManagement>  
        <!--持续集成系统的名字，例如continuum -->  
        <system />  
        <!--该项目使用的持续集成系统的URL（如果持续集成系统有web接口的话）。 -->  
        <url />  
        <!--构建完成时，需要通知的开发者/用户的配置项。包括被通知者信息和通知条件（错误，失败，成功，警告） -->  
        <notifiers>  
            <!--配置一种方式，当构建中断时，以该方式通知用户/开发者 -->  
            <notifier>  
                <!--传送通知的途径 -->  
                <type />  
                <!--发生错误时是否通知 -->  
                <sendOnError />
                <!--构建失败时是否通知 -->  
                <sendOnFailure /> 
                <!--构建成功时是否通知 -->  
                <sendOnSuccess />
                <!--发生警告时是否通知 -->  
                <sendOnWarning />
                <!--不赞成使用。通知发送到哪里 -->  
                <address />  
                <!--扩展配置项 -->  
                <configuration />  
            </notifier>  
        </notifiers>  
    </ciManagement>  
    <!--项目创建年份，4位数字。当产生版权信息时需要使用这个值。 -->  
    <inceptionYear />  
    <!--项目相关邮件列表信息 -->  
    <mailingLists>  
        <!--该元素描述了项目相关的所有邮件列表。自动产生的网站引用这些信息。 -->  
        <mailingList>  
            <!--邮件的名称 -->  
            <name>Demo</name>  
            <!--发送邮件的地址或链接，如果是邮件地址，创建文档时，mailto: 链接会被自动创建 -->  
            <post>Demo@126.com</post>  
            <!--订阅邮件的地址或链接，如果是邮件地址，创建文档时，mailto: 链接会被自动创建 -->  
            <subscribe>Demo@126.com</subscribe>  
            <!--取消订阅邮件的地址或链接，如果是邮件地址，创建文档时，mailto: 链接会被自动创建 -->  
            <unsubscribe>Demo@126.com</unsubscribe>  
            <!--你可以浏览邮件信息的URL -->  
            <archive>http://localhost:8080/demo/dev/</archive>  
        </mailingList>  
    </mailingLists>  
    <!--项目开发者列表 -->  
    <developers>  
        <!--某个项目开发者的信息 -->  
        <developer>  
            <!--SCM里项目开发者的唯一标识符 -->  
            <id>HELLO WORLD</id>  
            <!--项目开发者的全名 -->  
            <name>youname</name>  
            <!--项目开发者的email -->  
            <email>youname@qq.com</email>  
            <!--项目开发者的主页的URL -->  
            <url />  
            <!--项目开发者在项目中扮演的角色，角色元素描述了各种角色 -->  
            <roles>  
                <role>Project Manager</role>  
                <role>Architect</role>  
            </roles>  
            <!--项目开发者所属组织 -->  
            <organization>demo</organization>  
            <!--项目开发者所属组织的URL -->  
            <organizationUrl>http://www.xxx.com/</organizationUrl>  
            <!--项目开发者属性，如即时消息如何处理等 -->  
            <properties>  
                <dept>No</dept>  
            </properties>  
            <!--项目开发者所在时区， -11到12范围内的整数。 -->  
            <timezone>+8</timezone>  
        </developer>  
    </developers>  
    <!--项目的其他贡献者列表 -->  
    <contributors>  
        <!--项目的其他贡献者。参见developers/developer元素 -->  
        <contributor>  
            <name />  
            <email />  
            <url />  
            <organization />  
            <organizationUrl />  
            <roles />  
            <timezone />  
            <properties />  
        </contributor>  
    </contributors>  
    <!--该元素描述了项目所有License列表。 应该只列出该项目的license列表，不要列出依赖项目的 license列表。如果列出多个license，用户可以选择它们中的一个而不是接受所有license。 -->  
    <licenses>  
        <!--描述了项目的license，用于生成项目的web站点的license页面，其他一些报表和validation也会用到该元素。 -->  
        <license>  
            <!--license用于法律上的名称 -->  
            <name>Apache 2</name>  
            <!--官方的license正文页面的URL -->  
            <url>http://www.xxxx.com/LICENSE-2.0.txt</url>  
            <!--项目分发的主要方式： repo，可以从Maven库下载 manual， 用户必须手动下载和安装依赖 -->  
            <distribution>repo</distribution>  
            <!--关于license的补充信息 -->  
            <comments>A business-friendly OSS license</comments>  
        </license>  
    </licenses>  
    <!--SCM(Source Control Management)标签允许你配置你的代码库，供Maven web站点和其它插件使用。 -->  
    <scm>  
        <!--SCM的URL,该URL描述了版本库和如何连接到版本库。欲知详情，请看SCMs提供的URL格式和列表。该连接只读。 -->  
        <connection>  
            scm:svn:http://svn.xxxx.com/maven/xxxxx-maven2-trunk(dao-trunk)     
        </connection>  
        <!--给开发者使用的，类似connection元素。即该连接不仅仅只读 -->  
        <developerConnection>  
            scm:svn:http://svn.xxxx.com/maven/dao-trunk     
        </developerConnection>  
        <!--当前代码的标签，在开发阶段默认为HEAD -->  
        <tag />  
        <!--指向项目的可浏览SCM库（例如ViewVC或者Fisheye）的URL。 -->  
        <url>http://svn.xxxxx.com/</url>  
    </scm>  
    <!--描述项目所属组织的各种属性。Maven产生的文档用 -->  
    <organization>  
        <!--组织的全名 -->  
        <name>demo</name>  
        <!--组织主页的URL -->  
        <url>http://www.xxxxxx.com/</url>  
    </organization>  
    <!--构建项目需要的信息 -->  
    <build>  
        <!--该元素设置了项目源码目录，当构建项目的时候，构建系统会编译目录里的源码。该路径是相对于pom.xml的相对路径。 -->  
        <sourceDirectory />  
        <!--该元素设置了项目脚本源码目录，该目录和源码目录不同：绝大多数情况下，该目录下的内容 会被拷贝到输出目录(因为脚本是被解释的，而不是被编译的)。 -->  
        <scriptSourceDirectory />  
        <!--该元素设置了项目单元测试使用的源码目录，当测试项目的时候，构建系统会编译目录里的源码。该路径是相对于pom.xml的相对路径。 -->  
        <testSourceDirectory />  
        <!--被编译过的应用程序class文件存放的目录。 -->  
        <outputDirectory />  
        <!--被编译过的测试class文件存放的目录。 -->  
        <testOutputDirectory />  
        <!--使用来自该项目的一系列构建扩展 -->  
        <extensions>  
            <!--描述使用到的构建扩展。 -->  
            <extension>  
                <!--构建扩展的groupId -->  
                <groupId /> 
                <!--构建扩展的artifactId -->  
                <artifactId /> 
                <!--构建扩展的版本 -->  
                <version /> 
            </extension>  
        </extensions>  
        <!--当项目没有规定目标（Maven2 叫做阶段）时的默认值 -->  
        <defaultGoal />  
        <!--这个元素描述了项目相关的所有资源路径列表，例如和项目相关的属性文件，这些资源被包含在最终的打包文件里。 -->  
        <resources>  
            <!--这个元素描述了项目相关或测试相关的所有资源路径 -->  
            <resource>  
                <!--描述了资源的目标路径。该路径相对target/classes目录（例如${project.build.outputDirectory}）。举个例子，如果你想资源在特定的包里(org.apache.maven.messages)，你就必须该元素设置为org/apache/maven/messages。然而，如果你只是想把资源放到源码目录结构里，就不需要该配置。 -->  
                <targetPath />  
                <!--是否使用参数值代替参数名。参数值取自properties元素或者文件里配置的属性，文件在filters元素里列出。 -->  
                <filtering /> 
                <!--描述存放资源的目录，该路径相对POM路径 -->  
                <directory />  
                <!--包含的模式列表，例如**/*.xml. -->  
                <includes />  
                <!--排除的模式列表，例如**/*.xml -->  
                <excludes />  
            </resource>  
        </resources>  
        <!--这个元素描述了单元测试相关的所有资源路径，例如和单元测试相关的属性文件。 -->  
        <testResources>  
            <!--这个元素描述了测试相关的所有资源路径，参见build/resources/resource元素的说明 -->  
            <testResource>  
                <targetPath />  
                <filtering /> 
                <directory />  
                <includes />  
                <excludes />  
            </testResource>  
        </testResources>  
        <!--构建产生的所有文件存放的目录 -->  
        <directory />  
        <!--产生的构件的文件名，默认值是${artifactId}-${version}。 -->  
        <finalName />  
        <!--当filtering开关打开时，使用到的过滤器属性文件列表 -->  
        <filters />  
        <!--子项目可以引用的默认插件信息。该插件配置项直到被引用时才会被解析或绑定到生命周期。给定插件的任何本地配置都会覆盖这里的配置 -->  
        <pluginManagement>  
            <!--使用的插件列表 。 -->  
            <plugins>  
                <!--plugin元素包含描述插件所需要的信息。 -->  
                <plugin>  
                    <!--插件在仓库里的group ID -->  
                    <groupId /> 
                    <!--插件在仓库里的artifact ID -->  
                    <artifactId /> 
                    <!--被使用的插件的版本（或版本范围） -->  
                    <version /> 
                    <!--是否从该插件下载Maven扩展（例如打包和类型处理器），由于性能原因，只有在真需要下载时，该元素才被设置成enabled。 -->  
                    <extensions /> 
                    <!--在构建生命周期中执行一组目标的配置。每个目标可能有不同的配置。 -->  
                    <executions>  
                        <!--execution元素包含了插件执行需要的信息 -->  
                        <execution>  
                            <!--执行目标的标识符，用于标识构建过程中的目标，或者匹配继承过程中需要合并的执行目标 -->  
                            <id />  
                            <!--绑定了目标的构建生命周期阶段，如果省略，目标会被绑定到源数据里配置的默认阶段 -->  
                            <phase />
                            <!--配置的执行目标 -->  
                            <goals />  
                            <!--配置是否被传播到子POM -->  
                            <inherited />  
                            <!--作为DOM对象的配置 -->  
                            <configuration />  
                        </execution>  
                    </executions>  
                    <!--项目引入插件所需要的额外依赖 -->  
                    <dependencies>  
                        <!--参见dependencies/dependency元素 -->  
                        <dependency>......</dependency>  
                    </dependencies>  
                    <!--任何配置是否被传播到子项目 -->  
                    <inherited />
                    <!--作为DOM对象的配置 -->  
                    <configuration />  
                </plugin>  
            </plugins>  
        </pluginManagement>  
        <!--使用的插件列表 -->  
        <plugins>  
            <!--参见build/pluginManagement/plugins/plugin元素 -->  
            <plugin>  
                <groupId /> 
                <artifactId /> 
                <version /> 
                <extensions /> 
                <executions>  
                    <execution>  
                        <id />  
                        <phase /> 
                        <goals />  
                        <inherited />  
                        <configuration />  
                    </execution>  
                </executions>  
                <dependencies>  
                    <!--参见dependencies/dependency元素 -->  
                    <dependency></dependency>  
                </dependencies>  
                <goals />  
                <inherited />  
                <configuration />  
            </plugin>  
        </plugins>  
    </build>  
    <!--在列的项目构建profile，如果被激活，会修改构建处理 -->  
    <profiles>  
        <!--根据环境参数或命令行参数激活某个构建处理 -->  
        <profile>  
            <!--构建配置的唯一标识符。即用于命令行激活，也用于在继承时合并具有相同标识符的profile。 -->  
            <id />  
            <!--自动触发profile的条件逻辑。Activation是profile的开启钥匙。profile的力量来自于它 能够在某些特定的环境中自动使用某些特定的值；这些环境通过activation元素指定。activation元素并不是激活profile的唯一方式。 -->  
            <activation>  
                <!--profile默认是否激活的标志 -->  
                <activeByDefault /> 
                <!--当匹配的jdk被检测到，profile被激活。例如，1.4激活JDK1.4，1.4.0_2，而!1.4激活所有版本不是以1.4开头的JDK。 -->  
                <jdk />  
                <!--当匹配的操作系统属性被检测到，profile被激活。os元素可以定义一些操作系统相关的属性。 -->  
                <os>  
                    <!--激活profile的操作系统的名字 -->  
                    <name>Windows XP</name>  
                    <!--激活profile的操作系统所属家族(如 'windows') -->  
                    <family>Windows</family>  
                    <!--激活profile的操作系统体系结构 -->  
                    <arch>x64</arch>  
                    <!--激活profile的操作系统版本 -->  
                    <version>6.1.7100</version>  
                </os>  
                <!--如果Maven检测到某一个属性（其值可以在POM中通过${名称}引用），其拥有对应的名称和值，Profile就会被激活。如果值 字段是空的，那么存在属性名称字段就会激活profile，否则按区分大小写方式匹配属性值字段 -->  
                <property>  
                    <!--激活profile的属性的名称 -->  
                    <name>mavenVersion</name>  
                    <!--激活profile的属性的值 -->  
                    <value>2.0.3</value>  
                </property>  
                <!--提供一个文件名，通过检测该文件的存在或不存在来激活profile。missing检查文件是否存在，如果不存在则激活 profile。另一方面，exists则会检查文件是否存在，如果存在则激活profile。 -->  
                <file>  
                    <!--如果指定的文件存在，则激活profile。 -->  
                    <exists>/usr/local/xxxx/xxxx-home/tomcat/maven-guide-zh-to-production/workspace/  
                    </exists>  
                    <!--如果指定的文件不存在，则激活profile。 -->  
                    <missing>/usr/local/xxxx/xxxx-home/tomcat/maven-guide-zh-to-production/workspace/  
                    </missing>  
                </file>  
            </activation>  
            <!--构建项目所需要的信息。参见build元素 -->  
            <build>  
                <defaultGoal />  
                <resources>  
                    <resource>  
                        <targetPath />  
                        <filtering />  
                        <directory />  
                        <includes />  
                        <excludes />  
                    </resource>  
                </resources>  
                <testResources>  
                    <testResource>  
                        <targetPath />  
                        <filtering />  
                        <directory />  
                        <includes />  
                        <excludes />  
                    </testResource>  
                </testResources>  
                <directory />  
                <finalName />  
                <filters />  
                <pluginManagement>  
                    <plugins>  
                        <!--参见build/pluginManagement/plugins/plugin元素 -->  
                        <plugin>  
                            <groupId />  
                            <artifactId />  
                            <version />  
                            <extensions />  
                            <executions>  
                                <execution>  
                                    <id />  
                                    <phase />  
                                    <goals />  
                                    <inherited />  
                                    <configuration />  
                                </execution>  
                            </executions>  
                            <dependencies>  
                                <!--参见dependencies/dependency元素 -->  
                                <dependency>......</dependency>  
                            </dependencies>  
                            <goals />  
                            <inherited />  
                            <configuration />  
                        </plugin>  
                    </plugins>  
                </pluginManagement>  
                <plugins>  
                    <!--参见build/pluginManagement/plugins/plugin元素 -->  
                    <plugin>  
                        <groupId />  
                        <artifactId />  
                        <version />  
                        <extensions />  
                        <executions>  
                            <execution>  
                                <id />  
                                <phase />  
                                <goals />  
                                <inherited />  
                                <configuration />  
                            </execution>  
                        </executions>  
                        <dependencies>  
                            <!--参见dependencies/dependency元素 -->  
                            <dependency>......</dependency>  
                        </dependencies>  
                        <goals />  
                        <inherited />  
                        <configuration />  
                    </plugin>  
                </plugins>  
            </build>  
            <!--模块（有时称作子项目） 被构建成项目的一部分。列出的每个模块元素是指向该模块的目录的相对路径 -->  
            <modules />  
            <!--发现依赖和扩展的远程仓库列表。 -->  
            <repositories>  
                <!--参见repositories/repository元素 -->  
                <repository>  
                    <releases>  
                        <enabled />  
                        <updatePolicy />  
                        <checksumPolicy />  
                    </releases>  
                    <snapshots>  
                        <enabled />  
                        <updatePolicy />  
                        <checksumPolicy />  
                    </snapshots>  
                    <id />  
                    <name />  
                    <url />  
                    <layout />  
                </repository>  
            </repositories>  
            <!--发现插件的远程仓库列表，这些插件用于构建和报表 -->  
            <pluginRepositories>  
                <!--包含需要连接到远程插件仓库的信息.参见repositories/repository元素 -->  
                <pluginRepository>  
                    <releases>  
                        <enabled />  
                        <updatePolicy />  
                        <checksumPolicy />  
                    </releases>  
                    <snapshots>  
                        <enabled />  
                        <updatePolicy />  
                        <checksumPolicy />  
                    </snapshots>  
                    <id />  
                    <name />  
                    <url />  
                    <layout />  
                </pluginRepository>  
            </pluginRepositories>  
            <!--该元素描述了项目相关的所有依赖。 这些依赖组成了项目构建过程中的一个个环节。它们自动从项目定义的仓库中下载。要获取更多信息，请看项目依赖机制。 -->  
            <dependencies>  
                <!--参见dependencies/dependency元素 -->  
                <dependency>......</dependency>  
            </dependencies>  
            <!--不赞成使用. 现在Maven忽略该元素. -->  
            <reports />  
            <!--该元素包括使用报表插件产生报表的规范。当用户执行“mvn site”，这些报表就会运行。 在页面导航栏能看到所有报表的链接。参见reporting元素 -->  
            <reporting>......</reporting>  
            <!--参见dependencyManagement元素 -->  
            <dependencyManagement>  
                <dependencies>  
                    <!--参见dependencies/dependency元素 -->  
                    <dependency>......</dependency>  
                </dependencies>  
            </dependencyManagement>  
            <!--参见distributionManagement元素 -->  
            <distributionManagement>......</distributionManagement>  
            <!--参见properties元素 -->  
            <properties />  
        </profile>  
    </profiles>  
    <!--模块（有时称作子项目） 被构建成项目的一部分。列出的每个模块元素是指向该模块的目录的相对路径 -->  
    <modules />  
    <!--发现依赖和扩展的远程仓库列表。 -->  
    <repositories>  
        <!--包含需要连接到远程仓库的信息 -->  
        <repository>  
            <!--如何处理远程仓库里发布版本的下载 -->  
            <releases>  
                <!--true或者false表示该仓库是否为下载某种类型构件（发布版，快照版）开启。 -->  
                <enabled />  
                <!--该元素指定更新发生的频率。Maven会比较本地POM和远程POM的时间戳。这里的选项是：always（一直），daily（默认，每日），interval：X（这里X是以分钟为单位的时间间隔），或者never（从不）。 -->  
                <updatePolicy />  
                <!--当Maven验证构件校验文件失败时该怎么做：ignore（忽略），fail（失败），或者warn（警告）。 -->  
                <checksumPolicy />  
            </releases>  
            <!--如何处理远程仓库里快照版本的下载。有了releases和snapshots这两组配置，POM就可以在每个单独的仓库中，为每种类型的构件采取不同的策略。例如，可能有人会决定只为开发目的开启对快照版本下载的支持。参见repositories/repository/releases元素 -->  
            <snapshots>  
                <enabled />  
                <updatePolicy />  
                <checksumPolicy />  
            </snapshots>  
            <!--远程仓库唯一标识符。可以用来匹配在settings.xml文件里配置的远程仓库 -->  
            <id>banseon-repository-proxy</id>  
            <!--远程仓库名称 -->  
            <name>banseon-repository-proxy</name>  
            <!--远程仓库URL，按protocol://hostname/path形式 -->  
            <url>http://10.10.10.123:8080/repository/</url>  
            <!--用于定位和排序构件的仓库布局类型-可以是default（默认）或者legacy（遗留）。Maven 2为其仓库提供了一个默认的布局；然而，Maven   
                1.x有一种不同的布局。我们可以使用该元素指定布局是default（默认）还是legacy（遗留）。 -->  
            <layout>default</layout>  
        </repository>  
    </repositories>  
    <!--发现插件的远程仓库列表，这些插件用于构建和报表 -->  
    <pluginRepositories>  
        <!--包含需要连接到远程插件仓库的信息.参见repositories/repository元素 -->  
        <pluginRepository>......</pluginRepository>  
    </pluginRepositories>  
  
    <!--该元素描述了项目相关的所有依赖。 这些依赖组成了项目构建过程中的一个个环节。它们自动从项目定义的仓库中下载。要获取更多信息，请看项目依赖机制。 -->  
    <dependencies>  
        <dependency>  
            <!--依赖的group ID -->  
            <groupId>org.apache.maven</groupId>  
            <!--依赖的artifact ID -->  
            <artifactId>maven-artifact</artifactId>  
            <!--依赖的版本号。 在Maven 2里, 也可以配置成版本号的范围。 -->  
            <version>3.8.1</version>  
            <!--依赖类型，默认类型是jar。它通常表示依赖的文件的扩展名，但也有例外。一个类型可以被映射成另外一个扩展名或分类器。类型经常和使用的打包方式对应，尽管这也有例外。一些类型的例子：jar，war，ejb-client和test-jar。如果设置extensions为   
                true，就可以在plugin里定义新的类型。所以前面的类型的例子不完整。 -->  
            <type>jar</type>  
            <!--依赖的分类器。分类器可以区分属于同一个POM，但不同构建方式的构件。分类器名被附加到文件名的版本号后面。例如，如果你想要构建两个单独的构件成JAR，一个使用Java   
                1.4编译器，另一个使用Java 6编译器，你就可以使用分类器来生成两个单独的JAR构件。 -->  
            <classifier></classifier>  
            <!--依赖范围。在项目发布过程中，帮助决定哪些构件被包括进来。欲知详情请参考依赖机制。 - compile ：默认范围，用于编译 - provided：类似于编译，但支持你期待jdk或者容器提供，类似于classpath   
                - runtime: 在执行时需要使用 - test: 用于test任务时使用 - system: 需要外在提供相应的元素。通过systemPath来取得   
                - systemPath: 仅用于范围为system。提供相应的路径 - optional: 当项目自身被依赖时，标注依赖是否传递。用于连续依赖时使用 -->  
            <scope>test</scope>  
            <!--仅供system范围使用。注意，不鼓励使用这个元素，并且在新的版本中该元素可能被覆盖掉。该元素为依赖规定了文件系统上的路径。需要绝对路径而不是相对路径。推荐使用属性匹配绝对路径，例如${java.home}。 -->  
            <systemPath></systemPath>  
            <!--当计算传递依赖时， 从依赖构件列表里，列出被排除的依赖构件集。即告诉maven你只依赖指定的项目，不依赖项目的依赖。此元素主要用于解决版本冲突问题 -->  
            <exclusions>  
                <exclusion>  
                    <artifactId>spring-core</artifactId>  
                    <groupId>org.springframework</groupId>  
                </exclusion>  
            </exclusions>  
            <!--可选依赖，如果你在项目B中把C依赖声明为可选，你就需要在依赖于B的项目（例如项目A）中显式的引用对C的依赖。可选依赖阻断依赖的传递性。 -->  
            <optional>true</optional>  
        </dependency>  
    </dependencies>  
    <!--不赞成使用. 现在Maven忽略该元素. -->  
    <reports></reports>  
    <!--该元素描述使用报表插件产生报表的规范。当用户执行“mvn site”，这些报表就会运行。 在页面导航栏能看到所有报表的链接。 -->  
    <reporting>  
        <!--true，则，网站不包括默认的报表。这包括“项目信息”菜单中的报表。 -->  
        <excludeDefaults />  
        <!--所有产生的报表存放到哪里。默认值是${project.build.directory}/site。 -->  
        <outputDirectory />  
        <!--使用的报表插件和他们的配置。 -->  
        <plugins>  
            <!--plugin元素包含描述报表插件需要的信息 -->  
            <plugin>  
                <!--报表插件在仓库里的group ID -->  
                <groupId />  
                <!--报表插件在仓库里的artifact ID -->  
                <artifactId />  
                <!--被使用的报表插件的版本（或版本范围） -->  
                <version />  
                <!--任何配置是否被传播到子项目 -->  
                <inherited />  
                <!--报表插件的配置 -->  
                <configuration />  
                <!--一组报表的多重规范，每个规范可能有不同的配置。一个规范（报表集）对应一个执行目标 。例如，有1，2，3，4，5，6，7，8，9个报表。1，2，5构成A报表集，对应一个执行目标。2，5，8构成B报表集，对应另一个执行目标 -->  
                <reportSets>  
                    <!--表示报表的一个集合，以及产生该集合的配置 -->  
                    <reportSet>  
                        <!--报表集合的唯一标识符，POM继承时用到 -->  
                        <id />  
                        <!--产生报表集合时，被使用的报表的配置 -->  
                        <configuration />  
                        <!--配置是否被继承到子POMs -->  
                        <inherited />  
                        <!--这个集合里使用到哪些报表 -->  
                        <reports />  
                    </reportSet>  
                </reportSets>  
            </plugin>  
        </plugins>  
    </reporting>  
    <!--继承自该项目的所有子项目的默认依赖信息。这部分的依赖信息不会被立即解析,而是当子项目声明一个依赖（必须描述group ID和artifact   
        ID信息），如果group ID和artifact ID以外的一些信息没有描述，则通过group ID和artifact ID匹配到这里的依赖，并使用这里的依赖信息。 -->  
    <dependencyManagement>  
        <dependencies>  
            <!--参见dependencies/dependency元素 -->  
            <dependency>......</dependency>  
        </dependencies>  
    </dependencyManagement>  
    <!--项目分发信息，在执行mvn deploy后表示要发布的位置。有了这些信息就可以把网站部署到远程服务器或者把构件部署到远程仓库。 -->  
    <distributionManagement>  
        <!--部署项目产生的构件到远程仓库需要的信息 -->  
        <repository>  
            <!--是分配给快照一个唯一的版本号（由时间戳和构建流水号）？还是每次都使用相同的版本号？参见repositories/repository元素 -->  
            <uniqueVersion />  
            <id>xxx-maven2</id>  
            <name>xxx maven2</name>  
            <url>file://${basedir}/target/deploy</url>  
            <layout />  
        </repository>  
        <!--构件的快照部署到哪里？如果没有配置该元素，默认部署到repository元素配置的仓库，参见distributionManagement/repository元素 -->  
        <snapshotRepository>  
            <uniqueVersion />  
            <id>xxx-maven2</id>  
            <name>xxx-maven2 Snapshot Repository</name>  
            <url>scp://svn.xxxx.com/xxx:/usr/local/maven-snapshot</url>  
            <layout />  
        </snapshotRepository>  
        <!--部署项目的网站需要的信息 -->  
        <site>  
            <!--部署位置的唯一标识符，用来匹配站点和settings.xml文件里的配置 -->  
            <id>banseon-site</id>  
            <!--部署位置的名称 -->  
            <name>business api website</name>  
            <!--部署位置的URL，按protocol://hostname/path形式 -->  
            <url>  
                scp://svn.baidu.com/xxx:/var/www/localhost/web     
            </url>  
        </site>  
        <!--项目下载页面的URL。如果没有该元素，用户应该参考主页。使用该元素的原因是：帮助定位那些不在仓库里的构件（由于license限制）。 -->  
        <downloadUrl />  
        <!--如果构件有了新的group ID和artifact ID（构件移到了新的位置），这里列出构件的重定位信息。 -->  
        <relocation>  
            <!--构件新的group ID -->  
            <groupId />  
            <!--构件新的artifact ID -->  
            <artifactId />  
            <!--构件新的版本号 -->  
            <version />  
            <!--显示给用户的，关于移动的额外信息，例如原因。 -->  
            <message />  
        </relocation>  
        <!--给出该构件在远程仓库的状态。不得在本地项目中设置该元素，因为这是工具自动更新的。有效的值有：none（默认），converted（仓库管理员从Maven   
            1 POM转换过来），partner（直接从伙伴Maven 2仓库同步过来），deployed（从Maven 2实例部署），verified（被核实时正确的和最终的）。 -->  
        <status />  
    </distributionManagement>  
    <!--以值替代名称，Properties可以在整个POM中使用，也可以作为触发条件（见settings.xml配置文件里activation元素的说明）。格式是<name>value</name>。 -->  
    <properties />  
</project>
```

## 相关参考文档 

[Maven 配置setting.xml](https://zhuanlan.zhihu.com/p/623373861)

[Maven 配置详解](https://zhuanlan.zhihu.com/p/693332013/)
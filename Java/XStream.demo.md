---
layout: default
title: Java  XStream
parent: Java
nav_order: 93
---

Here are java 8 stream experience .
{: .fs-6 .fw-300 }


## Here are stream demo .
{: .no_toc .text-delta }

1. TOC
{:toc}


## XStream 

## 组件介绍

Xstream是一种OXMapping 技术，是用来处理XML文件序列化的框架,在将JavaBean序列化，或将XML文件反序列化的时候，不需要其它辅助类和映射文件，使得XML序列化不再繁索。

Xstream也可以将JavaBean序列化成Json或反序列化，使用非常方便。

**主要功能：**

1、对象序列化和反序列化：XStream 提供了简单的方式将 Java 对象序列化为 XML 或 JSON 格式，并将 XML 或 JSON 转换回 Java 对象。

2、灵活的配置选项：XStream 允许你通过配置选项来定制对象的序列化和反序列化过程，包括设置别名、自定义转换器、忽略字段等。

3、支持复杂对象和集合：XStream 能够处理复杂对象和集合类型，如嵌套对象、数组、列表等，使得序列化和反序列化过程更加灵活和方便。

4、自动类型转换：XStream 能够自动处理基本数据类型和常见 Java 类型之间的转换，无需额外配置。

5、良好的性能：XStream 在处理对象序列化和反序列化时具有良好的性能表现，能够有效地处理大型对象图。

6、支持扩展：XStream 具有丰富的扩展功能，可以通过编写自定义转换器、注解处理器等来扩展其功能，满足特定的序列化需求。

7、可读性强：生成的 XML 或 JSON 数据具有良好的可读性，便于调试和理解。

8、跨平台兼容：XStream 可以在不同平台上运行，并且支持多种 Java 版本，具有良好的跨平台兼容性。

总的来说，XStream 是一个功能丰富、易于使用且灵活的对象序列化库，适用于各种 Java 后端应用程序中需要进行对象和 XML/JSON 数据转换的场景。


### 使用


1、添加依赖

```xml
<dependency>
    <groupId>com.thoughtworks.xstream</groupId>
    <artifactId>xstream</artifactId>
    <version>1.4.20</version> <!-- 使用最新版本 -->
</dependency>
```

{: .tips}
> 低版本时：Xstream序列化XML时需要引用的jar包: xstream-[version].jar、xpp3-[version].jar、xmlpull-[version].jar。
> 
> Xstream序列化Json需要引用的jar包: jettison-[version].jar。


Xstream提供了对Json的支持，是因为Xstream内置了两个Driver：

- 1.JsonHierarchicalStreamDriver：不依赖其他类库，只实现 obj->JSON ,**只能序列号JSON，不能反序列化（解析）**

- 2.JettisonMappedXmlDriver：依赖jettison类库，实现 JSON->obj or obj->JSON

两种Driver在处理相同设置的Object时会得到不同的JSON串，JsonHierarchicalStreamDriver得到的串更简洁，确如官网所说。

JsonHierarchicalStreamDriver 有个小问题——默认输出带格式的JSON串，结构中带空格、换行，并且没有提供修饰方式。

```xml
<dependency>
    <groupId>org.codehaus.jettison</groupId>
    <artifactId>jettison</artifactId>
    <version>1.5.4</version>
</dependency>
```


## XStream 使用

### 初始化

使用以下的语句进行初始化操作：

```
XStreamxstream = new XStream();
等同于
XStream xs = new XStream(new Xpp3Driver(new NoNameCoder()));
```
默认情况下，XStream会 采用Xpp3库，XPP3是一种运行效率非常高的XML全解析实现。如果你不想依靠Xpp3库的话，也可以使用一个标准的JAXP DOM解析器，可以采用以下语句进行初始化：

```
//不使用XPP3库
XStreamxstream = new XStream(new DomDriver());
```
此xstream实例，为线程安全的，可以供多个线程进行调用，共享使用。参考 com.thoughtworks.xstream.io.xml包，会发现系统提供了多种标识解析器供我们选择，包括，DomDriver、 JDomDriver、StaxDriver等等


### 常用方法


 - xStream.toXML(object)：将对象转换成XML、Json。
 - xStream.toXML(obj, outputStream):将对象转换XML、Json并封装成输出流。
 - xStream.toXML(object, writer): 将对象转换XML、Json并封成写入流。
 - xStream.fromXML()：将XML、Json转换成对象，此方法接受File、InputStream、Reader、String、URL类型的参数。
 - xStream.alias("news", News.class)：为指定类名创建别名。
 - xStream.useAttributeFor(News.class, "id")：将id设为 News的元素的属性。
 - xStream.aliasField("other", BookShelf.class,"remark"):修改节点名称,将BookShelf类中的remark节点名修改为other。
 - xStream.addImplicitCollection(BookShelf.class, "books")：去掉集体节点的父节点。
 - xStream.aliasAttribute("姓名", "name")：修改属性的name，为姓名, 给属性取别名。


### 入门案例 Demo

```java
package com.xiaocai.demo.java.xstream.bean;

import lombok.Data;

/**
 * @Project : small-demo-java
 * @Author : zhangxiaocai
 * @Description : [ Person ] 说明：无
 * @Function :  功能说明：无
 * @Date ：2024/8/16 15:04
 * @Version ： 1.0
 **/
@Data
public class Person {

    private String name;
    private int age;
    private String lockStatus;
    public Person(String name, int age, String lockStatus)
    {
        this.name=name;
        this.age=age;
        this.lockStatus = lockStatus ;
    }
    @Override
    public String toString() {
        return "Person{" +
                "name='" + name + '\'' +
                ", age=" + age +
                ", lockStatus='" + lockStatus + '\'' +
                '}';
    }
}
```

```java
public class Test{
    @Test
    public void demoSimple() {
        //XStream xstream = new XStream();//需要XPP3库
        //XStream xs = new XStream(new Xpp3Driver(new NoNameCoder()));
        //XStream xstream = new XStream(new DomDriver());//不需要XPP3库
        //XStream xstream = new XStream(new StaxDriver());//不需要XPP3库开始使用Java6
        Person bean = new Person("张三", 22, "1");
        XStream xstream = new XStream();
        //给 Perion 节点取别名
        xstream.alias("people", Person.class);
        // 将age 设置为 Persion 的属性
        xstream.useAttributeFor(Person.class, "age");
        //XML序列化
        String xml = xstream.toXML(bean);
        System.out.println(xml);
        //XML反序列化
        xstream.alias("people", Person.class);
        xstream.allowTypes(new Class[]{Person.class});
        bean = (Person) xstream.fromXML(xml);
        System.out.println(bean);
        xstream = new XStream(new JettisonMappedXmlDriver());
        xstream.setMode(XStream.NO_REFERENCES);
        //Json序列化
        String json = xstream.toXML(bean);
        System.out.println(json);
        //Json反序列
        xstream.allowTypes(new Class[]{Person.class});
        xstream.setMode(XStream.NO_REFERENCES);
        bean = (Person) xstream.fromXML(json);
        System.out.println(bean);
    }
}
```

输出结果

```
<people age="22">
  <name>张三</name>
  <lockStatus>1</lockStatus>
</people>
Person{name='张三', age=22, lockStatus='1'}
{"com.xiaocai.demo.java.xstream.bean.Person":{"name":"张三","age":22,"lockStatus":1}}
Person{name='张三', age=22, lockStatus='1'}
```

{: .tips}
> com.thoughtworks.xstream.security.ForbiddenClassException
> 
> Starting with version 1.14.12 nine years ago, XStream contains a Security Framework to implement a black- or whitelist for the allowed types at deserialization time. Until version 1.4.17, XStream kept a default blacklist in order to deny all types of the Java runtime, which are used for all kinds of security attacks, in order to guarantee optimal runtime compatibility for existing users. However, this approach has failed. The last months have shown, that the Java runtime alone contains dozens of types that can be used for an attack, not even looking at the 3rd party libraries on a classpath. The new version of XStream uses therefore now by default a whitelist, which is recommended since nine years. It also has been complaining on the console for a long time about an uninitialized security framework the first time it was run. Anyone who has followed the advice and initialized the security framework for their own scenario can easily update to the new version without any problem. Everyone else will have to do a proper initialization now, otherwise the new version will fail with certainty at deserialization time.
>
> 人话翻译: xstream 为防止反序列化攻击, 从 1.14.12 版本开始做了个黑名单和白名单的功能，到1.4.17版为止，都只默认使用了黑名单，白名单只是推荐。但是从1.4.18开始，XStream 做 xml/json 反序列化前，都会强制使用者提供白名单类型。
>
> 就是得调用 xstream.allowTypeXxx() 的方法，给自己反序列的类增加白名单。


### 方法案例

XML转换别名和属性

```java
public class Test{
    @Test
    public void demoXmlTest01() {
        Person bean = new Person("张三", 22,"1");
        XStream xstream = new XStream();
        //给 Perion 节点取别名
        xstream.alias("people", Person.class);
        // 将age 设置为 Persion 的属性
        xstream.useAttributeFor(Person.class, "age");
        // 将 name 节点名换成 user_name
        xstream.aliasField("user_name", Person.class, "name");
        xstream.aliasAttribute("status", "lockStatus");
        //XML序列化
        String xml = xstream.toXML(bean);
        System.out.println(xml);
        //XML反序列化
        xstream.alias("people", Person.class);
        xstream.allowTypes(new Class[]{Person.class});
        bean = (Person) xstream.fromXML(xml);
        System.out.println(bean);
    }
}
```

输出结果

```
<people age="22">
  <userName>张三</userName>
  <lockStatus>1</lockStatus>
</people>
Person{name='张三', age=22, lockStatus='1'}
```

如果我们把 将 name 节点名换成 user_name , 这种带下划线的
```
xstream.aliasField("user_name", Person.class, "name");
```
输出结果 

```
<people age="22">
  <user__name>张三</user__name>
  <lockStatus>1</lockStatus>
</people>
Person{name='张三', age=22, lockStatus='1'}
```

可以看到下划线变成了2个, 调整一下创建解析器参数 NoNameCoder, 至于使用何种解析器, 都可以。

```java
public class Test{
    @Test
    public void demoToXMLTest01() {
        Person bean = new Person("张三", 22,"1");
        Xpp3Driver xpp3Driver =  new Xpp3Driver(new NoNameCoder());
        DomDriver domDriver = new DomDriver("utf-8",new NoNameCoder());
        JDom2Driver jDomDriver = new JDom2Driver(new NoNameCoder());
        StaxDriver staxDriver = new StaxDriver(new NoNameCoder());
        XStream xstream = new XStream(xpp3Driver);
        //给 Perion 节点取别名
        xstream.alias("people", Person.class);
        // 将age 设置为 Persion 的属性
        xstream.useAttributeFor(Person.class, "age");
        // 将 name 节点名换成 user_name
        xstream.aliasField("user_name", Person.class, "name");
        xstream.aliasAttribute("status", "lockStatus");
        //XML序列化
        String xml = xstream.toXML(bean);
        System.out.println(xml);
    }
}
```

使用不同的XML解析器的输出结果：

```
使用默认  xpp3Driver xpp3 驱动序列号
<people age="22">
  <user_name>张三</user_name>
  <lockStatus>1</lockStatus>
</people>


使用 DomDriver Dom 解析器序列化
<people age="22">
  <user_name>张三</user_name>
  <lockStatus>1</lockStatus>
</people>


使用 JDomDriver JDom 解析器序列化
<people age="22">
  <user_name>张三</user_name>
  <lockStatus>1</lockStatus>
</people>


使用 StaxDriver Stax 解析器序列化
<?xml version="1.0" ?><people age="22"><user_name>张三</user_name><lockStatus>1</lockStatus></people>
```


{.tips}
> 如果想使用其他组件，可以参考 HierarchicalStreamDriver 接口的实现，引入相关依赖支持。也可以实现接口自己实现。

比如使用 dom2

```xml
<!-- 原始 -->
<dependency>
    <groupId>org.jdom</groupId>
    <artifactId>jdom2</artifactId>
    <version>2.0.6.1</version>
</dependency>
```

无漏洞版本:
```xml
<!-- This OSGi bundle wraps jdom2 2.0.6.1 jar file.  -->
<dependency>
    <groupId>org.apache.servicemix.bundles</groupId>
    <artifactId>org.apache.servicemix.bundles.jdom</artifactId>
    <version>2.0.6.1_1</version>
</dependency>
```



Json 转换

```java
public class Test{
    @Test
    public void demoXmlTest01() {
        Person bean = new Person("张三", 22, "1");
        //JsonHierarchicalStreamDriver 这个类只能做json序列号，不能反序列化
        XStream xstream = new XStream(new JsonHierarchicalStreamDriver());
        xstream.setMode(XStream.NO_REFERENCES);
        //Json序列化
        String json = xstream.toXML(bean);
        System.out.println(json);

        xstream = new XStream(new JettisonMappedXmlDriver());
        //Json序列化
        String json2 = xstream.toXML(bean);
        System.out.println(json2);

        //Json反序列
        xstream.allowTypes(new Class[]{Person.class});
        bean = (Person) xstream.fromXML(json);
        System.out.println(bean);
    }
}
```

输出结果

```
{"com.xiaocai.demo.java.xstream.bean.Person": {
  "name": "张三",
  "age": 22,
  "lockStatus": "1"
}}
{"com.xiaocai.demo.java.xstream.bean.Person":{"name":"张三","age":22,"lockStatus":1}}
Person{name='张三', age=22, lockStatus='1'}
```

可以看出不同的序列号样子稍微有的不同，`JsonHierarchicalStreamDriver` 会原样格式化，有空格换行。

## 最佳实践

通常接口不止一个，报文有很多套，有可能会涉及部分重复，但是body部分根据不同业务进行不同变化，可以设计通用的。

定义报文主体

```java
package com.xiaocai.demo.java.xstream.bean;

import com.thoughtworks.xstream.annotations.XStreamAlias;
import com.thoughtworks.xstream.annotations.XStreamAsAttribute;
import lombok.Data;

import java.io.Serializable;

/**
 * @Project : small-demo-java
 * @Author : zhangxiaocai
 * @Description : [ XmlObject ] 说明：无
 * @Function :  功能说明：无
 * @Date ：2024/8/16 13:07
 * @Version ： 1.0
 **/

@Data
@XStreamAlias("message")
public class XmlObject implements Serializable {

    private static final long serialVerionUID = -41080087734545487L ;

    @XStreamAsAttribute
    private MsgHeader head ;
    @XStreamAsAttribute
    private MsgBody body ;
}
```

定义报文头

```java
package com.xiaocai.demo.java.xstream.bean;

import com.thoughtworks.xstream.annotations.XStreamAlias;
import lombok.Data;

/**
 * @Project : small-demo-java
 * @Author : zhangxiaocai
 * @Description : [ MsgHeader ] 说明：无
 * @Function :  功能说明：无
 * @Date ：2024/8/16 13:10
 * @Version ： 1.0
 **/


@Data
@XStreamAlias("head")
public class MsgHeader {

    @XStreamAlias("trade_time")
    private String tradeTime ;

    @XStreamAlias("trade_type")
    private String tradeType ;

    @XStreamAlias("content_Length")
    private int contentLength ;

    @XStreamAlias("content_Type")
    private String contentType ;

    @XStreamAlias("request_Id")
    private String requestId ;
}
```

定义报文体

```java
package com.xiaocai.demo.java.xstream.bean;

import com.thoughtworks.xstream.annotations.XStreamAlias;
import lombok.AllArgsConstructor;
import lombok.Data;
import lombok.NoArgsConstructor;

/**
 * @Project : small-demo-java
 * @Author : zhangxiaocai
 * @Description : [ MsgBody ] 说明：无
 * @Function :  功能说明：无
 * @Date ：2024/8/16 13:11
 * @Version ： 1.0
 **/

@AllArgsConstructor
@NoArgsConstructor
@Data
@XStreamAlias("body")
public class MsgBody {

    private Object object ;
}
```

定义业务报文体

```java
package com.xiaocai.demo.java.xstream.bean;

import com.thoughtworks.xstream.annotations.XStreamAlias;
import lombok.Data;

/**
 * @Project : small-demo-java
 * @Author : zhangxiaocai
 * @Description : [ UserInfo ] 说明：无
 * @Function :  功能说明：无
 * @Date ：2024/8/16 13:51
 * @Version ： 1.0
 **/

@Data
@XStreamAlias("userInfo")
public class UserInfo {

    @XStreamAlias("name")
    private String name ;

    @XStreamAlias("birth")
    private String birth ;

    @XStreamAlias("login_Time")
    private String loginTime;
}
```

定义转换工具类:

```java
package com.xiaocai.demo.java.xstream.uitl;

import com.thoughtworks.xstream.XStream;
import com.thoughtworks.xstream.io.naming.NoNameCoder;
import com.thoughtworks.xstream.io.xml.DomDriver;
import com.xiaocai.demo.java.xstream.bean.MsgBody;
import com.xiaocai.demo.java.xstream.bean.MsgHeader;
import com.xiaocai.demo.java.xstream.bean.XmlObject;

import java.util.Arrays;
import java.util.HashMap;
import java.util.Iterator;
import java.util.Map;

/**
 * @Project : small-demo-java
 * @Author : zhangxiaocai
 * @Description : [ XstreamUtil ] 说明：无
 * @Function :  功能说明：无
 * @Date ：2024/8/15 13:40
 * @Version ： 1.0
 **/
public class XStreamUtil {

    private static Map<String, Class> xml2ObjectMap = new HashMap<>();

    static {
        xml2ObjectMap.put("message", XmlObject.class);
        xml2ObjectMap.put("head", MsgHeader.class);
        xml2ObjectMap.put("body", MsgBody.class);
    }

    private static XStream getXStream(){
        XStream xstream = new XStream(new DomDriver("utf-8", new NoNameCoder()));
        xstream.autodetectAnnotations(true);
        return xstream ;
    }

    public static String toXML(Object msgObj){
        XStream xStream = getXStream();
        return xStream.toXML(msgObj);
    }

    public static Object toObject(String xml){
        XStream xStream = getXStream();
        Map<String, Class> mapper = getXml2ObjectMap();
        Iterator<String> iterator = mapper.keySet().iterator();
        while (iterator.hasNext()){
            String s = iterator.next();
            xStream.alias(s, mapper.get(s));
            xStream.allowTypes((Class[]) Arrays.asList(mapper.get(s)).toArray());
        }
        //配成包路径模式可以写在外面
        //xStream.allowTypesByWildcard(new String[] { "com.xiaocai.demo.java.xstream.bean.**" });

        return  xStream.fromXML(xml);
    }

    public static  Map<String, Class> getXml2ObjectMap() {
        return xml2ObjectMap ;
    }

    public static   void putXml2ObjectMapper(String mapperKey, Class mapperClass) {
        xml2ObjectMap.put(mapperKey, mapperClass) ;
    }
}
```

测试转换

```java
package com.xiaocai.demo.java.xstream;

import com.xiaocai.demo.java.xstream.bean.MsgBody;
import com.xiaocai.demo.java.xstream.bean.MsgHeader;
import com.xiaocai.demo.java.xstream.bean.UserInfo;
import com.xiaocai.demo.java.xstream.bean.XmlObject;
import com.xiaocai.demo.java.xstream.uitl.XStreamUtil;
import org.junit.jupiter.api.Test;

import java.time.LocalDateTime;
import java.time.format.DateTimeFormatter;

/**
 * @Project : small-demo-java
 * @Author : zhangxiaocai
 * @Description : [ XstreamDemoTest ] 说明：无
 * @Function :  功能说明：无
 * @Date ：2024/8/16 13:32
 * @Version ： 1.0
 **/
public class XstreamDemoTest {

    @Test
    public void toXml(){
        XmlObject xmlObject = new XmlObject();
        MsgHeader header = new MsgHeader();
        header.setTradeType("01");
        header.setTradeTime(LocalDateTime.now().format(DateTimeFormatter.ofPattern("yyyy-MM-dd HH:mm:ss:SSS")));
        header.setContentType("A1");
        header.setContentLength(3203);
        xmlObject.setHead(header);
        UserInfo userInfo = new UserInfo();
        userInfo.setName("Jack");
        userInfo.setBirth("1997-09-09");
        userInfo.setLoginTime(LocalDateTime.now().format(DateTimeFormatter.ofPattern("yyyy-MM-dd HH:mm:ss:SSS")));

        MsgBody body = new MsgBody(userInfo);
        xmlObject.setBody(body);
        String reqXml = XStreamUtil.toXML(xmlObject);
        System.out.println(reqXml);
    }
}
```

XML转换结果
```xml
<message>
  <head>
    <trade_time>2024-08-16 14:54:01:412</trade_time>
    <trade_type>01</trade_type>
    <content_Length>3203</content_Length>
    <content_Type>A1</content_Type>
  </head>
  <body>
    <object class="userInfo">
      <name>Jack</name>
      <birth>1997-09-09</birth>
      <login_Time>2024-08-16 14:54:01:421</login_Time>
    </object>
  </body>
</message>
```

反向解析XML结果

```
XmlObject(head=MsgHeader(tradeTime=2024-08-16 15:02:30:145, tradeType=01, contentLength=3203, contentType=A1, requestId=null), body=MsgBody(object=UserInfo(name=Jack, birth=1997-09-09, loginTime=2024-08-16 15:02:30:154)))
```
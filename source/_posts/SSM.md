# SpingMVC+Spring+Mybatis

<style>
    pre{
        background-color:white;
    }
    </style>
[TOC]



### **一、SpringMVC简介**

[Spring官网](https://spring.io/)

1️⃣ **Spring MVC是围绕前端控制器模式设计的，其中一个中央Servlet DispatcherServlet提供了一个用于请求处理的共享算法，而实际工作是由可配置的委托组件执行的**

2️⃣ **重要组件 DispacherServlet HandlerMapping HandlerAdapter ViewResovler**

### 二、基本配置及使用	

1️⃣ **创建相应包和控制器**

2️⃣ **导入依赖(包含整合依赖)**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.xq</groupId>
    <artifactId>mvc-bootstrap</artifactId>
    <version>1.0-SNAPSHOT</version>

<dependencies>
    <!-- 给springmvc返回json数据做准备的 -->
    <dependency>
        <groupId>com.alibaba</groupId>
        <artifactId>fastjson</artifactId>
        <version>1.2.47</version>
    </dependency>
    <!-- 想要用el表达式得有这个jar包 -->
    <dependency>
        <groupId>taglibs</groupId>
        <artifactId>standard</artifactId>
        <version>1.1.2</version>
    </dependency>
    <!-- spring核心依赖 -->
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-core</artifactId>
        <version>5.2.5.RELEASE</version>
    </dependency>
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-context</artifactId>
        <version>5.2.5.RELEASE</version>
    </dependency>
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-beans</artifactId>
        <version>5.2.5.RELEASE</version>
    </dependency>
    <!-- log4j日志记录 -->
    <dependency>
        <groupId>log4j</groupId>
        <artifactId>log4j</artifactId>
        <version>1.2.17</version>
    </dependency>
    <!-- springmvc依赖 -->
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-web</artifactId>
        <version>5.2.5.RELEASE</version>
    </dependency>
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-webmvc</artifactId>
        <version>5.2.5.RELEASE</version>
    </dependency>
    <!-- servlet依赖 -->
    <dependency>
        <groupId>javax.servlet</groupId>
        <artifactId>javax.servlet-api</artifactId>
        <version>4.0.1</version>
    </dependency>
    <!-- mysql驱动 -->
    <dependency>
        <groupId>mysql</groupId>
        <artifactId>mysql-connector-java</artifactId>
        <version>5.1.30</version>
    </dependency>
    <!-- aspectJ AOP 织入器 -->
    <dependency>
        <groupId>org.aspectj</groupId>
        <artifactId>aspectjweaver</artifactId>
        <version>1.8.9</version>
    </dependency>
    <!--mybatis-spring适配器 -->
    <dependency>
        <groupId>org.mybatis</groupId>
        <artifactId>mybatis-spring</artifactId>
        <version>1.3.0</version>
    </dependency>
    <!-- mybatis ORM框架 -->
    <dependency>
        <groupId>org.mybatis</groupId>
        <artifactId>mybatis</artifactId>
        <version>3.4.6</version>
    </dependency>
    <!-- JUnit单元测试工具 -->
    <dependency>
        <groupId>junit</groupId>
        <artifactId>junit</artifactId>
        <version>4.12</version>
    </dependency>
    <!-- spring连接数据源需要使用得jar包 -->
    <dependency>
        <groupId>commons-dbcp</groupId>
        <artifactId>commons-dbcp</artifactId>
        <version>1.4</version>
    </dependency>
    <!-- jstl -->
    <dependency>
        <groupId>javax.servlet</groupId>
        <artifactId>jstl</artifactId>
        <version>1.2</version>
    </dependency>
    <!-- jdbc依赖 -->
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-jdbc</artifactId>
        <version>5.2.5.RELEASE</version>
    </dependency>
    <dependency>
        <groupId>com.github.pagehelper</groupId>
        <artifactId>pagehelper</artifactId>
        <version>5.1.11</version>
    </dependency>
</dependencies>
    <build>
        <resources>
            <resource>
                <directory>src/main/java</directory>
                <includes>
                    <include>**/*.xml</include>
                </includes>
                <filtering>true</filtering>
            </resource>
        </resources>
    </build>
</project>
```

3️⃣ **配置resource下的xml文件**

> **db.properties**

```properties
driver=com.mysql.jdbc.Driver
url=jdbc:mysql://localhost:3306/ssm?characterEncoding=UTF-8
user=root
password=999999
minIdle=45
maxIdle=50
initialSize=5
maxActive=100
maxWait=100
removeAbandonedTimeout=180
removeAbandoned=true
```

> **log4j.properties**

```properties
log4j.rootLogger=INFO,stdout,logfile

log4j.logger.com.xq.mapper=DEBUG

log4j.appender.stdout=org.apache.log4j.ConsoleAppender
log4j.appender.stdout.layout=org.apache.log4j.PatternLayout
log4j.appender.stdout.layout.ConversionPattern=%d %p [%c] - %m%n

log4j.appender.logfile=org.apache.log4j.RollingFileAppender
log4j.appender.logfile.File=D:/Idealog/ibatis.log
log4j.appender.logfile.MaxFileSize=512KB
# Keep three backup files.
log4j.appender.logfile.MaxBackupIndex=3
# Pattern to output: date priority [category] - message
log4j.appender.logfile.layout=org.apache.log4j.PatternLayout
log4j.appender.logfile.layout.ConversionPattern=%d %p [%c] -%m%n?
```

> **mybatis.xml**

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    <settings>
        <setting name="logImpl" value="LOG4J"/>
    </settings>
</configuration>
```

> **applicationContext.xml**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context" xmlns:tx="http://www.springframework.org/schema/tx"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/context https://www.springframework.org/schema/context/spring-context.xsd http://www.springframework.org/schema/tx http://www.springframework.org/schema/tx/spring-tx.xsd http://www.springframework.org/schema/aop https://www.springframework.org/schema/aop/spring-aop.xsd">
    <!-- 注解扫描 -->
    <context:component-scan base-package="com.xq.mapper,com.xq.service" />
    <!-- 加载属性文件 -->
    <context:property-placeholder location="classpath:db.properties" />
    <!-- 创建数据源 -->
    <bean id="dataSource" class="org.apache.commons.dbcp.BasicDataSource">
        <property name="driverClassName" value="${driver}" />
        <property name="url" value="${url}" />
        <property name="username" value="${user}" />
        <property name="password" value="${password}" />
    </bean>
    <!-- 配置会话工厂 -->
    <bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
        <!-- 连接数据源 -->
        <property name="dataSource" ref="dataSource" />
        <!-- 别名设置 -->
        <property name="typeAliasesPackage" value="com.xq.pojo" />
        <!-- 加载mybatis-config配置文件 -->
        <property name="configLocation" value="classpath:mybatis-config.xml" />
        <!--<property name="mapperLocations" value=""-->

        <!--ssm分页插件得使用-->
        <property name="plugins">
            <array>
                <bean class="com.github.pagehelper.PageInterceptor">
                    <property name="properties">
                        <value>
                            helperDialect=mysql
                        </value>
                    </property>
                </bean>
            </array>
        </property>
    </bean>
    <!-- 扫描映射文件 -->
    <bean class="org.mybatis.spring.mapper.MapperScannerConfigurer">
        <property name="sqlSessionFactoryBeanName" value="sqlSessionFactory" />
        <property name="basePackage" value="com.xq.mapper" />
    </bean>
    <!-- 声明事务管理 -->
    <bean id="dataSourceTransactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
        <property name="dataSource" ref="dataSource" />
    </bean>
    <!-- 支持注解驱动的事务管理 -->
    <tx:annotation-driven transaction-manager="dataSourceTransactionManager"></tx:annotation-driven>
    <!-- aspectj支持自动代理生成aop -->
    <aop:aspectj-autoproxy proxy-target-class="true" ></aop:aspectj-autoproxy>

</beans>
```

> **spring-mvc.xml**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:mvc="http://www.springframework.org/schema/mvc"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context
        https://www.springframework.org/schema/context/spring-context.xsd
        http://www.springframework.org/schema/mvc
        https://www.springframework.org/schema/mvc/spring-mvc.xsd">
    <!-- 注解扫描 -->
    <context:component-scan base-package="com.xq.controller" />
    <!-- 注解驱动 -->
    <mvc:annotation-driven>
        <mvc:message-converters>
            <bean class="org.springframework.http.converter.StringHttpMessageConverter">
                <property name="supportedMediaTypes">
                    <list>
                        <value>application/json;charset=UTF-8</value>
                    </list>
                </property>
            </bean>
            <bean class="com.alibaba.fastjson.support.spring.FastJsonHttpMessageConverter">
                <property name="supportedMediaTypes">
                    <list>
                        <value>text/html;charset=UTF-8</value>
                        <value>application/json</value>
                    </list>
                </property>
                <property name="features">
                    <list>
                        <value>WriteDateUseDateFormat</value>
                    </list>
                </property>
            </bean>
        </mvc:message-converters>
    </mvc:annotation-driven>
    <!-- 类型处理器 -->
    <mvc:default-servlet-handler/>
    <!-- 配置视图解析器 -->
	<bean id="viewResolver" 				class="org.springframework.web.servlet.view.InternalResourceViewResolver">
<!-- 在路径加前缀 -->
	<property name="prefix" value="/" />
	<!-- 在路径加后缀 -->
	<property name="suffix" value=".jsp" />
	</bean>
</beans>
```

4️⃣ **配置web.xml**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
         version="4.0">
    <!-- 配置上下文参数 -->
    <context-param>
        <param-name>contextConfigLocation</param-name>
        <param-value>classpath:applicationContext.xml</param-value>
    </context-param>
    <!-- 配置监听器 -->
    <listener>
        <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
    </listener>
    <!-- 配置前端控制器 -->
    <servlet>
        <servlet-name>ExerciseController</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        <init-param>
            <param-name>contextConfigLocation</param-name>
            <param-value>classpath:spring-mvc.xml</param-value>
        </init-param>
        <load-on-startup>1</load-on-startup>
    </servlet>
    <servlet-mapping>
        <servlet-name>ExerciseController</servlet-name>
        <url-pattern>/</url-pattern>
    </servlet-mapping>
    <!-- 字符编码处理器 -->
    <filter>
        <filter-name>encoding</filter-name>
        <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
        <init-param>
            <param-name>encoding</param-name>
            <param-value>utf-8</param-value>
        </init-param>
        <init-param>
            <param-name>forceRequestEncoding</param-name>
            <param-value>true</param-value>
        </init-param>
        <init-param>
            <param-name>forceResponseEncoding</param-name>
            <param-value>true</param-value>
        </init-param>
    </filter>
    <filter-mapping>
        <filter-name>encoding</filter-name>
        <url-pattern>/*</url-pattern>
    </filter-mapping>
</web-app>
```

5️⃣ **编写控制器**

> **基本格式参考**

```java
@RequestMapping("demo")
/**
* 其中value指传过来的参数的name值
*/
public String demo(@RequestParam(value = "name1") String name,@RequestParam(value = "age") int age) {
System.out.println("执行demo！"+"你叫"+name+",今年"+age);
return "index.jsp";
}
@RequestMapping("demo2")
/**
* spring-mvc根据参数名称自动赋值
*/
public String demo2(People pp){
System.out.println("执行demo2！"+pp);
return "two.jsp";
}
@RequestMapping("demo3")
/**
* 如果方法参数是基本数据类型，不是封装类  防止500错误
* 其中defaultValue指 如果界面没有传指定参数值，参数就取默认值
*/
public String demo3(@RequestParam(defaultValue = "强哥") String name,@RequestParam(defaultValue = "2") int age){
System.out.println("demo3你的名称叫"+name+"---今年"+age+"岁！");
return "two.jsp";
}
@RequestMapping("demo4")
/**
* 其中required是指 指定参数必须传值，否则报400异常
*/
public String demo4(@RequestParam(required = true) String name){
System.out.println("demo4名称"+name);
return "two.jsp";
}
```

> **常用注解和参数详情**

| 参数            | 作用                                                         |
| --------------- | ------------------------------------------------------------ |
| @Controller     | 标识此类为控制器类，交由spring-mvc管理                       |
| @RequestMapping | 设置方法请求路径，参数类型是数组 produces可以设置头部响应类型 |
| @Resource       | 默认按照ByName自动注入，然后再按照类型注入                   |
| @RequestParam   | 参数实际名称设置，用于匹配name值，参数名称==jsp中的name      |
| @ModelAttribute | 该Controller的所有方法在调用前，先执行此@ModelAttribute方法，可用于注解和方法参数中 |
| @PathVariable   | 获取请求路径中的值，一般用于restful风格                      |
| restful风格     | 请求路径 demo7/123/谦哥                                      |
| @ResponseBody   | 恒不跳转 <br>如果返回值类型满足key-value<br>①响应头设置为application/json;charset=utf-8<br/>②把转换后的内容以输出流的形式响应给客户端<br>如果返回值类型不满足key-value<br>① 把响应头设置为text/html<br/>② 把方法返回值以流的形式直接输出 |

### 三、必要扩充

1️⃣ **文件下载**

>  **导入依赖**

```xml
<!-- 文件下载需要的依赖 -->
<dependency>
<groupId>commons-fileupload</groupId>
<artifactId>commons-fileupload</artifactId>
<version>1.3.1</version>
</dependency>
<dependency>
<groupId>commons-io</groupId>
<artifactId>commons-io</artifactId>
<version>2.4</version>
</dependency>
```

>  **放行静态资源**

```xml
<!-- 放行静态资源 -->
<mvc:resources mapping="/files/**" location="/files/"></mvc:resources>
```

> **编写控制器**

```java
@RequestMapping("download")
public void download(String filename, HttpServletResponse response, HttpServletRequest request) throws IOException {
//设置头文件
response.setHeader("Content-Disposition","attachment;filename=dd.txt");
//输出二进制字符流
ServletOutputStream os = response.getOutputStream();
//得到资源文件夹的完整位置
String path = request.getServletContext().getRealPath("files");
//得到文件位置
File file = new File(path, filename);
byte[] bytes = FileUtils.readFileToByteArray(file);
os.write(bytes);
os.flush();
os.close();
}
```

2️⃣ **文件上传**

> **说明**

```txt
基于apache的commons-fileupload.jar文件上传
```

> MultipartResovler作用

```txt
  1 把客户端上传的文件流转换成MutipartFile封装类
  2 通过MutipartFile封装类获取到文件流
                
```

> 表单数据类型分类

```txt
1 在<form>的enctype属性控制表单类型

2 默认值application/x-www-form-urlencoded，表示普通表单数据（少量文字信息）

3 text/plain 大文字量时使用的类型，如邮件、论文

4 multipart/form-data 表单中包含二进制文件内容
```

> **实现步骤**

① 导入spring-mvc包和apache的commons-fileupload和commons-io.jar

② 编写jsp页面 提交方式不能为get请求

```html
get：字符流
post：字节流
<form enctype="multipart/form-data" action="upload" method="post" >
姓名：<input type="text" name="name" ><br>
文件：<input type="file" name="file" ><br>
<input type="submit" value="提交" >
</form>
```

③ 配置spring-mvc.xml

```xml
<!-- 文件解析器,id有固定值multipartResolver-->
<bean id="multipartResolver" class="org.springframework.web.multipart.commons.CommonsMultipartResolver">
<property name="maxUploadSize" value="20000"/>
<property name="defaultEncoding" value="utf-8" />
</bean>
<!-- 异常解析器配置 -->
<bean id="exceptionResolver" class="org.springframework.web.servlet.handler.SimpleMappingExceptionResolver">
<property name="exceptionMappings">
<props>
<!-- 当文件字节超过maxUploadSize时报错指向错误的界面 -->
<prop key="org.springframework.web.multipart.MaxUploadSizeExceededException">/error.jsp</prop>
</props>
</property>
</bean>
```

④ 编写控制器类

```java
@RequestMapping("upload")
public String upload(MultipartFile file,String name) throws IOException {
System.out.println("name:"+name);
//得到文件名称
String filename = file.getOriginalFilename();
//得到文件后缀 .?
String suffix = filename.substring(filename.lastIndexOf("."));
//判断是否是png后缀
if(suffix.equalsIgnoreCase(".png")){
//随机唯一id
String uuid = UUID.randomUUID().toString();
//下载路径
FileUtils.copyInputStreamToFile(file.getInputStream(),new File("E:/"+uuid+suffix));
return "/index.jsp";
}else {
return "/error.jsp";
}
```

3️⃣ **自定义拦截器**（跟过滤器比较像的技术）

> 发送请求时 被拦截器拦截，在控制器的前后添加额外功能         

```txt
1 跟AOP区分开，AOP在特定方法前后进行扩充织入
            
2 拦截器，请求的拦截，针对点是控制器的方法
```

> **SpringMVC拦截器和Filter的区别**

```txt
1 拦截器只能拦截Controller

2 Filter可以拦截所有请求
```

> **实现自定义拦截器的步骤**	

① 新建实现类HandlerInterceptor

```java
package com.xq.interceptor;

import org.springframework.web.servlet.HandlerInterceptor;
import org.springframework.web.servlet.ModelAndView;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

/**
* @author 蟹老板
* @version 1.0.0
* @ClassName DemoInterceptor.java
* @Description TODO
* @createTime 2020年05月31日 19:02:00
*/
public class DemoInterceptor implements HandlerInterceptor {
//在进入控制器之前进入 如果返回值为false，阻止进入控制器
public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
System.out.println(handler);
System.out.println("preHandle");
return true;
}
//控制执行完-->jsp执行前 执行
//用于日志记录 过滤敏感词语
public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) throws Exception {
modelAndView.getModel().put("md","我是监听器修改model值！");
String speak = modelAndView.getModel().get("speak").toString();
String newspeak = speak.replace("祖国", "**");
modelAndView.getModel().put("speak",newspeak);
System.out.println("postHandle");
}
//记录执行过程中产生的异常
//把异常输出到日志中
//jsp执行完执行
public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception {
System.out.println("异常信息："+ex.getMessage());
System.out.println("afterComletion:");
}
}
```

② 在spring-mvc.xml 配置xml

```xml
<!-- 拦截器 -->
<mvc:interceptors>
<mvc:interceptor>
<!-- 拦截特定路径 这里只拦截根目录下的demo01,demo02 -->
<mvc:mapping path="/demo01"/>
<mvc:mapping path="/demo02"/>
<bean class="com.xq.interceptor.DemoInterceptor"></bean>
</mvc:interceptor>
<!-- 直接配置bean 拦截所有控制器 -->
<!-- <bean class="com.xq.interceptor.DemoInterceptor"></bean>-->
</mvc:interceptors>
```

> **拦截器栈（多个拦截器同时生效时，组成了拦截器栈）**

① 执行顺序

```txt
  preHandle(A)-->preHandle(B)-->控制器方法-->postHandle(B)-->postHandle(A)
  -->jsp-->afterCompletion(B)-->afterCompletion(A)     
```

② 配置xml

```xml
<!-- 拦截器 -->
<mvc:interceptors>
<mvc:interceptor>
<!-- 拦截特定路径 这里只拦截根目录下的demo01,demo02 -->
<mvc:mapping path="/demo01"/>
<mvc:mapping path="/demo02"/>
<!-- 优些执行顺序 -->
<bean class="com.xq.interceptor.DemoInterceptor"></bean>
<bean class="com.xq.interceptor.DemoInterceptor1"></bean>
</mvc:interceptor>
<!-- 直接配置bean 拦截所有控制器 -->
<!-- <bean class="com.xq.interceptor.DemoInterceptor"></bean>-->
</mvc:interceptors>
```

### 四、SpringMVC运行原理

```txt
  如果在web.xml中配置了DispacherServlet的<url-pattern>为/时，
                
    当用户发起请求时，请求第一个控制器时，会执行DispacherServlet，
            
    由DispacherServlet调用HandlerMapping，具体调用哪个类根据项

    目环境，如果使用注解方式，那就调用DefaultAnnotactionHandler

    Mapping解析URL，解析后调用HandlerAdatper组件的Annotactio
            
    nMethodHandlerAdatper调用Controller中的HandlerMethod，当

    HandlerMethod执行完成后会返回View，会被ViewResovler进行视

    图解析，解析后执行对应的jsp文件，最终响应给客户端。    
```

用户请求---->DispacherServlet---->HandlerMapping---->HandlerAdapter---->控制器---->ViewResovler---->JSP

### 五、分页插件使用

> **导入依赖**

```xml
    <dependency>
        <groupId>com.github.pagehelper</groupId>
        <artifactId>pagehelper</artifactId>
        <version>5.1.11</version>
    </dependency>
```

> **在applicationContext.xml中的会话工厂里配置对应分页信息**

```xml
  <!-- 配置会话工厂 -->
    <bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
        <!-- 连接数据源 -->
        <property name="dataSource" ref="dataSource" />
        <!-- 别名设置 -->
        <property name="typeAliasesPackage" value="com.xq.pojo" />
        <!-- 加载mybatis-config配置文件 -->
        <property name="configLocation" value="classpath:mybatis-config.xml" />
        <!--<property name="mapperLocations" value=""-->

        <!--ssm分页插件得使用-->
        <property name="plugins">
            <array>
                <bean class="com.github.pagehelper.PageInterceptor">
                    <property name="properties">
                        <value>
                            helperDialect=mysql
                        </value>
                    </property>
                </bean>
            </array>
        </property>
    </bean>
```

> **编写Mapper.xml**

```xml
 <!--通过实体作为筛选条件查询,用于模糊查询和分页-->
    <select id="queryAll" resultMap="ExerciseMap">
        select
          id, age, name, height, hobby
        from ssm.exercise
        <where>
            <if test="name!=null and name!=''">
                name like concat ('%',#{name},'%')
            </if>
            <if test="age!=null and age !='' ">
                and age like concat ('%',#{age},'%')
            </if>
        </where>
    </select>
```

> **编写service接口**

```java
    /**
     * 查询指定行数据
     *
     * @param pageNumber 查询起始位置(分页)
     * @param pageSize 查询条数(分页)
     * @param name 查询条件name(模糊查询)
     * @param age 查询条件age(模糊查询)
     * @return 对象列表
     */
    PageInfo<Exercise> findByPage(Integer pageNumber,Integer pageSize,String name,Integer age);
```

> **编写serviceimpl实现该方法**

```java
 public PageInfo<Exercise> findByPage(Integer pageNumber, Integer pageSize,String name,Integer age) {
        //1、开启分页
        PageHelper.startPage(pageNumber,pageSize);
        //2、查询所有信息
        List<Exercise> list = ed.queryAll(name, age);
        PageInfo<Exercise> pageInfo=new PageInfo<Exercise>(list);
        return pageInfo;
    }
```

> **编写Controller类**

```java
 @Autowired
    private ExerciseServiceImpl sei;

    @RequestMapping({"/","findPage/{pageNum}/{pageSize}","findPage"})
    public ModelAndView findPage(@PathVariable(required = false) Integer pageNum,@PathVariable(required = false) Integer pageSize
    ,@RequestParam(required = false) String name,@RequestParam(required = false) Integer age){
        ModelAndView md=new ModelAndView();
        if(pageNum==null){
            pageNum=1;
        }
        if(pageSize==null){
            pageSize=3;
        }
        PageInfo<Exercise> arr=sei.findByPage(pageNum,pageSize,name,age);
        md.addObject("arr",arr);
        System.out.println("集合值："+arr);
        md.setViewName("/index.jsp");
        return md;
    }
```

> jsp展示 bootstrap前端框架美化前台

```java
<c:forEach items="${arr.list}" var="fw">
        <tr>
            <td>${fw.id}</td>
            <td>${fw.name}</td>
            <td>${fw.age}</td>
            <td>${fw.height}</td>
            <td>${fw.hobby}</td>
            <td style="text-align:center">
                <span style="margin-right:20px" class="glyphicon glyphicon-pencil" aria-hidden="true" onclick="upd(${fw.id})" title="修改"></span>
                <span class="glyphicon glyphicon-remove remove" aria-hidden="true" title="删除"></span>
            </td>
        </tr>
    </c:forEach>
    <tr>
        <td colspan="6">
            <%--分页--%>
            <nav aria-label="Page navigation" style="text-align: center">
                <ul class="pagination" >
                    <%--上一页--%>
                    <c:if test="${arr.hasPreviousPage}">
                        <li>
                            <a href="/findPage/${arr.pageNum-1}/3" aria-label="Previous">
                                <span aria-hidden="true">&laquo;</span>
                            </a>
                        </li>
                    </c:if>
                    <%-- 分页 --%>
                    <c:forEach begin="1" end="${arr.pages}" step="1" var="i">
                        <li <c:if test="${arr.pageNum==i}">class="active"</c:if>>
                            <a href="/findPage/${i}/3">${i}</a></li>
                    </c:forEach>
                    <%-- 下一页 --%>
                    <c:if test="${arr.hasNextPage}">
                        <li>
                            <a href="/findPage/${arr.pageNum+1}/3" aria-label="Next">
                                <span aria-hidden="true">&raquo;</span>
                            </a>
                        </li>
                    </c:if>
                </ul>
            </nav>
        </td>
    </tr>
```

> PageInfo参数详解

| 属性             | 作用                                                         |
| ---------------- | ------------------------------------------------------------ |
| pageNum          | 当前所在第几页                                               |
| pageSize         | 每页展示的最大数量                                           |
| size             | 当前页的数量                                                 |
| startRow         | *//由于startRow和endRow不常用，这里说个具体的用法*     <br>*//可以在页面中"显示startRow到endRow 共size条数据"*     <br>*//当前页面第一个元素在数据库中的行号* |
| endRow           | 当前页面最后一个数据在数据库中的记录                         |
| pages            | 总页数                                                       |
| list             | 分页查询的结果集                                             |
| firstPage        | 第一页                                                       |
| prePage          | 前一页                                                       |
| isFirstPage      | 是否为第一页                                                 |
| isLastPage       | 是否是最后一页                                               |
| hasPreviousPage  | 是否有前一页                                                 |
| hasNextPage      | 是否有下一页                                                 |
| navigatePages    | 导航页码数                                                   |
| navigatepageNums | 所有导航页码                                                 |


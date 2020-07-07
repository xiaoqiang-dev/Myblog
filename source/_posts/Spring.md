# Spring开发笔记



<style>
    pre{
        background-color:white;
    }
    </style>



[TOC]

### 一、官方文档

[点击进入Spring文档](https://spring.io/)

### 二、简介Spring

1、Spring是一个分层的JavaSE/EE full-stack(一站式) 轻量级开源框架

![{% asset_img Image.png This is an example image %}](E:/Myblog/source/_posts/Spring/Image.png)

2、核心**IOC（Inversion of Control）/DI（Dependency Injection）**和**AOP（Aspect Oriented Programming）**

​		1️⃣ **IOC（控制反转）：**

​						概念：程序员原先通过new来创建实例化对象的事情，转交给Spring

​						控制：控制价类的对象

​						反转：转交给Spring容器

​						作用：解耦（低耦合高内聚） 解耦是指程序员不需要管理对象，解除了管理对象和程序员之间的耦									合

​		2️⃣ **DI（依赖注入）：**

​						概念：当一个类（A）需要依赖另一个类（B）时，我们把B赋给A的过程叫做依赖注入

​		3️⃣ **AOP（面向切面编程）：**

​						概念：在原有程序纵向执行中对某一个方法或者多个方法添加通知(织入)，形成横向切面的过程叫做									面向切面编程

​						常用概念：

- ​							原有功能：切点（pointcut）

- ​							前置通知：在切点之前执行的功能（before advice）

- ​							后置通知：在切点之后执行的功能（after advice）

- ​							异常通知：如果切点执行的过程中出现异常，会触发异常通知（throws advice）

- ​							切面：所有的这些功能叫做切面

- ​							织入：把切面嵌入到原有的功能上叫做织入

  ​	

### 三、具体实现

**1、 环境搭建**

```xml
<dependencies>
    <!-- spring核心依赖 -->
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-beans</artifactId>
        <version>5.2.5.RELEASE</version>
    </dependency>
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-core</artifactId>
        <version>5.2.5.RELEASE</version>
    </dependency>
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-context</artifactId>
        <version>5.2.4.RELEASE</version>
    </dependency>
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-expression</artifactId>
        <version>5.2.4.RELEASE</version>
    </dependency>
    <!-- mybatis整合spring依赖 -->
    <dependency>
        <groupId>org.mybatis</groupId>
        <artifactId>mybatis-spring</artifactId>
        <version>2.0.4</version>
    </dependency>
    <!-- mybatis依赖 -->
    <dependency>
        <groupId>org.mybatis</groupId>
        <artifactId>mybatis</artifactId>
        <version>3.5.4</version>
    </dependency>
    <!-- 单元测试 -->
    <dependency>
        <groupId>junit</groupId>
        <artifactId>junit</artifactId>
        <version>4.13</version>
    </dependency>
    <!-- spring数据库驱动 -->
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-jdbc</artifactId>
        <version>5.1.2.RELEASE</version>
    </dependency>
    <!-- c3p0驱动 -->
    <dependency>
        <groupId>com.mchange</groupId>
        <artifactId>c3p0</artifactId>
        <version>0.9.5.2</version>
    </dependency>
    <!-- lombok插件依赖 -->
    <dependency>
        <groupId>org.projectlombok</groupId>
        <artifactId>lombok</artifactId>
        <version>1.18.12</version>
    </dependency>
    <!-- spring提供的测试类 -->
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-test</artifactId>
        <version>5.2.4.RELEASE</version>
        <scope>test</scope>
    </dependency>
    <!-- spring日志 -->
    <dependency>
        <groupId>commons-logging</groupId>
        <artifactId>commons-logging</artifactId>
        <version>1.2</version>
    </dependency>
    <!-- mybaits日志 -->
    <dependency>
        <groupId>log4j</groupId>
        <artifactId>log4j</artifactId>
        <version>1.2.17</version>
    </dependency>
    <!-- mysql驱动 -->
    <dependency>
        <groupId>mysql</groupId>
        <artifactId>mysql-connector-java</artifactId>
        <version>5.1.30</version>
    </dependency>

</dependencies>
    <build>
        <resources>
            <resource>
                <directory>src/main/java</directory>
                <includes>
                    <include>**/*.xml</include>
                    <include>**/*.properties</include>
                </includes>
                <filtering>true</filtering>
            </resource>
        </resources>
    </build>
```

**2、 创建applicationContext.xml**

1️⃣ bean作用域：默认单例 singleton 通过scop=“ ” 设置 值包括

| 参数值         | 作用                                                         |
| -------------- | ------------------------------------------------------------ |
| singleton      | 单例模型，只要容器不销毁或退出，该类型的bean的单一实例就会一直存活 |
| prototype      | spring容器在进行输出prototype的bean对象时，会每次都重新生成一个新的对象给请求方 |
| request        | 请求结束后，该对象的生命周期即告结束，**如同java web中request的生命周期** |
| session        | Spring容器会为每个独立的session创建属于自己的全新的UserPreferences实例，比request scope的bean会存活更长的时间，其他的方面没区别，**如同java web中session的生命周期**。 |
| global session | 只有应用在基于porlet的web应用程序中才有意义，它映射到porlet的global范围的session，如果普通的servlet的web 应用中使用了这个scope，容器会把它作为普通的session的scope对待。 |

2️⃣ pom.xml依赖

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/context https://www.springframework.org/schema/context/spring-context.xsd">
    <!-- 扫描对应包下的注解 -->
    <context:component-scan base-package="com.xq" />
    <!-- 配置数据源 -->
    <bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource" >
        <property name="driverClass" value="${jdbc.driverClassName}"/>
        <property name="jdbcUrl" value="${jdbc.url}"/>
        <property name="user" value="${jdbc.username}"/>
        <property name="password" value="${jdbc.password}"/>
    </bean>
    <!-- 加载properties文件 -->
    <context:property-placeholder location="jdbc.properties"/>
    <!-- 生产工厂 里面可以指定mybatis支持的事务 -->
    <bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
        <property name="dataSource" ref="dataSource" />
        <!-- mapper的返回值起别名 名称叫Pet可以省掉路径直接-->
        <property name="typeAliasesPackage" value="com.xq.pojo"/>

    </bean>
    <!-- 扫描器 -->
    <bean class="org.mybatis.spring.mapper.MapperScannerConfigurer">
        <!-- 扫描对应包 -->
        <property name="basePackage" value="com.xq.mapper"/>
        <!-- 指定工厂对象，如果只有一个可以省略，多个要如下指定 -->
        <property name="sqlSessionFactoryBeanName" value="sqlSessionFactory"/>
    </bean>
</beans>
```

**3、 相应注解**

| 参数（bean的id默认值类名首字母小写） | 作用范围                               |
| ------------------------------------ | -------------------------------------- |
| -@Component                          | 基本注解，标识了一个受Spring管理的组件 |
| -@Responsitory                       | 标识持久层组件                         |
| -@Service                            | 标识服务层(业务层)组件                 |
| -@Controller                         | 标识表现层组件                         |

**4、 bean的获取方式**

```java
ApplicationContext ac=new ClassPathXmlApplicationContext("applicationContext.xml");
getBean("<bean/>标签的id值",返回值类型bean.class);
String[] Names=ac.getBeanDefinitionNames(); //Spring容器中所有被管理的对象
```

**5、 AOP（Aspect Oriented Programming）**

1️⃣  利用一种称为"横切"的技术，剖解开封装的对象内部，并将那些影响了多个类的公共行为    封装到一个可重用模块，并将其命名为"Aspect"，即切面。所谓"切面"，简单说就是那些与业 务无关，却为业务模块所共同调用的逻辑或责任封装起来，便于减少系统的重复代码，降低模块之间的耦合度，并有利于未来的可操作性和可维护性。

2️⃣ 原理：底层采用代理机制（默认JDK的动态代理）

```xml
	//如果要使用cglib动态代理 proxy-target-class值改为true
	<aop:aspectj-autoproxy proxy-target-class="true"/>
```



3️⃣ 作用：不需要修改原有代码，具有高扩展性，原有功能相当于释放了部分逻辑，让方法功能职责更加明确

4️⃣ 常用：如权限认证、日志、事物

5️⃣ AOP的两种实现方式：

​	① Schema-method   

​			特点：每个通知都需要实现特定的类或接口

​			代码：

```java
@Component
@Aspect
public class NoteMyAdvice {
/**
* 前置通知
*/
@Before("com.xq.aoptest.NoteDemo.note2()")
public void myBefore(){
System.out.println("我是前置通知，哥们！");
}

/**
* 后置通知
*/
@After("com.xq.aoptest.NoteDemo.note2()")
public void myAfter(){
System.out.println("我是后置通知，小老弟！");
}

/**
* 异常通知
*/
@AfterThrowing(pointcut = "com.xq.aoptest.NoteDemo.note2()",throwing = "e")
public void myThrow(Exception e){
System.out.println("我是异常通知！"+e);
}

/**
* 环绕通知
*/
@Around("com.xq.aoptest.NoteDemo.note2()")
public Object myAround(ProceedingJoinPoint pjp) throws Throwable {
System.out.println("环绕-前置通知！");
Object result= pjp.proceed();
System.out.println("环绕-后置通知！");
return result;
}
}
```



​	② Aspectj

​			特点：每个通知不需要实现特定的接口或类

6️⃣ 两种配置方式：

xml：

```xml
 <aop:aspectj-autoproxy proxy-target-class="true"/>
    <bean id="myafter" class="com.xq.advice.MyAfterAdvice"/>
    <bean id="mythrow" class="com.xq.advice.MyThrow"/>
    <bean id="mybefore" class="com.xq.advice.MyBeforeAdvice"/>
    <aop:config>
        <aop:pointcut id="mypoint" expression="execution(* com.xq.aoptest.demo.*(..))"/>
        <!-- 前置通知 -->
        <aop:advisor advice-ref="mybefore" pointcut-ref="mypoint"/>
        <!-- 后置通知 -->
        <aop:advisor advice-ref="myafter" pointcut-ref="mypoint"/>
        <!-- 异常通知 -->
        <aop:aspect ref="mythrow">
            <aop:after-throwing method="mythrow1" pointcut-ref="mypoint" throwing="e"/>
        </aop:aspect>
    </aop:config>
    <aop:config>
        <aop:pointcut id="mypoint" expression="execution(* com.xq.aoptest.demo.*(..))"/>
        <aop:advisor advice-ref="myarround" pointcut-ref="mypoint"/>
    </aop:config>
    <bean id="myarround" class="com.xq.advice.MyArround"/>
    <bean id="demo" class="com.xq.aoptest.demo"/>
```

注解：

```java
import org.apache.log4j.Logger;
import org.aspectj.lang.JoinPoint;
import org.aspectj.lang.annotation.*;
import org.springframework.stereotype.Component;

/**
 * @author 蟹老板
 */
@Aspect
@Component
public class LogAspect {
    Logger logger=Logger.getLogger(LogAspect.class);
    @Before("execution(* com.xq.service.impl.*.*(..))")
    public void Before(){
        logger.info("前置记录日志...");
    }
    @AfterReturning(value = "execution(* com.xq.service.impl.*.*(..))",returning = "result")
    public void after(JoinPoint jp,Object result){
        Object[] args = jp.getArgs();
        int i=0;
        for (Object arg : args) {
            i++;
            System.out.println("第"+i+"参数值"+arg);
        }
        System.out.println("返回值："+result);
        logger.info("后置记录日志...");
    }
    @AfterThrowing(value = "execution(* com.xq.service.impl.*.*(..))",throwing = "e")
    public void Afterthows(Exception e){
        System.out.println("出现异常："+e);
    }
```

**6、事务**

1️⃣ 编程式事务

​	① 由程序员编程事务控制代码

​	② OpenSessionView 属于 编程式事务

2️⃣ 声明式事务

​	① 事务的控制代码已经由spring写好，程序员只需要声明哪些地方需要进行事务控制和如何进行事务控制

3️⃣ 声明式事务式是针对于serviceimpl类下的方法进行的   

4️⃣ 事务管理器是基于（advice）的     

5️⃣ 在spring配置文件中配置声明式事务

```xml
<!-- 配置声明式事务的对应数据源 -->
<bean id="txManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
<property name="dataSource" ref="dataSource"/>
</bean>
<!-- 配置事务管理的方法 -->
<tx:advice transaction-manager="txManager" id="txAdvice">
<tx:attributes>
<tx:method name="insert"/> //name：指哪些方法需要事务控制
</tx:attributes>
</tx:advice>
<!-- 声明配置事务管理的范围 -->
<aop:config>
<aop:pointcut id="mypoint" expression="execution(* com.xq.service.impl.*.*(..))"/>
<aop:advisor advice-ref="txAdvice" pointcut-ref="mypoint"/>
</aop:config>
```

​		

[^☞read-only]:如果为true，告诉数据库此事务是只读事务，数据库会优化，会对性能可能有一定的提升                         							，所以只要是查询建议使用此属性,如果为false（默认值），事务需要提交的事务，建议增删改

☞propagation

| **propagation参数** | **作用**                                                     |
| ------------------- | ------------------------------------------------------------ |
| REQUIRED（默认值）  | **如果当前有事务，就在事务中执行，如果当前没有事务，就在非事务状态下执行** |
| SUPPORTS            | **如果当前有事务就在事务就在事务中执行，如果当前没有事务，就在非事务状态下进行** |
| MANDATORY           | **必须在事务内部执行，如果当前有事务，就在事务中执行，如果没有事务，就报错** |
| REQUIRES_NEW        | **必须在事务中进行，如果当前没有事务，新建事务，如果当前有事务，把当前事务挂起** |
| NOT_SUPPORTED       | **必须在非事务状态下进行，如果当前没有事务，新建事务，如果当前有事务就把当前事务挂起** |
| NEVER               | **必须在非事务下进行，如果当前没有事务，正常执行，如果当前有事务，就报错** |
| NESTED              | **必须在事务状态下进行，如果当前没有事务，新建事务，如果当前有事务，创建一个嵌套事务** |

6️⃣ 事务的隔离级别 通过isolation属性设置

​	保证在多线程或并发访问下如何保证访问到的数据具有完整性

| 参数                             | 作用                                                         |
| -------------------------------- | ------------------------------------------------------------ |
| 脏读                             | 一个事务（A）读取另一个未提交的事务（B）时，B事务可能进行了改变，此时A事务读取的数据可能和数据库中的数据不是一致的，此时认为此数据是脏数据，读取脏数据过程叫做脏读 |
| 不可重复读（处理方法-->加上锁🔒） | 主要针对的操作是修改操作，俩次读取在同一个，表当事务A第一次读取事务后，事务B对事务A读取的数据进行了修改，事务A再次读取的数据和之前的数据不一致，过程叫做不可重复读 |
| 幻读                             | 事务A按照特定条件查询出来的结果，事务B新增了一条符合条件的数据，事务A中查询出来的数据和数据库中的数                               据不一致的，事务A好像出现了幻觉，这种情况叫做幻读 |

☞isolation

| isolation参数值 | 作用                                                         |
| --------------- | ------------------------------------------------------------ |
| DEFAULT         | 默认值，由底层数据库自动判断应该使用隔离级别                 |
| read_uncommit   | 可以读取未提交数据，可能出现脏读，不重复读，幻读 -->效率最高 |
| read_commit     | 只能读取其他已交提交的事务，防止脏读，可能出现不可重复读和幻读 |
| repeatable_read | 读取的数据会被添加锁，防止其它事务修改此数据，可以防止不可重复读、脏读，可能出现幻读 |
| serializable    | 排队操作，对整个表添加锁，一个事务在操作数据库数据时，另一个事务等待这个事务操作完成后才能操作这 个表（最安全、但最慢） |


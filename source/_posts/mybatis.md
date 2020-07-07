# Mybatis----强哥的开发日记

**一、开发工具 Maven+IDEA**

<style>
    pre{
        background-color:white;
    }

</style>

- [mybatis官网](https://mybatis.org/mybatis-3/)

- [maven官网](https://maven.apache.org/)
- [IDEA官网](https://www.jetbrains.com/idea/)

**二、mybatis简介**

1. `什么是mybatis`

   （1）Mybatis是一个半ORM（对象关系映射）框架，它内部封装了JDBC，开发时只需要关注SQL语句本身，		  不需要花费精力去处理加载驱动、创建连接、创建statement等繁杂的过程。程序员直接编写原生sql，	      可以严格控制sql执行性能，灵活度高。

   （2）MyBatis 可以使用 XML 或注解来配置和映射原生信息，将 POJO映射成数据库中的记录，避免了几乎所		  有的 JDBC 代码和手动设置参数以及获取结果集。

   （3）通过xml 文件或注解的方式将要执行的各种 statement 配置起来，并通过java对象和 statement中sql的		  动态参数进行映射生成最终执行的sql语句，最后由mybatis框架执行sql并将结果映射为java对象并返。	   （从执行sql到返回result的过程）。

2. `IDEA效率开发插件`

   （1）lombok 提供get、set、toString方法，一般用于实体类的自动封装（常用注解@Data）

   （2）Free Mybatis plugin 主要功能提供：

   ​			① 生成mapper xml文件
   ​			② 快速从代码跳转到mapper及从mapper返回代码
   ​			③ mybatis自动补全及语法错误提示
   ​			④ 集成mybatis generator gui界面     

   （3）EasyCode 基于IDEA内置DataBase 主要更能：

   ​			① 基于IntelliJ IDEA开发的代码生成插件，支持自定义任意模板（Java，html，js，xml）。
   ​			② 只要是与数据库相关的代码都可以通过自定义模板来生成。支持数据库类型与java类型映射关系配				置。
   ​			③ 支持同时生成生成多张表的代码。每张表有独立的配置信息。完全的个性化定义，规则由你设置。

   （4）Alibaba Java Coding 阿里编码规范，提供命名规范提示和扫描，有助于开发命令规范

**三、基本开发流程**

1. `编写dao层接口`

```java
package com.xq.mapper;

import com.xq.pojo.Edu_course;

import java.util.List;

/**
 * @author 蟹老板
 */
public interface Edu_courseMapper {
    /**
     * 查询全部
     * @return
     */
    List<Edu_course> seAll();
    Edu_course seById(@Param("id" int id);
}


```

[^@Param]:用于参数别名设置 底层原理是map集合 相当于给值设置key

​	`2.编写mapper映射文件`

```xml
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.xq.mapper.Edu_courseMapper">
    <cache readOnly="true"></cache>
    <!---自动映射 auto mapping-->
    <select id="seAll" resultType="Edu_course">
        select * from edu_course;
    </select>
    <!--手动映射-->
    <resultMap id="seById" type="Edu_course">
        <id column="COURSE_ID" property="COURSE_ID"/>
    </resultMap>
</mapper>
```

[^名称]:方法名称必须和映射文件中的对应标签的id值

​	`3.编写mybatis-config.xml文件`

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    <!-- 配置日志记录 -->
    <settings>
        <setting name="logImpl" value="LOG4J"/>
    </settings>
    <!-- 别名设置 -->
    <typeAliases>
        <package name="com.xq.pojo"/>
    </typeAliases>
    <!-- 数据源配置 -->
    <environments default="development">
        <environment id="development">
            <transactionManager type="JDBC"/>
            <dataSource type="POOLED">
                <property name="driver" value="com.mysql.jdbc.Driver"/>
                <property name="url" value="jdbc:mysql://localhost:3306/ssm?characterEncoding=UTF-8"/>
                <property name="username" value="root"/>
                <property name="password" value="999999"/>
            </dataSource>
        </environment>
    </environments>
    <mappers>
        <!-- 映射文件扫描 -->
        <package name="com.xq.mapper"/>
    </mappers>
</configuration>
```

[^参数配置]:可对应文档设置参数值及扩充

​	`4.编写工具类`

```java
package com.xq.util;

import org.apache.ibatis.io.Resources;
import org.apache.ibatis.session.SqlSession;
import org.apache.ibatis.session.SqlSessionFactory;
import org.apache.ibatis.session.SqlSessionFactoryBuilder;

import java.io.IOException;
import java.io.InputStream;

/**
 * @author 蟹老板
 */
public class MybatisUtil {
    /*声明工厂*/
    public static SqlSessionFactory factory;
    /*线程本地储存*/
    public static ThreadLocal<SqlSession> tl= new ThreadLocal<SqlSession> ();
    static {
        try {
            /*加载配置文件*/
            InputStream is= Resources.getResourceAsStream("mybatis-config.xml");
            /*创建工厂并赋值*/
            factory=new SqlSessionFactoryBuilder().build(is);
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
    public static SqlSession getSession(){
        /*判断session是否为空*/
        if(tl.get()==null){
            tl.set(factory.openSession());
        }
        return tl.get();
    }
}

```

[^工具类]:为了节省开发时的重复代码

​	`5.编写测试类 2x1种`

```java
 //引入解析文件
        InputStream is = Resources.getResourceAsStream("mybatis-cfg.xml");
        //得到工厂对象
        SqlSessionFactory factory=new SqlSessionFactoryBuilder().build(is);
        //通过factory得到session
        SqlSession session=factory.openSession();
        //注册接口并绑定
        Edu_courseMapper ec=session.getMapper(Edu_courseMapper.class);
        List<Edu_course> list=ec.seAll();
        for (Edu_course hh : list) {
            System.out.println(hh.toString());
        }
        //清理session缓存
        session.clearCache();
        //关闭session 同时事务也随之关闭
        session.close();
```

[^测试①]:第一种 没有编写工具类

```java
//得到工厂对象 
SqlSession session= MybatisUtil.getSession();
//注册接口
 AirplaneMapper am = session.getMapper(AirplaneMapper.class);
```

[^测试②]:直接调用工具类中的session对象

**四、知识扩充**

1. `懒加载`： 延迟加载 没有用到的sql语句不会执行（默认不会开启）

2. `mysql内连接和外连接的区别`： 

   ① 内连接（inner join）：取出两张表中匹配到的数据，匹配不到的不保留    

   ② 外连接（outer join）：取出连接表中匹配到的数据，匹配不到的也会保留，其值为NULL  

3. `缓存`

   ①  一级缓存（Map形式存在）：    key:sessionId    value:数据    同一个SqlSession下共享数据 也就是第一次	执行sql语法就把结果缓存起来    第二次执行同一个sql语法 不会去数据库拿 先从缓存里面读取    如果俩个	sql语句中执行增删改语句或者session.clearCache()清除缓存 也会去数据库重新读写 不共享上一次的sql缓	存     

   ②  二级缓存（Map形式存在）：    key：namespace    value：数据    同一个命名空间（namespace）下，	当[session.commit](http://session.commit/)()或者session.close()才会被缓存起来

4. `mybatis运行过程设计到的类：`

    ① Resources mybatis中IO流的工具类 

   ​      1.1.1 用于加载配置文件     

    ② SqlSessionFactoryBuilder() 构建器

   ​      1.2.1 用于构建SqlSessionFactory接口的实现类

    ③ XMLConfigBuilder mybatis全局配置文件内容的构建器类 

   ​       1.3.1 负责读取流内容并且转换为java代码      

    ④ Configuration 封装全局配置文件的所有内容 

   ​       1.4.1 全局配置文件内容存放在Configuration类中 类似于实体类      

    ⑤ DefaultSqlSessionFactory SqlSessionFactory的实现类      

    ⑥ Transaction 事务类

   ​        1.6.1 每一个SqlSession都有一个事务对象      

     ⑦ TransactionFactory 事务生产工厂 

   ​       1.7.1 负责生产Transaction      

     ⑧ Executor mybatis执行器 

   ​       1.8.1 负责执行SQL命令       

   ​	   1.8.2 相当于JDBC中的Statement或ParaStatement或CallableStatement 

   ​       1.8.3 默认执行器SimpleExecutor 

   ​       1.8.4 批量操作BatchExecutor 

   ​       1.8.5 通过openSession(参数控制)      

     ⑨ DefaultSqlSession SqlSession的实现类      

     ⑩ ExceptionFactory 错误工厂

# mysql5转mysql8

## 一、简介

1️⃣  [mysql官网](https://dev.mysql.com/)

2️⃣   **为什么要选择mysql8**<p style="color=red"></p>

**① MySQL 8.0 正式版 8.0.11 已发布，官方表示 MySQL 8 要比 MySQL 5.7 快 2 倍，还带来了大量的改进和更快的性能！**

② **MySQL 8.0 的速度要比 MySQL 5.7 快 2 倍。MySQL 8.0 在以下方面带来了更好的性能：读/写工作负载、IO 密集型工作负载、以及高竞争（”hot spot”热点竞争问题）工作负载。**

![img](https://cdn.jsdelivr.net/gh/xiaoqiang-dev/static/images/014526_i6nC_2720166.png)

**③ NoSQL**：MySQL 从 5.7 版本开始提供 NoSQL 存储功能，目前在 8.0 版本中这部分功能也得到了更大的改进。该项功能消除了对独立的 NoSQL 文档数据库的需求，而 MySQL 文档存储也为 schema-less 模式的 JSON 文档提供了多文档事务支持和完整的 ACID 合规性。

![img](https://cdn.jsdelivr.net/gh/xiaoqiang-dev/static/images/015939_Q5Q7_2720166.png)

**④窗口函数(Window Functions)**：从 MySQL 8.0 开始，新增了一个叫窗口函数的概念，它可以用来实现若干新的查询方式。窗口函数与 SUM()、COUNT() 这种集合函数类似，但它不会将多行查询结果合并为一行，而是将结果放回多行当中。即窗口函数不需要 GROUP BY。

**⑤ 隐藏索引**：在 MySQL 8.0 中，索引可以被“隐藏”和“显示”。当对索引进行隐藏时，它不会被查询优化器所使用。我们可以使用这个特性用于性能调试，例如我们先隐藏一个索引，然后观察其对数据库的影响。如果数据库性能有所下降，说明这个索引是有用的，然后将其“恢复显示”即可；如果数据库性能看不出变化，说明这个索引是多余的，可以考虑删掉。

**⑥ 降序索引**：MySQL 8.0 为索引提供按降序方式进行排序的支持，在这种索引中的值也会按降序的方式进行排序。

**⑦ 通用表表达式(Common Table Expressions CTE)**：在复杂的查询中使用嵌入式表时，使用 CTE 使得查询语句更清晰。

**⑧ UTF-8 编码**：从 MySQL 8 开始，使用 utf8mb4 作为 MySQL 的默认字符集。

**⑨ JSON**：MySQL 8 大幅改进了对 JSON 的支持，添加了基于路径查询参数从 JSON 字段中抽取数据的 JSON_EXTRACT() 函数，以及用于将数据分别组合到 JSON 数组和对象中的 JSON_ARRAYAGG() 和 JSON_OBJECTAGG() 聚合函数。

**⑩ 可靠性**：InnoDB 现在支持表 DDL 的原子性，也就是 InnoDB 表上的 DDL 也可以实现事务完整性，要么失败回滚，要么成功提交，不至于出现 DDL 时部分成功的问题，此外还支持 crash-safe 特性**，**元数据存储在单个事务数据字典中。

**⑩+ 高可用性(High Availability)**：InnoDB 集群为您的数据库提供集成的原生 HA 解决方案。

**⑩++**：对 OpenSSL 的改进、新的默认身份验证、SQL 角色、密码强度、授权。



## 二、开搞

1️⃣ [mysql8.0安装网址](https://dev.mysql.com/)

2️⃣ **解压到自己存放的地址文件**

![image-20200609154503459](https://cdn.jsdelivr.net/gh/xiaoqiang-dev/static/images/image-20200609154503459.png)

3️⃣ mysql8目录下创建data文件和my.ini

![image-20200609164139961](https://cdn.jsdelivr.net/gh/xiaoqiang-dev/static/images/image-20200609164139961.png)

```ini
[mysqld]
#设置3306端口
port=3306
#设置mysql的安装目录
basedir=E:\mysql\mysql-8.0.20-winx64
#设置mysql数据库的数据的存放目录
datadir=E:\mysql\mysql-8.0.20-winx64\data
#允许最大连接数
max_connections=200
#允许连接失败的次数。
max_connect_errors=10
#服务端使用的字符集默认为utf8mb4
character-set-server=utf8mb4
#创建新表时将使用的默认存储引擎
default-storage-engine=INNODB
#默认使用“mysql_native_password”插件认证
#mysql_native_password
default_authentication_plugin=mysql_native_password
[mysql]
#设置mysql客户端默认字符集
default-character-set=utf8mb4
[client]
#设置mysql客户端连接服务端时默认使用的端口
port=3306
default-character-set=utf8mb4
```



3️⃣ **把以前的mysql5.7的环境变量替换成8.0的**

![image-20200609154740858](https://cdn.jsdelivr.net/gh/xiaoqiang-dev/static/images/image-20200609154740858.png)

4️⃣ **执行命令1 mysqld --initialize --console** 

![image-20200609155103267](https://cdn.jsdelivr.net/gh/xiaoqiang-dev/static/images/image-20200609155103267.png)

5️⃣ **执行命令2 net start mysql**

![image-20200609155535405](https://cdn.jsdelivr.net/gh/xiaoqiang-dev/static/images/image-20200609155535405.png)

6️⃣ **登录mysql 语法 mysql -u root -p**

![image-20200609161104925](https://cdn.jsdelivr.net/gh/xiaoqiang-dev/static/images/image-20200609161104925.png)

7️⃣ 输入**ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY '密？？？码';**

8️⃣  **大功告成**

> 驱动记得换成8.0以上的版本

```xml
  <!-- java连接mysql的jar包 -->
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>8.0.20</version>
        </dependency>
```

> db属性文件

```properties
driver=com.mysql.cj.jdbc.Driver
url=jdbc:mysql://localhost:3306/ssm?characterEncoding=utf8&useSSL=FALSE&serverTimezone=UTC
user=root
password=******
minIdle=45
maxIdle=50
initialSize=5
maxActive=100
maxWait=100
removeAbandonedTimeout=180
removeAbandoned=true
```


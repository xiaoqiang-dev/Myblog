## SpringBoot 服务端监控客户端 附带热部署

#### 一、简介

> 1️⃣  **对于SpringBoot项目 可以监控客户端的详细数据，提前预防错误或加以改进**

#### 二、创建服务端（一个服务端可以对应多个客户端）

> 1️⃣  **创建服务端工程（必须含有web启动器）**

![Image](https://pic.baixiongz.com/2020/06/28/f3193bdffd464.png)

> 2️⃣  **添加pom依赖 （针对服务端注意：目前在Spring Boot Admin Starter Server2.1.6版本不支持Spring Boot2.2.x版本，只支持到2.1.X）**

```java
	<dependency>
		<groupId>de.codecentric</groupId>
		<artifactId>spring-boot-admin-starter-server</artifactId>
		<version>2.1.6</version>
	</dependency>
```



> 3️⃣ **配置端口号**

````properties
server.port=9090
````

> 4️⃣ **启动类添加注解**

```java
@EnableAdminServer //开启Spring Boot Admin 服务端
```

> 5️⃣ 效果图

![Image](https://pic.baixiongz.com/2020/06/28/824a82a581630.png)

#### 三、创建客户端（可以有多个）

> 1️⃣  **导入依赖**

```java
<!-- https://mvnrepository.com/artifact/de.codecentric/spring-boot-admin-starter-client -->
<dependency>
<groupId>de.codecentric</groupId>
<artifactId>spring-boot-admin-starter-client</artifactId>
<version>2.1.6</version>
</dependency>

<!--Actuator坐标依赖-->
<dependency>
<groupId>org.springframework.boot</groupId>
<artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

> 2️⃣  **修改配置文件*（本人用yml配置出错，换成properties配置这个客户端配置就OK，不知道你们行不行）**

```properties
# 指定服务端
spring.boot.admin.url=http://localhost:9090
# 设置暴露所有端点
management.endpoints.web.exposure.include=*
 
# 设置单个端点（/shutdown）可用
# management.endpoint.shutdown.enabled=true
 
# 设置暴露所有端点，除了env
# management.endpoints.web.exposure.include=*
# management.endpoints.web.exposure.exclude=env
```

> 3️⃣  **效果图**

![Image](https://pic.baixiongz.com/2020/06/28/671ccbd438d9f.png)

#### 四、IDEA热部署配置

1️⃣ 导入依赖

```java
<!--热部署-->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-devtools</artifactId>
    <optional>true</optional>
</dependency>
```

2️⃣ 开启自动编译

![Image](https://pic.baixiongz.com/2020/06/28/fd28ad47321e2.png)

3️⃣ **勾选对应坐标（pom文件中 ctrl+shift+alt+/ ）*compiler.automake.allow.when.app.running***

![Image](https://pic.baixiongz.com/2020/06/28/2acae125471e1.png)
### SpringBoot整合Swagger2	

> 一、导入依赖

```txt
<!--添加对swagger的依赖-->
        <dependency>
            <groupId>io.springfox</groupId>
            <artifactId>springfox-swagger2</artifactId>
            <version>2.7.0</version>
        </dependency>
        <dependency>
            <groupId>io.springfox</groupId>
            <artifactId>springfox-swagger-ui</artifactId>
            <version>2.7.0</version>
        </dependency>
```

> 二、编写配置文件

```java
package com.xq.springbootmybatis.config;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import springfox.documentation.builders.ApiInfoBuilder;
import springfox.documentation.builders.PathSelectors;
import springfox.documentation.builders.RequestHandlerSelectors;
import springfox.documentation.service.ApiInfo;
import springfox.documentation.spi.DocumentationType;
import springfox.documentation.spring.web.plugins.Docket;
import springfox.documentation.swagger2.annotations.EnableSwagger2;

import java.util.ArrayList;

/**
 * swagger2作用在控制层上
 */
@Configuration
@EnableSwagger2
public class SwaggerConfig {
    @Bean
    public Docket createRestApi() {
        return new Docket(DocumentationType.SWAGGER_2)
                .apiInfo(apiInfo())
                .select()
                .apis(RequestHandlerSelectors.basePackage("com.xq.springbootmybatis.controller"))
                .paths(PathSelectors.any())
                .build();
    }

    private ApiInfo apiInfo() {
        return new ApiInfoBuilder()
                .title("springboot利用swagger构建api文档")
                .description("简单优雅的restfun风格，http://blog.csdn.net/saytime")
                .termsOfServiceUrl("http://blog.csdn.net/saytime")
                .version("1.0")
                .build();
    }
}
```

> 四、启动类上添加注解''@EnableSwagger2''

```java
@SpringBootApplication
@MapperScan("com.xq.springbootmybatis.dao") //指定扫描接口与映射配置文件
@EnableSwagger2
public class SpringbootMybatisApplication {
    public static void main(String[] args) {
        SpringApplication.run(SpringbootMybatisApplication.class, args);
    }

}
```

> 五、控制层注解

| 注解                                                     | 用法                         |
| -------------------------------------------------------- | ---------------------------- |
| @Api                                                     | 描述该控制层对于哪个表的操作 |
| @ApiOperation(value = "查询用户",notes = "返回用户集合") | 声明控制方法上，用于描述方法 |
| @GetMapping("访问路径")                                  | 处理查询的Get请求            |
| @PostMapping("访问路径")                                 | 处理增加的Post请求           |
| @PutMapping("访问路径")                                  | 处理修改的Put请求            |
| @DeleteMapping("访问路径")                               | 处理删除的Delete请求         |
|                                                          |                              |

> 六、响应结果(访问"http://localhost:8080/swagger-ui.html")

![image-20200622110311747](https://pic.baixiongz.com/2020/06/22/2066f85bb347c.png)
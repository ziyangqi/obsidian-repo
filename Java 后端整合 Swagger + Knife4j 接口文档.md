#### Java 后端整合 Swagger + Knife4j 接口文档

官方文档： https://doc.xiaominfo.com/docs/quick-start 

1.引入依赖

```java
<!--引入Knife4j的官方start包,Swagger2基于Springfox2.10.5项目-->
<dependency>
    <groupId>com.github.xiaoymin</groupId>
    <!--使用Swagger2-->
    <artifactId>knife4j-spring-boot-starter</artifactId>
    <version>2.0.9</version>
</dependency>
```

2.添加配置类，**注意：basePackage 需要填写 controller 的路径**

千万注意：线上环境不要把接口暴露出去！！！可以通过在 SwaggerConfig 配置文件开头加上 `@Profile({"dev", "test"})` 限定配置仅在部分环境开启

```java
@Configuration
@EnableSwagger2WebMvc
@Profile({"dev","test"})
public class Knife4jConfiguration {

    @Bean(value = "dockerBean")
    public Docket dockerBean() {
        //指定使用Swagger2规范
        Docket docket=new Docket(DocumentationType.SWAGGER_2)
                .apiInfo(new ApiInfoBuilder()
                //描述字段支持Markdown语法
                .description("# Knife4j RESTful APIs")
                .termsOfServiceUrl("https://doc.xiaominfo.com/")
                .contact("xiaoymin@foxmail.com")
                .version("1.0")
                .build())
                //分组名称
                .groupName("用户服务")
                .select()
                //这里指定Controller扫描包路径
                .apis(RequestHandlerSelectors.basePackage("com.yupi.yupao.controller"))
                .paths(PathSelectors.any())
                .build();
        return docket;
    }
}

```

 如果开发者使用的是Knife4j 2.x版本，并且Spring Boot版本高于2.4,那么需要在Spring Boot的yml文件中做如下配置： 

3.在 application.yml 中

```java
spring:
    mvc:
        pathmatch:
            # 配置策略
            matching-strategy: ant-path-matcher
```

4.controller类中备注接口相应的信息

```java
@Api(tags = "首页模块")
@RestController
public class IndexController {

    @ApiImplicitParam(name = "name",value = "姓名",required = true)
    @ApiOperation(value = "向客人问好")
    @GetMapping("/sayHi")
    public ResponseEntity<String> sayHi(@RequestParam(value = "name")String name){
        return ResponseEntity.ok("Hi:"+name);
    }
}
```

5.访问地址，**注意端口、实际的地址**

端口对应，正常访问http://localhost:8080/doc.html

如果配置类当中有配置路径，则需要加上

```java
server:
  port: 8888
  servlet:
    context-path: /api
```

该地址访问： http://localhost:8888/api/doc.html


#### springboot2.6.7的版本整合Swagger文档

1 引入依赖
```java
<!--    Swagger 依赖    -->  
<dependency>  
    <groupId>io.springfox</groupId>  
    <artifactId>springfox-swagger2</artifactId>  
    <version>2.9.2</version>  
</dependency>  
  
<!--    Swagger UI 依赖    -->  
<dependency>  
    <groupId>io.springfox</groupId>  
    <artifactId>springfox-swagger-ui</artifactId>  
    <version>2.9.2</version>  
</dependency>
```

2 创建配置类

``` Java
package cn.com.zxrail.config;  
  
import org.springframework.context.annotation.Bean;  
import org.springframework.context.annotation.Configuration;  
import org.springframework.web.servlet.config.annotation.WebMvcConfigurer;  
import springfox.documentation.builders.ApiInfoBuilder;  
import springfox.documentation.builders.PathSelectors;  
import springfox.documentation.builders.RequestHandlerSelectors;  
import springfox.documentation.service.ApiInfo;  
import springfox.documentation.service.Contact;  
import springfox.documentation.spi.DocumentationType;  
import springfox.documentation.spring.web.plugins.Docket;  
import springfox.documentation.swagger2.annotations.EnableSwagger2;  
  
@Configuration // 让 Spring 来加载该类配置  
@EnableSwagger2 // 启用 Swagger2.createRestApi 函数创建 Docket 的 Beanpublic class Swagger2Config implements WebMvcConfigurer {  
  
    /**  
     * 创建 API 应用  
     * apiInfo 增加 API 相关信息  
     * 通过 select() 函数返回一个 ApiSelectorBuilder 实例，用来控制哪些接口暴露给 Swagger 来展现  
     * 本例采用指定扫描的包路径来定义指定要建立 API 的目录  
     */  
    @Bean  
    public Docket createRestApi(){  
        return new Docket(DocumentationType.SWAGGER_2)  
                .apiInfo(apiInfo()) // 用来展示该 API 的基本信息  
                .select()   // 返回一个 ApiSelectorBuilder 实例，用来控制哪些接口暴露给 Swagger 来展现  
                .apis(RequestHandlerSelectors.basePackage("cn.com.zxrail.web"))  
                .paths(input -> {  
                    if (input != null && input.toString() != null) {  
                        return PathSelectors.any().apply(input);  
                    }  
                    return false;  
                })  
                .build();  
  
  
    }  
  
    /**  
     * 创建 API 的基本信息（这些基本信息会展现在文档页面中）  
     * 访问地址：http://xxx/swagger-ui.html  
     */    private ApiInfo apiInfo(){  
        return new ApiInfoBuilder()  
                .title("RESTful APIs")  
                .description("RESTful APIs")  
                .termsOfServiceUrl("http://localhost:8080/")  
                .contact(new Contact("ambrose", "swagger.example", "123@456.com"))  
                .version("1.0")  
                .build();  
    }  
}
```

3 我遇到的问题因为版本的冲突我需要在yml中加入

``` Java
spring:
  mvc:  
     pathmatch:  
        matching-strategy: ant-path-matcher
```

4 关于几个api的参考的内容
查看连接 参考链接  *https://blog.csdn.net/JokerLJG/article/details/123544934*

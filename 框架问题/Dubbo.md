### Dubbo框架

Dubbo框架 阿里开发
GRPC框架Google开发
TRPC框架腾讯开发

[Dubbo 框架官方文档](https://dubbo.incubator.apache.org/zh/docs3-v2/java-sdk/quick-start/spring-boot/)
Dubbo底层用的是 Triple 协议：[Triple 协议](https://dubbo.incubator.apache.org/zh/docs3-v2/java-sdk/concepts-and-architecture/triple/)

**两种使用方式
1. SpringBoot代码(注解+编程): 写java接口，服务和消费都引用这个接口
2. IDL(接口调用语言):创建一个公共的接口定义的文件，服务提供者和消费者都读取这个文件，有点可以跨语言，所以框架都可以用


**整合运用**

1. backend项目作为服务提供者，有三个方法

2. 
    1. 实际情况应该是去数据库中查是否已分配给用户
    2. 从数据库中查询模拟接口是否存在，以及请求方法是否匹配（还可以校验请求参数） 
    3. 调用成功，接口调用次数 + 1 invokeCount


#### Nacos


##### 安装配置


进入解压后的bin目录

``` bash
D:\Program Files\javapz\nacos\bin>startup.cmd -m standalone
```

然后cmd 

``` bash
# Linux/Unix/Mac
sh startup.sh -m standalone

# ubuntu
bash startup.sh -m standalone

# Windows
startup.cmd -m standalone
```


##### 2.2 整合Nacos 

1. 引入依赖
 
``` xml
<dependency>
  <groupId>org.apache.dubbo</groupId>
  <artifactId>dubbo</artifactId>
  <version>3.0.9</version>
</dependency>
<dependency>
  <groupId>com.alibaba.nacos</groupId>
  <artifactId>nacos-client</artifactId>
  <version>2.1.0</version>
</dependency>

```

2. 配置信息

```yml
# 以下配置指定了应用的名称、使用的协议（Dubbo）、注册中心的类型（Nacos）和地址
dubbo:
  application:
    # 设置应用的名称
    name: dubbo-springboot-demo-provider
  # 指定使用 Dubbo 协议，且端口设置为 -1，表示随机分配可用端口
  protocol:
    name: dubbo
    port: -1
  registry:
    # 配置注册中心为 Nacos，使用的地址是 nacos://localhost:8848
    id: nacos-registry
    address: nacos://localhost:8848

```

3. 模拟代码

  *提供者*
```java

package com.yupi.project.provider;  
  
import java.util.concurrent.CompletableFuture;  
// 接口
public interface DemoService {  
  
    String sayHello(String name);  
  
    String sayHello2(String name);  
  
    default CompletableFuture<String> sayHelloAsync(String name) {  
        return CompletableFuture.completedFuture(sayHello(name));  
    }  
  
}
```



##### 遇到问题没有找到提供者可能是主类的注解没有进行编写
没有配置注解

```java
@EnableDubbo
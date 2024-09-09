
## 前端

###  Table问题

####  1 为什么我点击我的删除表格就选择所有的内容了

``` TS
rowSelection={{  
  defaultSelectedRowKeys: [1],  
}}  
tableAlertRender={({  
                     selectedRowKeys,  
                     selectedRows,  
                     onCleanSelected,  
                   }) => {  
  console.log(selectedRowKeys, selectedRows);  
  return (  
    <Space size={24}>  
    <span>      已选 {selectedRowKeys.length} 项  
      <a style={{ marginInlineStart: 8 }} onClick={onCleanSelected}>  
        取消选择  
      </a>  
    </span>    </Space>  );  
}}  
tableAlertOptionRender={() => {  
  return (  
    <Space size={16}>  
      <a>批量删除</a>  
      <a>导出数据</a>  
    </Space>  );  
}}  
toolBarRender={() => [  
  <Button  
    type="primary"  
    key="primary"  
    onClick={() => {  
      setCreateModalVisible(true);  
    }}  
  >  
    <PlusOutlined /> 新建  
  </Button>,  
]}
```

*答案: 是因为 rowKey 要选择唯一的标识标志 rowKey作为row的唯一标识，需要进行唯一的标识*
更改 rowKey = {id} 进行唯一的标识


#### 2 遍历这个数组
``` TS
selectRowDelete?.map(item => item.id); 遍历这个数组

// 结果 
selectRowDelete?.forEach(item => { console.log(item.id); // 输出每个元素的 id // 你也可以在这里执行其他操作 });
```
#### 4 根据record的状态来判断是否显示
``` ts
render: (_, record) => (  
  <Space size="middle">  
    <Typography.Link      onClick={() => {  
        setCurrentRow(record);  
        setUpdateModalVisible(true);  
      }}  
    >  
      修改  
    </Typography.Link>  
    <Typography.Link type="danger" onClick={() => handleDelete(record)}>  
      删除  
    </Typography.Link>  
    {record.status === 0 && (  
      <Typography.Link type="secondary" onClick={() => handleOnLine(record)}>  
        发布  
      </Typography.Link>  
    )}  
    {record.status === 1 && (  
      <Typography.Link type="secondary" onClick={() => handleOnLine(record)}>  
        下线  
      </Typography.Link>  
    )}  
  </Space>  
),
```
## 后端

### 1 获取Swagger文档 在Applicantion中配置

``` java
public static void main(String[] args) throws UnknownHostException {  
    ConfigurableApplicationContext application = SpringApplication.run(MyApplication.class, args);  
  
    Environment environment = application.getEnvironment();  
    // 获取本地Ip  
    String ip = InetAddress.getLocalHost().getHostAddress();  
    // 获取本地端口  
    String port = environment.getProperty("server.port");  
    // 获取本地路径  
    String property = environment.getProperty("server.servlet.context-path");  
    String path = StringUtils.isBlank(property) ? "" : property;  
    log.info("\n----------------------------------------------------------\n\t" +  
            "Local: \t\thttp://localhost:" + port + path + "/\n\t" +  
            "Swagger接口文档: \thttp://" + ip + ":" + port + path + "/doc.html\n" +  
            "----------------------------------------------------------");  

}
```



### 2 快捷方法

 1. 按[Ctrl+Alt+O]自动删除未使用的导入语句
 2. 下载generallgetSet 插件 使用[ALT+ENTER]


### 3 生成openAi 的内容在哪里
*在config.ts* 中


``` ts
openAPI: [  
  {  
    requestLibPath: "import { request } from '@umijs/max'",
    // 所请求的swagger路径  
    schemaPath: 'http://localhost:7529/api/v3/api-docs',  
    // 所请求的projectName
    projectName: 'apizyq-backend',  
  },  
],
```


### 4 问题
#### 1 前端在传递pageSize和current的时候乱码以及...的作用

`...params` 是 JavaScript（特别是ES6及更高版本）中的一种语法，称为“扩展运算符”（spread operator）。它用于展开对象或数组中的元素。这种语法在多种场景下非常有用，特别是在函数调用、数组和对象操作中。


``` ts 
const params = {
  current: 1,
  pageSize: 10
};

const apiRequest = {
  url: '/api/interfaceInfo/list/page',
  method: 'GET',
  params: { ...params }
};

```

这里的 `{ ...params }` 语法会将 `params` 对象的所有属性展开到新的对象中，所以 `params` 对象的内容（即 `current: 1, pageSize: 10`）会被添加到 `apiRequest.params` 中。

![[Pasted image 20240830104738.png]]
$http://localhost:7529/api/interfaceInfo/list/page?params=%7B%22current%22:1,%22pageSize%22:10%7D]


```ts
http://localhost:7529/api/interfaceInfo/list/page?params=%7B%22current%22:1,%22pageSize%22:10%7D
// 实际的格式应该为
  
http://localhost:7529/api/user/list/page?current=2&pageSize=1
// 主要是因为 在传递参数的时候没有进行...   ...params,  主要是这个params参数

request={async (params, sort, filter) => {  
  const sortField = Object.keys(sort)?.[0];  
  const sortOrder = sort?.[sortField] ?? undefined;  
  const { data, code } = await listInterfaceInfoByPageUsingGet({  
    ...params,  
    sortField,  
    sortOrder,  
    ...filter,  
  } as API.InterfaceInfoQueryRequest);  
  return {  
    success: code === 0,  
    data: data?.records || [],  
    total: Number(data?.total) || 0,  
  };  
}}  
columns={columns}
```


##### 我在使用封装token的时候会遇到两个不同网站塞入Token之后会出现不同账号搜索内容出现紊乱

```ts
// 以前使用的是localStorage 会出现登陆的时候用户会有问题，
// 后面使用的是SessionStorage
```

`sessionStorage` 是浏览器提供的一个用于存储会话级别数据的对象。与 `localStorage` 类似，`sessionStorage` 允许你在客户端存储数据，但有以下几个关键区别：

- **作用域**：`sessionStorage` 的数据仅在页面会话期间可用，也就是说数据在每个标签页或窗口中是独立的。关闭标签页或浏览器窗口后，这些数据会被清除。
- **生命周期**：`sessionStorage` 中的数据在页面会话期间始终存在，页面刷新不会清除数据，但关闭标签页或窗口时数据会被清除。

```ts
// 基本用法
// 存储一个键值对
sessionStorage.setItem('key', 'value');
// 也可以直接通过属性设置值
sessionStorage.key = 'value';

// 通过键名获取值
const value = sessionStorage.getItem('key');

// 或者通过属性访问
const value = sessionStorage.key;
// 通过键名删除特定的数据
sessionStorage.removeItem('key');

// 清除所有数据
sessionStorage.clear();
// 获取长度
const length = sessionStorage.length;
// 通过索引获取键名
const keyName = sessionStorage.key(index);
```

#### 2 在接入SDK之后一直报错，就是大致意思就是Bean没有注入成功

```
org.springframework.beans.factory.UnsatisfiedDependencyException: Error creating bean with name 'com.yue.yueinterface.ApiDemoApplicationTests': Unsatisfied dependency expressed through field 'yueApiClient': No qualifying bean of type 'com.yue.yueclientsdk.client.YueApiClient' available: expected at least 1 bean which qualifies as autowire candidate. Dependency annotations: {@org.springframework.beans.factory.annotation.Autowired(required=true)}
```

解决办法 ：

xxxInterfaceApplication启动类上@EnableConfigurationProperties(xxxApiClientConfig.class)


*@ComponentScan不会自动创建带有 @ConfigurationProperties注解的类的 bean，和鱼总这个不一样。或者hutool的版本比鱼总的新不少，从而导致@Data影响了bean的创建？



### 5 HTTP调用接口的方式
- HttpClient
- RestTemplate
- 第三方库(OKHttp、Hutool)


官方文档
hutool
https://hutool.cn/docs/#/
http客户端工具类 httpUtil
https://hutool.cn/docs/#/http/Http%E5%AE%A2%E6%88%B7%E7%AB%AF%E5%B7%A5%E5%85%B7%E7%B1%BB-HttpUtil



### 总结
#### 开发SDK

##### 1 使用starter的好处

还记得我们使用 starter 之后，我们有哪些好处？比如，对于 Redis 的 starter，我们可以直接在 application.yml 配置文件中进行相关配置。我们可以在配置文件中简单地定义一个连接到 Redis 的配置块，或者对于 Swagger 接口文档，我们也可以在配置文件中进行相应的配置。这样做的好处是，我们无需手动编写繁琐的配置代码或者创建客户端实例。通过引入适当的 starter，我们就可以直接使用它们提供的代码和客户端。只需在配置文件中进行简单的配置，整个过程就自动完成了。

### AOP切面

*问题 *: 每个接口的方法都写调用次数 + 1 是比较麻烦的
同时: 接口开发者需要自己去添加统计代码*


*作用: *
AOP的优点： 独立于每个接口，在每个接口调用后都统计次数 +1
缺点: 存在于单个项目中，每个团队都要开发自己的模拟接口都要写切面
1. 可以在调用每个接口的前或者后，帮助我们执行一些额外的操作，比如统计调用的次数，本质的底层是动态代理。也就是在调用每个接口之后，为接口添加一段额外的代码逻辑。
2. 因为我们的系统架构是放在多个项目之间的，所以因为AOP的缺点独立在单个项目之间，每个项目都需要实现AOP的切面所以我们采用网关来实现这个功能。


步骤
1. 创建aop包
2. 创建InvokeCountAOP.java
``` java
 public void doInvokeCount(){


   // 伪代码
   private UserInterfaceInfoService userInterfaceInfoService;
   public void doInvokeCount(){
        // 调用方法
        object.proceed();
        // 调用成功后 次数+1
        userInterfaceInfoService.invokeCount();
   }
 }
```


### 网关

#### 定义
##### 什么是网关: 
- 理解成火车站的检票口，统一去检票
- 作用: 统一的去进行一些操作，处理这些问题
##### 路由 
- 起到转发的作用 比如有接口A和接口B，网关会记录这些信息，根据用户访问的地址和参数，转发到相应的接口(服务器和集群)
参考文档 
https://docs.spring.io/spring-cloud-gateway/docs/current/reference/html/#the-after-route-predicate-factory

##### 负载均衡
- 在路由的基础上

##### 统一跨域处理
- 网关统一处理跨域，不在每个项目里面单独处理
- 参考文档：[Global CORS Configuration](https://docs.spring.io/spring-cloud-gateway/docs/current/reference/html/#global-cors-configuration)
##### 发布控制
- 灰度分布，比如新上线的接口，先给接口20%的流量，老接口80%流量，再慢慢调整比例。
- 参考文档 [The Weight Route Predicate Factory](https://docs.spring.io/spring-cloud-gateway/docs/current/reference/html/#the-weight-route-predicate-factory)。
##### 流量灰色
- 给请求流量添加一些标识，一般是设置在请求头中，添加新的请求头
- 参考文档 :[TheAddRequestHeaderGatewayFilterFactory](https://docs.spring.io/spring-cloud-gateway/docs/current/reference/html/#the-addrequestheader-gatewayfilter-factory)
- 全局染色 : [Default Filters](https://docs.spring.io/spring-cloud-gateway/docs/current/reference/html/#default-filters)

##### 统一的接口保护
1.  限制请求：[requestheadersize-gatewayfilter-factory](https://docs.spring.io/spring-cloud-gateway/docs/current/reference/html/#requestheadersize-gatewayfilter-factory)。
2. 信息脱敏：[the-removerequestheader-gatewayfilter-factory](https://docs.spring.io/spring-cloud-gateway/docs/current/reference/html/#the-removerequestheader-gatewayfilter-factory)
3. 降级（熔断）：[fallback-headers](https://docs.spring.io/spring-cloud-gateway/docs/current/reference/html/#fallback-headers)。
4. 限流：[the-requestratelimiter-gatewayfilter-factory](https://docs.spring.io/spring-cloud-gateway/docs/current/reference/html/#the-requestratelimiter-gatewayfilter-factory)。
5. 超时时间：[http-timeouts-configuration](https://docs.spring.io/spring-cloud-gateway/docs/current/reference/html/#http-timeouts-configuration)
6. 重试（业务保护）：[the-retry-gatewayfilter-factory](https://docs.spring.io/spring-cloud-gateway/docs/current/reference/html/#the-retry-gatewayfilter-factory)

##### 统一的业务处理
- 把一些每个项目中都要做的通用逻辑放到上层(网关)，进行统一的处理。
##### 统一的鉴权处理
- 判断用户是否进行权限进行操作，无论什么接口，我都去统一的判断权限不重复写。
##### 访问控制
- 黑白名单 比如限制DDOS IP。
##### 统一日志
- 统一的请求、相应信息记录。
##### 统一文档
- 将下游项目的文档进行聚合，在一个项目中统一查看查看。





#### 2 分类讲解
##### 2.1 全局网关(接入层网关) : 
- 作用是进行负载均衡，请求日志，不和业务逻辑绑定。
- 关注请求的本身，主要用于负载均衡。在大多数情况下并不涉及负责的业务逻辑。
##### 2.2 业务网关(微服务网关)
- 会有一些业务逻辑，作用是将请求转发到不同的业务/项目/接口/服务
- 业务网关通常位于多个项目或者微服务之上，负责根据用户的请求转发到不同的业务之上。

#### 3 实现讲解
- Nginx(全局网关)、kong网关(API网关、kong) 编程成本相对高一点
- Spring Cloud GateWay 性能高，可以用java代码来编程适用于学习。

#### 4 核心概念
![[Pasted image 20240905160901.png]]



**路由** (根据什么条件，转到请求到哪里)
*断言* ： 一组规则
*过滤器* 对这请求加上一些列的处理，比如添加请求头，添加请求参数

**请求流程**
1. 客户端发起请求
2. HandleMapping: 根据断言，去将请求转发到对应的路由
3. WebHandler: 处理请求 (一层层经过过滤器)
4. 实际调用服务


**两种配置方式**
1.  配置式 (方便、规范、推荐)
2.  简化版
3.  全程版本
4.  编程式(灵活，相对麻烦)

**断言**
1. After在xx时间以后
2. Before在xx时间之前
3. BetWeen在xx时间之间
4. 请求类别
5. 请求头(包含cookie)
6. 查询参数
7. 客户端地址
8. 权重

**过滤器**
基本功能:  对请求头。请求参数。响应头的增删改查

1. 添加请求头
2. 添加请求参数
3. 添加相应头
4. 降级
5. 限流
6. 重试


#### 5 实例


##### 5.1 拦截器
主要作用: 对请求进行处理，主要是对请求进行修改和相应等操作

`AddRequestHeader ` : 添加请求头
`AddRequestParameter` : 添加请求参数
`AddResoponseHeader` : 添加响应头




##### 5.1 实现请求转发

比如接口地址是以: `http:// localhost:8123/api` 开头的，可以进行前缀匹配路由器使得所有路径开头为`api/**` 的都被匹配到，并且转发到相同的路径上面去。

**前缀匹配断言**


```yaml
spring:
  cloud:
    gateway:
      routes:
      - id: path_route
        uri: https://example.org
        predicates:
        - Path=/red/{segment},/blue/{segment}

```

*也就是访问8090的时候会自动转发到8123的路径前提是*
```YAML
spring:  
  cloud:  
    gateway:  
      default-filters:  
        - AddResponseHeader=source, yupi  
      routes:  
        # 定义了一个名为"api_route"的路由规则,该规则将匹配以"/api/"开头的路径,例如"/api/user",  
        # 并将这些请求转发到"http://localhost:8123"这个目标地址  
        - id: api_route  
          uri: http://localhost:8123  
          predicates:  
            - Path=/api/**
        - 
```


##### 5.2 添加全局过滤器

作用: 是可以自己定义对每个请求的规则，对所有经过网关的请求都执行相同的逻辑


```java
public class CustomGlobalFilter implements GlobalFilter, Ordered {

    @Override
    public Mono<Void> filter(ServerWebExchange exchange, GatewayFilterChain chain) {
        log.info("custom global filter");
        return chain.filter(exchange);
    }

    @Override
    public int getOrder() {
        return -1;
    }
}

```

**实现了GlobalFilter和Ordered接口**

order: 表示一个执行的顺序
也就是Spring Cloud GateWay 请求到真实的接口会经过多个过滤器，通过Ordered可以编制过滤器的优先级，确定哪个先拦截，哪个后拦截。

![[Pasted image 20240906094956.png]]


官方的文档一个叫 filter 的方法，这里还有一个日志，我们所有的业务逻辑都可以写在这个 filter 里



##### 5.3 编写业务逻辑
###### 5.3.1 请求日志
请求的日志要怎么拿到
比如我需要*输出请求地址、输出请求参数*
通过这里可以拿request对象
然后从Request里面拿到它的请求地址。请求参数、请求头之类的。

Spring Clound GateWay也可以这样做

- exchange 路由交换机所有的请求的信息，响应的信息，响应体。请求头都可以拿到
- chain 责任链模式，请求的所有过滤器是由上到下顺序依次执行，形成一个链条，所以用到了一个过滤器，也就是filter有时候我们需要在责任链中使用 next，而在这里它使用了 filter 来找到下一个过滤器，从而正常地放行请求


```java
ServerHttpRequest request = exchange.getRequest();
log.info("请求的唯一标识:" + request.getId());  
log.info("请求路径:" +request.getPath().value());  
log.info("请求方法:"+ request.getMethod());  
log.info("请求参数:"+ request.getQueryParams());  
String sourceAddress = request.getLocalAddress().getHostString();  
log.info("请求来源地址:" + sourceAddress);  
log.info("请求远程地址:+" +request.getRemoteAddress());
```
*获取request *
可以拿到id ，path.value Method, QueryParams RemoteAddress. 可以拿到好多信息


###### 5.3.2 黑白名单
通常情况下面都是某个远程ip频繁访问，就添加到黑名单来拒绝访问，现在可以试试这个规则请求的来源地址不是本地就拒绝访问

```
private static final List<String> IP_WHITE_LIST = Array.asList("127.0.0.1);
```

如果不是指定的地址就拒绝访问 **但是怎么拒绝**

```java
ServerHttpResponse = exchage.getResponse();
if(!IP_WHITE_LIST.contains(sourceAddress)){
   response.setStatusCode (HttpStatus.FORBIDDEN);
   return respose.setComplete();
}
```


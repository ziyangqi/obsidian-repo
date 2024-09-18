

##### 1 为什么SpringGateway的过滤器中使用writewith()重写响应体时会失效？


``` java
package com.yupi.zyqapigateway;  
import com.zyq.zyqapiclientsdk.utils.SignUtils;  
import com.zyq.zyqapicommon.model.entity.InterfaceInfo;  
import com.zyq.zyqapicommon.model.entity.User;  
import com.zyq.zyqapicommon.service.InnerInterfaceInfoService;  
import com.zyq.zyqapicommon.service.InnerUserInterfaceInfoService;  
import com.zyq.zyqapicommon.service.InnerUserService;  
import jakarta.annotation.Resource;  
import lombok.extern.slf4j.Slf4j;  
import org.apache.dubbo.config.annotation.DubboReference;  
import org.reactivestreams.Publisher;  
import org.springframework.cloud.gateway.filter.GatewayFilterChain;  
import org.springframework.cloud.gateway.filter.GlobalFilter;  
import org.springframework.core.Ordered;  
import org.springframework.core.io.buffer.DataBuffer;  
import org.springframework.core.io.buffer.DataBufferFactory;  
import org.springframework.core.io.buffer.DataBufferUtils;  
import org.springframework.http.HttpHeaders;  
import org.springframework.http.HttpStatus;  
import org.springframework.http.HttpStatusCode;  
import org.springframework.http.server.reactive.ServerHttpRequest;  
import org.springframework.http.server.reactive.ServerHttpResponse;  
import org.springframework.http.server.reactive.ServerHttpResponseDecorator;  
import org.springframework.stereotype.Component;  
import org.springframework.web.server.ServerWebExchange;  
import reactor.core.publisher.Flux;  
import reactor.core.publisher.Mono;  
  
import java.nio.charset.StandardCharsets;  
import java.util.ArrayList;  
import java.util.Arrays;  
import java.util.List;  
  
  
/**  
 * 添加全局过滤器  
 */  
@Slf4j  
@Component  
public class CustomGlobalFilter implements GlobalFilter, Ordered {  
  
    // 建立访问白名单  
    private static final List<String> IP_WHITE_LIST = Arrays.asList("127.0.0.1");  
    // 这里的注解不能用Resource 只能使用DubboReference  
    @DubboReference  
    private InnerInterfaceInfoService interfaceInfoService;  
  
    @DubboReference  
    private InnerUserService innerUserService;  
  
    @DubboReference  
    private InnerUserInterfaceInfoService innerUserInterfaceInfoService;  
  
  
  
    @Override  
    public Mono<Void> filter(ServerWebExchange exchange, GatewayFilterChain chain) {  
        // 1 请求日志  
        ServerHttpRequest request = exchange.getRequest();  
        String path = request.getPath().value();  
        String method = request.getMethod().toString();  
        log.info("请求的唯一标识:" + request.getId());  
        log.info("请求路径:" +path);  
        log.info("请求方法:"+method);  
        log.info("请求参数:"+ request.getQueryParams());  
        String sourceAddress = request.getLocalAddress().getHostString();  
        log.info("请求来源地址:" + sourceAddress);  
        log.info("请求远程地址:+" +request.getRemoteAddress());  
        // 2 黑白名单 todo 后面可以做成去配置文件读取的格式  
         ServerHttpResponse  response = exchange.getResponse();  
        if (!IP_WHITE_LIST.contains(sourceAddress)){  
            response.setStatusCode(HttpStatus.FORBIDDEN);  
            return response.setComplete();  
        }  
        // 用户鉴权  
        // 从请求头中获取参数  
        HttpHeaders headers = request.getHeaders();  
        String accessKey = headers.getFirst("accessKey");  
        String nonce = headers.getFirst("nonce");  
        String timestamp = headers.getFirst("timestamp");  
        String sign = headers.getFirst("sign");  
        String body = headers.getFirst("body");  
  
        User invokeUser = null;  
  
        try{  
            invokeUser = innerUserService.getInvokeUser(accessKey);  
        }catch (Exception e){  
            // 抛出异常记录日志  
            log.error("getInvokeUser Error");  
        }  
  
        // 直接校验如果随机数大于1万，则抛出异常，并提示"无权限"  
        if (Long.parseLong(nonce) > 10000) {  
            return handleNoAuth(response);  
        }  
  
        // todo 时间和当前时间不能超过5分钟  
        // System.currentTimeMillis()返回当前时间的毫秒数，除以1000后得到当前时间的秒数。  
        Long currentTime = System.currentTimeMillis() / 1000;  
        // 定义一个常量FIVE_MINUTES,表示五分钟的时间间隔(乘以60,将分钟转换为秒,得到五分钟的时间间隔)。  
        final Long FIVE_MINUTES = 60 * 5L;  
        // 判断当前时间与传入的时间戳是否相差五分钟或以上  
        // Long.parseLong(timestamp)将传入的时间戳转换成长整型  
        // 然后计算当前时间与传入时间戳之间的差值(以秒为单位),如果差值大于等于五分钟,则返回true,否则返回false  
        if ((currentTime - Long.parseLong(timestamp)) >= FIVE_MINUTES) {  
            // 如果时间戳与当前时间相差五分钟或以上，调用handleNoAuth(response)方法进行处理  
            return handleNoAuth(response);  
        }  
  
        // 这里获取SecretKey  
        String secretKey = invokeUser.getSecretKey();  
        // todo 实际情况中是从数据库中查出 secretKey        String serverSign = SignUtils.genSign(body, secretKey);  
  
        // 如果生成的签名不一致，则抛出异常，并提示"无权限"  
        if ( sign==null || !sign.equals(serverSign)) {  
            return handleNoAuth(response);  
        }  
        // chain.filter 是一个异步的操作*解决方案：利用 response 装饰者，增强原有 response 的处理能力  
        // 4 请求模拟接口是否存在  
        InterfaceInfo interfaceInfo = interfaceInfoService.getInterfaceInfo(path, method);  
        if (interfaceInfo == null){  
            return handleNoAuth(response);  
        }  
        // 5 请求转发调用模拟接口  
        // 调用成功后输入一个响应的日志  
        log.info("响应" + response.getStatusCode());  
        // 调用失败返回一个失败的错误码  
        return handleResponse(exchange , chain , interfaceInfo.getId() , invokeUser.getId());  
    }  
  
  
    @Override  
    public int getOrder() {  
        return -1;  
    }  
  
    public Mono<Void> handleNoAuth(ServerHttpResponse response){  
        response.setStatusCode(HttpStatus.FORBIDDEN);  
        return response.setComplete();  
    }  
  
    public Mono<Void> handleInvokeErroe(ServerHttpResponse response){  
        response.setStatusCode(HttpStatus.INTERNAL_SERVER_ERROR);  
        return response.setComplete();  
    }  
  
  
    /**  
     * 处理相应  
     * @param exchange  
     * @param chain  
     * @return  
     */  
    public Mono<Void> handleResponse(ServerWebExchange exchange,GatewayFilterChain chain,long interfaceId, long userId){  
        try{  
            //获得原始的相应对象  
            ServerHttpResponse originalResponse = exchange.getResponse();  
            // 获得数据缓冲工厂  
            DataBufferFactory dataBufferFactory = originalResponse.bufferFactory();  
            // 获取相应的状态码  
            HttpStatusCode statusCode = originalResponse.getStatusCode();  
            // 如果状态码为0k也就是可以正常的访问  
            if (statusCode == HttpStatus.OK){  
                // 创建一个装饰后的相应对象(开始增强能力)  
                ServerHttpResponseDecorator decoratorResponse = new ServerHttpResponseDecorator(originalResponse){  
                    // 重写writeWith方法，处理响应体的数据  
                    // 这段方法就是当我们的模拟接口调用完成之后，等他返回结果  
                    // 就会调用writeWith方法，我们就能根据相应结果做一些自己的处理  
                    @Override  
                    public Mono<Void> writeWith(Publisher<? extends DataBuffer> body){  
                        log.info("body instanceof Flux: {}", (body instanceof Flux));  
                        // 判断响应体是否是Flux类型  
                        if (body instanceof Flux){  
                            Flux<? extends DataBuffer> fluxBody = Flux.from(body);  
                            // 返回一个处理后的响应体  
                            // 这里就理解为它在拼装字符串，它把缓冲区的数据提取出来，一点一点的拼接好  
                            return super.writeWith(fluxBody.map(dataBuffer -> {  
                                try{  
                                    innerUserInterfaceInfoService.invokeCount(interfaceId,userId);  
                                }  
                                catch (Exception e){  
                                    log.error("InvokeCount Error",e);  
                                }  
                                // 读取响应体的内容转化为字节数组  
                                byte[] content = new byte[dataBuffer.readableByteCount()];  
                                dataBuffer.read(content);  
                                // 释放内存  
                                DataBufferUtils.release(dataBuffer);  
                                // 构建日志  
                                StringBuilder sb2 = new StringBuilder(200);  
                                sb2.append("<--- {} {} \n");  
                                List<Object> rspArgs = new ArrayList<>();  
                                rspArgs.add(originalResponse.getStatusCode());  
                                // data  
                                String data = new String(content, StandardCharsets.UTF_8);  
                                sb2.append(data);  
                                log.info(sb2.toString(), rspArgs.toArray());//log.info("<-- {} {}\n", originalResponse.getStatusCode(), data);  
                                return dataBufferFactory.wrap(content);  
                            }));  
                        }else{  
                            log.error("<--- {} 响应code异常", getStatusCode());  
                        }  
                        return super.writeWith(body);  
                    }  
                };  
                // 对于200ok的请求，将装饰后的相应对象传递给下一个过滤器，并继续处理(设置response装饰过得)  
                return chain.filter(exchange.mutate().response(decoratorResponse).build());  
            }  
            // 对于非200的请求，直接返回降级处理  
            return chain.filter(exchange);  
        }catch (Exception e){  
            log.error("gateWay log exception.\n"+ e);  
            return chain.filter(exchange);  
        }  
    }  
}
```



罪魁祸首是getOrder
``` java
  @Override  
    public int getOrder() {  
        return -1;  

```

```java
public Mono<Void> filter(ServerWebExchange exchange, GatewayFilterChain chain)
// 这个方法是异步的所以不会立即执行，应式编程就是这里Mono这个例子

```

*因为Mono VoidhandleResponse* 是异步的
反应式编程和命令式编程是两种方式
命令式编程就是我们常见的，一行一行代码执行下来，最后输出结果
反应式编程就是这里Mono这个例子
chain.filter(exchange) 返回的是一个 MonoVoid，
这意味着它会异步执行，而不是立即执行。因此，后面的日志打印和数据库操作会在 chain.filter(exchange) 被调用后立即执行，而不等待过滤器链的完成
换句话说就是执行chain.filter(exchange)这个方法太慢了，你这一个进程就一直堵在这里，后面的操作都没法执行了，然后程序就先执行下面的打印日志操作了。

所以才会进行数据增强

利用到了Flux这个东西提前把请求给完成了，我们才能继续执行后面写日志的操作


##### 在 Spring Cloud Gateway 中，getOrder详解

在 Spring Cloud Gateway 中，`GlobalFilter` 是用于过滤和处理 HTTP 请求的过滤器接口，而 `Ordered` 接口允许你定义多个过滤器之间的执行顺序。

关于 `getOrder()` 的执行顺序：

- **值越小，优先级越高**：Spring 会按照`getOrder()`的返回值进行排序，值越小的过滤器优先执行。
- **`getOrder()` 返回 0**：如果多个过滤器的 `getOrder()` 返回的值相同，它们的执行顺序将取决于 Spring 内部的具体执行机制，可能会导致顺序不稳定。
- **`getOrder()` 返回负数（如 -1）**：比 0 更高的优先级，这样该过滤器会在返回 0 的过滤器之前执行。
- **顺序冲突**： 如果你的过滤器 `getOrder()` 返回 0，而系统中有多个过滤器也返回 0，则它们之间的执行顺序可能不确定。Spring Cloud Gateway 在同一个优先级中（`getOrder()`返回相同值的过滤器）不保证执行顺序，这可能导致你的过滤器在意料之外的时机执行。

- **系统默认过滤器的顺序**： Spring Cloud Gateway 和 Spring 本身有一些默认的过滤器，它们可能设置了与 0 相同的执行优先级。如果你不希望你的过滤器与这些默认的过滤器同时执行，那么应该选择一个更高或更低的优先级来避免冲突。



###### Spring 内置的过滤器

Spring Cloud Gateway 内置了一些全局过滤器，每个过滤器都有自己的默认顺序。以下是一些常见的内置过滤器及其默认顺序：

- **`NettyWriteResponseFilter`**：`getOrder()` 返回 -1。它是负责将响应写回到客户端的最后一个过滤器。
- **`GatewayMetricsFilter`**：`getOrder()` 返回 0。用于收集网关性能指标。
- **`WebsocketRoutingFilter`**：`getOrder()` 返回 1。处理 WebSocket 请求的过滤器。
- **`RoutingFilter`**：`getOrder()` 返回 2147483647（`Integer.MAX_VALUE`）。处理实际路由请求的过滤器。

默认过滤器顺序

如果你自定义了过滤器并实现了 `Ordered` 接口，但没有重写 `getOrder()` 方法，默认的优先级取决于 `Ordered` 接口的实现。

- 如果没有实现 `Ordered` 接口，Spring 框架会将它视为 **无优先级** 的过滤器，其执行顺序将由 Spring 框架根据其他实现的 `getOrder()` 方法决定。

**未实现 `Ordered` 接口的情况**

如果你定义的过滤器没有实现 `Ordered` 接口，过滤器的顺序将无法由你直接控制，Spring Cloud Gateway 会将它放置在一个默认的顺序中，这通常是较低优先级的顺序。

 **如何查看某个过滤器的默认 `getOrder()` 值？**

如果你想了解某个具体过滤器的默认执行顺序，可以查看对应过滤器的源码，寻找 `getOrder()` 方法的实现。例如：

java

复制代码

`@Override public int getOrder() {     return 0;  // 默认顺序为 0 }`

##### 自定义过滤器顺序

通过实现 `Ordered` 接口，你可以自定义过滤器的执行顺序。例如：

java

复制代码

``` java

public class CustomGlobalFilter implements GlobalFilter, Ordered {

    @Override
    public int getOrder() {
        return -1;  // 设置为 -1 保证优先执行
    }

    @Override
    public Mono<Void> filter(ServerWebExchange exchange, GatewayFilterChain chain) {
        // 自定义过滤器逻辑
        return chain.filter(exchange);
    }
}

```


- **内置过滤器的顺序** 是预先定义好的，常见的过滤器例如 `NettyWriteResponseFilter` 为 -1，`RoutingFilter` 为最大整数值（`Integer.MAX_VALUE`）。
- 如果你**自定义过滤器**且实现了 `Ordered` 接口，顺序是你自己定义的；如果没有实现 `Ordered`，它将没有明确的顺序。
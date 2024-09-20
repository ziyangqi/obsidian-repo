

参考连接: https://open.bigmodel.cn/dev/api/devguide/sdk-install
### 安装JavaSDK

``` java
<dependency>
     <groupId>cn.bigmodel.openapi</groupId> 
     <artifactId>oapi-java-sdk</artifactId> 
     <version>release-V4-2.3.0</version>
</dependency>
```


### 我想每次只在bean启动的时候进行new这个实例，避免浪费
``` java

public class AiZhiPuManager {  
  
    @Autowired  
    private ClientServiceUtils clientServiceUtils;  
  
  
    public String testInvoke(String context) {  
        List<ChatMessage> messages = new ArrayList<>();  
        ChatMessage chatMessage = new ChatMessage(ChatMessageRole.USER.value(), context);  
        messages.add(chatMessage);  
        String requestIdTemplate = "";  
        String requestId = String.format(requestIdTemplate, System.currentTimeMillis());  
        System.out.println(requestId);  
        ObjectMapper mapper = new ObjectMapper();  
        ChatCompletionRequest chatCompletionRequest = ChatCompletionRequest.builder()  
                .model(Constants.ModelChatGLM4)  
                .stream(Boolean.FALSE)  
                .invokeMethod(Constants.invokeMethod)  
                .messages(messages)  
                .build();  
  
        ClientV4 client = clientServiceUtils.getClient(); // 使用 getClient() 获取 client 实例  
        ModelApiResponse invokeModelApiResp = client.invokeModelApi(chatCompletionRequest);  
        try {  
            return mapper.writeValueAsString(invokeModelApiResp);  
        } catch (JsonProcessingException e) {  
            e.printStackTrace();  
            return e.getMessage();  
        }  
    }  
}


@Component  
public class ClientServiceUtils {  
  
    private ClientV4 client;  
  
    // 注入 ZhiPuAiConfig 配置类  
    private final ZhiPuAiConfig zhiPuAiConfig;  
  
    @Autowired  
    public ClientServiceUtils(ZhiPuAiConfig zhiPuAiConfig) {  
        this.zhiPuAiConfig = zhiPuAiConfig;  
    }  
  
    // 获取 ClientV4 实例，确保只初始化一次  
    public ClientV4 getClient() {  
        if (client == null) {  
            String key = zhiPuAiConfig.getKey(); // 通过依赖注入获取 key            client = new ClientV4.Builder(key).build();  
        }  
        return client;  
    }  
}


```




### OkHttpClient 怎么设置超时的时间
```
```



### ModelApiResponse invokeModelApiResp = client.invokeModelApi(chatCompletionRequest); 调用这个方法一直超时
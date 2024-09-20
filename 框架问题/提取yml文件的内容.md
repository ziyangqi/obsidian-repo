
### 方法 1


可以通过注解的方式将 YAML 文件中的配置直接映射到 Java 类的字段中。Spring Framework 提供了非常方便的方式来使用注解从 YAML 配置文件中获取值，特别是在 Spring Boot 项目中。通常，使用 `@ConfigurationProperties` 或 `@Value` 注解来从 YAML 文件中提取配置。

 
 *使用 `@ConfigurationProperties` 注解


```

```


### 方法  2 使用value注解

``` yml
# yml 内容
zhipu:  
  key: f3b3303267961fd413cf6507cd1df853.VldpXa82vs6EYeUg
```

建立一个类来针对静态的方法获取这个类
``` java
@Component  
public class ZhiPuAiConfig {  
  
    @Value("${zhipu.key}")  
    private String key;  
  
    // 静态变量来保存配置值  
    private static String staticKey;  
  
    // 使用 @PostConstruct 将实例变量赋值给静态变量  
    @PostConstruct  
    public void init() {  
        staticKey = this.key;  
    }  
  
    // 静态方法来获取 key    public static String getKey() {  
        return staticKey;  
    }  
}
```

这里注入这个类并且注入这个类的时候就已经把这个内容注入进去
``` java

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
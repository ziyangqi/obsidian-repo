### 名词

VO:
DTO： dto是前端给后端的内容
V0 : 是视图打交道的内容是后端给前端


### 接口\

获取任务列表（待办）
``` Java
/**  
 
  URL:  a1bpmn/api/runtime/task/v2/list
 * 获取任务列表（待办）  
 * 在TaskResource中
 */  
@GetMapping("/wait/list")
```


### form-data 怎么传送
![[Pasted image 20240809171449.png]]

使用 使用注解来进行传送form-Data的格式
``` Java
@GetMapping(value = "/a1bpmn/api/commu/list" , headers =  "Content-Type=application/x-www-form-urlencoded")  
HashMap<String, Object> getTodoList( @RequestBody CommuReveiverQueryDTO commuReceiverQueryDTO );
```

### 面相切面编程
对于那些Code没有0的情况下全部返回 抛出异常

```Java
@Aspect  
@Component  
public class FeignResponseAspect {  
    @Pointcut("execution(public java.util.HashMap<String, Object> cn.com.zxrail.client.*.*(..))")  
    void point() {}  
    @AfterReturning(pointcut = "point()", returning = "result")  
    public HashMap<String, Object> checkCode(HashMap<String, Object> result) {  
        if(!"0".equals(result.get("code").toString())){  
            throw new CodeException(HttpResultCode.SERVER_FAIL);  
        }  
        return result;  
    }  
}
```

``` Java 
// 获取数据 当前拿到了两次data

@Override  
public List<CommReceiverEntityVO> getTodoTaskReceiver(CommuReveiverQueryDTO commuReveiverQueryDTO) {  
    Map<String, Object> result = taskClient.getTodoList(commuReveiverQueryDTO);  
    // 如果返回的code 不为0，则抛出异常  
    if(!"0".equals(result.get("code").toString())){  
        throw new CodeException(HttpResultCode.SERVER_FAIL);  
    }  
    List<CommReceiverEntityVO> commReceiverEntityVOList = new ArrayList<>();  
    for (Map<String, Object> dataMap : (List<Map<String, Object>>) result.get("data")) {  
        CommReceiverEntityVO commReceiverEntityVO = new CommReceiverEntityVO();  
        // 这里可以添加其他的字段， 当前dataMap已经获取到了全部的data信息，然后获取到全部的内容  
        commReceiverEntityVO.setData((String) dataMap.get("data"));  
        commReceiverEntityVOList.add(commReceiverEntityVO);  
    }  
      
    return commReceiverEntityVOList;  
}
```



### String data1 = taskSpeedResult.get("data").toString(); String 怎么转化为Map？
这个方法目前测试的不太行 ，就是replacement的类型不太一样
``` Java 
ObjectMapper objectMapper = new ObjectMapper();  
Object dataObject = taskSpeedResult.get("data");  
String dataJson = objectMapper.writeValueAsString(dataObject);  
dataJson = dataJson.replace("\\\n", "\n");

```



### 接口驳回驳回接口

*获取下一个节点接口: *   http://127.0.0.1:10086/fastflow-admin/a1bpmn/api/cockpit/process-instance/getNextNode/3106517

在`CockpitResource`中

*获取回退的接口 *http://127.0.0.1:10086/fastflow-admin/a1bpmn/api/runtime/task/v1/getBackNode/3106517


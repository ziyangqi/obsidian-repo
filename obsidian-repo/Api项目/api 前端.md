
### arco design vue3 相关事项

#### 1 部署快速开始

参考链接 ： https://arco.design/vue/docs/pro/start


#### 2 如何和本地的后端进行联调

##### 2.1 在.env.development 中配置后端的接口
``` java
VITE_API_BASE_URL= 'http://localhost:8080'
```

##### 2.2 更改后前端的getUserInfo 找不到
``` Java
#  发现是Session的问题 用户登陆的时候传递了一个Session ，但是getSession的时候确是没有找到
#  最后解决了是跨域的问题
#  参考文档: https://blog.csdn.net/m0_46313726/article/details/129320450

```

A9B5D388737B7378C07CE1FC3DFF0954
67447D5E057EA64C8F65CA23B622540B

##### 2.3  配置文件yml 中配置的内容 后端怎么获取
```java
@CrossOrigin(origins = {"${CrossOriginConfig.url}"}, allowCredentials = "true")
```

``` yml 
# 配置前端跨域的内容以及端口
CrossOriginConfig:
  url: http://localhost:5173 
```

##### 2.4  Arco Pro 加入新的路径新的内容 


##### 2.5 双跨域遇到的问题
![[bfcf0d72c4c8f701e5cfd890a4619af.png]]
#### 3 常见问题

##### 3.1 springboot中如果配置端口

``` shell
server:  
  port: 7529
```


#####  调用GateWay的前端接口要怎么写


##### 3.2 Get 怎么传递参数
``` Java
export async function readByAlreadyGet(  
  body: API.changeAlready,  
  options?: { [key: string]: any },  
) {  
  return request<API.BaseResponseBoolean_>('engine/task/read/', {  
    method: 'Get',  
    headers: {  
      'Content-Type': 'application/json',  
    },  
    data: body,  
    ...(options || {}),  
  });  
}


// 这种
export async function readByAlreadyGet(  
  body: API.changeAlready,  
  options?: { [key: string]: any },  
) {  
  const queryParams = new URLSearchParams(body as any).toString();  
  const url = `engine/task/read/?${queryParams}`;  
  return request<API.BaseResponseBoolean_>(url, {  
    method: 'Get',  
    headers: {  
      'Content-Type': 'application/json',  
    },  
    data: body,  
    ...(options || {}),  
  });  
}

// 两种格式不同的效果
export async function readByAlreadyGet(  
  body: API.changeAlready,  
  options?: { [key: string]: any },  
) {  
  const taskId = body.taskId  
  const url = `engine/task/read/${taskId}`;  
  return request<API.BaseResponseBoolean_>(url, {  
    method: 'Get',  
    headers: {  
      'Content-Type': 'application/json',  
    },  
    data: body,  
    ...(options || {}),  
  });  
}
```

##### 3.3 怎么解析获取的JSON的格式并且把他转化为窗体弹窗

``` JSON

{"list":[{"icon":"icon-input","name":"字段1","options":{"showAlpha":false,"defaultValue":"","remote":false,"required":false,"showLabel":false,"readonly":false,"showInput":false,"rangeAble":false,"border":false,"filterable":false,"forecast":false,"bold":false,"showTitle":true,"arrowControl":false,"fontSize":0,"allowHalf":false,"show":true,"range":false,"disabled":false,"placeholder":"","dateCalculation":false,"relationship":{"filed":"","table":""},"timestamp":false,"clearable":false,"editable":false,"multiple":false,"inline":false,"width":"","showPassword":false,"span":12},"model":"field1","rules":[],"type":"input","key":"field1"},{"icon":"icon-input","name":"字段2","options":{"showAlpha":false,"defaultValue":"","remote":false,"required":false,"showLabel":false,"readonly":false,"showInput":false,"rangeAble":false,"border":false,"filterable":false,"forecast":false,"bold":false,"showTitle":true,"arrowControl":false,"fontSize":0,"allowHalf":false,"show":true,"range":false,"disabled":false,"placeholder":"","dateCalculation":false,"relationship":{"filed":"","table":""},"timestamp":false,"clearable":false,"editable":false,"multiple":false,"inline":false,"width":"","showPassword":false,"span":12},"model":"field2","rules":[],"type":"input","key":"field2"},{"icon":"icon-input","name":"字段3","options":{"showAlpha":false,"defaultValue":"","remote":false,"required":false,"showLabel":false,"readonly":false,"showInput":false,"rangeAble":false,"border":false,"filterable":false,"forecast":false,"bold":false,"showTitle":true,"arrowControl":false,"fontSize":0,"allowHalf":false,"show":true,"range":false,"disabled":false,"placeholder":"","dateCalculation":false,"relationship":{"filed":"","table":""},"timestamp":false,"clearable":false,"editable":false,"multiple":false,"inline":false,"width":"","showPassword":false,"span":12},"model":"field3","rules":[],"type":"input","key":"field3"},{"controlType":"","icon":"icon-input","name":"字段4","options":{"showAlpha":false,"defaultValue":"","remote":false,"required":false,"showLabel":false,"readonly":false,"showInput":false,"rangeAble":false,"border":false,"remoteFunc":"func_1723713590201","filterable":false,"dataType":"string","forecast":false,"bold":false,"unit":"","showTitle":true,"arrowControl":false,"allowHalf":false,"dateCalculationStart":"","pattern":"","show":true,"range":false,"disabled":false,"dateCalcDiffType":"day","placeholder":"","dateCalculation":false,"relationship":{"filed":"","table":""},"timestamp":false,"clearable":false,"editable":false,"multiple":false,"showRed":"false","inline":false,"width":"100%","customClass":"","showPassword":false,"span":12},"model":"input_1723713590201","rules":[],"type":"input","key":"1723713590201"},{"controlType":"","icon":"icon-input","name":"单行","options":{"showAlpha":false,"defaultValue":"","remote":false,"required":false,"showLabel":false,"readonly":false,"showInput":false,"rangeAble":false,"border":false,"filterable":false,"dataType":"string","forecast":false,"bold":false,"unit":"","showTitle":true,"arrowControl":false,"allowHalf":false,"dateCalculationStart":"","pattern":"","show":true,"range":false,"disabled":false,"dateCalcDiffType":"day","placeholder":"","dateCalculation":false,"relationship":{"filed":"","table":""},"timestamp":false,"clearable":false,"editable":false,"multiple":false,"showRed":"false","inline":false,"width":"100%","customClass":"","showPassword":false,"span":12},"model":"textarea_1723713878966","rules":[],"type":"input","key":"1723713878966"}],"config":{"size":"small","labelPosition":"right","formName":"多路流程","labelWidth":100}}

```


``` 对获取表单的数据进行更改
{\"field1\":\"2323\",\"field2\":\"2323\",\"field3\":\"2323\",\"input_1723713590201\":\"2323\",\"textarea_1723713878966\":\"232323\"}
```



获取信息
```
```
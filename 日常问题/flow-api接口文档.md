## GET /flow/list 流程列表
```json
返回
{
  "code": 0,
  "data": [
    {
      "description": "string", //描述
      "href": "string", // 图标src
      "modelKey": "string", // 调用/flow/detail/{modelKey}时传递
      "name": "string" // 流程名称
    }
  ],
  "msg": "string"
}
```
## GET /flow/detail/{modelKey} 流程详情
```json
返回
{
  "code": 0,
  "data": {
    "a1DeModelDetailVO": {
    **"defId": "string", 
      "external": "string",
    **"modelId": "string",
      "sysTypeEntity": {
        "children": [
          null
        ],
        "contextmenu": true,
        "createTime": "string",
        "expand": true,
        "id": 0,
        "leaf": true,
        "orderId": 0,
        "parentId": 0,
        "scopeId": "string",
        "status": "string",
        "tenantId": "string",
      **"title": "string", // 
        "typeKey": "string",
        "updateTime": "string"
      },
      "xml": "string"
    },
    "formDetailVO": {
      "account": "string",
      "formJson": "string", // 需要进行JSON解析
      "userName": "string",
      "userNo": "string"
    }
  },
  "msg": "string"
}
--------------------
formJson格式
{
	"config":{
		......
	},
	"list":[
		{
			"name":"string", // 字段名
			"model":"string", // 字段定义key
			......
		}
	]
}

```
## POST /flow/start/task 发起
```json
传参
{
  "defId": "string",
  "externalUrl": "string",  //填 "" 
  "formData": "string", //{model:value,model:value}
  "modelId": "string",
  "typeTitle": "string" // /flow/start/task中的title
}
-----------------------------------------------------
返回
{
  "code": 0,
  "data": {
    "procId": "string", // 流程id
    "taskTitle": "string" // 任务标题
  },
  "msg": "string"
}
```
## GET /flow/detail/task/{taskId} 任务详情
```json
返回
{
  "code": 0,
  "data": {
    "busFormInfo": {
      "editForm": "string", // 需要进行JSON解析
      "external": "string",
      "formJson": "string"  // 需要进行JSON解析
    },
    "nextNodeInfoVOs": [
      {
        "nodeType": "string",
      **"taskId": "string",
        "taskName": "string",
        
        "nodeUsers": [
          {
            "fullname": "string",
	      **"userNo": "string" 
          }
        ],
      }
    ]
  },
  "msg": "string"
}
------------------------
formJson格式
{
	"config":{
		......
	},
	"list":[
		{
			"name":"string", // 字段名
			"model":"string", // 字段定义key
			......
		}
	]
}
------------------------
editForm格式
{
	"字段定义key":"字段值" 
	......
}

```
## POST /flow/agree 同意
```json
传参
{
  "action": "string", // 填 agree
  "actionName": "string", // 填 agree
  "bpmVar": "string", // 填 ""
  "chooseNode": "string", // 任务详情中的nextNodeInfoVOs.taskId，多个使用 , 分隔
  "chooseNodeUser": "string", // 任务详情中的userNo,多个使用 , _ 分隔
  "formData": "string", //{model:value,model:value}
  "formType": "string", // 填 inner
  "monitor": false, // 填fasle
  "nodeIndex": 0,  // 填 0
  "nodeType": "string", // 空
  "opinion": "string", // 审批意见
  "priority": 0, // 填50
  "taskId": "string", // 本任务的taskId
  "taskName": "string", // 空
  "taskTitle": "string", // 查询待办列表时返回的title
}

```
## POST /flow/reject 驳回
```json
{
  "newActivityId": "string", // 前面的节点
  "operate": "string", // 填backToNode
  "option": "string", // 审批意见
  "priority": "string", // 50
  "taskId": "string", // 本任务的taskId
  "taskTitle": "string", // 查询待办列表时返回的title
}
```
## POST /flow/transfer 转办
```json
{
  "option": "string", // 审批意见
  "taskId": "string", // 本任务id
  "usersInfo": { // 查询任务详情时返回的节点nodeUser
    "fullname": "string",
    "userNo": "string"
  }
}
```
## POST /flow/entrust 委托流程
```json
{
  "agentType": "string",  填 "1"
  "authIds": [ // 被委托人的userNo
    "string"
  ],
  "endDate": "string",
  "startDate": "string",
  "opinion": "string", // 委托说明
  "subject": "string", // 委托标题
}
```
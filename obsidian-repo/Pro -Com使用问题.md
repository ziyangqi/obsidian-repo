
### 1 有没有浮层表格 
```  java
import { deleteUserUsingPost } from '@/services/backend/userController';  
import { PlusOutlined } from '@ant-design/icons';  
import type { ActionType, ProColumns } from '@ant-design/pro-components';  
import { PageContainer, ProTable } from '@ant-design/pro-components';  
import '@umijs/max';  
import {Button, Drawer, message, Space, Table, Typography} from 'antd';  
import React, { useRef, useState } from 'react';  
import {waitListPost, waitReadListPost} from "@/services/backend/taskController";  
  
const UserAdminPage: React.FC = () => {  
  const [drawerVisible, setDrawerVisible] = useState<boolean>(false);  
  const [createModalVisible, setCreateModalVisible] = useState<boolean>(false);  
  const [updateModalVisible, setUpdateModalVisible] = useState<boolean>(false);  
  const actionRef = useRef<ActionType>();  
  const [currentRow, setCurrentRow] = useState<API.waitList>();  
  const [drawerData, setDrawerData] = useState<any[]>([]);  
  
  const handleDelete = async (row: API.waitList) => {  
    const hide = message.loading('正在删除');  
    if (!row) return true;  
    try {  
      await deleteUserUsingPost({  
        id: row.id as any,  
      });  
      hide();  
      message.success('删除成功');  
      actionRef?.current?.reload();  
      return true;  
    } catch (error: any) {  
      hide();  
      message.error('删除失败，' + error.message);  
      return false;  
    }  
  };  
  
  const columns: ProColumns<API.waitList>[] = [  
    {  
      title: 'id',  
      dataIndex: 'id',  
      valueType: 'text',  
      hideInForm: true,  
      hideInSearch:true  
    },  
    {  
      title: '名称',  
      dataIndex: 'name',  
      valueType: 'text',  
      hideInSearch: true  
    },  
    {  
      title: '流程id',  
      dataIndex: 'processInstanceId',  
      valueType: 'text',  
      hideInSearch: true  
    },  
    {  
      title: '当前余额',  
      dataIndex: 'processDefinitionId',  
      valueType: 'text',  
      hideInSearch: true  
    },  
    {  
      title: '标题',  
      dataIndex: 'title',  
      valueType: 'text',  
      hideInSearch: true  
    },  
    {  
      title: '创建时间',  
      sorter: true,  
      dataIndex: 'createTime',  
      valueType: 'dateTime',  
      hideInSearch: true,  
      hideInForm: true,  
    },  
    {  
      title: '时间区间',  
      key: 'dateTimeRange',  
      dataIndex: 'createdAtRange',  
      valueType: 'dateTimeRange',  
      colSize:1.5,  
      hideInTable:true,  
      search: {  
        transform: (value: any) => ({  
          startTime: value[0],  
          endTime: value[1],  
        }),  
      },  
    },  
    {  
      title: '操作',  
      dataIndex: 'option',  
      valueType: 'option',  
      render: (_, record) => (  
        <Space size="middle">  
          <Typography.Link            onClick={() => {  
              setCurrentRow(record);  
              setUpdateModalVisible(true);  
            }}  
          >  
            修改  
          </Typography.Link>  
          <Typography.Link type="danger" onClick={() => handleDelete(record)}>  
            删除  
          </Typography.Link>  
          <Typography.Link type="success" onClick={async () => {  
            const {data, code} = await waitReadListPost({  
              taskId: record.id as any,  
            } as API.BaseResponseReadList_)  
            if(code === 200) {  
              console.log()  
              setDrawerData(data || []);  
              console.log(drawerData)  
              setDrawerVisible(true);  
            }  
          }}>  
            查看当前流程代办列表  
          </Typography.Link>  
        </Space>      ),  
    },  
  ];  
  
  const drawerColumnsList = [  
    {  
      title: '批次ID',  
      dataIndex: 'batchId',  
      key: 'batchId',  
    },  
    {  
      title: '业务状态',  
      dataIndex: 'bizStatus',  
      key: 'bizStatus',  
    },  
    {  
      title: '操作人',  
      dataIndex: 'commoId',  
      key: 'commoId',  
    },  
    {  
      title: '操作时间',  
      dataIndex: 'commoTime',  
      key: 'commoTime',  
    },  
    {  
      title: '流程实例ID',  
      dataIndex: 'id',  
      key: 'id',  
    },  
    {  
      title: '意见',  
      dataIndex: 'opinion',  
      key: 'opinion',  
    },  
  ];  
  
  return (  
    <PageContainer>  
      <ProTable<API.waitList>  
        headerTitle={'查询表格'}  
        actionRef={actionRef}  
        rowKey="id"  
        search={{ labelWidth: 120 }}  
        toolBarRender={() => [  
          <Button  
            type="primary"  
            key="primary"  
            onClick={() => setCreateModalVisible(true)}  
          >  
            <PlusOutlined /> 新建  
          </Button>,  
        ]}  
        request={async (params, sort, filter) => {  
          const sortField = Object.keys(sort)?.[0];  
          const sortOrder = sort?.[sortField] ?? undefined;  
          const {data ,code} = await waitListPost({  
            ...params,  
            sortField,  
            sortOrder,  
            ...filter,  
          } as API.TodoTaskQueryDTO)  
  
          return {  
            success: code === 200,  
            data: data || [],  
            total: Number(data?.length) || 0,  
          };  
        }}  
        columns={columns}  
      />  
  
      <Drawer        title="当前流程代办列表"  
        width={720}  
        onClose={() => setDrawerVisible(false)}  
        visible={drawerVisible}  
        bodyStyle={{ paddingBottom: 80 }}  
      >  
        <Table          columns={drawerColumnsList}  
          dataSource={drawerData.map(item => item.data)} // 提取 data 内的数据  返回的是MAp的结构所有要提取一下MAp
          rowKey="batchId"  
        />  
      </Drawer>    </PageContainer>  );  
};  
export default UserAdminPage;

```

### 2 查询重复的数据
``` python 
import pandas as pd  
  
# 桌面路径  
desktop_path = r'C:\Users\114514191980\Desktop'  
  
# 读取两张表格  
df1 = pd.read_excel(f'{desktop_path}\\调拨明细.xlsx')  # 第一张表格  
df2 = pd.read_excel(f'{desktop_path}\\库存表.xlsx')  # 第二张表格  
  
# 要匹配的字段列表  
fields = ['MAT_CODE', 'BATCH_NO',  'LOGICAL_NO', 'WAREHOUSE_NO']  
  
# 初始化一个新列，用于标记匹配到的行  
df2['matched'] = ''  
# 用于记录已经匹配过的组合  
matched_records = set()  
  
for index, row in df1.iterrows():  
    condition = (df2[fields] == row[fields].values).all(axis=1)  
    matching_indices = df2[condition].index  
  
    if len(matching_indices) > 0 and tuple(row[fields].values) not in matched_records:  
        first_match_index = matching_indices[0]  
        df2.at[first_match_index, 'matched'] = 'Yes'  
        matched_records.add(tuple(row[fields].values))  
  
# 保存标记后的表格  
output_path = f'{desktop_path}\\table2_marked.xlsx'  
df2.to_excel(output_path, index=False)  
  
print(f"数据对比完成，标记已保存到 {output_path} 文件中。")

```


标记所有重复的
``` python
import pandas as pd  
  
# 桌面路径  
desktop_path = r'C:\Users\114514191980\Desktop'  
  
# 读取两张表格  
df1 = pd.read_excel(f'{desktop_path}\\调拨明细.xlsx')  # 第一张表格  
df2 = pd.read_excel(f'{desktop_path}\\库存表.xlsx')  # 第二张表格  
  
# 要匹配的字段列表  
fields = ['MAT_CODE', 'BATCH_NO',  'LOGICAL_NO', 'WAREHOUSE_NO']  
  
# 初始化一个新列，用于标记匹配到的行  
df2['matched'] = ''  
# 用于记录已经匹配过的组合  
matched_records = set()  
  
for index, row in df1.iterrows():  
    condition = (df2[fields] == row[fields].values).all(axis=1)  
    matching_indices = df2[condition].index  
  
    if len(matching_indices) > 0 and tuple(row[fields].values) not in matched_records:  
        first_match_index = matching_indices[0]  
        df2.at[first_match_index, 'matched'] = 'Yes'  
        matched_records.add(tuple(row[fields].values))  
  
# 保存标记后的表格  
output_path = f'{desktop_path}\\table2_marked.xlsx'  
df2.to_excel(output_path, index=False)  
  
print(f"数据对比完成，标记已保存到 {output_path} 文件中。")
```

``` python 
## 改进的内容
import pandas as pd

# 桌面路径
desktop_path = r'C:\Users\114514191980\Desktop'

# 读取两张表格
df1 = pd.read_excel(f'{desktop_path}\\调拨明细.xlsx')  # 第一张表格
df2 = pd.read_excel(f'{desktop_path}\\库存表.xlsx')  # 第二张表格

# 要匹配的字段列表
fields = ['MAT_CODE', 'BATCH_NO', 'REC_CREATE_TIME', 'LOGICAL_NO', 'WAREHOUSE_NO']

# 初始化一个新列，用于标记匹配到的行
df2['matched'] = ''

# 用于记录已经匹配过的组合
matched_records = set()

# 遍历第一张表的每一行，在第二张表中查找匹配的记录
for index, row in df1.iterrows():
    condition = (df2[fields] == row[fields].values).all(axis=1)
    matching_indices = df2[condition].index

    # 如果有匹配的记录
    if len(matching_indices) > 0:
        # 标记所有匹配的行
        for idx in matching_indices:
            if tuple(row[fields].values) not in matched_records:
                df2.at[idx, 'matched'] = 'Yes'
                matched_records.add(tuple(row[fields].values))
            else:
                df2.at[idx, 'matched'] = '标记重复'

# 将标记后的表格保存回原文件
df2.to_excel(f'{desktop_path}\\库存表.xlsx', index=False)

print(f"数据对比完成，标记已保存到原文件 '库存表.xlsx' 中。")

```

```
import React, { useEffect, useState } from 'react';
import { Form, Input, Button, Modal } from 'antd';

interface FormJson {
  list: Array<{
    icon: string;
    key: string;
    model: string;
    name: string;
    options: {
      showAlpha: boolean;
      defaultValue: string;
      [key: string]: any; // 允许额外的属性
    };
    rules: Array<any>;
    type: string;
  }>;
  config: {
    size: string;
    labelPosition: string;
    formName: string;
    labelWidth: number;
  };
}

const DynamicForm: React.FC<{ formJson: FormJson }> = ({ formJson }) => {
  const [form] = Form.useForm();

  useEffect(() => {
    // 动态设置表单值，确保每次都使用传入的defaultValue
    const initialValues = formJson.list.reduce((values, item) => {
      values[item.model] = item.options.defaultValue || '';
      return values;
    }, {} as Record<string, any>);

    form.setFieldsValue(initialValues);
  }, [formJson, form]);

  const renderFormItem = (item: any) => {
    switch (item.type) {
      case 'input':
        return (
          <Form.Item
            key={item.key}
            label={item.name}
            name={item.model}
            rules={item.rules}
            style={{ width: item.options.width || '100%' }}
          >
            <Input placeholder={item.options.placeholder} />
          </Form.Item>
        );
      case 'textarea':
        return (
          <Form.Item
            key={item.key}
            label={item.name}
            name={item.model}
            rules={item.rules}
            style={{ width: item.options.width || '100%' }}
          >
            <Input.TextArea placeholder={item.options.placeholder} />
          </Form.Item>
        );
      // 你可以在这里添加其他类型的表单字段
      default:
        return null;
    }
  };

  const handleSubmit = (values: any) => {
    console.log('表单值:', values);
  };

  return (
    <Form
      form={form}
      layout="vertical"
      onFinish={handleSubmit}
    >
      {formJson.list.map(item => renderFormItem(item))}
      <Form.Item>
        <Button type="primary" htmlType="submit">
          提交
        </Button>
      </Form.Item>
    </Form>
  );
};

export default DynamicForm;

```


### 3 Form表单自定义

```JAVA
<Form form={form} name="validateOnly" layout="vertical" autoComplete="off">  
  <Form.Item name="name" label="下一个节点:"  >  
  </Form.Item>  <Form.Item name="age" label="Age" rules={[{ required: true }]}>  
    <Input />  </Form.Item>  <Form.Item>    <Space>  
      <Button type="primary" htmlType="submit"> 提交 </Button>  
      <Button htmlType="reset">Reset</Button>  
    </Space>  </Form.Item></Form>
```

问题 1 我想在下一个节点后加上自己传送的值 


```JAVA
<Button type="primary" >按钮</Button> //按钮的值怎么自定义获取
{buttonText} 来进行获取
const [buttonText, setButtonText] = useState('');
// 可以通过 
```  


问题2 动态的进行按钮的编写

获取nextNodeMessage 中的taskID和taskName 中的值，进行一个获取
``` JAva
{nextNodeMessage?.map(({ taskId, taskName }, index) => (  
  <Button key={taskId || index} type="primary">  
    { taskName}  
  </Button>  
))}
```

问题3 换行符是什么？


问题4  button能不能变成可以选择的 
``` Java

<div style={{gap: '8px', alignItems: 'center'}}>  
  {nextNodeMessage?.map(({taskName, nodeUsers}, index) => (  
    <div key={index} style={{display: 'flex', alignItems: 'center', marginBottom: '10px'}}>  
      <p style={{marginRight: '8px'}}>{taskName}:</p>  
      <div style={{display: 'flex', gap: '8px'}}>  
        {nodeUsers?.map(({fullname}, index) => (  
          <Button key={index} type="primary" size="middle"  
                  style={{display: 'flex', alignItems: 'center', padding: '0 8px'}}>  
            {fullname}  
          </Button>  
        ))}  
      </div>  
    </div>  ))}  
</div>
```


问题5 这里报错我loginUser不存在？
``` JAVA
<span style={{ marginRight: 8 }}>审批人: {loginUserData.userName}</span>

// 变成
loginUserData?.userName
```


问题6 表格组件
一套protable 组件
``` typeScript
// 表格组件 
// 显示我表格所有的内容 
const columns: ProColumns<API.waitList>[] = [  
  {  
    title: 'id',  
    dataIndex: 'id',  
    valueType: 'text',  
    hideInForm: true,  
    hideInSearch:true  
  },  
  {  
    title: '名称',  
    dataIndex: 'name',  
    valueType: 'text',  
    hideInSearch: true  
  },  
  {  
    title: '流程id',  
    dataIndex: 'processInstanceId',  
    valueType: 'text',  
    hideInSearch: true  
  },  
  {  
    title: '当前余额',  
    dataIndex: 'processDefinitionId',  
    valueType: 'text',  
    hideInSearch: true  
  },  
  {  
    title: '标题',  
    dataIndex: 'title',  
    valueType: 'text',  
    hideInSearch: true  
  },  
  {  
    title: '创建时间',  
    sorter: true,  
    dataIndex: 'createTime',  
    valueType: 'dateTime',  
    hideInSearch: true,  
    hideInForm: true,  
  },  
  {  
    title: '时间区间',  
    key: 'dateTimeRange',  
    dataIndex: 'createdAtRange',  
    valueType: 'dateTimeRange',  
    colSize:1.5,  
    hideInTable:true,  
    search: {  
      transform: (value: any) => ({  
        startTime: value[0],  
        endTime: value[1],  
      }),  
    },  
  },  
  {  
    title: '操作',  
    dataIndex: 'option',  
    valueType: 'option',  
    render: (_, record) => (  
      <Space size="middle">  
        <Typography.Link          onClick={() => {  
            handleApprove(record)  
            setCurrentRow(record);  
          }}  
        >审批  
        </Typography.Link  >  
        <Typography.Link type="success" onClick={async () => {  
          const {data, code} = await getSpeedListGet({  
            taskId:record.id as any  
          });  
          if(code === 200) {  
            console.log()  
            // @ts-ignore  
            setDrawerData(data.data || []);  
            console.log(drawerData)  
            setDrawerVisible(true);  
          }  
        }}>  
          流程进度列表  
        </Typography.Link>  
        <Typography.Link type="success" onClick={async () => {  
          const {data, code} = await getSpeedGet({  
            taskId: record.processInstanceId as any,  
          })  
          if(code === 200) {  
            // @ts-ignore  
            console.log(data.data)  
            // @ts-ignore  
            setModalData(data.data || []);  
            console.log(drawerData)  
            setModalVisible(true);  
          }  
        }}>  
          流程进度  
        </Typography.Link>  
      </Space>    ),  
  },  
];
// type 的内容
type waitList = {  
  id?: string  
  name?: string  
  createTime?: string  
  processDefinitionId?: string  
  processInstanceId?: string  
  title?: string  
}

// protable的内容

<ProTable<API.waitList>  
  headerTitle={'查询表格'}  
  actionRef={actionRef}  
  rowKey="id"  
  pagination={{  
    pageSize: 10,         // 每页显示10条记录  
    showSizeChanger: true, // 允许用户修改每页记录数  
    defaultPageSize: 10,  // 默认每页显示10条  
    showQuickJumper: true // 允许快速跳转到某一页  
  }}  
  search={{ labelWidth: 120 }}  
  toolBarRender={() => [  
    <Button  
      type="primary"  
      key="primary"  
    >  
      <PlusOutlined /> 新建  
    </Button>,  
  ]}  
  request={async (params, sort, filter) => {  
    const sortField = Object.keys(sort)?.[0];  
    const sortOrder = sort?.[sortField] ?? undefined;  
    const {data ,code} = await waitListPost({  
      page: 1,  
      limit:10000,  
      sortField,  
      sortOrder,  
      ...filter,  
    } as API.TodoTaskQueryDTO)  
  
    const userData = await getLoginUserUsingGet();  
    // @ts-ignore  
    setLoginUserData(userData.data)  
  
    return {  
      success: code === 200,  
      data: Array.isArray(data) ? data : [],  
      total: Number(data?.length) || 0,  
    };  
  }}  
  columns={columns}  
/>
```
*主要是我第一次的时候进行查询的时候需要查询所有的信息
我需要设置我的page 和 limit 为1和1000来传递给后端，然后使用这个方法来进行分页的管理*
``` java
pagination={{  
    showSizeChanger: true, // 允许用户修改每页记录数  
    defaultPageSize: 10,  // 默认每页显示10条  
    showQuickJumper: true // 允许快速跳转到某一页  
  }}  
```


问题7 新建一个命名，从以前的内容里面拿出来
![[Pasted image 20240829084448.png]]

比如我新建一个东西来拿到userName和userAccount 转化为一个新的内容

```TypeScript
const user = {
  id: 1,
  userName: 'zhyw114515',
  userAccount: 'zyqyqy',
  userAvatar: '2222',
  userRole: 'admin',
  gender: 2,
  createTime: '2024-08-16T06:31:47.000+00:00',
  updateTime: '2024-08-28T08:45:54.000+00:00'
};

// 转换为新的对象
const transformedUser = {
  userNo: user.userName,          // 将 userName 转换为 userNo
  fullname: `${user.userName} (${user.userAccount})`  // 将 userName 和 userAccount 组合为 fullname
};

console.log(transformedUser);


// 如果是数组
const transformedUsers = user?.map((user: { userName: any; userAccount: any; }) => ({  
  userNo: user.userName,  
  fullname: user.userAccount,  
}));
```
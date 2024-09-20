
### 使用jmeter工具

#### 1 使用jmeter我的请求参数中的某个参数怎么是json格式的
可以直接在消息体内直接使用JSON的格式
![[Pasted image 20240904173412.png]]

#### 2 使用全局jmeter 中的 cookie管理器没有起作用

*实际是要把本地环境中配置的jmeter中的文件*
- bin目录下的 jmeter.properties文件值的 cookie.save 给改为ture
- 其次是要把登录放在某些调用cookie的前面，不能放在后面
参考连接: https://blog.csdn.net/weixin_45294964/article/details/118969044

#### 3基础使用方法
##### 1 基础模板使用本地
![[本地测试.jmx]]

##### 2 本地的基本配置和jmeter的文件

![[apache-jmeter-5.4.3.zip]]

##### 3 本地环境变量配置
1.  `%JMETER_HOME%\bin`
2. `Jmeter_HOME ：D:\Program Files\javapz\apache-jmeter-5.4.3`
3. `ClassPath : %JMETER_HOME%\lib\jorphan.jar;%JMETER_HOME%\lib\logkit-2.0.jar;`
##### 4 配置好之之后比如说要打开其他的文件

可能是缺少插件 缺少把这个插件放在 : `apache-jmeter-5.4.3\lib\ext`文件夹之下

![[jmeter-plugins-manager-1.10.jar]]

#### 4 添加请求头添加CooKie管理器

##### 4.1 添加请求头
一般都是添加JSON格式
Content-Type : application/json

![[Pasted image 20240904173524.png]]


##### 4.2 添加Cookie管理器
默认添加cookie管理器之后每次访问都是自己带上cookie。
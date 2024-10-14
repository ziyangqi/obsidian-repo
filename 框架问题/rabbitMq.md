## 1 安装rabbitMq
1. 安装rabbitMq的网址:https://www.rabbitmq.com/docs/install-windows#chocolatey
2. 安装rabbitMq之前要安装erlang:https://www.erlang.org/downloads
3. 安装好之后，看看服务是否开启，如果已经开启了打开本地的地址 http://localhost:15672/
4. 直接使用docker来进行安装 https://blog.csdn.net/weixin_50348837/article/details/136751792

## 2 rabbitMq的使用方法


## 3 SpringAMQP 

## 4 处理情况
### 4.1 WorkQueues模型
*简单来说就是多个消费者绑定一个队列，共同消费队列中的消息*
因为有些时候生产者的消息会远远大于消费者的消费速度。

首先建立一个新的队列 work-queue，并且建立了两个消费者的队列去进行处理，

```JAVA
@Test  
public void testWorkQueue() throws InterruptedException{  
    // 队列名称  
    String queueName = "work-queue";  
    // 消息  
    String message = "hello, zyq!";  
  
    for (int i = 0; i < 50; i++) {  
        rabbitTemplate.convertAndSend(queueName, message + i);  
        Thread.sleep(20);  
    }  
}
```

```JAVA
@RabbitListener(queues = "work-queue")  
public void listenWorkQueueMessage(String msg) throws InterruptedException {  
    System.out.println("work 消费者1接收到消息：【" + msg + "】");  
    Thread.sleep(20);  
}  
  
@RabbitListener(queues = "work-queue")  
public void listenWordQueueMessage2(String msg) throws InterruptedException {  
    System.out.println("word 消费者2接收到消息：【" + msg + "】");  
    Thread.sleep(2000);  
}
```

**会发现这两个消费者会共同处理50条数据，并不是先到先得的类型**

所以需要改成先到先得的内容。

**解决**
```yml
# spring中有一个简单的流程

spring:
  rabbitmq:
    listener:
      simple:
        prefetch: 1 # 每次只能获取一条消息，处理完成才能获取下一个消息
```

*prefetch 表示 每个消费者每次从队列中预先取一个，执行完之后，才可以取出来下一个，避免rabbitmq采用公平轮询的方式，将消息依次发给一个消费，等他消费完了再发下一个给另外的消费者。*





**结果**
二者不再是每次只执行25次(对于50个数据)
![[Pasted image 20240731143326.png]]



### 4.2 交换机类型
之前的测试中，没有交换机，生产者直接发送给消息队列，**而一旦引入了交换机。消息的模式会发生变化**
![[Pasted image 20240731143708.png]]
可以看到，在订阅模型中，*多了一个exchange角色*，而且过程略有变化：
- Publisher：生产者，不再发送消息到队列中，而是*发给交换机。*
- *Exchange：交换机，一方面，接收生产者发送的消息。另一方面，知道如何处理消息，例如递交给某个特别队列、递交给所有队列、或是将消息丢弃。到底如何操作，取决于Exchange的类型*。
- Queue：消息队列也与以前一样，接收消息、缓存消息。*不过队列一定要与交换机绑定。
- Consumer：消费者，与以前一样，订阅队列，没有变化

**Exchange（交换机）只负责转发消息，不具备存储消息的能力，因此如果没有任何队列与Exchange绑定，或者没有符合路由规则的队列，那么消息会丢失！**


#### 4.2.1 Fanout交换机
Fanout 英文翻译是扇出，MQ中叫广播。在广播模式下的流程是：

![[Pasted image 20240731144054.png]]
1. 可以有多个队列
2. 每个队列都要绑定Exchange交换机
3. 生产者发送的消息只能发送到交换机
4. 交换机把消息发送给*绑定过的所有队列*
5. 订阅队列的消费者*都能拿到消息*

##### 4.2.1.1 声明队列和交换机
  创建两个队列fanout-queue1，fanout-queue2
![[Pasted image 20240731144533.png]]
创建一个交换机Exchange Type为fanout的类型
![[Pasted image 20240731144634.png]]

把这两个队列绑定为这个交换机
![[Pasted image 20240731144759.png]]
会看到这里已经绑定了两个交换机

---
在消费者的代码里面去接收这个队列的方法

``` JAVA
/**  
 * fanout 模式  
 * @param msg 消息  
 * @throws InterruptedException  
 */@RabbitListener(queues = "fanout-queue2")  
public void listenFanoutQueueMessage1(String msg) throws InterruptedException {  
    System.out.println("fanout 消费者2接收到消息：【" + msg + "】");  
}  
  
/**  
 *  fanout 模式  
 * @param msg 消息  
 * @throws InterruptedException  
 */@RabbitListener(queues = "fanout-queue1")  
public void listenFanoutQueueMessage2(String msg) throws InterruptedException {  
    System.out.println("fanout 消费者1接收到消息：【" + msg + "】");  
}

```

会发现这两个队列的内容是都有的。说明*fanout交换机的类型是广播的*
![[Pasted image 20240731150241.png]]
##### 4.2.1.2 fanout总结
1.  交换机是接收publisher发送的消息
2.  消息按照规则路由到与之绑定的队列
3.  队列不能缓存消息，路由失败，消息会消失
4.  *fanoutExchange会将消息路由到每个绑定的队列*




#### 4.2.2 Direct 交换机
在fanout模式中，一条消息，会被所有订阅也就是*被绑定*的队列消费掉，但是某些场景下，我们希望*不同的消息被不同的队列所接收*，这个时候需要用到Direct类型的Exchange。
![[Pasted image 20240731151554.png]]
**在Direct模式下**
1.  队列与交换机绑定，不能是任意绑定了，而是必须需要一个指定的RoutingKey(路由Key)
2.  消息的发送方向Exchange发送消息的时候，也必须指定消息的RoutingKey
3. Exchange不再把消息交给每一个绑定的队列，而是根据消息的RoutingKey 进行判断，只有队列的RoutingKey与消息的RoutingKey完全一致，才会进行接收。

##### 4.2.2.1 声明交换机和队列

特殊的地方，绑定队列的时候需要写出RoutingKey
![[Pasted image 20240731152557.png]]
写出RoutingKey [red]  [Black] 来进行表示
![[Pasted image 20240731152620.png]]
###### 4.2.2.1.1 代码进行测试

publisher 代码
``` Java
// 执行结束这个代码就表示了有两条消息，放到了zyq.direct Exchange中 根据routerKey 分别到了不同的队列中
@Test  
public void testDirectExchange() {  
    // 交换机名称  
    String exchangeName = "zyq.direct";  
    // routing key  
    String routingKey1 = "red";  
    String routingKey2 = "black";  
    // 消息  
    String message1 = "hello, direct red exchange!";  
    String message2 = "hello ,direct black exchange!";  
    // 发送消息  
    rabbitTemplate.convertAndSend(exchangeName, routingKey1, message1);  
    rabbitTemplate.convertAndSend(exchangeName, routingKey2, message2);  
  
}
```
执行这个方法后每个队列各1个数据
![[Pasted image 20240731153337.png]]

Consumer代码
``` Java
  
/**  
 * Direct 模式  
 * @param msg 消息  
 * @throws InterruptedException 异常  
 */  
@RabbitListener(queues = "direct-queue1")  
public void listenDirectQueueMessage1(String msg) throws InterruptedException {  
    System.out.println("direct 消费者1接收到消息：【" + msg + "】");  
}  
  
/**  
 * Direct 模式  
 * @param msg 消息  
 * @throws InterruptedException 异常  
 */  
@RabbitListener(queues = "direct-queue2")  
public void listenDirectQueueMessage2(String msg) throws InterruptedException {  
    System.out.println("direct 消费者2接收到消息：【" + msg + "】");  
}

```

执行结果
``` sERVICE
direct 消费者2接收到消息：【hello ,direct black exchange!】
direct 消费者1接收到消息：【hello, direct red exchange!】
```

##### 4.2.2.2 Direct总结
1.  *Fanout* 交换机将消息路由给*每一个绑定的队列*
2.  *Direct*交换机根据*RouterKey*判断给哪一个队列
3.  如果队列中多个队列有一样的*RouterKey*则与*Fanout的功能相似*，类似与广播的格式。



#### 4.2.3 Topic 交换机

尽管 *direct* 交换机改进了我们的系统，但是仍热有一些局限性，比如我们的交换机绑定了多个RouterKey，在*direct*模式下，选择性是单一的，必须相同，也就是说一个消息，只能被一个相同的RoutingKey绑定消息，但是如果我们想要变的多元，*比如划分一个分组，一个消息可以对一个组别进行投递*
用到Topic模式

**关键：Topic类型的Exchange可以队列绑定BindingKey的时候使用通配符**

*BindingKey* 一般由1个或者多个单词组成，多个单词之间以 . 进行分割。比如item.insert 

通配符的规则
-  # : 匹配一个或者对个词
-  *  : 匹配一个单词
---
例子: 
- item.# ：能够匹配item.spu.insert或者item.spu
- item.*  ：只能够匹配一个单词，item.spu


![[Pasted image 20240731160415.png]]

![[Pasted image 20240731160423.png]]

![[Pasted image 20240731160449.png]]

##### 4.2.3.1 声明交换机和队列

交换机
![[Pasted image 20240731160727.png]]

队列1 
![[Pasted image 20240731160800.png]]
两个队列
![[Pasted image 20240731160828.png]]
绑定两个队列

`#` 表示支持1个或者多个匹配的字符
![[Pasted image 20240731161032.png]]

代码
``` Java
// 设计了3个RouterKey
// 根据上述的china.# #.news 应该队列1有两个，分别是china.news,china.weather ，队列2也应该有两个china.news,america.news
@Test  
public void testTopicExchange() {  
    // 交换机名称  
    String exchangeName = "zyq.topic";  
    // routing key  
    String routingKey1 = "china.news";  
    String routingKey2 = "america.news";  
    String routingKey3 = "china.weather";  
    // 消息  
    String message1 = "hello, topic china.news exchange!";  
    String message2 = "hello ,topic america.news exchange!";  
    String message3 = "hello ,topic china.weather exchange!";  
    // 发送消息  
    rabbitTemplate.convertAndSend(exchangeName, routingKey1, message1);  
    rabbitTemplate.convertAndSend(exchangeName, routingKey2, message2);  
    rabbitTemplate.convertAndSend(exchangeName, routingKey3, message3);  
}

```
显示如下就是两个队列
![[Pasted image 20240731162202.png]]

消费者代码
``` jAVA
/**  
 * topic交换机  
 * @param msg 队列中消息  
 * @throws InterruptedException 异常  
 */  
@RabbitListener(queues = "topic.queue1")  
public void listenTopicQueueMessage1(String msg) throws InterruptedException {  
    System.out.println("topic 消费者1接收到消息：【" + msg + "】");  
}  
  
/**  
 * topic交换机  
 * @param msg 队列中消息  
 * @throws InterruptedException 异常  
 */  
@RabbitListener(queues = "topic.queue2")  
public void listenTopicQueueMessage2(String msg) throws InterruptedException {  
    System.out.println("topic 消费者2接收到消息：【" + msg + "】");  
  
}
```

执行结果
```SERVICES
topic 消费者1接收到消息：【hello, topic china.news exchange!】
topic 消费者1接收到消息：【hello ,topic china.weather exchange!】
topic 消费者2接收到消息：【hello, topic china.news exchange!】
topic 消费者2接收到消息：【hello ,topic america.news exchange!】
```

##### 4.2.3.2 总结
1. Topic交换机接收的消息RouteringKey必须是多个单词 以 `*`.`*` 或者 `#.#` 进行分割。
2. Topic交换机与队列绑定时的BindingKey可以指定通配符
3. `#` 代表0个或者对个词
4. `*` 代表1个词


### 4.3 API声明队列和交换机

在实际的开发中队列和交换机是程序员定义的，推荐的算法是在创建队列的时候检查队列和交换机是否存在，如果不存在就自动创建。

#### 4.3.1 基础API
SpringAMQP提供了一个Queue类，创建队列
![[Pasted image 20240731165332.png]]
同时SpringAMQP还提供了一个Exchange接口，表示不同类型的交换机
![[Pasted image 20240731165425.png]]

可以再此创建自己的队列和交换机，不过SpringAMQP还提供了ExchageBuilder来简化这个过程。

![[Pasted image 20240731165549.png]]

绑定队列和交换机时候需要使用BindingBuilder来创建Binding对象。

![[Pasted image 20240731165708.png]]

#### 4.3.2 fanout实例
在consumer中创建一个配置类，生命队列和交换机


### 错误
##### 1 出现 PLAIN login refused: user 'zyq' - invalid credentials 或者出现ACCESS_REFUSED - Login was refused using authentication mechanism PLAIN. For details see the broker logfile.
---
大多数都是因为没有yml文件有问题，直接复制一般是有空格的，建议好好检查一下yml的文件的rabbitmq的配置是否有问题。
或者去日志看看是否是这个报错，日志文件，在':\Users\114514191980\AppData\Roaming\RabbitMQ\log' 默认在

参看链接 https://blog.csdn.net/Ming13416908424/article/details/114654747

```yml
spring:  
  rabbitmq:  
    host: localhost  
    port: 5672  
    virtual-host: /hmail  
    password: zyqzyq  
    username: zyq
```




```
# 安装
## 压缩包安装
关于Kafka的安装 [kafka官网](https://kafka.apache.org/quickstart) 去下载安装包 然后进入到你本地的下载目录 以mac为例：
```go
tar -xzf kafka_2.13-2.8.0.tgz
cd kafka_2.13-2.8.0
```
运行以下命令以正确的顺序启动所有服务
```go
# Start the ZooKeeper service
# Note: Soon, ZooKeeper will no longer be required by Apache Kafka.
$ bin/zookeeper-server-start.sh config/zookeeper.properties
```
打开另一个终端会话并运行：

```go
# Start the Kafka broker service
$ bin/kafka-server-start.sh config/server.properties
```


## brew 安装
```go
brew install kafka
```
![image.png](https://cdn.nlark.com/yuque/0/2021/png/2547874/1619075715951-5313e5d6-841d-4573-9de8-f3d26885edf1.png#clientId=u2d72c93a-af0f-4&from=paste&height=754&id=u094e466b&margin=%5Bobject%20Object%5D&name=image.png&originHeight=1508&originWidth=1266&originalType=binary&size=274419&status=done&style=none&taskId=ufa949e3d-2f87-4934-9452-1bae88d51aa&width=633)
如果你不想以服务的方式启动（临时启动）执行：
```go
 zookeeper-server-start /usr/local/etc/kafka/zookeeper.properties & kafka-server-start /usr/local/etc/kafka/server.properties
```
For the system Java wrappers to find this JDK, symlink it with（为了使系统Java包装器可以找到此JDK，请使用以下符号进行符号链接）
```go
sudo ln -sfn /usr/local/opt/openjdk/libexec/openjdk.jdk /Library/Java/JavaVirtualMachines/openjdk.jdk
```
If you need to have openjdk first in your PATH, run:（如果您需要先在路径中包含openjdk，请运行）
```go
 echo 'export PATH="/usr/local/opt/openjdk/bin:$PATH"' >> ~/.zshrc
```
For compilers to find openjdk you may need to set:   （为了使编译器找到openjdk，您可能需要设置：）
```go
export CPPFLAGS="-I/usr/local/opt/openjdk/include"
```




然后执行：
```go
vim /usr/local/etc/kafka/server.properties
```
找到 `listeners=PLAINTEXT://:9092` 那一行，把注释取消掉 
修改为：`listeners=PLAINTEXT://localhost:9092`


如果想以服务的方式启动，那么可以:
```go
brew services start zookeeper
brew services start kafka
```
![image.png](https://cdn.nlark.com/yuque/0/2021/png/2547874/1619076498037-9bc1ea70-0125-474d-a464-a5815b432882.png#clientId=u2d72c93a-af0f-4&from=paste&height=182&id=u8764667d&margin=%5Bobject%20Object%5D&name=image.png&originHeight=254&originWidth=1078&originalType=binary&size=45313&status=done&style=none&taskId=u125e0979-6b45-4c15-ab0f-5b3533dc408&width=774)
## 基本应用
### 创建Topic
打开一个终端（命名为a）
```go
$ kafka-topics --create --zookeeper localhost:2181 --replication-factor 1 --partitions 1 --topic test
```
### 生产消息
在开一个终端（命名为b）
```go
kafka-console-producer --broker-list localhost:9092 --topic test
```
### 消费消息
在a中输入：
```go
kafka-console-consumer --bootstrap-server localhost:9092 --topic test --from-beginning
```
此时在b中输入任何输入 在a中 都可以输出
![image.png](https://cdn.nlark.com/yuque/0/2021/png/2547874/1619077400499-dc60fe6e-e49c-4112-8f72-ca5f34fbc5e3.png#clientId=u2d72c93a-af0f-4&from=paste&height=260&id=u58c2defb&margin=%5Bobject%20Object%5D&name=image.png&originHeight=520&originWidth=2560&originalType=binary&size=110318&status=done&style=none&taskId=u255017a1-6ba1-4b09-8f22-54319de2963&width=1280)
# 基本概念
kafka是什么？
> Kafka 是分布式发布-订阅消息系统。它最初由 LinkedIn 公司开发，之后成为 Apache 项目的一部分。Kafka 是一个分布式的，可划分的，冗余备份的持久性的日志服务

以下图片转发自[大白话 + 13 张图解 Kafka](https://zhuanlan.zhihu.com/p/103276657)


![image.png](https://cdn.nlark.com/yuque/0/2021/png/2547874/1619079268367-6c69a527-7872-476d-9c93-37959dc96783.png#clientId=u2d72c93a-af0f-4&from=paste&height=508&id=ub12ceef1&margin=%5Bobject%20Object%5D&name=image.png&originHeight=508&originWidth=640&originalType=binary&size=119278&status=done&style=none&taskId=u8d25d233-f085-49bf-b610-aba64a6fec3&width=640)
### Broker-节点
> 消息队列中常用的概念，在Kafka中指部署了Kafka实例的服务器节点。对于Kafka而言，Broker可以简单地看作一个独立的Kafka服务节点或Kafka服务实例。

### Topic-主题
> kafka学习了数据库里面的设计，在里面设计了topic（主题），这个东西类似于关系型数据库的数据库。

![image.png](https://cdn.nlark.com/yuque/0/2021/png/2547874/1619079410809-f3581025-f6a4-4060-977a-0555e1120924.png#clientId=u2d72c93a-af0f-4&from=paste&height=519&id=u8a4a6e20&margin=%5Bobject%20Object%5D&name=image.png&originHeight=519&originWidth=640&originalType=binary&size=151982&status=done&style=none&taskId=u54f33dd1-e563-45f3-8e13-dd4ebb81a44&width=640)


如果需要cu的数据 直接监听TopicB的数据即可


### Partition-分区
> kafka还有一个概念叫Partition（分区），分区具体在服务器上面表现起初就是一个目录，一个主题下面有多个分区，这些分区会存储到不同的服务器上面，或者说，其实就是在不同的主机上建了不同的目录。这些分区主要的信息就存在了.log文件里面。跟数据库里面的分区差不多，是为了提高性能



![image.png](https://cdn.nlark.com/yuque/0/2021/png/2547874/1619080249857-3d5f40e7-acb6-4815-8c73-164408d1c08d.png#clientId=u2d72c93a-af0f-4&from=paste&height=516&id=u0942c19c&margin=%5Bobject%20Object%5D&name=image.png&originHeight=516&originWidth=640&originalType=binary&size=166479&status=done&style=none&taskId=u3062291d-0e8b-443b-baaf-f9bdf93ae6f&width=640)
**Topic也是逻辑概念**，而partition就是分布式存储单元


### Producer - 生产者
> 往消息系统里面发送数据的就是生产者

![image.png](https://cdn.nlark.com/yuque/0/2021/png/2547874/1619080356942-31df4baf-2b7c-4cea-aa19-fa303872c8dd.png#clientId=u2d72c93a-af0f-4&from=paste&height=504&id=u1bd76df0&margin=%5Bobject%20Object%5D&name=image.png&originHeight=504&originWidth=640&originalType=binary&size=170682&status=done&style=none&taskId=u7d9f8d4c-f061-4464-9281-ff41f5d21e9&width=640)
### Consumer - 消费者
> 从kafka里读取数据的就是消费者

![image.png](https://cdn.nlark.com/yuque/0/2021/png/2547874/1619080411514-11dd45df-45ed-447c-97d4-eaff083880d5.png#clientId=u2d72c93a-af0f-4&from=paste&height=493&id=ue1ff4b95&margin=%5Bobject%20Object%5D&name=image.png&originHeight=493&originWidth=640&originalType=binary&size=183112&status=done&style=none&taskId=uf7b4e389-1b21-4a3a-a030-1efb4999a25&width=640)
### Message - 消息
> kafka里面的我们处理的数据叫做消息

### Record
> 实际写入Kafka中并可以被读取的消息记录。每个record包含了key、value和timestamp。



## kafka的硬件选择
![image.png](https://cdn.nlark.com/yuque/0/2021/png/2547874/1619080588930-8c93b152-2ff0-44e4-b1b1-263973daa691.png#clientId=u2d72c93a-af0f-4&from=paste&height=213&id=u7f96824f&margin=%5Bobject%20Object%5D&name=image.png&originHeight=360&originWidth=1316&originalType=binary&size=40760&status=done&style=none&taskId=u937153cf-c684-4a7d-9479-dac8a14f7e0&width=779)
## kafka的集群架构

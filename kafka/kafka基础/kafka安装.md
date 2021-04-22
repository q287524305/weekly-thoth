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

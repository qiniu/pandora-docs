Pandora Java SDK 让 Java 开发人员可以非常方便地使用 Java 程序接入  Pandora 大数据平台的 Pipeline 服务。

Github地址：[SDK-Java](https://github.com/qiniu/pandora-java-sdk)

### 安装方式

开发者可以直接使用 Maven 依赖添加 SDK，在您的项目 pom 文件里面加入以下几行代码：

```
<dependency>
    <groupId>com.qiniu</groupId>
    <artifactId>qiniu-pandora-sdk</artifactId>
    <version>1.x</version>
</dependency>
```

### 使用范例

1.[顺次发送数据点](https://github.com/qiniu/pandora-java-sdk/blob/master/example/sender/src/main/java/SequenceSender.java)

2.[并发发送数据点](https://github.com/qiniu/pandora-java-sdk/blob/master/example/sender/src/main/java/ParallelSender.java)

3.[使用底层接口，攒成一批数据发送数据](https://github.com/qiniu/pandora-java-sdk/blob/master/example/sender/src/main/java/BatchPointsSender.java)

4.[迭代生成数据点进行发送，避免把所有数据点缓存到内存中](https://github.com/qiniu/pandora-java-sdk/blob/master/example/sender/src/main/java/LazyPointsSender.java)

5.[从数据文件中读取内容进行发送](https://github.com/qiniu/pandora-java-sdk/blob/master/example/sender/src/main/java/FileSender.java)

6.[日志查询](https://github.com/qiniu/pandora-java-sdk/blob/master/example/logdb/src/main/java/Search.java)
工作流是一个类思维脑图形式的数据接收、计算、导出工具，使用户可视化的抽象数据业务走向，把业务流程映射到页面上，更直观、更精简的来进行大数据分析流程管理。

![](http://docs.qiniucdn.com/01%E5%BC%B9%E5%87%BA.png)



## 概念

用户可以创建一个或多个工作流，每一个工作流都有自己独立的状态，用户可以自主控制每一个工作流的`启动`和`停止`，类似于主机的开机和关机行为。

工作流内包含3种组件：数据源、数据计算、导出；通常情况下，至少包含一个数据源和一个数据导出的工作流，才是一个正常的工作流。

工作流有两种运行模式：调度执行和手动执行；调度执行是指将工作流启动后，系统自动将自动开始运行，并按照条件执行调度；手动执行是指将工作流运行一遍后停止。

!> 注意1：流式计算属于调度执行模式。

!> 注意2：一个工作流不能同时包含调度模式（流式计算/定时批量计算/循环批量计算）和手动执行模式（手动执行）。

</br>

工作流中的数据结构由以下几种类型组成：

|类型|解释|数据样例|
|:--|:--|:--|
|date|日期类型，默认展示格式为`RFC3339`，可自定义格式|`2017-01-01T15:00:25Z07:00`|
|string|字符串类型|"qiniu.com"|
|long|64位整数|1024|
|float|单精度64位浮点|322.00|
|boolean|布尔类型，值为`true`或`false`|false|

具体用法请观看视频教程。

**视频教程：**

<video id="video" controls="" preload="none" poster="http://onzeipdi1.bkt.clouddn.com/mp4-bg.png">
      <source id="mp4" src="http://docs.qiniucdn.com/workflow_2.mov" type="video/mp4"></video>

</br>


## 数据源

数据源是工作流的起始节点，它可以接收实时上传的数据或读取离线存储的数据，在工作流中，目前支持以下几种类型的数据源：

|名称|流式计算|批量计算|备注|
|:--|:--|:--|:--|
|消息队列|yes|no|只能作用于流式计算，实时接收用户上传的数据；每一条进入消息队列的数据，都会被存储2天时间，过期自动删除|
|对象存储|no|yes|只能作用于批量计算，可以一次性加载大量数据|
|CDN|no|yes|只能作用于批量计算，数据来源于七牛CDN产品|
|HDFS|no|yes|只能作用于批量计算，仅支持私有云，公有云不提供此服务|

!> 注意：消息队列节点在保存工作流之后，无论是否启动该工作流，都可以接收数据。

具体用法请观看视频教程。

**视频教程：**

<video id="video" controls="" preload="none" poster="http://onzeipdi1.bkt.clouddn.com/mp4-bg.png">
      <source id="mp4" src="http://docs.qiniucdn.com/datasource_1.mov" type="video/mp4"></video>


</br>

## 数据计算

工作流的计算模型分为两种：`流式计算`和`批量计算`；

流式计算既我们常说的实时计算，批量计算即我们常说的离线计算。

在批量计算中，又有2种运行模式：`定时执行`和`循环执行`。

!> 注意：每一个工作流都可以同时包含一个或多个流式计算与批量计算，并且计算之间可以串行。

具体用法请观看视频教程。

**视频教程：**

<video id="video" controls="" preload="none" poster="http://onzeipdi1.bkt.clouddn.com/mp4-bg.png">
      <source id="mp4" src="http://docs.qiniucdn.com/workflow_3.mov" type="video/mp4"></video>

</br>


## 数据导出

将数据源、消息队列或批量计算任务中的数据导出到指定的地址。

目前我们支持将数据导出到以下地址：
```
1. 指定一个日志分析服务的日志仓库；
2. 指定一个时序数据库的数据仓库下的序列；
3. 指定一个HTTP服务器地址；
4. 指定一个对象存储的Bucket；
5. 指定一个报表工作室的数据仓库下的数据表；
```

## 自定义计算

编写代码，提交Jar包到工作流，并在流式计算中执行。

目前仅支持Java语言。

具体用法请观看视频教程。

**视频教程：**

<video id="video" controls="" preload="none" poster="http://onzeipdi1.bkt.clouddn.com/mp4-bg.png">
      <source id="mp4" src="http://docs.qiniucdn.com/plugin.mov" type="video/mp4"></video>

## UDF管理

UDF可以在批量计算和流式计算的`SQL计算`模式中使用；

目前系统内部提供了大约50多种内置UDF函数；

用户也可以自己编写UDF，提交Jar包并注册之后即可使用。



## 魔法变量

魔法变量的概念类似于编程语言中的变量；目前魔法变量仅支持时间类型的值；

目前系统提供了8个内置变量，只能引用，不可修改和删除：

|变量名称|类型|值|格式|
|:--:|:--:|:--:|:--:|
|date|时间表达式|$(date)|yyyy-MM-dd|
|day|时间表达式|$(day)	|dd|	
|hour|时间表达式|$(hour)|HH|	
|min|时间表达式|$(min)|mm|	
|mon|时间表达式|$(mon)|MM|	
|now|时间表达式|$(now)	|`yyyy-MM-dd HH:mm:ss`|
|sec|时间表达式|$(sec)|ss|	
|year|时间表达式|$(year)|yyyy|

用户也可以自行创建魔法变量，并且创建的魔法变量的值可以引用系统变量。

当我们需要使用魔法变量的时候，只需要输入 `$(变量名称)` 即可，如:
```
select * from stream where time = $(now)
```



## 报警设置

敬请期待...

## 权限管理

敬请期待...



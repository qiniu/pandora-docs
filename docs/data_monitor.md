**视频教程：**

<video id="video" controls="" preload="none" poster="http://onzeipdi1.bkt.clouddn.com/mp4-bg.png">
      <source id="mp4" src="http://docs.qiniucdn.com/monitor.mov" type="video/mp4"></video>

</br>

要进行实时的监控，我们需要用到`时序数据库`服务和`Grafana`应用；

我们需要将刚刚打入工作流中的数据再导出到时序数据库中一份；

### 1.将数据打入时序数据库

在七牛控制台进入大数据工作流引擎，找到logkit自动帮我们创建的工作流，点击编辑进入工作流页面；

我们右键日志检索节点选择删除；

然后拖拽一个时序数据库节点，并与消息队列进行连接，按照下图内容进行填写；

!> 注意：在字段设置中，将sensor与location设置为索引字段，collection_time设置为时间戳字段，其余的为普通字段。

点击 保存/更新，
重新启动工作流；

![](http://docs.qiniucdn.com/data_monitor1.png)

从控制台进入时序数据库服务，进入数据查询页面，查询我们刚才导出到时序数据库的数据；

![](http://docs.qiniucdn.com/data_monitor2.png)

### 2.部署Grafana

在控制台主页的最下方，我们可以找到Grafana应用，点击进入；
![](http://docs.qiniucdn.com/monitor3.png)

点击右上角的新建部署，待部署完成后，设置一个应用密码；
![](http://docs.qiniucdn.com/monitor4.png)

### 3.配置Grafana

点击访问Grafana，进入主页；
![](http://docs.qiniucdn.com/monitor5.png)

点击Add data source，添加一个数据源；
![](http://docs.qiniucdn.com/monitor6.png)

添加数据源页面，首先输入一个Name（名称）；

然后Type（类型）选择Qiniu:Pandora TSDB；

Database（数据库）输入我们创建的时序数据仓库名称；

User输入账户公钥；

Password输入账户私钥；

最后点击Add按钮；
![](http://docs.qiniucdn.com/monitor7.png)

接下来开始制作实时监控图表；

点击左上角Grafana的logo，选择Dashboards-New；
![](http://docs.qiniucdn.com/monitor8.png)

在图表类型中我们选择Graph；
![](http://docs.qiniucdn.com/monitor9.png)

点击Panel Title，选择edit；

![](http://docs.qiniucdn.com/data_monitor3.png)

在左下角的SQL配置窗口，输入的条件意思如下：

```
统计两个采集器在地址1，每10秒监测到的蝴蝶平均数
```

![](http://docs.qiniucdn.com/data_monitor4.png)

图表编辑完成后，点击左上角的保存按钮。
![](http://docs.qiniucdn.com/data_monitor5.png)

以上是最基础的时序数据库+Grafana组合的实时监控场景，更多用法请参考产品详解 —— [时序数据库](tsdb.md)
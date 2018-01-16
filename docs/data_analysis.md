**视频教程：**

<video id="video" controls="" preload="none" poster="http://onzeipdi1.bkt.clouddn.com/mp4-bg.png">
      <source id="mp4" src="http://docs.qiniucdn.com/logdb.mov" type="video/mp4"></video>

</br>

在七牛控制台进入日志分析服务，在仓库列表页面我们可以看到，刚刚的数据采集器logkit已经自动为我们创建了一个日志仓库，现在我们可以进行一些日志搜索和分析了。

![](http://docs.qiniucdn.com/data_logdb2.png)

在搜索条件框，我们选择一个时间范围为前24小时，然后点击搜索按钮，即可看到日志信息，共计1800条，以及最近100条的日志详情。

条件框中的 `*` 表示搜索所有日志。

![](http://docs.qiniucdn.com/data_logdb1.png)
![](http://docs.qiniucdn.com/data_logdb3.png)

在极速模式上点击左键可切换到详细模式，此处可以查看到日志时间分布的统计图。

![](http://docs.qiniucdn.com/data_logdb4.png)

我们输入一个条件来查询统计蜜蜂数量大于90的日志。

![](http://docs.qiniucdn.com/data_logdb5.png)

点击某一条日志的左侧 `>` 可以展开，查看日志详情。
![](http://docs.qiniucdn.com/data_logdb6.png)

以上是日志分析服务最基础的搜索用法；

更多用法，请参考产品详解 —— [日志分析服务](logdb.md)
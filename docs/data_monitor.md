**视频教程：**

<video id="video" controls="" preload="none" poster="http://onzeipdi1.bkt.clouddn.com/mp4-bg.png">
      <source id="mp4" src="http://docs.qiniucdn.com/monitor111.mov" type="video/mp4"></video>

</br>

### 1.部署Grafana

在控制台主页的最下方，我们可以找到Grafana应用，点击进入；
![](http://docs.qiniucdn.com/monitor3.png)

点击右上角的新建部署，待部署完成后，设置一个应用密码；
![](http://docs.qiniucdn.com/monitor4.png)

### 3.配置Grafana

点击访问Grafana，进入主页；
![](http://docs.qiniucdn.com/monitor5.png)

点击Add data source，添加一个数据源；
![](http://docs.qiniucdn.com/monitor6.png)

添加数据源页面，请按照下图内容输入；

Index name中输入仓库名称+日期格式：`[仓库名称-]YYYY.MM.DD`；

最后点击Save & Test按钮；
![](http://docs.qiniucdn.com/monitor77.png)

接下来开始制作实时监控图表；

点击左上角Grafana的logo，选择Dashboards-New；
![](http://docs.qiniucdn.com/monitor8.png)

在图表类型中我们选择Graph；
![](http://docs.qiniucdn.com/monitor9.png)

点击Panel Title，选择edit；

然后在Panel data source中我们选择刚刚配置的数据源；

![](http://docs.qiniucdn.com/monitor111.png)

然后在query中输入我们的Lucene Query语句，例如：`sensor: "sensor1"`表示只展示sensor1的数据，这样我们就可以监控所有sensor1的数据情况了。

![](http://docs.qiniucdn.com/monitor112.png)


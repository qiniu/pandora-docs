现在我们将上面准备的样例数据采集到Pandora平台的工作流当中。

**视频教程：**

<video id="video" controls="" preload="none" poster="http://onzeipdi1.bkt.clouddn.com/mp4-bg.png">
      <source id="mp4" src="http://docs.qiniucdn.com/collection_data.mov" type="video/mp4"></video>

</br>

### 1.下载并启动logkit

首先在 [下载中心](download_logkit.md) 下载最新版的logkit，下载完成后将其解压。
![](http://docs.qiniucdn.com/data_collection_logkit_1.png)
![](http://docs.qiniucdn.com/data_collection_logkit_2.png)

在命令行工具中输入以下命令启动logkit：
```
Linux & Mac 版本：
./logkit -f logkit.conf

Windows 版本：
./logkit.exe -f logkit.conf
```

![](http://docs.qiniucdn.com/data_collection_logkit_3.png)

在浏览器中输入`localhost:3000`进入logkit主页

!> 注意：如果您的3000端口被占用或想更换接口，请到 `logkit.conf` 中修改。

![](http://docs.qiniucdn.com/data_collection_logkit_4.png)

### 2.配置logkit

!> 注意：以下配置信息只列出了我们需要关心的内容，其他可选配置已被隐藏。

点击左上角的增加日志收集器来添加一个新的采集器；

![](http://docs.qiniucdn.com/data_collection_logkit_5.png)

在第一步中首先我们选择读取模式：`从文件中读取`；

然后输入日志文件路径：`/usr/local/action.log`；

填写完成后，直接点击下一步即可；

![](http://docs.qiniucdn.com/data_logkit1.png)

在第二步，首选选择解析方式：`csv`方式；

在csv格式字段&类型中填写以下内容：

```
collection_time date,butterflies long,honeybees long,location long,sensor string
```

点击下一步；

![](http://docs.qiniucdn.com/data_logkit_2.png)

第三步配置transformer我们直接跳过，点击下一步；

在七牛的公钥和私钥栏输入您的账号`公钥`和`私钥`；

?> 提示：账号公钥和私钥可以在七牛控制台的个人中心中的秘钥管理查看。

除了`是否自动创建并导出到Pandora LogDB`和`是否根据数据自动创建与增加字段`选项选择为true以外，其他所有选项选择为false；

点击下一步，然后确认并提交；

![](http://docs.qiniucdn.com/data_logkit_3.png)
![](http://docs.qiniucdn.com/data_logkit_4.png)

此时，您的数据已经发送到Pandora平台的工作流和日志分析服务之中。
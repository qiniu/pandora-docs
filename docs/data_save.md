**视频教程：**

<video id="video" controls="" preload="none" poster="http://onzeipdi1.bkt.clouddn.com/mp4-bg.png">
      <source id="mp4" src="http://docs.qiniucdn.com/save.mov" type="video/mp4"></video>

</br>

如果我们希望把原始数据或经过计算后的数据永久存储下来，那么可以使用七牛提供的对象存储服务，Pandora平台和对象存储服务内部互通，可直接将输入导出到对象存储之中。

首先进入工作流的编辑页面；

!> 注意：如果工作流正在运行中，请先停止，然后点击编辑。

拖拽一个对象存储导出节点，并将需要导出数据的节点和其连接起来；

然后填写节点内部信息，完成后保存并启动工作流，数据即可导出到指定地址。

![](http://docs.qiniucdn.com/data_save1.png)


如果您需要将数据通过公网传输出去，可以在工作流中找到HTTP服务节点；

在此节点中填写服务器地址与请求资源路径后启动工作流，即可将数据通过公网传输出去。

![](http://docs.qiniucdn.com/data_http1.png)
logkit-pro 是基于社区版logkit开发的一款面向企业的日志收集器管理平台，解决了开源logkit孤立运行无法统一管理的难题。

同时，我们为logkit-pro提供了更友好的一键部署、机器监控、收集器统一分发、标签管理、监控告警等功能，弥补了开源logkit在规模化后管理上的缺陷。

logkit-pro的目标是助力企业在每台机器只安装一个agent的基础上，解决机器管理和运维监控的难题。

## logkit-pro 官方网站

https://logkit-pro.qiniu.com

## 安装

选择【机器管理】-> 【添加机器】，点击【复制】，就可以获取我们提供的一键式安装脚本：

![](https://pandora-dl.qiniu.com/yijiananzhang.png)

对于`linux`或`mac`用户来说，直接在控制台粘贴并执行复制的命令即可。

对于`windows`用户，将复制的url在浏览器打开，将内容复制到文本文件中，并另存为 `logkit.vbs`，再双击执行 `logkit.vbs` 脚本安装。


## 机器管理

安装完成后在【机器管理】界面可以看到已经添加进来的机器。

![]((https://pandora-dl.qiniu.com/jiqiguanli.png)

在这里你不仅可以看到agent安装的各种状态，点击机器名称(hostname)，还能看到agent部署的机器各项指标，如下图所示。

![](https://pandora-dl.qiniu.com/jiqixiangqing.png)

一根中轴线贯穿各项指标，方便您进行对比。

## 配置收集器

选择【收集器管理】-> 【添加收集器模板】，您可以在这里配置您的日志收集器模板。

![](https://pandora-dl.qiniu.com/addconfig.png)

### 获取数据

第一步是配置读取的数据源，当您实际配置完成后，可以选择您添加的agent，点击【获取数据】，系统将使用您的配置，让agent去获取实际的数据。

![](https://pandora-dl.qiniu.com/getdata.png)

在这里，您可以根据实际的数据配置解析器，充分保障您的配置一定能在实际环境中生效。

### 解析数据

### 转换数据

### 发送数据

## 分发收集器

## 标签管理



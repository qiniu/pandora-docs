
## 产品简介

### logkit Pro 官方网站

[https://logkit-pro.qiniu.com](https://logkit-pro.qiniu.com)

### logkit Pro 概述

logkit Pro 是基于社区版 [logkit](https://github.com/qiniu/logkit/wiki) 开发的一款面向企业的日志收集器管理平台，解决了开源 logkit 孤立运行无法统一管理的难题。致力于让企业在每台机器只安装一个 agent 的基础上，解决机器管理和运维监控的难题。

除了基础的数据读取、解析、转换、发送等功能，logkit Pro 提供了更友好的一键部署、机器监控、收集器统一分发、标签管理、监控告警等功能，弥补了开源 logkit 在规模化后管理上的缺陷。

让我们一起先来快速体验 logkit Pro 方便的日志解析、转换和发送功能吧。

## 快速体验

您可以通过快速体验功能迅速开始体验 logkit Pro 的数据采集、解析与发送功能。

### 步骤1:配置数据源

进入 logkit Pro [快速体验](https://logkit-pro.qiniu.com/#/quickStart)页面。

![](https://pandora-kibana.qiniu.com/quick_start.png)

快速体验支持通过上传本地的日志文件、系统生成的随机数据、手动输入数据三种方式接入数据源。

1.直接上传本地文件，上传完毕点击`获取数据`，验证上传是否成功：

![](https://pandora-kibana.qiniu.com/upload_json.png)

2.使用`生成随机数据`功能，使用系统随机生成的数据开始您的收集数据体验之旅，其中，您可以修改随机数据来玩转 logkit Pro 收集数据的功能。

![](https://pandora-kibana.qiniu.com/quick_start1.png)

3.如果本地文件和随机数据都没有您想要体验的数据，可以通过手动输入数据快速开始体验：

![](https://pandora-kibana.qiniu.com/type.png)

logkit Pro 提`字符换行`和`正则换行`两种方式来规范您的数据源数据的格式：输入换行规则，点击`获取数据`，数据内容会按指定的格式显示。默认情况下是以换行符分隔数据。


### 步骤2:解析数据

根据数据源数据格式配置合适的解析方式来结构化数据内容, 方便后续在数据平台进行数据计算和分析。

logkit Pro 提供 json、csv、grok 等格式的数据解析，根据需要在左侧栏选择解析方式。

以 json 解析方式为例：

对上传的 json 格式文件，选择`按 json 格式解析`，

json 格式的解析无需任何配置，点击`解析样例数据`查看解析结果，验证您的配置的解析方式是否正确。您可以自行尝试其他数据格式的解析，感受 logkit Pro 强大便捷的一键解析功能。

![](https://pandora-kibana.qiniu.com/jsonparser.png)

### 步骤3:转换数据

灵活运用 logkit Pro 的转换数据功能，您可以实现更精细的字段解析。如将 ip 字符串扩展为 ip 对应的区域、城市、省份、运营商等信息，或者将指定字段解析为时间等。logkit Pro 支持包括字符串替换、ip 扩展、数组转换等多种数据变换方式，详情参考[转换数据](#转换数据)。

以字符串替换(rename)为例，rename 转换功能对字段进行重新命名：

![](https://pandora-kibana.qiniu.com/rename2.png)

选择进行变换的原字段名并填写新字段名，点击`添加`转换功能才会生效。点击`转化样例数据`，查看转换效果。您可以根据需求在同一个数据收集器里配置多个数据转换。

如果没有这一部分的需求，跳过这一步即可。

### 步骤4:发送数据

这一步可以将您的日志数据导入到七牛大数据平台，通过工作流任务的计算后，再将计算结果导出到您指定的数据分析产品中：日志仓库、时序数据库或者云存储。

![](https://pandora-kibana.qiniu.com/sendpandora.png)

### 查看数据

这样，您的数据就收集好啦！现在您可以去下游产品如日志仓库查看刚才收集的数据或者在工作流对数据进行计算。

![](https://pandora-kibana.qiniu.com/complete.png)

您已经体验了 logkit Pro 简单且有趣的数据收集功能，现在让我们全面了解 logkit Pro 。

## logkit Pro 工作流程

logkit Pro 通过如下图的工作流程实现对机器和收集器的统一管理。

![](http://p6ykx3lg2.bkt.clouddn.com/logkit_pro1.png)

1. 首先需要安装 agent  以便将您的机器添加上来，在机器上执行安装脚本即可。
2. 其次就是按照您的业务添加收集器，收集器添加的过程可以通过 agent 获取实际的业务数据，进行一系列校验。
2. 有了收集器，就可以将收集器分发到不同的机器上。
3. 针对不同的业务组，可以将机器设置不同的标签，收集器也可以针对标签统一发送。


## 用户指南

### 安装 logkit Pro 到机器

进入 [logkit Pro 官网](https://logkit-pro.qiniu.com)，点击【机器管理】-> 【添加机器】，进入安装页面，logkit Pro 提供自动录入和手动安装两种方式添加机器。

1.手动安装：点击【复制地址】，就可以获取我们提供的一键式安装脚本。

在安装之前，您可以为机器指定标签来统一管理机器。

![](https://pandora-kibana.qiniu.com/add_machine.png)

对于`linux`或`mac`用户来说，直接在控制台粘贴并执行复制的命令即可。

对于`windows`用户，将复制的url在浏览器打开，将内容复制到文本文件中，并另存为 `logkit.vbs`，再双击执行 `logkit.vbs` 脚本安装。

![](https://pandora-kibana.qiniu.com/logkit_prostart.png)

2.自动录入：输入机器 ip、ssh 端口、用户信息等在服务器上安装 Agent。
![](https://pandora-kibana.qiniu.com/auto_add.png)

安装好以后您可以在【机器管理】列表看到机器的相关信息，如机器所属标签、机器里的所有收集器、机器地址等，详细信息阅读[机器管理](#机器管理)。

### 收集数据

在机器上安装好 logkit Pro 之后就可以开始收集数据。进入数据收集页面，点击**添加收集器**开始配置收集器。

![](https://pandora-kibana.qiniu.com/add_collector1.png)

#### 获取数据

第一步是接收数据源，logkit Pro 支持多种数据来源和读取模式，根据需要在左侧列表选取。

填写好配置信息之后，选择您添加的 agent，点击【获取数据】，系统将使用您的配置，让 agent 去获取实际的数据，并且展示样例数据以验证您的配置是否正确。

![](https://pandora-kibana.qiniu.com/add_datasource.png)

默认不需要配置高级选项，高级选项保持默认值即可。

**logkit Pro 支持的采集数据源**

* File: 读取文件中的日志数据，包括 csv 格式的文件，kafka-rest 日志文件， nginx 日志文件等，并支持多种读取模式（fileauto、dir、file、tailx）
* MySQL: 读取 MySQL 中的数据。
* MSSQL: 读取 Microsoft SQL Server中的数据。
* Postgre SQL: 读取 PostgreSQL 中的数据。
* ElasticSearch: 读取 ElasticSearch 中的数据。
* MongoDB: 读取 MongoDB 中的数据。
* Kafka: 读取 Kafka 中的数据。
* Redis: 读取 Redis 中的数据。
* Socket: 读取 tcp\udp\unixsocket 协议中的数据。
* Http: 作为 http 服务端，接受 POST 请求发送过来的数据。
* Script: 支持执行脚本，读取执行结果中的数据。
* Snmp: 主动抓取 Snmp 服务中的数据。
* AWS CloudWatch: 主动抓取 AWS CloudWatch接口中的数据。
* AWS CloudTrail: 主动抓取 AWS CloudTrail中的数据。

#### 解析数据

第二步，您需要根据实际需要配置解析方式，抽取数据中的字段，转化为结构化数据，充分保障您的配置一定能在实际场景中生效。

* 按 json 格式解析：通过 json 反序列化解析日志内容，无需任何配置，系统根据日志内容自动解析。

![](https://pandora-kibana.qiniu.com/jsonparser.png)

* 按 grok 格式解析：通过配置 grok pattern 解析，将文本格式的字符串转化为结构化的数据。使用 grok 解析日志内容会使得日志分析更加容易。
 
1.默认 grok 表达式：

使用系统默认 grok pattern 来解析您的数据：

![](https://pandora-kibana.qiniu.com/grok_default.png)

2.如果默认 pattern 解析没法满足您的需求，logkit Pro 支持自定义 grok pattern。

**grok划词**

通过在数据上划词解析数据：在数据上点选数据内容，系统会自动匹配 grok 变量生成 pattern,字段名、字段类型与 grok 变量均可编辑。通过划词生成的 pattern 以及 划词解析后的数据均可见。

![](https://pandora-kibana.qiniu.com/huaci.png)

如果部分数据内容无法全部用划词解析，您还可以编辑 pattern。

![](https://pandora-kibana.qiniu.com/custom_grok1.png)

点击验证即查看解析结果。

您也可以直接输入自定义 pattern。

![](https://pandora-kibana.qiniu.com/custom.png)

* 按 csv 格式解析：系统根据分割符自动解析，生成默认字段名与字段内容一一对应，字段名与字段类型可修改。

![](https://pandora-kibana.qiniu.com/csv_analysis.png)

* 按 syslog 格式解析: 自动解析系统日志

![](https://pandora-kibana.qiniu.com/syslog.png)

其他解析方式：

* 按原始日志逐行发送：直接按行读取日志内容。
* 按 nginx 日志解析：按 nginx 日志格式解析
* 七牛日志库格式解析: 七牛开源的 golang 日志库日志格式解析
* kafkarest 日志解析: Kafkarest 日志解析
* 通过解析清空数据：清空读取的数据
* mysqllog: mysql慢请求日志解析

[点此](https://github.com/qiniu/logkit/wiki/Parsers)了解更多 logkit Pro 数据解析功能。

#### 转换数据

logkit Pro 提供数据转换功能来满足一些更精细的字段解析需求。

* urlparam: 将指定字段的内容按照 url 参数的格式转换为键值对。
![](https://pandora-kibana.qiniu.com/urlparam.png)

* arrayexpand: 将数组字段展开并转换为键值对。
![](https://pandora-kibana.qiniu.com/arrayexpand.png)

* convert: 转换数据格式。点击`新增转化字段`，选择需要转换的字段名给它指定字段类型即可完成数据格式的转换，也可手动书写 dsl 描述将数据类型转换为指定的格式。
![](https://pandora-kibana.qiniu.com/convert1.png)

* discard: 将指定字段的数据抛弃，可以同时选择多个字段一键删除。
![](https://pandora-kibana.qiniu.com/discard.png)

* date: 将字段解析为时间并做一些转换。
![](https://pandora-kibana.qiniu.com/datetransform.png)

* trim: 去掉字符串多余的字符。
![](https://pandora-kibana.qiniu.com/trim.png)

* json: 将一个符合json格式的字符串字段，反序列化为对应结构体类型。
![](https://pandora-kibana.qiniu.com/json_transformer.png)

* rename: 将字段名称重命名，解决不同下游系统对字段名称中特殊字符不支持的问题。 
 ![](https://pandora-kibana.qiniu.com/rename2.png)
 
* split: 将指定字段的数据切分为字符串数组。
![](https://pandora-kibana.qiniu.com/split.png)

* xml: 将一个符合xml格式的字符串字段，反序列化为对应 map[string]interface{} 结构体类型。
![](https://pandora-kibana.qiniu.com/xml.png)

其他字段转换功能：

* script: 执行脚本文件，并将脚本结果加入到数据中。
* pandora_key_convert: 将不符合 Pandora 字段命名规则的 key 字符转化为下划线。
* cloudtrail: 针对 AWS CloudTrail 的数据做格式转换，将 CloudTrail 的 json 格式中的 Records 逐条变为数据。
* IP: 针对ip做运营商信息字段扩展。
* replace: 替换原有的字符串内容。
* UserAgent: 浏览器中的user agent信息解析，可以解析出包括设备、操作系统、版本号在内的多种信息。
* k8stag: 自动获取 k8stag。
* label: 增加标签，添加一个带有固定值的字段到数据中。

[点此](https://github.com/qiniu/logkit/wiki/Transformers)了解更多 logkit Pro 转换数据功能。

#### 发送数据

logkit Pro 支持多种数据发送平台如七牛大数据平台、MongoDB、InfluxDB 等，根据需要在左侧列表选取。完成这一步就可以在数据平台进行后续数据计算、分析、查看。典型地，发送数据至七牛大数据平台后，数据可以在`workflow`进行数据计算和导出。

![](https://pandora-kibana.qiniu.com/SEND2.png)

**logkit Pro 支持的数据发送平台**

* Pandora Sender: 发送到七牛大数据处理平台(Pandora)。
* File Sender: 发送到本地文件。
* MongoDB Accumulate Sender: 聚合后发送到 MongoDB 服务。
* InfluxDB Sender: 发送到 InfluxDB 服务。
* 消费数据但不发送：不执行发送行为。
* Elasticsearch Sender: 发送到 ElasticSearch 服务。
* Kafka Sender: 发送到 Kafka 服务。
* Http Sender: 以 Http POST 请求的方式发送数据。

配置收集器操作案例可参考[快速体验](#快速体验)。

### 分发收集器

收集器配置好之后，将收集器分发到指定的机器实现收集器的统一管理。

点击【数据收集】->【收集器】进入收集器的分发和编辑。

![](https://pandora-kibana.qiniu.com/add_collector2.png)

点击`分发`，可以将收集器分发到机器或者直接分发到标签。

![](http://p6ykx3lg2.bkt.clouddn.com/collector_distri.png)

点击`编辑`，可以更新收集器配置。

### 查看收集器运行信息

完成收集器的分发后，收集器就在机器成功运行。点击【数据收集】->【运行信息】即可查看收集器的运行信息，如运行时间、读取成功条数、解析成功条数、错误日志等，结合`详细配置`信息，及时查错。在这里您可以对每个收集器进行`停止`、`重启`、`删除`操作。

![](https://pandora-kibana.qiniu.com/runner2.png)

### 机器管理

通过【机器管理】对机器运行状态进行监控。在【机器管理】界面可以看到已经添加进来的机器列表，显示机器地址、机器状态（在线、离线）等、该机器下的收集器等信息。

![](https://pandora-kibana.qiniu.com/machine_manage.png)

在右上角的`显示选项`勾选您感兴趣的显示信息。默认情况下，收集器信息不显示，需要手动勾选。

![](https://pandora-kibana.qiniu.com/machine_manage3.png)

logkit Pro 支持对机器性能进行监控，点击右上角【启动机器监控】，输入数据收集时间间隔与数据源名称收集机器性能信息并保存到 repo。

![](https://pandora-kibana.qiniu.com/monitor_machine.png)

启动机器监控以后，点击机器名称即可直接看到机器性能数据。

![](https://pandora-kibana.qiniu.com/machine_info.png)

### 标签管理

通过标签管理功能来对同一个业务下的机器统一管理。在机器管理页面，点击`添加标签按钮`将指定机器添加到已有标签。

![](https://pandora-kibana.qiniu.com/add_labelll.png)

![](https://pandora-kibana.qiniu.com/add_label1.png)

在弹出的添加标签窗口点击`标签管理`，可进入标签管理页面，对标签进行编辑、删除、创建标签操作。

![](https://pandora-kibana.qiniu.com/labelmanagement.png)

您也可以在标签管理页面对指定标签添加机器来批量管理机器。

![](https://pandora-kibana.qiniu.com/label_add.png)

现在开始玩转您的 logkit Pro 吧！






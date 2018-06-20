# 基于 syslog 的数据中心日志统一化管理实践指南

## 场景介绍

如今大数据、人工智能不断被提及，企业越来越意识到数据的价值。企业自身面对每天产生数以亿计的数据，如何管理成为一大难题。仅从业务日志这部分数据来看，每天散落在服务器上的不同日志就可以为你提供大量价值，如服务性能、机器性能、安全审计、问题排查等等。可见，收集这些业务日志、机器数据，并构建一个可以利用这些数据的平台，不仅有价值，而且非常必要。

而实际上，利用如rsyslog、logkit这样的开源软件，以及Pandora大数据平台，就可以轻松做到这一点： 收集、转换、存储、分析你的日志数据。

本文将介绍如何利用 rsyslog 在每台机器上收集你的业务数据，并通过 logkit 接收，最终通过Pandora中心化管理你的业务日志。


## 目标

本场景主要教大家如何将syslog统一管理，主要针对比较知名的一款syslog管理软件，即 `rsyslog`。 众所周知，syslog，或者说基于Syslog协议的软件(如rsyslog)，在开源的类Unix系统中是非常知名的，它会自动收集内核及一些核心软件的服务日志，以确保系统正常运行。 syslog作为一种[rfc标准](https://tools.ietf.org/html/rfc5424), 不仅支持内核程序的数据，同样也支持其他上层的业务软件向其发生数据。正是基于此，我们可以通过对syslog的统一收集，做到日志数据的统一管理。所以程序统一向syslog发送数据，而我们提供中心化管理syslog，就可以更简单的实现安全审计、应用程序监控、以及追踪其他重要数据。

对于一台机器来说，我们可以将该机器上所有业务产生的日志统一发送到syslog服务，然后我们只需要将 syslog 日志转发到 logkit，最后通过logkit发送到Pandora。 以此类推，就可以实现完整的数据中心日志统一化管理。本文的最后，我们将会介绍如何利用Pandora实现安全审计、问题排查、服务监控等功能。

我们一共分三部分进行介绍：

1. `rsyslog 客户端` 配置介绍，它会将本地的一些系统日志收集发送给`logkit`，也可以作为一个代理接收本机的业务程序发来的日志，并转发到 `logkit`。
2. `logkit` 配置介绍，一方面，logkit 接收 rsyslog 发来的数据，另一方面，logkit会将数据发送到Pandora。
3. 利用 `Pandora` 对数据中心日志统一管理。

## 准备工作

1. 准备一台机器，安装logkit, logkit机器有一个web界面可以用于配置和监控，默认使用的端口为 `3000`，请确保开启；为了接收syslog日志，还需要开启 `5140` 端口（该端口可以改为其他端口，配置中也相应变更即可）， rsyslog-client需要能访问logkit机器的 `5140` 端口。
2. 准备一台机器(假设为Ubuntu14.04)，安装rsyslog-client；最好再部署一个能将日志发送到rsyslog的业务程序（可选）。
3. rsyslog-client 和 logkit 确保网络联通。
4. logkit所在机器可以访问外网 https://pipeline.qiniu.com （私有部署没有这个要求）

## 开始配置

做好上述准备工作后，我们开始完整的实践。

### 第一步，安装logkit

```
wget https://pandora-dl.qiniu.com/logkit_v1.5.0.tar.gz
tar xzf logkit_v1.5.0.tar.gz
cd _package
```

可以看到_package文件夹下包含如下内容

```
$ ls
logkit  logkit.conf  public  stats  template_confs
```

若需要修改logkit对外的web界面使用端口，请打开 logkit.conf 对应更改其中的3000端口

```
vi logkit.conf
```

假设修改为 `30001`

```
{
	"max_procs": 1,
	"debug_level": 0,
	"clean_self_log":true,
	"bind_host":":30001",
	"disable_web":false,
	"static_root_path":"./public",
	"confs_path": ["confs*"]
}
```

修改完成，保存启动：

```
nohup ./logkit -f logkit.conf > logkit.log 2>&1 &
```

此时logkit已经启动，可以在浏览器访问。

![syslog1](https://pandora-dl.qiniu.com/syslog1.png)

### 第二步，选择监听的IP和端口

logkit需要接收rsyslog发送过来的日志，所以要确保logkit和rsyslog的网络连通性，只要 rsyslog 能访问 logkit 即可，如果logkit有多个IP，为了确保安全性，我们尽可能选择rsyslog机器能访问到的内网IP。

查看IP的方法, `ifconfig -a`

可能获得的结果如下：

```
eth0      Link encap:Ethernet  HWaddr 04:01:06:a7:6f:01  
          inet addr:101.46.8.0  Bcast:123.456.78.255  Mask:255.255.255.0
          inet6 addr: fe80::601:6ff:fea7:6f01/64 Scope:Link
          UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
          RX packets:168 errors:0 dropped:0 overruns:0 frame:0
          TX packets:137 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000 
          RX bytes:18903 (18.9 KB)  TX bytes:15024 (15.0 KB)

eth1      Link encap:Ethernet  HWaddr 04:01:06:a7:6f:02  
          inet addr:10.100.20.41  Bcast:10.128.255.255  Mask:255.255.0.0
          inet6 addr: fe80::601:6ff:fea7:6f02/64 Scope:Link
          UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
          RX packets:6 errors:0 dropped:0 overruns:0 frame:0
          TX packets:5 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000 
          RX bytes:468 (468.0 B)  TX bytes:398 (398.0 B)

lo        Link encap:Local Loopback  
          inet addr:127.0.0.1  Mask:255.0.0.0
          inet6 addr: ::1/128 Scope:Host
          UP LOOPBACK RUNNING  MTU:16436  Metric:1
          RX packets:0 errors:0 dropped:0 overruns:0 frame:0
          TX packets:0 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:0 
          RX bytes:0 (0.0 B)  TX bytes:0 (0.0 B)
```

可以看到 eth0 设备为外网可访问的IP，而 eth1 为内网IP，若rsyslog可以访问 10.200.20.41，那就选择内网IP， 通过`ping`命令可以验证网络联通性。

```
$ping 10.200.20.41
sun@lcoal:~/logkit_test/_package$ ping 10.200.20.41
PING 10.200.20.41 (10.200.20.41) 56(84) bytes of data.
64 bytes from 10.200.20.41: icmp_seq=1 ttl=64 time=0.122 ms
64 bytes from 10.200.20.41: icmp_seq=2 ttl=64 time=0.105 ms
^C
--- 10.200.20.41 ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 999ms
rtt min/avg/max/mdev = 0.105/0.113/0.122/0.013 ms
```

如上所示表示网络可以联通，我们将 `10.200.20.41` 选做我们监听的IP地址，而 `5140` 作为监听的端口（`514`一般为rsyslog server默认的监听端口，我们不使用514一方面是不与服务端本身可能存在的rsyslog的514冲突，另外一方面也是选用一个非常用端口，避免权限不够，logkit此处使用 `5140`作为示例，也可以配置为别的端口）。

### 第三步，配置logkit收集数据

回到浏览器界面，选择 `【增加日志采集收集器】`

![syslog2](https://pandora-dl.qiniu.com/syslog2.png)

如上图所示，在 `socket监听的地址` 一栏填入我们之前确定好的IP和端口内容，协议可以使用udp，也可以使用tcp，一般syslog使用udp协议传输，对于重要的数据，建议使用tcp。

```
udp://10.200.20.40:514
```

下一步，配置解析方式

![syslog3](https://pandora-dl.qiniu.com/syslog3.png)

默认的syslog日志选择按syslog格式解析即可，**但是需要注意的是，对于应用程序自己定义的日志格式，默认的syslog格式无法解析，可以根据实际情况选择别的解析方式**

继续下一步，直到配置发送方式

![syslog4](https://pandora-dl.qiniu.com/syslog4.png)

如图所示，填写您的 `工作流名称`，`数据源名称`，`七牛公钥`以及`七牛私钥`四项，其他都可以不作修改。

其中 `工作流名称`，`数据源名称` 可以根据需要自定义一个名称，如 `myworkflow` 以及 `mysyslog`

而`七牛公钥`和`七牛私钥` 可以在[七牛官网](https://portal.qiniu.com)的【个人面板】-> 【秘钥管理】页面获得，如下图所示。

![syslog5](https://pandora-dl.qiniu.com/syslog5.png)

配置好后，点击【确认并提交】。

提交后，可以看到logkit的首页有一个runner在运行。

![syslog6](https://pandora-dl.qiniu.com/syslog6.png)


### 第四步，安装rsyslog

默认情况下，机器应该已经安装了rsyslog，如果还没有安装，可以执行如下命令安装：

```
sudo apt-get install rsyslog
```

安装好以后，你会在系统的目录中看到文件夹 `/etc/rsyslog.d/`

其中包含2个文件：

```
sun@local:/etc/rsyslog.d$ ls
20-ufw.conf  50-default.conf
```

### 第五步，配置rsyslog发送

打开 `sudo vi /etc/rsyslog.d/50-default.conf`，增加一行发送的配置。

```
*.*                         @10.200.20.41:5140
```

如图所示：

![syslog7](https://pandora-dl.qiniu.com/syslog7.png)

其中 `*.*` 表示我们会发送所有的数据，当然，你也可以配置rsyslog只发送某些你关心的数据，关于如何配置rsyslog有策略性的发送数据，可以参阅rsyslog相关文档，在这里我们不多做展开。

剩下的部分表示发送到哪里， `@` 符号表示用 UDP 协议传输，如果使用 TCP协议，可以改为使用 `@@` 2个符号替换，当然，如果替换，那么logkit端也要对应替换为tcp接收数据。
紧接着`@`符号，是我们刚刚确认的logkit所在机器的一个内网IP，在我们这个例子中是 `10.200.20.41`，请根据你的实际情况修改，最后是端口号，也与logkit那边配置的保持一致，在我们的例子中是`5140`。

配置完成后，保存配置，并重启 rsyslog

```
service restart rsyslog
```

至此，我们的配置就全部完成了，你可以在logkit的页面看到数据接收和发送的情况。

### 第六步，查看logkit接收和发送的状态

![syslog8](https://pandora-dl.qiniu.com/syslog8.png)

可以看到，数据正在源源不断的收集并发送出去。


## 在七牛云智能日志管理平台中查看日志

登录 [七牛云智能日志管理平台](https://portal.qiniu.com/pandora/logdb) 查看日志

logkit配置时输入的数据源名称，会出现在日志仓库中，我们选择并搜索，查看点击
![syslog9](https://pandora-dl.qiniu.com/syslog9.png)

默认情况下，系统发送的syslog日志就自带一些信息，如 标签(tag),设备(facility),优先级(priority),重要性(severity)等等，其他日志都在`content`字段内。

如我们要搜一个 `sudo` 基本的日志，并且优先级(priority)为 `86` 的日志，搜索结果如下。

![syslog10](https://pandora-dl.qiniu.com/syslog10.png)
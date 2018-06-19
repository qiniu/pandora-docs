使用C SDK 进行数据上报

现在移动端及物联网终端越来越普及，大家都有自己的APP，想要把数据从终端上传上来进行分析。有不少底层代码是用C/Cpp写成，想上报数据，Pandora 有现成的SDK 提供使用。比如播放器的播放行为上报，监控设备使用情况上报，等等。

Github地址：[SDK-C](https://github.com/qiniu/pandora-c-sdk)

接入步骤：
* 源代码下载下来进行使用，只依赖libcurl

* 根据账号 AK，SK 生成上报数据用的 Token，也可以用[签名生成工具](https://qiniu.github.io/pandora-docs/#/akutil)。

* 在[七牛工作流引擎控制台](https://portal.qiniu.com/pipeline) 按照需要的字段创建好对应的工作流，记住 repo 名字，后面需要用到，具体操作详见 [workflow 文档](https://qiniu.github.io/pandora-docs/#/workflow)

* 代码示例:[使用C SDK 进行数据上报](https://github.com/qiniu/pandora-c-sdk/blob/master/test/pandora_curl_test.cpp) 

```
  PANDORA_Points* points = pandora_points_new();
    pandora_points_add_long(points, "ll", 999988887L);
    pandora_points_add_string(points, "str", "hello");
    pandora_points_add_float(points, "dbl", 999.99);
    pandora_points_add_boolean(points, "bool", 0);
    pandora_points_add_time(points, "tm", time(NULL));
    pandora_points_newline(points);
    pandora_points_add_long(points, "ll", 888888L);
    pandora_points_add_string(points, "str", "llo");
    pandora_points_add_float(points, "dbl", 666.66);
    pandora_points_add_boolean(points, "bool", 1);
    pandora_points_add_time(points, "tm", time(NULL));
    pandora_points_newline(points);
    PANDORA_curl_send("csdk", 
        "token...", 
        points);

pandora_points_delete(points);
```
具体数值字段支持long，double，time，Bool, 字符串. 可以一次上传单条或者多条数据。

如果想上传文件，请按照如下步骤来操作：

* 前两步相同

* 在七牛实时日志上传这里进行文件上传, 具体参考 [logdb 文档](https://qiniu.github.io/pandora-docs/#/logdb)，将文件中的字段都解析出来。如SDK 中带的syslog 这种格式的文件都可以

* 如果文件需要滚动处理建议使用系统的日志滚动配置，但文件不能进行压缩，或者使用 https://github.com/logrotate/logrotate 编译后的命令。另外一种方案是参考logrotate的代码自己进行定时的日志滚动

* 代码示例：
```
PANDORA_CURL_CODE c = PANDORA_curl_send_file("syslog", 
        "Pandora 0knikszBTnSnfqhcWLw1yvunjhqweRYSV2GlLbrP:_WQP2SeErz10br9Q5tCzyEN1qHQ=:eyJyZXNvdXJjZSI6Ii92Mi9yZXBvcy9zeXNsb2cvZGF0YSIsImV4cGlyZXMiOjQ2ODE5NDE1NDYsImNvbnRlbnRNRDUiOiIiLCJjb250ZW50VHlwZSI6InRleHQvcGxhaW4iLCJoZWFkZXJzIjoiIiwibWV0aG9kIjoiUE9TVCJ9", 
        "./x.log");
```
log文件示例：
```
Jan  1 00:00:22 NiuDemo syslog.info syslogd started: BusyBox v1.22.1
Apr  2 10:27:05 NiuDemo daemon.info button[116]: (116) main L69: Run in daemon
Apr  2 10:27:05 NiuDemo daemon.debug button[116]: (116) KeyEvent_Init L69: _gpio_proc_fd:6
Apr  2 10:27:05 NiuDemo daemon.info button[116]: (116) RunShellInSubProcessFile L14: run shell: /usr/button/debug_mode_monitor
Apr  2 10:27:05 NiuDemo daemon.debug button[117]: (117) KeyEvent_Release L77: _gpio_proc_fd:6
Apr  2 10:27:05 NiuDemo daemon.debug button[116]: (116) main L82: _child_pid:117
Jun  4 19:14:49 NiuDemo daemon.debug proxy_system_daemon[199]: main L37: proxy_execute_daemon started.
Jun  4 19:14:49 NiuDemo daemon.debug proxy_system_daemon[204]: proxy_execute_thread L22: thread start, queue=0x413018
Jun  4 19:14:49 NiuDemo daemon.err telnetd[202]: bind: Address already in use
Jun  4 19:14:49 NiuDemo 
```

上传之后的数据查询可以直接在日志仓库直接进行查询， 以及使用 Grafana 进行展示、监控和告警。





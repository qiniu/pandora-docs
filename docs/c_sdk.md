使用C SDK 进行数据上报

现在移动端及物联网终端越来越普及，大家都有自己的APP，想要把数据从终端上传上来进行分析。有不少底层代码是用C/Cpp写成，想上报数据，Pandora 有现成的SDK 提供使用。比如播放器的播放行为上报，监控设备使用情况上报，等等。


Github地址：[SDK-C](https://github.com/qiniu/pandora-c-sdk)

接入步骤：
* 源代码下载下来进行使用，只依赖libcurl

* 在[七牛工作流引擎控制台](https://portal.qiniu.com/pipeline) 按照需要的字段创建好对应的工作流，记住 repo 名字，后面需要用到，具体操作详见 [workflow 文档。](https://qiniu.github.io/pandora-docs/#/workflow)

* 根据账号 AK，SK 生成上报数据用的 Token，也可以用[签名生成工具](https://qiniu.github.io/pandora-docs/#/akutil)。

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
可以直接上传NSDictionary。具体数值字段支持long，double，time，Bool, 字符串. 可以一次上传单条或者多条数据。

上传之后的数据查询可以直接在日志仓库直接进行查询， 以及使用 Grafana 进行展示、监控和告警。



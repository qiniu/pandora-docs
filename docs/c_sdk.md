使用C SDK 进行数据上报

现在移动端及物联网终端越来越普及，大家都有自己的APP，想要把数据从终端上传上来进行分析。有不少底层代码是用C/Cpp写成，想上报数据，Pandora 有现成的SDK 提供使用。比如播放器的播放行为上报，监控设备使用情况上报，等等。


代码地址在：https://github.com/qiniu/pandora-c-sdk

接入步骤：
* 源代码下载下来进行使用，只依赖libcurl

* 在portal.qiniu.com 按照需要的字段创建好对应的工作流，记住repo名字，后面用到，具体操作详见 https://qiniu.github.io/pandora-docs/#/workflow

* 根据账号AK，SK 生成上报数据用的Token，也可以用 https://qiniu.github.io/pandora-docs/#/akutil 这里的工具生成

* 参考样例代码 
https://github.com/qiniu/pandora-c-sdk/blob/master/test/pandora_curl_test.cpp


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

上传之后的数据查询可以直接在 https://qiniu.github.io/pandora-docs/#/logdb 直接进行查询， 也可以使用 https://qiniu.github.io/pandora-docs/#/keywordalert?id=grafana-%E7%9B%91%E6%8E%A7%E5%92%8C%E5%91%8A%E8%AD%A6 进行展示，监控和告警



使用Object-C SDK 进行数据上报

现在移动端越来越普及，大家都有自己的APP，想要把数据从终端上传上来进行分析。Pandora 有现成的SDK 提供使用。比如播放器的播放行为上报，App使用情况上报，等等。


Panddora 上报SDK已经集成到存储SDK中，可以直接使用。支持Mac和iOS
代码地址在：https://github.com/qiniu/objc-sdk/tree/master/QiniuSDK/BigData

接入步骤：
* 通过 CocoaPods `pod "Qiniu", "~> 7.2" `

* 在portal.qiniu.com 按照需要的字段创建好对应的工作流，记住repo名字，后面用到，具体操作详见 https://qiniu.github.io/pandora-docs/#/workflow

* 根据账号AK，SK 生成上报数据用的Token，也可以用 https://qiniu.github.io/pandora-docs/#/akutil 这里的工具生成

* 参考样例代码 
https://github.com/qiniu/objc-sdk/blob/master/QiniuSDKTests/QNPipelineTest.m


```
  NSDictionary *dict = @{ @"platform" : @"ios",
                            @"tl" : @1L,
                            @"tf" : @1.5,
                            @"tb" : @YES,
                            @"td" : [NSDate new] };

    QNPipeline *pipe = [[QNPipeline alloc] init:nil];
    [pipe pumpRepo:@"testsdk" event:dict token:@"token..." handler:^(QNResponseInfo *info) {

    }];
```
可以直接上传NSDictionary。具体数值字段支持long，double，NSDate，Bool, NSString. 可以一次上传单条或者多条数据。

上传之后的数据查询可以直接在 https://qiniu.github.io/pandora-docs/#/logdb 直接进行查询， 也可以使用 https://qiniu.github.io/pandora-docs/#/keywordalert?id=grafana-%E7%9B%91%E6%8E%A7%E5%92%8C%E5%91%8A%E8%AD%A6 进行展示，监控和告警



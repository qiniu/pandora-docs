## logkit 管理用户 API

用户 API 允许通过 Token 或 AK/SK 鉴权， Token的生成方式与数据推送相同。

## Agents

### 获取 agent 列表

```
GET /v1/api/agents?tag=<tagValue>&id=<idValue>&state=<stateValue1,stateValue2>&sort=<sortKey>&order=<order>&page=<pageNum>&size=<pageSize>&search=<search>
```

#### 返回值

- 如果没有错误, 返回：
```
{
    "agentList":[
    {
        "id": "<agent_id>",
        "ip":"10.10.0.1",
        "tags":["tag1", "tag2"],
        "hostname":"nb110",
        "create_time": <timestamp>,
        "state": "<state>",
        "runners": ["runner1", "runner2"],
        "machine": {
            "hostname": "<hostname>",
            "boot_time": <启动时间>,
            "kernel": "<内核类型>",
            "kernel_version": "<内核版本>",
            "platform": "<平台类型>",
            "platform_version": "<平台版本>",
            "cpu": {
                "model": "<cpu_model_name>",
                "Mhz": <主频, 单位 Mhz>,
                "Cache": <二级缓存大小>,
                "Cores": <cpu 核数>,
                "count": <cpu 个数>
            },
            "memory": <内存大小>,
            "interface": [ //网卡信息
                {
                    "name": "<名称>",
                    "mtu": <最大数据包大小(字节)>,
                    "mac": "<Mac 地址>",
                    "ip": [
                        "<ip1>",
                        "<ip2>",
                        ...
                    ],
                    "mask": [
                        "<子网掩码1>",
                        "<子网掩码2>",
                        ...
                    ] // 子网掩码的与 ip 一一对应
                },
                ...
            ],
            "disk": [
                {
                    "device": "<名称>",
                    "fstype": "<文件系统类型>",
                    "mount": "<挂载点>",
                    "size": <容量>
                },
                ...
            ]
        }
    },
    ...
    ],
    "totalSize":<total_size>
}
```
- 如果有错误则返回相应错误

#### 参数说明

- 参数 `tag`、`id` 和 `state` 非空时将作为被操作 agent 的过滤条件，即上述操作只对满足对应条件的 agent 有效
- 参数 `sort`、`order` 会根据 `sort` 的值进行升序/降序排序，`order` 的值可以是 ascend/descend，`sort` 默认为 hostname，`order` 默认为 ascend
- 参数 `search` 会对 hostname 进行搜索，将包含 `search` 值的结果搜索出来
- status 状态有三种，`ok` 表示正常（30s 内有联系）；`bad` 表示 1 分钟内有联系；`lost` 表示超过 1 分钟无心跳
- 如果需要分页则提供当前页数 `page` 以及每页的数量 `size`
- 状态有三种:
    - `register`: 注册了但是还没有发第一次心跳
    - `online`: 与 tcpserver 保持连接时
    - `offline`: 与 tcpserver 断开连接时

### 删除 agents

```
DELETE /v1/api/agents?tag=tagValue&id=idvalue&state=stateValue
```

#### 返回值

- 如果没有错误，返回空
- 如果有错误则返回相应错误

#### 参数说明

- 参数 `tag`、`id` 和 `state` 非空时将作为被操作 agent 的过滤条件，即上述操作只对满足对应条件的 agent 有效
- 执行该操作进行后，agent 必须重启才能重新注册到 server 端

### 删除多个 agent

```
DELETE /v1/api/agents/batch?id=<agent_id1>,<agent_id2>
```

#### 返回值

- 如果没有错误，返回空
- 如果有错误则返回相应错误

### 为 agent 添加标签

```
POST /v1/api/agents/<agent_id>/tags/<tag_name>
```

#### 返回值

- 如果没有错误，返回空
- 如果有错误则返回相应错误

### 为 agent 添加多个标签

```
POST /v1/api/agents/<agent_id>/tags
Content-Type: application/json
{
   "tags": ["<tag_name1>","<tag_name2>"]
}
```

#### 返回值

- 如果没有错误，返回空
- 如果有错误则返回相应错误

### 删除 agent 标签

```
DELETE /v1/api/agents/<agent_id>/tags/<tag_name>
```

#### 返回值

- 如果没有错误，返回空
- 如果有错误则返回相应错误

### 获取 release 的最新信息

```
GET /v1/api/release
```

#### 返回值

```
[
    {
        "os": "<os>",
        "arch":"<arch>",
        "hash":"<hash>",
        "message":"<message>",
        "version":"<version>",
        "package":"<package>",
        "download_url":"<download_url>",
        "version_stamp":"<version_stamp>"
    }
    ...
]
```

### 升级 agent

```
POST /v1/api/agents/upgrade
Content-Type: application/json
{
    "agent_ids": [],
    "tags": []
}
```

## Configs

### 获取 config 列表

```
GET /v1/api/configs?name=<tagName>&sort=<sortKey>&order=<order>&page=<pageNum>&size=<pageSize>&search=<search>
```

#### 返回值

- 如果没有错误, 返回：

```
{
    "configs":[
        {
            "name": "<config_name>",
            "tags": ["tag1", "tag2"],
            "config": {},
            "note": "<config 备注>",
            "timestamp": <timestamp>
        },
        ...
    ],
    "totalSize":<total_size>
}
```
- 如果有错误则返回相应错误

#### 参数说明

- 参数 `sort`、`order` 会根据 `sort` 的值进行升序/降序排序，`order` 的值可以是 ascend/descend，`sort` 默认为 hostname，`order` 默认为 ascend
- 参数 `search` 会对 hostname 进行搜索，将包含 `search` 值的结果搜索出来

### 添加 config

```
POST /v1/api/configs/<config_name>
Content-Type: application/json
{
   "name": "<config_name>",
   "tags": "["tag1", "tag2"]",
   "config": <runner_config>,
   "note": "<config 说明>"
}
```

#### 返回值

- 如果没有错误，返回空
- 如果有错误则返回相应错误

#### 参数说明

- `name` 字段可有可无，最终会被链接中的 `<config_name>` 替换掉

### 更新 config

```
PUT /v1/api/configs/<config_name>/config
Content-Type: application/json
{
   "config": <runner_config>,
   "note": "<config 说明>"
}
```

#### 返回值

- 如果没有错误，返回空
- 如果有错误则返回相应错误

#### 参数说明

- 当前仅支持更新 `config` 和 `note` 字段

### 删除 config

```
DELETE /v1/api/configs/<config_name>
```

#### 返回值

- 如果没有错误，返回空
- 如果有错误则返回相应错误

### 删除多个 config

```
POST /v1/api/configs/delete
Content-Type: application/json
[
    "config1", "config2"
]
```

#### 返回值

- 如果没有错误，返回空
- 如果有错误则返回相应错误

### 分配 tags 给 config

```
POST /v1/api/configs/<config_name>/tags
Content-Type: application/json
{
   "tags": ["<tag1>", "<tag2>"]
}
```

#### 返回值

- 如果没有错误，返回空
- 如果有错误则返回相应错误

### 分发 config 到 agents

```
POST /v1/api/configs/<config_name>/agents
Content-Type: application/json
{
   "agent_ids": ["<agent_id1>", "<agent_id2>"]
}
```

#### 返回值

- 如果没有错误，返回空
- 如果有错误则返回相应错误

## Metrics

### 获取用户设定信息

```
GET /v1/api/metrics/machine
```

#### 返回值

- 如果用户填写过 metrics 信息的设定，则返回：
```
{
    "opened": true,
    "interval": "3",
    "repo_name":"logkit_pro_internal"
}
```

- 如果用户没有填写过, 则返回：

```
{
    "opened": false
}
```

### 获取某个 agent 对应机器的 metric 信息

```
GET /v1/api/metrics/<agent_id>?beginTime=<begin_time>&endTime=<end_time>
```

#### 返回值

- 如果该 agent 开通了 metrics 收集, 则返回：
```
{
    "opened": true,
    "data": {
        "cpu_usage": {                                  // cpu 利用率
            "buckets": [
                {
                    "cpu_usage_system":{
                        "value":null                    // 可能为 null
                    },
                    "cpu_usage_user":{
                        "value":null
                    },
                    "key":1517413680000                 // 时间戳(毫秒级)
                },
                {
                    "cpu_usage_system":{                // 系统 cpu 资源占用
                        "value":11.126897295316061      // 百分比
                    },
                    "cpu_usage_user":{                  // 用户 cpu 资源占用
                        "value":20.454540321562025      // 百分比
                    },
                    "key":1517824920000
                },
                ...
            ]
        }
        "diskio":{                                      // 磁盘 I/O
            "buckets":[
                {
                    "key":1517808260000                 // 可能不存在这个字段(只会在第一个值时出现)
                },
                {
                    "diskio_reads_rate":{
                        "value":null                    // 可能为 null
                    },
                    "diskio_writes_rate":{
                        "value":null
                    },
                    "key":1517808360000
                },
                {
                    "diskio_reads_rate":{               // 磁盘读取频率
                        "value":3800.909496286884       // 单位 op/sec
                    },
                    "diskio_writes_rate":{              // 磁盘写入频率
                        "value":2468.5610533740837      // 单位 op/sec
                    },
                    "key":1517809740000                 // 时间戳
                },
            ]
        },
        "mem_used_percent":{                            // 内存占用
            "buckets":[
                {
                    "key":1517413680000,
                    "mem_used_percent":{
                        "value":null                    // 可能为 null
                    }
                },
                {
                    "key":1517818020000,                // 时间戳
                    "mem_used_percent":{                // 内存占用
                        "value":78.59851748055786       // 百分比
                    }
                },
                ...
            ]
        },
        "net_bytes_rate":{                              // 网络流量
            "buckets":[
                {
                    "key":1517413680000                 // 字段可能不存在
                },
                {
                    "bytes_recv_rate":{
                        "value":null                    // 字段可能为 null
                    },
                    "bytes_sent_rate":{
                        "value":null
                    },
                    "key":1517806980000
                },
                {
                    "bytes_recv_rate":{                 // 接收速率
                        "value":5683943.616149664       // bytes/sec
                    },
                    "bytes_sent_rate":{                 // 发送速率
                        "value":2472513.4745543003
                    },
                    "key":1517808360000                 // 时间戳
                },
            ]
        },
        "net_packets_rate":{                            // 收发包速率
            "buckets":[
                {
                    "key":1517413680000                 // 字段可能不存在
                },
                {
                    "key":1517415060000,
                    "packets_recv_rate":{               // 字段可能为 null
                        "value":null
                    },
                    "packets_sent_rate":{               // 字段可能为 null
                        "value":null
                    }
                },
                {
                    "key":1517809740000,                // 时间戳
                    "packets_recv_rate":{               // 接收包速率
                        "value":8283.852036660304       // 单位 个/sec
                    },
                    "packets_sent_rate":{               // 发送包速率
                        "value":6568.934223250486
                    }
                },
            ]
        },
        "system_load":{                                 // 负载
            "buckets":[
                {
                    "key":1517413680000
                    "system_load1":{
                        "value":null                    // 字段可能为空
                    },
                    "system_load15":{
                        "value":null
                    },
                    "system_load5":{
                        "value":null
                    }
                },
                {
                    "key":1517820780000,                // 时间戳
                    "system_load1":{                    // 一分钟平均负载
                        "value":2.496029057745206
                    },
                    "system_load15":{                   // 十五分钟平均负载
                        "value":2.340774799951918
                    },
                    "system_load5":{                    // 五分钟平均负载
                        "value":2.3685714188268627
                    }
                },
                ...
            ]
        },
        "tcp_established_num":{                         // TCP 连接数
            "buckets":[
                {
                    "key":1517413680000,
                    "tcp_established_num":{
                        "value":null                    // 字段可能为 null
                    }
                },
                {
                    "key":1517815260000,
                    "tcp_established_num":{
                        "value":67.02427184466019
                    }
                },
                ...
            ]
        }
    }
```

- 如果没有开通，则返回：

```
{
    "opened": false,
    "data": {
        "post_url": "<开通请求的 url>",
        "message": "<提示语>",
        <可能会有其他的字段>
    }
}
```

#### 参数说明

- `beginTime` 和 `endTime` 请提供时间戳，精度秒级（10 位数字）
- 返回的数据中的时间戳将覆盖从 `beginTime` 到 `endTime` 的所有时间点, 没有数据的使用 null 来填充
- 对于 "速率" 类的指标可能会出现字段不存在的情况，但是只会在第一个时间点的时候出现
- 无论提供的时间段有多长, 返回的数据量都在 300 左右
- 开通请求的 url 不包括 url 前缀, 即以该 API（`/v1/metrics`）为例, `post_url` 字段的内容为 `/metrics`
- `message` 是对于用户的提示语, 比如: "开通后所有的agent都将开始收集其所在系统上的metric信息,并发往pandora平台"
- 以上两个字段是一定会有的，剩下的字段根据场景可能会没有
- 剩下的字段每个都是一个 `object`, 每个 `object` 都有两个字段, `chinese` 表示中文的字段名称, `default` 表示默认值.

## Runners

### 获取 runner 列表
```
GET /v1/api/runners?tag=<tagvalue>&id=<idvalue>&name=<runnerName>&agents=<true|false>&sort=<sortKey>&order=<order>&page=<pageNum>&size=<pageSize>&search=<search>
```

#### 返回值

- 如果没有错误，返回：
```
{
    "runners": [
        {
            "name": "<runner_name1>",
            "agent_id": "<agent_id>",
            "config": "<runner_config>",
            "state": "<runner_state>",
            "status": "<runner_status>",
            "config_timestamp": <config_timestamp>
        },
        {
            "name": "<runner_name2>",
            "agent_id": "<agent_id>",
            "state": "<runner_state>",
            "config": "<runner_config>",
            "status": "<runner_status>",
            "config_timestamp": <config_timestamp>,
        },
        ...
    ],
    "agents": {
        "<agent_id>"":{
            "hostname": "<hostname>",
            "ip": "<ip>",
            ...
        }
    }
    "totalSize":<totalSize>
}
```
- 如果有错误则返回相应错误

#### 参数说明

- 参数 `tag` 和 `id` 是指 `agent` 的属性, `name` 是 `runner` 的属性，他们都可以作为 `runner` 的过滤条件
- 参数`sort`、`order`会根据sort的值进行升序/降序排序，`order`的值可以是ascend/descend，`sort`默认为name，`order`默认为ascend
- 参数`search`会对hostnam进行搜索，将包含`search`值的结果搜索出来
- 获取的 runner name list 为各个符合条件的 agent 上的 runner name 的并集
- 返回的数据中没有 `tag` 信息，因为后端不太好加，如果前端需要且前端无法自己解决的话，再协商
- `state` 有 `running` 和 `stopped` 两种情况，分别代表 `运行中` 和 `已停止` 两种状态

### 启动 runners

```
POST /v1/api/runners/start
[
    {
        "config_name": "config1",
        "agent_id": "agent_id1"
    },
    {
        "config_name": "config1",
        "agent_id": "agent_id2"
    }
    ...
]
```

#### 返回值

- 如果没有错误，返回空
- 如果有错误则返回相应错误

### 停止 runners

```
POST /v1/api/runners/stop
[
    {
        "config_name": "config1",
        "agent_id": "agent_id1"
    },
    {
        "config_name": "config1",
        "agent_id": "agent_id2"
    }
    ...
]
```

#### 返回值

- 如果没有错误，返回空
- 如果有错误则返回相应错误

### 重置 runners

```
POST /v1/api/runners/reset
[
    {
        "config_name": "config1",
        "agent_id": "agent_id1"
    },
    {
        "config_name": "config1",
        "agent_id": "agent_id2"
    }
    ...
]
```

#### 返回值

- 如果没有错误，返回空
- 如果有错误则返回相应错误

### 删除 runners

```
POST /v1/api/runners/delete
[
    {
        "config_name": "config1",
        "agent_id": "agent_id1"
    },
    {
        "config_name": "config1",
        "agent_id": "agent_id2"
    }
    ...
]
```

#### 返回值

- 如果没有错误，返回空
- 如果有错误则返回相应错误

## Grok

### 验证 log 和相应的 Grok pattern 是否匹配

```
POST /v1/api/grok/check
Content-Type: application/json
{
  "log":"110.220.330.550 - - [12/Oct/2017:14:16:50 +0800]",
  "pattern":base64("%{NOTSPACE:client_ip} %{USER:ident} %{USER:auth} \[%{HTTPDATE:ts:date}\]")
  "custom_pattern":""
}
```

#### 返回值

- 如果没有错误，返回解析后的匹配数据：

```
{"auth":"-","client_ip":"110.220.330.550","grokInternalCustomSemantic":"110.220.330.550 - - [12/Oct/2017:14:16:50 +0800]","ident":"-","ts":"12/Oct/2017:14:16:50 +0800"}
```

- 如果有错误则返回相应错误

#### 参数说明

- `pattern` 和 `custom_pattern` 前端传过来时都需要 base64 编码

## Tags

### 获取 tag 列表

请求

```
GET /v1/api/tags?name=<tagName>&agents=true&sort=sortKey&order=order&page=<pageNum>&size=<pageSize>&search=<search>
```

#### 返回值

- 如果没有错误，返回：

```
{
    "tags": [
        {
            "name": "<tag_name>",
            "agent_ids": ["id1", "id2"],
            "config_names1": ["name1", "name2"],
            "note": "<tags 备注>"
        },
        "agents": {
             "<agent_id>"":{
                 "hostname": "<hostname>",
                 "ip": "<ip>",
                 ...
             }
        }
    ],
    "totalSize": <totalSize>
}
```

- 如果有错误则返回相应错误

### 添加 tag

```
POST /v1/api/tags/<tag_name>
Content-Type: application/json
{
   "name": "<tag_name>",
   "note": "<tag 说明>",
   "config_names": ["<config_name1>"],
   "agent_ids": ["<agent_id1", "agent_id2"]
}
```

#### 返回值

- 如果没有错误，返回空
- 如果有错误则返回相应错误

#### 参数说明

- `name` 字段可有可无，最终会被链接中的 `<tag_name>` 替换掉

### 更新 tag

```
PUT /v1/api/tags/<tag_name>/note
Content-Type: application/json
{
   "note": "<tag 说明>"
}
```

#### 返回值

- 如果没有错误，返回空
- 如果有错误则返回相应错误

#### 参数说明

- 当前仅支持更新 `note` 字段

### 分配 tag 给 agents

```
POST /v1/api/tags/<tag_name>/agentids
Content-Type: application/json
{
   "agent_ids": ["<agent_id1>", "<agent_id2>"]
}
```

#### 返回值

- 如果没有错误，返回空
- 如果有错误则返回相应错误

### 解除 tag 与 agents 的关联

```
DELETE /v1/api/tags/<tag_name>/agentids/batch?agentids=<agent_id1>,<agent_id2>
```

#### 返回值

- 如果没有错误，返回空
- 如果有错误则返回相应错误

### 删除为 tags 分发的 configs

```
DELETE /v1/api/tags/<tag_name>/configs/<config_name>
```

#### 返回值

- 如果没有错误，返回空
- 如果有错误则返回相应错误

### 删除 tag

```
DELETE /v1/api/tags/<tag_name>
```

#### 返回值

- 如果没有错误，返回空
- 如果有错误则返回相应错误


### 删除多个 tag

```
DELETE /v1/api/tags/batch?tag=<tag1>,<tag2>
```

#### 返回值

- 如果没有错误，返回空
- 如果有错误则返回相应错误
**请求语法**

```
POST /v2/repos/<RepoName>/data
Content-Type: text/plain
Authorization: Pandora <auth>
keyName=valName<TAB>keyName=valName ...
keyName=valName<TAB>keyName=valName ...
...
```

**请求内容**

|参数|类型|必填|说明|
|:---|:---|:---|:---|
|RepoName |string|是|消息队列名称|
|keyName |string|是|字段名称|
|valName |string|是|对应字段名称的数**请求语法**

```
POST /v2/repos/<RepoName>/data
Content-Type: text/plain
Authorization: Pandora <auth>
keyName=valName<TAB>keyName=valName ...
keyName=valName<TAB>keyName=valName ...
...
```

**请求内容**

|参数|类型|必填|说明|
|:---|:---|:---|:---|
|RepoName |string|是|消息队列名称|
|keyName |string|是|字段名称|
|valName |string|是|对应字段名称的数据内容<br/> 注意：如果是`string`类型</br>那么 `\t`、`\r`、`\n` `\` 需要用`\`转义</br>空格`' '` 可以不转义|

> 多个`keyName`和`valName`之间应使用单个 `<TAB>` 分隔。
>
> 对于`array`类型：
> 
> 打点格式为`[e1,e2,...,en]`，数组元素采用逗号分割，且所有元素使用`[]`包括，当元素类型为`string`时，需要加上双引号;
> 
> 对于`map`类型：
> 
> 打点格式为json字符串，比如`{"f1":123,"f2":"abc"}`，注意所有元素使用`{}`包括;


**示例**

```
curl -X POST https://nb-pipeline.qiniuapi.com/v2/repos/test_Repo/data \
-H 'Content-Type: text/plain' \
-H 'Authorization: Pandora 2J1e7iG13J66GA8vWBzZdF-UR_d1MF-kacOdUUS4:NTi3wH_WlGxYOnXsvgUrO4XMD6Y=' \
-d '
	userName=小张		age=22   addresses=["beijing","shanghai"] profile={"position":"engineer",salary:15000} 
	userName=小王		age=28   addresses=["hangzhou","shenzhen"] profile={"position":"engineer",salary:12000}
	'
```

**日志原始文本上传接口：**

**请求语法**

```
POST /stream/<RepoName>/data
Content-Type: application/json
Authorization: Pandora <auth>
[日志原始文本]
```

**请求内容**

|   参数   |  类型  | 必填 |     说明     |
| :------: | :----: | :--: | :----------: |
| RepoName | String |  是  | 消息队列名称 |

> 每个数据仓库最多可以绑定 5 条解析规则。 上传数据时，如果数据仓库绑定了解析规则，则按照解析规则进行解析。多个解析规则依次解析时有覆盖效果，举例如下： 假定当前有两个规则，规则 1 解析后结果为 {A : "aa" , B : "bb"}，规则 2 解析后结果为 {A : "abcd" , C : "cc"}，则最终的解析结果为{A : "abcd" , B : "bb" , C : "cc"}。
>
> 如果数据仓库没有绑定解析规则，上传数据格式遵从`POST /v2/repos/ {RepoName}/data`接口规定的形式

**示例**

```
curl -X Post https://nb-pipeline.qiniuapi.com/v2/stream/token_agent__test2/data \
-H content-type: text/plain \
-H 'Authorization: Pandora 2J1e7iG13J66GA8vWBzZdF-UR_d1MF-kacOdUUS4:NTi3wH_WlGxYOnXsvgUrO4XMD6Y=' \
-d '
200 {} POST 000.00.00.00 fusion_applog 43274 pipe.niuniu.ggeh.com FgAAABCbBb4s9zsV PostData ["lili:97"] text/plain /n5/sche/applog/file REQ 1000 1328438666 application/json pipeline-apiserver-31355784-jnk2f STRONGER 2 938343 2 1536863302210 2018-06-27T08:31:09.961094Z
200 {} POST 000.00.00.00 kodo_pfdstg 4821 pipe.niuniu.io FgAAA4s9zsV PostData ["STREAMING:350"] text/plain /nb/sche/kodo_pfdstg/file REQ 1500 1538921591 application/json pipeline-apiserver-3179997084-gxvmf STRONGER 2 3505878 2 1530088274411 2018-06-27T08:31:09.960729Z
'
```


据内容<br/> 注意：如果是`string`类型</br>那么 `\t`、`\r`、`\n` `\` 需要用`\`转义</br>空格`' '` 可以不转义|

> 多个`keyName`和`valName`之间应使用单个 `<TAB>` 分隔。
>
> 对于`array`类型：
> 
> 打点格式为`[e1,e2,...,en]`，数组元素采用逗号分割，且所有元素使用`[]`包括，当元素类型为`string`时，需要加上双引号;
> 
> 对于`map`类型：
> 
> 打点格式为json字符串，比如`{"f1":123,"f2":"abc"}`，注意所有元素使用`{}`包括;


**示例**

```
curl -X POST https://nb-pipeline.qiniuapi.com/v2/repos/test_Repo/data \
-H 'Content-Type: text/plain' \
-H 'Authorization: Pandora 2J1e7iG13J66GA8vWBzZdF-UR_d1MF-kacOdUUS4:NTi3wH_WlGxYOnXsvgUrO4XMD6Y=' \
-d '
	userName=小张		age=22   addresses=["beijing","shanghai"] profile={"position":"engineer",salary:15000} 
	userName=小王		age=28   addresses=["hangzhou","shenzhen"] profile={"position":"engineer",salary:12000}
	'
```

**日志原始文本上传接口：**

**请求语法**

```
POST /stream/<RepoName>/data
Content-Type: application/json
Authorization: Pandora <auth>
[日志原始文本]
```

**请求内容**

|   参数   |  类型  | 必填 |     说明     |
| :------: | :----: | :--: | :----------: |
| RepoName | String |  是  | 消息队列名称 |

> 每个数据仓库最多可以绑定 5 条解析规则。 上传数据时，如果数据仓库绑定了解析规则，则按照解析规则进行解析。多个解析规则依次解析时有覆盖效果，举例如下： 假定当前有两个规则，规则 1 解析后结果为 {A : "aa" , B : "bb"}，规则 2 解析后结果为 {A : "abcd" , C : "cc"}，则最终的解析结果为{A : "abcd" , B : "bb" , C : "cc"}。
>
> 如果数据仓库没有绑定解析规则，上传数据格式遵从`POST /v2/repos/ {RepoName}/data`接口规定的形式

**示例**

```
curl -X Post https://pipeline.qiniu.com/v2/stream/token_agent__test2/data \
-H content-type: text/plain \
-H 'Authorization: Pandora 2J1e7iG13J66GA8vWBzZdF-UR_d1MF-kacOdUUS4:NTi3wH_WlGxYOnXsvgUrO4XMD6Y=' \
-d '
200 {} POST 000.00.00.00 fusion_applog 43274 pipe.niuniu.ggeh.com FgAAABCbBb4s9zsV PostData ["lili:97"] text/plain /n5/sche/applog/file REQ 1000 1328438666 application/json pipeline-apiserver-31355784-jnk2f STRONGER 2 938343 2 1536863302210 2018-06-27T08:31:09.961094Z
200 {} POST 000.00.00.00 kodo_pfdstg 4821 pipe.niuniu.io FgAAA4s9zsV PostData ["STREAMING:350"] text/plain /nb/sche/kodo_pfdstg/file REQ 1500 1538921591 application/json pipeline-apiserver-3179997084-gxvmf STRONGER 2 3505878 2 1530088274411 2018-06-27T08:31:09.960729Z
'
```




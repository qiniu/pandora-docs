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
|valName |string|是|对应字段名称的数据内容<br/> 注意：如果是`string`类型</br>那么 `\t`、`\r`、`\n` `\` 需要用`\`转义</br>空格`' '` 可以不转义|

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
curl -X POST https://pipeline.qiniu.com/v2/repos/test_Repo/data \
-H 'Content-Type: text/plain' \
-H 'Authorization: Pandora 2J1e7iG13J66GA8vWBzZdF-UR_d1MF-kacOdUUS4:NTi3wH_WlGxYOnXsvgUrO4XMD6Y=' \
-d '
	userName=小张		age=22   addresses=["beijing","shanghai"] profile={"position":"engineer",salary:15000} 
	userName=小王		age=28   addresses=["hangzhou","shenzhen"] profile={"position":"engineer",salary:12000}
	'
```

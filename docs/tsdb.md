时间序列数据库 Time Series Database (TSDB) 主要用于存储、查询时间序列数据。时间序列数据就是数据格式里包含 timestamp（数据产生时间）字段的数据，您也可以将它理解为历史数据，比如某个时间点的某台机器的 CPU 使用率。

![](https://pandora-kibana.qiniu.com/time_series_database.png)

时间序列数据的典型特点是：

1.实时写入、无更新：随着时间推进实时生成新数据，旧数据成为历史数据不会更新。

2.按时间范围读取：通常时间序列数据用于描述一个随时间变化的状态信息，如最近 1 小时某台机器的 CPU 使用率。

3.数据量大：常规的实时监测系统均有成千上万的监测点，监测点每秒钟都会产生多条数据。

## 数据模型

时间序列数据主要由以下几个部分组成：主体、测量值、时间戳。

以一个示例数据为例来解析一下时序数据的数据模型，这些数据是虚构的，它们显示了 2015 年 8 月 18 日午夜至 2015 年 8 月 18 日上午 6:12 期间两个科学家（ langstroth 和 perpetua ）在两个地点（地点 1 和地点 2 ）计算的蝴蝶和蜜蜂的数量：

```
database: my_database
series: test_series

----------
time                    butterflies     honeybees     location     scientist
2015-08-18T00:00:00Z    12              23            1            langstroth
2015-08-18T00:00:00Z    1               30            1            perpetua
2015-08-18T00:06:00Z    11              28            1            langstroth
2015-08-18T00:06:00Z    3               28            1            perpetua
2015-08-18T05:54:00Z    2               11            2            langstroth
2015-08-18T06:00:00Z    1               10            2            langstroth
2015-08-18T06:06:00Z    8               23            2            perpetua
2015-08-18T06:12:00Z    7               22            2            perpetua
```

主体：采集的主体，一个主体会拥有多个维度的属性，在这个例子中，主体是采集蝴蝶和蜜蜂的数量这个行为，其拥有的属性有 butterflies、honeybees、location、 scientist。

测量值：对应主体的每个属性的具体的值，在这个例子中，对应 butterflies 这个属性，它的测量值有 12，1，11，3，2，1，8，7。

时间戳：每次采集到一个数据，都会带有一个时间戳属性来表示其产生的时间。

## 概念&术语

在七牛 TSDB 产品中，常用到的术语有以下几个：

```
series          timestamp
field key       field value     
tag key         tag value       
```

### 序列（series）

在七牛 TSDB 产品中，序列等同于关系型数据库`表（table）`的概念，在上述数据例子中，数据仓库是`my_database`，序列是`test_series`。

### 时间戳（timestamp）

时间戳是每次采集一个数据伴随的数据产生时间，时间戳使用的是RFC 3339的日期格式。timestamp 可以作为查询条件(where)、分组条件(group by)和排序条件(order by)。

### 数据字段（filed key）& 数据内容（filed value）

数据字段是我们的查询主体，可以进行函数运算（如 sum()、mean()、count()等）。

以上方示例数据为例：

butterflies 和 honeybees 就是我们的 field key，用来存储数据内容（field value)；

field key 可以作为查询条件（where），无法作为分组条件(group by)、排序条件(order by)。

### 索引字段（tag key）& 索引内容（tag value）

为了处理大量的数据，需要建立索引来提高查询速度。

我们用一个例子来解释索引的用处：

假如您想查询蝴蝶数量是 1 的所有数据：

```
SELECT * FROM test_series WHERE butterflies = 1
```
在没有索引的情况下，时序数据库会扫描所有蝴蝶的值，然后返回结果；

这种行为会影响您的查询效率，特别是在数据范围更大时；为了优化查询，提升效率，我们为 butterflies 加上索引，butterflies 就成了 tag key，它的值则为 tag value。

那么sql 语句：

```
SELECT * FROM "test_series" WHERE "butterflies" = 1
```
只扫描 butterflies=1 的值，快速返回结果，大大提升查询效率。

被索引的字段（tag key）不能单独作为查询主体，但是可以和 field key 一起作为查询主体。tag key 可以作为查询条件（where）与分组条件(group by)，但不能作为排序条件(order by)。

了解更多 field key , tag key , timestamp 在SQL 语句里的用法，请阅读 [SQL详解](https://qiniu.github.io/pandora-docs/#/tsdb?id=%E6%9F%A5%E8%AF%A2%E8%8C%83%E5%BC%8F)。

## TSDB使用指南

### 创建仓库和序列

通过时序数据库查询数据，首先您需要有用来存储数据的仓库和序列。通过七牛开发者平台的[时序数据库](https://portal.qiniu.com/tsdb/repo)进入创建仓库和序列入口。

![](https://pandora-kibana.qiniu.com/series.png)

创建序列时，需要指定序列的retention，它的意思是指，任何一条数据从写入序列开始，存储的时间超过retention指定的时间后，这一条数据将会被自动删除。

创建序列的时候无需建立字段，字段在数据打入时自动绑定。将数据打入 TSDB 时，在 `workflow`的 TSDB 数据导出节点，选择已有的仓库和序列即可。

![](https://pandora-kibana.qiniu.com/tsdb_database.png)

或者，在`workflow`中将数据导出到 TSDB 时，直接创建新的数据仓库和序列，输入您想创建的仓库和序列名即可。

!>注意：数据打入时间序列数据库需要经过`workflow`进行数据导出。

### 建立索引

您可以根据查询需要为指定字段建立索引，提高查询效率。在`workflow`的 TSDB 数据导出节点，在您输入好仓库名和序列名以后，您可以看到打入到序列中的字段及其类型，并且可以直接通过勾选为字段建立索引，非常直观。建立好索引以后，任务需要重新运行索引才会生效。

![](https://pandora-kibana.qiniu.com/index.png )

### 数据查询

在时序数据库的`数据查询`页面，通过选择仓库和序列，输入 SQL 查询语句，点击输入框右下角的搜索图标即可查询到该序列里面的数据。

为了方便您查询，我们提供普通字段和索引字段预览，通过切换 Tags， Fields 来预览该序列里面的索引字段和普通字段。当您想查询数据却遗忘序列里的数据结构时可以使用这个功能，由于field key 和 tag key 在 SQL 里的用法不同（参考 [SQL详解](https://qiniu.github.io/pandora-docs/#/tsdb?id=%E6%9F%A5%E8%AF%A2%E8%8C%83%E5%BC%8F)），您可以根据字段预览正确地使用 SQL 。

![](https://pandora-kibana.qiniu.com/search_index.png)

例如，您可以以一个 field key 为查询主体，tag key 为分组条件：

![](https://pandora-kibana.qiniu.com/index_search2.png)

结果如图，数据已经按照 clientIp 分好组，每个分组下面显示详细respTime 的值。

![](https://pandora-kibana.qiniu.com/index_search1.png)

### 快速查询

您可以使用`保存SQL语句`功能，将输入过的SQL语句保存起来，下次使用的时候点击`常用SQL`即可快速查询，避免重复工作,保存过的 SQL 可以更改或删除。

![](https://pandora-kibana.qiniu.com/sql.png)

### 统计图表

TSDB 提供查询结果的可视化功能，点击 SQL 输入框右下角的图表图标,您可以看到指定字段在某个时间范围内的变化情况。

![](https://pandora-kibana.qiniu.com/graph.png)

## 系统关键字

在时序数据库中，我们有很多系统关键字，在命名的过程中，我们应该避免使用这些关键字（包括大小写）。

|server|repo|view|tagKey|ILLEGAL|
|:-:|:-:|:-:|:-:|:-:|
|EOF|WS|IDENT|BOUNDPARAM|NUMBER|
|INTEGER|DURATIONVAL|STRING|BADSTRING|BADESCAPE|
|TRUE|FALSE|REGEX|BADREGEX|ADD|
|SUB|MUL|DIV|AND|OR|
|EQ|NEQ|EQREGEX|NEQREGEX|LT|
|LTE|GT|GTE|LPAREN|RPAREN|
|COMMA|COLON|DOUBLECOLON|SEMICOLON|DOT|
|ALL|ALTER|ANY|AS|ASC|
|BEGIN|BY|CREATE|CONTINUOUS|DATABASE|    
|DATABASES|DEFAULT|DELETE|DESC|DESTINATIONS|       
|DIAGNOSTICS|DISTINCT|DROP|DURATION|END|            
|EVERY|EXISTS|EXPLAIN|FIELD|FOR|
|FROM|GROUP|GROUPS|IF|IN|                     
|INF|INSERT|INTO|KEY|KEYS| 
|KILL|LIMIT|MEASUREMENT|MEASUREMENTS|NAME|                  
|NOT|OFFSET|ON|ORDER|PASSWORD|   
|POLICY|POLICIES|PRIVILEGES|QUERIES|QUERY|
|READ|REPLICATION|RESAMPLE|RETENTION|REVOKE|
|SELECT|SERIES|SET|SHOW|SHARD|
|SHARDS|SLIMIT|SOFFSET|STATS|SUBSCRIPTION|
|SUBSCRIPTIONS|TAG|TO|TIME|VALUES|
|WHERE|WITH|WRITE|
      
    
## 查询语句详解

### 查询主体

* `select *`：返回所有 tag key 和 field key；但一般不建议这么写，非常消耗性能，并且费用很高；
* `select <field_key>`：返回一个特定的 field key；
* `select <field_key>,<field_key>`：返回多个特定的 field key；
* `select <field_key>,<tag_key>`：返回特定的 field key 和tag key；

!>注意1：tag key 不能单独作为查询主体，任何一个查询主体至少包含1个field key；

!>注意2：如果标识符号包含除了[A-z,0-9,_]之外的符号，或者包含时序数据库的关键字，又或者以数字开头，那么必须用双引号`""`括起来。

### 函数运算

函数运算只能作用于`field key`。

目前 TSDB 支持的函数有以下几种：

|函数名|解释|
|:--|:--|
|count|求总数|
|sum|求总和|
|mean|求平均值|
|distinct|去除重复内容|
|max|取最大值|
|min|取最小值|
|bottom（filed key,N）|取最小的N个值|
|top（filed key,N）|取最大的N个值|
|first| timestamp 最新的值|
|last| timestamp 最老的值|
|difference（filed key,time interval）|指定一个字段隔一段时间，比较其内容，此函数不保证精确|

>difference举例：select difference(field_key,5m) from "series"

### 条件语句

条件语句允许使用 field key，tag key，timestamp 来对数据进行筛选。

当使用`field key`作为查询条件时，支持以下运算符：

|运算符|解释|
|:--|:--|
|=|等于|
|<>|不等于|
|!=|不等于|
|>|大于|
|<|小于|
|>=|大于等于|
|<=|小于等于|

当使用`tag key`作为查询条件时，支持以下运算符：

|运算符|解释|
|:--|:--|
|=|等于|
|<>|不等于|
|!=|不等于|

当使用`timestamp`作为查询条件时，支持以下运算符：

|运算符|解释|
|:--|:--|
|=|等于|
|>|大于|
|<|小于|
|>=|大于等于|
|<=|小于等于|

当使用时间字段（timestamp 或者序列里面的时间字段）作为查询条件时，我们可以使用`now()`函数，它表示当前时间，并且支持运算；

例：

```
select mean(waterLevel),area from waterLevelInfo where createTime > now() - 5m

select mean(waterLevel),area from waterLevelInfo where createTime > now() - 3d and createTime > now() - 1d

select mean(waterLevel),area from waterLevelInfo where createTime > '2017-01-01T13:00:00Z'
```

!> 注意1： `now()`表示当前时间， `'-'`符号的左右两边都必须至少包含一个空格。

!> 注意2：s=秒，m=分，h=小时，d=天

### 分组

分组只能作用于`tag key`和`timestamp`；

语法规则：

|语法|解释|
|:--|:--|
|group by `*`|根据所有tag key进行分组|
|group by `<tag_key>`|根据单个tag key进行分组|
|group by `<tag_key>`,`<tag_key>`|根据多个tag key进行分组|

### 排序

在时序数据库，排序只能作用于`timestamp`，无法作用于`tag key`和`field key`；

在排序时，`timestamp`可以指定一个区间，用来对时间进行粒度聚合；

```
例：
select mean(waterLevel) from waterLevelInfo order by time 

select mean(waterLevel) from waterLevelInfo order by time(5m)
```

时序数据库全称为时间序列数据库。时间序列数据库主要用于指处理带时间标签（按照时间的顺序变化，即时间序列化）的数据，带时间标签的数据也称为时间序列数据。时序数据的典型特点是：产生频率快（一秒钟内可产生多条数据）、严重依赖于采集时间（每一条数据均要求对应唯一的时间）、数据条数多信息量大（常规的实时监测系统均有成千上万的监测点，监测点每秒钟都会产生多条数据）。

![](https://odum9helk.qnssl.com/FoFY8l0UBVdpb4fOgEntVwmy3QNr)

</br>

## 概念&术语

```
databse          series          timestamp
filed key        filed value     filed set
tag key          tag value       tag set
```

</br>
在讲解时序数据库的基本概念和术语之前，我们首先准备一些简单的示例数据，以用来更简单的解释各个名词的概念：

这些数据是虚构的，它们显示了2015年8月18日午夜至2015年8月18日上午6:12期间两个科学家（langstroth和perpetua）在两个地点（地点1和地点2）计算的蝴蝶和蜜蜂的数量。假设数据位于名为my_database数据库的test_series序列中。
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

现在您已经看到了一些示例数据。 时间序列数据库所做的一切的根源开始就是`时间`。在上面的数据中有一个名为time的列，时序数据库中的所有数据都有该列；它的内容是数据存储时间的时间戳，时间戳使用的是`RFC 3339`的日期格式，这一列通常我们称之为timestamp。

</br>

### 数据仓库（database）& 序列（series）

传统的数据仓库概念，内部可以存放多个序列，在上方的示例数据中，`my_database`就是我们的数据仓库名称。

而序列相当于关系型数据库`表（table）`的概念，在时序数据库中我们叫它序列；

序列内的字段是完全自由的，用户可以随意向序列中打入不同数据格式的内容，字段的定义也在打入数据时指定；在上方的示例数据中，`test_series`就是我们的序列名称。

</br>

### 数据字段（filed key）& 数据字段集（filed set）& 数据内容（filed value）

每一个序列中都至少需要包含1个数据字段，它通常是我们的查询主体，可以进行函数运算，但无法进行`分组`和`排序`。

以上方示例数据为例：

butterflies和honeybees就是我们的filed key，用来存储元数据；

而12-7、23-22则是我们的filed value，它实际上就是你的数据内容；

在上面的数据中，field-key和field-value对的集合构成了一个filed set。 以下是样本数据中的八个filed set：
```
butterflies = 12	honeybees = 23
butterflies = 1 	honeybees = 30
butterflies = 11    honeybees = 28
butterflies = 3 	honeybees = 28
butterflies = 2 	honeybees = 11
butterflies = 1 	honeybees = 10
butterflies = 8 	honeybees = 23
butterflies = 7 	honeybees = 22
```

</br>

### 索引字段（tag key）& 索引字段集（tag set）& 索引内容（tag value）

为filed key增加索引会极大的提升查询效率，被索引的字段不能单独作为查询主体，通常我们把它用做匹配和分组条件。

我们为上方数据的location和scientist打上索引，他们就成了tag key；

而它们的内容1-2、langstroth&perpetua则为tag value；

和filed set概念相同，样例数据中的4个tag value为：
```
location = 1, scientist = langstroth
location = 2, scientist = langstroth
location = 1, scientist = perpetua
location = 2, scientist = perpetua
```

!> 注意：在任何一个序列中，filed是必须的，而tags则是可选的。

</br>

为什么打上索引之后查询速度会变快？

从上面的数据可以看出，我们关注的点大多在蝴蝶和蜜蜂的值上，那么在编写sql时：
```
SELECT * FROM "test_series" WHERE "butterflies" = 1
SELECT * FROM "test_series" WHERE "honeybees" = 23
```
假如我们没有索引的话，时序数据库在第一条语句会扫描所有蝴蝶的值，而第二条语句会扫描所有蜜蜂的值，然后返回结果；

这种行为会影响您的查询效率，特别是在数据范围更大时；为了优化查询，提升效率，我们引入索引之后，示例数据会变成以下结构：
```
database: my_database
series: test_series

----------
time				    location    scientist     butterflies    honeybees
2015-08-18T00:00:00Z    1           langstroth    12             23
2015-08-18T00:00:00Z    1           perpetua      1              30
2015-08-18T00:06:00Z    1           langstroth    11             28
2015-08-18T00:06:00Z    1           perpetua      3              28
2015-08-18T05:54:00Z    2           langstroth    2              11
2015-08-18T06:00:00Z    2           langstroth    1              10
2015-08-18T06:06:00Z    2           perpetua      8              23
2015-08-18T06:12:00Z    2           perpetua      7              22
```
我们将butterflies和honeybees转为tag key，将location和scientist转为filed key，这样在查询时就不会扫描所有的内容，而只会扫描tag key，从而提升查询效率。

</br>

### 存储时限（retention）& 数据点（point）
在时序数据库创建序列时，我们需要指定retention，它的意思是指，当任何一条数据从写入序列开始，当存储的时间超过retention指定的时间后，这一条数据将会被自动删除，也就是说：rentention指的是这个series中每一条数据的存储时限。

通常，我们将一条完整的数据称为一个point。

</br>

### 索引值组合
在时序数据库中，每一个 tag value + series 形成一个唯一的索引，我们将多个唯一索引的概念称为索引值组合。

```
例：
假设现在有series1和series2，其中series1中包含a，b，c共3个tag value，而series2中包含d，e，f，g共4个tag value，
那么计算索引值组合数量的方式为：
（series1.a + series1.b + series1.c） * （series2.d + series2.e + series2.f + series2.g）= 3 * 4 = 12

```

</br>

## 系统关键字
在时序数据库中，我们有很多系统关键字，在使用的过程中，我们应该避免使用这些关键字（包括大小写）。

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
    
</br>    
    
## 查询范式
编写时序数据库的sql语句大体上和我们平时编写的sql没有什么区别，但还是有些细节需要注意！

</br>

### 查询主体
* `select *`：返回所有tag和filed；但我们不建议这么写，非常消耗性能，并且费用很高；
* `select <filed_key>`：返回一个特定的filed；
* `select <filed_key>,<filed_key>`：返回多个特定的filed；
* `select <filed_key>,<tag_key>`：返回特定的filed和tag；

!>注意1：tag不能单独作为查询主体，任何一个查询主体至少包含1个filed；

!>注意2：如果标识符号包含除了[A-z,0-9,_]之外的符号，或者包含时序数据库的关键字，又或者以数字开头，那么必须用双引号（`""`）扩起来。

</br>

### 函数运算
函数运算只能作用于`filed key`，`tag key`和`timestamp`只能作为分组和查询条件。

目前我们支持的函数有以下几种：

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
|first|timestamp最新的值|
|last|timestamp最老的值|
|difference（filed key,time interval）|指定一个字段和一段时间，比较其内容，此函数不保证精确|

>difference举例：select difference(filed_key,5m) from "series"

</br>

### 条件语句
条件语句允许使用filed key，tag key，timestamp来对数据进行筛选。

当使用`filed key`作为查询条件时，支持以下运算符：

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

</br>

### 分组
分组只能作用于`tag key`和`timestamp`；

当`timestamp`作为分组条件时，`where`语句后方必须含有`timestamp`的条件。

语法规则：

|语法|解释|
|:--|:--|
|group by `*`|根据所有tag key进行分组|
|group by `<tag_key>`|根据单个tag key进行分组|
|group by `<tag_key>`,`<tag_key>`|根据多个tag key进行分组|

</br>

### 排序
在时序数据库，排序只能作用于`timestamp`，无法作用于`tag key`和`filed key`；

在排序时，`timestamp`可以指定一个区间，用来对时间进行粒度聚合；

```
例：
select mean(waterLevel) from waterLevelInfo order by time desc

select mean(waterLevel) from waterLevelInfo order by time(5m) desc
```

</br>

### 时间作为条件

当时间作为查询条件时，我们可以使用`now()`函数，它表示当前时间，并且支持运算；

```
例：
select mean(waterLevel),area from waterLevelInfo where createTime > now() - 5m

select mean(waterLevel),area from waterLevelInfo where createTime > now() - 3d and createTime > now() - 1d

select mean(waterLevel),area from waterLevelInfo where createTime > '2017-01-01T13:00:00Z'
```

!> 注意1： `now()`表示当前时间， `'-'`符号的左右两边都必须至少包含一个空格。

!> 注意2：s=秒，m=分，h=小时，d=天
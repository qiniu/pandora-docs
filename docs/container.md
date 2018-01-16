### 计算资源吞吐量估计值

我们准备100GB数据，这些数据以10个字段、单条数据1kb、sql复杂度为1来计算，在我们提供的所有规格下，平均吞吐量如下表：

?> sql复杂度为1表示该条sql只有一个函数计算，如： select count(a) as b from stream

!> 注意：受数据格式、类型、sql复杂度影响，以下数值仅供参考，不作为标准依据。


|规格|100GB数据计算|计算时间|平均吞吐量|
|:--|:--|:--|:--|
|1U2G|select and filter|340秒|300MB/sec|
|1U4G|select and filter|273秒|375MB/sec|
|2U4G|select and filter|170秒|600MB/sec|
|4U8G|select and filter|136秒|750MB/sec|
|4U16G|select and filter|100秒|1GB /sec|
|8U16G|select and filter|50秒|2GB ／sec|
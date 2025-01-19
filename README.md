# perf2serial (WIP)

CPU/GPU使用率、メモリ使用率などのリソース情報をシリアルインタフェースを通じて外部の DIY デバイスに表示するためのプロトコルです。

# Metric Class Definition

|Value|Description|
|:--:|:--|
|0|Custom|
|1|Total CPU Load|
|2|Physical Memory Usage|

# Commands

## metric_add

デバイスに表示するメトリックを追加する

### Arguments

|Name|Required|Type|Range|Default|Description|
|:--:|:--:|:--:|:--:|:--:|:--|
|`-c`||i32|see table|0|class of metric|
|`-n`||str|any|(determined by device)|display name of metric|
|`-u`||str|any|`""`|unit of sample value (such as `%`, `GB`)|
|`-m`||f32|any|0|minimum value of sample|
|`-x`||f32|any|100|maximum value of sample|

### Return Value

|Value|Type|Description|
|:--:|:--:|:--|
|≧ 0|i32|slot ID|
|< 0|i32|failed|

## metric_update

メトリックの値を更新する

### Arguments

|Name|Required|Type|Range|Default|Description|
|:--:|:--:|:--:|:--:|:--:|:--|
|`-i`|✔️|i32|≧ 0||slot ID|
|`-v`|✔️|f32|any||value of sample|

### Return Value

void

----

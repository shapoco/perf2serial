# perf2serial

CPU/GPU使用率、メモリ使用率などのリソース情報をシリアルインタフェースを通じて外部の DIY デバイスに表示するためのプロトコル。

## Serial Setting

|Config|Value|
|:--|:--|
|Baudrate|115200 bps|
|Stop Bits|1|
|Parity Bits|0|
|Loopback|none|
|New Line Code|`CR` or `LF` or `CRLF`|

## Type Definition

|Type Name|Description|Format|
|:--:|:--|:--|
|i32|32bit signed integer|`-?(0\|[1-9][0-9]*)`<br>`0x[0-9a-fA-F]+`|
|f32|32bit floating point|`-?(0\|[1-9][0-9]*(\.[0-9]+)?([eE][0-9]+)?)`|
|str|string (max 63 Bytes)|Printable ASCII characters only<br>should be enclosed it in `"..."`<br>`\"`-->`"`, `\r`-->`CR`, `\n`-->`LF`, `\t`-->Tab|

## Metric Class Definition

|Value|Description|
|:--:|:--|
|0|Custom|
|1|Total CPU Load|
|2|Physical Memory Usage|

## Command/Response Format

空行は無視すること。

送信側は改行文字に `CRLF` を使用できるが、受信側は 1 個の `CR` または `LF` を改行として扱ってよい。この場合 `CRLF` は行末の後に空行が存在するように見える。

### Command (Host --> Device)

一般的な CLI のコマンドラインと同じような形でコマンドを 1 行で送信する。

`command_name -arg_name arg_value`

### Response (Device --> Host)

1個のコマンドに対してデバイスは 1 個の応答を返す。

## Command Definition

### perf2serial_hello

他の全てのコマンドの前に送信する。ホストは、コマンドの成功を示す文字列が返ってきたことをもって接続の確立と判断する。

- Arguments: none
- Return value:

  |Value|Type|Description|
  |:--:|:--:|:--|
  |`"perf2serial_welcome"`|str|indicates connection established|

### device_name

- Arguments: none
- Return value:

  |Value|Type|Description|
  |:--:|:--:|:--|
  |any|str|device name|

### metric_add

デバイスに表示するメトリックを追加する

- Arguments:

  |Name|Required|Type|Range|Default|Description|
  |:--:|:--:|:--:|:--:|:--:|:--|
  |`-c`||i32|see table|0|class of metric|
  |`-n`||str|any|(determined by device)|display name of metric|
  |`-u`||str|any|`""`|unit of sample value (such as `%`, `GB`)|
  |`-m`||f32|any|0|minimum value of sample|
  |`-x`||f32|any|100|maximum value of sample|

- Return value:

  |Value|Type|Description|
  |:--:|:--:|:--|
  |≧ 0|i32|slot ID|
  |< 0|i32|failed|

### metric_update

メトリックの値を更新する

- Arguments:

  |Name|Required|Type|Range|Default|Description|
  |:--:|:--:|:--:|:--:|:--:|:--|
  |`-i`|✔️|i32|≧ 0||slot ID|
  |`-v`|✔️|f32|any||value of sample|

- Return value:

  |Value|Type|Description|
  |:--:|:--:|:--|
  |0|i32|accepted|
  |(other)|i32|failed|

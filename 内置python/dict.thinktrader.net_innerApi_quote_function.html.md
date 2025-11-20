---
url: "https://dict.thinktrader.net/innerApi/quote_function.html"
title: "引用函数 | 迅投知识库"
---

## [\#](https://dict.thinktrader.net/innerApi/quote_function.html\#ext-data-%E8%8E%B7%E5%8F%96%E6%89%A9%E5%B1%95%E6%95%B0%E6%8D%AE) ext\_data - 获取扩展数据

获取扩展数据

**调用方法：**`ext_data(extdataname, stockcode, deviation, ContextInfo)`

**参数：**

| 参数名 | 类型 | 说明 | 提示 |
| --- | --- | --- | --- |
| `extdataname` | `string` | 扩展数据名 |  |
| `stockcode` | `string` | 证券代码 | 形式如 '600000.SH' |
| `deviation` | `number` | K 线偏移 | 0：不偏移，N：向右偏移N，-N：向左偏移N |
| `ContextInfo` | `pythonObj` | Python 对象 | ython 对象，这里必须是 ContextInfo |

**返回：** number

\\*\\* 示例：\*\*

```
#coding:gbk
def init(ContextInfo):
	print(ext_data('CR', '600000.SH', 0, ContextInfo))
```

## [\#](https://dict.thinktrader.net/innerApi/quote_function.html\#ext-data-rank-%E8%8E%B7%E5%8F%96%E5%BC%95%E7%94%A8%E7%9A%84%E6%89%A9%E5%B1%95%E6%95%B0%E6%8D%AE%E7%9A%84%E6%95%B0%E5%80%BC%E5%9C%A8%E6%89%80%E6%9C%89%E5%93%81%E7%A7%8D%E4%B8%AD%E7%9A%84%E6%8E%92%E5%90%8D) ext\_data\_rank - 获取引用的扩展数据的数值在所有品种中的排名

获取引用的扩展数据的数值在所有品种中的排名

**调用方法：**`ext_data_rank(extdataname, stockcode, deviation, ContextInfo)`

**参数：**

| 参数名 | 类型 | 说明 | 提示 |
| --- | --- | --- | --- |
| `extdataname` | `string` | 扩展数据名 |  |
| `stockcode` | `string` | 证券代码 | 形式如 '600000.SH' |
| `deviation` | `number` | K 线偏移 | 0：不偏移，N：向右偏移N，-N：向左偏移N |
| `ContextInfo` | `pythonObj` | Python 对象 | ython 对象，这里必须是 ContextInfo |

**返回：** number

\\*\\* 示例：\*\*

```
#coding:gbk
def init(ContextInfo):
	print(ext_data_rank('mycci', '600000.SH', 0, ContextInfo))
```

## [\#](https://dict.thinktrader.net/innerApi/quote_function.html\#ext-data-rank-range-%E8%8E%B7%E5%8F%96%E5%BC%95%E7%94%A8%E7%9A%84%E6%89%A9%E5%B1%95%E6%95%B0%E6%8D%AE%E7%9A%84%E6%95%B0%E5%80%BC%E5%9C%A8%E6%8C%87%E5%AE%9A%E6%97%B6%E9%97%B4%E5%8C%BA%E9%97%B4%E5%86%85%E6%89%80%E6%9C%89%E5%93%81%E7%A7%8D%E4%B8%AD%E7%9A%84%E6%8E%92%E5%90%8D) ext\_data\_rank\_range - 获取引用的扩展数据的数值在指定时间区间内所有品种中的排名

获取引用的扩展数据的数值在指定时间区间内所有品种中的排名

\\*\\* 调用方法： \*\*`ext_data_rank_range(extdataname, stockcode, begintime, endtime, ContextInfo)`

**参数：**

| 参数名 | 类型 | 说明 | 提示 |
| --- | --- | --- | --- |
| `extdataname` | `string` | 扩展数据名 |  |
| `stockcode` | `string` | 证券代码 | 形式如 '600000.SH' |
| `begintime` | `string` | 区间的起始时间 | 格式为 '2016-08-02 12:12:30'（包括该时间点在内） |
| `endtime` | `string` | 区间的结束时间 | 格式为 '2017-08-02 12:12:30' （包括该时间点在内） |
| `ContextInfo` | `pythonObj` | Python对象 | Python 对象，这里必须是 ContextInfo |

**返回：** pythonDict

\\*\\* 示例：\*\*

```
#coding:gbk
def init(ContextInfo):
	print(ext_data_rank_range('mycci', '600000.SH','2022-08-02 12:12:30', '2023-08-02 12:12:30', ContextInfo))
```

## [\#](https://dict.thinktrader.net/innerApi/quote_function.html\#ext-data-range-%E8%8E%B7%E5%8F%96%E6%89%A9%E5%B1%95%E6%95%B0%E6%8D%AE%E5%9C%A8%E6%8C%87%E5%AE%9A%E6%97%B6%E9%97%B4%E5%8C%BA%E9%97%B4%E5%86%85%E7%9A%84%E5%80%BC) ext\_data\_range - 获取扩展数据在指定时间区间内的值

获取扩展数据在指定时间区间内的值

**调用方法：**`ext_data_range(extdataname, stockcode, begintime, endtime, ContextInfo)`

**参数：**

| 参数名 | 类型 | 说明 | 提示 |
| --- | --- | --- | --- |
| `extdataname` | `string` | 扩展数据名 |  |
| `stockcode` | `string` | 证券代码 | 形式如 '600000.SH' |
| `begintime` | `string` | 区间的起始时间 | 格式为 '2016-08-02 12:12:30'（包括该时间点在内） |
| `endtime` | `string` | 区间的结束时间 | 格式为 '2017-08-02 12:12:30' （包括该时间点在内） |
| `ContextInfo` | `pythonObj` | Python对象 | Python 对象，这里必须是 ContextInfo |

**返回：** pythonDict

**示例：**

```
#coding:gbk
def init(ContextInfo):
	print(ext_data_range('mycci', '600000.SH','2022-08-02 12:12:30', '2023-08-02 12:12:30', ContextInfo))
```

## [\#](https://dict.thinktrader.net/innerApi/quote_function.html\#get-factor-value-%E8%8E%B7%E5%8F%96%E5%9B%A0%E5%AD%90%E6%95%B0%E6%8D%AE) get\_factor\_value - 获取因子数据

获取因子数据

**调用方法：**`get_factor_value(factorname, stockcode, deviation, ContextInfo)`

**参数：**

| 参数名 | 类型 | 说明 | 提示 |
| --- | --- | --- | --- |
| `factorname` | `string` | 因子名称 |  |
| `stockcode` | `string` | 证券代码 | 形式如 '600000.SH' |
| `deviation` | `number` | K 线偏移 | 0 不偏移，N 向右偏移 N，-N 向左偏移 N |
| `ContextInfo` | `pythonObj` | Python对象 | Python 对象，这里必须是 ContextInfo |

**返回：** number

**示例：**

```
#coding:gbk
def init(ContextInfo):
	print(get_factor_value('zzz', '600000.SH', 0, ContextInfo))
```

## [\#](https://dict.thinktrader.net/innerApi/quote_function.html\#get-factor-rank-%E8%8E%B7%E5%8F%96%E5%BC%95%E7%94%A8%E7%9A%84%E5%9B%A0%E5%AD%90%E6%95%B0%E6%8D%AE%E7%9A%84%E6%95%B0%E5%80%BC%E5%9C%A8%E6%89%80%E6%9C%89%E5%93%81%E7%A7%8D%E4%B8%AD%E6%8E%92%E5%90%8D) get\_factor\_rank - 获取引用的因子数据的数值在所有品种中排名

获取引用的因子数据的数值在所有品种中排名

**调用方法：**`get_factor_rank(factorname, stockcode, deviation, ContextInfo)`

**参数：**

| 参数名 | 类型 | 说明 | 提示 |
| --- | --- | --- | --- |
| `factorname` | `string` | 因子名称 |  |
| `stockcode` | `string` | 证券代码 | 形式如 '600000.SH' |
| `deviation` | `number` | K 线偏移 | 0 不偏移，N 向右偏移 N，-N 向左偏移 N |
| `ContextInfo` | `pythonObj` | Python对象 | Python 对象，这里必须是 ContextInfo |

**示例：**

```
#coding:gbk
def init(ContextInfo):
	print(get_factor_rank('zzz', '600000.SH', 0, ContextInfo))
```

## [\#](https://dict.thinktrader.net/innerApi/quote_function.html\#%E4%B8%8D%E6%8E%A8%E8%8D%90-call-vba-%E8%8E%B7%E5%8F%96%E5%BC%95%E7%94%A8%E7%9A%84-vba-%E6%A8%A1%E5%9E%8B%E8%BF%90%E8%A1%8C%E7%9A%84%E7%BB%93%E6%9E%9C) （不推荐）call\_vba - 获取引用的 VBA 模型运行的结果

更推荐函数：

- 获取历史数据 [call\_formula在新窗口打开](https://dict.thinktrader.net/innerApi/data_function.html?id=e2M5nZ#call-formula-%E8%B0%83%E7%94%A8%E6%A8%A1%E5%9E%8B)
- 订阅实时数据 [subscribe\_formula在新窗口打开](https://dict.thinktrader.net/innerApi/data_function.html?id=e2M5nZ#subscribe-formula-%E8%AE%A2%E9%98%85%E6%A8%A1%E5%9E%8B) 更多实时和历史数据调用的示例: [Python调用VBA因子公式的案例在新窗口打开](https://dict.thinktrader.net/codeExample/python%E8%B0%83%E7%94%A8VBA.html?id=e2M5nZ)

获取引用的 VBA 模型运行的结果

提示

注意

1. 使用该函数时需补充好本地 K 线或分笔数据

**调用方法：**`call_vba(factorname, stockcode,[period, dividend_type, barpos],ContextInfo)`

**参数：**

| 参数名 | 类型 | 说明 | 提示 |
| --- | --- | --- | --- |
| `factorname` | `string` | 因子名称 |  |
| `stockcode` | `string` | 证券代码 | 形式如 '600000.SH' |
| `period` | `string` | K 线偏移 | 可缺省，默认为当前主图周期线型 |
| `dividend_type` | `string` | 复权方式 | 可缺省，默认当前图复权方式，具体可选值如下 |
| `barpos` | `number` | 对应 bar 下标 | 可缺省，默认当前主图调用到的 bar 的对应下标xtInfo |
| `ContextInfo` | `pythonObj` | Python 对象 | Python 对象，这里必须是 ContextInfo |

- period 可选值：


> 'tick'：分笔线 '1d'：日线 '1m'：1分钟线 '3m'：3分钟线 '5m'：5分钟线 '15m'：15分钟线 '30m'：30分钟线 '1h'：小时线 '1w'：周线 '1mon'：月线 '1q'：季线 '1hy'：半年线 '1y'：年线

- dividend\_type 可选值：


> 'none'：不复权 'front'：向前复权 'back'：向后复权 'front\_ratio'：等比向前复权 'back\_ratio'：等比向后复权


**返回：** number

**示例：**

python

```
#coding:gbk
def init(ContextInfo):
	print(call_vba('MA.ma1', '600036.SH', ContextInfo))
```

@ tab 返回值

```
-1.0
```
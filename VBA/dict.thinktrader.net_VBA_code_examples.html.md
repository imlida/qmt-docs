---
url: "https://dict.thinktrader.net/VBA/code_examples.html"
title: "完整示例 | 迅投知识库"
---

## [\#](https://dict.thinktrader.net/VBA/code_examples.html\#%E8%A1%8C%E6%83%85%E7%A4%BA%E4%BE%8B) 行情示例

## [\#](https://dict.thinktrader.net/VBA/code_examples.html\#_1-vba%E6%A8%A1%E5%BC%8F%E8%8E%B7%E5%8F%96%E6%B6%A8%E5%81%9C%E8%82%A1) 1.VBA模式获取涨停股

本示例用于获取某列表涨停股。

示例

```
stocktype:=INBLOCK('创业板') or INBLOCK('科创板');

lastprice:=ref(c,1);

ZT:IF(stocktype,ROUNDS(lastprice*1.2,2)=close,ROUNDS(lastprice*1.1,2)=close);
```

## [\#](https://dict.thinktrader.net/VBA/code_examples.html\#_2-%E5%A6%82%E4%BD%95%E8%8E%B7%E5%8F%96%E8%BF%9E%E7%BB%AD%E6%94%BE%E9%87%8F%E6%A0%87%E7%9A%84) 2.如何获取连续放量标的

本示例用于获取连续放量标的。

示例

```
B:=VOL>REF(VOL,1);

UPVOL:COUNT(B,M)=M;//持续M个周期放量
```

## [\#](https://dict.thinktrader.net/VBA/code_examples.html\#_3-macd%E8%83%8C%E7%A6%BB%E6%A8%A1%E5%9E%8B) 3.MACD背离模型

在相应股票上,建立MACD背离模型，以指标背离作为入场或出出场参考。

示例

```
//…………MACD指标计算…………
DIFF:=EMA(CLOSE,12)-EMA(CLOSE,26);
DEA:=EMA(DIFF,9);
MACD:=2*(DIFF-DEA),COLORSTICK;
//……………………………………………………
N:=BARSLAST(CROSS(DIFF,DEA))+1;
N1:=BARSLAST(CROSS(DEA,DIFF))+1;
DIFF1:=REF(REF(DIFF,N-1),1);
DIFF2:=REF(REF(DIFF,N1-1),1);
C1:=REF(REF(C,N-1),1);
C2:=REF(REF(C,N1-1),1);
DBL1:DIFF>DIFF1 AND CROSS(DIFF,DEA) AND C<C1; //底背离
DBL:DIFF<DIFF2 AND  CROSS(DEA,DIFF) AND C>C2; //顶背离
```

## [\#](https://dict.thinktrader.net/VBA/code_examples.html\#_4-%E5%A6%82%E4%BD%95%E5%9C%A8%E6%97%A5%E5%86%85%E5%88%86%E9%92%9F%E7%BA%A7%E5%88%AB%E5%AE%9E%E7%8E%B0%E3%80%8A%E8%8F%B2%E9%98%BF%E9%87%8C%E5%9B%9B%E4%BB%B7%E3%80%8B%E5%88%A4%E6%96%AD) 4.如何在日内分钟级别实现《菲阿里四价》判断

什么是菲阿里四价？

昨天高点、昨天低点、昨日收盘价、今天开盘价，可并称为菲阿里四价。它由日本期货冠军菲阿里实盘采用的主要突破交易参照系统。

主要特点： 日内交易策略，收盘平仓；

上轨＝昨日高点；

下轨＝昨日低点；

核心点

实现该指标的核心在于跨周期引用数据，可以使用CALLSTOCK函数。

用法

CALLSTOCK(CODE,TYPE\[,CYC,N\]),引用指定品种代码为 CODE,周期为 CYC(可选)若不填或者为-1 表示使用当前周期,类型为 TYPE 的数据

N 为左右偏移周期个数（可选）0 表示引用当前数据，<0 为引用之前数据，>0为引用之后数据。

其中 TYPE 的值可为 VTOPEN(开盘) VTHIGH(最高) VTLOW(最低) VTCLOSE(收盘)

VTVOL(成交量) VTAMOUNT(成交额) vtOPENINT(持仓量) VTADVANCE(涨数,大盘有效) VTDECLINE(跌数,大盘有效)以及外部数据和万德数据

如果找不到同期数据，那么将返回最近的一个。

CYC 范围为 0-19，分别表示

0:分笔成交、1:1 分钟、2:5 分钟、3:15 分钟、4:30 分钟、5:60 分钟

6:日、7:周、8:月、9:年、10:多日、11:多分钟、12:多秒

13:多小时、14:季度线、15:半年线、16:节气线、17:3 分钟、18:10 分钟、19:多笔线

`注意`引用数据时，需要确认被引用品种周期数据齐全，在首次使用或者在不确定时，请手工进行数据补充工作；

示例

```
昨高:=callstock('',vthigh,6,-1);//获取当前主图标的昨高
昨低:=callstock('',vtlow,6,-1);//获取当前主图标的昨低
昨收:=callstock('',vtclose,6,-1);//获取当前主图标的昨收
上轨:昨高;
下轨:昨低;
```

## [\#](https://dict.thinktrader.net/VBA/code_examples.html\#%E5%9B%9E%E6%B5%8B%E7%A4%BA%E4%BE%8B) 回测示例

### [\#](https://dict.thinktrader.net/VBA/code_examples.html\#%E5%8D%95%E8%82%A1%E5%9B%9E%E6%B5%8B%E6%A8%A1%E5%9E%8Ba) 单股回测模型A

参见默认脚本`单股回测模型A`

### [\#](https://dict.thinktrader.net/VBA/code_examples.html\#%E5%8D%95%E8%82%A1%E5%9B%9E%E6%B5%8B%E6%A8%A1%E5%9E%8Bb) 单股回测模型B

参见默认脚本`单股回测模型B`
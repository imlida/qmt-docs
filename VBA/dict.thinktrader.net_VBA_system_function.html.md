---
url: "https://dict.thinktrader.net/VBA/system_function.html"
title: "系统函数 | 迅投知识库"
---

迅投量化投研平台编辑器提供完备的下单交易函数，可以完成下单、监控以及控制一系列的交易逻辑。

### [\#](https://dict.thinktrader.net/VBA/system_function.html\#%E4%B8%8B%E5%8D%95%E5%87%BD%E6%95%B0) 下单函数

#### [\#](https://dict.thinktrader.net/VBA/system_function.html\#passorder%E5%87%BD%E6%95%B0) PASSORDER函数

提示

该函数的参数值与内置python的passorder一致，具体值请参考内置python文档

```
passorder(opType, orderType, accountID , orderCode, prType, price, volume)
[, orderCode, prType, price, volume] 不是必填，默认值分别是, "", -1, 0.0, 0
```

参数数据类型

[opType：数字型（int）](https://dict.thinktrader.net/innerApi/enum_constants.html#optype-%E6%93%8D%E4%BD%9C%E7%B1%BB%E5%9E%8B)

[orderType：数字型（int）](https://dict.thinktrader.net/innerApi/enum_constants.html#ordertype-%E4%B8%8B%E5%8D%95%E6%96%B9%E5%BC%8F)

[accountID：字符串型（string）](https://dict.thinktrader.net/innerApi/trading_function.html?id=null#passorder-%E7%BB%BC%E5%90%88%E4%B8%8B%E5%8D%95%E5%87%BD%E6%95%B0)

[orderCode：字符串型（string）](https://dict.thinktrader.net/innerApi/trading_function.html?id=null#passorder-%E7%BB%BC%E5%90%88%E4%B8%8B%E5%8D%95%E5%87%BD%E6%95%B0)

[prType：数字型（int）](https://dict.thinktrader.net/innerApi/enum_constants.html#prtype-%E4%B8%8B%E5%8D%95%E9%80%89%E4%BB%B7%E7%B1%BB%E5%9E%8B)

[price：数字型（double）](https://dict.thinktrader.net/innerApi/trading_function.html?id=null#passorder-%E7%BB%BC%E5%90%88%E4%B8%8B%E5%8D%95%E5%87%BD%E6%95%B0)

[volume：数字型（double）](https://dict.thinktrader.net/innerApi/trading_function.html?id=null#passorder-%E7%BB%BC%E5%90%88%E4%B8%8B%E5%8D%95%E5%87%BD%E6%95%B0)

一个发出套利单的例子：

```
stockAccountID:='6000000255';
futureAccountID:='039427';
basketName:='testStock';//预先建立好的股票篮子
futureName:='IF1703';
operateType:=25;//股票篮子买入
orderType:=2333;//按账号可用方式下单并套利
priceType:=5;//以最新价
model_price:=1;//套利比例 100%，这里对 modelprice 这个字段进行了重用作 套利比例用
volume:=0.05;//以账号可用资金的 5%买入篮子

AccountID:=stockAccountID+','+futureAccountID;
orderCode:=basketName+','+futureName;

doit:=0;
if c>o then //是阳线就套利
begin
//下套利单
orderType=2333
passorder(operateType,orderType,AccountID,orderCode,priceType,model_price,volume)
;
doit:=1;
end
single:doit;
```

passorder支持融资融券

#### [\#](https://dict.thinktrader.net/VBA/system_function.html\#holding-%E8%BF%94%E5%9B%9E%E6%8C%81%E4%BB%93%E6%95%B0%E9%87%8F) holding :返回持仓数量

调用：holding(AccountID , MarketID, StockID,Direction);

AccountID, MarketID, StockID 为字符串；Direction 为整型（1 多，2 空）

```
例 ：
ho:=holding(‘037055’,’IF’,’IF06’,2);
```

#### [\#](https://dict.thinktrader.net/VBA/system_function.html\#holdings-%E8%BF%94%E5%9B%9E%E6%8C%81%E4%BB%93%E4%BF%A1%E6%81%AF) holdings：返回持仓信息

调用：holdings(Account);

```
例 1：
xxx:= holdings(‘037055’);

例 2：
统计某个账号所有品种做多方向的持仓
xxx := holdings('037055');
loh := 0;
for x in xxx do begin if
x.direction = 48 then loh:=
loh + x.volume;
end
longhold:loh;
```

**xxx 为一个 positiondetail 结构体，含有如下项：**

```
exchangeid 证券市场,交易所代码
exchangename 市场名字
productid 品种代码
productname 品种名称
instrumentid 证券代码,合约代码
instrumentname 证券名称,合约名称
hedgeflag 投保
direction 买卖
opendate 成交日期
tradeid 最初开仓位的成交
volume 持仓量 当前拥股
openprice 开仓价
tradingday 交易日
margin 使用的保证金 历史的直接用 ctp 的，新的自己用成本价*存量*系数算 股票不需要
opencost 开仓成本 等于股票的成本价*第一次建仓的量，后续减持不影响，不算手续费 股票不需要
settlementprice /结算价 对于股票的当前价
closevolume 平仓量 等于股票已经卖掉的 股票不需要
closeamount 平仓额 等于股票每次卖出的量*卖出价*合约乘数（股票为 1）的累加 股票不需要
dloatprofit 浮动盈亏 当前量*（当前价-开仓价）*合约乘数（股票为 1）
closeprofit 平仓盈亏 平仓额 - 开仓价*平仓量*合约乘数（股票为 1） 股票不需要
marketvalue 市值 合约价值
positioncost 持仓成本 股票不需要
positionprofit 持仓盈亏 股票不需要
lastsettlementprice 最新结算价 股票不需要
instrumentvalue 合约价值 股票不需要
istoday 是否今仓
xttag 迅投量化投研平台标签
stockholder 股东账号
frozenvolume 期货不用这个字段，冻结数量
canusevolume 期货不用这个字段，股票的可用数量
onroadvolume 期货不用这个字段，股票的在途数量
yesterdayvolume 期货不用这个字段，股票的股份余额
lastprice 结算价 对于股票的当前价
profitrate 持仓盈亏比例
futuretradetype 成交类型
expiredate 到期日，逆回购用
comtradeid 套利成交 Id
legid 组合 Id
totalcost 自定义累计成本 股票信用用到
singlecost 自定义单股成本 股票信用用到
coveredvolume 用于个股期权
sideflag 用于个股期权，标记 '0' - 权利，'1' - 义务，'2' - '备兑'
referencerate 汇率,目前用于港股通
structfundvol 分级基金可用（可分拆或可合并）
redemptionvolume 分级基金可赎回量
```

#### [\#](https://dict.thinktrader.net/VBA/system_function.html\#ordering-%E8%BF%94%E5%9B%9E%E5%BD%93%E5%89%8D%E5%A7%94%E6%89%98%E6%95%B0%E9%87%8F) ordering：返回当前委托数量

调用：ordering(AccountID , MarketID, StockID,Direction);

AccountID, MarketID, StockID 为字符串；Direction 为整型（1 buy，2 sell）

```
例 ：
ord:=ordering(‘037055’,’IF’,’IF06’,2);
（7） orderings：返回委托信息
调用：orderings(AccountID);

例：
统计某账号买入的所有的当天委托量
xxx := orderings('037055');
loo := 0;
for x in xxx do begin
if x.direction = 48 then
loo:= loo + x.volumetotaloriginal;
end
longorder:loo
```

**xxx 为一个 orderdetail 结构体，含有如下项：**

```
exchangeid 证券市场,交易所代码
exchangename 市场名字
productid 品种代码
productname 品种名称
instrumentid 证券代码,合约代码
instrumentname 证券名称,合约名称
sessionid
frontid 前端 id
orderref 下单引用 等于股票的内部委托号
orderpricetype 类型，例如市价单 限价单
direction 期货多空 股票买卖
offsetflag 期货开平，股票买卖其实就是开平
hedgeflag 投保
limitprice 限价单的限价，就是报价
volumetotaloriginal 最初委托量
ordersubmitstatus 提交状态
ordersysid 委托号
orderstatus 委托状态
volumetraded 已成交量
volumetotal 当前总委托量 股票不需要总委托量
errorid
errormsg 状态信息
taskid
frozenmargin 冻结保证金
frozencommission 冻结手续费
insertdate 日期
inserttime 时间
xttag 迅投量化投研平台标签
tradeprice 成交均价
cancelamount 已撤数量
optname 展示委托属性的中文
tradeamount 成交额 期货=均价*量*合约乘数
entrusttype 委托类别
cancelinfo 废单原因
undercode 标的证券
covereflag 备兑标记 '0' - 非备兑，'1' - 备兑
orderpricermb 委托价格 人民币 用于港股通
tradeamountrmb 成交金额 人民币用于港股通
referencerate 参考汇率 用于港股通
```

#### [\#](https://dict.thinktrader.net/VBA/system_function.html\#deal-%E8%BF%94%E5%9B%9E%E6%9F%90%E4%B8%AA%E6%97%B6%E9%97%B4%E5%86%85%E7%9A%84%E6%88%90%E4%BA%A4%E6%95%B0%E9%87%8F) deal：返回某个时间内的成交数量

调用：deal(AccountID , MarketID, StockID,Direction);

或 deal(AccountID , MarketID, StockID,Direction,Seconds);

AccountID, MarketID, StockID 为字符串；Direction 为整型（1 多，2 空）, Seconds 为整型，表示多少秒内。

```
例 1：
de:=deal(‘037055’,’IF’,’IF06’,2);//返回当天 IF06 的 sell 的成交数量

例 2：
de:=deal(‘037055’,’IF’,’IF06’,2,90);//返回最近 90 秒内 IF06 的 sell 的成交数量
```

#### [\#](https://dict.thinktrader.net/VBA/system_function.html\#deals-%E8%BF%94%E5%9B%9E%E6%88%90%E4%BA%A4%E4%BF%A1%E6%81%AF) deals：返回成交信息

调用：deals(AccountID);

```
例：
返回某账号当天 buy 的成交量
xxx := deals('037055');
dea:= 0;
for x in xxx do begin if
x.direction = 48 then dea:=
dea + x.volume;
end
longdeal:dea
```

**xxx 为一个 dealdetail 结构体，含有如下项：**

```
exchangeid 证券市场,交易所代码
exchangename 市场名字
productid 品种代码
productname 品种名称
instrumentid 证券代码,合约代码
instrumentname 证券名称,合约名称
tradeid 成交编号
orderref 下单引用 等于股票的内部委托号
ordersysid 委托号
direction 买卖 股票不需要
offsetflag 开平 股票的买卖
hedgeflag 投保 股票不需要
price 成交均价
volume 成交量 期货单位手 股票做到股
tradedate 成交日期
tradetime 成交时间
comssion 手续费
tradeamount 成交额 期货=均价*量*合约乘数
taskid
xttag 迅投量化投研平台标签
orderpricetype 类型，例如市价单 限价单
optname 展示委托属性的中文
entrusttype 委托类别
futuretradetype 成交类型
realoffsetflag 实际开平,主要是区分平今和平昨
coveredflag 备兑标记 '0' - 非备兑，'1' - 备兑
closetodayvolume 平今量, 不显示
orderpricermb 委托价格 人民币 用于港股通
pricermb 目前用于港股通
tradeamountrmb 目前用于港股通
referencerate 汇率,目前用于港股通
xttrade 是否是迅投量化投研平台交易
```

#### [\#](https://dict.thinktrader.net/VBA/system_function.html\#cancel-%E6%92%A4%E5%8D%95) cancel:撤单

cancel(委托号);

一个模型例子：

```
//VAB 不区分大小写
variable:TestHolding=0;
//……………………下单参数定义……………………………………
ORDERTYPE:=1101; //下单类型
ACCOUNTID:='6000000248'; //填写对应资金账号,用户修改成自己的资金账号
ORDERCODE:=STKLABEL(); //下单代码,当前主图品种
PRICETYPE:=5; //下单价格类型,4 卖 1 价,5 最新价,6 买 1 价,12 市价,13 挂单价,
14 对手价,11(指定价)模型价VOLUME:=100; //下单数量,100 股
//………………………下单…………………………………………
DIFF := EMA(CLOSE,12) - EMA(CLOSE,26);
DEA := EMA(DIFF,9);
MACD1 := 2*(DIFF-DEA), COLORSTICK;
t:BARSLAST(TestHolding=0),nodraw;//////////持仓周期
bk:= macd1>0;//////////开仓条件,可根据需要更改,如 bk:= close>open;
bp:=(t>=3 and macd1<0);////平仓条件
nn:=0;//////往后推迟几个周期交易//
IF (ref(Bk,nn) and not(bp) and TestHolding=0 ） THEN BEGIN
TestHolding:=1;
BBD:=BARPOS;
DRAWTEXT(1 ,H+4,'买入');
VERTLINE(1 ,h+10,l-10,coloryellow,1,VTDOT);
PASSORDER(23{股票买入},ORDERTYPE{单股下单类型},ACCOUNTID{账号},ORDERCODE{下单代
码},PRICETYPE{选价类型},-1{下单价格选价类型非指定价填-1},VOLUME{下单量});
END
//卖出
IF (ref(bp,nn) AND TestHolding>0 ) THEN BEGIN
TestHolding:=0;
BBD:=0;
DRAWTEXT(1,H+1,'卖出');
VERTLINE(1,h+10,l-10,colorwhite,1,VTDOT);
PASSORDER(24{股票买入},ORDERTYPE,ACCOUNTID,ORDERCODE,PRICETYPE,-1,VOLUME);
END
//..................撤单..................................
orderss:= orderings(ACCOUNTID);//获取该资金账号所有的委托信息
nowtime:=CURRENTTIME();
for ord in orderss do begin
insertime:=strtonum(ord.inserttime);
if isequalv(ord.orderstatus,56)=0 and nowtime - insertime > 30 then begin //委托没
成功,30s 内没成交或部分成交则撤单
cancel(ord.ordersysid);
end
end
```

## [\#](https://dict.thinktrader.net/VBA/system_function.html\#%E7%9B%91%E6%8E%A7%E5%87%BD%E6%95%B0) 监控函数

迅投量化投研平台提供 TACCOUNT、MARKETVALUE、HOLDING 和 HOLDINGS 函数用以监控账户资金以及持仓状况

**TACCOUNT**

| 释义 | 获取指定账号的可用资金 |
| --- | --- |
| 用法 | TACCOUNT(1,'37500001');1 表示是期货账号，2 为普通股票账号，3 为信用账号，37500001 是账号 ID |
| 示例 | TACCOUNT(1,'37500001');//获取37500001期货账号的可用资金 |

**HOLDING**

| 释义 | 得到当前帐户持仓量,多仓返回正数空仓返回负数 |
| --- | --- |
| 用法 | HOLDING(AccountID,MarketID,StockID,Direction);<br>AccountID,MarketID,StockID为字符串；Direction为整型（1多,2空） |
| 示例 | HOLDING('037055','IF','IF09',2);//获取037055账号IF09做空持仓；<br>HOLDING('6000000255','SH','600000',1)//获取6000000255账号的股票600000的持仓 |

**HOLDINGS**

| 释义 | 取某资金帐号对应的持仓 |
| --- | --- |
| 用法 | HOLDINGS(Account)表示获取帐号"Account"的持仓 |
| 示例 | 统计某个账号所有品种做多方向的持仓<br>xxx := holdings('037055');<br>loh := 0;<br>for x in xxx do begin<br>if x.direction = 48 then<br>loh := loh+xvolume;<br>end<br>longhold:loh;<br>xxx为一个positiondetail结构体,含有如下项：<br>exchangeid证券市场,交易所代码<br>exchangename市场名字<br>productid品种代码<br>productname品种名称<br>instrumentid证券代码,合约代码<br>instrumentname证券名称,合约名称<br>hedgeflag投保<br>direction买卖<br>opendate成交日期<br>tradeid最初开仓位的成交<br>volume持仓量当前拥股<br>openprice开仓价<br>tradingday交易日<br>margin使用的保证金历史的直接用ctp的,新的自己用成本价 _存量_ 系数算股票不需要<br>opencost开仓成本等于股票的成本价 _第一次建仓的量,后续减持不影响,不算手续费股票不需要_<br>_settlementprice/结算价对于股票的当前价_<br>_closevolume平仓量等于股票已经卖掉的股票不需要_<br>_closeamount平仓额等于股票每次卖出的量_ 卖出价 _合约乘数（股票为1）的累加股票不需要_<br>_dloatprofit浮动盈亏当前量_（当前价-开仓价） _合约乘数（股票为1）_<br>_closeprofit平仓盈亏平仓额-开仓价_ 平仓量\*合约乘数（股票为1）股票不需要<br>marketvalue市值合约价值<br>positioncost持仓成本股票不需要<br>positionprofit持仓盈亏股票不需要<br>lastsettlementprice最新结算价股票不需要<br>instrumentvalue合约价值股票不需要<br>istoday是否今仓<br>xttag迅投标签<br>stockholder股东账号<br>frozenvolume期货不用这个字段,冻结数量<br>canusevolume期货不用这个字段,股票的可用数量<br>onroadvolume期货不用这个字段,股票的在途数量<br>yesterdayvolume期货不用这个字段,股票的股份余额<br>lastprice结算价对于股票的当前价<br>profitrate持仓盈亏比例<br>futuretradetype成交类型<br>expiredate到期日,逆回购用<br>comtradeid套利成交Id<br>legid组合Id<br>totalcost自定义累计成本股票信用用到<br>singlecost自定义单股成本股票信用用到<br>coveredvolume用于个股期权<br>sideflag用于个股期权,标记'0'-权利,'1'-义务,'2'-'备兑'<br>referencerate汇率,目前用于港股通<br>structfundvol分级基金可用（可分拆或可合并）<br>redemptionvolume分级基金可赎回量 |

**MARKETVALUE**

| 释义 | 获取指定账号的可用资金 |
| --- | --- |
| 用法 | MARKETVALUE(2，'AA'）表示返回帐号"AA"的股票市值，1 表示是期货账号，2 为普通股票账号，3 为信用账号 |
| 示例 | MARKETVALUE(2，'37500001'）;//返回股票账号37500001的股票市值 |

## [\#](https://dict.thinktrader.net/VBA/system_function.html\#%E6%8E%A7%E5%88%B6%E5%87%BD%E6%95%B0) 控制函数

控制函数主要有 SLEEP 和 CANCEL 函数分别用于控制下单的频率以及对于指定报单指令执行撤单。

**CANCEL**

| 释义 | 针对委托号进行撤单 |
| --- | --- |
| 用法 | CANCEL(orderId,accountID,accountType)表示撤销委托号为orderId的委托<br>orderId：委托号；<br>accountId:资金账号；<br>accountType:资金账号类型（'STOCK','FUTURE','CREDIT','HUGANGTONG','SHENGANGTONG'） |
| 示例 | CANCEL('1234','37500001','STOCK');//撤销股票账号37500001委托号为1234的委托 |

**SLEEP**

| 释义 | 函数作用于模型的最后一个周期，在最新周期中延时设定的时间之后再执行之后的语句 |
| --- | --- |
| 用法 | SLEEP(D),D 为延时的设置时间，单位为毫秒(1 秒钟等于 1000 毫秒) |
| 示例 | SLEEP(1000);//表示等待 1 秒后再执行下行语句 |

## [\#](https://dict.thinktrader.net/VBA/system_function.html\#%E7%BA%BF%E5%9E%8B%E6%8F%8F%E8%BF%B0) 线型描述

**COLORBLACK**

| 释义 | 设为黑色 |
| --- | --- |
| 示例 | MA1:MA(CLOSE,5),COLORBLACK；//5日均线设为黑色 |

**COLORBLUE**

| 释义 | 设为蓝色 |
| --- | --- |
| 示例 | MA1:MA(CLOSE,5),COLORBLUE；//5日均线设为蓝色 |

**COLORBROWN**

| 释义 | 设为棕色 |
| --- | --- |
| 示例 | MA1:MA(CLOSE,5),COLORBROWN；//5日均线设为棕色 |

**COLORCYAN**

| 释义 | 设为青色 |
| --- | --- |
| 示例 | MA1:MA(CLOSE,5),COLORCYAN；//5日均线设为青色 |

**COLORGRAY**

| 释义 | 设为灰色 |
| --- | --- |
| 示例 | MA1:MA(CLOSE,5),COLORGRAY；//5日均线设为灰色 |

**COLORGREEN**

| 释义 | 设为绿色 |
| --- | --- |
| 示例 | MA1:MA(CLOSE,5),COLORGREEN；//5日均线设为绿色 |

**COLORMAGENTA**

| 释义 | 设为晶红色 |
| --- | --- |
| 示例 | MA1:MA(CLOSE,5),COLORMAGENTA；//5日均线设为晶红色 |

**COLORRED**

| 释义 | 设为红色 |
| --- | --- |
| 示例 | MA1:MA(CLOSE,5),COLORRED；//5日均线设为红色 |

**COLORWHITE**

| 释义 | 设为白色 |
| --- | --- |
| 示例 | MA1:MA(CLOSE,5),COLORWHITE；//5日均线设为白色 |

**COLORYELLOW**

| 释义 | 设为黄色 |
| --- | --- |
| 示例 | MA1:MA(CLOSE,5),COLORYELLOW；//5日均线设为黄色 |

**COLORSTICK**

| 释义 | 颜色柱状线：以零轴为中心画彩色棒状线，零轴下为阴线颜色，零轴上为阳线颜色 |
| --- | --- |
| 示例 | CLOSE-OPEN,COLORSTICK;//收盘价大于开盘价画阳线颜色，收盘价小于开盘价画阴线颜色 |

**CIRCLEDOT**

| 释义 | 画小圆圈线 |
| --- | --- |
| 示例 | CLOSE,CIRCLEDOT;//收盘价画小圆圈线 |

**NOAXIS**

| 释义 | 无坐标：不影响坐标最高最低值 |
| --- | --- |
| 示例 | C,NOAXIS; //输出收盘价,但不影响坐标最高最低值,用于叠加到其它指标上 |

**LINETHICK**

| 释义 | 改变指标线粗细 |
| --- | --- |
| 示例 | MA1:MA(CLOSE,5),LINETHICK3;//5日均线绘制使用3号粗细度 |

## [\#](https://dict.thinktrader.net/VBA/system_function.html\#%E9%80%BB%E8%BE%91%E5%87%BD%E6%95%B0) 逻辑函数

**ALL**

| 释义 | 是否一直满足条件 |
| --- | --- |
| 用法 | ALL(X,N),统计 N 周期中是否一直都满足 X 条件,若 N=0 则从第一个有效值开始 |
| 示例 | ALL(CLOSE>OPEN,20);//表示是否 20 周期内全部都收阳线，满足返回1，不满足返回0 |

**ANY**

| 释义 | 是否存在 |
| --- | --- |
| 用法 | ANY(X,N)返回 N 周期内是否存在满足条件 X,N 可为常数或变量,若 N=0 则从第一个有效值开始 |
| 示例 | ANY(C>O,10);//表示 10 个周期中存在阳线，存在返回1，不存在返回0 |

**BETWEEN**

| 释义 | 介于两个数之间 |
| --- | --- |
| 用法 | BETWEEN(A,B,C)表示 A 处于 B 和 C 之间时返回 1，否则返回 0 |
| 示例 | BETWEEN(CLOSE,MA(CLOSE,10),MA(CLOSE,5));//表示收盘价介于5 日均线和10日均线之间，满足返回1，不满足返回0 |

**CROSS**

| 释义 | 两条线交叉 |
| --- | --- |
| 用法 | CROSS(A,B)表示当 A 从下方向上穿过 B 时返回 1，否则返回 0 |
| 示例 | CROSS(MA(CLOSE,5),MA(CLOSE,10));//表示 5 日均线与 10 日均线交金叉 |

**IF**

| 释义 | 根据条件求不同的值 |
| --- | --- |
| 用法 | IF(X,A,B)若 X 不为 0 则返回 A,否则返回 B |
| 示例 | IF(CLOSE>OPEN,HIGH,LOW);//表示该周期收阳则返回最高值，否则返回最低值 |

**IFN**

| 释义 | 根据条件求不同的值 |
| --- | --- |
| 用法 | IFN(X,A,B)若 X 不为 0 则返回 B,否则返回 A |
| 示例 | IFN(CLOSE>OPEN,HIGH,LOW);//表示该周期收阴则返回最高值,否则返回最低值 |

**ISDOWN**

| 释义 | 该周期是否收阴 |
| --- | --- |
| 用法 | ISDOWN() |
| 示例 | ISDOWN();//当收盘价<开盘价时，返回值为 1，否则为 0 |

**ISEQUAL**

| 释义 | 该周期是否平盘 |
| --- | --- |
| 用法 | ISEQUAL() |
| 示例 | ISEQUAL();//当收盘价=开盘价时，返回值为 1，否则为 0 |

**ISLASTBAR**

| 释义 | 该周期是否为最后一个周期。 |
| --- | --- |
| 用法 | ISLASTBAR |
| 示例 | ISLASTBAR;//最后一个周期返回值为 1，其余为 0 |

**ISUP**

| 释义 | 该周期是否收阳。 |
| --- | --- |
| 用法 | SUP() |
| 示例 | SUP();//当收盘价>开盘价时，返回值为 1，否则为 0 |

**LAST**

| 释义 | 持续存在 |
| --- | --- |
| 用法 | LAST(X,A,B)返回第前 A 周期到第前 B 周期是否一直满足条件 X <br> 若 A 为 0，表示从第一天开始，B 为 0，表示到最后日止 |
| 示例 | LAST(C>O,10,5);//表示从第前10个周期到第前5个周期内一直是阳线，满足返回1，不满足返回0 |

**LONGCROSS**

| 释义 | 两条线维持一定周期后交叉。 |
| --- | --- |
| 用法 | LONGCROSS(A,B,N)表示 A 在 N 周期内都小于 B，本周期从下方向上穿过 B 时返回 1，否则返回 0 |
| 示例 | LONGCROSS(MA(CLOSE,5),MA(CLOSE,10),5);//5日均线在5周期后与10日均线交金叉，满足返回1，不满足返回0 |

**NOT**

| 释义 | 求逻辑非。 |
| --- | --- |
| 用法 | NOT(X)返回非 X,即当 X=0 时返回 1，否则返回 0 |
| 示例 | NOT(ISUP);//表示平盘或收阴 |

**RANGE**

| 释义 | 介于某个范围之间。 |
| --- | --- |
| 用法 | RANGE(A,B,C)表示 A 大于 B 同时小于 C 时返回 1，否则返回 0 |
| 示例 | RANGE(CLOSE,MA(CLOSE,5),MA(CLOSE,10));//表示收盘价大于5日均线并且小于10日均线，满足返回1，不满足返回0 |

**VALID**

| 释义 | 判断指定值是否是有效数据 |
| --- | --- |
| 用法 | VALID(X) |
| 示例 | VALID(X);//当 X 为有效数据时返回 1,否则返回 0 |

**VALUEWHEN**

| 释义 | 条件跟随：当条件 COND 满足时，取当时的 DATA 的值，否则取得 VALUEWHEN 的前一个值。 |
| --- | --- |
| 用法 | VALUEWHEN(COND, DATA) |
| 示例 | VALUEWHEN(HIGH>REF(HIGH,5),HIGH);// 表示当前最高价大于前五个周期最高价的最大值时返回当前最高价 |

## [\#](https://dict.thinktrader.net/VBA/system_function.html\#%E5%8A%A8%E6%80%81%E8%A1%8C%E6%83%85) 动态行情

| 序号 | 函数名称 | 释义 |
| --- | --- | --- |
| 1 | DYNAINFO(3) | 取得最新动态行情: 昨收 |
| 2 | DYNAINFO(4) | 取得最新动态行情: 今开 |
| 3 | DYNAINFO(5) | 取得最新动态行情: 最高 |
| 4 | DYNAINFO(6) | 取得最新动态行情: 最低 |
| 5 | DYNAINFO(7) | 取得最新动态行情: 最新 |
| 6 | DYNAINFO(8) | 取得最新动态行情: 总手 |
| 7 | DYNAINFO(9) | 取得最新动态行情: 现手 |
| 8 | DYNAINFO(10) | 取得最新动态行情: 总额 |
| 9 | DYNAINFO(12) | 取得最新动态行情: 涨跌 |
| 10 | DYNAINFO(13) | 取得最新动态行情: 振幅 |
| 11 | DYNAINFO(14) | 取得最新动态行情: 涨幅 |
| 12 | DYNAINFO(15) | 取得最新动态行情: 委比 |
| 13 | DYNAINFO(16) | 取得最新动态行情: 委差 |
| 14 | DYNAINFO(18) | 取得最新动态行情: 委买 |
| 15 | DYNAINFO(19) | 取得最新动态行情: 委卖 |
| 16 | DYNAINFO(20) | 取得最新动态行情: 委买价 |
| 17 | DYNAINFO(21) | 取得最新动态行情: 委卖价 |
| 18 | DYNAINFO(25) | 取得最新动态行情: 买一量 |
| 19 | DYNAINFO(26) | 取得最新动态行情: 买二量 |
| 20 | DYNAINFO(27) | 取得最新动态行情: 买三量 |
| 21 | DYNAINFO(28) | 取得最新动态行情: 买一价 |
| 22 | DYNAINFO(29) | 取得最新动态行情: 买二价 |
| 23 | DYNAINFO(30) | 取得最新动态行情: 买三价 |
| 24 | DYNAINFO(31) | 取得最新动态行情: 卖一量 |
| 25 | DYNAINFO(32) | 取得最新动态行情: 卖二量 |
| 26 | DYNAINFO(33) | 取得最新动态行情: 卖三量 |
| 27 | DYNAINFO(34) | 取得最新动态行情: 卖一价 |
| 28 | DYNAINFO(35) | 取得最新动态行情: 卖二价 |
| 29 | DYNAINFO(36) | 取得最新动态行情: 卖三价 |
| 30 | markettime | 获取主图品种市场的最新时间：markettime;获取指定市场的最新时间：markettime('SH'); |

## [\#](https://dict.thinktrader.net/VBA/system_function.html\#%E7%BB%98%E5%9B%BE%E5%87%BD%E6%95%B0) 绘图函数

**BARSSET**

| 释义 | 绘制标记. |
| --- | --- |
| 用法 | BARSSET(COND,PRICE,N,OFFSET),当 COND 条件满足时，在 PRICE 位置绘制标记。N 为所影响到的周期数。OFFSET为影响周期的偏移，0 为以中心点前后影响，负数为往前影响，正数为向后影响。该函数通常用在逻辑公式中。 |
| 示例 | BARSSET( BARPOS=100,HIGH ,10 ,0);//以第100根k线位置，前后10个周期内绘制标记。 |

**COLORRGB**

| 释义 | 将红，绿，蓝三基色混和成一个颜色值。 |
| --- | --- |
| 用法 | COLORRGB(R,G,B)R,G,B 分别取值为 0－255。该函数仅用在 DRAWTEXT 等画线函数中 COLOR 参数指定颜色使用。 |
| 示例 | DRAWTEXT(CLOSE/OPEN>1.08,LOW,'大阳线',COLORRGB(255,0,0));//将显示红色的大阳线文字。<br> 若用户需指定指标线颜色，请使用 COLOR00FFFF 等这种语法 |

**DRAWTEXT**

| 释义 | 在图形上显示文字。 |
| --- | --- |
| 用法 | DRAWTEXT(COND,PRICE,TEXT\[,COLOR,ALIGN\]),当 COND 条件满足时,在 PRICE 位置书写文字 TEXT。COLOR(可选参数)文字颜色,ALIGN(可选参数)对齐方式 0 中对齐;1 左对齐;2 右对齐;3 图中;4 图上;5 图下。 |
| 示例 | DRAWTEXT(CLOSE/OPEN>1.08,LOW,'大阳线');//表示当日涨幅大于 8%时在最低价位置显示"大阳线"字样 |

**KLINE**

| 释义 | 在图形上绘制 K 线. |
| --- | --- |
| 用法 | KLINE(O,H,L,C,T)O,H,L,C 分别为开高低收T<br>为绘制类型,0 表示与主图 K 线画法相同,1 表示不影响坐标高低值,可用于叠加在其它指标上 |
| 示例 | 例 1：KLINE(O,H,L,C,0);<br> 例 2：在 KDJ 公式中叠加 KLINE(O,H,L,C,1); |

**VERTLINE**

| 释义 | 在图形上绘制垂直线。 |
| --- | --- |
| 用法 | VERTLINE(COND,\[PRICE1,PRICE2,COLOR,WIDTH,TYPE\])<br>当 COND 条件满足时，在 PRICE1 和 PRICE2 之间画线。<br>PRICE1 和 PRICE2 均省略时表示在窗格高低之间画垂直线<br>COLOR(可选参数)为线颜色<br>WIDTH(可选参数)为线的宽度<br>TYPE(可选参数)为线的风格分别为：<br>VTSOLID 普通线<br>VTDASH 虚线<br>VTDOT 点线<br>VTDASHDOT 虚线和点交替<br>VTDASHDOTDOT 虚线和两点交替。<br>除了 VTSOLID 风格以外，其他风格必须保证 WIDTH 为 1 时才有效。 |
| 示例 | VERTLINE(c>o,10,20,colorred,1,VTDASH);//K线收阳，在10-20间使用1号粗细度画普通线 |

**DRAWICON**

| 释义 | 在图形上绘制小图标。 |
| --- | --- |
| 用法 | DRAWICON(COND,PRICE,TYPE\[,ALIGN\]),当 COND 条件满足时,在 PRICE 位置画TYPE 号图标。<br>ALIGN(可选参数)对齐方式 ,0 图标中对齐;1 图标上缘;2 图标中缘;3 图中;4图上;5 图下<br>例如：DRAWICON(CLOSE>OPEN,LOW,1)表示当收阳时在最低价位置画 1 号图标。<br>绘制图标按照“符号”工具栏对应排列，如果要自定义绘制的图标，请在“自定义”工具栏操作里将对应的图标修改即可。 |
| 示例 | DRAWICON(CLOSE>OPEN,LOW,1);//当收阳时在最低价位置画1号图标 |

**DRAWBMP**

| 释义 | 在图形上绘制位图。 |
| --- | --- |
| 用法 | DRAWBMP(COND,PRICE,BMPFILE\[,ALIGN\]),当 COND 条件满足时,在 PRICE 位置画 BMPFILE 文件名指定的 BMP 位图，ALIGN(可选参数)对齐方式 ,0 图标中对齐;1 图标上缘;2 图标中缘;3 图中;4图上;5 图下<br>(初始路径与\\DOCUMENT 目录的文档 \*.STK 文件放在一起)。 |
| 示例 | DRAWBMP(CLOSE>OPEN,LOW,'SUN');//表示当收阳时在最低价位置画 SUN.BMP 位图 |

**DRAWNUMBER**

| 释义 | 在图形上显示数字。 |
| --- | --- |
| 用法 | DRAWNUMBER(COND,PRICE,NUMBER,PRECISION\[,COLOR,ALIGN\])当 COND 条件满足时,在 PRICE 位置书写数字 NUMBER,PRECISION 为小数显示位数(取值范围 0-7)，其中 0-6 表示位数，7 表示自动显示位数。<br>COLOR(可选参数)为数字颜色。<br>ALIGN(可选参数)对齐方式 0 中对齐;1 左对齐;2 右对齐;3 图中;4 图上;5 图下。 |
| 示例 | DRAWNUMBER(CLOSE/OPEN>1.08,HIGH,(CLOSE-REF(C,1))/REF(C,1)\*100,2);//表示当日涨幅大于 8%时在最高价位置显示涨幅(相对开盘价的百分比)。 |

**VOLSTICK**

| 释义 | 将数据画成柱状线 |
| --- | --- |
| 示例 | VOL,VOLSTICK;画成交量柱状线 |

**STACKVOLSTICK**

| 释义 | 将数据画成叠加柱状线 |
| --- | --- |
| 示例 | VOL,stackvolstick;画成交量柱状线 |

## [\#](https://dict.thinktrader.net/VBA/system_function.html\#%E5%AD%97%E7%AC%A6%E4%B8%B2%E5%87%BD%E6%95%B0) 字符串函数

**MARKETNAME**

| 释义 | 取得当前品种的市场名称 |
| --- | --- |
| 用法 | MARKETNAME(),将返回当前品种的市场名称 |
| 示例 | MARKETNAME();//主图为沪市标的返回"上证所";主图为深市标的返回"深交所" |

**INBLOCK**

| 释义 | 判断本股票是否板块成员 |
| --- | --- |
| 用法 | INBLOCK(S),若本股票是板块 S 的成员将返回 1，否则返回 0. |
| 示例 | INBLOCK('工业板块');//若本股票属于工业板块则返回 1,不属于返回0 |

**LOWERSTR**

| 释义 | 将字符串转换为小写。 |
| --- | --- |
| 用法 | LOWERSTR(STR),将返回 STR 对应的小写字符串 |
| 示例 | LOWERSTR('EFGH');//将返回"efgh" |

**LTRIM**

| 释义 | 除去字符串开始空格 |
| --- | --- |
| 用法 | LTRIM(STR) |
| 示例 | LTRIM(' SH');//将返回 SH |

**MARKETLABEL**

| 释义 | 取得当前品种的市场代码 |
| --- | --- |
| 用法 | MARKETLABEL(),将返回当前品种的市场代码 |
| 示例 | MARKETLABEL();//沪市标的返回"SH",深市标的返回"SZ" |

**MARKETLABEL1**

| 释义 | 取得当前品种的市场代码。<br>较 MARKETLABEL,MARKETLABEL1 返回值更加精确 |
| --- | --- |
| 用法 | MARKETLABEL1(),将返回当前品种的市场代码 |
| 示例 | MARKETLABEL1();//上证 A 股返回"SHZB",深证主板返回"SZZB",深证中小板返回"SZZX",深圳创业板返回"SZCY",其他板块的返回值与 MARKETLABEL 函数相同。 |

**NUMTOSTR**

| 释义 | 将数字转化为字符串，用户可以设定精度 |
| --- | --- |
| 用法 | NUMTOSTR(N,M),将 N 转化为字符串返回，精确到小数点后 M 位 |
| 示例 | NUMTOSTR(CLOSE,5)；//将返回收盘价对应的字符串，例如"15.78000" |

**RTRIM**

| 释义 | 除去字符串尾部空格 |
| --- | --- |
| 用法 | RTRIM(STR) |
| 示例 | RTRIM('SH ');//将返回 SH |

**STKLABEL**

| 释义 | 取得品种代码 |
| --- | --- |
| 用法 | STKLABEL()将返回当前品种的代码 |
| 示例 | STKLABEL();//例如主图标的为浦发银行将返回600000 |

**STKNAME**

| 释义 | 取得品种名称 |
| --- | --- |
| 用法 | STKNAME(),将返回当前品种的名称 |
| 示例 | STKNAME();//例如主图标的为浦发银行将返回"浦发银行" |

**STRCAT**

| 释义 | 把一个字符串添加到另一个字符串中 |
| --- | --- |
| 用法 | STRCAT(DES,STR),将 STR 字符串添加到 DES 字符串末尾 |
| 示例 | STRCAT('ABC','DEF');//将返回"ABCDEF" |

**STRCMP**

| 释义 | 字符串比较 |
| --- | --- |
| 用法 | STRCMP(STR1,STR2),若 STR1>STR2 则返回 1，STR1<STR2 返回-1，若STR1=STR2 返回 0 |
| 示例 | STRCMP('ABCDEF','ABC');//返回1 表示'ABCDEF'>'ABC' |

**STRFIND**

| 释义 | 在字符串中查找另一个字符串 |
| --- | --- |
| 用法 | STRFIND(STR,S1,N),从字符串 STR 的第 N 个字符开始查找字符串 S1,返回找到的位置，若没有找到就返回 0 |
| 示例 | STRFIND('ABCDEFGH','CDE',1);//将返回 3 |

**STRICMP**

| 释义 | 忽略大小写比较字符串 |
| --- | --- |
| 用法 | STRICMP(STR1,STR2),若 STR1>STR2 则返回 1，STR1<STR2 返回-1，若STR1=STR2返回 0 |
| 示例 | STRCMP('ABCDEF','ABC');//返回 1表示'ABCDEF'>'ABC' |

**STRINGTOFILE**

| 释义 | 输出指定的字符串到一个指定的文件中<br>用户可以在公式中通过输出指定的字符串到文件来实现调试或者其他的目的.借此可以借助这个功能来完成监控公式运行的各种细节参数.该函数用法与DEBUGFILE2基本相同，唯一区别是该函数在写文件时会自动清空之前文件中写入的数据 |
| --- | --- |
| 用法 | STRINGTOFILE(PATH,STR),PATH 为用户的本地计算机路径,STR 为用户指定输出的一个行文字 |
| 示例 | STRINGTOFILE('D:TEST.TXT','当前资产为 1000');//将在公式的监控部分输出到 D:TEST.TXT 文件.<br>如果字符串输出中涉及到数字变量，可以使用 NUMTOSTR 函数转化成字符串后，然后再进行相加 |

**STRINSERT**

| 释义 | 从指定位置插入一个子字符串 |
| --- | --- |
| 用法 | STRINSERT(STR,INDEX,STR1)，在 STR 字符串的第 INDEX 后方开始插入字符串STR1 |
| 示例 | STRINSERT('ABCDEF',2,'ZZZ')；//函数将返回"ABZZZCDEF" |

**STRLEFT**

| 释义 | 取得字符串的左边部分 |
| --- | --- |
| 用法 | STRLEFT(STR,N),返回字符串 STR 的左边 N 个字符 |
| 示例 | STRLEFT('ABCDEF',3);//将返回"ABC" |

**STRLEN**

| 释义 | 求字符串的长度 |
| --- | --- |
| 用法 | STRLEN(STR),将返回 STR 字符串的长度，由于系统采用 ANSI 字符编码，一个汉字等于 2 个字节 |
| 示例 | STRLEN('ABCD');//将返回4 |

**STRMID**

| 释义 | 取得字符串的中间部分 |
| --- | --- |
| 用法 | STRMID(STR,N,M),返回字符串STR 的第N 个字符开始的长度为M 个字符的字符串 |
| 示例 | STRMID('ABCDEF',3,3);//将返回"CDE" |

**STRNCMP**

| 释义 | 指定长度比较字符串 |
| --- | --- |
| 用法 | STRNCMP(STR1,STR2,LEN) |
| 示例 | VAR1:STRNCMP(STKNAME,'ST',2)=0;// 若返回 1 表示该股为 ST 股 |

**STRREMOVE**

| 释义 | 从指定位置开始的地方删除一个或多个字符 |
| --- | --- |
| 用法 | STRREMOVE(STR,INDEX,COUND)，在 STR 字符串的第 INDEX 地方开始删除 COUND个字符 |
| 示例 | STRREMOVE('ABCDEF',2,2)函数将返回"ABEF" |

**STRREPLACE**

| 释义 | 用一个字符替换另一个字符 |
| --- | --- |
| 用法 | STRREPLACE(STR,STROLD,STRNEW),将 STR 字符串中的 STROLD 替换为 STRNEW<br>在替换之后,该字符串有可能增长或缩短；那是因为STRNEW和STROLD的长度不需要是相等的. |
| 示例 | STRREPLACE('ABCDEFG','BCD','ZZZ');//函数将返回"AZZZEFG" |

**STRRIGHT**

| 释义 | 取得字符串的右边部分 |
| --- | --- |
| 用法 | STRRIGHT(STR,N),返回字符串 STR 的右边 N 个字符 |
| 示例 | STRRIGHT('ABCDEF',3)得到"DEF" |

**STRTONUM**

| 释义 | 将字符串转化为数字 |
| --- | --- |
| 用法 | STRTONUM(STR),将 STR 转化为数字返回 |
| 示例 | STRTONUM('12.5');//将返回数值 12.5 |

**STRTRIMLEFT**

| 释义 | 整理字符串左边 |
| --- | --- |
| 用法 | STRTRIMLEFT(STR,STR1),将一群特定的字符 STR1 从字符串 STR 的开始处删除 |
| 示例 | TRTRIMLEFT(' ABC',' ');//函数将返回"ABC" |

**STRTRIMRIGHT**

| 释义 | 整理字符串右边 |
| --- | --- |
| 用法 | STRTRIMLEFT(STR,STR1),将一群特定的字符 STR1 从字符串 STR 的末尾处删除 |
| 示例 | STRTRIMRIGHT('ABC ',' ');//函数将返回"ABC" |

**UPPERSTR**

| 释义 | 将字符串转换为大写 |
| --- | --- |
| 用法 | UPPERSTR(STR),将返回 STR 对应的大写字符串 |
| 示例 | UPPERSTR('abcd');//将返回"ABCD" |

## [\#](https://dict.thinktrader.net/VBA/system_function.html\#%E5%BC%95%E7%94%A8%E5%87%BD%E6%95%B0) 引用函数

**BARSCOUNT**

| 释义 | 求有效周期数 |
| --- | --- |
| 用法 | BARSCOUNT(X)第一个有效数据到当前的天数 |
| 示例 | BARSCOUNT(CLOSE);//取得上市以来总交易日数 |

**BACKSET**

| 释义 | 将满足条件时当前位置到若干周期前的数据设为 1 ，条件不满足返回0 |
| --- | --- |
| 用法 | BACKSET(X,N),若 X 非 0,则将当前位置到 N 周期前的数值设为 1 |
| 示例 | BACKSET(CLOSE>OPEN,2);<br>若收阳则将该周期及前一周期数值设为 1,否则返回 0 |

**BARSLAST**

| 释义 | 上一次条件成立到当前的周期数 |
| --- | --- |
| 用法 | BARSLAST(X):上一次X不为0到现在的天数 |
| 示例 | BARSLAST(CLOSE/REF(CLOSE,1)>=1.1);//表示上一个涨停板到当前的周期数<br>如果没有符合条件的周期，函数将返回0 |

**BARSSINCE**

| 释义 | 第一个条件成立到当前的周期数 |
| --- | --- |
| 用法 | BARSSINCE(X):第一次X不为0到现在的天数 |
| 示例 | BARSSINCE(HIGH>10);//表示股价超过10元时到当前的周期数<br>如果没有符合条件的周期，函数将返回0 |

**BARSSINCEN**

| 释义 | N 个周期内第一个条件成立到当前的周期数 |
| --- | --- |
| 用法 | BARSSINCEN(X,N):N 周期内第一次 X 不为 0 到现在的周期数,N 大于或等于 2 |
| 示例 | BARSSINCEN(HIGH>10,N);//表示 N 个周期内的股价超过 10 元时到当前的周期数如果没有符合条件的周期，函数将返回0 |

**COUNT**

| 释义 | 统计满足条件的周期数 |
| --- | --- |
| 用法 | COUNT(X,N),统计 N 周期中满足 X 条件的周期数,若 N=0 则从第一个有效值开始 |
| 示例 | COUNT(CLOSE>OPEN,20);//表示统计 20 周期内收阳的周期数 |

**CALLSTOCK**

| 释义 | 引用同期的其他证券数据 |
| --- | --- |
| 用法 | CALLSTOCK(CODE,TYPE\[,CYC,N\]),引用指定品种代码为 CODE,周期为 CYC(可选)若不填或者为-1 表示使用当前周期,类型为 TYPE 的数据<br>N 为左右偏移周期个数（可选）0 表示引用当前数据，<0 为引用之前数据，>0为引用之后数据。<br>其中 TYPE 的值可为 VTOPEN(开盘) VTHIGH(最高) VTLOW(最低) VTCLOSE(收盘)<br>VTVOL(成交量) VTAMOUNT(成交额) vtOPENINT(持仓量) VTADVANCE(涨数,大盘有效) VTDECLINE(跌数,大盘有效)以及外部数据和万德数据<br>如果找不到同期数据，那么将返回最近的一个。<br>CYC 范围为 0-19，分别表示<br>0:分笔成交、1:1 分钟、2:5 分钟、3:15 分钟、4:30 分钟、5:60 分钟<br>6:日、7:周、8:月、9:年、10:多日、11:多分钟、12:多秒<br>13:多小时、14:季度线、15:半年线、16:节气线、17:3 分钟、18:10 分钟、19:多笔线 |
| 示例 | CALLSTOCK('SH600000',VTCLOSE,6,-1);//表示引用昨日SH市场的 600000 的日线收盘价<br>CALLSTOCK('SH600000',VTOPEN)表示引用 SH 市场的 600000的日开盘价，使用当前周期<br>引用数据时，需要实现确认被引用品种周期数据齐全，再首次使用或者在不确定时，请手工进行数据补充工作 |

**CURRBARSCOUNT**

| 释义 | 求到最后交易日的周期数 |
| --- | --- |
| 用法 | CURRBARSCOUNT 求到最后交易日的周期数 |

**DMA**

| 释义 | 求动态移动平均 |
| --- | --- |
| 用法 | DMA(X,A),求 X 的动态移动平均。<br>算法: 若 Y=DMA(X,A)<br>则 Y=A\*X+(1-A)\*Y',其中 Y'表示上一周期 Y 值,A 必须小于 1。 |
| 示例 | DMA(CLOSE,VOL/CAPITAL());//表示求以换手率作平滑因子的平均价 |

**DRAWNULL**

| 释义 | 取得一个无效数字，不输出值 |
| --- | --- |
| 示例 | IF(CLOSE>REF(CLOSE,1),CLOSE,DRAWNULL);//当前周期收盘价小于上一周期收盘价时不输出值 |

**EMA**

| 释义 | 求指数平滑移动平均 |
| --- | --- |
| 用法 | EMA(X,N),求 X 的 N 日指数平滑移动平均。算法：若 Y=EMA(X,N)<br>则 Y=\[2\*X+(N-1)\*Y'\]/(N+1),其中 Y'表示上一周期 Y 值 |
| 示例 | EMA(CLOSE,30);//表示求 30 日指数平滑均价 |

**FILTER**

| 释义 | 过滤连续出现的信号 |
| --- | --- |
| 用法 | FILTER(X,N):X 满足条件后，删除其后 N 周期内的数据置为 0 |
| 示例 | FILTER(CLOSE>OPEN,5);//查找阳线，5 天内再次出现的阳线不被记录在内 |

**HHV**

| 释义 | 求最高值 |
| --- | --- |
| 用法 | HHV(X,N),求 N 周期内 X 最高值,N=0 则从第一个有效值开始 |
| 示例 | HHV(HIGH,30);//表示求 30 日最高价 |

**HHvbRS**

| 释义 | 求上一高点到当前的周期数 |
| --- | --- |
| 用法 | HHvbRS(X,N):求 N 周期内 X 最高值到当前周期数，N=0 表示从第一个有效值开始统计 |
| 示例 | HHvbRS(HIGH,0);//求得历史新高到到当前的周期数 |

**HOD**

| 释义 | 求高值名次 |
| --- | --- |
| 用法 | HOD(X,N):求当前 X 数据是 N 周期内的第几个高值,N=0 则从第一个有效值开始 |
| 示例 | HOD(HIGH,20);//返回当前最高价是20个周期内的第几个高价 |

**IMA**

| 释义 | 求指数权重移动平均 |
| --- | --- |
| 用法 | IMA(X,N,S)求 X 的 N 日 S 系数权重的指数移动平均，S 如果小于 100 表示远期权重大于近期权重，大于 100 表示近期的权重大于远期权重，等于 100 就相当于 MA |
| 示例 | IMA(CLOSE,10,120);//表示求收盘价 10 日的 120%指数权重移动平均 |

**LLV**

| 释义 | 求最低值 |
| --- | --- |
| 用法 | LLV(X,N),求 N 周期内 X 最低值,N=0 则从第一个有效值开始 |
| 示例 | LLV(LOW,0);//表示求历史最低价 |

**LLvbRS**

| 释义 | 求上一低点到当前的周期数 |
| --- | --- |
| 用法 | LLvbRS(X,N):求 N 周期内 X 最低值到当前周期数，N=0 表示从第一个有效值开始统计 |
| 示例 | LLvbRS(HIGH,20);//求得 20 个周期内最低点到当前的周期数 |

**LOD**

| 释义 | 求低值名次 |
| --- | --- |
| 用法 | LOD(X,N):求当前 X 数据是 N 周期内的第几个低值,N=0 则从第一个有效值开始 |
| 示例 | LOD(LOW,20);//返回当前最低价是 20 个周期内的第几个低价 |

**MA**

| 释义 | 求简单移动平均 |
| --- | --- |
| 用法 | MA(X,N),求 X 的 N 日移动平均值。算法：(X1+X2+X3+...+XN)/N |
| 示例 | MA(CLOSE,10);//表示求 10 日均价 |

**MEDIAN**

| 释义 | 取若干指定周期的中位数据 |
| --- | --- |
| 用法 | MEDIAN(X,N),取 N 周期 X 的中数，如果 N 是奇数，取排完序的 X 中间一个元素；如果偶数，取中间两个的平均值返回 |
| 示例 | MEDIAN(CLOSE,3);//表示取近3个周期收盘价排序后的中间数 |

**NEWHBARS**

| 释义 | 在历史上所有比当前数值高的数值序列中，离当前第 N 个近的数字到当前的周期数 |
| --- | --- |
| 用法 | NEWHBARS(X,N):求高于当前周期 X 的第 N 个 x 的距离 |
| 示例 | NEWHBARS(HIGH,1);//表示高于当前周期 h 的上一个 h 距离当前的周期数，即，今天的 h，创了多少个周期以来的新高 |

**NEWLBARS**

| 释义 | 在历史上所有比当前数值低的数值序列中，离当前第 N 个近的数字到当前的周期数 |
| --- | --- |
| 用法 | NEWLBARS(X,N):求低于当前周期 X 的第 N 个 x 的距离 |
| 示例 | NEWLBARS(LOW,1);//表示低于当前周期 l 的上一个 l 距离当前的周期数，即，今天的 l，创了多少个周期以来的新低 |

**REF**

| 释义 | 引用若干周期前的数据 |
| --- | --- |
| 用法 | REF(X,A),引用 A 周期前的 X 值 |
| 示例 | REF(CLOSE,1);//表示上一周期的收盘价，在日线上就是昨收 |

**REFDATE**

| 释义 | 引用自 1900 年以来指定日期的数据 |
| --- | --- |
| 用法 | REFDATE(X,DATE\[,TIME\]),引用 DATE 日期 TIME （可省略）的 X 值 |
| 示例 | REFDATE(CLOSE,20011208);//表示 2001 年 12 月 08 日的收盘价;<br>REFDATE(CLOSE,20011208, 133030);//表示 2001 年 12 月 08 日 13:30:30 的收盘价<br>TIME 参数可省略使用，省略时间一般用在日线及其以上周期使用，对于日线以下周期则一般需要带时间参数。<br>注意：对于逐 K 线运行模式，X 值不可以引用到未来数据，但是序列模式则无 |
| 此限 |  |

**REFX**

| 释义 | 引用若干周期后的数据 |
| --- | --- |
| 用法 | REFX(X,A),引用 A 周期后的 X 值 |
| 示例 | REFX(CLOSE,1);//表示后一周期的收盘价，在日线上就是明收 |

**RET**

| 释义 | 按时间引用若干周期前的数据 |
| --- | --- |
| 用法 | RET(X,A),引用 A 周期时间前的 X 值 |
| 示例 | RET(CLOSE,10);//在日线上表示引用 10 天前的收盘价 |

**SFILTER**

| 释义 | 过滤连续出现的信号 |
| --- | --- |
| 用法 | SFILTER(X,COND):X 满足条件后，将其后所有周期内的数据置为 0,直到 COND条件满足为止 |
| 示例 | SFILTER(CLOSE>OPEN,CLOSE<OPEN);//查找阳线，再次出现的阳线不被记录在内,直到出现阴线后再次出现阳线为止 |

**SMA**

| 释义 | 求移动平均 |
| --- | --- |
| 用法 | SMA(X,N,M),求 X 的 N 日移动平均，M 为权重。<br>算法: 若 Y=SMA(X,N,M)<br>则 Y=\[M\*X+(N-M)\*Y')/N,其中 Y'表示上一周期 Y 值,N 必须大于 M |\
| 示例 | SMA(CLOSE,30,1);//表示求 30 日移动平均价 |\
\
**STKINDI**\
\
| 释义 | 引用任意品种任意周期的任意指标输出 |\
| --- | --- |\
| 用法 | STKINDI(STKLABEL,INDINAME,CO,PERIOD\[,m,n\])<br>STKLABEL 指定品种代码，如为空表示当前品种<br>INDINAME 为指标公式调用<br>CO 为坐标轴类型 0 交易日坐标 1 自然日 2 交易交易时间<br>PERIOD 为周期类型，有效值范围为(0-19)，依次表示:<br>0:分笔成交、1:1 分钟、2:5 分钟、3:15 分钟、4:30 分钟、5:60 分钟、6:日、7:周、8:月、9:年、10:多日、11:多分钟、12:多秒、13:多小时、14:季度线、15:半年线、16:节气线、17:3 分钟、18:10 分钟、19:多笔线<br>m 为左右偏移周期个数（可选），0 表示引用当前数据，小于 0 为引用之前数据，大于 0 为引用之后数据<br>n 为 0,1,2,3,4 表示复权数据类型,0 为不复权,1 为前复权,2 为后复权,3 为等比前复权,4 为等比后复权 |\
| 示例 | STKINDI('1A0001','MA.MA1',0,DATAPERIOD);<br>计算 1A0001 的当前周期 MA 指标的 MA1 指标线<br>STKINDI('','RSI.RSI1',0,6);<br>计算当前品种的日线周期 RSI 指标的 RST1 指标线<br>STKINDI('SH600000','RSI',0,6,-1);<br>引用昨日 SH 市场 600000 品种的日线周期 RSI 指标最后—行输出并且使用公式的默认参数<br>若参数为数字变量，那么需要有个字符串转换的过程<br>s:=5;<br>m:=NUMTOSTR(s,0);//NUMTOSTR 函数将数字转换到字符串，再带入变量中<br>vola:stkindi('if10','ATR.ATR',0,6,-1);//计算 IF10 合约的日线周期指标ATR 的 ATR 指标线，传递参数 m 值为 5。<br>第 2 个参数''里的是文本，由三部分组成：'ATR.ATR('和 m 和')',这三部分由两个连接符号&连接起来，实现对数值参数的文本传输。引用数据时，需要实现确认被引用品种周期数据齐全，再首次使用或者在不确定时，请手工进行数据补充工作 |\
\
**SUM**\
\
| 释义 | 求总和 |\
| --- | --- |\
| 用法 | SUM(X,N),统计 N 周期中 X 的总和,N=0 则从第一个有效值开始 |\
| 示例 | SUM(VOL,0);//表示统计从上市第一天以来的成交量总和 |\
\
**SUMBARS**\
\
| 释义 | 向前累加到指定值到现在的周期数 |\
| --- | --- |\
| 用法 | SUMBARS(X,A);将 X 向前累加直到大于等于 A,返回这个区间的周期数 |\
| 示例 | SUMBARS(CLOSE,200);//求收盘价累加大于200所用的周期数 |\
\
**TMA**\
\
| 释义 | 求递归移动平均 |\
| --- | --- |\
| 用法 | TMA(X,N,M),求 X 的递归移动平均，N、M 为权重 |\
| 算法 | 若 Y=TMA(X,N,M) 则 Y=(N _Y'+M_ X), 其中 Y'表示上一周期 Y 值。初值为 M\*X |\
| 示例 | MA(CLOSE,0.9,0.1);//表示求 X 的递归移动平均 |\
\
**TODAYBAR**\
\
| 释义 | 求当日数据周期的数量 |\
| --- | --- |\
| 用法 | TODAYBAR,得到当日从开盘以来到现在的周期数量 |\
| 示例 | TODAYBAR;//若在1分钟周期返回20，表示当日1分钟K线已生成20个周期 |\
\
**TODAYBAR**\
\
| 释义 | 求真实波幅 |\
| --- | --- |\
| 用法 | TR,求真实波幅 |\
| 示例 | ATR:=MA(TR,10);//表示求真实波幅的 10 周期均值 |\
\
**TRMA**\
\
| 释义 | 三角移动平均 |\
| --- | --- |\
| 用法 | TRMA(X,N)求 X 在 N 周期内的三角移动平均<br>三角移动平均计算方法:第1 种可能先算(奇数+1)/2 周期移动平均,得出值再算这个值的(奇数+1)/2 周期的移动平均.<br>第2 种可能先算偶数/2 周期移动平均,得出值再算这个值的(偶数/2+1)周期的移动平均，<br>也就是先判断一下 N 是奇数还是偶数，然后再选对应的计算式 |\
| 示例 | TRMA(CLOSE,10);//求收盘价的 10 周期三角移动平均 |\
\
**WMA**\
\
| 释义 | 求加权移动平均 |\
| --- | --- |\
| 用法 | WMA(X,N),求 X 的加权移动平均 |\
| 算法 | 若 Y=WMA(X,N) 则<br>Y=(N\*X0+(N-1)\*X1+(N-2) _X2)+...+1_ XN)/(N+(N-1)+(N-2)+...+1)<br>X0 表示本周期值，X1 表示上一周期值... |\
| 示例 | WMA(CLOSE,20);//表示求 20 周期加权均价 |\
\
**gettreasury10y**\
\
| 释义 | 取十年期国债无风险利率 |\
| --- | --- |\
| 用法 | gettreasury10y(date,type),取十年期国债无风险利率 |\
| 示例 | gettreasury10y(20170306,1);//取2017年3月5日的中债1年期收益率 |\
\
**getfindata**\
\
| 释义 | 获取财务数据 |\
| --- | --- |\
| 用法 | 表格名称CAPITALSTRUCTURE(股本结构)、PERSHAREINDEX(主要指标)、ASHAREINCOME(利润表)、ASHARECASHFLOW(现金流量表)、ASHAREBALANCESHEET(资产负债表) |\
| 示例 | getfindata('CAPITALSTRUCTURE','total\_capital');//取总股本 |\
\
**getlonghubang**\
\
| 释义 | 获取龙虎榜数据 |\
| --- | --- |\
| 用法 | getlonghubang('字段名','买卖方向',席位排行);<br>字段名:上榜日期,上榜原因,成交金额,交易营业部名称,买入金额,买入金额占总成交比例,卖出金额,卖出金额占总成交比例,净额<br>买卖方向:B或S<br>席位:1-5 |\
| 示例 | getlonghubang('交易营业部名称','B',1);//取买一席位交易营业部名称 |\
\
**gettop10shareholder**\
\
| 释义 | 获取十大股东数据 |\
| --- | --- |\
| 用法 | gettop10shareholder('类型名','字段名',排行);类型名和字段名中文或英文均可<br>类型名:流通股东(flow\_holder),股东(holder)<br>字段名:股东名称(shareholder\_name),股东类型(shareholder\_type),持股数量(shareholder\_quantity),变动原因(change\_reason),持股比例(shareholder\_ratio),股份性质(share\_nature),持股排名(shareholder\_rank)<br>排行:1-10 |\
| 示例 | gettop10shareholder('flow\_holder','shareholder\_name',1);//获取第一大股东的名称 |\
\
## [\#](https://dict.thinktrader.net/VBA/system_function.html\#%E6%8E%A7%E5%88%B6%E5%87%BD%E6%95%B0-1) 控制函数\
\
**AND**\
\
| 释义 | 逻辑与运算 |\
| --- | --- |\
| 用法 | A AND B 或 A && B<br>表示条件 A 与条件 B 同时成立 |\
| 示例 | COND:CLOSE>OPEN AND HOLDING=0 ;//阳线并且持仓为0 |\
\
**BEGIN**\
\
| 释义 | 把多条语句看作一条语句 |\
| --- | --- |\
| 用法 | 语法:BEGIN...END |\
| 示例 | IF CLOSE>OPEN THEN<br>BEGIN<br> AA:=1;<br>END//若该周期收盘，将AA赋值为1 |\
\
**BREAK**\
\
| 释义 | 跳出循环 |\
| --- | --- |\
| 用法 | 语法:BREAK; |\
| 示例 | if ISLASTBAR()=1 then BEGIN<br> For i=1 TO 10 DO BEGIN<br> a:=a+1; <br>if i=5 then BREAK;<br> END<br>END//最后一根k上时，当A=5时，退出循环 |\
\
**DO**\
\
| 释义 | 执行语句 |\
| --- | --- |\
| 用法 | FOR VAR=N1 TO N2 DO EXPR;<br>WHILE COND DO EXPR |\
\
**EXIT**\
\
| 释义 | 终止公式执行.注意:逐 K 线模式下运行时,EXIT 的使用不当会导致比如 HHV,MA,等统计性质的函数计算出现失误,建议这些函数都要放在 EXIT 退出语句的前面保证每个周期都能执行到. |\
| --- | --- |\
| 用法 | 语法:EXIT; |\
| 示例 | if AA:=1 then begin <br> EXIT <br>end//若AA等于1，停止公式运行 |\
\
**FOR**\
\
| 释义 | 循环语句 |\
| --- | --- |\
| 用法 | FOR...TO...DO...<br>FOR...DOWNTO...DO... |\
| 示例 | FOR VAR=N1 TO N2 DO EXPR;<br>从 VAR=N1 开始到 VAR=N2 开始循环执行 EXPR 语句，每执行一次 VAR 加 1<br>FOR VAR=N1 DOWNTO N2 DO EXPR2;<br>从 VAR=N1 开始到 VAR=N2 开始循环执行 EXPR 语句，每执行一次 VAR 减 1<br>注意:逐 K 线计算模式时，请尽量不要在 FOR 循环控制语句中使用 MA,HHV,LLV等带有序列变量的统计类函数,<br>虽然能通过编译,但是会运行不正常,应该把他们放在语句的外面计算 |\
\
**GLOBALVARIABLE**\
\
| 释义 | 申明并初始化超全局变量 |\
| --- | --- |\
| 用法 | GLOBALVARIABLE 除了不支持数组外其他使用均与 VARIABLE 普通全局变量相同，<br>唯一不同之处在与 GLOBALVARIABLE 超全局变量在不会每次从头刷新指标数据时重新被刷新计算，而是始终会记住最后一次被赋予的值。<br>超全局变量一般会在客户停止后台程式化交易或者关闭框架图表后销毁重置。 |\
\
**IF**\
\
| 释义 | 根据条件求不同的值 |\
| --- | --- |\
| 用法 | IF(X,A,B)若 X 不为 0 则返回 A,否则返回 B |\
| 示例 | IF(CLOSE>OPEN,HIGH,LOW);//表示该周期收阳则返回最高值，否则返回最低值 |\
\
**INPUT**\
\
| 释义 | 申明并设置参数 |\
| --- | --- |\
| 用法 | INPUT:PNAME1(DFT,MIN,MAX,STEP),PNAME2(DFT,MIN,MAX,STEP)...;<br>PNAME 表示参数名, DFT 表示缺省值<br>MIN 表示最小值,MAX 表示最大值<br>STEP 表示优化步长,除 DEFAULT 外都可省略 |\
| 示例 | INPUT:N(5), M(10,1,100,2)<br> 表示定义参数 N,缺省值为 5<br>定义参数 M,缺省值为 10,最小值为 1,最大值为 100,优化步长为 2; |\
\
**OR**\
\
| 释义 | 逻辑或运算 |\
| --- | --- |\
| 用法 | A OR B 或 A |\
| 示例 | COND:CLOSE>OPEN OR HOLDING=0 ;//阳线或者持仓为0 |\
\
**REPEAT**\
\
| 释义 | 循环语句 |\
| --- | --- |\
| 用法 | 语法:REPEAT EXPR UNTIL COND<br>循环执行语句 EXPR 直到满足 COND 条件的时候才中止<br>注意:逐 K 线计算模式时，请尽量不要在 REPEAT 条件控制语句中使用MA,HHV,LLV 等带有序列变量的统计类函数,<br>虽然能通过编译,但是会运行不正常,应该把他们放在语句的外面计算 |\
\
**VARIABLE**\
\
| 释义 | 申明并初始化变量；variable 为全局变量申明语句，一般用在逐 K 线计算模式下声明一个全局变量或者在序列模式和逐 K 线模式下声明一个内部数组。序列模式下variable 申明的序列变量会被强制转换为常数，故与普通的常数变量是没有区别的，因此序列模式下的序列变量不要使用 variable 进行申明。 |\
| --- | --- |\
| 语法 | VARIABLE:VARNAME1=INITVALUE1,VARNAME2=INITVALUE2...; |\
| 示例 | VARIABLE:X=1,Y=CLOSE,ARR\[10\]=0,SARR\[5\]='STR';<br>表示定义常数变量 X 并初始化为 1,<br>申明序列变量 Y 并初始化为收盘价,<br>申明含 10 个浮点数的数组并全部初始化为 0<br>申明含 5 个字符串的数组并都初始化为'STR' |\
\
**WHILE**\
\
| 释义 | 循环语句 |\
| --- | --- |\
| 用法 | WHILE COND DO EXPR<br>当满足 COND 条件的时候，循环执行语句 EXPR<br>注意:逐K 线计算模式时，请尽量不要在WHILE 条件控制语句中使用MA,HHV,LLV等带有序列变量的统计类函数,<br>虽然能通过编译,但是会运行不正常,应该把他们放在语句的外面计算 |\
\
## [\#](https://dict.thinktrader.net/VBA/system_function.html\#%E6%8C%87%E6%A0%87%E5%87%BD%E6%95%B0) 指标函数\
\
**SAR**\
\
| 释义 | 抛物转向 |\
| --- | --- |\
| 用法 | SAR(N,S,M),N 为计算周期,S 为步长,M 为步长的极限值 |\
| 示例 | SAR(10,2,20);//表示计算 10 日抛物转向，步长为 2%，极限值为 20% |\
\
**SARTURN**\
\
| 释义 | 抛物转向点 |\
| --- | --- |\
| 用法 | SARTURN(N,S,M);N 为计算周期,S 为步长,M 为极值,若发生向上转向则返回 1，若发生向下转向则返回-1，否则为 0<br>其用法与 SAR 函数相同 |\
\
## [\#](https://dict.thinktrader.net/VBA/system_function.html\#%E7%BB%9F%E8%AE%A1%E5%87%BD%E6%95%B0) 统计函数\
\
**BETA2**\
\
| 释义 | 指定序列的贝塔系数 |\
| --- | --- |\
| 用法 | BETA2(A1,A2,N);求 A1,A2,N 周期的贝塔系数，该系数表明 A1 每变动 1%,则A2 将变动 V% |\
| 示例 | BETA2(INDEXC,CLOSE,5);// 表示大盘收盘价与该品种的 5 周期贝塔系数 |\
\
**COVAR**\
\
| 释义 | 求 2 个序列的协方差 |\
| --- | --- |\
| 用法 | COVAR(X1,X2,N) 为 X1 与 X2 的 N 周期的协方差 |\
| 示例 | COVAR(CLOSE,INDEXC,8);// 表示收盘价与大盘指数之间的 8 周期的协方差 |\
\
**MODE**\
\
| 释义 | 数据集中出现最多的值用法 返回在某一数组或数据 |\
| --- | --- |\
| 用法 | 返回在某一数组或数据区域中出现频率最多的数值。同 MEDIAN 一样，MODE 也是一个位置测量函数。<br>MODE(array,N); array：序列变量或数组； N： 周期范围。<br>如果数据集合中不含有重复的数据，则 MODE 数返回错误值 N/A。 |\
| 示例 | MODE(array,N);//求数据集 array 中 N 周期内出现频率最多的数值 |\
\
**DEVSQ**\
\
| 释义 | 数据偏差平方和 |\
| --- | --- |\
| 用法 | DEVSQ(X,N);//X：数值表达式，N：周期数 |\
\
**PEARSON**\
\
| 释义 | 返回 Pearson（皮尔生）乘积矩相关系数 r，这是一个范围在 -1.0 到 1.0 之间（包括 -1.0 和 1.0 在内）的无量纲指数，反映了两个数据集合之间的线性相关程度。 |\
| --- | --- |\
| 用法 | PEARSON(A,B,N),计算 A,B 序列的 N 周期乘积矩相关系数 |\
| 示例 | PEARSON(L,H,10);//表示最低价与最高价的 10 周期乘积矩相关系数 |\
\
**INTERCEPT**\
\
| 释义 | 利用现有的 x 值与 y 值计算直线与 y 轴的截距。截距为穿过已知的 known\_x's和 known\_y's 数据点的线性回归线与 y 轴的交点。当自变量为 0（零）时，使用INTERCEPT 函数可以决定因变量的值。例如，当所有的数据点都是在室温或更高的温度下取得的，可以用 INTERCEPT 函数预测在 0°C 时金属的电阻 |\
| --- | --- |\
| 用法 | INTERCEPT(Y,X,N),求序列 Y,X 的线性回归线截距,Y 为因变的观察值或数据集合,X 为自变的观察值或数据集合 |\
| 示例 | INTERCEPT(L,H,5);//表示计算最低价和最高价的 5 周期线性回归线截距 |\
\
**KURT**\
\
| 释义 | 返回数据集的峰值。峰值反映与正态分布相比某一分布的尖锐度或平坦度。正峰值表示相对尖锐的分布。负峰值表示相对平坦的分布。 |\
| --- | --- |\
| 用法 | KURT(X,N),计算数据集 X 的 N 周期峰值 |\
| 示例 | KURT(C,10);//表示收盘价的 10 周期峰值 |\
\
**BINOMDIST**\
\
| 释义 | 一元二项式分布的概率值 |\
| --- | --- |\
| 用法 | 返回一元二项式分布的概率值。函数 BINOMDIST 适用于固定次数的独立试验，KURT(C,10),表示收盘价的 10 周期峰值当试验的结果只包含成功或失败二种情况，且当成功的概率在实验期间固定不变。例如，函数 BINOMDIST 可以计算三个婴儿中两个是男孩的概率。<br>BINOMDIST(A,B,P,F),A 为试验成功的次数,B 为独立试验的次数,P 为每次试验中成功的概率,为一逻辑值，用于确定函数的形式。如果 F 为 TRUE，函数BINOMDIST 返回累积分布函数，即至多 A 次成功的概率；如果为 FALSE，返回概率密度函数，即 A 次成功的概率 |\
| 示例 | BINOMDIST(6,10,0.5,FALSE);//10 次试验成功 6 次的概率为(0.205078) |\
\
**EXPONDIST**\
\
| 释义 | 返回指数分布。使用函数 EXPONDIST 可以建立事件之间的时间间隔模型，例如，在计算银行自动提款机支付一次现金所花费的时间时，可通过函数 EXPONDIST 来确定这一过程最长持续一分钟的发生概率 |\
| --- | --- |\
| 用法 | EXPONDIST(A,B,F),A 为函数的数值,B 为参数值,F 为一逻辑值，指定指数函数的形式。如果 F 为 TRUE，函数 EXPONDIST 返回累积分布函数；如果 F 为 FALSE，返回概率密度函数 |\
| 示例 | EXPONDIST(0.2,10,TRUE);//返回累积分布函数<br>EXPONDIST(0.2,10,FALSE);//返回概率密度函数 |\
\
**FISHER**\
\
| 释义 | Fisher 变换 |\
| --- | --- |\
| 用法 | 返回点 x 的 Fisher 变换。该变换生成一个正态分布而非偏斜的函数。使用此函数可以完成相关系数的假设检验。<br>FISHER(x)<br>X 为一个数字，在该点进行变换。<br>说明：<br>如果 x 为非数值型，函数 FISHER 返回错误值 #VALUE!。<br>如果 x ≤ -1 或 x ≥ 1，函数 FISHER 返回错误值 #NUM!。 |\
| 示例 | FISHER(0.75);//在点 0.75 进行 Fisher 变换的函数值(0.972955) |\
\
**FISHERINV**\
\
| 释义 | 反 Fisher 变换 |\
| --- | --- |\
| 用法 | 返回 Fisher 变换的反函数值。使用此变换可以分析数据区域或数组之间的相关性。如果 y = FISHER(x)，则 FISHERINV(y) = x。<br>FISHERINV(y)<br>Y 为一个数值，在该点进行反变换。<br>说明：<br>如果 y 为非数值型，函数 FISHERINV 返回错误值 #VALUE! |\
| 示例 | FISHERINV(0.972955);//在点 0.972955 进行 Fisher 变换的反函数值(0.75) |\
\
**HYPGEOMDIST**\
\
| 释义 | 返回超几何分布。给定样本容量、样本总体容量和样本总体中成功的次数，函数HYPGEOMDIST 返回样本取得给定成功次数的概率。<br>使用函数 HYPGEOMDIST 可以解决有限总体的问题，其中每个观察值或者为成功或者为失败，且给定样本容量的每一个子集有相等的发生概率 |\
| --- | --- |\
| 用法 | HYPGEOMDIST(A,B,K,D),A 样本中成功的次数,B 样本容量,K 样本总体中成功的次数,D 样本总体的容量 |\
\
**FTEST**\
\
| 释义 | F 检验的结果 |\
| --- | --- |\
| 用法 | 返回 F 检验的结果。F 检验返回的是当数组 1 和数组 2 的方差无明显差异时的单尾概率。可以使用此函数来判断两个样本的方差是否不同。例如，给定公立和私立学校的测试成绩，可以检验各学校间测试成绩的差别程度。 |\
| 语法 | FTEST(array1,array2,N)<br>Array1 第一个数组或数据区域。<br>Array2 第二个数组或数据区域。<br>N 数组数据周期数量 |\
| 示例 | FTEST(C,H,10);//返回收盘价和最高价 10 周期内的检验结果 |\
\
**LARGE**\
\
| 释义 | 数据集中第 k 个最大值 |\
| --- | --- |\
| 用法 | 返回数据集中第 K 个最大值。使用此函数可以根据相对标准来选择数值。例如，可以使用函数 LARGE 得到第一名、第二名或第三名的得分 |\
| 语法 | LARGE(ARRAY,N,K)<br>ARRAY 为需要从中选择第 K 个最大值的数组或数据区域。<br>N 为数组的计算数据周期数量<br>K 为返回值在数组或数据单元格区域中的位置（从大到小排）。<br>说明<br>如果数组为空，函数 LARGE 返回错误值 #NUM!。<br>如果 K ≤ 0 或 K 大于数据点的个数，函数 LARGE 返回错误值 #NUM!。如<br>果区域中数据点的个数为 N，则函数 LARGE(ARRAY,1) 返回最大值，函数LARGE(ARRAY,N) 返回最小值。 |\
| 示例 | LARGE(C,10,3);//求收盘价的 10 周期内的第三个最大值 |\
\
**FORCAST**\
\
| 释义 | 线性回归预测值 |\
| --- | --- |\
| 用法 | FORCAST(X,N)为 X 的 N 周期线性回归预测值 |\
| 示例 | FORCAST(CLOSE,10);//表示求 10 周期线性回归预测本周期收盘价 |\
\
**DRL**\
\
| 释义 | 计算回归偏离度 |\
| --- | --- |\
| 用法 | DRL(X,N);得到 X 的 N 周期回归偏离度 |\
| 示例 | DRL(C,10);//表示求收盘价的 10 周期回归偏离度 |\
\
**FORCAST2**\
\
| 释义 | 曲线回归预测值 |\
| --- | --- |\
| 用法 | FORCAST2(X,N)为 X 的 N 周期曲线(方程：y=a _x_ x+b\*x+c)回归预测值 |\
| 示例 | FORCAST2(CLOSE,10);//表示求 10 周期收盘价曲线回归预测本周期的值 |\
\
**DRL2**\
\
| 释义 | 曲线回归偏离度 |\
| --- | --- |\
| 用法 | DRL2(X,N),得到X的N周期曲线回归偏离度 |\
| 示例 | DRL2(C,10);//表示求收盘价的 10 周期曲线回归偏离度的值（%） |\
\
**NOLOT**\
\
| 释义 | 交易家数取指定市场分类中、有效交易家数 |\
| --- | --- |\
| 用法 | NOLOT(MARKET,D)MARKET为市场名称,D为分类序号（1指数;2A股;3B股;4基金;5债券;6权证） |\
| 示例 | NOLOT('SH',2);//表示取上海 A 股的总交易家数<br>使用该函数前，如果是在开盘以后才接入，那么请补充沪深股市分笔成交以后，刷新扩展统计数据 |\
\
**PERCENTILE**\
\
| 释义 | 返回区域中数值的第 K 个百分点的值。可以使用此函数来建立接受阈值。例如，可以确定得分排名在第 90 个百分点之上的检测侯选人。 |\
| --- | --- |\
| 用法 | PERCENTILE(ARRAY,N,K)<br>ARRAY 为定义相对位置的数组或数据区域。N 数组的数据周期数据量<br>K 0 到 1 之间的百分点值，包含 0 和 1。<br>说明<br>如果 ARRAY 为空或其数据点超过 8,191 个，函数 PERCENTILE 返回错误值#NUM!。<br>如果 K 为非数字型，函数 PERCENTILE 返回错误值 #VALUE!。<br>如果 K < 0 或 K > 1，函数 PERCENTILE 返回错误值 #NUM!。<br>如果 K 不是 1/(N-1) 的倍数，函数 PERCENTILE 使用插值法来确定第 K 个百分点的值。 |\
| 示例 | 若 ARRAY={1,3,2,4},N=4,K=0.3,上面列表中的数据在第 30 个百分点的值(1.9) |\
\
**PERCENTRANK**\
\
| 释义 | 返回特定数值在一个数据集中的百分比排位。此函数可用于查看特定数据在数据集中所处的位置。例如，可以使用函数 PERCENTRANK 计算某个特定的能力测试得分在所有的能力测试得分中的位置。 |\
| --- | --- |\
| 用法 | PERCENTRANK(ARRAY,N,X,SIGNIFICANCE)<br>ARRAY 为定义相对位置的数组或数字区域。N 为数组的周期数量大小；X 为数组中需要得到其排位的值，SIGNIFICANCE 表示返回的百分数值的有效位数。说明 ：如果数组为空，函数 PERCENTRANK 返回错误值 #NUM!。 如果 SIGNIFICANE <<br>1，函数 PERCENTRANK 返回错误值 #NUM!。如果数组里没有与 X 相匹配的值，函数PERCENTRANK 将进行插值以返回正确的百分比排位。 |\
| 示例 | ARRAY={13,12,11,8,4,3,2,1,1,1},N=10,X=2,SIGNIFICANCE=3,2 在上面数据列表中的百分比排位（0.333)，因为该数据集中小于 2 的值有 3 个，而大于 2 的值有 6 个，因此为 3/(3+6)=0.333） |\
\
**PERMUT**\
\
| 释义 | 返回从给定数目的对象集合中选取的若干对象的排列数。排列为有内部顺序的对象或事件的任意集合或子集。排列与组合不同，组合的内部顺序无意义。此函数可用于彩票抽奖的概率计算 |\
| --- | --- |\
| 用法 | PERMUT(A,B),A 表示对象个数的整数,B 表示每个排列中对象个数的整数 |\
| 示例 | PERMUT(100,3);//在上述条件下所有可能的排列数量(970200) |\
\
**POISSON**\
\
| 释义 | 返回泊松分布。泊松分布通常用于预测一段时间内事件发生的次数，比如一分钟内通过收费站的轿车的数量。 |\
| --- | --- |\
| 用法 | POISSON(X,B,F),X 事件数,B 期望值,F 为一逻辑值，确定所返回的概率分布形式。如果 F 为 TRUE，函数 POISSON 返回泊松累积分布概率，即，随机事件发生的次数在 0 到 x 之间（包含 0 和 1）；如果为 FALSE，则返回泊松概率密度函数，即，随机事件发生的次数恰好为 x。 |\
| 示例 | POISSON(0.2,10,TRUE);//返回泊松累积分布概率<br>POISSON(0.2,10,FALSE);//返回泊松概率密度函数 |\
\
**QUARTILE**\
\
| 释义 | 返回数据集的四分位数。四分位数通常用于在销售额和测量数据中对总体进行分组。例如，可以使用函数 QUARTILE 求得总体中前 25% 的收入值 |\
| --- | --- |\
| 用法 | QUARTILE(ARRAY,N,QUART)<br>ARRAY 为需要求得四分位数值的数组或数字型单元格区域。<br>N 为数组数据周期数量<br>QUART 决定返回哪一个四分位值。<br>说明<br>如果数组为空，函数 QUARTILE 返回错误值 #NUM!。<br>如果 QUART 不为整数，将被截尾取整。<br>如果 QUART < 0 或 QUART > 4，函数 QUARTILE 返回错误值 #NUM!。<br>当 QUART 分别等于 0、2 和 4 时，函数 MIN、MEDIAN 和 MAX 返回的值与函数 QUARTILE 返回的值相同。 |\
| 示例 | ARRAY={1,2,4,7,8,9,10,12},N=8,QUART=1,上述数据的第一个四分位数（第 25个百分点值）(3.5) |\
\
**FORCAST2**\
\
| 释义 | 曲线回归预测值 |\
| --- | --- |\
| 用法 | FORCAST2(X,N)为 X 的 N 周期曲线(方程：y=a _x_ x+b\*x+c)回归预测值 |\
| 示例 | FORCAST2(CLOSE,10);//表示求 10 周期收盘价曲线回归预测本周期的值 |\
\
**RSQ**\
\
| 释义 | 返回根据 known\_y's 和 known\_x's 中数据点计算得出的 Pearson 乘积矩相关系数的平方。有关详细信息，请参阅函数 REARSON。R 平方值可以解释为 y 方差与 x 方差的比例 |\
| --- | --- |\
| 用法 | RSQ(A,B,N),计算 A,B 序列的 N 周期乘积矩相关系数的平方 |\
| 示例 | RSQ(L,H,10);//表示最低价与最高价的 10 周期乘积矩相关系数的平方 |\
\
**SKEW**\
\
| 释义 | 返回分布的偏斜度。偏斜度反映以平均值为中心的分布的不对称程度。正偏斜度表示不对称部分的分布更趋向正值。负偏斜度表示不对称部分的分布更趋向负值。 |\
| --- | --- |\
| 用法 | SKEW(X,N),表示求X在N个周期的分布的偏斜度 |\
| 示例 | SKEW(close,10);//求收盘价在10个周期的分布的偏斜度 |\
\
**SLOPE**\
\
| 释义 | 线性回归斜率 |\
| --- | --- |\
| 用法 | SLOPE(X,N)为 X 的 N 周期线性回归线的斜率 |\
| 示例 | SLOPE(CLOSE,10);//表示求 10 周期线性回归线的斜率 |\
\
**SMALL**\
\
| 释义 | 返回数据集中第 K 个最小值。使用此函数可以返回数据集中特定位置上的数值。 |\
| --- | --- |\
| 用法 | SMALL(ARRAY,N,K)<br>ARRAY 为需要找到第 K 个最小值的数组或数字型数据区域。<br>N 为数组周期数量。<br>K 为返回的数据在数组或数据区域里的位置（从小到大）。<br>说明<br>如果 ARRAY 为空，函数 SMALL 返回错误值 #NUM!。<br>如果 K ≤ 0 或 K 超过了数据点个数，函数 SMALL 返回错误值 #NUM!。<br>如果 N 为数组中的数据点个数，则 SMALL(ARRAY,1) 等于最小值，SMALL(ARRAY,N) 等于最大值。 |\
| 示例 | SMALL(C,10,2);//返回收盘价的 10 周期内第 2 个最小值 |\
\
**STANDARDIZE**\
\
| 释义 | 返回正态化数值 |\
| --- | --- |\
| 用法 | STANDARDIZE(A,B,S),A 为需要进行正态化的数值,B 分布的算术平均值,S 为分布的标准偏差,返回以 B 为平均值，以 S 为标准偏差的分布的正态化数值. |\
| 示例 | STANDARDIZE(42,40,1.5);//符合上述条件的 42 的正态化数值(1.333333) |\
\
**STD**\
\
| 释义 | 估算标准差 |\
| --- | --- |\
| 用法 | STD(X,N)为 X 的 N 日估算标准差 |\
| 示例 | STD(close,10);//为收盘价的10日估算标准差 |\
\
**STDP**\
\
| 释义 | 总体标准差 |\
| --- | --- |\
| 用法 | STDP(X,N)为 X 的 N 日总体标准差 |\
| 示例 | STDP(close,10);//计算close 的 10 日总体标准差 |\
\
**STEYX**\
\
| 释义 | 返回通过线性回归法计算每个 x 的 y 预测值时所产生的标准误差。标准误差用来度量根据单个 x 变量计算出的 y 预测值的误差量。 |\
| --- | --- |\
| 用法 | STEYX(Y,X,N),计算 Y,X 序列变量的线性回归法预测标准误差. |\
| 示例 | STEYX(L,H,10);//表示最低价与最高价的 10 周期线性回归法预测标准误差 |\
\
**TRIMMEAN**\
\
| 释义 | 返回数据集的内部平均值。函数 TRIMMEAN 先从数据集的头部和尾部除去一定百分比的数据点，然后再求平均值。当希望在分析中剔除一部分数据的计算时，可以使用此函数。 |\
| --- | --- |\
| 用法 | TRIMMEAN(ARRAY,N,PERCENT)<br>ARRAY 为需要进行整理并求平均值的数组或数值区域。<br>N 为数组数据周期数量<br>PERCENT 为计算时所要除去的数据点的比例，例如，如果 PERCENT = 0.2，在 20 个数据点的集合中，就要除去 4 个数据点(20 X 0.2)：头部除去 2 个，尾部除去 2 个。<br>说明<br>如果 PERCENT < 0 或 PERCENT > 1，函数 TRIMMEAN 返回错误值 #NUM!。<br>函数 TRIMMEAN 将除去的数据点数目向下舍入为最接近的 2 的倍数。如果PERCENT = 0.1，30 个数据点的 10% 等于 3 个数据点。函数 TRIMMEAN 将对称地在数据集的头部和尾部各除去一个数据。 |\
| 示例 | TRIMMEAN(C,20,0.2);//从计算中除去 20%的 20 周期收盘价内部平均值 |\
\
**VAR**\
\
| 释义 | 估算样本方差 |\
| --- | --- |\
| 用法 | VAR(X,N)为 X 的 N 日估算样本方差. |\
| 示例 | VAR(close,10);//计算收盘价的10日估算样本方差 |\
\
**VARP**\
\
| 释义 | 总体样本方差 |\
| --- | --- |\
| 用法 | VARP(X,N)为 X 的 N 日总体样本方差 |\
| 示例 | VARP(close,10);//计算收盘价的10日总体样本方差 |\
\
**WEIBULL**\
\
| 释义 | 返回韦伯分布。使用此函数可以进行可靠性分析，比如计算设备的平均故障时间 |\
| --- | --- |\
| 用法 | WEIBULL(A,B,D,F),A 为参数值,B 为分布参数,D 为分布参数,F 为指明函数的形式,TRUE 为韦伯累积分布,FALSE 为韦伯概率密度 |\
\
**AVEDEV**\
\
| 释义 | 平均绝对偏差 |\
| --- | --- |\
| 用法 | AVEDEV(X,N) |\
| 示例 | AVEDEV(close,10);//计算收盘价的 10 日平均绝对偏差 |\
\
**DELIVERYINTERVAL**\
\
| 释义 | 取当前品种在当前 K 线上，距离最近交割日的交易日数 |\
| --- | --- |\
| 用法 | DELIVERYINTERVAL() |\
| 示例 | DELIVERYINTERVAL();//主图为股指期货，返回5表示距离交割日还有5个交易日 |\
\
## [\#](https://dict.thinktrader.net/VBA/system_function.html\#%E6%97%B6%E9%97%B4%E5%87%BD%E6%95%B0) 时间函数\
\
**BARSTATUS**\
\
| 释义 | 函数返回数据位置状态 |\
| --- | --- |\
| 用法 | 1 表示第 1 根 K 线，2 表示最后 1 根 K 线，0 表示中间的 K 线 |\
| 示例 | BARSTATUS(); |\
\
**BARPOS**\
\
| 释义 | 第一个有效数据到当前的周期数 |\
| --- | --- |\
| 用法 | BARPOS（X）;x:变量名 |\
| 示例 | BARSCOUNT(MA（C，60）);//返回第一个MA60有效值到当前的周期数。(即k线图第60根k线位置开始) |\
\
**CURRENTDATE**\
\
| 释义 | 计算时的当前日期 |\
| --- | --- |\
| 用法 | CURRENTDATE 函数返回计算时的日期,有效值范围为(101-1991231),表示1900/01/01-2099/12/31<br>注意：该函数返回常数 |\
| 示例 | CURRENTDATE;//返回当前日期 |\
\
**CURRENTTIME**\
\
| 释义 | 计算当前计算机时间 |\
| --- | --- |\
| 用法 | CURRENTTIME<br>函数返回计算时的时间(时分秒),有效值范围为(000000-235959)<br>注意：该函数返回常数 |\
| 示例 | CURRENTTIME;//返回当前时间 |\
\
**DATE**\
\
| 释义 | 取得该周期年月日的数值 |\
| --- | --- |\
| 用法 | DATE()<br>函数返回有效值范围为(19000101-20991231),表示 19000101-20991231 |\
| 示例 | DATE();//返回当前周期年月日 |\
\
**DATEDIFF**\
\
| 释义 | 取得日期之间的时间间隔天数 |\
| --- | --- |\
| 用法 | DATEDIFF(DATE1,DATE2)<br>DATE1,DATE2 为序列变量或常数，格式与 DATE 同，有效值范围为(101-1991231),<br>表示 19000101-20991231 返回 DATE1、DATE2 两个日期之间的相差的天数，如果 DATE1 晚于 DATE2，则 DATEDIFF 函数返回负数 |\
| 示例 | DATEDIFF(LSOLARTERMDATE(1),DATE);//表示求当年小寒到当前周期相差的天数 |\
\
**DATEPOS**\
\
| 释义 | 取指定日期的数据序号 |\
| --- | --- |\
| 用法 | DATEPOS(DATE),DATE 日期格式为一字符串格式，比如是标准日期时间格式，并只能是常数 |\
| 示例 | DATEPOS('2012-04-17 10:38:00');//求 2012-04-17 10:38:00 日期时间的数据对应图表上数据的序号 |\
\
**DAY**\
\
| 释义 | 取得该周期的日期 |\
| --- | --- |\
| 用法 | DAY()<br>函数返回有效值范围为(1-31) |\
| 示例 | DATE();//返回10表示该周期为10号 |\
\
**DAYOFWEEK**\
\
| 释义 | 取得指定日期的星期数 |\
| --- | --- |\
| 用法 | DAYOFWEEK(D)，D 为指定的日期<br>函数返回有效值范围为(0-6) |\
| 示例 | DAYOFWEEK(DATE);//表示当前周期是星期几 |\
\
**DAYS1970**\
\
| 释义 | 取得该周期从 1970 以来的天数。<br>返回自从 1970 年 1 月 1 日以来的天数，例如在 1971 年 1 月 1 日返回 365。 |\
| --- | --- |\
| 用法 | DAYS1970 |\
| 示例 | aa:DAYS1970;//当前周期位置距离1970年的天数 |\
\
**HOUR**\
\
| 释义 | 取得该周期的小时数。 |\
| --- | --- |\
| 用法 | HOUR()<br>函数返回有效值范围为(0-23)，对于日线及更长周期此函数无效 |\
| 示例 | HOUR();//返回15表示该周期在下午3点 |\
\
**MINUTE**\
\
| 释义 | 取得该周期的分钟数 |\
| --- | --- |\
| 用法 | MINUTE()<br>函数返回有效值范围为(0-59)，对于日线及更长周期此函数无效 |\
| 示例 | MINUTE();//返回15表示该周期在15分钟 |\
\
**MONTH**\
\
| 释义 | 取得该周期的月份 |\
| --- | --- |\
| 用法 | MONTH()<br>函数返回有效值范围为(1-12) |\
| 示例 | MONTH();//返回8表示该周期在8月份 |\
\
**OPENMINUTES**\
\
| 释义 | 取得该周期的月份 |\
| --- | --- |\
| 用法 | OPENMINUTES(TIME)<br>返回已开盘分钟数(0-总开盘分钟数),开盘前的都为 1,收盘后都为总开盘分钟数 |\
| 示例 | OPENMINUTES(CURRENTTIME);//在日线上可得到现在已开盘分钟数<br>OPENMINUTES(TIME);//在分钟线上可得到当时已开盘分钟数 |\
\
**T0TOTIME**\
\
| 释义 | 取得从 0 点开始 X 秒后的时间值 |\
| --- | --- |\
| 用法 | T0TOTIME(X) |\
\
**TIME**\
\
| 释义 | 取得该周期的时分秒 |\
| --- | --- |\
| 用法 | TIME()<br>函数返回有效值范围为(000000-235959)，对于日线及更长周期此函数无效.<br>该函数返回序列变量 |\
| 示例 | TIME();//返回145000表示该周期在14点50分 |\
\
**TIME0**\
\
| 释义 | 取得该周期从当日 0 点以来的秒数。<br>返回自从当日 0 点以来的秒数，对于日线以上的分析周期返回 0 |\
| --- | --- |\
| 用法 | TIME0 |\
| 示例 | TIME0;//返回2000表示该周期在0点以来有2000秒 |\
\
**TIMETOT0**\
\
| 释义 | 取得时间 X 距离当日 0 点的秒数 |\
| --- | --- |\
| 用法 | TIMETOT0(X);X需指定时间 |\
| 示例 | TIMETOT0(140000);//返回14点距离当日0点以来的秒数 |\
\
**WEEKDAY**\
\
| 释义 | 取得该周期的星期数 |\
| --- | --- |\
| 用法 | WEEKDAY()<br>函数返回有效值范围为(0-6) |\
| 示例 | WEEKDAY();//返回2表示该周期为星期二 |\
\
**YEAR**\
\
| 释义 | 取得该周期的年份 |\
| --- | --- |\
| 用法 | YEAR()<br>函数返回有效值范围为(1900-2099) |\
| 示例 | YEAR();//返回2022表示该周期为2022年 |\
\
## [\#](https://dict.thinktrader.net/VBA/system_function.html\#%E6%95%B0%E5%AD%A6%E5%87%BD%E6%95%B0) 数学函数\
\
**ABS**\
\
| 释义 | 求绝对值 |\
| --- | --- |\
| 用法 | ABS(X)返回 X 的绝对值 |\
| 示例 | ABS(-34)返回 34 |\
\
**ACOS**\
\
| 释义 | 反余弦值 |\
| --- | --- |\
| 用法 | ACOS(X)返回 X 的反余弦值 |\
| 示例 | Acos(-1);//返回3.1415926 |\
\
**ASIN**\
\
| 释义 | 反正弦值 |\
| --- | --- |\
| 用法 | ASIN(X)返回 X 的反正弦值 |\
| 示例 | ASIN(1);//求1的反正弦值 |\
\
**ATAN**\
\
| 释义 | 反正切值 |\
| --- | --- |\
| 用法 | ATAN(X)返回 X 的反正切值 |\
| 示例 | ATAN(15.5);//求15.5的反正切值 |\
\
**CEILING**\
\
| 释义 | 向数值增大方向舍入 |\
| --- | --- |\
| 用法 | CEILING(A)返回沿 A 数值增大方向最接近的整数 |\
| 示例 | CEILING(12.3);//求得 13<br>CEILING(-3.5);//求得-3 |\
\
**COMBIN**\
\
| 释义 | 计算从给定数目的对象集合中提取若干对象的组合数。利用函数 COMBIN 可以确定一组对象所有可能的组合数。 |\
| --- | --- |\
| 用法 | COMBIN(A,B),A 为对象的总数量,B 为每一组合中对象的数量 |\
| 示例 | COMBIN(8,2);//从八个候选人中提取两个候选人的组合数(28) |\
\
**COS**\
\
| 释义 | 余弦值 |\
| --- | --- |\
| 用法 | COS(X)返回 X 的余弦值 |\
| 示例 | COS(-1.87);//返回0.294759 |\
\
**EXP**\
\
| 释义 | 指数 |\
| --- | --- |\
| 用法 | EXP(X)为 E 的 X 次幂 |\
| 示例 | EXP(CLOSE);//返回 E 的 CLOSE 次幂 |\
\
**FLOOR**\
\
| 释义 | 向数值减小方向舍入 |\
| --- | --- |\
| 用法 | FLOOR(A)返回沿 A 数值减小方向最接近的整数 |\
| 示例 | FLOOR(12.3);//求得 12,FLOOR(-3.5)求得-4 |\
\
**FRACPART**\
\
| 释义 | 取得数据的小数部分 |\
| --- | --- |\
| 用法 | FRACPART(X)返回数值的小数部分 |\
| 示例 | FRACPART(12.3);//求得 0.3,FRACPART(-3.5)求得-0.5 |\
\
**INTPART**\
\
| 释义 | 绝对值减小取整，即取得数据的整数部分 |\
| --- | --- |\
| 用法 | INTPART(A)返回沿 A 绝对值减小方向最接近的整数 |\
| 示例 | INTPART(12.3);//求得 12,INTPART(-3.5)求得-3 |\
\
**LN**\
\
| 释义 | 求自然对数 |\
| --- | --- |\
| 用法 | LN(X)以 E 为底的对数 |\
| 示例 | LN(CLOSE);//求收盘价的对数 |\
\
**LOG**\
\
| 释义 | 求以 10 为底的对数 |\
| --- | --- |\
| 用法 | LOG(X)取得 X 的对数 |\
| 示例 | LOG(100);//等于 2 |\
\
**MAX**\
\
| 释义 | 求最大值 |\
| --- | --- |\
| 用法 | MAX(A,B)返回 A 和 B 中的较大值 |\
| 示例 | MAX(CLOSE-OPEN,0);//表示若收盘价大于开盘价返回它们的差值，否则返回 0 |\
\
**MIN**\
\
| 释义 | 求最小值 |\
| --- | --- |\
| 用法 | MIN(A,B)返回 A 和 B 中的较小值 |\
| 示例 | MIN(CLOSE,OPEN);//返回开盘价和收盘价中的较小值 |\
\
**MOD**\
\
| 释义 | 求模运算 |\
| --- | --- |\
| 用法 | MOD(A,B)返回 A 对 B 求模 |\
| 示例 | MOD(26,10);//返回 6 |\
\
**POW**\
\
| 释义 | 乘幂 |\
| --- | --- |\
| 用法 | POW(A,B)返回 A 的 B 次幂 |\
| 示例 | POW(CLOSE,3);//求得收盘价的 3 次方 |\
\
**RAND**\
\
| 释义 | 随机整数 |\
| --- | --- |\
| 用法 | RAND(N)<br>返回一个范围在 1-N 的随机整数 |\
| 示例 | CLOSE\*(RAND(10)/10+0.4);<br>输出收盘价乘以\[0.5-1.4\]的随机系数 |\
\
**REVERSE**\
\
| 释义 | 求相反数 |\
| --- | --- |\
| 用法 | REVERSE(X)返回-X |\
| 示例 | REVERSE(CLOSE);//返回-CLOSE |\
\
**ROUND**\
\
| 释义 | 四舍五入为整数,显示时不带小数 |\
| --- | --- |\
| 用法 | ROUND(X)将 X 四舍五入为整数 |\
| 示例 | ROUND(3.3);//求得 3<br>ROUND(3.5);//求得 4<br>ROUND(-3.5);//求得-4 |\
\
**ROUNDS**\
\
| 释义 | 四舍五入整理小数到指定位数 |\
| --- | --- |\
| 用法 | ROUNDS(A,B) 表示整理数字 A 的小数点位数到 B。该函数可用以做浮点数的精确相等判断 |\
| 示例 | ROUNDS(12.345,2);//将返回 12.35 |\
\
**SGN**\
\
| 释义 | 求符号值 |\
| --- | --- |\
| 用法 | SGN(X)，当 X>0,X=0,X<0 分别返回 1,0,-1 |\
\
**SIN**\
\
| 释义 | 正弦值 |\
| --- | --- |\
| 用法 | SIN(X)返回 X 的正弦值 |\
\
**SQRT**\
\
| 释义 | 开平方 |\
| --- | --- |\
| 用法 | SQRT(X)为 X 的平方根 |\
| 示例 | SQRT(CLOSE);//收盘价的平方根 |\
\
**TAN**\
\
| 释义 | 正切值 |\
| --- | --- |\
| 用法 | TAN(X)返回 X 的正切值 |\
\
## [\#](https://dict.thinktrader.net/VBA/system_function.html\#%E8%A1%8C%E6%83%85%E5%87%BD%E6%95%B0) 行情函数\
\
**CLOSE**\
\
| 释义 | 取得该周期收盘价 |\
| --- | --- |\
| 用法 | CLOSE |\
\
**C**\
\
| 释义 | 取得该周期收盘价，与 CLOSE 等价 |\
| --- | --- |\
| 用法 | c |\
\
**HIGH**\
\
| 释义 | 取得该周期最高价 |\
| --- | --- |\
| 用法 | HIGH |\
\
**H**\
\
| 释义 | 取得该周期最高价，与 HIGH 等价 |\
| --- | --- |\
| 用法 | H |\
\
**LOW**\
\
| 释义 | 取得该周期最低价 |\
| --- | --- |\
| 用法 | LOW |\
\
**L**\
\
| 释义 | 取得该周期最低价 |\
| --- | --- |\
| 用法 | LOW |\
\
**AMOUNT**\
\
| 释义 | 取得该周期原始成交额 |\
| --- | --- |\
| 用法 | AMOUNT |\
| 示例 | AMOUNT;//返回当前k线位置的成交额 |\
\
**OAMOUNT**\
\
| 释义 | 取得该周期原始成交额(未复权) |\
| --- | --- |\
| 用法 | OAMOUNT |\
\
**OCLOSE**\
\
| 释义 | 取得该周期原始收盘价(未复权) |\
| --- | --- |\
| 用法 | OCLOSE |\
\
**OHIGH**\
\
| 释义 | 取得该周期原始最高价(未复权) |\
| --- | --- |\
| 用法 | OHIGH |\
\
**OLOW**\
\
| 释义 | 取得该周期原始最低价(未复权) |\
| --- | --- |\
| 用法 | OLOW |\
\
**OOPEN**\
\
| 释义 | 取得该周期原始开盘价(未复权) |\
| --- | --- |\
| 用法 | OOPEN |\
\
**OPEN**\
\
| 释义 | 取得该周期开盘价 |\
| --- | --- |\
| 用法 | OPEN |\
\
**O**\
\
| 释义 | 取得该周期开盘价，与开盘价等价 |\
| --- | --- |\
| 用法 | O |\
\
**OVOL**\
\
| 释义 | 取得该周期原始成交量(未复权) |\
| --- | --- |\
| 用法 | OVOL |\
\
**VOL**\
\
| 释义 | 取得该周期成交量 |\
| --- | --- |\
| 用法 | VOL |\
\
**V**\
\
| 释义 | 取得该周期成交量，与 VOL 等价。 |\
| --- | --- |\
| 用法 | V |\
\
**OPENA**\
\
| 释义 | 取得该周期开盘成交额 |\
| --- | --- |\
| 用法 | OPENA |\
\
**OPENV**\
\
| 释义 | 取得该周期开盘成交量 |\
| --- | --- |\
| 用法 | OPENV |\
\
**OPENINT**\
\
| 释义 | 取得该周期持仓量 |\
| --- | --- |\
| 用法 | OPENINT |\
\
**ASKPRICE**\
\
| 释义 | 取得该周期的委卖价 |\
| --- | --- |\
| 用法 | ASKPRICE,个股品种在分笔周期上有效 |\
| 示例 | MA(ASKPRICE,10);//获取10笔周期范围的平均委卖价 |\
\
**BIDPRICE**\
\
| 释义 | 取得该周期的委买价 |\
| --- | --- |\
| 用法 | BIDPRICE,个股品种在分笔周期上有效 |\
| 示例 | MA(BIDPRICE,10);//获取10笔周期范围的平均委买价 |\
\
**ASKVOL**\
\
| 释义 | 取得该周期的委卖量 |\
| --- | --- |\
| 用法 | ASKVOL,个股品种在分笔周期上有效 |\
| 示例 | MA(ASKVOL,10);//获取10笔周期范围的平均委卖量 |\
\
**BIDVOL**\
\
| 释义 | 取得该周期的委买量 |\
| --- | --- |\
| 用法 | BIDVOL,个股品种在分笔周期上有效 |\
| 示例 | MA(BIDVOL,10);//获取10笔周期范围的平均委买量 |\
\
**INDEXA**\
\
| 释义 | 取得同期大盘的成交额 |\
| --- | --- |\
| 用法 | INDEXA |\
\
**INDEXC**\
\
| 释义 | 取得同期大盘的收盘价 |\
| --- | --- |\
| 用法 | INDEXC |\
\
**INDEXH**\
\
| 释义 | 取得同期大盘的最高价 |\
| --- | --- |\
| 用法 | INDEXH |\
\
**INDEXL**\
\
| 释义 | 取得同期大盘的最低价 |\
| --- | --- |\
| 用法 | INDEXL |\
\
**INDEXO**\
\
| 释义 | 取得同期大盘的开盘价 |\
| --- | --- |\
| 用法 | INDEXO |\
\
**INDEXV**\
\
| 释义 | 取得同期大盘的成交量 |\
| --- | --- |\
| 用法 | INDEXV |\
\
**IOPV**\
\
| 释义 | 取得ETF的基金份额参考净值 |\
| --- | --- |\
| 用法 | IOPV，该函数仅对ETF基金产品合约有效 |\
\
**BVOL**\
\
| 释义 | 取得外盘成交量 |\
| --- | --- |\
| 用法 | BVOL;//取当日外盘成交量 |\
\
**SVOL**\
\
| 释义 | 取得内盘成交量 |\
| --- | --- |\
| 用法 | SVOL;//取当日内盘成交量 |\
\
**CAPITAL**\
\
| 释义 | 流通盘大小 |\
| --- | --- |\
| 用法 | CAPITAL() |\
| 示例 | CAPITAL();//返回当前主图代码的流通盘，单位为手，主图为指数返回0 |\
\
**SUSPEND**\
\
| 释义 | 是否停牌 |\
| --- | --- |\
| 用法 | SUSPEND()，无参数时默认返回后一个交易日是否停牌，参数为-1 返回前一个交易日是否停牌，参数为日期返回指定日期是否停牌<br>返回值为 1 时，为停牌，为 0 时为非停牌 |\
| 示例 | SUSPEND(20220906);//返回2022年9月6日是否停牌 |\
\
## [\#](https://dict.thinktrader.net/VBA/system_function.html\#%E6%89%A9%E5%B1%95%E6%95%B0%E6%8D%AE) 扩展数据\
\
**EXTDATA**\
\
| 释义 | 获取指定周期的扩展数据 |\
| --- | --- |\
| 用法 | EXTDATA(NAME, STOCK, TIME)<br>其中 NAME 是扩展数据的名字， STOCK 表示品种，TIME 表示相对当前周期的偏移值，0 表示当前周期 |\
| 示例 | X:= EXTDATA('ZZZ', 'SZ600000', 0);//则 X 表示 600000在当前周期对应的扩展数据ZZZ中的值，股票市场须用大写字母 |\
\
**EXTDATARANGE**\
\
| 释义 | 获取扩展数据中满足指定条件的股票列表 |\
| --- | --- |\
| 用法 | EXTDATARANGE(NAME, TIME, CONDITION)<br>NAME 表示扩展数据的名字，TIME 表示相对当前周期的偏移值，0 表示当前周期，CONDITION 为条件字符串 |\
| 示例 | LIST := EXTDATARANGE('ZZZ', 0, 'ZZZ == 10');// 则 LIST为当前周期，ZZZ 的值等于 10 的股票代码的集合 |\
\
**EXTDATAMATCH**\
\
| 释义 | 判断该周期当前品种的扩展数据是否满足指定条件 |\
| --- | --- |\
| 用法 | EXTDATAMATCH(NAME, TIME, CONDITION)<br>表示当前品种在名为 NAME 的扩展数据中的 TIME 周期的值是否满足 CONDITION条件 |\
| 示例 | X:=EXTDATARANGE('ZZZ',0,'ZZZ==10');//则X表示当前品种当前周期ZZZ的值是否等于10,若等于10,则X=1,否则X=0 |\
\
**EXTDATARANK**\
\
| 释义 | 判断当前品种的扩展数据在所有品种中的排名，按扩展数据值从大到小排 |\
| --- | --- |\
| 用法 | EXTDATARANK(NAME, TIME)<br>表示当前品种在名为 NAME 的扩展数据中的 TIME 周期的值在所有品种中的排名 |\
| 示例 | X:=EXTDATARANK('ZZZ',0,'SZ000001') ;//表示当前品种('SZ000001')当前周期 ZZZ 的值在 ZZZ 对应的股票集合中的排名 |\
\
**EXTDATABIGGER**\
\
| 释义 | 返回扩展数据中比某个值大的股票数 |\
| --- | --- |\
| 用法 | EXTDATABIGGER(NAME, TIME, VALUE)<br>表示当前品种在名为 NAME 的扩展数据中的 TIME 周期的值在所有品种中的排名 |\
| 示例 | X := EXTDATABIGGER('ZZZ',0, 10);// 表示扩展数据当前周期 ZZZ 的值比 10 大的数的个数 |\
\
**EXTRANKTOVALUE**\
\
| 释义 | 返回扩展数据中排名为某一位置对应的数值 |\
| --- | --- |\
| 用法 | EXTRANKTOVALUE(NAME, TIME, RANK)<br>表示当前品种在名为 NAME 的扩展数据中的 TIME 周期排名为 RANK 的对应数值 |\
| 示例 | X := EXTRANKTOVALUE('ZZZ', 0,10);// 表示扩展数据 ZZZ当前周期排名为 10 的股票对应的值 |\
\
## [\#](https://dict.thinktrader.net/VBA/system_function.html\#%E7%BB%84%E5%90%88%E6%A8%A1%E5%9E%8B) 组合模型\
\
**SETGROUPMAXHOLDING**\
\
| 释义 | 设置组合模型的最大持仓 |\
| --- | --- |\
| 用法 | SETGROUPMAXHOLDING(50)表示最多同时持有 50 支股票的仓位 |\
\
**SETGROUPMODE**\
\
| 释义 | 组合模型运行模式 |\
| --- | --- |\
| 用法 | SETGROUPMODE(MODE)<br>MODE 的取值为0或1，0 表示纯脚本运行的模型，1 表示在 C++中控制买卖逻辑的运行方式，默认为 0 |\
\
**GETSTOCKINFO**\
\
| 释义 | 获取个股的买卖点信息 |\
| --- | --- |\
| 用法 | STOCKINFO := GETSTOCKINFO(STOCK，1); 表示取股票代码为 STOCK 的股票下一周期的买卖点信息，STOCK 为股票名称，1 为周期偏移量（取当前为 0，向前取为负数，向后取为正数）<br>返回值是一个结构体，该结构体有三个属性：BUY, SELL, PRICE;<br>STOCKINFO.BUY > 0 表示该周期出现了买点<br>STOCKINFO.SELL > 0 表示该周期出现了卖点<br>STOCKINFO.PRICE 表示该周期该个股的收盘价，可以用于计算买入价和卖出价<br>STOCKINFO.HOLDING 表示该周期该个股的持仓，可用于判断买卖点<br>STOCKINFO.SUSPEND 表示该周期该个股是否停盘，STOCKINFO.SUSPEND=1 表示停盘，STOCKINFO.SUSPEND=0 表示未停盘 |\
\
**GETSTOCKINFOBYINDEX**\
\
| 释义 | 获取个股的买卖点信息 |\
| --- | --- |\
| 用法 | 用法和 GETSTOCKINFO 一样 |\
| 示例 | GETSTOCKINFOBYINDEX(STOCKID，0);//表示取股票代码为 STOCKID 的股票的当前周期的买卖点信息 |\
\
**ISSTOCKINHOLDING**\
\
| 释义 | 查询当前股票是否有持仓 |\
| --- | --- |\
| 用法 | ISSTOCKINHOLDING(STOCK);表示股票代码为 STOCK 的股票是否在当前的持仓组合中。TRUE 表示在，FALSE 表示不在 |\
| 示例 | ISSTOCKINHOLDING('SZ000001')表示当前持仓中是否有平安银行 |\
\
**ISSTOCKINHOLDINGBYINDEX**\
\
| 释义 | 查询当前股票是否有持仓 |\
| --- | --- |\
| 用法 | 用法和 ISSTOCKINHOLDING 一样 |\
| 示例 | ISSTOCKINHOLDING(STOCKID);// 表示股票代码为 STOCKID 的股票是否在当前的持仓组合中。TRUE 表示在，FALSE 表示不在。 |\
\
**GETHOLDINGINFO**\
\
| 释义 | 获取个股的持仓信息 |\
| --- | --- |\
| 示例 | HOLDING = GETHOLDINGINFO(STOCK);//表示获取当前持仓中，STOCK 所对应的股票的持仓信息，返回值是一个结构体，该结构体有五个属性：HOLDING, BUYPRICE,BUYDATE, PROFIT, PRICE, HOLDINGPERIODS;<br>HOLDING.HOLDING 表示持仓手数<br>HOLDING.BUYPRICE 表示买入价格<br>HOLDING.BUYDATE 表示买入的时间点<br>HOLDING.PROFIT 表示从买入到当前的利润<br>HOLDING.PRICE 表示当前周期的收盘价<br>HOLDING.HOLDINGPERIODS 表示从买入到当前周期的周期数 |\
\
**GETHOLDINGINFOBYINDEX**\
\
| 释义 | 获取个股的持仓信息 |\
| --- | --- |\
| 用法 | 用法和 GETHOLDINGINFO 一样 |\
| 示例 | HOLDING = GETHOLDINGINFOBYINDEX(STOCKID);//表示获取当前持仓中，STOCK所对应的股票的持仓信息 |\
\
**GROUPBUY**\
\
| 释义 | 组合模型买入 |\
| --- | --- |\
| 示例 | GROUPBUY(STOCK);//表示在组合模型中买入股票 STOCK，该操作会将该股票记入组合模型的持仓中，并将股票加入该周期的买入面板中 |\
\
**GROUPBUYBYINDEX**\
\
| 释义 | 组合模型买入 |\
| --- | --- |\
| 用法 | 用法和 GROUPBUY 一样 |\
| 示例 | GROUPBUYINDEX(STOCK);//表示在组合模型中买入股票 STOCK |\
\
**GROUPSELL**\
\
| 释义 | 组合模型卖出 |\
| --- | --- |\
| 用法 | GROUPSELL(STOCK)表示在组合模型中卖出股票 STOCK，该操作会从组合模型的持仓中删除该股票，并将股票加入该周期的卖出面板中。<br>GROUPSELL 返回一个 BOOL 变量，表示卖出是否成功，比如在股票停牌时，是无法卖出的 |\
\
**GROUPSELLBYINDEX**\
\
| 释义 | 组合模型卖出 |\
| --- | --- |\
| 用法 | 用法和 GROUPSELL 一样 |\
| 示例 | GROUPSELLBYINDEX(STOCKID);//表示在组合模型中卖出股票 STOCKID |\
\
**GROUPPOSSIBLEBUY**\
\
| 释义 | 组合模型计算买入备选 |\
| --- | --- |\
| 用法 | GROUPPOSSIBLEBUY(STOCKID)表示将 STOCKID 加入组合模型该周期中的买入备选面板中，每周期买入的股票是买入备选股票集的一个子集 |\
\
**GROUPPOSSIBLEBUYBYINDEX**\
\
| 释义 | 组合模型计算买入备选 |\
| --- | --- |\
| 用法 | 用法和 GROUPPOSSIBLEBUY 一样 |\
| 示例 | GROUPPOSSIBLEBUYBYINDEX(STOCKID);//表示将STOCKID 加入组合模型该周期中的买入备选面板中 |\
\
**GROUPPOSSIBLESELL**\
\
| 释义 | 组合模型计算卖出备选 |\
| --- | --- |\
| 用法 | GROUPPOSSIBLESELL(STOCK)表示将STOCK 加入组合模型该周期中的卖出备选面板中，每周期卖出的股票是卖出备选股票集的一个子集 |\
\
**GROUPPOSSIBLESELLBYINDEX**\
\
| 释义 | 组合模型计算卖出备选 |\
| --- | --- |\
| 用法 | 用法和 GROUPPOSSIBLESELL 一样 |\
| 示例 | GROUPPOSSIBLESELLBYINDEX(STOCKID);//表示将 STOCKID 加入组合模型该周期中的卖出备选面板中 |\
\
**INDYNAMICBASKET**\
\
| 释义 | 是否在动态股票篮子中 |\
| --- | --- |\
| 用法 | INDYNAMICBASKET(STOCKID)表示 STOCKID 是否在动态股票篮子中，TRUE 表示在，FALSE 表示不在。 |\
\
**SETGROUPINDEX**\
\
| 释义 | 当组合模型的运行模式为 C++模式时，设置这个参数，可以在买入备选中的股票数大于可能的最大持仓时，将买入备选中的股票根据扩展数据的排名排序，并买入排名靠前的股票。 |\
| --- | --- |\
| 用法 | 组合模型中有下面的代码时<br>SETGROUPMAXHOLDING(50);<br>SETGROUPMODE(1);<br>SETGROUPINDEX('RISE');<br>则当某个周期持仓中已经有了 40 支股票，并且还有 30 支股票出现了买点时，组合模型会根据 RISE 中的排名信息，将买入备选中的 30 支股票进行排序，并买入排名靠前的 10 支股票 |\
\
**GETHOLDINGPROFIT**\
\
| 释义 | 获取组合模型当前周期持仓的浮动赢亏 |\
| --- | --- |\
| 用法 | X := GETHOLDINGPROFIT(); 则 X 表示当前周期组合模型持仓的浮动赢亏 |\
\
## [\#](https://dict.thinktrader.net/VBA/system_function.html\#%E7%BB%84%E5%90%88%E6%A8%A1%E5%9E%8B%E4%BA%A4%E6%98%93%E5%87%BD%E6%95%B0) 组合模型交易函数\
\
**ORDER**\
\
| 释义 | 通过网络端口向指定的接口单发送交易信号 |\
| --- | --- |\
| 用法 | ORDER 的语法格式为 ORDER(OPERATION, CHANNEL, ADDR);<br>OPERATION 表示交易的类型，CHANNEL 为指定的接口单通道号，ADDR 为可选参数，不填表示使用系统默认的地址连接迅投量化投研平台，可以指定多个迅投量化投研平台中的接口单地址，多个地址间用逗号隔开 |\
| 示例 | ORDER(3, 1, '192.168.1.128:5000, 192.168.1.129:5000'); 表示向192.168.1.128, 192.168.1.129 这两台机器上的 1 号接口单发送开空信号 |\
\
**PASSORDER**\
\
| 释义 | 通过函数交易发送交易信号 |\
| --- | --- |\
| 用法 | PASSORDER 的语法格式为 PASSORDER(OPTYPE, ORDERTYPE , ACCOUNTID,ORDERCODE, PRTYPE, PRICE, VOLUME\[,strategyName\])<br>数据类型：OPTYPE,ORDERTYPE,PRTYPE,PRICE,VOLUME 是数字类型;ACCOUNTID,ORDERCODE 是字符串类型<br>（1）最少填七个参数<br>（2）其中\[strategyName\] 自定义策略名不是必填<br>（3）OPTYPE(操作类型):<br>OPTYPE 参数内容说明<br>期货六键:<br>0/开多<br>1/平昨多<br>2/平今多<br>3/开空<br>4/平昨空<br>5/平今空<br>期货四键:<br>6/平多,优先平今<br>7/平多,优先平昨<br>8/平空,优先平今<br>9/平空,优先平昨<br>期货两键:<br>10/卖出,如有多仓,优先平仓,优先平今,如有余量,再开空<br>11/卖出,如有多仓,优先平仓,优先平昨,如有余量,再开空<br>12/买入,如有空仓,优先平仓,优先平今,如有余量,再开多<br>13/买入,如有空仓,优先平仓,优先平昨,如有余量,再开多<br>14/买入,不优先平仓<br>15/卖出,不优先平仓<br>股票买卖:<br>23/股票买入<br>24/股票卖出<br>融资融券:<br>27/融资买入<br>28/融券卖出<br>29/买券还券<br>30/直接还券<br>31/卖券还款<br>32/直接还款<br>组合交易:<br>25/组合买入<br>26/组合卖出<br>37/卖出投资组合<br>40/期货组合开多<br>43/期货组合开空<br>46/期货组合平多,优先平今<br>47/期货组合平多,优先平昨<br>48/期货组合平空,优先平今<br>49/期货组合平空,优先平昨<br>（4）ORDERTYPE(下单类型):<br>参数详细说明<br>11:单股、单账号、普通 、默认 方式下单,兼容以前的 11 模式<br>1101:单股、单账号、普通、股/手 方式下单(同 11)<br>1102:单股、单账号、普通、金额（元）方式下单(该方式只支持股票下单)<br>1113:单股、单账号、总资产、比例(0~1)方式下单<br>1123:单股、单账号、可用、比例(0~1)方式下单<br>12:单股、账号组(无权重)、普通 、默认 方式下单,兼容以前的 12 模式<br>1201:单股、账号组(无权重)、普通、股/手 方式下单(同 12)<br>1202:单股、账号组(无权重)、普通、金额（元）方式下单(该方式只支持股票下单)<br>1213:单股、账号组(无权重)、总资产、比例(0~1)方式下单<br>1223:单股、账号组(无权重)、可用、比例(0~1)方式下单<br>21:组合、单账号、普通 、默认方式下单，兼容以前的 21 模式<br>2101:组合、单账号、普通、按组合股票数量 方式下单,对应 VOLUME 填篮子份数(同 21)<br>2102:组合、单账号、普通、按组合股票权重 方式下单,对应 VOLUME 填金额(元)<br>2103:组合、单账号、普通、按账号可用方式下单,对应VOLUME 填比例(0~1)(该方式只支持股票组合)<br>22:组合、账号组(无权重)、普通 、默认方式下单，兼容以前的 22 模式<br>2201:组合、账号组(无权重)、普通、按组合股票数量 方式下单,对应 VOLUME填篮子份数(同 22)<br>2202:组合、账号组(无权重)、普通、按组合股票权重 方式下单,对应 VOLUME填金额(元)<br>2203:组合、账号组(无权重)、普通、按账号可用方式下单,对应 VOLUME 填比例(0~1)(该方式只支持股票组合)<br>2331:组合、套利、合约价值自动套利、按组合股票数量 方式下单,对应VOLUME 填篮子份数<br>2332:组合、套利、按合约价值自动套利、按组合股票权重 方式下单,对应VOLUME 填金额(元)<br>2333:组合、套利、按合约价值自动套利、按账号可用方式下单,对应 VOLUME填比例(0~1)<br>组合套利对 ACCOUNTID 参数的约定:accountID :=<br>'stockAccountID,futureAccountID'<br>组合套利对 ORDERCODE 参数的约定:orderCode :='basketName,futureName'<br>对 PRICE 参数的约定为套利比例(0~1)<br>融资融券只支持 ORDERTYPE:11,1101,1102,12,1201,1202<br>（5） ACCOUNTID(账号 ID):下单的账号 ID 或 账号组名;组合套利时用逗号隔开股票账号和期货账号<br>（6） ORDERCODE(下单代码):<br>两种情况:单股或单期货、港股,则该参数填合约代码;组合交易,则该参数填篮子名称,组合套利时用逗号隔开篮子名称和期货合约名<br>（7） PRTYPE(下单选价类型):<br>4 卖 1 价,5 最新价,6 买 1 价,12 市价,13 挂单价,14 对手价,11(指定价)模型价<br>（8） PRICE(下单价格):当 PRTYPE 是模型价(指定价)11 时 PRICE 有效,其它情况下 PRICE 无效(注意:组合套利时该参数作套利比例)<br>（9） VOLUME:下单数量(股 OR 手)、组合份数、资金比例(0~1) |\
| 示例 | PASSORDER(23,1101,'6000000201','SH600000',5, -1, 100); 表示发送账号6000000201 以最新价买入 100 股 SH600000 的信号；<br>PASSORDER(25,2333,'6000000201,037429','stockbasket,IF1703',5, 1,0.5);表示以最新价买入账号 6000000201 的 50%的可用资金的一篮子股票stockbasket,并以 100%的套利比例在期货账号 037429 下开空单 IF1703 |\
\
**TACCOUNT**\
\
| 释义 | 获取指定账号的可用资金 |\
| --- | --- |\
| 用法 | TACCOUNT(1, '37500001');<br>1 表示是期货账号，2 为普通股票账号，3 为信用账号，37500001 是账号 ID，返回可用资金； |\
\
**HOLDING**\
\
| 释义 | 得到当前帐户持仓量,多仓返回正数空仓返回负数 |\
| --- | --- |\
| 用法 | HOLDING(AccountID , MarketID, StockID, Direction);<br>AccountID, MarketID, StockID 为字符串；Direction 为整型（1 多，2 空） |\
| 示例 | 例 1: ho:=holding('037055','IF','IF06',2); //IF06 做空持仓<br>例 2: ho:=holding('6000000255','SH','600000',1) //股票 600000 持仓 |\
\
**HOLDINGS**\
\
| 释义 | 取某资金帐号对应的持仓 |\
| --- | --- |\
| 用法 | holdings(Account);表示获取帐号 Account 的持仓 |\
| 示例 | 统计某个账号所有品种做多方向的持仓<br>xxx := holdings('037055');<br>loh := 0;<br>for x in xxx do begin<br>if x.direction = 48 then<br>loh:= loh + x.volume;<br>end<br>longhold:loh;<br>xxx 为一个 positiondetail 结构体，含有如下项：<br>exchangeid 证券市场,交易所代码<br>exchangename 市场名字<br>productid 品种代码<br>productname 品种名称<br>instrumentid 证券代码,合约代码<br>instrumentname 证券名称,合约名称<br>hedgeflag 投保<br>direction 买卖<br>opendate 成交日期<br>tradeid 最初开仓位的成交<br>volume 持仓量 当前拥股<br>openprice 开仓价<br>tradingday 交易日<br>margin 使用的保证金 历史的直接用 ctp 的，新的自己用成本价 _存量_ 系数算 股票不需要<br>opencost 开仓成本 等于股票的成本价 _第一次建仓的量，后续减持不影响，不算手续费 股票不需要_<br>_settlementprice /结算价 对于股票的当前价_<br>_closevolume 平仓量 等于股票已经卖掉的 股票不需要_<br>_closeamount 平仓额 等于股票每次卖出的量_ 卖出价 _合约乘数（股票为 1）的累加 股票不需要_<br>_dloatprofit 浮动盈亏 当前量_（当前价-开仓价） _合约乘数（股票为 1）_<br>_closeprofit 平仓盈亏 平仓额 - 开仓价_ 平仓量\*合约乘数（股票为 1）股票不需要<br>marketvalue 市值 合约价值<br>positioncost 持仓成本 股票不需要<br>positionprofit 持仓盈亏 股票不需要<br>lastsettlementprice 最新结算价 股票不需要<br>instrumentvalue 合约价值 股票不需要<br>istoday 是否今仓<br>xttag 迅投量化投研平台标签<br>stockholder 股东账号<br>frozenvolume 期货不用这个字段，冻结数量<br>canusevolume 期货不用这个字段，股票的可用数量<br>onroadvolume 期货不用这个字段，股票的在途数量<br>yesterdayvolume 期货不用这个字段，股票的股份余额<br>lastprice 结算价 对于股票的当前价<br>profitrate 持仓盈亏比例<br>futuretradetype 成交类型<br>expiredate 到期日，逆回购用<br>comtradeid 套利成交 Id<br>legid 组合 Id<br>totalcost 自定义累计成本 股票信用用到<br>singlecost 自定义单股成本 股票信用用到<br>coveredvolume 用于个股期权<br>sideflag 用于个股期权，标记 '0' - 权利，'1' - 义务，'2' - '备兑'<br>referencerate 汇率,目前用于港股通<br>structfundvol 分级基金可用（可分拆或可合并）<br>redemptionvolume 分级基金可赎回量 |\
\
**ORDERING（不生效）**\
\
| 释义 | 返回当前委托数量 |\
| --- | --- |\
| 用法 | ordering(AccountID , MarketID, StockID, Direction, \[strategyName\]);<br>AccountID, MarketID, StockID 为字符串；Direction 为整型(1 buy，2sell),strategyName 自义定策略名,为字符串 |\
| 示例 | 例 1: ho:=ordering('037055','IF','IF06',2);<br>例 2: ho:=ordering('6000000255','SZ','000001',1)<br>例 3: ho:=ordering('6000000255','SZ','000001',1, '策略 1') |\
\
**ORDERINGS**\
\
| 释义 | 取某资金账号对应的委托信息 |\
| --- | --- |\
| 用法 | orderings(AccountID, \[strategyName\]); |\
| 示例 | 统计某账号买入的所有的当天委托量<br>xxx := orderings('037055');<br>loo := 0;<br>for x in xxx do begin<br>if x.direction = 48 then<br>loo:= loo + x.volumetotaloriginal;<br>end<br>longorder:loo<br>xxx 为一个 orderdetail 结构体，含有如下项：<br>exchangeid 证券市场,交易所代码<br>exchangename 市场名字<br>productid 品种代码<br>productname 品种名称<br>instrumentid 证券代码,合约代码<br>instrumentname 证券名称,合约名称<br>sessionid<br>frontid 前端 id<br>orderref 下单引用 等于股票的内部委托号<br>orderpricetype 类型，例如市价单 限价单<br>direction 期货多空 股票买卖<br>offsetflag 期货开平，股票买卖其实就是开平<br>hedgeflag 投保<br>limitprice 限价单的限价，就是报价<br>volumetotaloriginal 最初委托量<br>ordersubmitstatus 提交状态<br>ordersysid 委托号<br>orderstatus 委托状态<br>volumetraded 已成交量<br>volumetotal 当前总委托量 股票不需要总委托量<br>errorid<br>errormsg 状态信息<br>taskid<br>frozenmargin 冻结保证金<br>frozencommission 冻结手续费<br>insertdate 日期<br>inserttime 时间<br>xttag 迅投量化投研平台标签<br>tradeprice 成交均价<br>cancelamount 已撤数量<br>optname 展示委托属性的中文<br>tradeamount 成交额 期货=均价 _量_ 合约乘数<br>entrusttype 委托类别<br>cancelinfo 废单原因<br>undercode 标的证券<br>covereflag 备兑标记 '0' - 非备兑，'1' - 备兑<br>orderpricermb 委托价格 人民币 用于港股通<br>tradeamountrmb 成交金额 人民币用于港股通<br>referencerate 参考汇率 用于港股通 |\
\
**DEAL**\
\
| 释义 | 返回某个时间内的成交数量 |\
| --- | --- |\
| 用法 | deal(AccountID , MarketID, StockID, Direction,\[strategyName\]);<br>或 deal(AccountID , MarketID, StockID, Direction, Senconds,\[strategyName\]);<br>AccountID, MarketID, StockID 为字符串；Direction 为整型（1 多，2 空）,Senconds 为整型，表示多少秒内;strategyName 自义定策略名,为字符串 |\
| 示例 | 例 1：de:=deal('037055','IF','IF06',2);//返回当天 IF06 的 sell 的成交数量 |\
\
**DEALS**\
\
| 释义 | 返回资金账号的成交信息 |\
| --- | --- |\
| 用法 | deals(AccountID,\[strategyName\]); |\
| 示例 | 返回某账号当天 buy 的成交量<br>xxx := deals('037055');<br>dea:= 0;<br>for x in xxx do begin<br>if x.direction = 48 then<br>dea:= dea + x.volume;<br>end<br>longdeal:dea<br>xxx 为一个 dealdetail 结构体，含有如下项：<br>exchangeid 证券市场,交易所代码<br>exchangename 市场名字<br>productid 品种代码<br>productname 品种名称<br>instrumentid 证券代码,合约代码<br>instrumentname 证券名称,合约名称<br>tradeid 成交编号<br>orderref 下单引用 等于股票的内部委托号<br>ordersysid 委托号<br>direction 买卖 股票不需要<br>offsetflag 开平 股票的买卖<br>hedgeflag 投保 股票不需要<br>price 成交均价<br>volume 成交量 期货单位手 股票做到股<br>tradedate 成交日期<br>tradetime 成交时间<br>comssion 手续费<br>tradeamount 成交额 期货=均价 _量_ 合约乘数<br>taskid<br>xttag 迅投量化投研平台标签<br>orderpricetype 类型，例如市价单 限价单<br>optname 展示委托属性的中文<br>entrusttype 委托类别<br>futuretradetype 成交类型<br>realoffsetflag 实际开平,主要是区分平今和平昨<br>coveredflag 备兑标记 '0' - 非备兑，'1' - 备兑<br>closetodayvolume 平今量, 不显示<br>orderpricermb 委托价格 人民币 用于港股通<br>pricermb 目前用于港股通<br>tradeamountrmb 目前用于港股通<br>referencerate 汇率,目前用于港股通<br>xttrade 是否是迅投量化投研平台交易 |\
\
**LOADBASKET（返回结果和定义不符）**\
\
| 释义 | 读取导入指定定路径下的指定的交易篮子 |\
| --- | --- |\
| 用法 | loadbasket(dir,filename,iscover) {filename 格式支持：xls、csv}<br>iscover:遇到同名篮子操作: 0 取消导入, 1 覆盖, 2 合并 |\
| 示例 | retv:=loadbasket('c:/test/','stockbasket.csv',1)//该路径下存在对应文件返回 0，否则返回-1<br>或<br>retv:=loadbasket('c:\\test\\','stockbasket.csv',1)//该路径下存在对应文件返回 0，否则返回-1 |\
\
**CANCEL**\
\
| 释义 | 针对委托号进行撤单 |\
| --- | --- |\
| 用法 | CANCEL(AA)表示委托号为“AA”的委托 |\
\
**STOPPRICE**\
\
| 释义 | 取个股,期货,期权涨停价,跌停价 |\
| --- | --- |\
| 用法 | stopprice(select)或 stopprice(select , market, stockCode);<br>其中 select 为整型（1 跌停价,2 涨停价）,market, stockCode 为字符串. |\
| 示例 | 例 1：dd:=stopprice(1);//返回当前股票的跌停价<br>例 2：dd:=stopprice(1,'IF','IF06',);//指定 IF06,返回 IF06 的跌停价 |\
\
**Contractmultiplier**\
\
| 释义 | 函数返回期货合约的乘数 |\
| --- | --- |\
| 用法 | contractmultiplier('') //取当前图合约的乘数<br>contractmultiplier(contract)<br>contract 为合约代码，支持变量赋值 |\
| 示例 | contract:='cu06';<br>contract:='cu06'; |\
\
## [\#](https://dict.thinktrader.net/VBA/system_function.html\#%E7%B3%BB%E7%BB%9F%E5%87%BD%E6%95%B0) 系统函数\
\
**PRINTOUT**\
\
| 释义 | 把变量输出到文件 |\
| --- | --- |\
| 用法 | PRINTOUT(X,Y) |\
| 示例 | //模型名是“新建模型 1”<br>X : CLOSE;<br>Y : HIGH;<br>PRINTOUT(X,Y);//在安装目录的 BIN 子目录下生成名为：新建模型 1.OUT |\
\
**ISEQUALV**\
\
| 释义 | 判断两个数是否相等 |\
| --- | --- |\
| 用法 | ISEQUALV（X，Y）判断X和Y是否相等，相等返回 1，否则返回 0 |\
| 示例 | ISEQUALV(2,2);//返回 1 |\
\
**ISGREATER**\
\
| 释义 | 判断是否大于 |\
| --- | --- |\
| 用法 | ISGREATER（A,B）,如果 A 大于 B 返回 1，否则返回 0 |\
| 示例 | ISGREATER(5,4);//返回 1 |\
\
**ISGREATEREQUAL**\
\
| 释义 | 判断是否大于等于 |\
| --- | --- |\
| 用法 | ISGREATEREQUAL（A,B）,如果 A 大于等于 B 返回 1，否则返回 0 |\
| 示例 | ISGREATEREQUAL(1,1);//返回 1 |\
\
**ISLESSEQUAL**\
\
| 释义 | 判断是否小于等于 |\
| --- | --- |\
| 用法 | ISLESSEQUAL（A,B），如果 A 小于等于 B 返回 1，否则返回 0 |\
| 示例 | ISLESSEQUAL(2,1);//返回 0 |\
\
**ISVALID**\
\
| 释义 | 判断是否为有效数据 |\
| --- | --- |\
| 用法 | ISVALID(X)判断X是否是有效数据。有效数据时返回1,否则返回0 |\
| 示例 | ISVALID(2/0);//返回 0 |\
\
**NOSORTED**\
\
| 释义 | 对集合中的元素不进行排序，在不计算排名的情况下使用 |\
| --- | --- |\
| 示例 | NOSORTED($ZZZ);//表示对股票篮子 ZZZ 不排序 |\
\
**EXIST**\
\
| 释义 | 判断是否存在下标中 |\
| --- | --- |\
| 用法 | EXIST(SELLMMM\[\], SELLRANK) |\
| 示例 | EXIST(SELLMMM\[\], SELLRANK);//如果 SELLMMM 下标存在 SELLRANK 返回 1，否则返回 0 |\
\
**HOLDINGORNOT**\
\
| 释义 | 判断是否存在集合或数组中 |\
| --- | --- |\
| 用法 | HOLDINGORNOT(A\[\],B);//如果 A 中存在 B 返回 1，或者返回 0 |\
\
**TOHOLD**\
\
| 释义 | 把股票加入到一个集合中 |\
| --- | --- |\
| 用法 | TOHOLD(POSITION,STOCKID);//表示把股票 STOCKID 加入到 POSITION 中 |\
\
**TOABANDON**\
\
| 释义 | 把股票从集合中删除 |\
| --- | --- |\
| 用法 | TOABANDON(POSITION,STOCKID);//表示把股票 STOCKID 从 POSITION 中删除 |\
\
**SETDATAALIGNMODE**\
\
| 释义 | 当模型引用了多个品种的数据时，该函数设置数据对齐的方式，该函数有 1 个参数，取值说明如下：<br>0：当前 K线所有品种的数据到齐后才开始计算 <br>1：当前 K 线只要主图品种的数据到了后就开始计算 |\
| --- | --- |\
| 用法 | SETDATAALIGNMODE（1）;//当前K线只要主图品种的数据到了后就开始计算 |\
\
**SENDMAIL**\
\
| 释义 | 发送邮件，该函数有 7 个参数，取值说明如下：<br>SERVERNAME smtp 或 pop 服务器地址USERNAME<br>发 送 邮 箱 地 址<br>PASSWORD 发 送 邮 箱 登 陆 密 码<br>RECVNAME 接 收 者 姓 名<br>RECVADDR 接 收 者 邮 箱 地 址<br>SUBJECT 标题<br>CONTENT 内容 |\
| --- | --- |\
| 用法 | SENDMAIL('smtp.thinktrader.net','zhangsan@thinktrader.net','123456','lisi','lisi@thinktrader.net','say hello','Hello,lisi') |\
\
## [\#](https://dict.thinktrader.net/VBA/system_function.html\#%E9%99%84%E5%8A%A0%E5%87%BD%E6%95%B0) 附加函数\
\
**CONST**\
\
| 释义 | 取常数值 |\
| --- | --- |\
| 用法 | CONST(A)返回 A 的常数值 |\
\
**ISBUYORDER**\
\
| 释义 | 取得该成交是否为主动性买单 |\
| --- | --- |\
| 用法 | ISBUYORDER()仅用于分笔图 |\
| 示例 | ISBUYORDER();//当本笔成交为主动性买盘时,返回1,否则为0 |\
\
**ISSELLORDER**\
\
| 释义 | 取得该成交是否为主动性卖单 |\
| --- | --- |\
| 用法 | ISSELLORDER()仅用于分笔图 |\
| 示例 | ISSELLORDER();//当本笔成交为主动性卖盘时,返回1,否则为0 |\
\
**BUYVOL**\
\
| 释义 | 主动性买单量 |\
| --- | --- |\
| 用法 | BUYVOL()仅用于分笔图,非主动性买单返回 0 |\
| 示例 | BUYVOL();//当本笔成交为主动性买盘时,其数值等于成交量,否则为0 |\
\
**SELLVOL**\
\
| 释义 | 主动性卖单量 |\
| --- | --- |\
| 用法 | SELLVOL()仅用于分笔图，非主动性卖单返回 0 |\
| 示例 | SELLVOL();//当本笔成交为主动性卖盘时,其数值等于成交量,否则为0 |\
\
**UPNDAY**\
\
| 释义 | 返回是否连涨周期数 |\
| --- | --- |\
| 用法 | UPNDAY(X,N)判断X是否连续N周期上涨 |\
| 示例 | upclose:=UPNDAY(close,2);//判断是否收盘价连续两周期上涨<br>ma5:=MA(CLOSE,5);<br>UPma5:UPNDAY(ma5,5);//判断5日均线是否连续五周期上涨 |\
\
**DOWNNDAY**\
\
| 释义 | 返回是否连跌周期数 |\
| --- | --- |\
| 用法 | DOWNNDAY(X,N)判断X是否连续N周期下跌 |\
| 示例 | DOWNclose:=DOWNNDAY(close,2);//判断是否收盘价连续两周期下跌<br>ma5:=MA(CLOSE,5);<br>DOWNma5:DOWNNDAY(ma5,5);//判断5日均线是否连续五周期下跌 |\
\
**NDAY**\
\
| 释义 | 判断是否多周期一直有某式成立 |\
| --- | --- |\
| 用法 | NDAY(X,N)判断在N个周期内X一直满足 |\
| 示例 | NDAY(close>open,2);//判断是否连续两周期收盘价大于开盘价 |\
\
**BUYSELLVOLS**\
\
| 释义 | 获取当前标的品种总买、总卖量 |\
| --- | --- |\
| 用法 | BUYSELLVOLS(MODE)MODE为0或1,0代表获取总卖,1代表获取总买。Level-2专用 |\
| 示例 | BUYSELLVOLS(0);//获取当前标的品种总卖量<br>BUYSELLVOLS(1);//获取当前标的品种总买量 |\
\
**GETOPENDATE**\
\
| 释义 | 获取当前标的品种总买、总卖量 |\
| --- | --- |\
| 用法 | GETOPENDATE(S)获取股票S上市日期,S为股票代码与交易所代码组合代码,如'SH600000',当S为空,取当前主界面显示的交易所及合约代码.，返回值为YYYYMMDD |\
| 示例 | GETOPENDATE('');//获取当前主图标的上市日期<br>BUYSELLVOLS('SH600000');//获取SH600000上市日期 |\
\
**GETOPENAMOUNT**\
\
| 释义 | 获取当前主界面显示的合约代码集合竞价的成交额 |\
| --- | --- |\
| 用法 | GETOPENAMOUNT() |\
| 示例 | GETOPENAMOUNT();//获取主图标的集合竞价的成交额 |\
\
**GETOPENVOL**\
\
| 释义 | 获取当前主界面显示的合约代码集合竞价的成交量(手) |\
| --- | --- |\
| 用法 | GETOPENVOL() |\
| 示例 | GETOPENVOL();//获取主图标的集合竞价的成交量 |\
\
**TICKVOLDISTRIBUTION**\
\
| 释义 | 返回固定时间范围内成交量分布中超过某个占比对应的最优 VOL |\
| --- | --- |\
| 用法 | TICKVOLDISTRIBUTION(seconds,ratio,direction),seconds:秒数, ratio:总笔数占比(0~1\],direction:0 all,1 buy,2 sell |
| 示例 | TICKVOLDISTRIBUTION(120,0.4,1);//主动性买盘 120 秒内占总笔数 40%的最优vol值 |
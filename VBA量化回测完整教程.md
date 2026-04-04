# VBA量化回测完整教程

> 本教程详细介绍如何使用QMT的VBA模型进行量化策略回测,从基础语法到高级策略实战

## 目录

- [一、VBA回测基础概念](#一vba回测基础概念)
- [二、VBA模型编写规范](#二vba模型编写规范)
- [三、核心系统函数](#三核心系统函数)
- [四、完整回测策略示例](#四完整回测策略示例)
- [五、回测运行机制](#五回测运行机制)
- [六、高级回测技巧](#六高级回测技巧)
- [七、常见问题与优化](#七常见问题与优化)
- [八、VBA与Python混合编程](#八vba与python混合编程)

---

## 一、VBA回测基础概念

### 1.1 什么是VBA模型回测

VBA模型是QMT内置的公式化交易系统,采用类似通达信、大智慧的公式语法,通过序列计算和逐K线模式进行策略回测和实盘交易。

**VBA模型的特点**:
- 基于序列数据计算,天然支持向量化运算
- 语法简洁,适合技术指标计算
- 运行在QMT高性能计算引擎中
- 支持实时行情推送和自动化交易

### 1.2 VBA回测 vs Python回测

| 对比维度 | VBA模型 | 内置Python |
|---------|---------|-----------|
| 语法风格 | 公式语言(类似通达信) | 标准Python 3.6+ |
| 计算模式 | 序列向量化计算 | 逐K线循环计算 |
| 适合场景 | 技术指标计算、选股模型 | 复杂策略逻辑、机器学习 |
| 性能 | 高性能(底层C++优化) | 中等(Python解释器) |
| 调试难度 | 较简单(公式直观) | 较复杂(代码量大) |
| 学习曲线 | 低(公式语法简单) | 中(需Python基础) |

### 1.3 VBA回测优势

1. **计算效率高**: VBA模型运行在QMT内置的高性能引擎中,对序列数据和K线计算做了深度优化
2. **代码简洁**: 一条公式即可完成复杂指标计算,无需导入库和编写循环
3. **向量化运算**: 天然支持序列运算,避免Python中的循环开销
4. **可视化强**: 直接在K线图上显示指标线和买卖信号
5. **易于分享**: 公式可以加密导出,方便策略共享

---

## 二、VBA模型编写规范

### 2.1 模型编辑器使用

**创建新模型**:
1. 在QMT左侧模型树中右键点击
2. 选择"新建模型"
3. 填写模型名称、描述、显示位置(主图/副图)

**模型界面要素**:
- **模型名称**: 由中文、字母和数字组成,必须唯一
- **模型描述**: 简短描述公式含义
- **显示位置**: 主图叠加或副图显示
- **参数设置**: 可定义可调参数(最小值、最大值、缺省值、步长)
- **用法注释**: 详细说明使用方法

### 2.2 VBA基础语法

#### 2.2.1 公式语句类型

**赋值语句** (会显示为图形):
```vba
ST:MA(CLOSE,5);  ' 计算5日均线并显示,名称为ST
```

**中间语句** (不显示,仅用于计算):
```vba
A:=X+Y;          ' 中间变量,不会显示
B:=A/Z;          ' 使用中间变量简化计算
C:=B*0.618;      ' 最终结果会显示
```

**关键区别**:
- `:` 冒号表示赋值并显示
- `:=` 冒号等号表示中间变量,不显示

#### 2.2.2 公式计算符

**算术计算符**:
```vba
+  加法
-  减法
*  乘法
/  除法
```

**逻辑计算符**:
```vba
>   大于
<   小于
<>  不等于
>=  大于等于
<=  小于等于
=   等于
AND 逻辑与
OR  逻辑或
```

**逻辑运算规则**:
- 条件成立返回 1
- 条件不成立返回 0

```vba
' 示例
X:=4>3;           ' X=1
Y:=3<=12;         ' Y=0
Z:=4>3 AND 12>=4; ' Z=1 (两个条件都成立)
W:=4>3 OR 3>12;   ' W=1 (至少一个条件成立)
```

#### 2.2.3 线形描述符

用于控制指标线的显示样式:

```vba
STICK            ' 柱状线
COLORSTICK       ' 彩色柱状线(正红负绿)
COLORRED         ' 红色线
COLORBLUE        ' 蓝色线
COLORYELLOW      ' 黄色线
VOLSTICK         ' 成交量柱状线(涨红跌绿)
LINESTICK        ' 柱状线+指标线
LINETHICK        ' 线粗细(0-7)
CROSSDOT         ' 小叉线
CIRCLEDOT        ' 小圆圈线
POINTDOT         ' 小圆点线
NODRAW           ' 不画线
```

**使用示例**:
```vba
' MACD彩色柱状图
DIFF:EMA(CLOSE,12)-EMA(CLOSE,26);
DEA:EMA(DIFF,9);
MACD:2*(DIFF-DEA),COLORSTICK;

' 自定义颜色
MA5:MA(CLOSE,5),COLOR00FFFF;  ' 纯红+纯绿混合(黄色)

' 自定义线粗细
MA10:MA(CLOSE,10),LINETHICK2; ' 较粗的线
```

### 2.3 数据引用规则

#### 2.3.1 基础行情数据

```vba
CLOSE      ' 收盘价
OPEN       ' 开盘价
HIGH       ' 最高价
LOW        ' 最低价
VOL        ' 成交量
AMOUNT     ' 成交额
```

#### 2.3.2 跨品种引用

```vba
' 引用其他股票数据
"SZ000002$VOL"     ' 000002的成交量
"SH000001$CLOSE"   ' 上证指数收盘价

' 使用CALLSTOCK函数
CALLSTOCK('SH000001',CLOSE)  ' 引用上证指数收盘价
```

#### 2.3.3 跨周期引用

**基本格式**: `"指标.指标线#周期"(参数)`

**周期代码**:
```
MIN1: 1分钟    MIN5: 5分钟    MIN15: 15分钟
MIN30: 30分钟  MIN60: 60分钟  DAY: 日线
WEEK: 周线     MONTH: 月线    YEAR: 年线
```

**示例**:
```vba
' 日线图中引用周线MACD
WEEKLY_MACD:"MACD.DEA#WEEK"(26,12,9);

' 引用昨日最高价(使用##扩展格式)
preDayHigh:"H.H1##day";
preDayLOW:"H.L1##day";
```

**注意**: `#` 和 `##` 的区别:
- `#`: 调用本周期所在的上一级周期数据
- `##`: 调用前一个周期数据

---

## 三、核心系统函数

### 3.1 行情函数

#### 常用行情函数

```vba
MA(CLOSE, N)              ' 简单移动平均
EMA(CLOSE, N)             ' 指数移动平均
REF(CLOSE, N)             ' 引用N周期前的收盘价
HHV(HIGH, N)              ' N周期内最高价
LLV(LOW, N)               ' N周期内最低价
CROSS(A, B)               ' A上穿B
BARSLAST(COND)            ' 上次条件COND成立到现在的周期数
COUNT(COND, N)            ' N周期内COND成立的次数
EVERY(COND, N)            ' N周期内COND一直成立
EXIST(COND, N)            ' N周期内COND至少成立一次
NOT(COND)                 ' 逻辑非
IF(COND, A, B)            ' 条件函数: 如果COND成立返回A,否则返回B
```

#### 技术指标函数

```vba
' MACD指标
DIFF:EMA(CLOSE,12)-EMA(CLOSE,26);
DEA:EMA(DIFF,9);
MACD:2*(DIFF-DEA);

' KDJ指标
RSV:=(CLOSE-LLV(LOW,9))/(HHV(HIGH,9)-LLV(LOW,9))*100;
K:SMA(RSV,3,1);
D:SMA(K,3,1);
J:3*K-2*D;

' RSI指标
RSI1:SMA(MAX(CLOSE-REF(CLOSE,1),0),6,1)/SMA(ABS(CLOSE-REF(CLOSE,1)),6,1)*100;
RSI2:SMA(MAX(CLOSE-REF(CLOSE,1),0),12,1)/SMA(ABS(CLOSE-REF(CLOSE,1)),12,1)*100;

' 布林带
MID:MA(CLOSE,20);
UPPER:MID+2*STD(CLOSE,20);
LOWER:MID-2*STD(CLOSE,20);
```

### 3.2 交易函数

#### 3.2.1 PASSORDER - 综合下单函数

```vba
passorder(opType, orderType, accountID, orderCode, prType, price, volume)
```

**参数说明**:

| 参数 | 类型 | 说明 |
|------|------|------|
| opType | int | 操作类型: 23=买入, 24=卖出 |
| orderType | int | 下单方式: 1101=按股数 |
| accountID | string | 资金账号 |
| orderCode | string | 股票代码 |
| prType | int | 报价类型: 5=最新价, 11=限价, 12=市价 |
| price | double | 价格(最新价填-1) |
| volume | double | 数量(股) |

**完整示例**:
```vba
' MACD金叉买入策略
ORDERTYPE:=1101;              ' 按股数下单
ACCOUNTID:='6000000248';      ' 资金账号
ORDERCODE:=STKLABEL();        ' 当前股票
PRICETYPE:=5;                 ' 最新价
VOLUME:=100;                  ' 买入100股

DIFF:=EMA(CLOSE,12)-EMA(CLOSE,26);
DEA:=EMA(DIFF,9);

' 金叉买入
BK:=CROSS(DIFF,DEA);
IF BK THEN BEGIN
    PASSORDER(23,ORDERTYPE,ACCOUNTID,ORDERCODE,PRICETYPE,-1,VOLUME);
    DRAWTEXT(1,H+2,'买入');
END

' 死叉卖出
BP:=CROSS(DEA,DIFF);
IF BP THEN BEGIN
    PASSORDER(24,ORDERTYPE,ACCOUNTID,ORDERCODE,PRICETYPE,-1,VOLUME);
    DRAWTEXT(1,L-2,'卖出');
END
```

#### 3.2.2 持仓查询函数

```vba
' 查询持仓数量
HO:=HOLDING('6000000248','SH','600000',1);  ' 查询600000持仓

' 查询完整持仓信息
XXX:=HOLDINGS('6000000248');
' XXX是positiondetail结构体,包含:
'   volume: 持仓量
'   openprice: 开仓价
'   dloatprofit: 浮动盈亏
'   canusevolume: 可用数量
```

#### 3.2.3 账户监控函数

```vba
' 查询可用资金
CASH:=TACCOUNT(2,'6000000248');  ' 2=普通股票账号

' 查询股票市值
MKTVAL:=MARKETVALUE(2,'6000000248');
```

### 3.3 绘图函数

```vba
' 显示文字
DRAWTEXT(COND,PRICE,TEXT);
DRAWTEXT(CROSS(MA(CLOSE,5),MA(CLOSE,10)),HIGH,'金叉');

' 显示图标
DRAWICON(COND,PRICE,ICONTYPE);
DRAWICON(CROSS(DIFF,DEA),LOW,1);  ' 1=买入图标

' 绘制垂直线
VERTLINE(COND,PRICE1,PRICE2,COLOR,WIDTH,STYLE);
VERTLINE(CROSS(DIFF,DEA),HIGH,LOW,COLORYELLOW,1,VTDOT);

' 绘制水平线
HORIZONTALLINE(COND,PRICE,COLOR,WIDTH,STYLE);

' 绘制柱状线
STICKLINE(COND,PRICE1,PRICE2,WIDTH,EMPTY);
STICKLINE(CLOSE>OPEN,HIGH,LOW,0,0);  ' K线
```

---

## 四、完整回测策略示例

### 4.1 双均线策略(入门级)

**策略逻辑**:
- 快线上穿慢线 → 买入
- 快线下穿慢线 → 卖出

```vba
{双均线策略}
{参数: FAST(5,50,10), SLOW(10,200,20)}

' 参数定义
FAST_PERIOD:=10;   ' 快线周期
SLOW_PERIOD:=20;   ' 慢线周期

' 计算均线
FAST_MA:MA(CLOSE,FAST_PERIOD),COLORRED;
SLOW_MA:MA(CLOSE,SLOW_PERIOD),COLORGREEN;

' 交易参数
ORDERTYPE:=1101;
ACCOUNTID:='testS';
ORDERCODE:=STKLABEL();
PRICETYPE:=5;

' 仓位管理(80%资金)
AVAILABLE:=TACCOUNT(2,ACCOUNTID);
BUY_VOLUME:=INTPART(AVAILABLE*0.8/CLOSE/100)*100;

' 持仓查询
HOLD_VOL:=HOLDING(ACCOUNTID,'SH',STKLABEL(),1);

' 金叉买入
GOLDEN_CROSS:=CROSS(FAST_MA,SLOW_MA);
IF GOLDEN_CROSS AND HOLD_VOL=0 AND BUY_VOLUME>=100 THEN BEGIN
    PASSORDER(23,ORDERTYPE,ACCOUNTID,ORDERCODE,PRICETYPE,-1,BUY_VOLUME);
    DRAWTEXT(1,LOW-0.1,'买入'),COLORRED;
    VERTLINE(1,HIGH,LOW,COLORYELLOW,1,VTDOT);
END

' 死叉卖出
DEATH_CROSS:=CROSS(SLOW_MA,FAST_MA);
IF DEATH_CROSS AND HOLD_VOL>0 THEN BEGIN
    PASSORDER(24,ORDERTYPE,ACCOUNTID,ORDERCODE,PRICETYPE,-1,HOLD_VOL);
    DRAWTEXT(1,HIGH+0.1,'卖出'),COLORGREEN;
    VERTLINE(1,HIGH,LOW,COLORWHITE,1,VTDOT);
END
```

### 4.2 MACD策略(进阶级)

**策略逻辑**:
- 零轴下方金叉 → 买入
- 死叉或止损/止盈 → 卖出

```vba
{MACD策略 - 带止盈止损}
{参数: FAST(5,20,12), SLOW(20,50,26), SIGNAL(5,20,9)}

' MACD参数
FAST:=12;
SLOW:=26;
SIGNAL:=9;

' 计算MACD
DIFF:EMA(CLOSE,12)-EMA(CLOSE,26),COLORYELLOW;
DEA:EMA(DIFF,9),COLORMAGENTA;
MACD:2*(DIFF-DEA),COLORSTICK;

' 交易参数
ORDERTYPE:=1101;
ACCOUNTID:='MACD策略';
ORDERCODE:=STKLABEL();
PRICETYPE:=5;

' 风控参数
STOP_LOSS:=-0.08;     ' 止损-8%
TAKE_PROFIT:=0.15;    ' 止盈+15%

' 持仓状态跟踪
VARIABLE:HOLD_FLAG=0;
VARIABLE:BUY_PRICE=0;

' 持仓查询
HOLD_VOL:=HOLDING(ACCOUNTID,'SH',STKLABEL(),1);

' MACD金叉买入(零轴下方)
GOLDEN_CROSS:=CROSS(DIFF,DEA) AND DIFF<0;
IF GOLDEN_CROSS AND HOLD_VOL=0 THEN BEGIN
    AVAILABLE:=TACCOUNT(2,ACCOUNTID);
    BUY_VOLUME:=INTPART(AVAILABLE*0.8/CLOSE/100)*100;
    IF BUY_VOLUME>=100 THEN BEGIN
        PASSORDER(23,ORDERTYPE,ACCOUNTID,ORDERCODE,PRICETYPE,-1,BUY_VOLUME);
        HOLD_FLAG:=1;
        BUY_PRICE:=CLOSE;
        DRAWTEXT(1,LOW-0.1,'MACD金叉买入'),COLORRED;
    END
END

' 止损逻辑
IF HOLD_FLAG=1 AND HOLD_VOL>0 THEN BEGIN
    PROFIT_RATIO:=(CLOSE-BUY_PRICE)/BUY_PRICE;
    IF PROFIT_RATIO<=STOP_LOSS THEN BEGIN
        PASSORDER(24,ORDERTYPE,ACCOUNTID,ORDERCODE,PRICETYPE,-1,HOLD_VOL);
        HOLD_FLAG:=0;
        BUY_PRICE:=0;
        DRAWTEXT(1,LOW-0.1,'止损卖出'),COLORGREEN;
    END
END

' 止盈逻辑
IF HOLD_FLAG=1 AND HOLD_VOL>0 THEN BEGIN
    PROFIT_RATIO:=(CLOSE-BUY_PRICE)/BUY_PRICE;
    IF PROFIT_RATIO>=TAKE_PROFIT THEN BEGIN
        PASSORDER(24,ORDERTYPE,ACCOUNTID,ORDERCODE,PRICETYPE,-1,HOLD_VOL);
        HOLD_FLAG:=0;
        BUY_PRICE:=0;
        DRAWTEXT(1,HIGH+0.1,'止盈卖出'),COLORBLUE;
    END
END

' MACD死叉卖出
DEATH_CROSS:=CROSS(DEA,DIFF);
IF DEATH_CROSS AND HOLD_VOL>0 THEN BEGIN
    PASSORDER(24,ORDERTYPE,ACCOUNTID,ORDERCODE,PRICETYPE,-1,HOLD_VOL);
    HOLD_FLAG:=0;
    BUY_PRICE:=0;
    DRAWTEXT(1,HIGH+0.1,'MACD死叉卖出'),COLORGREEN;
END
```

### 4.3 KDJ+RSI组合策略(高级版)

**策略逻辑**:
- KDJ金叉 + RSI超卖区 → 买入
- KDJ死叉 + RSI超买区 → 卖出

```vba
{KDJ+RSI组合策略}
{参数: N(2,20,9), M1(2,10,3), M2(2,10,3), RSI_PERIOD(5,20,14)}

' KDJ参数
N:=9;
M1:=3;
M2:=3;

' 计算KDJ
RSV:=(CLOSE-LLV(LOW,N))/(HHV(HIGH,N)-LLV(LOW,N))*100;
K:SMA(RSV,M1,1),COLORYELLOW;
D:SMA(K,M2,1),COLORMAGENTA;
J:3*K-2*D,COLORRED;

' RSI指标
RSI_PERIOD:=14;
RSI:SMA(MAX(CLOSE-REF(CLOSE,1),0),RSI_PERIOD,1)/SMA(ABS(CLOSE-REF(CLOSE,1)),RSI_PERIOD,1)*100,COLORGREEN;

' 交易参数
ORDERTYPE:=1101;
ACCOUNTID:='KDJ_RSI策略';
ORDERCODE:=STKLABEL();
PRICETYPE:=5;

' 持仓查询
HOLD_VOL:=HOLDING(ACCOUNTID,'SH',STKLABEL(),1);

' 买入条件: KDJ金叉 + RSI<30(超卖)
KDJ_GOLDEN:=CROSS(K,D) AND K<30;
BUY_SIGNAL:=KDJ_GOLDEN AND RSI<30;

IF BUY_SIGNAL AND HOLD_VOL=0 THEN BEGIN
    AVAILABLE:=TACCOUNT(2,ACCOUNTID);
    BUY_VOLUME:=INTPART(AVAILABLE*0.7/CLOSE/100)*100;
    IF BUY_VOLUME>=100 THEN BEGIN
        PASSORDER(23,ORDERTYPE,ACCOUNTID,ORDERCODE,PRICETYPE,-1,BUY_VOLUME);
        DRAWTEXT(1,LOW-0.1,'KDJ+RSI买入'),COLORRED;
        VERTLINE(1,HIGH,LOW,COLORYELLOW,1,VTDOT);
    END
END

' 卖出条件: KDJ死叉 + RSI>70(超买)
KDJ_DEATH:=CROSS(D,K) AND K>70;
SELL_SIGNAL:=KDJ_DEATH AND RSI>70;

IF SELL_SIGNAL AND HOLD_VOL>0 THEN BEGIN
    PASSORDER(24,ORDERTYPE,ACCOUNTID,ORDERCODE,PRICETYPE,-1,HOLD_VOL);
    DRAWTEXT(1,HIGH+0.1,'KDJ+RSI卖出'),COLORGREEN;
    VERTLINE(1,HIGH,LOW,COLORWHITE,1,VTDOT);
END

' 辅助显示
DRAWTEXT(BUY_SIGNAL,LOW*0.98,'买'),COLORRED;
DRAWTEXT(SELL_SIGNAL,HIGH*1.02,'卖'),COLORGREEN;
```

### 4.4 布林带突破策略

**策略逻辑**:
- 价格突破上轨 → 买入(趋势跟踪)
- 价格跌破下轨 → 卖出
- 价格回归中轨 → 平仓

```vba
{布林带突破策略}
{参数: N(10,50,20), K(1,3,2)}

' 布林带参数
N:=20;
K:=2;

' 计算布林带
MID:MA(CLOSE,N),COLORWHITE;
UPPER:MID+K*STD(CLOSE,N),COLORRED;
LOWER:MID-K*STD(CLOSE,N),COLORGREEN;

' 交易参数
ORDERTYPE:=1101;
ACCOUNTID:='BOLL策略';
ORDERCODE:=STKLABEL();
PRICETYPE:=5;

' 持仓查询
HOLD_VOL:=HOLDING(ACCOUNTID,'SH',STKLABEL(),1);

' 突破上轨买入
BREAK_UPPER:=CROSS(CLOSE,UPPER);
IF BREAK_UPPER AND HOLD_VOL=0 THEN BEGIN
    AVAILABLE:=TACCOUNT(2,ACCOUNTID);
    BUY_VOLUME:=INTPART(AVAILABLE*0.6/CLOSE/100)*100;
    IF BUY_VOLUME>=100 THEN BEGIN
        PASSORDER(23,ORDERTYPE,ACCOUNTID,ORDERCODE,PRICETYPE,-1,BUY_VOLUME);
        DRAWTEXT(1,LOWER-0.1,'突破买入'),COLORRED;
    END
END

' 跌破下轨卖出
BREAK_LOWER:=CROSS(LOWER,CLOSE);
IF BREAK_LOWER AND HOLD_VOL>0 THEN BEGIN
    PASSORDER(24,ORDERTYPE,ACCOUNTID,ORDERCODE,PRICETYPE,-1,HOLD_VOL);
    DRAWTEXT(1,HIGH+0.1,'跌破卖出'),COLORGREEN;
END

' 回归中轨平仓
RETURN_MID:=CROSS(MID,CLOSE);
IF RETURN_MID AND HOLD_VOL>0 THEN BEGIN
    PASSORDER(24,ORDERTYPE,ACCOUNTID,ORDERCODE,PRICETYPE,-1,HOLD_VOL);
    DRAWTEXT(1,HIGH+0.1,'回归平仓'),COLORBLUE;
END
```

---

## 五、回测运行机制

### 5.1 VBA模型的两种运行模式

#### 5.1.1 序列模式(默认)

- 所有公式从左到右一次性计算完成
- 每个变量都是完整的时间序列数组
- 适合技术指标计算和图形绘制

```vba
' 序列模式下,MA5是一个包含所有均线值的数组
MA5:MA(CLOSE,5);
' 可以直接引用任意位置的元素
MA5_YESTERDAY:=REF(MA5,1);
```

#### 5.1.2 逐K线模式(回测用)

- 从左向右逐根K线触发计算
- 每根K线执行一次策略逻辑
- 适合策略回测和实盘交易

```vba
' 逐K线模式下,每次只计算当前K线的值
' 使用REF引用历史数据
MA5:=MA(CLOSE,5);
CURRENT_MA5:=MA5;           ' 当前K线的MA5
PREV_MA5:=REF(MA5,1);       ' 上一根K线的MA5
```

### 5.2 回测数据准备

#### 5.2.1 下载历史数据

**界面操作**:
1. 点击左上角"操作" → "数据管理"
2. 选择回测周期(日线/分钟线)
3. 选择板块数据(沪深A股)
4. 时间范围选择"全部"
5. 下载完整历史行情

**注意事项**:
- 首次使用必须下载完整数据
- 定期更新数据保证回测准确性
- 跨周期引用需下载对应周期数据

### 5.3 回测参数设置

在QMT界面运行VBA回测时:

**基础设置**:
- **周期选择**: 与策略设计周期一致(日线/分钟线)
- **主图代码**: 选择要回测的股票
- **起始日期**: 建议至少3年以上
- **结束日期**: 最近一个交易日
- **初始资金**: 根据策略设置(如100万)
- **手续费**: 股票建议万分之三
- **滑点**: 建议1-2个tick

---

## 六、高级回测技巧

### 6.1 参数优化回测

通过调整参数寻找最优组合:

```vba
{参数优化模板}
{参数: FAST(5,30,10), SLOW(20,100,30)}

' 待优化参数(每次回测手动修改)
FAST_PERIOD:=10;   ' 尝试: 5, 10, 15, 20
SLOW_PERIOD:=30;   ' 尝试: 20, 30, 60

' 策略逻辑
FAST_MA:=MA(CLOSE,FAST_PERIOD);
SLOW_MA:=MA(CLOSE,SLOW_PERIOD);

BUY_SIGNAL:=CROSS(FAST_MA,SLOW_MA);
SELL_SIGNAL:=CROSS(SLOW_MA,FAST_MA);

' ... 交易逻辑 ...
```

**批量测试流程**:
1. 手动修改参数组合
2. 逐个运行回测
3. 记录每次回测结果(收益率、最大回撤、胜率等)
4. 对比分析找出最优参数

### 6.2 多周期共振策略

利用跨周期引用实现多周期信号共振:

```vba
{多周期共振策略 - 日线为主,周线过滤}

' 日线指标
DIF:EMA(CLOSE,12)-EMA(CLOSE,26);
DEA:EMA(DIFF,9);
DAY_SIGNAL:=CROSS(DIF,DEA);

' 周线指标(跨周期引用)
WEEKLY_DIF:"MACD.DIFF#WEEK"(12,26,9);
WEEKLY_DEA:"MACD.DEA#WEEK"(12,26,9);
WEEK_TREND:=WEEKLY_DIF>WEEKLY_DEA;  ' 周线趋势向上

' 共振买入(日线金叉 + 周线趋势向上)
BUY_SIGNAL:=DAY_SIGNAL AND WEEK_TREND;

' 交易逻辑
IF BUY_SIGNAL THEN BEGIN
    ' 买入...
END
```

### 6.3 动态仓位管理

根据账户净值和波动率调整仓位:

```vba
{动态仓位管理}

' 查询账户信息
AVAILABLE:=TACCOUNT(2,ACCOUNTID);
TOTAL_ASSET:=AVAILABLE+MARKETVALUE(2,ACCOUNTID);
INITIAL_CAPITAL:=1000000;  ' 初始资金100万

' 计算净值
NET_VALUE:=TOTAL_ASSET/INITIAL_CAPITAL;

' 基础仓位50%
BASE_POSITION:=0.5;

' 净值>1.1,提高到70%
IF NET_VALUE>1.1 THEN POSITION:=0.7
' 净值<0.9,降低到30%
ELSE IF NET_VALUE<0.9 THEN POSITION:=0.3
' 其他情况保持基础仓位
ELSE POSITION:=BASE_POSITION;

' 计算买入数量
BUY_VOLUME:=INTPART(AVAILABLE*POSITION/CLOSE/100)*100;
```

### 6.4 止盈止损策略

#### 6.4.1 固定比例止损

```vba
' 止损-8%,止盈+15%
STOP_LOSS:=-0.08;
TAKE_PROFIT:=0.15;

' 持仓成本
VARIABLE:BUY_PRICE=0;

' 计算盈亏比例
IF BUY_PRICE>0 THEN
    PROFIT_RATIO:=(CLOSE-BUY_PRICE)/BUY_PRICE
ELSE
    PROFIT_RATIO:=0;

' 止损卖出
IF PROFIT_RATIO<=STOP_LOSS THEN BEGIN
    ' 卖出逻辑
    BUY_PRICE:=0;
END

' 止盈卖出
IF PROFIT_RATIO>=TAKE_PROFIT THEN BEGIN
    ' 卖出逻辑
    BUY_PRICE:=0;
END
```

#### 6.4.2 移动止盈(追踪止损)

```vba
' 移动止盈策略
VARIABLE:HIGHEST_PRICE=0;
VARIABLE:TRAIL_STOP=0;

' 更新最高价
IF HOLD_VOL>0 AND CLOSE>HIGHEST_PRICE THEN
    HIGHEST_PRICE:=CLOSE;

' 计算追踪止损价(从最高点回撤8%)
TRAIL_STOP:=HIGHEST_PRICE*(1-0.08);

' 跌破追踪止损价卖出
IF CLOSE<TRAIL_STOP AND HOLD_VOL>0 THEN BEGIN
    PASSORDER(24,ORDERTYPE,ACCOUNTID,ORDERCODE,PRICETYPE,-1,HOLD_VOL);
    HIGHEST_PRICE:=0;
    TRAIL_STOP:=0;
END
```

### 6.5 组合策略(多策略投票)

```vba
{多策略组合投票}

' 策略1: 均线交叉
FAST_MA:=MA(CLOSE,10);
SLOW_MA:=MA(CLOSE,20);
STRATEGY1:=IF(CROSS(FAST_MA,SLOW_MA),1,IF(CROSS(SLOW_MA,FAST_MA),-1,0));

' 策略2: MACD
DIFF:=EMA(CLOSE,12)-EMA(CLOSE,26);
DEA:=EMA(DIFF,9);
STRATEGY2:=IF(CROSS(DIFF,DEA),1,IF(CROSS(DEA,DIFF),-1,0));

' 策略3: RSI
RSI:=SMA(MAX(CLOSE-REF(CLOSE,1),0),14,1)/SMA(ABS(CLOSE-REF(CLOSE,1)),14,1)*100;
STRATEGY3:=IF(RSI<30,1,IF(RSI>70,-1,0));

' 加权投票
FINAL_SIGNAL:=STRATEGY1*0.4 + STRATEGY2*0.3 + STRATEGY3*0.3;

' 买入信号(投票>0.5)
IF FINAL_SIGNAL>0.5 THEN BEGIN
    ' 买入...
END

' 卖出信号(投票<-0.5)
IF FINAL_SIGNAL<-0.5 THEN BEGIN
    ' 卖出...
END
```

---

## 七、常见问题与优化

### 7.1 常见错误

#### 错误1: 数据不完整

**问题**: 回测时取不到数据或数据缺失

**解决**:
- 确保已下载完整历史数据
- 跨周期引用需下载对应周期数据
- 使用"数据管理"功能补充数据

#### 错误2: 语法错误

**常见语法错误**:
```vba
' 错误: 赋值符号使用错误
MA5=MA(CLOSE,5);          ' 应该是: MA5:=MA(CLOSE,5);

' 错误: 缺少分号
MA5:=MA(CLOSE,5)          ' 缺少结尾分号

' 错误: 括号不匹配
MA5:=MA(CLOSE,5;          ' 应该是: MA5:=MA(CLOSE,5);
```

#### 错误3: 逻辑错误

```vba
' 错误: 逻辑运算符优先级
IF A OR B AND C THEN  ' 实际执行: A OR (B AND C)
' 正确: 使用括号明确优先级
IF (A OR B) AND C THEN
```

### 7.2 性能优化

#### 优化1: 减少重复计算

```vba
' 错误: 重复计算
IF MA(CLOSE,5)>MA(CLOSE,10) THEN
    DIFF:=MA(CLOSE,5)-MA(CLOSE,10);

' 正确: 缓存计算结果
FAST_MA:=MA(CLOSE,5);
SLOW_MA:=MA(CLOSE,10);
IF FAST_MA>SLOW_MA THEN
    DIFF:=FAST_MA-SLOW_MA;
```

#### 优化2: 避免不必要的绘图

```vba
' 错误: 每根K线都绘图
DRAWTEXT(1,HIGH,'信号');  ' 每根K线都显示

' 正确: 只在信号出现时绘图
DRAWTEXT(BUY_SIGNAL,HIGH,'买入');  ' 只在买入时显示
```

#### 优化3: 使用中间变量简化公式

```vba
' 错误: 公式冗长
IF CROSS(MA(CLOSE,5),MA(CLOSE,10)) AND MA(CLOSE,5)>MA(CLOSE,20) THEN

' 正确: 使用中间变量
FAST_MA:=MA(CLOSE,5);
SLOW_MA:=MA(CLOSE,10);
LONG_MA:=MA(CLOSE,20);
IF CROSS(FAST_MA,SLOW_MA) AND FAST_MA>LONG_MA THEN
```

### 7.3 策略验证清单

回测前检查:

- [ ] 历史数据已完整下载
- [ ] 参数设置合理(周期、资金、手续费)
- [ ] 回测时间跨度足够长(至少3年)
- [ ] 覆盖牛熊市不同市场环境
- [ ] 交易频率合理(不过度交易)
- [ ] 胜率>40%,盈亏比>1.5
- [ ] 最大回撤<20%(保守策略)
- [ ] 参数在一定范围内稳定(不过度拟合)

### 7.4 从回测到实盘

**回测版本**:
```vba
' 使用模拟账号测试
ACCOUNTID:='testS';
```

**实盘版本**:
```vba
' 切换到真实账号
ACCOUNTID:='6000000248';

' 增加风控检查
AVAILABLE:=TACCOUNT(2,ACCOUNTID);
IF AVAILABLE<10000 THEN BEGIN
    ' 资金不足,停止交易
    EXIT;
END
```

---

## 八、VBA与Python混合编程

### 8.1 为什么需要混合编程

**VBA擅长**:
- 技术指标计算(MACD、KDJ、布林带等)
- 序列数据向量化运算
- 快速选股和筛选

**Python擅长**:
- 复杂策略逻辑控制
- 数据分析和机器学习
- 多品种组合管理
- 回测报告生成

### 8.2 Python调用VBA模型

#### 方式1: call_formula函数

```python
def handlebar(ContextInfo):
    # 调用VBA模型获取指标值
    modelRet = call_formula(
        'MACD策略',          # VBA模型名称
        '000300.SH',        # 股票代码
        '1d',               # 日线周期
        '20240101',         # 起始时间
        '20240201',         # 截止时间
        -1,                 # 运行所有bar
        "none",             # 不复权
        {'a': 100}          # 模型参数
    )
    
    # 返回结果
    outputs = modelRet['outputs']
    dif_values = outputs['DIF']
    dea_values = outputs['DEA']
    
    # Python负责交易逻辑
    if dif_values[-1] > dea_values[-1]:
        # 买入...
        pass
```

#### 方式2: subscribe_formula订阅

```python
def callback(data):
    # 接收VBA模型实时推送
    print(f"时间: {data['timelist']}")
    print(f"指标值: {data['outputs']}")

def init(ContextInfo):
    # 订阅VBA模型
    subID = subscribe_formula(
        'MACD策略',
        '000300.SH',
        '1d',
        callback=callback
    )
    ContextInfo.subID = subID

def stop(ContextInfo):
    # 反订阅
    unsubscribe_formula(ContextInfo.subID)
```

#### 方式3: 批量调用(高效选股)

```python
def handlebar(ContextInfo):
    # 批量运行VBA选股模型
    stock_pool = ['600000.SH', '000001.SZ', '600036.SH']
    
    results = call_formula_batch(
        ['MACD策略', 'KDJ策略'],  # 多个VBA模型
        stock_pool,               # 多只股票
        '1d',
        count=1
    )
    
    # 分析选股结果
    for ret in results:
        outputs = ret['result']['outputs']
        if outputs.get('BUY_SIGNAL', [0])[-1] == 1:
            print(f"{ret['stock']} 出现买入信号")
            # 执行交易...
```

### 8.3 典型应用场景

#### 场景1: VBA计算指标 + Python交易逻辑

```python
# Python策略
def handlebar(ContextInfo):
    # 调用VBA计算的MACD指标
    macd_ret = call_formula(
        'MACD模型',
        ContextInfo.stock,
        '1d',
        count=100
    )
    
    dif = macd_ret['outputs']['DIF']
    dea = macd_ret['outputs']['DEA']
    
    # Python负责交易逻辑
    if dif[-1] > dea[-1] and dif[-2] <= dea[-2]:
        # 金叉买入
        passorder(23, 1101, account, ContextInfo.stock, 
                  5, -1, 100, "MACD策略", 0, "", ContextInfo)
```

#### 场景2: VBA批量选股 + Python执行交易

```python
def handlebar(ContextInfo):
    # VBA批量选股
    stock_pool = get_stock_list_in_sector('沪深A股')
    
    results = call_formula_batch(
        ['涨停股模型', '放量模型'],
        stock_pool,
        '1d',
        count=1
    )
    
    # Python分析结果并执行
    target_stocks = []
    for ret in results:
        if ret['result']['outputs']['ZT'][-1] == 1:
            target_stocks.append(ret['stock'])
    
    # 买入目标股票
    for stock in target_stocks:
        order_target_percent(stock, 0.1, 'LATEST', ContextInfo, 'test')
```

### 8.4 性能对比

| 场景 | 纯Python | VBA | Python+VBA混合 |
|------|---------|-----|---------------|
| 单只股票指标计算 | 慢 | 快 | 快 |
| 100只股票批量计算 | 很慢 | 很快 | 很快 |
| 复杂策略逻辑 | 灵活 | 受限 | 灵活 |
| 代码可维护性 | 高 | 中 | 中高 |

**推荐方案**:
- 简单指标计算: 直接用Python
- 复杂技术指标: VBA计算 + Python调用
- 批量选股: VBA批量计算 + Python分析
- 机器学习策略: Python为主,VBA辅助

---

## 附录: 常用技术指标VBA实现

### A.1 MACD指标

```vba
DIFF:EMA(CLOSE,12)-EMA(CLOSE,26),COLORRED;
DEA:EMA(DIFF,9),COLORGREEN;
MACD:2*(DIFF-DEA),COLORSTICK;
```

### A.2 KDJ指标

```vba
RSV:=(CLOSE-LLV(LOW,9))/(HHV(HIGH,9)-LLV(LOW,9))*100;
K:SMA(RSV,3,1),COLORRED;
D:SMA(K,3,1),COLORGREEN;
J:3*K-2*D,COLORYELLOW;
```

### A.3 RSI指标

```vba
RSI1:SMA(MAX(CLOSE-REF(CLOSE,1),0),6,1)/SMA(ABS(CLOSE-REF(CLOSE,1)),6,1)*100,COLORRED;
RSI2:SMA(MAX(CLOSE-REF(CLOSE,1),0),12,1)/SMA(ABS(CLOSE-REF(CLOSE,1)),12,1)*100,COLORYELLOW;
RSI3:SMA(MAX(CLOSE-REF(CLOSE,1),0),24,1)/SMA(ABS(CLOSE-REF(CLOSE,1)),24,1)*100,COLORGREEN;
```

### A.4 布林带

```vba
MID:MA(CLOSE,20),COLORWHITE;
UPPER:MID+2*STD(CLOSE,20),COLORRED;
LOWER:MID-2*STD(CLOSE,20),COLORGREEN;
```

### A.5 均线系统

```vba
MA5:MA(CLOSE,5),COLORWHITE;
MA10:MA(CLOSE,10),COLORYELLOW;
MA20:MA(CLOSE,20),COLORMAGENTA;
MA60:MA(CLOSE,60),COLORGREEN;
```

### A.6 成交量指标

```vba
VOL:VOL,VOLSTICK;
MAVOL1:MA(VOL,5),COLORWHITE;
MAVOL2:MA(VOL,10),COLORYELLOW;
```

---

## 参考资源

- QMT官方文档: https://dict.thinktrader.net/VBA/
- 迅投QMT社区: https://www.xuntou.net/
- VBA指标函数编写教程: 本项目VBA目录

---

**文档版本**: v1.0  
**更新时间**: 2026-04-03  
**适用版本**: QMT VBA模型系统  
**编写说明**: 本教程基于QMT官方文档整理,结合实战经验编写

# QMT内置Python回测策略完整指南

> 本文档详细介绍如何使用QMT内置Python创建具有详细回测报告和图表展示的回测策略

## 目录

- [一、回测基础概念](#一回测基础概念)
- [二、回测策略编写规范](#二回测策略编写规范)
- [三、核心API函数](#三核心api函数)
- [四、完整回测策略示例](#四完整回测策略示例)
- [五、回测报告生成](#五回测报告生成)
- [六、图表展示技巧](#六图表展示技巧)
- [七、高级回测技巧](#七高级回测技巧)
- [八、常见问题与优化](#八常见问题与优化)

---

## 一、回测基础概念

### 1.1 什么是回测

回测是指在历史K线上,自左向右逐根遍历K线,以模拟的资金账号记录每日的买卖信号、持仓盈亏,最终展示策略在历史上的净值走势结果。

### 1.2 回测运行机制

QMT回测采用**逐K线驱动**机制:
- 历史K线从左向右每根触发一次`handlebar`函数调用
- 使用`get_market_data_ex`读取本地行情数据(需设置`subscribe=False`)
- 撮合规则:指定交易价格在当前K线高低点间的,按指定价格撮合;超过高低点的,按当前K线收盘价撮合

### 1.3 回测准备

#### 1.3.1 下载历史数据

**方式一:界面操作**
1. 点击左上角`操作` → `数据管理`
2. 选择回测周期(如`日线`)
3. 选择板块数据(如`沪深A股板块`)
4. 时间范围选择`全部`
5. 下载完整历史行情

**方式二:代码下载**
```python
#coding:gbk
def init(C):
    # 下载指定股票日线数据
    download_history_data("000001.SZ", "1d", "20200101", "")
    # 增量下载
    download_history_data("000001.SZ", "1d", "", "", incrementally=True)
```

#### 1.3.2 设置定时更新

1. 点击客户端右下角`行情`按钮
2. 在`批量下载`界面选择需要每天更新的数据
3. 勾选`定时下载`选项
4. 设置每天自动下载时间

---

## 二、回测策略编写规范

### 2.1 代码规范

```python
#coding:gbk  # 必须在第一行统一编码格式

# 缩进统一使用4个空格或Tab(二选一,不可混用)
import pandas as pd
import numpy as np
import talib

def init(C):
    """初始化函数,仅执行一次"""
    pass

def handlebar(C):
    """逐K线执行函数,每根K线触发一次"""
    pass
```

### 2.2 生命周期函数

| 函数 | 执行时机 | 用途 |
|------|----------|------|
| `init(C)` | 策略启动时 | 初始化参数、变量、标的 |
| `handlebar(C)` | 每根K线 | 核心策略逻辑、交易判断 |
| `after_init(C)` | init执行后 | 补充初始化(可选) |
| `stop(C)` | 策略结束时 | 清理资源、输出报告(可选) |

### 2.3 ContextInfo对象核心属性

```python
C.stockcode      # 股票代码(不含市场)
C.market         # 市场代码(SH/SZ等)
C.stock          # 完整代码(需拼接: C.stockcode + '.' + C.market)
C.period         # 当前周期(1d/1m/5m等)
C.barpos         # 当前K线位置索引
C.is_last_bar()  # 是否为最后一根K线(实盘用)
```

---

## 三、核心API函数

### 3.1 行情数据获取

#### ContextInfo.get_market_data_ex

```python
data = C.get_market_data_ex(
    fields=['open', 'high', 'low', 'close', 'volume', 'amount'],  # 字段列表
    stock_code=['000001.SZ'],      # 股票列表
    period='1d',                   # 周期
    start_time='20200101',         # 起始时间
    end_time='20231231',           # 结束时间
    count=-1,                      # 数据条数(-1为全部)
    dividend_type='front',         # 复权方式: none/front/back
    fill_data=True,                # 是否填充数据
    subscribe=False                # 回测必须设为False!
)
```

**返回值**: `dict`, key为股票代码, value为`pd.DataFrame`

**常用字段**:
- `open`: 开盘价
- `high`: 最高价
- `low`: 最低价
- `close`: 收盘价
- `volume`: 成交量
- `amount`: 成交额
- `preClose`: 前收盘价

### 3.2 交易下单函数

#### passorder - 综合下单函数(推荐)

```python
passorder(
    opType,          # 操作类型: 23=买入, 24=卖出
    orderType,       # 下单方式: 1101=按股数
    accountid,       # 资金账号(回测可填任意字符串)
    orderCode,       # 股票代码: '000001.SZ'
    prType,          # 报价类型: 5=最新价, 11=限价
    price,           # 价格(最新价时填-1或0)
    volume,          # 数量(股)
    strategyName,    # 策略名称(可选)
    quickTrade,      # 快速交易: 0=逐K线(回测), 2=立即下单
    userOrderId,     # 投资备注(可选)
    C                # ContextInfo对象
)
```

**opType操作类型**:
- `23`: 股票买入
- `24`: 股票卖出
- `0`: 期货买入开仓
- `1`: 期货卖出平仓

**prType报价类型**:
- `5`: 最新价
- `11`: 限价(需配合price参数)
- `12`: 市价

**示例**:
```python
# 以最新价买入1000股
passorder(23, 1101, C.accountid, '000001.SZ', 5, -1, 1000, '均线策略', 0, '金叉买入', C)

# 以指定价卖出全部持仓
passorder(24, 1101, C.accountid, '000001.SZ', 11, 15.5, 1000, '均线策略', 0, '死叉卖出', C)
```

#### 便捷下单函数(仅回测可用)

```python
# 指定股数交易
order_shares('000001.SZ', 1000, 'LATEST', C, 'test_account')
order_shares('000001.SZ', -1000, 'COMPETE', C, 'test_account')  # 负数卖出

# 指定价值交易(元)
order_value('000001.SZ', 100000, 'LATEST', C, 'test_account')

# 指定手数交易(1手=100股)
order_lots('000001.SZ', 10, 'LATEST', C, 'test_account')

# 指定目标持仓比例
order_target_percent('000001.SZ', 0.5, 'LATEST', C, 'test_account')  # 50%仓位

# 指定目标持仓价值
order_target_value('000001.SZ', 200000, 'LATEST', C, 'test_account')
```

### 3.3 交易查询函数

```python
# 查询账户资金
account = get_trade_detail_data('test', 'stock', 'account')
available_cash = account[0].m_dAvailable  # 可用资金
total_asset = account[0].m_dBalance       # 总资产

# 查询持仓
holdings = get_trade_detail_data('test', 'stock', 'position')
for h in holdings:
    print(f"股票: {h.m_strInstrumentID}.{h.m_strExchangeID}")
    print(f"持仓量: {h.m_nVolume}")
    print(f"可用量: {h.m_nCanUseVolume}")
    print(f"成本价: {h.m_dOpenPrice}")
    print(f"浮盈: {h.m_dPositionProfit}")

# 查询委托
orders = get_trade_detail_data('test', 'stock', 'order')

# 查询成交
deals = get_trade_detail_data('test', 'stock', 'deal')
```

### 3.4 绘图函数

```python
# 在副图绘制指标线
C.paint('指标名', 数值, -1, 0, 'red', 'noaxis')
# line_style: 0=曲线, 42=柱状线
# color: red/green/blue/yellow/white等
# limit: 'noaxis'=不影响坐标, 'nodraw'=不画线

# 在图形上显示文字
C.draw_text(1, 10, '买入信号')  # 条件, 位置, 文字

# 在图形上显示数字
C.draw_number(1, 100, 15.5, 2)  # 条件, 位置, 数字, 小数位数

# 绘制垂直线
C.draw_vertline(1, 10, 20, 'cyan')  # 条件, 数值1, 数值2, 颜色

# 绘制图标
C.draw_icon(1, 10, 1)  # 条件, 位置, 类型: 0=矩形, 1=椭圆
```

---

## 四、完整回测策略示例

### 4.1 双均线策略(基础版)

```python
#coding:gbk
import pandas as pd
import numpy as np

def init(C):
    # 设置测试标的
    C.stock = C.stockcode + '.' + C.market
    
    # 策略参数
    C.fast_period = 10   # 快线周期
    C.slow_period = 20   # 慢线周期
    
    # 回测账号
    C.accountid = "testS"
    
    # 记录交易日志
    C.trade_log = []

def handlebar(C):
    # 当前K线日期
    bar_date = timetag_to_datetime(C.get_bar_timetag(C.barpos), '%Y%m%d%H%M%S')
    
    # 获取历史数据(回测必须subscribe=False)
    local_data = C.get_market_data_ex(
        ['close'], 
        [C.stock], 
        end_time=bar_date, 
        period=C.period, 
        count=max(C.fast_period, C.slow_period) + 5, 
        subscribe=False
    )
    
    if C.stock not in local_data or len(local_data[C.stock]) < max(C.fast_period, C.slow_period):
        return
    
    close_list = list(local_data[C.stock]['close'])
    
    # 计算均线
    fast_ma = np.mean(close_list[-C.fast_period:])
    slow_ma = np.mean(close_list[-C.slow_period:])
    
    # 计算前一期均线(判断交叉)
    pre_fast_ma = np.mean(close_list[-C.fast_period-1:-1])
    pre_slow_ma = np.mean(close_list[-C.slow_period-1:-1])
    
    # 查询账户和持仓
    account = get_trade_detail_data('test', 'stock', 'account')
    if len(account) == 0:
        return
    account = account[0]
    available_cash = account.m_dAvailable
    
    holdings = get_trade_detail_data('test', 'stock', 'position')
    holdings_dict = {h.m_strInstrumentID + '.' + h.m_strExchangeID: h.m_nCanUseVolume for h in holdings}
    holding_vol = holdings_dict.get(C.stock, 0)
    
    current_price = close_list[-1]
    
    # 金叉买入(快线上穿慢线)
    if holding_vol == 0 and pre_fast_ma <= pre_slow_ma and fast_ma > slow_ma:
        # 计算买入数量(8成仓位,向下取整到100的倍数)
        buy_vol = int(available_cash * 0.8 / current_price / 100) * 100
        if buy_vol >= 100:
            passorder(23, 1101, C.accountid, C.stock, 5, -1, buy_vol, '双均线', 0, '金叉买入', C)
            C.draw_text(1, 1, '买')
            C.trade_log.append(f"{bar_date} 金叉买入 {buy_vol}股 @ {current_price}")
            print(f"{bar_date} 金叉买入 {buy_vol}股 @ {current_price}")
    
    # 死叉卖出(快线下穿慢线)
    elif holding_vol > 0 and pre_fast_ma >= pre_slow_ma and fast_ma < slow_ma:
        passorder(24, 1101, C.accountid, C.stock, 5, -1, holding_vol, '双均线', 0, '死叉卖出', C)
        C.draw_text(1, 1, '卖')
        C.trade_log.append(f"{bar_date} 死叉卖出 {holding_vol}股 @ {current_price}")
        print(f"{bar_date} 死叉卖出 {holding_vol}股 @ {current_price}")
    
    # 绘制均线值到副图
    C.paint('快线', fast_ma, -1, 0, 'yellow', 'noaxis')
    C.paint('慢线', slow_ma, -1, 0, 'magenta', 'noaxis')

def stop(C):
    # 策略结束时输出交易日志
    print("\n========== 交易记录 ==========")
    for log in C.trade_log:
        print(log)
```

### 4.2 MACD策略(进阶版)

```python
#coding:gbk
import pandas as pd
import numpy as np
import talib

def init(C):
    C.stock = C.stockcode + '.' + C.market
    C.accountid = "MACD策略"
    
    # MACD参数
    C.fastperiod = 12
    C.slowperiod = 26
    C.signalperiod = 9
    
    # 风控参数
    C.stop_loss = -0.08    # 止损-8%
    C.take_profit = 0.15   # 止盈+15%
    C.max_position = 0.8   # 最大仓位80%
    
    # 统计变量
    C.trade_count = 0
    C.win_count = 0
    C.total_profit = 0
    C.buy_price = 0

def handlebar(C):
    bar_date = timetag_to_datetime(C.get_bar_timetag(C.barpos), '%Y%m%d')
    
    # 获取数据
    data = C.get_market_data_ex(
        ['close', 'high', 'low', 'volume'],
        [C.stock],
        end_time=bar_date,
        period='1d',
        count=C.slowperiod + C.signalperiod + 30,
        subscribe=False
    )
    
    if C.stock not in data or len(data[C.stock]) < C.slowperiod + C.signalperiod:
        return
    
    df = data[C.stock]
    close = df['close'].values
    high = df['high'].values
    low = df['low'].values
    
    # 计算MACD
    DIF, DEA, MACD = talib.MACD(
        close, 
        fastperiod=C.fastperiod,
        slowperiod=C.slowperiod,
        signalperiod=C.signalperiod
    )
    
    # 查询持仓
    holdings = get_trade_detail_data('test', 'stock', 'position')
    holdings_dict = {h.m_strInstrumentID + '.' + h.m_strExchangeID: h for h in holdings}
    
    current_price = close[-1]
    
    # 止盈止损逻辑
    if C.stock in holdings_dict and C.buy_price > 0:
        profit_ratio = (current_price - C.buy_price) / C.buy_price
        
        # 止损
        if profit_ratio <= C.stop_loss:
            vol = holdings_dict[C.stock].m_nCanUseVolume
            if vol > 0:
                passorder(24, 1101, C.accountid, C.stock, 5, -1, vol, 'MACD策略', 0, '止损卖出', C)
                C.draw_text(1, 1, '止损')
                print(f"{bar_date} 止损卖出 @ {current_price} 亏损{profit_ratio*100:.2f}%")
                C.buy_price = 0
                return
        
        # 止盈
        if profit_ratio >= C.take_profit:
            vol = holdings_dict[C.stock].m_nCanUseVolume
            if vol > 0:
                passorder(24, 1101, C.accountid, C.stock, 5, -1, vol, 'MACD策略', 0, '止盈卖出', C)
                C.draw_text(1, 1, '止盈')
                print(f"{bar_date} 止盈卖出 @ {current_price} 盈利{profit_ratio*100:.2f}%")
                C.win_count += 1
                C.total_profit += profit_ratio
                C.buy_price = 0
                return
    
    # MACD金叉买入
    if C.stock not in holdings_dict and len(DIF) >= 2:
        if DIF[-2] <= DEA[-2] and DIF[-1] > DEA[-1] and DIF[-1] < 0:  # 零轴下方金叉
            account = get_trade_detail_data('test', 'stock', 'account')
            if len(account) > 0:
                available = account[0].m_dAvailable
                buy_vol = int(available * C.max_position / current_price / 100) * 100
                if buy_vol >= 100:
                    passorder(23, 1101, C.accountid, C.stock, 5, -1, buy_vol, 'MACD策略', 0, 'MACD金叉', C)
                    C.draw_text(1, 1, '买')
                    C.buy_price = current_price
                    C.trade_count += 1
                    print(f"{bar_date} MACD金叉买入 {buy_vol}股 @ {current_price}")
    
    # MACD死叉卖出
    elif C.stock in holdings_dict and len(DIF) >= 2:
        if DIF[-2] >= DEA[-2] and DIF[-1] < DEA[-1]:
            vol = holdings_dict[C.stock].m_nCanUseVolume
            if vol > 0:
                passorder(24, 1101, C.accountid, C.stock, 5, -1, vol, 'MACD策略', 0, 'MACD死叉', C)
                C.draw_text(1, 1, '卖')
                if C.buy_price > 0:
                    profit = (current_price - C.buy_price) / C.buy_price
                    C.total_profit += profit
                    if profit > 0:
                        C.win_count += 1
                C.buy_price = 0
                print(f"{bar_date} MACD死叉卖出 {vol}股 @ {current_price}")
    
    # 绘制MACD到副图
    C.paint('DIF', DIF[-1], -1, 0, 'yellow', 'noaxis')
    C.paint('DEA', DEA[-1], -1, 0, 'magenta', 'noaxis')
    C.paint('MACD', MACD[-1] * 2, -1, 42, 'cyan', 'noaxis')  # 柱状图

def stop(C):
    print("\n========== 策略统计 ==========")
    print(f"总交易次数: {C.trade_count}")
    print(f"盈利次数: {C.win_count}")
    if C.trade_count > 0:
        print(f"胜率: {C.win_count/C.trade_count*100:.2f}%")
        print(f"平均盈利: {C.total_profit/C.trade_count*100:.2f}%")
```

### 4.3 多股票轮动策略(高级版)

```python
#coding:gbk
import pandas as pd
import numpy as np

def init(C):
    # 股票池
    C.stock_pool = ['600519.SH', '000858.SZ', '600036.SH', '000001.SZ']
    C.stock_names = {
        '600519.SH': '贵州茅台',
        '000858.SZ': '五粮液',
        '600036.SH': '招商银行',
        '000001.SZ': '平安银行'
    }
    
    # 策略参数
    C.momentum_period = 20  # 动量周期
    C.top_n = 2             # 持有前N只
    C.rebalance_days = 5    # 调仓频率(天)
    
    C.accountid = "轮动策略"
    C.day_count = 0
    C.trade_log = []

def handlebar(C):
    bar_date = timetag_to_datetime(C.get_bar_timetag(C.barpos), '%Y%m%d')
    C.day_count += 1
    
    # 非调仓日跳过
    if C.day_count % C.rebalance_days != 0:
        return
    
    # 获取所有股票的历史数据
    momentum_dict = {}
    for stock in C.stock_pool:
        data = C.get_market_data_ex(
            ['close'],
            [stock],
            end_time=bar_date,
            period='1d',
            count=C.momentum_period + 10,
            subscribe=False
        )
        
        if stock in data and len(data[stock]) >= C.momentum_period:
            closes = data[stock]['close'].values
            # 计算动量(涨跌幅)
            momentum = (closes[-1] - closes[-C.momentum_period]) / closes[-C.momentum_period]
            momentum_dict[stock] = momentum
    
    if len(momentum_dict) == 0:
        return
    
    # 按动量排序,选前N只
    sorted_stocks = sorted(momentum_dict.items(), key=lambda x: x[1], reverse=True)
    target_stocks = [s[0] for s in sorted_stocks[:C.top_n]]
    
    print(f"\n{bar_date} 调仓日 - 目标持仓: {[C.stock_names.get(s, s) for s in target_stocks]}")
    
    # 查询当前持仓
    holdings = get_trade_detail_data('test', 'stock', 'position')
    current_holdings = {h.m_strInstrumentID + '.' + h.m_strExchangeID: h for h in holdings}
    
    # 卖出不在目标中的股票
    for stock in current_holdings:
        if stock not in target_stocks and current_holdings[stock].m_nCanUseVolume > 0:
            vol = current_holdings[stock].m_nCanUseVolume
            passorder(24, 1101, C.accountid, stock, 5, -1, vol, '轮动策略', 0, '调仓卖出', C)
            C.trade_log.append(f"{bar_date} 卖出 {C.stock_names.get(stock, stock)} {vol}股")
            print(f"  卖出 {C.stock_names.get(stock, stock)} {vol}股")
    
    # 买入目标股票
    account = get_trade_detail_data('test', 'stock', 'account')
    if len(account) > 0:
        available = account[0].m_dAvailable
        # 等权重分配
        weight = available / C.top_n
        
        for stock in target_stocks:
            if stock not in current_holdings:
                data = C.get_market_data_ex(['close'], [stock], end_time=bar_date, period='1d', count=1, subscribe=False)
                if stock in data and len(data[stock]) > 0:
                    price = data[stock]['close'].values[-1]
                    buy_vol = int(weight / price / 100) * 100
                    if buy_vol >= 100:
                        passorder(23, 1101, C.accountid, stock, 5, -1, buy_vol, '轮动策略', 0, '调仓买入', C)
                        C.trade_log.append(f"{bar_date} 买入 {C.stock_names.get(stock, stock)} {buy_vol}股 @ {price:.2f}")
                        print(f"  买入 {C.stock_names.get(stock, stock)} {buy_vol}股 @ {price:.2f}")

def stop(C):
    print("\n========== 调仓记录 ==========")
    for log in C.trade_log:
        print(log)
```

---

## 五、回测报告生成

### 5.1 策略内嵌统计报告

在`stop`函数中生成详细统计:

```python
def stop(C):
    """策略结束时生成回测报告"""
    print("\n" + "="*60)
    print("                    回测报告")
    print("="*60)
    
    # 1. 基础信息
    print("\n【基础信息】")
    print(f"策略名称: 双均线策略")
    print(f"测试标的: {C.stock}")
    print(f"测试周期: {C.period}")
    print(f"快线周期: {C.fast_period}")
    print(f"慢线周期: {C.slow_period}")
    
    # 2. 交易统计
    print("\n【交易统计】")
    print(f"总交易次数: {C.trade_count}")
    print(f"盈利次数: {C.win_count}")
    print(f"亏损次数: {C.trade_count - C.win_count}")
    if C.trade_count > 0:
        print(f"胜率: {C.win_count/C.trade_count*100:.2f}%")
    
    # 3. 收益统计
    print("\n【收益统计】")
    # 获取最终账户信息
    account = get_trade_detail_data('test', 'stock', 'account')
    if len(account) > 0:
        final_asset = account[0].m_dBalance
        initial_asset = 1000000  # 假设初始资金100万
        total_return = (final_asset - initial_asset) / initial_asset * 100
        print(f"初始资金: {initial_asset:,.2f}元")
        print(f"最终资金: {final_asset:,.2f}元")
        print(f"总收益率: {total_return:.2f}%")
        print(f"总盈亏: {final_asset - initial_asset:,.2f}元")
    
    # 4. 风险指标
    print("\n【风险指标】")
    if len(C.daily_returns) > 0:
        returns = np.array(C.daily_returns)
        print(f"年化收益: {np.mean(returns)*252*100:.2f}%")
        print(f"年化波动: {np.std(returns)*np.sqrt(252)*100:.2f}%")
        print(f"夏普比率: {np.mean(returns)/np.std(returns)*np.sqrt(252):.2f}")
        print(f"最大回撤: {C.max_drawdown*100:.2f}%")
    
    # 5. 交易明细
    print("\n【交易明细】(前10笔)")
    for i, trade in enumerate(C.trade_details[:10], 1):
        print(f"{i}. {trade['date']} {trade['direction']} {trade['stock']} "
              f"{trade['volume']}股 @ {trade['price']:.2f} 盈亏:{trade['profit']:.2f}%")
    
    print("\n" + "="*60)
```

### 5.2 完整回测指标计算

```python
def init(C):
    # 统计变量
    C.trade_count = 0
    C.win_count = 0
    C.lose_count = 0
    C.total_profit = 0
    C.total_loss = 0
    C.trade_details = []
    
    # 净值曲线
    C.daily_returns = []
    C.net_values = [1.0]  # 初始净值为1
    C.max_drawdown = 0
    C.peak_value = 1.0

def calculate_metrics(C):
    """计算回测指标"""
    if len(C.net_values) < 2:
        return {}
    
    # 日收益率
    returns = np.diff(C.net_values) / C.net_values[:-1]
    
    # 年化收益率
    total_days = len(C.net_values)
    annual_return = (C.net_values[-1] / C.net_values[0]) ** (252 / total_days) - 1
    
    # 年化波动率
    annual_volatility = np.std(returns) * np.sqrt(252)
    
    # 夏普比率(假设无风险利率3%)
    risk_free_rate = 0.03
    sharpe_ratio = (annual_return - risk_free_rate) / annual_volatility if annual_volatility > 0 else 0
    
    # 最大回撤
    peak = C.net_values[0]
    max_dd = 0
    for nv in C.net_values:
        if nv > peak:
            peak = nv
        dd = (peak - nv) / peak
        if dd > max_dd:
            max_dd = dd
    
    # 胜率
    win_rate = C.win_count / C.trade_count if C.trade_count > 0 else 0
    
    # 盈亏比
    avg_profit = C.total_profit / C.win_count if C.win_count > 0 else 0
    avg_loss = C.total_loss / C.lose_count if C.lose_count > 0 else 0
    profit_loss_ratio = abs(avg_profit / avg_loss) if avg_loss != 0 else 0
    
    return {
        '年化收益率': annual_return * 100,
        '年化波动率': annual_volatility * 100,
        '夏普比率': sharpe_ratio,
        '最大回撤': max_dd * 100,
        '总交易次数': C.trade_count,
        '胜率': win_rate * 100,
        '盈亏比': profit_loss_ratio,
        '平均盈利': avg_profit * 100,
        '平均亏损': avg_loss * 100
    }
```

---

## 六、图表展示技巧

### 6.1 副图指标绘制

```python
def handlebar(C):
    # ... 策略逻辑 ...
    
    # 在副图绘制多条指标线
    C.paint('RSI', rsi_value, -1, 0, 'yellow', 'noaxis')
    C.paint('超买线', 70, -1, 0, 'red', 'nodraw')  # 只画线不显示数值
    C.paint('超卖线', 30, -1, 0, 'green', 'nodraw')
    
    # 绘制柱状图
    if volume_ratio > 1:
        C.paint('量比', volume_ratio, -1, 42, 'red', 'noaxis')
    else:
        C.paint('量比', volume_ratio, -1, 42, 'green', 'noaxis')
```

### 6.2 买卖信号标注

```python
def handlebar(C):
    # ... 交易逻辑 ...
    
    # 买入信号 - 在K线下方显示文字和图标
    if buy_signal:
        C.draw_text(1, 1, '买入')
        C.draw_icon(1, 1, 1)  # 椭圆标记
        C.draw_vertline(1, 0, 100, 'red')  # 垂直线
    
    # 卖出信号 - 在K线上方显示
    if sell_signal:
        C.draw_text(1, 100, '卖出')
        C.draw_icon(1, 100, 0)  # 矩形标记
```

### 6.3 动态显示数值

```python
def handlebar(C):
    # 显示当前价格
    C.draw_number(1, 50, current_price, 2)
    
    # 显示涨跌幅
    change_pct = (current_price - pre_close) / pre_close * 100
    C.draw_number(1, 60, change_pct, 2)
    
    # 显示持仓盈亏
    if holding_vol > 0:
        profit = (current_price - buy_price) / buy_price * 100
        C.draw_number(1, 70, profit, 2)
```

### 6.4 条件绘图

```python
# 只在满足条件时绘图
if show_indicator:
    C.paint('MACD', macd_value, -1, 0, 'cyan', 'noaxis')

# 根据数值大小使用不同颜色
if macd_value > 0:
    C.paint('MACD柱', macd_value, -1, 42, 'red', 'noaxis')
else:
    C.paint('MACD柱', macd_value, -1, 42, 'green', 'noaxis')
```

---

## 七、高级回测技巧

### 7.1 参数优化回测

通过修改init中的参数,多次运行回测寻找最优参数:

```python
def init(C):
    C.stock = C.stockcode + '.' + C.market
    C.accountid = "参数优化"
    
    # 待优化参数(每次回测手动修改)
    C.fast_period = 10   # 尝试: 5, 10, 15, 20
    C.slow_period = 20   # 尝试: 10, 20, 30, 60
    
    C.results = []
```

**批量测试脚本** (在QMT外运行):
```python
# 生成不同参数组合
params = []
for fast in [5, 10, 15, 20]:
    for slow in [20, 30, 60]:
        if fast < slow:
            params.append((fast, slow))

# 逐个运行回测并记录结果
# (需要调用QMT的回测接口)
```

### 7.2 多周期回测

```python
def handlebar(C):
    # 在日线回测中参考周线趋势
    daily_data = C.get_market_data_ex(['close'], [C.stock], 
                                       end_time=bar_date, period='1d', 
                                       count=20, subscribe=False)
    
    weekly_data = C.get_market_data_ex(['close'], [C.stock], 
                                        end_time=bar_date, period='1w', 
                                        count=10, subscribe=False)
    
    # 周线趋势向上时,日线才做多
    if weekly_trend_up and daily_signal:
        # 买入
        pass
```

### 7.3 动态仓位管理

```python
def calculate_position_size(C, account_value, volatility):
    """根据账户净值和波动率动态调整仓位"""
    base_position = 0.5  # 基础仓位50%
    
    # 净值越高,仓位越高(但不超过80%)
    if account_value > 1100000:
        position = min(0.8, base_position + 0.2)
    elif account_value < 900000:
        position = max(0.3, base_position - 0.2)
    else:
        position = base_position
    
    # 波动率越高,仓位越低
    if volatility > 0.03:
        position *= 0.7
    
    return position
```

### 7.4 组合回测

```python
def init(C):
    # 多策略组合
    C.strategies = {
        'ma_cross': {'weight': 0.4, 'enabled': True},
        'macd': {'weight': 0.3, 'enabled': True},
        'rsi': {'weight': 0.3, 'enabled': True}
    }

def handlebar(C):
    # 分别计算各策略信号
    signals = {}
    for name, config in C.strategies.items():
        if config['enabled']:
            signal = calculate_signal(C, name)
            signals[name] = signal
    
    # 加权投票
    final_signal = sum([signals[k] * C.strategies[k]['weight'] 
                       for k in signals])
    
    if final_signal > 0.5:
        # 买入
        pass
    elif final_signal < -0.5:
        # 卖出
        pass
```

---

## 八、常见问题与优化

### 8.1 回测注意事项

⚠️ **常见错误**:

1. **subscribe参数未设为False**
   ```python
   # 错误! 回测时subscribe=True会订阅实时行情
   data = C.get_market_data_ex(['close'], [stock], subscribe=True)
   
   # 正确! 回测必须subscribe=False
   data = C.get_market_data_ex(['close'], [stock], subscribe=False)
   ```

2. **在init中获取行情**
   ```python
   # 错误! init中只能取本地数据
   def init(C):
       data = C.get_market_data_ex(...)  # 可能取不到数据
   
   # 正确! 在handlebar中获取
   def handlebar(C):
       data = C.get_market_data_ex(...)
   ```

3. **编码格式错误**
   ```python
   # 必须在第一行声明
   #coding:gbk
   ```

4. **缩进混用**
   ```python
   # 统一使用4个空格或Tab,不可混用
   ```

### 8.2 性能优化

```python
# 1. 减少不必要的数据获取
# 错误: 每次都获取全部历史数据
data = C.get_market_data_ex(..., count=1000)

# 正确: 只获取需要的数据
data = C.get_market_data_ex(..., count=30)

# 2. 使用局部变量缓存
# 错误: 重复查询账户
for i in range(10):
    account = get_trade_detail_data(...)

# 正确: 查询一次,多次使用
account = get_trade_detail_data(...)
for i in range(10):
    use(account)

# 3. 避免在循环中调用函数
# 错误
for stock in stocks:
    data = C.get_market_data_ex(..., [stock], ...)

# 正确: 批量获取
data = C.get_market_data_ex(..., stocks, ...)
```

### 8.3 回测参数设置

在QMT界面运行回测时:

**基础信息**:
- 默认周期: 选择策略使用的周期(日线/分钟线)
- 默认主图: 选择要交易的股票

**回测参数**:
- 起始日期: 建议至少3年以上数据
- 结束日期: 最近一个交易日
- 初始资金: 根据策略设置(如100万)
- 手续费: 股票建议万分之三
- 滑点: 建议设置1-2个tick

### 8.4 策略验证

✅ **验证清单**:

- [ ] 回测时间跨度足够长(至少3年)
- [ ] 覆盖牛熊市不同市场环境
- [ ] 交易频率合理(不过度交易)
- [ ] 胜率>40%,盈亏比>1.5
- [ ] 最大回撤<20%(保守策略)
- [ ] 夏普比率>1
- [ ] 参数在一定范围内稳定(不过度拟合)
- [ ] 样本外测试表现良好

### 8.5 从回测到实盘

回测通过后,转换为实盘策略:

```python
# 回测版本
def handlebar(C):
    data = C.get_market_data_ex(..., subscribe=False)  # 读取本地数据
    passorder(..., quickTrade=0)  # 逐K线模式

# 实盘版本
def handlebar(C):
    if not C.is_last_bar():  # 跳过历史K线
        return
    data = C.get_market_data_ex(..., subscribe=True)  # 订阅实时数据
    passorder(..., quickTrade=2)  # 立即下单模式
```

---

## 附录:常用技术指标计算

### A.1 使用talib库

```python
import talib

# 均线
MA = talib.MA(close, timeperiod=20)
SMA = talib.SMA(close, timeperiod=20)
EMA = talib.EMA(close, timeperiod=20)

# MACD
DIF, DEA, MACD = talib.MACD(close, fastperiod=12, slowperiod=26, signalperiod=9)

# RSI
RSI = talib.RSI(close, timeperiod=14)

# KDJ
K, D = talib.STOCH(high, low, close, fastk_period=9, slowk_period=3, slowd_period=3)
J = 3 * K - 2 * D

# 布林带
UPPER, MIDDLE, LOWER = talib.BBANDS(close, timeperiod=20)

# ATR(平均真实波幅)
ATR = talib.ATR(high, low, close, timeperiod=14)
```

### A.2 手动计算

```python
import numpy as np

# 简单移动平均
def SMA(close, period):
    return np.mean(close[-period:])

# 指数移动平均
def EMA(close, period):
    ema = np.zeros_like(close)
    ema[0] = close[0]
    multiplier = 2 / (period + 1)
    for i in range(1, len(close)):
        ema[i] = (close[i] - ema[i-1]) * multiplier + ema[i-1]
    return ema

# 波动率
def volatility(returns, period=20):
    return np.std(returns[-period:]) * np.sqrt(252)
```

---

## 参考资源

- QMT官方文档: https://dict.thinktrader.net/innerApi/
- 迅投QMT社区: https://www.xuntou.net/
- TA-Lib文档: https://ta-lib.github.io/ta-lib-python/

---

**文档版本**: v1.0  
**更新时间**: 2024-01-15  
**适用版本**: QMT内置Python 3.6+

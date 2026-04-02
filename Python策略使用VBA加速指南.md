# Python策略如何使用VBA进行加速

在QMT中，你可以利用VBA模型的高性能计算能力来加速Python策略，特别是对于**复杂的指标计算、批量选股、因子计算**等场景。

---

## 📌 方式一：使用 `call_vba` 函数（不推荐，但最简单）

这是最直接的方式，用于获取VBA模型的运行结果。

```python
# 调用VBA模型获取指标值
def handlebar(ContextInfo):
    # 调用名为 'MA' 的VBA模型中的 'ma1' 变量
    ma_value = call_vba('MA.ma1', '600036.SH', ContextInfo)
    print(f'均线值: {ma_value}')
```

**参数说明：**
- `factorname`: VBA模型的变量名，格式为 `'模型名.变量名'`
- `stockcode`: 股票代码
- `period`: K线周期（可选）
- `dividend_type`: 除权方式（可选）
- `barpos`: K线位置（可选）
- `ContextInfo`: 策略上下文对象

---

## 📌 方式二：使用 `call_formula` 函数（推荐）

这是更灵活的方式，可以获取VBA模型的完整输出结果。

```python
def handlebar(ContextInfo):
    # 调用VBA模型 '单股模型示范'
    modelRet = call_formula(
        '单股模型示范',      # 模型名称
        '000300.SH',        # 主图代码
        '1d',               # 日线周期
        '20240101',         # 起始时间
        '20240201',         # 截止时间
        -1,                 # 运行所有bar
        "none",             # 不复权
        {'a': 100}          # 模型参数
    )
    
    # 返回结果格式：
    # {
    #   'dbt': 0,
    #   'timelist': [时间戳列表],
    #   'outputs': {'变量1': [值列表], '变量2': [值列表]}
    # }
    print(modelRet)
```

---

## 📌 方式三：使用 `subscribe_formula` 订阅模型（实时推送）

适用于需要持续接收VBA模型计算结果的场景。

```python
def callback(data):
    # 回调函数接收VBA模型的实时推送
    print(f'时间戳: {data["timelist"]}')
    print(f'输出值: {data["outputs"]}')

def init(ContextInfo):
    # 订阅VBA模型
    basket = {
        '600000.SH': 0.06,
        '000001.SZ': 0.01
    }
    argsDict = {'a': 100, '__basket': basket}
    
    subID = subscribe_formula(
        '单股模型示范',        # 模型名称
        '000300.SH',         # 主图代码
        '1d',                # 日线周期
        '20240101',          # 起始时间
        '20240201',          # 截止时间
        -1,                  # 运行所有bar
        "none",              # 不复权
        argsDict,            # 扩展参数
        callback             # 回调函数
    )
    
    ContextInfo.subID = subID

def stop(ContextInfo):
    # 策略结束时反订阅
    unsubscribe_formula(ContextInfo.subID)
```

---

## 📌 方式四：批量调用VBA模型（高效选股）

适用于需要同时运行多个模型或多个股票的场景。

```python
def handlebar(ContextInfo):
    formulas = ['testModel1', 'testModel2']
    codes = ['600000.SH', '000001.SZ']
    basket = {'600000.SH': 0.06, '000001.SZ': 0.01}
    args = [
        {'a': 100, '__basket': basket},
        {'a': 200, '__basket': basket}
    ]
    
    # 批量获取VBA模型运行结果
    modelRet = call_formula_batch(
        formulas,          # 模型列表
        codes,             # 股票列表
        '1d',              # 日线周期
        extend_params=args # 各模型参数
    )
    
    # 返回格式：
    # [
    #   {'formula': '模型名', 'stock': '股票代码', 'argument': 参数, 'result': 结果},
    #   ...
    # ]
    for ret in modelRet:
        print(f"模型: {ret['formula']}, 股票: {ret['stock']}")
        print(ret['result'])
```

---

## 🔥 典型应用场景

### 场景1：用VBA计算复杂技术指标，Python负责交易逻辑

```python
# VBA模型：MACD背离检测（在VBA模型编辑器中编写）
# DIFF:=EMA(CLOSE,12)-EMA(CLOSE,26);
# DEA:=EMA(DIFF,9);
# DBL:DIFF>REF(DIFF,1) AND CROSS(DIFF,DEA);

# Python策略：调用VBA模型并下单
def handlebar(ContextInfo):
    # 调用VBA计算的MACD背离信号
    macd_signal = call_formula(
        'MACD背离模型',
        ContextInfo.stockcode,
        '1d',
        count=100
    )
    
    # 获取最新的背离信号
    dbl_values = macd_signal['outputs']['DBL']
    if dbl_values[-1] == 1:  # 出现底背离
        # Python负责下单
        passorder(23, 1101, account, ContextInfo.stockcode, 
                  5, 0, 100, "MACD背离买入", 2, "", ContextInfo)
```

### 场景2：批量选股 + Python执行交易

```python
def handlebar(ContextInfo):
    # 批量运行VBA选股模型
    stock_pool = ['600000.SH', '000001.SZ', '600036.SH']
    
    results = call_formula_batch(
        ['涨停股模型', '连续放量模型'],
        stock_pool,
        '1d',
        count=1
    )
    
    # 分析选股结果
    for ret in results:
        outputs = ret['result']['outputs']
        if outputs.get('ZT', [0])[-1] == 1:  # 涨停信号
            print(f"{ret['stock']} 出现涨停信号")
            # 执行交易...
```

---

## ⚡ 加速原理和优势

1. **VBA引擎优化**：VBA模型运行在QMT内置的高性能计算引擎中，对序列数据和K线计算做了深度优化
2. **向量化计算**：VBA天然支持序列运算，避免Python中的循环
3. **减少Python开销**：将复杂计算交给VBA，Python只负责策略逻辑和下单
4. **批量处理**：`call_formula_batch` 可以一次性计算多个模型和多只股票

---

## ⚠️ 注意事项

1. **数据补充**：使用前要确保本地K线数据或分笔数据已补充完整
2. **编码格式**：VBA模型可能返回GBK编码，需要注意字符编码转换
3. **性能权衡**：简单计算直接用Python即可，VBA适合复杂指标和批量计算
4. **实时性**：`call_formula` 是同步调用，会阻塞等待结果；`subscribe_formula` 是异步推送，更适合实时场景

---

## 📖 相关API函数参考

### call_formula - 调用模型
```python
call_formula(formula_name, stock_code, period, 
             start_time="", end_time="", count=-1, 
             dividend_type="none", extend_param={})
```

### subscribe_formula - 订阅模型
```python
subscribe_formula(formula_name, stock_code, period,
                  start_time="", end_time="", count=-1,
                  dividend_type="none", extend_param={},
                  callback=None)
```

### call_formula_batch - 批量调用模型
```python
call_formula_batch(formula_names, stock_codes, period,
                   start_time="", end_time="", count=-1,
                   dividend_type="none", extend_params=[])
```

### unsubscribe_formula - 反订阅模型
```python
unsubscribe_formula(subID)
```

---

## 💡 最佳实践建议

1. **指标计算**：使用VBA计算复杂技术指标（如MACD、KDJ、布林带等）
2. **批量选股**：使用 `call_formula_batch` 同时筛选多只股票
3. **实时监控**：使用 `subscribe_formula` 订阅实时信号
4. **策略分工**：VBA负责计算密集型任务，Python负责逻辑控制和交易执行
5. **参数优化**：通过 `extend_param` 动态调整VBA模型参数

---

**文档生成时间**: 2026-04-02  
**基于文档**: QMT迅投知识库 - VBA篇 & 内置Python篇

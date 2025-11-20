---
url: "https://dict.thinktrader.net/dictionary/question_answer.html"
title: "常见问题 | 迅投知识库"
---

## [\#](https://dict.thinktrader.net/dictionary/question_answer.html\#token-%E4%BD%BF%E7%94%A8%E7%9B%B8%E5%85%B3) Token 使用相关

### [\#](https://dict.thinktrader.net/dictionary/question_answer.html\#%E6%97%A0%E6%B3%95%E8%BF%9E%E6%8E%A5-xtquant-%E6%9C%8D%E5%8A%A1) 无法连接 xtquant 服务

**描述：**

`Exception: 无法连接xtquant服务，请检查QMT-投研版或QMT-极简版是否开启`

提示

您未成功启动数据服务或请检查端口是否正确填写`port=XXXXX`。

### [\#](https://dict.thinktrader.net/dictionary/question_answer.html\#token-%E5%A4%B1%E6%95%88%E5%90%8E%E6%98%AF%E5%90%A6%E5%86%8D%E6%AC%A1%E8%AE%A2%E9%98%85) Token 失效后是否再次订阅

**描述：**

如果token刷新失效后，换成新的token，是否需要再次进行订阅操作？

提示

需要让`xtdatacenter`调整token后重新连接行情，这个可以直接重启`xtdatacenter`的进程，这会导致下游的`xtdata`连接断开，断开后重连上需要重新订阅

## [\#](https://dict.thinktrader.net/dictionary/question_answer.html\#%E8%8E%B7%E5%8F%96%E8%A1%8C%E6%83%85%E7%9B%B8%E5%85%B3) 获取行情相关

### [\#](https://dict.thinktrader.net/dictionary/question_answer.html\#k-%E7%BA%BF%E6%95%B0%E6%8D%AE%E7%BC%BA%E5%A4%B1) k 线数据缺失

**描述：**

获取到的数据 1m 的 K 线数据缺失。

提示

如果是`get_market_data_ex`获取K线可以用fill\_data=True来填充。

这个填充现在只对 k 线这种标准的数据做了，其他的特色数据没有处理填充的操作。

因此其他函数如果在获取k线数据的时候，如果存在数据缺失，用`fill_data=True`来填充，并不适用。

### [\#](https://dict.thinktrader.net/dictionary/question_answer.html\#%E8%AE%A2%E9%98%85%E6%95%B0%E6%8D%AE%E7%9B%B8%E5%85%B3) 订阅数据相关

**描述：**

盘中订阅完行情全推后，切换行情站点需要重新订阅吗？

提示

1. 对于内置python策略来说，需要重新订阅
2. 对于xtquant.xtdata来说，不需要重新订阅，除非xtdata断开

### [\#](https://dict.thinktrader.net/dictionary/question_answer.html\#%E6%95%B0%E6%8D%AE%E6%9B%B4%E6%96%B0%E6%97%B6%E9%97%B4%E7%9B%B8%E5%85%B3) 数据更新时间相关

**描述：**

收盘后什么时候能够下载到完整的当日行情数据

提示

股票收盘有两个时间点

大部分股票是 15:00，在交易所撮合尾盘竞价成交后下发行情，一般会延后几秒，大约是15:00:03左右

创业板、科创板有盘后定价交易，如果需要获取这部分数据的话，在15:30收盘之后获取，不需要的话也在15:00获取

期货一般在15:00收盘后有最新的收盘价，在16:30左右有结算价
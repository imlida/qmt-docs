# QMT 极速策略交易系统文档

## 项目简介

QMT (Quick Multi-Trading) 极速策略交易系统文档仓库，提供完整的API接口文档、编程指南和数据字典，支持多种编程语言进行量化交易策略开发与回测。

## 项目概述

QMT系统是一套功能强大的量化交易平台，内置多种编程语言支持和丰富的API接口，可用于：
- 策略研发与回测
- 实时行情订阅与分析
- 自动化交易执行
- 多市场、多品种交易支持
- 高性能数据处理

## 文档结构

本仓库按照不同的编程语言接口和文档类型组织，主要包含以下目录：

```
qmt-docs/
├── VBA/                    # VBA语言接口文档
│   ├── dict.thinktrader.net_VBA_basic_syntax.html.md        # VBA基础语法
│   ├── dict.thinktrader.net_VBA_code_examples.html.md       # VBA代码示例
│   ├── dict.thinktrader.net_VBA_control_statement.html.md   # VBA控制语句
│   ├── dict.thinktrader.net_VBA_operating_mode.html.md      # VBA运行模式
│   ├── dict.thinktrader.net_VBA_start_now.html.md           # VBA快速开始
│   └── dict.thinktrader.net_VBA_system_function.html.md     # VBA系统函数
├── XtQuant文档/            # XtQuant Python库文档
│   ├── dict.thinktrader.net_nativeApi_code_examples.html.md  # 代码示例
│   ├── dict.thinktrader.net_nativeApi_download_xtquant.html.md # 下载指南
│   ├── dict.thinktrader.net_nativeApi_question_function.html.md # 常见问题
│   ├── dict.thinktrader.net_nativeApi_start_now.html.md     # 快速开始
│   ├── dict.thinktrader.net_nativeApi_xtdata.html.md        # xtdata模块
│   └── dict.thinktrader.net_nativeApi_xttrader.html.md      # xttrader模块
├── 内置python/             # 内置Python接口文档
│   ├── dict.thinktrader.net_innerApi_callback_function.html.md # 回调函数
│   ├── dict.thinktrader.net_innerApi_code_examples.html.md  # 代码示例
│   ├── dict.thinktrader.net_innerApi_data_function.html.md  # 数据函数
│   ├── dict.thinktrader.net_innerApi_data_structure.html.md # 数据结构
│   ├── dict.thinktrader.net_innerApi_drawing_function.html.md # 绘图函数
│   ├── dict.thinktrader.net_innerApi_enum_constants.html.md # 枚举常量
│   ├── dict.thinktrader.net_innerApi_interface_operation.html.md # 界面操作
│   ├── dict.thinktrader.net_innerApi_question_answer.html.md # 问答
│   ├── dict.thinktrader.net_innerApi_quote_function.html.md # 行情函数
│   ├── dict.thinktrader.net_innerApi_start_now.html.md      # 快速开始
│   ├── dict.thinktrader.net_innerApi_system_function.html.md # 系统函数
│   ├── dict.thinktrader.net_innerApi_trading_function.html.md # 交易函数
│   ├── dict.thinktrader.net_innerApi_user_attention.html.md # 用户注意事项
│   └── dict.thinktrader.net_innerApi_variable_convention.html.md # 变量约定
└── 数据字典/                # 数据字典文档
    ├── dict.thinktrader.net_dictionary_bond.html.md         # 债券数据
    ├── dict.thinktrader.net_dictionary_floorfunds.html.md   # 分级基金
    ├── dict.thinktrader.net_dictionary_future.html.md       # 期货数据
    ├── dict.thinktrader.net_dictionary_indexes.html.md      # 指数数据
    ├── dict.thinktrader.net_dictionary_industry.html.md     # 行业数据
    ├── dict.thinktrader.net_dictionary_option.html.md       # 期权数据
    ├── dict.thinktrader.net_dictionary_question_answer.html.md # 问答
    ├── dict.thinktrader.net_dictionary_scenario_based_example.html.md # 场景示例
    ├── dict.thinktrader.net_dictionary_stock.html.md        # 股票数据
    └── dict.thinktrader.net_dictionary_xuntou_factor.html.md # 迅投因子
```

## 主要功能模块

### 1. 内置Python接口

QMT系统内置Python 3.6运行环境，提供完整的API接口，主要功能包括：
- **行情数据获取**：历史和实时行情、K线数据、分笔数据等
- **交易下单功能**：委托、撤单、查询持仓、资产等
- **回测与实盘模式**：支持策略回测和实盘交易两种运行模式
- **绘图与可视化**：提供K线图和技术指标绘制功能

### 2. XtQuant Python库

XtQuant是基于迅投MiniQMT的Python策略运行框架，提供以下核心模块：
- **xtdata**：行情模块，提供历史和实时K线、分笔数据、财务数据、合约基础信息等
- **xttrader**：交易模块，封装报单、撤单、查询资产、委托、成交、持仓等API接口

支持Python 3.6至3.12版本，需配合MiniQMT客户端使用。

### 3. VBA接口

QMT系统支持VBA语言编写量化交易策略，主要特点：
- 公式编写系统：支持主图指标和副图指标
- 参数化设计：可灵活调整计算参数
- 序列模式和逐K线模式：不同的运行机制
- 支持模型加密：保护策略代码安全

### 4. 数据字典

提供全面的金融市场数据字典，包括：
- 股票、债券、期货、期权等多种金融产品数据结构
- 行情数据、财务数据、行业分类等详细字段说明
- 数据获取方法和示例代码

## 快速开始

根据您的编程语言选择，推荐以下入门文档：

- **Python用户**：
  - 内置Python：[快速开始](内置python/dict.thinktrader.net_innerApi_start_now.html.md)
  - XtQuant：[快速开始](XtQuant文档/dict.thinktrader.net_nativeApi_start_now.html.md)

- **VBA用户**：
  - [快速开始](VBA/dict.thinktrader.net_VBA_start_now.html.md)

## 系统要求

- **内置Python**：QMT客户端内置Python 3.6环境
- **XtQuant**：支持Python 3.6至3.12，需配合MiniQMT客户端使用
- **VBA**：需在QMT客户端中使用

## 文档使用指南

1. **按功能查找**：根据您需要实现的功能，查找对应模块的文档
2. **参考代码示例**：各目录下的`code_examples`文件提供实用代码片段
3. **查询数据字典**：使用数据字典了解数据结构和字段含义
4. **FAQ参考**：各模块下的问答文档解答常见问题

## 版权信息

本文档仓库包含的所有内容均为迅投（ThinkTrader）所有，仅供学习和参考使用。

---

*最后更新时间：2024年*

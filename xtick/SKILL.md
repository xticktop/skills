---
name: xtick
description:XTick行情API提供了全面、准确、稳定的实时行情数据，帮助开发者和研究者构建创新的交易和分析工具，满足金融行业的需求，进行深入的市场分析和模型验证。
---

# XTick

## 概述

XTick平台接入了沪深京A股、ETF基金、主流指数、港股等数据种类，包括tick数据、竞价数据、分钟数据、分笔数据、分价数据、量化指标、所有K线等高频数据，还有丰富的财务报表数据以及100多个金融指标数据接口。

## 快速上手

- 安装python运行环境(推荐python3.7+)。

- XTick官网注册，获取token。 [注册地址](http://www.xtick.top/user/account)

- 查询XTick接口文档，找到对应的接口。 [在线接口文档](http://www.xtick.top/apidoc)

- 根据接口文档，使用Python或Java代码获取数据。（如**股票列表**接口）

```python
from xtick.scripts.api import XTickMarketApi, XTickIndicatorApi, XTickBaseApi, XTickWebSocketApi, XTickWatchApi,XTickQuantApi, XTickCoreApi

# 登录XTick官网，获取token
token: str = "123456789"
# 获取平安银行日K线数据，时间范围从2025-01-01到2026-01-01
result = XTickMarketApi.getKlineMarket(1, "000001", "1d", "1", "2025-01-01", "2026-01-01", token, "get")
print(f"Received data: {result}")

```

## 参数格式说明

- 日期：yyyy-MM-dd（如 2026-01-01）
- 股票类型：type 格式（如 1） 1:沪深京A股，3:港股，10：沪深指数，20：ETF;
- 股票代码：code 格式（如 000001），如支持数据全推，则code填写为all。
- 返回格式：json

## python脚本示例

- [股票数据获取示例](scripts/XTickStockApiClient.py)

## 详细文档说明列表

|  ID | 详细文档说明                                                | 描述                                                                                                                                                       |
|----:|:---------------------------------------------------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------|
|   1 | [XTick股票数据接口文档](references/xtick.md)                     | 获取沪深京A股、ETF基金、主流指数、港股等数据种类，包括tick数据、竞价数据、分钟数据、分笔数据、分价数据、量化指标、所有K线等高频数据，还包括资产负债表、利润表、现金流量表、股本表、股东数、十大股东、十大流通股东、每股指标八类财务数据，可以根据每个财务报表类型查阅对应的数据字段，从而获取到需要的财务数据。 |
|   2 | [XTick金融指标数据接口文档](references/indicator.md)               | 获取MACD、KDJ、RSI、BIAS、WR、CCI、SAR、OBV、SAR、OBV、MA, BOLL、DMI等股票金融指标数据,包含100多个金融指标数据接口。                                                                        |

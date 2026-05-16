# XTick API 接口文档

## 概述

XTick是一个提供股票实时数据API接口的平台，接入了沪深京A股、ETF基金、主流指数、港股等数据种类。

- **官网地址**: http://www.xtick.top/
- **API基础地址**: http://api.xtick.top
- **数据格式**: JSON
- **认证方式**: Token认证

## 权限等级说明

| 等级 | 名称 | 说明 |
|------|------|------|
| 1 | 青铜版 | 基础行情数据 |
| 2 | 白银版 | 盯盘数据 + 青铜版权限 |
| 3 | 黄金版 | 核心数据 + 白银版权限 |
| 4 | 至尊版 | 热点数据 + 黄金版权限 |
| 5 | 量化版 | 量化因子 + 至尊版权限 |


---

## 1. 行情数据接口

行情数据接口


### 1. 股票列表

**描述**: 按照股票池获取股票代码，包括沪深京A股、港股、沪深指数、ETF、可转债几类数据。

**标签**: 其它数据

**权限要求**: 青铜版、白银版、黄金版、至尊版

**接口地址**: `http://api.xtick.top/doc/stockinfo`

**请求示例**:
```
http://api.xtick.top/doc/stockinfo?symbol=all&token=123456789
```

**输入参数**:

| 参数名 | 类型 | 必填 | 取值范围 | 说明 |
|--------|------|------|----------|------|
| symbol | String | 否 | all-全部股票，sz-深交所股票，sh-上交所股票，bj-北交所股票，hk-港交所股票，index-指数，bond-可转债，cyb-创业板股票，kcb-科创板股票，etf-全部ETF，st-st股票，ts-退市股票 | 股票分类 |
| token | String | 是 | - | 登录网站获取token |

**输出参数**:

| 参数名 | 类型 | 说明 |
|--------|------|------|
| type | int | 标的类别 |
| code | String | 股票代码 |
| name | String | 股票名称 |

**返回示例**:
```json
[{"type":20,"code":"530680","name":"上证180ETF兴业"}]
```


### 2. 交易日历

**描述**: 获取A股交易日历，包含交易所交易日历和个股交易日历。交易所是指上交所、深交所、北交所的交易日历。数据从2020年开始。

**标签**: 其它数据

**权限要求**: 青铜版、白银版、黄金版、至尊版

**接口地址**: `http://api.xtick.top/doc/calendar`

**请求示例**:
```
http://api.xtick.top/doc/calendar?code=000001&startDate=2026-05-16&endDate=2026-05-16&token=123456789
```

**输入参数**:

| 参数名 | 类型 | 必填 | 取值范围 | 说明 |
|--------|------|------|----------|------|
| code | String | 否 | - | 股票代码。code取值为000001，则表示获取单个股票数据；code取值为all，且startDate和endDate必须是同一天，则表示获取某个交易日内的全市场股票数据。注意：不支持多个股票批量获取获取。
查询交易所交易日历，则code=ssb，代表上交所、深交所、北交所的交易日历。 |
| startDate | String | 否 | - | 开始日期 |
| endDate | String | 否 | - | 结束日期 |
| token | String | 是 | - | 登录网站获取token |

**输出参数**:

| 参数名 | 类型 | 说明 |
|--------|------|------|
| code | String | 股票代码，若是A股交易日历，code为ssb |
| time | long | 交易日期 |
| status | int | 交易状态，1为正常交易，2为休市，包括周末、节假日休市以及个股的停牌 |
| preTime | long | 上一个交易日 |

**返回示例**:
```json
[{"code":"600438","time":1773072000000,"status":2,"preTime":1771862400000}]
```


### 3. 分钟数据-实时接口

**描述**: 提供日内一分钟实时数据，这个分钟接口调取数据会更快。
如果需要盘后一次获取全市场1分钟数据，请参考【核心指标接口】-【增量更新】。

**标签**: 分钟数据

**权限要求**: 青铜版、白银版、黄金版、至尊版

**接口地址**: `http://api.xtick.top/doc/kline/minute`

**请求示例**:
```
http://api.xtick.top/doc/kline/minute?type=1&code=000001&fq=1&token=123456789
```

**输入参数**:

| 参数名 | 类型 | 必填 | 取值范围 | 说明 |
|--------|------|------|----------|------|
| type | int | 否 | 1-沪深京A股，2-沪深指数，3-港股，4-ETF基金，5-沪深可转债 | 标的类型 |
| code | String | 否 | - | 股票代码，仅支持单个股票获取，不支持批量参数，不支持ALL参数。 |
| fq | String | 否 | 1-不复权，2-前复权，3-后复权，4-等比前复权，5-等比后复权 | 复权类型 |
| token | String | 是 | - | 登录网站获取token |

**输出参数**:

| 参数名 | 类型 | 说明 |
|--------|------|------|
| type | int | 标的类型 |
| code | String | 股票代码 |
| time | long | 交易日期 |
| open | float | 开盘价 |
| close | float | 收盘价 |
| high | float | 最高价 |
| low | float | 最低价 |
| volume | float | 成交量（手） |
| amount | float | 成交额（元） |
| preClose | float | 昨日收盘价                           #在9:30开盘前，分钟数据的preClose是前一日的close。                         #在9:30开盘后，分钟数据的preClose是前一分钟的close。 |

**返回示例**:
```json
[{"type":1,"code":"000001","time":1773710100000,"open":10.91,"close":10.91,"high":10.91,"low":10.91,"volume":0.0,"amount":0.0,"preClose":10.92}]
```


### 4. 行情数据-通用接口

**描述**: 行情数据包括1分钟K线、5分钟K线、15分钟K线、30分钟K线、1小时K线、日K线、周K线、季度K线、年K线。支持复权数据获取，K线数据盘中实时更新。
注意：1、如果需要获取盘中实时行情数据，endDate参数填写当天交易日日期即可。
2、period为分钟类型（包括1m、5m、15m、30m、1h），则限制单次请求时间跨度最大为一月，即endDate - startDate不超过31天。如果调取历史数据，建议按整月调取；如果调取每日增量数据，则建议按实际需要的日期调取。两种方式，效率相对更快。
3、如果需要收盘后获取当天全市场股票日线数据，包括前复权、不复权、后复权三种方式。将code设置为all，startDate和endDate日期设置为当前交易日，调用接口即可。仅支持进一个月内的增量数据调用，使用all参数调用盘后全市场复权数据。一次拉取日内全市场行情实时数据，参考【盯盘数据接口】->【行情数据-实时接口】
- 盘后一次获取全市场1分钟数据，请参考【核心指标接口】-【增量更新】

**标签**: 行情数据, 分钟数据

**权限要求**: 青铜版、白银版、黄金版、至尊版

**接口地址**: `http://api.xtick.top/doc/kline/market`

**请求示例**:
```
http://api.xtick.top/doc/kline/market?type=1&code=000001&fq=1&period=1d&startDate=2026-05-16&endDate=2026-05-16&token=123456789
```

**输入参数**:

| 参数名 | 类型 | 必填 | 取值范围 | 说明 |
|--------|------|------|----------|------|
| type | int | 否 | 1-沪深京A股，2-沪深指数，3-港股，4-ETF基金，5-沪深可转债 | 标的类型 |
| code | String | 否 | - | 股票代码，仅支持单个股票获取，不支持批量参数，不支持ALL参数。 |
| fq | String | 否 | 1-不复权，2-前复权，3-后复权，4-等比前复权，5-等比后复权 | 复权类型 |
| period | String | 否 | 1m-1分钟线，5m-5分钟线，15m-15分钟线，30m-30分钟线，1h-1小时线，1d-日线，1w-周线，1mon-月线，1q-季度线，1y-年线 | K线周期 |
| startDate | String | 否 | - | 开始日期 |
| endDate | String | 否 | - | 结束日期 |
| token | String | 是 | - | 登录网站获取token |

**输出参数**:

| 参数名 | 类型 | 说明 |
|--------|------|------|
| type | int | 标的类型 |
| code | String | 股票代码 |
| time | long | 交易日期 |
| open | float | 开盘价 |
| close | float | 收盘价 |
| high | float | 最高价 |
| low | float | 最低价 |
| volume | float | 成交量（手） |
| amount | float | 成交额（元） |
| preClose | float | 昨日收盘价 |

**返回示例**:
```json
[{"type":20,"code":"159915","time":1773072000000,"open":3.267,"close":3.296,"high":3.298,"low":3.252,"volume":1.2409336E7,"amount":4.06937318E9,"preClose":3.201}]
```


### 5. 股东数

**描述**: 股东数，数据范围：2001年-至今

**标签**: 财务数据

**权限要求**: 青铜版、白银版、黄金版、至尊版

**接口地址**: `http://api.xtick.top/doc/holdernum`

**请求示例**:
```
http://api.xtick.top/doc/holdernum?code=000001&startDate=2026-05-16&endDate=2026-05-16&token=123456789
```

**输入参数**:

| 参数名 | 类型 | 必填 | 取值范围 | 说明 |
|--------|------|------|----------|------|
| code | String | 否 | - | 股票代码，仅支持单个股票获取 |
| startDate | String | 否 | - | 开始日期 |
| endDate | String | 否 | - | 结束日期 |
| token | String | 是 | - | 登录网站获取token |

**输出参数**:

| 参数名 | 类型 | 说明 |
|--------|------|------|
| code | String | 股票代码 |
| reportDate | String | 报告截止日 |
| publicDate | String | 公告日 |
| holder | Float | 股东总数 |
| holderA | Float | A股东户数 |
| holderB | Float | B股东户数 |
| holderH | Float | H股东户数 |
| holderFlow | Float | 已流通股东户数 |
| holderOther | Float | 未流通股东户数 |


### 6. 财务指标

**描述**: 财务指标，数据范围：2007年-至今。

**标签**: 财务数据

**权限要求**: 青铜版、白银版、黄金版、至尊版

**接口地址**: `http://api.xtick.top/doc/gaap`

**请求示例**:
```
http://api.xtick.top/doc/gaap?code=000001&startDate=2026-05-16&endDate=2026-05-16&token=123456789
```

**输入参数**:

| 参数名 | 类型 | 必填 | 取值范围 | 说明 |
|--------|------|------|----------|------|
| code | String | 否 | - | 股票代码，仅支持单个股票获取 |
| startDate | String | 否 | - | 开始日期 |
| endDate | String | 否 | - | 结束日期 |
| token | String | 是 | - | 登录网站获取token |

**输出参数**:

| 参数名 | 类型 | 说明 |
|--------|------|------|
| code | String | 股票代码 |
| reportDate | String | 报告截止日 |
| publicDate | String | 公告日 |
| ocfps | Float | 每股经营活动现金流量 |
| bps | Float | 每股净资产 |
| basicEps | Float | 基本每股收益 |
| dilutedEps | Float | 稀释每股收益 |
| dps | Float | 每股未分配利润 |
| fund | Float | 每股资本公积金 |
| equity | Float | 净资产收益率 |
| salesProfit | Float | 销售毛利率 |
| revenueInc | Float | 主营收入同比增长 |
| profitInc | Float | 净利润同比增长 |
| netProfitM | Float | 归属于母公司所有者的净利润同比增长 |
| netProfitA | Float | 扣非净利润同比增长 |
| roe | Float | 净资产收益率 |
| grossProfit | Float | 毛利率 |
| netProfit | Float | 净利率 |
| prePay | Float | 预收款 |
| salesCash | Float | 销售现金流 |
| gearRatio | Float | 资产负债比率 |
| turnover | Float | 存货周转率 |


### 7. 十大股东

**描述**: 十大股东，数据范围：公司上市-至今

**标签**: 财务数据

**权限要求**: 青铜版、白银版、黄金版、至尊版

**接口地址**: `http://api.xtick.top/doc/topholder`

**请求示例**:
```
http://api.xtick.top/doc/topholder?code=000001&startDate=2026-05-16&endDate=2026-05-16&token=123456789
```

**输入参数**:

| 参数名 | 类型 | 必填 | 取值范围 | 说明 |
|--------|------|------|----------|------|
| code | String | 否 | - | 股票代码，仅支持单个股票获取 |
| startDate | String | 否 | - | 开始日期 |
| endDate | String | 否 | - | 结束日期 |
| token | String | 是 | - | 登录网站获取token |

**输出参数**:

| 参数名 | 类型 | 说明 |
|--------|------|------|
| code | String | 股票代码 |
| reportDate | String | 报告截止日 |
| publicDate | String | 公告日 |
| name | String | 股东名称 |
| num | Float | 持股数量 |
| reason | String | 变动原因 |
| ratio | Float | 持股比例 |
| nature | String | 股份性质 |
| rank | int | 持股排名 |


### 8. 十大流通股东

**描述**: 十大流通股东，数据范围：2004年-至今

**标签**: 财务数据

**权限要求**: 青铜版、白银版、黄金版、至尊版

**接口地址**: `http://api.xtick.top/doc/topflowholder`

**请求示例**:
```
http://api.xtick.top/doc/topflowholder?code=000001&startDate=2026-05-16&endDate=2026-05-16&token=123456789
```

**输入参数**:

| 参数名 | 类型 | 必填 | 取值范围 | 说明 |
|--------|------|------|----------|------|
| code | String | 否 | - | 股票代码，仅支持单个股票获取 |
| startDate | String | 否 | - | 开始日期 |
| endDate | String | 否 | - | 结束日期 |
| token | String | 是 | - | 登录网站获取token |

**输出参数**:

| 参数名 | 类型 | 说明 |
|--------|------|------|
| code | String | 股票代码 |
| reportDate | String | 报告截止日 |
| publicDate | String | 公告日 |
| name | String | 股东名称 |
| num | Float | 持股数量 |
| reason | String | 变动原因 |
| ratio | Float | 持股比例 |
| nature | String | 股份性质 |
| rank | int | 持股排名 |


---

## 2. 盯盘数据接口

盯盘数据接口


### 1. 龙虎榜-历史数据

**描述**: 龙虎榜详情历史数据，盘后更新数据。

**标签**: 其它数据

**权限要求**: 白银版、黄金版、至尊版

**接口地址**: `http://api.xtick.top/doc/order/longhubang`

**请求示例**:
```
http://api.xtick.top/doc/order/longhubang?tradeDate=2026-05-16&token=123456789
```

**输入参数**:

| 参数名 | 类型 | 必填 | 取值范围 | 说明 |
|--------|------|------|----------|------|
| tradeDate | String | 否 | - | 交易日期 |
| token | String | 是 | - | 登录网站获取token |

**输出参数**:

| 参数名 | 类型 | 说明 |
|--------|------|------|
| code | String | 股票代码 |
| time | long | 交易时间 |
| explanation | String | 上榜原因 |
| name | String | 名称 |
| close | float | 收盘价 |
| zdf | float | 涨跌幅 |
| netamt | float | 龙虎榜净买额（元） |
| buyamt | float | 龙虎榜买入额（元） |
| sellamt | float | 龙虎榜卖出额（元） |
| dealamt | float | 龙虎榜成交额（元） |
| marketamt | float | 市场总成交额（元） |
| netratio | float | 净买额占总成交比 |
| amtratio | float | 成交额占总成交比 |
| hsl | float | 换手率 |
| freecap | float | 流通市值（元） |

**返回示例**:
```json
[{"code":"000533","time":1774454400000,"explanation":"日跌幅偏离值达到7%的前5只证券","name":"顺钠股份","close":15.53,"zdf":-9.971,"netamt":-1.17109E8,"buyamt":2.05136E8,"sellamt":3.22244992E8,"dealamt":5.27380992E8,"marketamt":2.2175401E9,"netratio":-5.281,"amtratio":23.7822,"hsl":20.2806,"freecap":1.06365E10}]
```


### 2. 日K线-实时数据

**描述**: 获取盘中实时日K线数据。支持批量参数，支持ALL参数。

**标签**: 行情数据

**权限要求**: 白银版、黄金版、至尊版

**接口地址**: `http://api.xtick.top/doc/order/day`

**请求示例**:
```
http://api.xtick.top/doc/order/day?type=1&code=000001,000002&token=123456789
```

**输入参数**:

| 参数名 | 类型 | 必填 | 取值范围 | 说明 |
|--------|------|------|----------|------|
| type | int | 否 | 1-沪深京A股，2-沪深指数，3-港股，4-ETF基金，5-沪深可转债 | 标的类型 |
| code | String | 否 | - | 股票代码。code取值为000001，则表示获取单个股票数据；code取值为000001,000002，则表示批量获取获取，股票数量最大为50个；code取值为all，则表示全推全市场数据。 |
| token | String | 是 | - | 登录网站获取token |

**输出参数**:

| 参数名 | 类型 | 说明 |
|--------|------|------|
| type | int | 标的类型 |
| code | String | 股票代码 |
| time | long | 交易日期 |
| open | float | 开盘价 |
| close | float | 收盘价 |
| high | float | 最高价 |
| low | float | 最低价 |
| volume | float | 成交量（手） |
| amount | float | 成交额（元） |
| preClose | float | 昨日收盘价 |

**返回示例**:
```json
[{"type":20,"code":"159915","time":1773072000000,"open":3.267,"close":3.296,"high":3.298,"low":3.252,"volume":1.2409336E7,"amount":4.06937318E9,"preClose":3.201}]
```


### 3. 分钟K线-实时数据

**描述**: 获取盘中分钟K线实时数据。支持批量参数，支持ALL参数。

**标签**: 分钟数据

**权限要求**: 白银版、黄金版、至尊版

**接口地址**: `http://api.xtick.top/doc/order/minute`

**请求示例**:
```
http://api.xtick.top/doc/order/minute?type=1&code=000001,000002&token=123456789
```

**输入参数**:

| 参数名 | 类型 | 必填 | 取值范围 | 说明 |
|--------|------|------|----------|------|
| type | int | 否 | 1-沪深京A股，2-沪深指数，3-港股，4-ETF基金，5-沪深可转债 | 标的类型 |
| code | String | 否 | - | 股票代码。code取值为000001，则表示获取单个股票数据；code取值为000001,000002，则表示批量获取获取，股票数量最大为50个；code取值为all，则表示全推全市场数据。 |
| token | String | 是 | - | 登录网站获取token |

**输出参数**:

| 参数名 | 类型 | 说明 |
|--------|------|------|
| type | int | 标的类型 |
| code | String | 股票代码 |
| time | long | 交易日期 |
| open | float | 开盘价 |
| close | float | 收盘价 |
| high | float | 最高价 |
| low | float | 最低价 |
| volume | float | 成交量（手） |
| amount | float | 成交额（元） |
| preClose | float | 昨日收盘价                           #在9:30开盘前，分钟数据的preClose是前一日的close。                         #在9:30开盘后，分钟数据的preClose是前一分钟的close。 |

**返回示例**:
```json
[{"type":1,"code":"000001","time":1773710100000,"open":10.91,"close":10.91,"high":10.91,"low":10.91,"volume":0.0,"amount":0.0,"preClose":10.92}]
```


### 4. 买卖五档-实时数据

**描述**: 获取盘中买卖五档盘口实时数据，Tick实时数据。支持批量参数，支持ALL参数。

**标签**: Tick数据

**权限要求**: 白银版、黄金版、至尊版

**接口地址**: `http://api.xtick.top/doc/order/five`

**请求示例**:
```
http://api.xtick.top/doc/order/five?type=1&code=000001,000002&token=123456789
```

**输入参数**:

| 参数名 | 类型 | 必填 | 取值范围 | 说明 |
|--------|------|------|----------|------|
| type | int | 否 | 1-沪深京A股，2-沪深指数，3-港股，4-ETF基金，5-沪深可转债 | 标的类型 |
| code | String | 否 | - | 股票代码。code取值为000001，则表示获取单个股票数据；code取值为000001,000002，则表示批量获取获取，股票数量最大为50个；code取值为all，则表示全推全市场数据。 |
| token | String | 是 | - | 登录网站获取token |

**输出参数**:

| 参数名 | 类型 | 说明 |
|--------|------|------|
| type | int | 标的类别 |
| code | String | 股票代码 |
| time | long | 交易时间 |
| lastPrice | float | 最新价 |
| open | float | 开盘价 |
| high | float | 最高价 |
| low | float | 最低价 |
| lastClose | float | 前收盘价 |
| amount | float | 成交总额 |
| volume | float | 成交总量（手） |
| transactionNum | int | 成交笔数，部分交易所填充该字段，实时数据中该字段为0 |
| bp1 | float | 买一价 |
| bp2 | float | 买二价 |
| bp3 | float | 买三价 |
| bp4 | float | 买四价 |
| bp5 | float | 买五价 |
| bv1 | float | 买一量 |
| bv2 | float | 买二量 |
| bv3 | float | 买三量 |
| bv4 | float | 买四量 |
| bv5 | float | 买五量 |
| sp1 | float | 卖一价 |
| sp2 | float | 卖二价 |
| sp3 | float | 卖三价 |
| sp4 | float | 卖四价 |
| sp5 | float | 卖五价 |
| sv1 | float | 卖一量 |
| sv2 | float | 卖二量 |
| sv3 | float | 卖三量 |
| sv4 | float | 卖四量 |
| sv5 | float | 卖五量 |

**返回示例**:
```json
[{"type":1,"code":"000001","time":1770345690000,"lastPrice":11.03,"open":11.08,"high":11.14,"low":10.99,"lastClose":11.09,"amount":4.35091072E8,"volume":393824.0,"transactionNum":19403,"bp1":11.03,"bp2":11.02,"bp3":11.01,"bp4":11.0,"bp5":10.99,"bv1":9493.0,"bv2":10548.0,"bv3":10617.0,"bv4":17181.0,"bv5":12014.0,"sp1":11.04,"sp2":11.05,"sp3":11.06,"sp4":11.07,"sp5":11.08,"sv1":397.0,"sv2":4132.0,"sv3":2210.0,"sv4":3571.0,"sv5":6065.0}]
```


### 5. 买卖五档-历史数据

**描述**: 获取盘中买卖五档历史数据，盘后更新数据。

**标签**: Tick数据

**权限要求**: 白银版、黄金版、至尊版

**接口地址**: `http://api.xtick.top/doc/order/history`

**请求示例**:
```
http://api.xtick.top/doc/order/history?type=1&code=000001&tradeDate=2026-05-16&token=123456789
```

**输入参数**:

| 参数名 | 类型 | 必填 | 取值范围 | 说明 |
|--------|------|------|----------|------|
| type | int | 否 | 1-沪深京A股，2-沪深指数，3-港股，4-ETF基金，5-沪深可转债 | 标的类型 |
| code | String | 否 | - | 股票代码，仅支持单个股票获取 |
| tradeDate | String | 否 | - | 交易日期 |
| token | String | 是 | - | 登录网站获取token |

**输出参数**:

| 参数名 | 类型 | 说明 |
|--------|------|------|
| type | int | 标的类别 |
| code | String | 股票代码 |
| time | long | 交易时间 |
| lastPrice | float | 最新价 |
| open | float | 开盘价 |
| high | float | 最高价 |
| low | float | 最低价 |
| lastClose | float | 前收盘价 |
| amount | float | 成交总额 |
| volume | float | 成交总量（手） |
| transactionNum | int | 成交笔数，部分交易所填充该字段，实时数据中该字段为0 |
| bp1 | float | 买一价 |
| bp2 | float | 买二价 |
| bp3 | float | 买三价 |
| bp4 | float | 买四价 |
| bp5 | float | 买五价 |
| bv1 | float | 买一量 |
| bv2 | float | 买二量 |
| bv3 | float | 买三量 |
| bv4 | float | 买四量 |
| bv5 | float | 买五量 |
| sp1 | float | 卖一价 |
| sp2 | float | 卖二价 |
| sp3 | float | 卖三价 |
| sp4 | float | 卖四价 |
| sp5 | float | 卖五价 |
| sv1 | float | 卖一量 |
| sv2 | float | 卖二量 |
| sv3 | float | 卖三量 |
| sv4 | float | 卖四量 |
| sv5 | float | 卖五量 |

**返回示例**:
```json
[{"type":1,"code":"000001","time":1770345690000,"lastPrice":11.03,"open":11.08,"high":11.14,"low":10.99,"lastClose":11.09,"amount":4.35091072E8,"volume":393824.0,"transactionNum":19403,"bp1":11.03,"bp2":11.02,"bp3":11.01,"bp4":11.0,"bp5":10.99,"bv1":9493.0,"bv2":10548.0,"bv3":10617.0,"bv4":17181.0,"bv5":12014.0,"sp1":11.04,"sp2":11.05,"sp3":11.06,"sp4":11.07,"sp5":11.08,"sv1":397.0,"sv2":4132.0,"sv3":2210.0,"sv4":3571.0,"sv5":6065.0}]
```


### 6. 成交统计-实时接口

**描述**: 按交易日，获取全市场成交额统计，包括科创板、创业板、北证、沪深两市等成交额统计。
注意：如果需要获取盘中实时行情数据，tradeDate参数填写当天交易日日期即可。

**标签**: 热点数据

**权限要求**: 白银版、黄金版、至尊版

**接口地址**: `http://api.xtick.top/doc/order/amount`

**请求示例**:
```
http://api.xtick.top/doc/order/amount?tradeDate=2026-05-16&token=123456789
```

**输入参数**:

| 参数名 | 类型 | 必填 | 取值范围 | 说明 |
|--------|------|------|----------|------|
| tradeDate | String | 否 | - | 交易日期 |
| token | String | 是 | - | 登录网站获取token |

**输出参数**:

| 参数名 | 类型 | 说明 |
|--------|------|------|
| time | long | 交易时间 |
| szcje | long | 深圳成交额 |
| shcje | long | 上海成交额 |
| bjcje | long | 北京成交额 |
| hscje | long | 两市成交额 |
| hsjcje | long | 三市成交额 |
| cybcje | long | 创业板成交额 |
| kcbcje | long | 科创板成交额 |
| up | int | 股票总上涨数 |
| down | int | 股票总下跌数 |
| fair | int | 股票总持平 |

**返回示例**:
```json
{'time':1773716576000,'szcje':709438865408,'shcje':531375423488,'bjcje':8807928832,'hscje':1240814288896,'hsjcje':1249622217728,'cybcje':319980535808,'kcbcje':113974116352,'up':2153,'down':3148,'fair':189}
```


---

## 3. 核心数据接口

核心数据接口


### 1. 竞价数据-实时接口

**描述**: 获取沪深京股票交易日盘中实时竞价数据，竞价时间段：9:15-9:25。每次调用接口返回最新竞价数据。

**标签**: 竞价数据

**权限要求**: 黄金版、至尊版

**接口地址**: `http://api.xtick.top/doc/core/bidtime`

**请求示例**:
```
http://api.xtick.top/doc/core/bidtime?type=1&code=000001,000002&option=&token=123456789
```

**输入参数**:

| 参数名 | 类型 | 必填 | 取值范围 | 说明 |
|--------|------|------|----------|------|
| type | int | 否 | 1-沪深京A股 | 标的类型 |
| code | String | 否 | - | 股票代码。code取值为000001，则表示获取单个股票数据；code取值为000001,000002，则表示批量获取获取，股票数量最大为50个；code取值为all，则表示全推全市场数据。 |
| option | String | 否 | - | option可选参数，为json字符串。如果不需要过滤和排序功能，可以忽略该参数。如果使用GET方式带上option参数请求，由于option字符串中带有特殊字符，需要对option参数先进行URL编码。URL在线编码工具网址：https://www.bejson.com/enc/urlencode/。比如常见的两种场景：
场景一：当天全市场股票竞价，按未成交额排序，从大到小，取前100条。
{"sort":"noe","asc":0,"limit":100}
场景二：当天全市场股票竞价，过滤出来当天竞价涨幅5个点以上且竞价额大于等于1000万的个股，结果数据按未成交额排序，从大到小，取前100条。
{"filter":"jjzf>5;jje>=10000000","sort":"noe","asc":0,"limit":100} |
| token | String | 是 | - | 登录网站获取token |

**输出参数**:

| 参数名 | 类型 | 说明 |
|--------|------|------|
| type | int | 标的类别 |
| code | String | 股票代码 |
| time | long | 交易时间 |
| seq | int | bar顺序 |
| price | float | 最新价 |
| close | float | 前收盘价 |
| jjzf | float | 竞价涨幅 |
| jjl | long | 竞价量 |
| jje | long | 竞价金额 |
| nol | long | 未匹配量 |
| noe | long | 未匹配金额 |
| trend | int | 交易方向（-1：主动性卖，0：中性盘，1：主动性买） |

**返回示例**:
```json
[{"type":1,"code":"000001","seq":0,"time":1773710700000,"price":10.91,"close":10.92,"jjzf":-0.09,"jjl":5034,"jje":5492100,"nol":1184,"noe":1291744,"trend":-1}]
```


### 2. 核心指标-实时接口

**描述**: 获取沪深京股票交易日盘中实时指标数据，包括涨速、换手率、市盈率、市净率、涨幅、均价、涨停板等数据。
如果需要一次调用，获取全市场的数据，请参考【量化因子-实时接口】。

**标签**: 量化因子

**权限要求**: 黄金版、至尊版

**接口地址**: `http://api.xtick.top/doc/core/time`

**请求示例**:
```
http://api.xtick.top/doc/core/time?type=1&code=000001&field=x001,x002,x003,x004,x005,x006,x007,x008,x009,x010&token=123456789
```

**输入参数**:

| 参数名 | 类型 | 必填 | 取值范围 | 说明 |
|--------|------|------|----------|------|
| type | int | 否 | 1-沪深京A股 | 标的类型 |
| code | String | 否 | - | 股票代码，仅支持单个股票获取，不支持批量参数，不支持ALL参数。如果需要一次调用获取全市场的数据，请参考【量化因子接口】-【量化因子-实时接口】。 |
| field | String | 否 | - | 需要返回字段。多个字段之间用英文逗号分割，单次请求不超过10个字段。 |
| token | String | 是 | - | 登录网站获取token |

**输出参数**:

| 参数名 | 类型 | 说明 |
|--------|------|------|
| x001 | long | 时间戳 |
| x002 | float | 昨收价 |
| x003 | float | 现价 |
| x004 | float | 开盘价 |
| x005 | float | 最高价 |
| x006 | float | 最低价 |
| x007 | int | 总手 |
| x008 | float | 总金额 |
| x009 | float | 量比 |
| x010 | String | 股票代码 |
| x011 | String | 股票名称 |
| x012 | float | 买入价 |
| x013 | float | 卖出价 |
| x014 | float | 买一量 |
| x015 | float | 卖一量 |
| x016 | float | 涨跌 |
| x017 | float | 振幅（%） |
| x018 | float | 涨停价 |
| x019 | float | 跌停价 |
| x020 | int | 涨停板或者跌停板，-1跌停板，1：涨停板 |
| x021 | float | 均价 |
| x022 | float | 现均差（%） |
| x023 | float | 涨速 |
| x024 | float | 1分钟涨速 |
| x025 | float | 2分钟涨速 |
| x026 | float | 3分钟涨速 |
| x027 | float | 4分钟涨速 |
| x028 | float | 5分钟涨速 |
| x029 | float | 涨幅（%） |
| x030 | float | 3日涨幅（%） |
| x031 | float | 5日涨幅（%） |
| x032 | float | 10日涨幅（%） |
| x033 | float | 20日涨幅（%） |
| x034 | float | 30日涨幅（%） |
| x035 | float | 60日涨幅（%） |
| x036 | float | 今年涨幅（%） |
| x037 | float | 换手率（实际） |
| x038 | float | 换手率 |
| x039 | float | 5日换手率 |
| x040 | float | 10日换手率 |
| x041 | float | 20日换手率 |
| x042 | float | 市盈率（静） |
| x043 | float | 市盈率（动） |
| x044 | float | 市盈率（TTM） |
| x045 | String | 最新报告期 |
| x046 | float | 流通市值 |
| x047 | float | 总市值 |
| x048 | float | 市净率 |
| x049 | float | 每股经营活动现金流量 |
| x050 | float | 每股净资产 |
| x051 | float | 基本每股收益 |
| x052 | float | 稀释每股收益 |
| x053 | float | 每股未分配利润 |
| x054 | float | 每股资本公积金 |
| x055 | float | 扣非每股收益 |
| x056 | float | 净资产收益率 |
| x057 | float | 销售毛利率 |
| x058 | float | 主营收入同比增长 |
| x059 | float | 净利润同比增长 |
| x060 | float | 归属于母公司所有者的净利润同比增长 |
| x061 | float | 扣非净利润同比增长 |
| x062 | float | 加权净资产收益率 |
| x063 | float | 摊薄净资产收益率 |
| x064 | float | 毛利率 |
| x065 | float | 净利率 |
| x066 | float | 预收款 / 营业收入 |
| x067 | float | 销售现金流 / 营业收入 |
| x068 | float | 资产负债比率 |
| x069 | float | 存货周转率 |
| x070 | float | 股东总数 |
| x071 | float | A股东户数 |
| x072 | float | B股东户数 |
| x073 | float | H股东户数 |
| x074 | float | 总股本 |
| x075 | float | 已上市流通A股 |


### 3. 除权变更数据

**描述**: 股票除权除息历史数据，可以获取有复权变化的股票数据。可以按单个股票获取个股除权除息历史记录，也可以使用all参数，获取全市场的股票除权除息数据。
应用场景：想增量更新每天的前复权数据，不想把全市场股票数据的日k线数据都更新一次，那么就可以使用除权变更数据接口。先通过该接口获取当天有复权变更的股票，再将有复权变化的股票的日K线重新拉取一次即可。

**标签**: 其它数据

**权限要求**: 黄金版、至尊版

**接口地址**: `http://api.xtick.top/doc/core/chuquan`

**请求示例**:
```
http://api.xtick.top/doc/core/chuquan?type=1&code=000001&startDate=2026-05-16&endDate=2026-05-16&token=123456789
```

**输入参数**:

| 参数名 | 类型 | 必填 | 取值范围 | 说明 |
|--------|------|------|----------|------|
| type | int | 否 | 1-沪深京A股，4-ETF基金 | 标的类型 |
| code | String | 否 | - | 股票代码。code取值为000001，则表示获取单个股票数据；code取值为all，且startDate和endDate必须是同一天，则表示获取某个交易日内的全市场股票数据。注意：不支持多个股票批量获取获取。 |
| startDate | String | 否 | - | 开始日期 |
| endDate | String | 否 | - | 结束日期 |
| token | String | 是 | - | 登录网站获取token |

**输出参数**:

| 参数名 | 类型 | 说明 |
|--------|------|------|
| type | int | 标的类别 |
| code | String | 股票代码 |
| time | long | 交易时间 |

**返回示例**:
```json
[{"type":1,"code":"300717","time":1770307200000}]
```


### 4. 停牌数据

**描述**: 停牌股票历史数据，盘后更新。
可以按单个股票获取个股停牌历史记录，也可以使用all参数，获取全市场股票的停牌数据。

**标签**: 其它数据

**权限要求**: 黄金版、至尊版

**接口地址**: `http://api.xtick.top/doc/core/tingpai`

**请求示例**:
```
http://api.xtick.top/doc/core/tingpai?type=1&code=000001&startDate=2026-05-16&endDate=2026-05-16&token=123456789
```

**输入参数**:

| 参数名 | 类型 | 必填 | 取值范围 | 说明 |
|--------|------|------|----------|------|
| type | int | 否 | 1-沪深京A股 | 标的类型 |
| code | String | 否 | - | 股票代码。code取值为000001，则表示获取单个股票数据；code取值为all，且startDate和endDate必须是同一天，则表示获取某个交易日内的全市场股票数据。注意：不支持多个股票批量获取获取。 |
| startDate | String | 否 | - | 开始日期 |
| endDate | String | 否 | - | 结束日期 |
| token | String | 是 | - | 登录网站获取token |

**输出参数**:

| 参数名 | 类型 | 说明 |
|--------|------|------|
| type | int | 标的类别 |
| code | String | 股票代码 |
| flag | int | 0:all，1：退市 |
| name | String | 股票名称 |
| startTime | long | 开始时间 |
| endTime | long | 结束时间 |

**返回示例**:
```json
[{"type":1,"code":"920305","time":1770307200000}]
```


### 5. ST数据

**描述**: ST股票历史数据，数据从2022年3月开始，盘后更新。
可以按单个股票获取个股ST历史记录，也可以使用all参数，获取全市场的ST股票数据。

**标签**: 其它数据

**权限要求**: 黄金版、至尊版

**接口地址**: `http://api.xtick.top/doc/core/st`

**请求示例**:
```
http://api.xtick.top/doc/core/st?type=1&code=000001&startDate=2026-05-16&endDate=2026-05-16&token=123456789
```

**输入参数**:

| 参数名 | 类型 | 必填 | 取值范围 | 说明 |
|--------|------|------|----------|------|
| type | int | 否 | 1-沪深京A股 | 标的类型 |
| code | String | 否 | - | 股票代码。code取值为000001，则表示获取单个股票数据；code取值为all，且startDate和endDate必须是同一天，则表示获取某个交易日内的全市场股票数据。注意：不支持多个股票批量获取获取。 |
| startDate | String | 否 | - | 开始日期 |
| endDate | String | 否 | - | 结束日期 |
| token | String | 是 | - | 登录网站获取token |

**输出参数**:

| 参数名 | 类型 | 说明 |
|--------|------|------|
| type | int | 标的类别 |
| code | String | 股票代码 |
| time | long | 交易时间 |
| name | String | 股票名称 |

**返回示例**:
```json
[{"type":1,"code":"000004","time":1773590400000,"name":"*ST国华"}]
```


### 6. 涨跌停价格

**描述**: 获取股票涨跌停历史数据，盘后更新。如果需要盘中实时获取涨停板、跌停板、炸板数据，请参考[连板天梯-实时接口]
可以按单个股票获取个股涨跌停历史记录，也可以使用all参数，获取全市场股票的涨跌停数据。

**标签**: 其它数据

**权限要求**: 黄金版、至尊版

**接口地址**: `http://api.xtick.top/doc/core/price`

**请求示例**:
```
http://api.xtick.top/doc/core/price?type=1&code=000001&fq=1&startDate=2026-05-16&endDate=2026-05-16&token=123456789
```

**输入参数**:

| 参数名 | 类型 | 必填 | 取值范围 | 说明 |
|--------|------|------|----------|------|
| type | int | 否 | 1-沪深京A股 | 标的类型 |
| code | String | 否 | - | 股票代码。code取值为000001，则表示获取单个股票数据；code取值为all，且startDate和endDate必须是同一天，则表示获取某个交易日内的全市场股票数据。注意：不支持多个股票批量获取获取。 |
| fq | String | 否 | 1-不复权，2-前复权，3-后复权，4-等比前复权，5-等比后复权 | 复权类型 |
| startDate | String | 否 | - | 开始日期 |
| endDate | String | 否 | - | 结束日期 |
| token | String | 是 | - | 登录网站获取token |

**输出参数**:

| 参数名 | 类型 | 说明 |
|--------|------|------|
| type | int | 标的类别 |
| code | String | 股票代码 |
| time | long | 交易时间 |
| open | float | 开盘价 |
| close | float | 收盘价 |
| high | float | 最高价 |
| low | float | 最低价 |
| volume | float | 成交量 |
| amount | float | 成交额 |
| preClose | float | 前收盘价 |
| maxPrice | float | 最高价 |
| minPrice | float | 最低价 |
| mark | int | 涨停板 -1为跌停板，1为涨停板 |

**返回示例**:
```json
[{"type":0,"code":"000001","time":1770912000000,"open":10.96,"close":10.91,"high":10.99,"low":10.9,"volume":555047.0,"amount":6.0750138E8,"preClose":10.96,"maxPrice":12.06,"minPrice":9.86,"mark":0}]
```


### 7. 分笔数据

**描述**: 股票分时成交数据接口，盘后更新。

**标签**: Tick数据

**权限要求**: 黄金版、至尊版

**接口地址**: `http://api.xtick.top/doc/core/fenbi`

**请求示例**:
```
http://api.xtick.top/doc/core/fenbi?type=1&code=000001&tradeDate=2026-05-16&token=123456789
```

**输入参数**:

| 参数名 | 类型 | 必填 | 取值范围 | 说明 |
|--------|------|------|----------|------|
| type | int | 否 | 1-沪深京A股 | 标的类型 |
| code | String | 否 | - | 股票代码，仅支持单个股票获取，不支持批量参数，不支持ALL参数。 |
| tradeDate | String | 否 | - | 开始日期 |
| token | String | 是 | - | 登录网站获取token |

**输出参数**:

| 参数名 | 类型 | 说明 |
|--------|------|------|
| time | long | 交易时间 |
| price | float | 成交价 |
| zd | float | 涨跌额 |
| cjl | long | 成交量 |
| cje | long | 成交额 |
| num | long | 成交笔数 |
| trend | int | 交易方向（-1：主动性卖，0：中性盘，1：主动性买） |

**返回示例**:
```json
[{"time":1770343236000,"price":11.07,"zd":-0.02,"cjl":1581,"cje":1750167,"num":10,"trend":1}]
```


### 8. 分价数据

**描述**: 股票分价成交数据接口，盘后更新。

**标签**: 其它数据

**权限要求**: 黄金版、至尊版

**接口地址**: `http://api.xtick.top/doc/core/fenjia`

**请求示例**:
```
http://api.xtick.top/doc/core/fenjia?type=1&code=000001&tradeDate=2026-05-16&token=123456789
```

**输入参数**:

| 参数名 | 类型 | 必填 | 取值范围 | 说明 |
|--------|------|------|----------|------|
| type | int | 否 | 1-沪深京A股 | 标的类型 |
| code | String | 否 | - | 股票代码，仅支持单个股票获取，不支持批量参数，不支持ALL参数。 |
| tradeDate | String | 否 | - | 开始日期 |
| token | String | 是 | - | 登录网站获取token |

**输出参数**:

| 参数名 | 类型 | 说明 |
|--------|------|------|
| price | float | 成交价 |
| np | long | 内盘 |
| wp | long | 外盘 |
| cjl | long | 成交量 |
| ratio | float | 占比 |

**返回示例**:
```json
[{"price":10.99,"np":5442,"wp":0,"cjl":5442,"ratio":0.75}]
```


---

## 4. 短线热点接口

短线热点接口


### 1. 连板天梯-实时接口

**描述**: 获取沪深京股票交易日盘中盘中涨停板、跌停板、炸板数据。梯队完整度是超短的重要指标。盘中实时更新。如果获取盘中实时数据，则tradeDate参数设置为当天交易日。

**标签**: 热点数据

**权限要求**: 至尊版

**接口地址**: `http://api.xtick.top/doc/hot/board`

**请求示例**:
```
http://api.xtick.top/doc/hot/board?type=1&flag=1&tradeDate=2026-05-16&token=123456789
```

**输入参数**:

| 参数名 | 类型 | 必填 | 取值范围 | 说明 |
|--------|------|------|----------|------|
| type | int | 否 | 1-沪深京A股，4-ETF基金 | 标的类型 |
| flag | int | 否 | 1-涨停板，2-跌停板，3-炸板 | 上榜类型 |
| tradeDate | String | 否 | - | 交易日期 |
| token | String | 是 | - | 登录网站获取token |

**输出参数**:

| 参数名 | 类型 | 说明 |
|--------|------|------|
| type | int | 标的类别 |
| code | String | 股票代码 |
| time | long | 交易时间 |
| flag | int | 状态，0：炸板 1：涨停 2：跌停 |
| count | int | 炸板次数 |
| ftime | long | 首次触及条件时间（涨停或者跌停板） |
| days | int | 连续涨停天数 |
| preClose | float | 昨日收盘价 |
| price | float | 最新价(收盘价) |
| volume | float | 成交量（手） |
| amount | float | 成交额（元） |
| bv1 | float | 买一量，若为涨停板，则是封单量 |
| sv1 | float | 卖一量，若为跌停板，则是封单量 |
| bp1 | float | 买一价，若为涨停板，则封单额=bv1*bp1 |
| sp1 | float | 卖一价，若为跌停板，则封单额=sv1*sp1 |
| updateTime | long | 更新操作时间戳 |

**返回示例**:
```json
[{"type":1,"code":"002565","time":1773676800000,"flag":1,"count":2,"ftime":1773710100000,"days":1,"preClose":13.4,"price":14.74,"volume":895743.0,"amount":1.31777626E9,"bv1":105238.0,"sv1":0.0,"bp1":14.74,"sp1":0.0,"updateTime":1773730800000}]
```


### 2. 市场情绪-实时接口

**描述**: 市场情绪，短线选手复盘必备工具。如果获取盘中实时数据，则tradeDate参数设置为当天交易日。

**标签**: 热点数据

**权限要求**: 至尊版

**接口地址**: `http://api.xtick.top/doc/hot/emotion`

**请求示例**:
```
http://api.xtick.top/doc/hot/emotion?type=1&tradeDate=2026-05-16&token=123456789
```

**输入参数**:

| 参数名 | 类型 | 必填 | 取值范围 | 说明 |
|--------|------|------|----------|------|
| type | int | 否 | 1-沪深京A股，4-ETF基金 | 标的类型 |
| tradeDate | String | 否 | - | 交易日期 |
| token | String | 是 | - | 登录网站获取token |

**输出参数**:

| 参数名 | 类型 | 说明 |
|--------|------|------|
| time | long | 交易时间 |
| lastTime | long | 前一个交易日时间 |
| ztlbnum | int | 连板涨停家数 |
| ztnum | int | 涨停家数 |
| dtnum | long | 跌停家数 |
| zbnum | int | 炸板家数 |
| mhigh | int | 连板高度 |
| jjl | float | 总打板成功率 |
| zbl | float | 总打板炸板率 |
| jj1t2 | float | 一进二晋级率 |
| jj2t3 | float | 二进三晋级率 |
| jj3t4 | float | 三进四晋级率 |
| jj4t5 | float | 四进五晋级率 |
| jj5tn | float | 五板以上晋级率 |
| zb1t2 | float | 一进二炸板率 |
| zb2t3 | float | 二进三炸板率 |
| zb3t4 | float | 三进四炸板率 |
| zb4t5 | float | 四进五炸板率 |
| zb5tn | float | 五板以上炸板率 |
| updateTime | long | 更新操作时间戳 |

**返回示例**:
```json
[{"time":1776960000000,"lastTime":1776873600000,"ztlbnum":10,"ztnum":66,"dtnum":37,"zbnum":29,"mhigh":6,"jjl":17.86,"zbl":10.71,"jj1t2":13.64,"jj2t3":50.0,"jj3t4":25.0,"jj4t5":0.0,"jj5tn":50.0,"zb1t2":9.09,"zb2t3":0.0,"zb3t4":25.0,"zb4t5":50.0,"zb5tn":0.0,"updateTime":1778299312228}]
```


### 3. 资金流向-实时接口

**描述**: 获取沪深京股票交易日盘中资金流数据。盘中实时更新。
如果需要获取盘中实时行情数据，startDate、endDate参数填写当天交易日日期即可。

**标签**: 热点数据

**权限要求**: 至尊版

**接口地址**: `http://api.xtick.top/doc/hot/money`

**请求示例**:
```
http://api.xtick.top/doc/hot/money?type=1&code=000001&startDate=2026-05-16&endDate=2026-05-16&token=123456789
```

**输入参数**:

| 参数名 | 类型 | 必填 | 取值范围 | 说明 |
|--------|------|------|----------|------|
| type | int | 否 | 1-沪深京A股，4-ETF基金 | 标的类型 |
| code | String | 否 | - | 股票代码。code取值为000001，则表示获取单个股票数据；code取值为all，且startDate和endDate必须是同一天，则表示获取某个交易日内的全市场股票数据。注意：不支持多个股票批量获取获取。 |
| startDate | String | 否 | - | 开始日期 |
| endDate | String | 否 | - | 结束日期 |
| token | String | 是 | - | 登录网站获取token |

**输出参数**:

| 参数名 | 类型 | 说明 |
|--------|------|------|
| type | int | 标的类别 |
| code | String | 股票代码 |
| time | long | 交易时间 |
| buyNumber | int | 主买单总单数 |
| sellNumber | int | 主卖单总单数 |
| transactionNumber | int | 成交笔数 |
| buyMostAmount | float | 主买特大单成交额 |
| buyBigAmount | float | 主买大单成交额 |
| buyMediumAmount | float | 主买中单成交额 |
| buySmallAmount | float | 主买小单成交额 |
| sellMostAmount | float | 主卖特大单成交额 |
| sellBigAmount | float | 主卖大单成交额 |
| sellMediumAmount | float | 主卖中单成交额 |
| sellSmallAmount | float | 主卖小单成交额 |
| buyMostVolume | int | 主买特大单成交量 |
| buyBigVolume | int | 主买大单成交量 |
| buyMediumVolume | int | 主买中单成交量 |
| buySmallVolume | int | 主买小单成交量 |
| sellMostVolume | int | 主卖特大单成交量 |
| sellBigVolume | int | 主卖大单成交量 |
| sellMediumVolume | int | 主卖中单成交量 |
| sellSmallVolume | int | 主卖小单成交量 |
| updateTime | long | 更新操作时间戳 |

**返回示例**:
```json
[{"type":1,"code":"000001","time":1773676800000,"buyNumber":2114,"sellNumber":2357,"transactionNumber":0,"buyMostAmount":3.87070016E8,"buyBigAmount":1.82859008E8,"buyMediumAmount":9.1263296E7,"buySmallAmount":1.12925E7,"sellMostAmount":2.09356E8,"sellBigAmount":1.91654E8,"sellMediumAmount":9.9635696E7,"sellSmallAmount":1.44357E7,"buyMostVolume":351138,"buyBigVolume":165628,"buyMediumVolume":82658,"buySmallVolume":10225,"sellMostVolume":189892,"sellBigVolume":173728,"sellMediumVolume":90304,"sellSmallVolume":13081,"updateTime":1773730800000}]
```


### 4. 竞价数据-历史接口

**描述**: 竞价历史数据，该接口仅保留集合竞价期间的最后一条竞价数据和开盘数据。

**标签**: 竞价数据

**权限要求**: 黄金版、至尊版

**接口地址**: `http://api.xtick.top/doc/hot/bidhistory`

**请求示例**:
```
http://api.xtick.top/doc/hot/bidhistory?type=1&code=000001&=1&startDate=2026-05-16&endDate=2026-05-16&token=123456789
```

**输入参数**:

| 参数名 | 类型 | 必填 | 取值范围 | 说明 |
|--------|------|------|----------|------|
| type | int | 否 | 1-沪深京A股 | 标的类型 |
| code | String | 否 | - | 股票代码。code取值为000001，则表示获取单个股票数据；code取值为all，且startDate和endDate必须是同一天，则表示获取某个交易日内的全市场股票数据。注意：不支持多个股票批量获取获取。 |
|  | String | 否 | 0-开盘数据，1-集合竞价最后一条数据 | seq 序列号，seq为0，表示集合竞价最后一条数据，即9:25分竞价数据，seq为1，表示集合竞价倒数第二条数据。目前seq取值就0和1，记录了集合竞价阶段最后两条数据。
一般来讲，seq为0是开盘数据，seq为1是集合竞价最后一条数据。 |
| startDate | String | 否 | - | 开始日期 |
| endDate | String | 否 | - | 结束日期 |
| token | String | 是 | - | 登录网站获取token |

**输出参数**:

| 参数名 | 类型 | 说明 |
|--------|------|------|
| type | int | 标的类别 |
| code | String | 股票代码 |
| time | long | 交易时间 |
| seq | int | bar顺序 |
| price | float | 最新价 |
| close | float | 前收盘价 |
| jjzf | float | 竞价涨幅 |
| jjl | long | 竞价量 |
| jje | long | 竞价金额 |
| nol | long | 未匹配量 |
| noe | long | 未匹配金额 |
| trend | int | 交易方向（-1：主动性卖，0：中性盘，1：主动性买） |

**返回示例**:
```json
[{"type":1,"code":"000001","seq":1,"time":1770307200000,"price":11.08,"close":11.09,"jjzf":-0.09,"jjl":8018,"jje":8883944,"nol":1469,"noe":1627652,"trend":-1}]
```


### 5. 竞价详情-实时接口

**描述**: 开盘集合竞价阶段，个股的所有竞价信息。当天竞价完成后，9:25更新完数据。请求一次，获取交易日当天全市场竞价详情数据，请参考【核心指标接口】-【增量更新】

**标签**: 竞价数据

**权限要求**: 黄金版、至尊版

**接口地址**: `http://api.xtick.top/doc/hot/biddetail`

**请求示例**:
```
http://api.xtick.top/doc/hot/biddetail?type=1&code=000001&tradeDate=2026-05-16&token=123456789
```

**输入参数**:

| 参数名 | 类型 | 必填 | 取值范围 | 说明 |
|--------|------|------|----------|------|
| type | int | 否 | 1-沪深京A股 | 标的类型 |
| code | String | 否 | - | 股票代码，仅支持单个股票获取，不支持批量参数，不支持ALL参数。 |
| tradeDate | String | 否 | - | 开始日期 |
| token | String | 是 | - | 登录网站获取token |

**输出参数**:

| 参数名 | 类型 | 说明 |
|--------|------|------|
| type | int | 标的类别 |
| code | String | 股票代码 |
| time | long | 交易时间 |
| price | float | 最新价 |
| close | float | 前收盘价 |
| jjzf | float | 竞价涨幅 |
| jjl | long | 竞价量 |
| jje | long | 竞价金额 |
| nol | long | 未匹配量 |
| noe | long | 未匹配金额 |
| trend | int | 交易方向（-1：主动性卖，0：中性盘，1：主动性买） |

**返回示例**:
```json
[{"type":1,"code":"000001","seq":0,"time":1770340500000,"price":11.06,"close":11.09,"jjzf":-0.27,"jjl":43,"jje":47558,"nol":22,"noe":24332,"trend":1}]
```


### 6. 新闻资讯-实时接口

**描述**: 获取财联社、新浪财经、格隆汇、华尔街见闻、凤凰网、同花顺、东方财富、雪球等主流金融平台资讯信息，跟随市场热点、核心。盘中实时更新。

**标签**: 热点数据

**权限要求**: 至尊版

**接口地址**: `http://api.xtick.top/doc/hot/news`

**请求示例**:
```
http://api.xtick.top/doc/hot/news?minutes=60&tradeDate=2026-05-16&token=123456789
```

**输入参数**:

| 参数名 | 类型 | 必填 | 取值范围 | 说明 |
|--------|------|------|----------|------|
| minutes | int | 否 | - | minutes 最新消息时间范围，表示获取几分钟内的最新消息。
注意：
minutes取值大于0，按按照minutes参数，获取minutes时间内的最新消息。
minutes取值为0，按按照tradeDate参数，获取历史数据。 |
| tradeDate | String | 否 | - | 交易日期 |
| token | String | 是 | - | 登录网站获取token |

**输出参数**:

| 参数名 | 类型 | 说明 |
|--------|------|------|
| platId | int | 新闻源 |
| id | String | 新闻id |
| platName | String | 新闻源名称 |
| time | long | 新闻时间 |
| title | String | 新闻标题 |
| info | String | 新闻摘要 |
| url | String | 新闻链接 |

**返回示例**:
```json
[{"platId":3,"id":"202603273686458580","platName":"东方财富-快讯","time":1774573380000,"title":"分析称以箭式拦截弹已消耗超8成","info":"【分析称以箭式拦截弹已消耗超8成】有分析指出，伊朗导弹打击效率提升背后，可能与多种因素有关：其中之一就是美国和以色列拦截弹库存的急剧减少。美国佩恩公共政策研究所发布的最新数据显示，美以伊冲突爆发以来，以色列的箭-2、箭-3和美国的“萨德”拦截弹是各型弹药中消耗速度最快的。其中，箭-2、箭-3拦截弹在战事开始前的库存量为150枚；冲突爆发后的16天内，已经消耗了122枚，消耗比例超过八成。","url":"https://finance.eastmoney.com/a/202603273686458580.html"}]
```


### 7. 日内分时-实时接口

**描述**: 获取股票盘中日内分时数据，保留了价格在每个时间点的变化细节，股价全天的波动轨迹。盘中实时更新。

**标签**: 热点数据, 分钟数据

**权限要求**: 至尊版

**接口地址**: `http://api.xtick.top/doc/hot/timekline`

**请求示例**:
```
http://api.xtick.top/doc/hot/timekline?type=1&code=000001&token=123456789
```

**输入参数**:

| 参数名 | 类型 | 必填 | 取值范围 | 说明 |
|--------|------|------|----------|------|
| type | int | 否 | 1-沪深京A股，2-沪深指数，3-港股，4-ETF基金，5-沪深可转债 | 标的类型 |
| code | String | 否 | - | 股票代码，仅支持单个股票获取，不支持批量参数，不支持ALL参数。 |
| token | String | 是 | - | 登录网站获取token |

**输出参数**:

| 参数名 | 类型 | 说明 |
|--------|------|------|
| type | int | 标的类型 |
| code | String | 股票代码 |
| time | long | 交易日期 |
| open | float | 开盘价 |
| close | float | 收盘价 |
| high | float | 最高价 |
| low | float | 最低价 |
| volume | float | 成交量（手） |
| amount | float | 成交额（元） |
| preClose | float | 昨日收盘价 |

**返回示例**:
```json
[{"type":1,"code":"000001","time":1774508400000,"open":10.94,"close":10.94,"high":10.94,"low":10.94,"volume":6388.0,"amount":6988416.0,"preClose":10.94}]
```


### 8. 概念板块成分股数据

**描述**: 获取概念板块、地域板块、行业板块数据，以及概念板块下对应的成分股数据。

**标签**: 其它数据

**权限要求**: 至尊版

**接口地址**: `http://api.xtick.top/doc/hot/bk`

**请求示例**:
```
http://api.xtick.top/doc/hot/bk?symbol=sw1&token=123456789
```

**输入参数**:

| 参数名 | 类型 | 必填 | 取值范围 | 说明 |
|--------|------|------|----------|------|
| symbol | String | 否 | sw1-申万一级行业划分，sw2-申万二级行业划分，sw3-申万三级行业划分，zjh1-证监会一级行业划分，zjh2-证监会二级行业划分，bdy1-一级地域划分，bdy1-二级地域划分，ahy-行业划分，afg-风格划分，agn-A概念划分，bgn-B概念划分，cgn-C概念划分 | 概念板块分类 |
| token | String | 是 | - | 登录网站获取token |

**输出参数**:

| 参数名 | 类型 | 说明 |
|--------|------|------|
| bkname | String | 概念板块名称 |
| stocks | List | 该概念板块下包含的成分股数据 |

**返回示例**:
```json
[{"bkname":"电子信息","stocks":["000021","000032","000062","000063","000066"]}]
```


### 9. 股票关联概念板块数据

**描述**: 获取个股关联的概念板块、地域板块、行业板块数据。

**标签**: 其它数据

**权限要求**: 至尊版

**接口地址**: `http://api.xtick.top/doc/hot/gainian`

**请求示例**:
```
http://api.xtick.top/doc/hot/gainian?code=000001&token=123456789
```

**输入参数**:

| 参数名 | 类型 | 必填 | 取值范围 | 说明 |
|--------|------|------|----------|------|
| code | String | 否 | - | 股票代码，仅支持单个股票获取。 |
| token | String | 是 | - | 登录网站获取token |

**输出参数**:

| 参数名 | 类型 | 说明 |
|--------|------|------|
| symbol | String | 概念板块的分类 |
| bknames | List | 该分类下包含的概念板块名称 |

**返回示例**:
```json
[{"symbol":"zjh1","bknames":["金融业"]},{"symbol":"zjh2","bknames":["货币金融服务"]}]
```


### 10. 增量更新

**描述**: 提供交易日当天全市场增量数据的更新，是收盘后的历史数据，是为了方便大家能快速的获取全市场数据，节省大家获取数据的时间，不需要按个股循环获取数据。
这个接口单次获取数据量非常大，请不要频繁获取，该接口严格限流。请求间隔时间大致按照3N秒计算，请求次数越多，间隔时间需要越长。
注意：这个接口大家成功获取一次数据，就是完整的当天全市场数据，且该数据不会再更新，所以不需要重复请求，多次请求返回的数据都是完全一样。


**标签**: 竞价数据, 分钟数据

**权限要求**: 黄金版、至尊版

**接口地址**: `http://api.xtick.top/doc/hot/dayupdate`

**请求示例**:
```
http://api.xtick.top/doc/hot/dayupdate?dataType=bid&symbol=bj&tradeDate=2026-05-16&token=123456789
```

**输入参数**:

| 参数名 | 类型 | 必填 | 取值范围 | 说明 |
|--------|------|------|----------|------|
| dataType | String | 否 | bid-全市场竞价详情数据，1m-全市场1分钟数据 | 数据类别，枚举类型如下：
bid-全市场竞价详情数据，需要携带symbol参数，按照股票分类获取竞价数据。预计9:26分可获取。是竞价结束后的历史数据，数据不会再变化，请勿重复请求。字段定义参考【竞价数据】
1m-全市场1分钟数据，需要携带symbol参数，按照股票分类获取分钟数据。是收盘后的1分钟历史数据，数据不会再变化，请勿重复请求。字段定义参考【k线数据】。 |
| symbol | String | 否 | index-主要指数，etf-场内基金，cyb-深交所创业板股票，kcb-上交所科创板股票，bj-北交所股票，szm-深交所主板股票，shm-上交所主板股票 | 股票分类 |
| tradeDate | String | 否 | - | 开始日期 |
| token | String | 是 | - | 登录网站获取token |

**输出参数**:

| 参数名 | 类型 | 说明 |
|--------|------|------|
| data | Object | 根据dataType数据类别参数，返回对应接口的数据。 - bid - 全市场竞价详情数据，字段定义参考【竞价数据】 - 1m - 全市场1分钟数据，字段定义参考【k线数据】。 |


---

## 8. 量化因子接口

量化因子接口


### 1. 量化因子-实时接口

**描述**: 获取沪深京股票交易日盘中因子指标数据，实时推送，包括涨速、换手率、市盈率、市净率等。支持数据全推。
如需要其它其它量化因子或者定制参数，请联系我们定制开发指标，指标支持实时推送。

**标签**: 量化因子

**权限要求**: 量化版

**接口地址**: `http://api.xtick.top/doc/quant/data`

**请求示例**:
```
http://api.xtick.top/doc/quant/data?type=1&field=x001,x002,x003,x004,x005,x006,x007,x008,x009,x010&token=123456789
```

**输入参数**:

| 参数名 | 类型 | 必填 | 取值范围 | 说明 |
|--------|------|------|----------|------|
| type | int | 否 | 1-沪深京A股 | 标的类型 |
| field | String | 否 | - | 需要返回字段。返回全部字段填写all；多个字段之间用英文逗号分割，单次请求不超过10个字段。 |
| token | String | 是 | - | 登录网站获取token |

**输出参数**:

| 参数名 | 类型 | 说明 |
|--------|------|------|
| code | String | 股票代码 |
| time | long | 交易时间 |
| x001 | float | 昨收价 |
| x002 | float | 最新价 |
| x003 | float | 开盘价 |
| x004 | float | 最高价 |
| x005 | float | 最低价 |
| x006 | float | 成交量 |
| x007 | float | 成交额 |
| x008 | float | 涨跌 |
| x009 | float | 振幅 |
| x010 | float | 均价 |
| x011 | float | 现均差 |
| x012 | float | 涨停价 |
| x013 | float | 跌停价 |
| x014 | int | 涨停板 -1为跌停板，1为涨停板 |
| x015 | float | 涨速 |
| x016 | float | 1分钟涨速 |
| x017 | float | 2分钟涨速 |
| x018 | float | 3分钟涨速 |
| x019 | float | 4分钟涨速 |
| x020 | float | 5分钟涨速 |
| x021 | float | 静态市盈率 |
| x022 | float | 动态市盈率 |
| x023 | float | TTM市盈率 |
| x024 | float | 总市值 |
| x025 | float | 流通市值 |
| x026 | float | 市净率 |
| x027 | float | 换手率 |
| x028 | float | 实际换手率 |
| x029 | float | 涨幅 |
| x030 | float | 5日涨幅 |
| x031 | float | 10日涨幅 |
| x032 | float | 20日涨幅 |
| x033 | float | 5日均线 |
| x034 | float | 10日均线 |
| x035 | float | 20日均线 |
| x036 | float | 30日均线 |
| x037 | float | 60日均线 |
| x038 | float | 120日均线 |
| x039 | float | MACD-DIF(12,26,9) |
| x040 | float | MACD-DEA(12,26,9) |
| x041 | float | MACD-MACD(12,26,9) |
| x042 | float | KDJ-K(9,3,3) |
| x043 | float | KDJ-D(9,3,3) |
| x044 | float | KDJ-J(9,3,3) |
| x045 | float | RSI(12) |
| x046 | float | WR(10) |
| x047 | float | CCI(14) |
| x048 | float | BOLL-UP(20,2) |
| x049 | float | BOLL-MID(20,2) |
| x050 | float | BOLL-LOW(20,2) |
| x051 | float | BIAS(6) |
| x052 | float | BIAS(12) |
| x053 | float | BIAS(24) |
| x054 | float | 量比 |
| x055 | float | 3日量比 |
| x056 | float | 10日量比 |
| x057 | float | 20日量比 |

**返回示例**:
```json
{"seqNo":0,"type":1,"market":null,"period":"quant.data","data":{"code":["000001","000002"],"x001":[10.0,10.1]}}
```


### 2. 量化因子-历史接口

**描述**: 获取沪深京股票交易日盘中因子指标历史数据，盘后更新。

**标签**: 量化因子

**权限要求**: 量化版

**接口地址**: `http://api.xtick.top/doc/quant/history`

**请求示例**:
```
http://api.xtick.top/doc/quant/history?tradeDate=2026-05-16&token=123456789
```

**输入参数**:

| 参数名 | 类型 | 必填 | 取值范围 | 说明 |
|--------|------|------|----------|------|
| tradeDate | String | 否 | - | 交易日期 |
| token | String | 是 | - | 登录网站获取token |

**输出参数**:

| 参数名 | 类型 | 说明 |
|--------|------|------|
| code | String | 股票代码 |
| time | long | 交易时间 |
| x001 | float | 昨收价 |
| x002 | float | 最新价 |
| x003 | float | 开盘价 |
| x004 | float | 最高价 |
| x005 | float | 最低价 |
| x006 | float | 成交量 |
| x007 | float | 成交额 |
| x008 | float | 涨跌 |
| x009 | float | 振幅 |
| x010 | float | 均价 |
| x011 | float | 现均差 |
| x012 | float | 涨停价 |
| x013 | float | 跌停价 |
| x014 | int | 涨停板 -1为跌停板，1为涨停板 |
| x015 | float | 涨速 |
| x016 | float | 1分钟涨速 |
| x017 | float | 2分钟涨速 |
| x018 | float | 3分钟涨速 |
| x019 | float | 4分钟涨速 |
| x020 | float | 5分钟涨速 |
| x021 | float | 静态市盈率 |
| x022 | float | 动态市盈率 |
| x023 | float | TTM市盈率 |
| x024 | float | 总市值 |
| x025 | float | 流通市值 |
| x026 | float | 市净率 |
| x027 | float | 换手率 |
| x028 | float | 实际换手率 |
| x029 | float | 涨幅 |
| x030 | float | 5日涨幅 |
| x031 | float | 10日涨幅 |
| x032 | float | 20日涨幅 |
| x033 | float | 5日均线 |
| x034 | float | 10日均线 |
| x035 | float | 20日均线 |
| x036 | float | 30日均线 |
| x037 | float | 60日均线 |
| x038 | float | 120日均线 |
| x039 | float | MACD-DIF(12,26,9) |
| x040 | float | MACD-DEA(12,26,9) |
| x041 | float | MACD-MACD(12,26,9) |
| x042 | float | KDJ-K(9,3,3) |
| x043 | float | KDJ-D(9,3,3) |
| x044 | float | KDJ-J(9,3,3) |
| x045 | float | RSI(12) |
| x046 | float | WR(10) |
| x047 | float | CCI(14) |
| x048 | float | BOLL-UP(20,2) |
| x049 | float | BOLL-MID(20,2) |
| x050 | float | BOLL-LOW(20,2) |
| x051 | float | BIAS(6) |
| x052 | float | BIAS(12) |
| x053 | float | BIAS(24) |
| x054 | float | 量比 |
| x055 | float | 3日量比 |
| x056 | float | 10日量比 |
| x057 | float | 20日量比 |

**返回示例**:
```json
{"seqNo":0,"type":1,"market":null,"period":"quant.data","data":{"code":["000001","000002"],"x001":[10.0,10.1]}}
```


---

## 9. 金融指标接口

金融指标接口


### 2. ad指标

**描述**: AD指标（Accumulation/Distribution）是一种用于量化分析股票、期货或其他金融资产的技术指标。它主要用于判断资金流向以及市场买卖压力的变化，进而辅助投资者做出买卖决策。

**标签**: 金融指标

**权限要求**: 青铜版、白银版、黄金版、至尊版

**接口地址**: `http://api.xtick.top/doc/ad`

**请求示例**:
```
http://api.xtick.top/doc/ad?type=1&code=000001&fq=1&period=1d&startDate=2026-05-16&endDate=2026-05-16&scale=3&round=1&token=123456789
```

**输入参数**:

| 参数名 | 类型 | 必填 | 取值范围 | 说明 |
|--------|------|------|----------|------|
| type | int | 否 | 1-沪深京A股，2-沪深指数，3-港股，4-ETF基金，5-沪深可转债 | 标的类型 |
| code | String | 否 | - | 股票代码，仅支持单个股票获取，不支持批量参数，不支持ALL参数。 |
| fq | String | 否 | 1-不复权，2-前复权，3-后复权，4-等比前复权，5-等比后复权 | 复权类型 |
| period | String | 否 | 1m-1分钟线，5m-5分钟线，15m-15分钟线，30m-30分钟线，1h-1小时线，1d-日线，1w-周线，1mon-月线，1q-季度线，1y-年线 | K线周期 |
| startDate | String | 否 | - | 开始日期 |
| endDate | String | 否 | - | 结束日期 |
| scale | int | 否 | - | 精度（保留几位小数位） |
| round | int | 否 | - | 舍入方式，参考：https://www.cnblogs.com/yingchen/p/5459501.html |
| token | String | 是 | - | 登录网站获取token |

**输出参数**:

| 参数名 | 类型 | 说明 |
|--------|------|------|
| time | long | 交易日期 |
| open | float | 开盘价 |
| close | float | 收盘价 |
| high | float | 最高价 |
| low | float | 最低价 |
| volume | float | 成交量（手） |
| amount | float | 成交额（元） |
| output | float | 指标计算值 |


### 3. adosc指标

**描述**: ADOSC指标是一种技术分析指标，全称为累积/派发指标（Accumulation/Distribution Oscillator）。它用于衡量市场买卖压力的强度和方向。

**标签**: 金融指标

**权限要求**: 青铜版、白银版、黄金版、至尊版

**接口地址**: `http://api.xtick.top/doc/adosc`

**请求示例**:
```
http://api.xtick.top/doc/adosc?type=1&code=000001&fq=1&period=1d&startDate=2026-05-16&endDate=2026-05-16&optInFastPeriod=3&optInSlowPeriod=10&scale=3&round=1&token=123456789
```

**输入参数**:

| 参数名 | 类型 | 必填 | 取值范围 | 说明 |
|--------|------|------|----------|------|
| type | int | 否 | 1-沪深京A股，2-沪深指数，3-港股，4-ETF基金，5-沪深可转债 | 标的类型 |
| code | String | 否 | - | 股票代码，仅支持单个股票获取，不支持批量参数，不支持ALL参数。 |
| fq | String | 否 | 1-不复权，2-前复权，3-后复权，4-等比前复权，5-等比后复权 | 复权类型 |
| period | String | 否 | 1m-1分钟线，5m-5分钟线，15m-15分钟线，30m-30分钟线，1h-1小时线，1d-日线，1w-周线，1mon-月线，1q-季度线，1y-年线 | K线周期 |
| startDate | String | 否 | - | 开始日期 |
| endDate | String | 否 | - | 结束日期 |
| optInFastPeriod | int | 否 | - | 快速移动平均线周期 |
| optInSlowPeriod | int | 否 | - | 慢速移动平均线周期 |
| scale | int | 否 | - | 精度（保留几位小数位） |
| round | int | 否 | - | 舍入方式，参考：https://www.cnblogs.com/yingchen/p/5459501.html |
| token | String | 是 | - | 登录网站获取token |

**输出参数**:

| 参数名 | 类型 | 说明 |
|--------|------|------|
| time | long | 交易日期 |
| open | float | 开盘价 |
| close | float | 收盘价 |
| high | float | 最高价 |
| low | float | 最低价 |
| volume | float | 成交量（手） |
| amount | float | 成交额（元） |
| output | float | 指标计算值 |


### 5. adx指标

**描述**: ADX指标（Average Directional Movement Index）是一种技术分析指标，用于衡量市场趋势的强弱程度。它由J. Welles Wilder于1978年提出，并广泛应用于股票、期货和外汇市场等。

**标签**: 金融指标

**权限要求**: 青铜版、白银版、黄金版、至尊版

**接口地址**: `http://api.xtick.top/doc/adx`

**请求示例**:
```
http://api.xtick.top/doc/adx?type=1&code=000001&fq=1&period=1d&startDate=2026-05-16&endDate=2026-05-16&optInTimePeriod=14&scale=3&round=1&token=123456789
```

**输入参数**:

| 参数名 | 类型 | 必填 | 取值范围 | 说明 |
|--------|------|------|----------|------|
| type | int | 否 | 1-沪深京A股，2-沪深指数，3-港股，4-ETF基金，5-沪深可转债 | 标的类型 |
| code | String | 否 | - | 股票代码，仅支持单个股票获取，不支持批量参数，不支持ALL参数。 |
| fq | String | 否 | 1-不复权，2-前复权，3-后复权，4-等比前复权，5-等比后复权 | 复权类型 |
| period | String | 否 | 1m-1分钟线，5m-5分钟线，15m-15分钟线，30m-30分钟线，1h-1小时线，1d-日线，1w-周线，1mon-月线，1q-季度线，1y-年线 | K线周期 |
| startDate | String | 否 | - | 开始日期 |
| endDate | String | 否 | - | 结束日期 |
| optInTimePeriod | int | 否 | - | 移动平均线周期 |
| scale | int | 否 | - | 精度（保留几位小数位） |
| round | int | 否 | - | 舍入方式，参考：https://www.cnblogs.com/yingchen/p/5459501.html |
| token | String | 是 | - | 登录网站获取token |

**输出参数**:

| 参数名 | 类型 | 说明 |
|--------|------|------|
| time | long | 交易日期 |
| open | float | 开盘价 |
| close | float | 收盘价 |
| high | float | 最高价 |
| low | float | 最低价 |
| volume | float | 成交量（手） |
| amount | float | 成交额（元） |
| output | float | 指标计算值 |


### 6. adxr指标

**描述**: ADXR指标（Average Directional Movement Index Rating）是根据ADX指标（Average Directional Index）计算得出的一个指标，用于衡量市场趋势的强度。它是J. Welles Wilder开发的技术分析工具之一。

**标签**: 金融指标

**权限要求**: 青铜版、白银版、黄金版、至尊版

**接口地址**: `http://api.xtick.top/doc/adxr`

**请求示例**:
```
http://api.xtick.top/doc/adxr?type=1&code=000001&fq=1&period=1d&startDate=2026-05-16&endDate=2026-05-16&optInTimePeriod=14&scale=3&round=1&token=123456789
```

**输入参数**:

| 参数名 | 类型 | 必填 | 取值范围 | 说明 |
|--------|------|------|----------|------|
| type | int | 否 | 1-沪深京A股，2-沪深指数，3-港股，4-ETF基金，5-沪深可转债 | 标的类型 |
| code | String | 否 | - | 股票代码，仅支持单个股票获取，不支持批量参数，不支持ALL参数。 |
| fq | String | 否 | 1-不复权，2-前复权，3-后复权，4-等比前复权，5-等比后复权 | 复权类型 |
| period | String | 否 | 1m-1分钟线，5m-5分钟线，15m-15分钟线，30m-30分钟线，1h-1小时线，1d-日线，1w-周线，1mon-月线，1q-季度线，1y-年线 | K线周期 |
| startDate | String | 否 | - | 开始日期 |
| endDate | String | 否 | - | 结束日期 |
| optInTimePeriod | int | 否 | - | 移动平均线周期 |
| scale | int | 否 | - | 精度（保留几位小数位） |
| round | int | 否 | - | 舍入方式，参考：https://www.cnblogs.com/yingchen/p/5459501.html |
| token | String | 是 | - | 登录网站获取token |

**输出参数**:

| 参数名 | 类型 | 说明 |
|--------|------|------|
| time | long | 交易日期 |
| open | float | 开盘价 |
| close | float | 收盘价 |
| high | float | 最高价 |
| low | float | 最低价 |
| volume | float | 成交量（手） |
| amount | float | 成交额（元） |
| output | float | 指标计算值 |


### 7. apo指标

**描述**: APO（Absolute Price Oscillator）指标是一种技术分析指标，用于衡量股票价格的变动幅度。它计算了两个不同时间周期的移动平均线之间的差异，从而提供了价格变动的绝对数值。

**标签**: 金融指标

**权限要求**: 青铜版、白银版、黄金版、至尊版

**接口地址**: `http://api.xtick.top/doc/apo`

**请求示例**:
```
http://api.xtick.top/doc/apo?type=1&code=000001&fq=1&period=1d&startDate=2026-05-16&endDate=2026-05-16&inReal=2&optInFastPeriod=12&optInSlowPeriod=26&optInMAType=1&scale=3&round=1&token=123456789
```

**输入参数**:

| 参数名 | 类型 | 必填 | 取值范围 | 说明 |
|--------|------|------|----------|------|
| type | int | 否 | 1-沪深京A股，2-沪深指数，3-港股，4-ETF基金，5-沪深可转债 | 标的类型 |
| code | String | 否 | - | 股票代码，仅支持单个股票获取，不支持批量参数，不支持ALL参数。 |
| fq | String | 否 | 1-不复权，2-前复权，3-后复权，4-等比前复权，5-等比后复权 | 复权类型 |
| period | String | 否 | 1m-1分钟线，5m-5分钟线，15m-15分钟线，30m-30分钟线，1h-1小时线，1d-日线，1w-周线，1mon-月线，1q-季度线，1y-年线 | K线周期 |
| startDate | String | 否 | - | 开始日期 |
| endDate | String | 否 | - | 结束日期 |
| inReal | int | 否 | 1-开盘价，2-收盘价，3-最高价，4-最低价，5-成交量，6-成交额 | 数据标签 |
| optInFastPeriod | int | 否 | - | 快速移动平均线周期 |
| optInSlowPeriod | int | 否 | - | 慢速移动平均线周期 |
| optInMAType | int | 否 | 1-简单移动平均线SMA，2-指数移动平均线EMA，3-加权移动平均线WMA，4-双指数移动平均线DEMA，5-三重指数移动平均线TEMA，6-三重移动平均线TRIMA，7-考夫曼自适应移动平均线KAMA，8-自适应移动平均线MAMA，9-三重移动平均线T3 | 移动平均线类型 |
| scale | int | 否 | - | 精度（保留几位小数位） |
| round | int | 否 | - | 舍入方式，参考：https://www.cnblogs.com/yingchen/p/5459501.html |
| token | String | 是 | - | 登录网站获取token |

**输出参数**:

| 参数名 | 类型 | 说明 |
|--------|------|------|
| time | long | 交易日期 |
| open | float | 开盘价 |
| close | float | 收盘价 |
| high | float | 最高价 |
| low | float | 最低价 |
| volume | float | 成交量（手） |
| amount | float | 成交额（元） |
| output | float | 指标计算值 |


### 8. aroon指标

**描述**: AROON指标的中文名称是“阿隆指标”，该指标是一种技术分析指标，用于衡量价格趋势的强度和趋势的方向。阿隆指标由两条线组成：上升线（Aroon Up）和下降线（Aroon Down）。

**标签**: 金融指标

**权限要求**: 青铜版、白银版、黄金版、至尊版

**接口地址**: `http://api.xtick.top/doc/aroon`

**请求示例**:
```
http://api.xtick.top/doc/aroon?type=1&code=000001&fq=1&period=1d&startDate=2026-05-16&endDate=2026-05-16&optInTimePeriod=14&scale=3&round=1&token=123456789
```

**输入参数**:

| 参数名 | 类型 | 必填 | 取值范围 | 说明 |
|--------|------|------|----------|------|
| type | int | 否 | 1-沪深京A股，2-沪深指数，3-港股，4-ETF基金，5-沪深可转债 | 标的类型 |
| code | String | 否 | - | 股票代码，仅支持单个股票获取，不支持批量参数，不支持ALL参数。 |
| fq | String | 否 | 1-不复权，2-前复权，3-后复权，4-等比前复权，5-等比后复权 | 复权类型 |
| period | String | 否 | 1m-1分钟线，5m-5分钟线，15m-15分钟线，30m-30分钟线，1h-1小时线，1d-日线，1w-周线，1mon-月线，1q-季度线，1y-年线 | K线周期 |
| startDate | String | 否 | - | 开始日期 |
| endDate | String | 否 | - | 结束日期 |
| optInTimePeriod | int | 否 | - | 移动平均线周期 |
| scale | int | 否 | - | 精度（保留几位小数位） |
| round | int | 否 | - | 舍入方式，参考：https://www.cnblogs.com/yingchen/p/5459501.html |
| token | String | 是 | - | 登录网站获取token |

**输出参数**:

| 参数名 | 类型 | 说明 |
|--------|------|------|
| time | long | 交易日期 |
| open | float | 开盘价 |
| close | float | 收盘价 |
| high | float | 最高价 |
| low | float | 最低价 |
| volume | float | 成交量（手） |
| amount | float | 成交额（元） |
| down | float | 指标计算值 |
| up | float | 指标计算值 |


### 9. aroonosc指标

**描述**: AroonOsc指标的名称是Aroon Oscillator（阿隆振荡器），它是Aroon指标的衍生指标。

**标签**: 金融指标

**权限要求**: 青铜版、白银版、黄金版、至尊版

**接口地址**: `http://api.xtick.top/doc/aroonosc`

**请求示例**:
```
http://api.xtick.top/doc/aroonosc?type=1&code=000001&fq=1&period=1d&startDate=2026-05-16&endDate=2026-05-16&optInTimePeriod=14&scale=3&round=1&token=123456789
```

**输入参数**:

| 参数名 | 类型 | 必填 | 取值范围 | 说明 |
|--------|------|------|----------|------|
| type | int | 否 | 1-沪深京A股，2-沪深指数，3-港股，4-ETF基金，5-沪深可转债 | 标的类型 |
| code | String | 否 | - | 股票代码，仅支持单个股票获取，不支持批量参数，不支持ALL参数。 |
| fq | String | 否 | 1-不复权，2-前复权，3-后复权，4-等比前复权，5-等比后复权 | 复权类型 |
| period | String | 否 | 1m-1分钟线，5m-5分钟线，15m-15分钟线，30m-30分钟线，1h-1小时线，1d-日线，1w-周线，1mon-月线，1q-季度线，1y-年线 | K线周期 |
| startDate | String | 否 | - | 开始日期 |
| endDate | String | 否 | - | 结束日期 |
| optInTimePeriod | int | 否 | - | 移动平均线周期 |
| scale | int | 否 | - | 精度（保留几位小数位） |
| round | int | 否 | - | 舍入方式，参考：https://www.cnblogs.com/yingchen/p/5459501.html |
| token | String | 是 | - | 登录网站获取token |

**输出参数**:

| 参数名 | 类型 | 说明 |
|--------|------|------|
| time | long | 交易日期 |
| open | float | 开盘价 |
| close | float | 收盘价 |
| high | float | 最高价 |
| low | float | 最低价 |
| volume | float | 成交量（手） |
| amount | float | 成交额（元） |
| output | float | 指标计算值 |


### 12. atr指标

**描述**: ATR称为真实波动幅度指标，英文名称为Average True Range。ATR指标是一种衡量市场波动性的技术指标，它通过计算一定时间内的价格波动幅度，来评估市场的波动性程度。

**标签**: 金融指标

**权限要求**: 青铜版、白银版、黄金版、至尊版

**接口地址**: `http://api.xtick.top/doc/atr`

**请求示例**:
```
http://api.xtick.top/doc/atr?type=1&code=000001&fq=1&period=1d&startDate=2026-05-16&endDate=2026-05-16&optInTimePeriod=14&scale=3&round=1&token=123456789
```

**输入参数**:

| 参数名 | 类型 | 必填 | 取值范围 | 说明 |
|--------|------|------|----------|------|
| type | int | 否 | 1-沪深京A股，2-沪深指数，3-港股，4-ETF基金，5-沪深可转债 | 标的类型 |
| code | String | 否 | - | 股票代码，仅支持单个股票获取，不支持批量参数，不支持ALL参数。 |
| fq | String | 否 | 1-不复权，2-前复权，3-后复权，4-等比前复权，5-等比后复权 | 复权类型 |
| period | String | 否 | 1m-1分钟线，5m-5分钟线，15m-15分钟线，30m-30分钟线，1h-1小时线，1d-日线，1w-周线，1mon-月线，1q-季度线，1y-年线 | K线周期 |
| startDate | String | 否 | - | 开始日期 |
| endDate | String | 否 | - | 结束日期 |
| optInTimePeriod | int | 否 | - | 移动平均线周期 |
| scale | int | 否 | - | 精度（保留几位小数位） |
| round | int | 否 | - | 舍入方式，参考：https://www.cnblogs.com/yingchen/p/5459501.html |
| token | String | 是 | - | 登录网站获取token |

**输出参数**:

| 参数名 | 类型 | 说明 |
|--------|------|------|
| time | long | 交易日期 |
| open | float | 开盘价 |
| close | float | 收盘价 |
| high | float | 最高价 |
| low | float | 最低价 |
| volume | float | 成交量（手） |
| amount | float | 成交额（元） |
| output | float | 指标计算值 |


### 14. bbands指标

**描述**: BBANDS称为布林带，英文名称为Bollinger Bands。布林带是一种基于统计学原理的技术分析指标，由约翰·布林格（John Bollinger）于20世纪80年代提出。它通过计算价格的标准差来确定价格的高低波动区间，并以此构建出上下两条通道线，从而帮助判断价格的超买和超卖情况。

**标签**: 金融指标

**权限要求**: 青铜版、白银版、黄金版、至尊版

**接口地址**: `http://api.xtick.top/doc/bbands`

**请求示例**:
```
http://api.xtick.top/doc/bbands?type=1&code=000001&fq=1&period=1d&startDate=2026-05-16&endDate=2026-05-16&inReal=2&optInTimePeriod=20&optInNbDevUp=2&optInNbDevDn=2&optInMAType=1&scale=3&round=1&token=123456789
```

**输入参数**:

| 参数名 | 类型 | 必填 | 取值范围 | 说明 |
|--------|------|------|----------|------|
| type | int | 否 | 1-沪深京A股，2-沪深指数，3-港股，4-ETF基金，5-沪深可转债 | 标的类型 |
| code | String | 否 | - | 股票代码，仅支持单个股票获取，不支持批量参数，不支持ALL参数。 |
| fq | String | 否 | 1-不复权，2-前复权，3-后复权，4-等比前复权，5-等比后复权 | 复权类型 |
| period | String | 否 | 1m-1分钟线，5m-5分钟线，15m-15分钟线，30m-30分钟线，1h-1小时线，1d-日线，1w-周线，1mon-月线，1q-季度线，1y-年线 | K线周期 |
| startDate | String | 否 | - | 开始日期 |
| endDate | String | 否 | - | 结束日期 |
| inReal | int | 否 | 1-开盘价，2-收盘价，3-最高价，4-最低价，5-成交量，6-成交额 | 数据标签 |
| optInTimePeriod | int | 否 | - | 移动平均线周期 |
| optInNbDevUp | double | 否 | - | 上轨道线的标准偏差倍数 |
| optInNbDevDn | double | 否 | - | 下轨道线的标准偏差倍数 |
| optInMAType | int | 否 | 1-简单移动平均线SMA，2-指数移动平均线EMA，3-加权移动平均线WMA，4-双指数移动平均线DEMA，5-三重指数移动平均线TEMA，6-三重移动平均线TRIMA，7-考夫曼自适应移动平均线KAMA，8-自适应移动平均线MAMA，9-三重移动平均线T3 | 移动平均线类型 |
| scale | int | 否 | - | 精度（保留几位小数位） |
| round | int | 否 | - | 舍入方式，参考：https://www.cnblogs.com/yingchen/p/5459501.html |
| token | String | 是 | - | 登录网站获取token |

**输出参数**:

| 参数名 | 类型 | 说明 |
|--------|------|------|
| time | long | 交易日期 |
| open | float | 开盘价 |
| close | float | 收盘价 |
| high | float | 最高价 |
| low | float | 最低价 |
| volume | float | 成交量（手） |
| amount | float | 成交额（元） |
| up | float | 指标计算值 |
| mid | float | 指标计算值 |
| down | float | 指标计算值 |


### 16. bop指标

**描述**: BOP指标的名称是Balance of Power，也称为能量平衡指标。

**标签**: 金融指标

**权限要求**: 青铜版、白银版、黄金版、至尊版

**接口地址**: `http://api.xtick.top/doc/bop`

**请求示例**:
```
http://api.xtick.top/doc/bop?type=1&code=000001&fq=1&period=1d&startDate=2026-05-16&endDate=2026-05-16&scale=3&round=1&token=123456789
```

**输入参数**:

| 参数名 | 类型 | 必填 | 取值范围 | 说明 |
|--------|------|------|----------|------|
| type | int | 否 | 1-沪深京A股，2-沪深指数，3-港股，4-ETF基金，5-沪深可转债 | 标的类型 |
| code | String | 否 | - | 股票代码，仅支持单个股票获取，不支持批量参数，不支持ALL参数。 |
| fq | String | 否 | 1-不复权，2-前复权，3-后复权，4-等比前复权，5-等比后复权 | 复权类型 |
| period | String | 否 | 1m-1分钟线，5m-5分钟线，15m-15分钟线，30m-30分钟线，1h-1小时线，1d-日线，1w-周线，1mon-月线，1q-季度线，1y-年线 | K线周期 |
| startDate | String | 否 | - | 开始日期 |
| endDate | String | 否 | - | 结束日期 |
| scale | int | 否 | - | 精度（保留几位小数位） |
| round | int | 否 | - | 舍入方式，参考：https://www.cnblogs.com/yingchen/p/5459501.html |
| token | String | 是 | - | 登录网站获取token |

**输出参数**:

| 参数名 | 类型 | 说明 |
|--------|------|------|
| time | long | 交易日期 |
| open | float | 开盘价 |
| close | float | 收盘价 |
| high | float | 最高价 |
| low | float | 最低价 |
| volume | float | 成交量（手） |
| amount | float | 成交额（元） |
| output | float | 指标计算值 |


### 17. cci指标

**描述**: CCI指标的全称是“商品通道指数”（Commodity Channel Index），它是一种技术分析指标，用于评估商品（或其他金融资产）的价格波动情况和超买超卖状态。

**标签**: 金融指标

**权限要求**: 青铜版、白银版、黄金版、至尊版

**接口地址**: `http://api.xtick.top/doc/cci`

**请求示例**:
```
http://api.xtick.top/doc/cci?type=1&code=000001&fq=1&period=1d&startDate=2026-05-16&endDate=2026-05-16&optInTimePeriod=14&scale=3&round=1&token=123456789
```

**输入参数**:

| 参数名 | 类型 | 必填 | 取值范围 | 说明 |
|--------|------|------|----------|------|
| type | int | 否 | 1-沪深京A股，2-沪深指数，3-港股，4-ETF基金，5-沪深可转债 | 标的类型 |
| code | String | 否 | - | 股票代码，仅支持单个股票获取，不支持批量参数，不支持ALL参数。 |
| fq | String | 否 | 1-不复权，2-前复权，3-后复权，4-等比前复权，5-等比后复权 | 复权类型 |
| period | String | 否 | 1m-1分钟线，5m-5分钟线，15m-15分钟线，30m-30分钟线，1h-1小时线，1d-日线，1w-周线，1mon-月线，1q-季度线，1y-年线 | K线周期 |
| startDate | String | 否 | - | 开始日期 |
| endDate | String | 否 | - | 结束日期 |
| optInTimePeriod | int | 否 | - | 移动平均线周期 |
| scale | int | 否 | - | 精度（保留几位小数位） |
| round | int | 否 | - | 舍入方式，参考：https://www.cnblogs.com/yingchen/p/5459501.html |
| token | String | 是 | - | 登录网站获取token |

**输出参数**:

| 参数名 | 类型 | 说明 |
|--------|------|------|
| time | long | 交易日期 |
| open | float | 开盘价 |
| close | float | 收盘价 |
| high | float | 最高价 |
| low | float | 最低价 |
| volume | float | 成交量（手） |
| amount | float | 成交额（元） |
| output | float | 指标计算值 |


### 80. cmo指标

**描述**: CMO指标的名称：Chande Momentum Oscillator（CMO，钱德动量振荡器）。

**标签**: 金融指标

**权限要求**: 青铜版、白银版、黄金版、至尊版

**接口地址**: `http://api.xtick.top/doc/cmo`

**请求示例**:
```
http://api.xtick.top/doc/cmo?type=1&code=000001&fq=1&period=1d&startDate=2026-05-16&endDate=2026-05-16&inReal=2&optInTimePeriod=14&scale=3&round=1&token=123456789
```

**输入参数**:

| 参数名 | 类型 | 必填 | 取值范围 | 说明 |
|--------|------|------|----------|------|
| type | int | 否 | 1-沪深京A股，2-沪深指数，3-港股，4-ETF基金，5-沪深可转债 | 标的类型 |
| code | String | 否 | - | 股票代码，仅支持单个股票获取，不支持批量参数，不支持ALL参数。 |
| fq | String | 否 | 1-不复权，2-前复权，3-后复权，4-等比前复权，5-等比后复权 | 复权类型 |
| period | String | 否 | 1m-1分钟线，5m-5分钟线，15m-15分钟线，30m-30分钟线，1h-1小时线，1d-日线，1w-周线，1mon-月线，1q-季度线，1y-年线 | K线周期 |
| startDate | String | 否 | - | 开始日期 |
| endDate | String | 否 | - | 结束日期 |
| inReal | int | 否 | 1-开盘价，2-收盘价，3-最高价，4-最低价，5-成交量，6-成交额 | 数据标签 |
| optInTimePeriod | int | 否 | - | 移动平均线周期 |
| scale | int | 否 | - | 精度（保留几位小数位） |
| round | int | 否 | - | 舍入方式，参考：https://www.cnblogs.com/yingchen/p/5459501.html |
| token | String | 是 | - | 登录网站获取token |

**输出参数**:

| 参数名 | 类型 | 说明 |
|--------|------|------|
| time | long | 交易日期 |
| open | float | 开盘价 |
| close | float | 收盘价 |
| high | float | 最高价 |
| low | float | 最低价 |
| volume | float | 成交量（手） |
| amount | float | 成交额（元） |
| output | float | 指标计算值 |


### 84. dema指标

**描述**: DEMA指标是一种双指数移动平均线，全称为Double Exponential Moving Average。DEMA指标用于平滑价格数据，以便更好地识别价格趋势。

**标签**: 金融指标

**权限要求**: 青铜版、白银版、黄金版、至尊版

**接口地址**: `http://api.xtick.top/doc/dema`

**请求示例**:
```
http://api.xtick.top/doc/dema?type=1&code=000001&fq=1&period=1d&startDate=2026-05-16&endDate=2026-05-16&inReal=2&optInTimePeriod=20&scale=3&round=1&token=123456789
```

**输入参数**:

| 参数名 | 类型 | 必填 | 取值范围 | 说明 |
|--------|------|------|----------|------|
| type | int | 否 | 1-沪深京A股，2-沪深指数，3-港股，4-ETF基金，5-沪深可转债 | 标的类型 |
| code | String | 否 | - | 股票代码，仅支持单个股票获取，不支持批量参数，不支持ALL参数。 |
| fq | String | 否 | 1-不复权，2-前复权，3-后复权，4-等比前复权，5-等比后复权 | 复权类型 |
| period | String | 否 | 1m-1分钟线，5m-5分钟线，15m-15分钟线，30m-30分钟线，1h-1小时线，1d-日线，1w-周线，1mon-月线，1q-季度线，1y-年线 | K线周期 |
| startDate | String | 否 | - | 开始日期 |
| endDate | String | 否 | - | 结束日期 |
| inReal | int | 否 | 1-开盘价，2-收盘价，3-最高价，4-最低价，5-成交量，6-成交额 | 数据标签 |
| optInTimePeriod | int | 否 | - | 移动平均线周期 |
| scale | int | 否 | - | 精度（保留几位小数位） |
| round | int | 否 | - | 舍入方式，参考：https://www.cnblogs.com/yingchen/p/5459501.html |
| token | String | 是 | - | 登录网站获取token |

**输出参数**:

| 参数名 | 类型 | 说明 |
|--------|------|------|
| time | long | 交易日期 |
| open | float | 开盘价 |
| close | float | 收盘价 |
| high | float | 最高价 |
| low | float | 最低价 |
| volume | float | 成交量（手） |
| amount | float | 成交额（元） |
| output | float | 指标计算值 |


### 86. dx指标

**描述**: 动向指标(DMI)，英文名称是Directional Movement Index。动向指标（Dx）是一种技术分析指标，用于衡量市场趋势的强度和方向。它由综合指标（+DI和-DI）计算得出，可以帮助交易者判断市场是上涨趋势、下跌趋势还是盘整。

**标签**: 金融指标

**权限要求**: 青铜版、白银版、黄金版、至尊版

**接口地址**: `http://api.xtick.top/doc/dx`

**请求示例**:
```
http://api.xtick.top/doc/dx?type=1&code=000001&fq=1&period=1d&startDate=2026-05-16&endDate=2026-05-16&optInTimePeriod=20&scale=3&round=1&token=123456789
```

**输入参数**:

| 参数名 | 类型 | 必填 | 取值范围 | 说明 |
|--------|------|------|----------|------|
| type | int | 否 | 1-沪深京A股，2-沪深指数，3-港股，4-ETF基金，5-沪深可转债 | 标的类型 |
| code | String | 否 | - | 股票代码，仅支持单个股票获取，不支持批量参数，不支持ALL参数。 |
| fq | String | 否 | 1-不复权，2-前复权，3-后复权，4-等比前复权，5-等比后复权 | 复权类型 |
| period | String | 否 | 1m-1分钟线，5m-5分钟线，15m-15分钟线，30m-30分钟线，1h-1小时线，1d-日线，1w-周线，1mon-月线，1q-季度线，1y-年线 | K线周期 |
| startDate | String | 否 | - | 开始日期 |
| endDate | String | 否 | - | 结束日期 |
| optInTimePeriod | int | 否 | - | 移动平均线周期 |
| scale | int | 否 | - | 精度（保留几位小数位） |
| round | int | 否 | - | 舍入方式，参考：https://www.cnblogs.com/yingchen/p/5459501.html |
| token | String | 是 | - | 登录网站获取token |

**输出参数**:

| 参数名 | 类型 | 说明 |
|--------|------|------|
| time | long | 交易日期 |
| open | float | 开盘价 |
| close | float | 收盘价 |
| high | float | 最高价 |
| low | float | 最低价 |
| volume | float | 成交量（手） |
| amount | float | 成交额（元） |
| output | float | 指标计算值 |


### 87. ema指标

**描述**: EMA指标是指数移动平均线，全称为Exponential Moving Average。它是一种常用的技术分析工具，用于平滑价格数据并识别趋势。

**标签**: 金融指标

**权限要求**: 青铜版、白银版、黄金版、至尊版

**接口地址**: `http://api.xtick.top/doc/ema`

**请求示例**:
```
http://api.xtick.top/doc/ema?type=1&code=000001&fq=1&period=1d&startDate=2026-05-16&endDate=2026-05-16&inReal=2&optInTimePeriod=20&scale=3&round=1&token=123456789
```

**输入参数**:

| 参数名 | 类型 | 必填 | 取值范围 | 说明 |
|--------|------|------|----------|------|
| type | int | 否 | 1-沪深京A股，2-沪深指数，3-港股，4-ETF基金，5-沪深可转债 | 标的类型 |
| code | String | 否 | - | 股票代码，仅支持单个股票获取，不支持批量参数，不支持ALL参数。 |
| fq | String | 否 | 1-不复权，2-前复权，3-后复权，4-等比前复权，5-等比后复权 | 复权类型 |
| period | String | 否 | 1m-1分钟线，5m-5分钟线，15m-15分钟线，30m-30分钟线，1h-1小时线，1d-日线，1w-周线，1mon-月线，1q-季度线，1y-年线 | K线周期 |
| startDate | String | 否 | - | 开始日期 |
| endDate | String | 否 | - | 结束日期 |
| inReal | int | 否 | 1-开盘价，2-收盘价，3-最高价，4-最低价，5-成交量，6-成交额 | 数据标签 |
| optInTimePeriod | int | 否 | - | 移动平均线周期 |
| scale | int | 否 | - | 精度（保留几位小数位） |
| round | int | 否 | - | 舍入方式，参考：https://www.cnblogs.com/yingchen/p/5459501.html |
| token | String | 是 | - | 登录网站获取token |

**输出参数**:

| 参数名 | 类型 | 说明 |
|--------|------|------|
| time | long | 交易日期 |
| open | float | 开盘价 |
| close | float | 收盘价 |
| high | float | 最高价 |
| low | float | 最低价 |
| volume | float | 成交量（手） |
| amount | float | 成交额（元） |
| output | float | 指标计算值 |


### 95. httrendline指标

**描述**: HTTRENDLINE称为趋势线，英文名称为HTTRENDLINE。该指标是一种基于趋势线的技术分析工具。

**标签**: 金融指标

**权限要求**: 青铜版、白银版、黄金版、至尊版

**接口地址**: `http://api.xtick.top/doc/httrendline`

**请求示例**:
```
http://api.xtick.top/doc/httrendline?type=1&code=000001&fq=1&period=1d&startDate=2026-05-16&endDate=2026-05-16&inReal=2&scale=3&round=1&token=123456789
```

**输入参数**:

| 参数名 | 类型 | 必填 | 取值范围 | 说明 |
|--------|------|------|----------|------|
| type | int | 否 | 1-沪深京A股，2-沪深指数，3-港股，4-ETF基金，5-沪深可转债 | 标的类型 |
| code | String | 否 | - | 股票代码，仅支持单个股票获取，不支持批量参数，不支持ALL参数。 |
| fq | String | 否 | 1-不复权，2-前复权，3-后复权，4-等比前复权，5-等比后复权 | 复权类型 |
| period | String | 否 | 1m-1分钟线，5m-5分钟线，15m-15分钟线，30m-30分钟线，1h-1小时线，1d-日线，1w-周线，1mon-月线，1q-季度线，1y-年线 | K线周期 |
| startDate | String | 否 | - | 开始日期 |
| endDate | String | 否 | - | 结束日期 |
| inReal | int | 否 | 1-开盘价，2-收盘价，3-最高价，4-最低价，5-成交量，6-成交额 | 数据标签 |
| scale | int | 否 | - | 精度（保留几位小数位） |
| round | int | 否 | - | 舍入方式，参考：https://www.cnblogs.com/yingchen/p/5459501.html |
| token | String | 是 | - | 登录网站获取token |

**输出参数**:

| 参数名 | 类型 | 说明 |
|--------|------|------|
| time | long | 交易日期 |
| open | float | 开盘价 |
| close | float | 收盘价 |
| high | float | 最高价 |
| low | float | 最低价 |
| volume | float | 成交量（手） |
| amount | float | 成交额（元） |
| output | float | 指标计算值 |


### 96. kama指标

**描述**: KAMA是考夫曼自适应移动平均线，全称为Kaufman Adaptive Moving Average。是由Perry J. Kaufman开发的一种技术分析工具，用于平滑股价走势并提供趋势信号。

**标签**: 金融指标

**权限要求**: 青铜版、白银版、黄金版、至尊版

**接口地址**: `http://api.xtick.top/doc/kama`

**请求示例**:
```
http://api.xtick.top/doc/kama?type=1&code=000001&fq=1&period=1d&startDate=2026-05-16&endDate=2026-05-16&inReal=2&optInTimePeriod=20&scale=3&round=1&token=123456789
```

**输入参数**:

| 参数名 | 类型 | 必填 | 取值范围 | 说明 |
|--------|------|------|----------|------|
| type | int | 否 | 1-沪深京A股，2-沪深指数，3-港股，4-ETF基金，5-沪深可转债 | 标的类型 |
| code | String | 否 | - | 股票代码，仅支持单个股票获取，不支持批量参数，不支持ALL参数。 |
| fq | String | 否 | 1-不复权，2-前复权，3-后复权，4-等比前复权，5-等比后复权 | 复权类型 |
| period | String | 否 | 1m-1分钟线，5m-5分钟线，15m-15分钟线，30m-30分钟线，1h-1小时线，1d-日线，1w-周线，1mon-月线，1q-季度线，1y-年线 | K线周期 |
| startDate | String | 否 | - | 开始日期 |
| endDate | String | 否 | - | 结束日期 |
| inReal | int | 否 | 1-开盘价，2-收盘价，3-最高价，4-最低价，5-成交量，6-成交额 | 数据标签 |
| optInTimePeriod | int | 否 | - | 移动平均线周期 |
| scale | int | 否 | - | 精度（保留几位小数位） |
| round | int | 否 | - | 舍入方式，参考：https://www.cnblogs.com/yingchen/p/5459501.html |
| token | String | 是 | - | 登录网站获取token |

**输出参数**:

| 参数名 | 类型 | 说明 |
|--------|------|------|
| time | long | 交易日期 |
| open | float | 开盘价 |
| close | float | 收盘价 |
| high | float | 最高价 |
| low | float | 最低价 |
| volume | float | 成交量（手） |
| amount | float | 成交额（元） |
| output | float | 指标计算值 |


### 103. macd指标

**描述**: MACD指标的中文名称为移动平均线收敛/发散指标，英文名称为Moving Average Convergence Divergence。

**标签**: 金融指标

**权限要求**: 青铜版、白银版、黄金版、至尊版

**接口地址**: `http://api.xtick.top/doc/macd`

**请求示例**:
```
http://api.xtick.top/doc/macd?type=1&code=000001&fq=1&period=1d&startDate=2026-05-16&endDate=2026-05-16&inReal=2&optInFastPeriod=26&optInSlowPeriod=12&optInSignalPeriod=9&scale=3&round=1&token=123456789
```

**输入参数**:

| 参数名 | 类型 | 必填 | 取值范围 | 说明 |
|--------|------|------|----------|------|
| type | int | 否 | 1-沪深京A股，2-沪深指数，3-港股，4-ETF基金，5-沪深可转债 | 标的类型 |
| code | String | 否 | - | 股票代码，仅支持单个股票获取，不支持批量参数，不支持ALL参数。 |
| fq | String | 否 | 1-不复权，2-前复权，3-后复权，4-等比前复权，5-等比后复权 | 复权类型 |
| period | String | 否 | 1m-1分钟线，5m-5分钟线，15m-15分钟线，30m-30分钟线，1h-1小时线，1d-日线，1w-周线，1mon-月线，1q-季度线，1y-年线 | K线周期 |
| startDate | String | 否 | - | 开始日期 |
| endDate | String | 否 | - | 结束日期 |
| inReal | int | 否 | 1-开盘价，2-收盘价，3-最高价，4-最低价，5-成交量，6-成交额 | 数据标签 |
| optInFastPeriod | int | 否 | - | 快速移动平均线周期 |
| optInSlowPeriod | int | 否 | - | 慢速移动平均线周期 |
| optInSignalPeriod | int | 否 | - | 信号移动平均线周期 |
| scale | int | 否 | - | 精度（保留几位小数位） |
| round | int | 否 | - | 舍入方式，参考：https://www.cnblogs.com/yingchen/p/5459501.html |
| token | String | 是 | - | 登录网站获取token |

**输出参数**:

| 参数名 | 类型 | 说明 |
|--------|------|------|
| time | long | 交易日期 |
| open | float | 开盘价 |
| close | float | 收盘价 |
| high | float | 最高价 |
| low | float | 最低价 |
| volume | float | 成交量（手） |
| amount | float | 成交额（元） |
| diff | float | 指标计算值 |
| dea | float | 指标计算值 |
| hist | float | 指标计算值 |


### 104. macdext指标

**描述**: MACDEXT指标的中文名称是MACD扩展，英文名称是MACD Extended。MACD扩展是基于移动平均线收敛背离（MACD）指标的一种变种指标。

**标签**: 金融指标

**权限要求**: 青铜版、白银版、黄金版、至尊版

**接口地址**: `http://api.xtick.top/doc/macdext`

**请求示例**:
```
http://api.xtick.top/doc/macdext?type=1&code=000001&fq=1&period=1d&startDate=2026-05-16&endDate=2026-05-16&inReal=2&optInFastPeriod=26&optInFastMAType=1&optInSlowPeriod=12&optInSlowMAType=1&optInSignalPeriod=9&optInSignalMAType=1&scale=3&round=1&token=123456789
```

**输入参数**:

| 参数名 | 类型 | 必填 | 取值范围 | 说明 |
|--------|------|------|----------|------|
| type | int | 否 | 1-沪深京A股，2-沪深指数，3-港股，4-ETF基金，5-沪深可转债 | 标的类型 |
| code | String | 否 | - | 股票代码，仅支持单个股票获取，不支持批量参数，不支持ALL参数。 |
| fq | String | 否 | 1-不复权，2-前复权，3-后复权，4-等比前复权，5-等比后复权 | 复权类型 |
| period | String | 否 | 1m-1分钟线，5m-5分钟线，15m-15分钟线，30m-30分钟线，1h-1小时线，1d-日线，1w-周线，1mon-月线，1q-季度线，1y-年线 | K线周期 |
| startDate | String | 否 | - | 开始日期 |
| endDate | String | 否 | - | 结束日期 |
| inReal | int | 否 | 1-开盘价，2-收盘价，3-最高价，4-最低价，5-成交量，6-成交额 | 数据标签 |
| optInFastPeriod | int | 否 | - | 快速移动平均线周期 |
| optInFastMAType | int | 否 | 1-简单移动平均线SMA，2-指数移动平均线EMA，3-加权移动平均线WMA，4-双指数移动平均线DEMA，5-三重指数移动平均线TEMA，6-三重移动平均线TRIMA，7-考夫曼自适应移动平均线KAMA，8-自适应移动平均线MAMA，9-三重移动平均线T3 | 快速移动平均线类型 |
| optInSlowPeriod | int | 否 | - | 慢速移动平均线周期 |
| optInSlowMAType | int | 否 | 1-简单移动平均线SMA，2-指数移动平均线EMA，3-加权移动平均线WMA，4-双指数移动平均线DEMA，5-三重指数移动平均线TEMA，6-三重移动平均线TRIMA，7-考夫曼自适应移动平均线KAMA，8-自适应移动平均线MAMA，9-三重移动平均线T3 | 慢速移动平均线类型 |
| optInSignalPeriod | int | 否 | - | 信号移动平均线周期 |
| optInSignalMAType | int | 否 | 1-简单移动平均线SMA，2-指数移动平均线EMA，3-加权移动平均线WMA，4-双指数移动平均线DEMA，5-三重指数移动平均线TEMA，6-三重移动平均线TRIMA，7-考夫曼自适应移动平均线KAMA，8-自适应移动平均线MAMA，9-三重移动平均线T3 | 信号移动平均线类型 |
| scale | int | 否 | - | 精度（保留几位小数位） |
| round | int | 否 | - | 舍入方式，参考：https://www.cnblogs.com/yingchen/p/5459501.html |
| token | String | 是 | - | 登录网站获取token |

**输出参数**:

| 参数名 | 类型 | 说明 |
|--------|------|------|
| time | long | 交易日期 |
| open | float | 开盘价 |
| close | float | 收盘价 |
| high | float | 最高价 |
| low | float | 最低价 |
| volume | float | 成交量（手） |
| amount | float | 成交额（元） |
| diff | float | 指标计算值 |
| dea | float | 指标计算值 |
| hist | float | 指标计算值 |


### 105. macdfix指标

**描述**: MACDFIX指标的中文名称为移动平均收敛/背离指标，英文名称为Moving Average Convergence Divergence Fix。

**标签**: 金融指标

**权限要求**: 青铜版、白银版、黄金版、至尊版

**接口地址**: `http://api.xtick.top/doc/macdfix`

**请求示例**:
```
http://api.xtick.top/doc/macdfix?type=1&code=000001&fq=1&period=1d&startDate=2026-05-16&endDate=2026-05-16&inReal=2&optInSignalPeriod=9&scale=3&round=1&token=123456789
```

**输入参数**:

| 参数名 | 类型 | 必填 | 取值范围 | 说明 |
|--------|------|------|----------|------|
| type | int | 否 | 1-沪深京A股，2-沪深指数，3-港股，4-ETF基金，5-沪深可转债 | 标的类型 |
| code | String | 否 | - | 股票代码，仅支持单个股票获取，不支持批量参数，不支持ALL参数。 |
| fq | String | 否 | 1-不复权，2-前复权，3-后复权，4-等比前复权，5-等比后复权 | 复权类型 |
| period | String | 否 | 1m-1分钟线，5m-5分钟线，15m-15分钟线，30m-30分钟线，1h-1小时线，1d-日线，1w-周线，1mon-月线，1q-季度线，1y-年线 | K线周期 |
| startDate | String | 否 | - | 开始日期 |
| endDate | String | 否 | - | 结束日期 |
| inReal | int | 否 | 1-开盘价，2-收盘价，3-最高价，4-最低价，5-成交量，6-成交额 | 数据标签 |
| optInSignalPeriod | int | 否 | - | 信号移动平均线周期 |
| scale | int | 否 | - | 精度（保留几位小数位） |
| round | int | 否 | - | 舍入方式，参考：https://www.cnblogs.com/yingchen/p/5459501.html |
| token | String | 是 | - | 登录网站获取token |

**输出参数**:

| 参数名 | 类型 | 说明 |
|--------|------|------|
| time | long | 交易日期 |
| open | float | 开盘价 |
| close | float | 收盘价 |
| high | float | 最高价 |
| low | float | 最低价 |
| volume | float | 成交量（手） |
| amount | float | 成交额（元） |
| diff | float | 指标计算值 |
| dea | float | 指标计算值 |
| hist | float | 指标计算值 |


### 106. mama指标

**描述**: MAMA是MESA自适应移动平均线，全称为MESA Adaptive Moving Average。它是根据价格的移动平均线和自适应移动平均线来计算的，它的设计初衷是能够更好地适应不同市场的变化。

**标签**: 金融指标

**权限要求**: 青铜版、白银版、黄金版、至尊版

**接口地址**: `http://api.xtick.top/doc/mama`

**请求示例**:
```
http://api.xtick.top/doc/mama?type=1&code=000001&fq=1&period=1d&startDate=2026-05-16&endDate=2026-05-16&inReal=2&optInFastLimit=0&optInSlowLimit=0&scale=3&round=1&token=123456789
```

**输入参数**:

| 参数名 | 类型 | 必填 | 取值范围 | 说明 |
|--------|------|------|----------|------|
| type | int | 否 | 1-沪深京A股，2-沪深指数，3-港股，4-ETF基金，5-沪深可转债 | 标的类型 |
| code | String | 否 | - | 股票代码，仅支持单个股票获取，不支持批量参数，不支持ALL参数。 |
| fq | String | 否 | 1-不复权，2-前复权，3-后复权，4-等比前复权，5-等比后复权 | 复权类型 |
| period | String | 否 | 1m-1分钟线，5m-5分钟线，15m-15分钟线，30m-30分钟线，1h-1小时线，1d-日线，1w-周线，1mon-月线，1q-季度线，1y-年线 | K线周期 |
| startDate | String | 否 | - | 开始日期 |
| endDate | String | 否 | - | 结束日期 |
| inReal | int | 否 | 1-开盘价，2-收盘价，3-最高价，4-最低价，5-成交量，6-成交额 | 数据标签 |
| optInFastLimit | double | 否 | - | 输入参数 |
| optInSlowLimit | double | 否 | - | 输入参数 |
| scale | int | 否 | - | 精度（保留几位小数位） |
| round | int | 否 | - | 舍入方式，参考：https://www.cnblogs.com/yingchen/p/5459501.html |
| token | String | 是 | - | 登录网站获取token |

**输出参数**:

| 参数名 | 类型 | 说明 |
|--------|------|------|
| time | long | 交易日期 |
| open | float | 开盘价 |
| close | float | 收盘价 |
| high | float | 最高价 |
| low | float | 最低价 |
| volume | float | 成交量（手） |
| amount | float | 成交额（元） |
| mama | float | 指标计算值 |
| fama | float | 指标计算值 |


### 110. mfi指标

**描述**: MFI指标的中文名称是资金流量指标，英文名称是Money Flow Index。MFI指标是一种衡量资金流入和流出的指标。它通过计算一定时期内的典型价格和成交量的买卖压力来衡量市场的超买和超卖情况。

**标签**: 金融指标

**权限要求**: 青铜版、白银版、黄金版、至尊版

**接口地址**: `http://api.xtick.top/doc/mfi`

**请求示例**:
```
http://api.xtick.top/doc/mfi?type=1&code=000001&fq=1&period=1d&startDate=2026-05-16&endDate=2026-05-16&optInTimePeriod=14&scale=3&round=1&token=123456789
```

**输入参数**:

| 参数名 | 类型 | 必填 | 取值范围 | 说明 |
|--------|------|------|----------|------|
| type | int | 否 | 1-沪深京A股，2-沪深指数，3-港股，4-ETF基金，5-沪深可转债 | 标的类型 |
| code | String | 否 | - | 股票代码，仅支持单个股票获取，不支持批量参数，不支持ALL参数。 |
| fq | String | 否 | 1-不复权，2-前复权，3-后复权，4-等比前复权，5-等比后复权 | 复权类型 |
| period | String | 否 | 1m-1分钟线，5m-5分钟线，15m-15分钟线，30m-30分钟线，1h-1小时线，1d-日线，1w-周线，1mon-月线，1q-季度线，1y-年线 | K线周期 |
| startDate | String | 否 | - | 开始日期 |
| endDate | String | 否 | - | 结束日期 |
| optInTimePeriod | int | 否 | - | 移动平均线周期 |
| scale | int | 否 | - | 精度（保留几位小数位） |
| round | int | 否 | - | 舍入方式，参考：https://www.cnblogs.com/yingchen/p/5459501.html |
| token | String | 是 | - | 登录网站获取token |

**输出参数**:

| 参数名 | 类型 | 说明 |
|--------|------|------|
| time | long | 交易日期 |
| open | float | 开盘价 |
| close | float | 收盘价 |
| high | float | 最高价 |
| low | float | 最低价 |
| volume | float | 成交量（手） |
| amount | float | 成交额（元） |
| output | float | 指标计算值 |


### 111. midpoint指标

**描述**: MIDPOINT是一种基于价格的技术指标，用于衡量价格趋势的中点。

**标签**: 金融指标

**权限要求**: 青铜版、白银版、黄金版、至尊版

**接口地址**: `http://api.xtick.top/doc/midpoint`

**请求示例**:
```
http://api.xtick.top/doc/midpoint?type=1&code=000001&fq=1&period=1d&startDate=2026-05-16&endDate=2026-05-16&inReal=2&optInTimePeriod=14&scale=3&round=1&token=123456789
```

**输入参数**:

| 参数名 | 类型 | 必填 | 取值范围 | 说明 |
|--------|------|------|----------|------|
| type | int | 否 | 1-沪深京A股，2-沪深指数，3-港股，4-ETF基金，5-沪深可转债 | 标的类型 |
| code | String | 否 | - | 股票代码，仅支持单个股票获取，不支持批量参数，不支持ALL参数。 |
| fq | String | 否 | 1-不复权，2-前复权，3-后复权，4-等比前复权，5-等比后复权 | 复权类型 |
| period | String | 否 | 1m-1分钟线，5m-5分钟线，15m-15分钟线，30m-30分钟线，1h-1小时线，1d-日线，1w-周线，1mon-月线，1q-季度线，1y-年线 | K线周期 |
| startDate | String | 否 | - | 开始日期 |
| endDate | String | 否 | - | 结束日期 |
| inReal | int | 否 | 1-开盘价，2-收盘价，3-最高价，4-最低价，5-成交量，6-成交额 | 数据标签 |
| optInTimePeriod | int | 否 | - | 移动平均线周期 |
| scale | int | 否 | - | 精度（保留几位小数位） |
| round | int | 否 | - | 舍入方式，参考：https://www.cnblogs.com/yingchen/p/5459501.html |
| token | String | 是 | - | 登录网站获取token |

**输出参数**:

| 参数名 | 类型 | 说明 |
|--------|------|------|
| time | long | 交易日期 |
| open | float | 开盘价 |
| close | float | 收盘价 |
| high | float | 最高价 |
| low | float | 最低价 |
| volume | float | 成交量（手） |
| amount | float | 成交额（元） |
| output | float | 指标计算值 |


### 117. minusdi指标

**描述**: MINUSDI指标中文名称为负向动向指标，英文名称为Negative Directional Indicator。它是技术分析中的一个指标，用于衡量市场下跌趋势的强度。

**标签**: 金融指标

**权限要求**: 青铜版、白银版、黄金版、至尊版

**接口地址**: `http://api.xtick.top/doc/minusdi`

**请求示例**:
```
http://api.xtick.top/doc/minusdi?type=1&code=000001&fq=1&period=1d&startDate=2026-05-16&endDate=2026-05-16&optInTimePeriod=14&scale=3&round=1&token=123456789
```

**输入参数**:

| 参数名 | 类型 | 必填 | 取值范围 | 说明 |
|--------|------|------|----------|------|
| type | int | 否 | 1-沪深京A股，2-沪深指数，3-港股，4-ETF基金，5-沪深可转债 | 标的类型 |
| code | String | 否 | - | 股票代码，仅支持单个股票获取，不支持批量参数，不支持ALL参数。 |
| fq | String | 否 | 1-不复权，2-前复权，3-后复权，4-等比前复权，5-等比后复权 | 复权类型 |
| period | String | 否 | 1m-1分钟线，5m-5分钟线，15m-15分钟线，30m-30分钟线，1h-1小时线，1d-日线，1w-周线，1mon-月线，1q-季度线，1y-年线 | K线周期 |
| startDate | String | 否 | - | 开始日期 |
| endDate | String | 否 | - | 结束日期 |
| optInTimePeriod | int | 否 | - | 移动平均线周期 |
| scale | int | 否 | - | 精度（保留几位小数位） |
| round | int | 否 | - | 舍入方式，参考：https://www.cnblogs.com/yingchen/p/5459501.html |
| token | String | 是 | - | 登录网站获取token |

**输出参数**:

| 参数名 | 类型 | 说明 |
|--------|------|------|
| time | long | 交易日期 |
| open | float | 开盘价 |
| close | float | 收盘价 |
| high | float | 最高价 |
| low | float | 最低价 |
| volume | float | 成交量（手） |
| amount | float | 成交额（元） |
| output | float | 指标计算值 |


### 118. minusdm指标

**描述**: MINUSDM指标的中文名称为负方向运动指标，英文名称为Minus Directional Movement Indicator。该指标用于衡量股价下跌的动力和趋势强度。

**标签**: 金融指标

**权限要求**: 青铜版、白银版、黄金版、至尊版

**接口地址**: `http://api.xtick.top/doc/minusdm`

**请求示例**:
```
http://api.xtick.top/doc/minusdm?type=1&code=000001&fq=1&period=1d&startDate=2026-05-16&endDate=2026-05-16&optInTimePeriod=14&scale=3&round=1&token=123456789
```

**输入参数**:

| 参数名 | 类型 | 必填 | 取值范围 | 说明 |
|--------|------|------|----------|------|
| type | int | 否 | 1-沪深京A股，2-沪深指数，3-港股，4-ETF基金，5-沪深可转债 | 标的类型 |
| code | String | 否 | - | 股票代码，仅支持单个股票获取，不支持批量参数，不支持ALL参数。 |
| fq | String | 否 | 1-不复权，2-前复权，3-后复权，4-等比前复权，5-等比后复权 | 复权类型 |
| period | String | 否 | 1m-1分钟线，5m-5分钟线，15m-15分钟线，30m-30分钟线，1h-1小时线，1d-日线，1w-周线，1mon-月线，1q-季度线，1y-年线 | K线周期 |
| startDate | String | 否 | - | 开始日期 |
| endDate | String | 否 | - | 结束日期 |
| optInTimePeriod | int | 否 | - | 移动平均线周期 |
| scale | int | 否 | - | 精度（保留几位小数位） |
| round | int | 否 | - | 舍入方式，参考：https://www.cnblogs.com/yingchen/p/5459501.html |
| token | String | 是 | - | 登录网站获取token |

**输出参数**:

| 参数名 | 类型 | 说明 |
|--------|------|------|
| time | long | 交易日期 |
| open | float | 开盘价 |
| close | float | 收盘价 |
| high | float | 最高价 |
| low | float | 最低价 |
| volume | float | 成交量（手） |
| amount | float | 成交额（元） |
| output | float | 指标计算值 |


### 119. mom指标

**描述**: MOM金融指标的中文名称为动量指标，英文名称为Momentum Indicator。该指标是通过比较当前价格与一定期间前的价格变动情况来衡量市场的趋势力量。

**标签**: 金融指标

**权限要求**: 青铜版、白银版、黄金版、至尊版

**接口地址**: `http://api.xtick.top/doc/mom`

**请求示例**:
```
http://api.xtick.top/doc/mom?type=1&code=000001&fq=1&period=1d&startDate=2026-05-16&endDate=2026-05-16&inReal=2&optInTimePeriod=10&scale=3&round=1&token=123456789
```

**输入参数**:

| 参数名 | 类型 | 必填 | 取值范围 | 说明 |
|--------|------|------|----------|------|
| type | int | 否 | 1-沪深京A股，2-沪深指数，3-港股，4-ETF基金，5-沪深可转债 | 标的类型 |
| code | String | 否 | - | 股票代码，仅支持单个股票获取，不支持批量参数，不支持ALL参数。 |
| fq | String | 否 | 1-不复权，2-前复权，3-后复权，4-等比前复权，5-等比后复权 | 复权类型 |
| period | String | 否 | 1m-1分钟线，5m-5分钟线，15m-15分钟线，30m-30分钟线，1h-1小时线，1d-日线，1w-周线，1mon-月线，1q-季度线，1y-年线 | K线周期 |
| startDate | String | 否 | - | 开始日期 |
| endDate | String | 否 | - | 结束日期 |
| inReal | int | 否 | 1-开盘价，2-收盘价，3-最高价，4-最低价，5-成交量，6-成交额 | 数据标签 |
| optInTimePeriod | int | 否 | - | 移动平均线周期 |
| scale | int | 否 | - | 精度（保留几位小数位） |
| round | int | 否 | - | 舍入方式，参考：https://www.cnblogs.com/yingchen/p/5459501.html |
| token | String | 是 | - | 登录网站获取token |

**输出参数**:

| 参数名 | 类型 | 说明 |
|--------|------|------|
| time | long | 交易日期 |
| open | float | 开盘价 |
| close | float | 收盘价 |
| high | float | 最高价 |
| low | float | 最低价 |
| volume | float | 成交量（手） |
| amount | float | 成交额（元） |
| output | float | 指标计算值 |


### 120. movingaverage指标

**描述**: MOVINGAVERAGE金融指标的中文名称为移动平均线，英文名称为Moving Average。该指标通过计算一段时间内收盘价的平均值来观察价格变动的趋势。

**标签**: 金融指标

**权限要求**: 青铜版、白银版、黄金版、至尊版

**接口地址**: `http://api.xtick.top/doc/movingaverage`

**请求示例**:
```
http://api.xtick.top/doc/movingaverage?type=1&code=000001&fq=1&period=1d&startDate=2026-05-16&endDate=2026-05-16&inReal=2&optInTimePeriod=14&optInMAType=1&scale=3&round=1&token=123456789
```

**输入参数**:

| 参数名 | 类型 | 必填 | 取值范围 | 说明 |
|--------|------|------|----------|------|
| type | int | 否 | 1-沪深京A股，2-沪深指数，3-港股，4-ETF基金，5-沪深可转债 | 标的类型 |
| code | String | 否 | - | 股票代码，仅支持单个股票获取，不支持批量参数，不支持ALL参数。 |
| fq | String | 否 | 1-不复权，2-前复权，3-后复权，4-等比前复权，5-等比后复权 | 复权类型 |
| period | String | 否 | 1m-1分钟线，5m-5分钟线，15m-15分钟线，30m-30分钟线，1h-1小时线，1d-日线，1w-周线，1mon-月线，1q-季度线，1y-年线 | K线周期 |
| startDate | String | 否 | - | 开始日期 |
| endDate | String | 否 | - | 结束日期 |
| inReal | int | 否 | 1-开盘价，2-收盘价，3-最高价，4-最低价，5-成交量，6-成交额 | 数据标签 |
| optInTimePeriod | int | 否 | - | 移动平均线周期 |
| optInMAType | int | 否 | 1-简单移动平均线SMA，2-指数移动平均线EMA，3-加权移动平均线WMA，4-双指数移动平均线DEMA，5-三重指数移动平均线TEMA，6-三重移动平均线TRIMA，7-考夫曼自适应移动平均线KAMA，8-自适应移动平均线MAMA，9-三重移动平均线T3 | 移动平均线类型 |
| scale | int | 否 | - | 精度（保留几位小数位） |
| round | int | 否 | - | 舍入方式，参考：https://www.cnblogs.com/yingchen/p/5459501.html |
| token | String | 是 | - | 登录网站获取token |

**输出参数**:

| 参数名 | 类型 | 说明 |
|--------|------|------|
| time | long | 交易日期 |
| open | float | 开盘价 |
| close | float | 收盘价 |
| high | float | 最高价 |
| low | float | 最低价 |
| volume | float | 成交量（手） |
| amount | float | 成交额（元） |
| output | float | 指标计算值 |


### 122. natr指标

**描述**: NATR称为归一化真实波动幅度，英文名称为Normalized Average True Range。该指标用于衡量市场的波动性。

**标签**: 金融指标

**权限要求**: 青铜版、白银版、黄金版、至尊版

**接口地址**: `http://api.xtick.top/doc/natr`

**请求示例**:
```
http://api.xtick.top/doc/natr?type=1&code=000001&fq=1&period=1d&startDate=2026-05-16&endDate=2026-05-16&optInTimePeriod=14&scale=3&round=1&token=123456789
```

**输入参数**:

| 参数名 | 类型 | 必填 | 取值范围 | 说明 |
|--------|------|------|----------|------|
| type | int | 否 | 1-沪深京A股，2-沪深指数，3-港股，4-ETF基金，5-沪深可转债 | 标的类型 |
| code | String | 否 | - | 股票代码，仅支持单个股票获取，不支持批量参数，不支持ALL参数。 |
| fq | String | 否 | 1-不复权，2-前复权，3-后复权，4-等比前复权，5-等比后复权 | 复权类型 |
| period | String | 否 | 1m-1分钟线，5m-5分钟线，15m-15分钟线，30m-30分钟线，1h-1小时线，1d-日线，1w-周线，1mon-月线，1q-季度线，1y-年线 | K线周期 |
| startDate | String | 否 | - | 开始日期 |
| endDate | String | 否 | - | 结束日期 |
| optInTimePeriod | int | 否 | - | 移动平均线周期 |
| scale | int | 否 | - | 精度（保留几位小数位） |
| round | int | 否 | - | 舍入方式，参考：https://www.cnblogs.com/yingchen/p/5459501.html |
| token | String | 是 | - | 登录网站获取token |

**输出参数**:

| 参数名 | 类型 | 说明 |
|--------|------|------|
| time | long | 交易日期 |
| open | float | 开盘价 |
| close | float | 收盘价 |
| high | float | 最高价 |
| low | float | 最低价 |
| volume | float | 成交量（手） |
| amount | float | 成交额（元） |
| output | float | 指标计算值 |


### 123. obv指标

**描述**: OBV指标（On-Balance Volume）是一种量能指标，用于衡量成交量的变化趋势和预测价格趋势的强弱。OBV指标通过统计成交量的正负值来判断市场的买卖力量，从而预测价格的上涨或下跌趋势。

**标签**: 金融指标

**权限要求**: 青铜版、白银版、黄金版、至尊版

**接口地址**: `http://api.xtick.top/doc/obv`

**请求示例**:
```
http://api.xtick.top/doc/obv?type=1&code=000001&fq=1&period=1d&startDate=2026-05-16&endDate=2026-05-16&inReal=2&scale=3&round=1&token=123456789
```

**输入参数**:

| 参数名 | 类型 | 必填 | 取值范围 | 说明 |
|--------|------|------|----------|------|
| type | int | 否 | 1-沪深京A股，2-沪深指数，3-港股，4-ETF基金，5-沪深可转债 | 标的类型 |
| code | String | 否 | - | 股票代码，仅支持单个股票获取，不支持批量参数，不支持ALL参数。 |
| fq | String | 否 | 1-不复权，2-前复权，3-后复权，4-等比前复权，5-等比后复权 | 复权类型 |
| period | String | 否 | 1m-1分钟线，5m-5分钟线，15m-15分钟线，30m-30分钟线，1h-1小时线，1d-日线，1w-周线，1mon-月线，1q-季度线，1y-年线 | K线周期 |
| startDate | String | 否 | - | 开始日期 |
| endDate | String | 否 | - | 结束日期 |
| inReal | int | 否 | 1-开盘价，2-收盘价，3-最高价，4-最低价，5-成交量，6-成交额 | 数据标签 |
| scale | int | 否 | - | 精度（保留几位小数位） |
| round | int | 否 | - | 舍入方式，参考：https://www.cnblogs.com/yingchen/p/5459501.html |
| token | String | 是 | - | 登录网站获取token |

**输出参数**:

| 参数名 | 类型 | 说明 |
|--------|------|------|
| time | long | 交易日期 |
| open | float | 开盘价 |
| close | float | 收盘价 |
| high | float | 最高价 |
| low | float | 最低价 |
| volume | float | 成交量（手） |
| amount | float | 成交额（元） |
| output | float | 指标计算值 |


### 124. plusdi指标

**描述**: 正向移动方向指标，英文名称是Positive Directional Indicator。

**标签**: 金融指标

**权限要求**: 青铜版、白银版、黄金版、至尊版

**接口地址**: `http://api.xtick.top/doc/plusdi`

**请求示例**:
```
http://api.xtick.top/doc/plusdi?type=1&code=000001&fq=1&period=1d&startDate=2026-05-16&endDate=2026-05-16&optInTimePeriod=14&scale=3&round=1&token=123456789
```

**输入参数**:

| 参数名 | 类型 | 必填 | 取值范围 | 说明 |
|--------|------|------|----------|------|
| type | int | 否 | 1-沪深京A股，2-沪深指数，3-港股，4-ETF基金，5-沪深可转债 | 标的类型 |
| code | String | 否 | - | 股票代码，仅支持单个股票获取，不支持批量参数，不支持ALL参数。 |
| fq | String | 否 | 1-不复权，2-前复权，3-后复权，4-等比前复权，5-等比后复权 | 复权类型 |
| period | String | 否 | 1m-1分钟线，5m-5分钟线，15m-15分钟线，30m-30分钟线，1h-1小时线，1d-日线，1w-周线，1mon-月线，1q-季度线，1y-年线 | K线周期 |
| startDate | String | 否 | - | 开始日期 |
| endDate | String | 否 | - | 结束日期 |
| optInTimePeriod | int | 否 | - | 移动平均线周期 |
| scale | int | 否 | - | 精度（保留几位小数位） |
| round | int | 否 | - | 舍入方式，参考：https://www.cnblogs.com/yingchen/p/5459501.html |
| token | String | 是 | - | 登录网站获取token |

**输出参数**:

| 参数名 | 类型 | 说明 |
|--------|------|------|
| time | long | 交易日期 |
| open | float | 开盘价 |
| close | float | 收盘价 |
| high | float | 最高价 |
| low | float | 最低价 |
| volume | float | 成交量（手） |
| amount | float | 成交额（元） |
| output | float | 指标计算值 |


### 125. plusdm指标

**描述**: 正向动向变动指标，英文名称是Positive Directional Movement。

**标签**: 金融指标

**权限要求**: 青铜版、白银版、黄金版、至尊版

**接口地址**: `http://api.xtick.top/doc/plusdm`

**请求示例**:
```
http://api.xtick.top/doc/plusdm?type=1&code=000001&fq=1&period=1d&startDate=2026-05-16&endDate=2026-05-16&optInTimePeriod=14&scale=3&round=1&token=123456789
```

**输入参数**:

| 参数名 | 类型 | 必填 | 取值范围 | 说明 |
|--------|------|------|----------|------|
| type | int | 否 | 1-沪深京A股，2-沪深指数，3-港股，4-ETF基金，5-沪深可转债 | 标的类型 |
| code | String | 否 | - | 股票代码，仅支持单个股票获取，不支持批量参数，不支持ALL参数。 |
| fq | String | 否 | 1-不复权，2-前复权，3-后复权，4-等比前复权，5-等比后复权 | 复权类型 |
| period | String | 否 | 1m-1分钟线，5m-5分钟线，15m-15分钟线，30m-30分钟线，1h-1小时线，1d-日线，1w-周线，1mon-月线，1q-季度线，1y-年线 | K线周期 |
| startDate | String | 否 | - | 开始日期 |
| endDate | String | 否 | - | 结束日期 |
| optInTimePeriod | int | 否 | - | 移动平均线周期 |
| scale | int | 否 | - | 精度（保留几位小数位） |
| round | int | 否 | - | 舍入方式，参考：https://www.cnblogs.com/yingchen/p/5459501.html |
| token | String | 是 | - | 登录网站获取token |

**输出参数**:

| 参数名 | 类型 | 说明 |
|--------|------|------|
| time | long | 交易日期 |
| open | float | 开盘价 |
| close | float | 收盘价 |
| high | float | 最高价 |
| low | float | 最低价 |
| volume | float | 成交量（手） |
| amount | float | 成交额（元） |
| output | float | 指标计算值 |


### 126. ppo指标

**描述**: 价格振荡百分比指标，英文名称为Percentage Price Oscillator。

**标签**: 金融指标

**权限要求**: 青铜版、白银版、黄金版、至尊版

**接口地址**: `http://api.xtick.top/doc/ppo`

**请求示例**:
```
http://api.xtick.top/doc/ppo?type=1&code=000001&fq=1&period=1d&startDate=2026-05-16&endDate=2026-05-16&inReal=2&optInFastPeriod=12&optInSlowPeriod=26&optInMAType=1&scale=3&round=1&token=123456789
```

**输入参数**:

| 参数名 | 类型 | 必填 | 取值范围 | 说明 |
|--------|------|------|----------|------|
| type | int | 否 | 1-沪深京A股，2-沪深指数，3-港股，4-ETF基金，5-沪深可转债 | 标的类型 |
| code | String | 否 | - | 股票代码，仅支持单个股票获取，不支持批量参数，不支持ALL参数。 |
| fq | String | 否 | 1-不复权，2-前复权，3-后复权，4-等比前复权，5-等比后复权 | 复权类型 |
| period | String | 否 | 1m-1分钟线，5m-5分钟线，15m-15分钟线，30m-30分钟线，1h-1小时线，1d-日线，1w-周线，1mon-月线，1q-季度线，1y-年线 | K线周期 |
| startDate | String | 否 | - | 开始日期 |
| endDate | String | 否 | - | 结束日期 |
| inReal | int | 否 | 1-开盘价，2-收盘价，3-最高价，4-最低价，5-成交量，6-成交额 | 数据标签 |
| optInFastPeriod | int | 否 | - | 快速移动平均线周期 |
| optInSlowPeriod | int | 否 | - | 慢速移动平均线周期 |
| optInMAType | int | 否 | 1-简单移动平均线SMA，2-指数移动平均线EMA，3-加权移动平均线WMA，4-双指数移动平均线DEMA，5-三重指数移动平均线TEMA，6-三重移动平均线TRIMA，7-考夫曼自适应移动平均线KAMA，8-自适应移动平均线MAMA，9-三重移动平均线T3 | 移动平均线类型 |
| scale | int | 否 | - | 精度（保留几位小数位） |
| round | int | 否 | - | 舍入方式，参考：https://www.cnblogs.com/yingchen/p/5459501.html |
| token | String | 是 | - | 登录网站获取token |

**输出参数**:

| 参数名 | 类型 | 说明 |
|--------|------|------|
| time | long | 交易日期 |
| open | float | 开盘价 |
| close | float | 收盘价 |
| high | float | 最高价 |
| low | float | 最低价 |
| volume | float | 成交量（手） |
| amount | float | 成交额（元） |
| output | float | 指标计算值 |


### 127. roc指标

**描述**: ROC金融指标的中文名称是变动率，英文名称是Rate of Change，ROC指标是一种衡量价格变动速度的技。

**标签**: 金融指标

**权限要求**: 青铜版、白银版、黄金版、至尊版

**接口地址**: `http://api.xtick.top/doc/roc`

**请求示例**:
```
http://api.xtick.top/doc/roc?type=1&code=000001&fq=1&period=1d&startDate=2026-05-16&endDate=2026-05-16&inReal=2&optInTimePeriod=12&scale=3&round=1&token=123456789
```

**输入参数**:

| 参数名 | 类型 | 必填 | 取值范围 | 说明 |
|--------|------|------|----------|------|
| type | int | 否 | 1-沪深京A股，2-沪深指数，3-港股，4-ETF基金，5-沪深可转债 | 标的类型 |
| code | String | 否 | - | 股票代码，仅支持单个股票获取，不支持批量参数，不支持ALL参数。 |
| fq | String | 否 | 1-不复权，2-前复权，3-后复权，4-等比前复权，5-等比后复权 | 复权类型 |
| period | String | 否 | 1m-1分钟线，5m-5分钟线，15m-15分钟线，30m-30分钟线，1h-1小时线，1d-日线，1w-周线，1mon-月线，1q-季度线，1y-年线 | K线周期 |
| startDate | String | 否 | - | 开始日期 |
| endDate | String | 否 | - | 结束日期 |
| inReal | int | 否 | 1-开盘价，2-收盘价，3-最高价，4-最低价，5-成交量，6-成交额 | 数据标签 |
| optInTimePeriod | int | 否 | - | 移动平均线周期 |
| scale | int | 否 | - | 精度（保留几位小数位） |
| round | int | 否 | - | 舍入方式，参考：https://www.cnblogs.com/yingchen/p/5459501.html |
| token | String | 是 | - | 登录网站获取token |

**输出参数**:

| 参数名 | 类型 | 说明 |
|--------|------|------|
| time | long | 交易日期 |
| open | float | 开盘价 |
| close | float | 收盘价 |
| high | float | 最高价 |
| low | float | 最低价 |
| volume | float | 成交量（手） |
| amount | float | 成交额（元） |
| output | float | 指标计算值 |


### 128. rocp指标

**描述**: ROCP指标的中文名称为变化率指标，英文名称为Rate of Change Percentage。

**标签**: 金融指标

**权限要求**: 青铜版、白银版、黄金版、至尊版

**接口地址**: `http://api.xtick.top/doc/rocp`

**请求示例**:
```
http://api.xtick.top/doc/rocp?type=1&code=000001&fq=1&period=1d&startDate=2026-05-16&endDate=2026-05-16&inReal=2&optInTimePeriod=12&scale=3&round=1&token=123456789
```

**输入参数**:

| 参数名 | 类型 | 必填 | 取值范围 | 说明 |
|--------|------|------|----------|------|
| type | int | 否 | 1-沪深京A股，2-沪深指数，3-港股，4-ETF基金，5-沪深可转债 | 标的类型 |
| code | String | 否 | - | 股票代码，仅支持单个股票获取，不支持批量参数，不支持ALL参数。 |
| fq | String | 否 | 1-不复权，2-前复权，3-后复权，4-等比前复权，5-等比后复权 | 复权类型 |
| period | String | 否 | 1m-1分钟线，5m-5分钟线，15m-15分钟线，30m-30分钟线，1h-1小时线，1d-日线，1w-周线，1mon-月线，1q-季度线，1y-年线 | K线周期 |
| startDate | String | 否 | - | 开始日期 |
| endDate | String | 否 | - | 结束日期 |
| inReal | int | 否 | 1-开盘价，2-收盘价，3-最高价，4-最低价，5-成交量，6-成交额 | 数据标签 |
| optInTimePeriod | int | 否 | - | 移动平均线周期 |
| scale | int | 否 | - | 精度（保留几位小数位） |
| round | int | 否 | - | 舍入方式，参考：https://www.cnblogs.com/yingchen/p/5459501.html |
| token | String | 是 | - | 登录网站获取token |

**输出参数**:

| 参数名 | 类型 | 说明 |
|--------|------|------|
| time | long | 交易日期 |
| open | float | 开盘价 |
| close | float | 收盘价 |
| high | float | 最高价 |
| low | float | 最低价 |
| volume | float | 成交量（手） |
| amount | float | 成交额（元） |
| output | float | 指标计算值 |


### 129. rocr指标

**描述**: ROCR金融指标是指Rate of Change Ratio，其中文名称是变动率比率，英文名称是Rate of Change Ratio。该指标用于衡量价格的变动率，并通过计算价格的百分比变化来表示。

**标签**: 金融指标

**权限要求**: 青铜版、白银版、黄金版、至尊版

**接口地址**: `http://api.xtick.top/doc/rocr`

**请求示例**:
```
http://api.xtick.top/doc/rocr?type=1&code=000001&fq=1&period=1d&startDate=2026-05-16&endDate=2026-05-16&inReal=2&optInTimePeriod=12&scale=3&round=1&token=123456789
```

**输入参数**:

| 参数名 | 类型 | 必填 | 取值范围 | 说明 |
|--------|------|------|----------|------|
| type | int | 否 | 1-沪深京A股，2-沪深指数，3-港股，4-ETF基金，5-沪深可转债 | 标的类型 |
| code | String | 否 | - | 股票代码，仅支持单个股票获取，不支持批量参数，不支持ALL参数。 |
| fq | String | 否 | 1-不复权，2-前复权，3-后复权，4-等比前复权，5-等比后复权 | 复权类型 |
| period | String | 否 | 1m-1分钟线，5m-5分钟线，15m-15分钟线，30m-30分钟线，1h-1小时线，1d-日线，1w-周线，1mon-月线，1q-季度线，1y-年线 | K线周期 |
| startDate | String | 否 | - | 开始日期 |
| endDate | String | 否 | - | 结束日期 |
| inReal | int | 否 | 1-开盘价，2-收盘价，3-最高价，4-最低价，5-成交量，6-成交额 | 数据标签 |
| optInTimePeriod | int | 否 | - | 移动平均线周期 |
| scale | int | 否 | - | 精度（保留几位小数位） |
| round | int | 否 | - | 舍入方式，参考：https://www.cnblogs.com/yingchen/p/5459501.html |
| token | String | 是 | - | 登录网站获取token |

**输出参数**:

| 参数名 | 类型 | 说明 |
|--------|------|------|
| time | long | 交易日期 |
| open | float | 开盘价 |
| close | float | 收盘价 |
| high | float | 最高价 |
| low | float | 最低价 |
| volume | float | 成交量（手） |
| amount | float | 成交额（元） |
| output | float | 指标计算值 |


### 130. rocr100指标

**描述**: ROCR100金融指标的中文名称为价格变动率，英文名称为Rate of Change Ratio 100，指标介绍为衡量价格在一定时间内的变动幅度，以百分比表示。

**标签**: 金融指标

**权限要求**: 青铜版、白银版、黄金版、至尊版

**接口地址**: `http://api.xtick.top/doc/rocr100`

**请求示例**:
```
http://api.xtick.top/doc/rocr100?type=1&code=000001&fq=1&period=1d&startDate=2026-05-16&endDate=2026-05-16&inReal=2&optInTimePeriod=12&scale=3&round=1&token=123456789
```

**输入参数**:

| 参数名 | 类型 | 必填 | 取值范围 | 说明 |
|--------|------|------|----------|------|
| type | int | 否 | 1-沪深京A股，2-沪深指数，3-港股，4-ETF基金，5-沪深可转债 | 标的类型 |
| code | String | 否 | - | 股票代码，仅支持单个股票获取，不支持批量参数，不支持ALL参数。 |
| fq | String | 否 | 1-不复权，2-前复权，3-后复权，4-等比前复权，5-等比后复权 | 复权类型 |
| period | String | 否 | 1m-1分钟线，5m-5分钟线，15m-15分钟线，30m-30分钟线，1h-1小时线，1d-日线，1w-周线，1mon-月线，1q-季度线，1y-年线 | K线周期 |
| startDate | String | 否 | - | 开始日期 |
| endDate | String | 否 | - | 结束日期 |
| inReal | int | 否 | 1-开盘价，2-收盘价，3-最高价，4-最低价，5-成交量，6-成交额 | 数据标签 |
| optInTimePeriod | int | 否 | - | 移动平均线周期 |
| scale | int | 否 | - | 精度（保留几位小数位） |
| round | int | 否 | - | 舍入方式，参考：https://www.cnblogs.com/yingchen/p/5459501.html |
| token | String | 是 | - | 登录网站获取token |

**输出参数**:

| 参数名 | 类型 | 说明 |
|--------|------|------|
| time | long | 交易日期 |
| open | float | 开盘价 |
| close | float | 收盘价 |
| high | float | 最高价 |
| low | float | 最低价 |
| volume | float | 成交量（手） |
| amount | float | 成交额（元） |
| output | float | 指标计算值 |


### 131. rsi指标

**描述**: RSI指标是相对强弱指标，全称为Relative Strength Index。它是一种用于衡量市场超买超卖状态的技术指标，由J. Welles Wilder于1978年提出。RSI指标的计算基于一定时期内的价格变动幅度，通过将一定时期内的上涨幅度和下跌幅度进行比较，以确定市场的强弱程度。

**标签**: 金融指标

**权限要求**: 青铜版、白银版、黄金版、至尊版

**接口地址**: `http://api.xtick.top/doc/rsi`

**请求示例**:
```
http://api.xtick.top/doc/rsi?type=1&code=000001&fq=1&period=1d&startDate=2026-05-16&endDate=2026-05-16&inReal=2&optInTimePeriod=12&scale=3&round=1&token=123456789
```

**输入参数**:

| 参数名 | 类型 | 必填 | 取值范围 | 说明 |
|--------|------|------|----------|------|
| type | int | 否 | 1-沪深京A股，2-沪深指数，3-港股，4-ETF基金，5-沪深可转债 | 标的类型 |
| code | String | 否 | - | 股票代码，仅支持单个股票获取，不支持批量参数，不支持ALL参数。 |
| fq | String | 否 | 1-不复权，2-前复权，3-后复权，4-等比前复权，5-等比后复权 | 复权类型 |
| period | String | 否 | 1m-1分钟线，5m-5分钟线，15m-15分钟线，30m-30分钟线，1h-1小时线，1d-日线，1w-周线，1mon-月线，1q-季度线，1y-年线 | K线周期 |
| startDate | String | 否 | - | 开始日期 |
| endDate | String | 否 | - | 结束日期 |
| inReal | int | 否 | 1-开盘价，2-收盘价，3-最高价，4-最低价，5-成交量，6-成交额 | 数据标签 |
| optInTimePeriod | int | 否 | - | 移动平均线周期 |
| scale | int | 否 | - | 精度（保留几位小数位） |
| round | int | 否 | - | 舍入方式，参考：https://www.cnblogs.com/yingchen/p/5459501.html |
| token | String | 是 | - | 登录网站获取token |

**输出参数**:

| 参数名 | 类型 | 说明 |
|--------|------|------|
| time | long | 交易日期 |
| open | float | 开盘价 |
| close | float | 收盘价 |
| high | float | 最高价 |
| low | float | 最低价 |
| volume | float | 成交量（手） |
| amount | float | 成交额（元） |
| output | float | 指标计算值 |


### 132. sar指标

**描述**: SAR称为抛物线指标，英文名称为Parabolic SAR (Stop and Reverse)。

**标签**: 金融指标

**权限要求**: 青铜版、白银版、黄金版、至尊版

**接口地址**: `http://api.xtick.top/doc/sar`

**请求示例**:
```
http://api.xtick.top/doc/sar?type=1&code=000001&fq=1&period=1d&startDate=2026-05-16&endDate=2026-05-16&optInAcceleration=0.02&optInMaximum=0.2&scale=3&round=1&token=123456789
```

**输入参数**:

| 参数名 | 类型 | 必填 | 取值范围 | 说明 |
|--------|------|------|----------|------|
| type | int | 否 | 1-沪深京A股，2-沪深指数，3-港股，4-ETF基金，5-沪深可转债 | 标的类型 |
| code | String | 否 | - | 股票代码，仅支持单个股票获取，不支持批量参数，不支持ALL参数。 |
| fq | String | 否 | 1-不复权，2-前复权，3-后复权，4-等比前复权，5-等比后复权 | 复权类型 |
| period | String | 否 | 1m-1分钟线，5m-5分钟线，15m-15分钟线，30m-30分钟线，1h-1小时线，1d-日线，1w-周线，1mon-月线，1q-季度线，1y-年线 | K线周期 |
| startDate | String | 否 | - | 开始日期 |
| endDate | String | 否 | - | 结束日期 |
| optInAcceleration | double | 否 | - | 加速因子 |
| optInMaximum | double | 否 | - | 最大值 |
| scale | int | 否 | - | 精度（保留几位小数位） |
| round | int | 否 | - | 舍入方式，参考：https://www.cnblogs.com/yingchen/p/5459501.html |
| token | String | 是 | - | 登录网站获取token |

**输出参数**:

| 参数名 | 类型 | 说明 |
|--------|------|------|
| time | long | 交易日期 |
| open | float | 开盘价 |
| close | float | 收盘价 |
| high | float | 最高价 |
| low | float | 最低价 |
| volume | float | 成交量（手） |
| amount | float | 成交额（元） |
| output | float | 指标计算值 |


### 133. sarext指标

**描述**: SAREXT称为拓展停损点指标，英文名称为SAREXT (Extended Stop and Reverse Indicator)。

**标签**: 金融指标

**权限要求**: 青铜版、白银版、黄金版、至尊版

**接口地址**: `http://api.xtick.top/doc/sarext`

**请求示例**:
```
http://api.xtick.top/doc/sarext?type=1&code=000001&fq=1&period=1d&startDate=2026-05-16&endDate=2026-05-16&optInStartValue=0&optInOffsetOnReverse=0&optInAccelerationInitLong=0.02&optInAccelerationLong=0.02&optInAccelerationMaxLong=0.02&optInAccelerationInitShort=0.02&optInAccelerationShort=0.02&optInAccelerationMaxShort=0.02&scale=3&round=1&token=123456789
```

**输入参数**:

| 参数名 | 类型 | 必填 | 取值范围 | 说明 |
|--------|------|------|----------|------|
| type | int | 否 | 1-沪深京A股，2-沪深指数，3-港股，4-ETF基金，5-沪深可转债 | 标的类型 |
| code | String | 否 | - | 股票代码，仅支持单个股票获取，不支持批量参数，不支持ALL参数。 |
| fq | String | 否 | 1-不复权，2-前复权，3-后复权，4-等比前复权，5-等比后复权 | 复权类型 |
| period | String | 否 | 1m-1分钟线，5m-5分钟线，15m-15分钟线，30m-30分钟线，1h-1小时线，1d-日线，1w-周线，1mon-月线，1q-季度线，1y-年线 | K线周期 |
| startDate | String | 否 | - | 开始日期 |
| endDate | String | 否 | - | 结束日期 |
| optInStartValue | double | 否 | - | 起始值 |
| optInOffsetOnReverse | double | 否 | - | 反转偏移量 |
| optInAccelerationInitLong | double | 否 | - | 多头初始加速因子 |
| optInAccelerationLong | double | 否 | - | 多头加速因子 |
| optInAccelerationMaxLong | double | 否 | - | 多头最大加速因子 |
| optInAccelerationInitShort | double | 否 | - | 空头初始加速因子 |
| optInAccelerationShort | double | 否 | - | 空头加速因子 |
| optInAccelerationMaxShort | double | 否 | - | 空头最大加速因子 |
| scale | int | 否 | - | 精度（保留几位小数位） |
| round | int | 否 | - | 舍入方式，参考：https://www.cnblogs.com/yingchen/p/5459501.html |
| token | String | 是 | - | 登录网站获取token |

**输出参数**:

| 参数名 | 类型 | 说明 |
|--------|------|------|
| time | long | 交易日期 |
| open | float | 开盘价 |
| close | float | 收盘价 |
| high | float | 最高价 |
| low | float | 最低价 |
| volume | float | 成交量（手） |
| amount | float | 成交额（元） |
| output | float | 指标计算值 |


### 136. sma指标

**描述**: SMA指标是简单移动平均线，全称为Simple Moving Average。它是一种常用的技术分析指标，用于平滑价格数据并显示价格趋势。

**标签**: 金融指标

**权限要求**: 青铜版、白银版、黄金版、至尊版

**接口地址**: `http://api.xtick.top/doc/sma`

**请求示例**:
```
http://api.xtick.top/doc/sma?type=1&code=000001&fq=1&period=1d&startDate=2026-05-16&endDate=2026-05-16&inReal=2&optInTimePeriod=20&scale=3&round=1&token=123456789
```

**输入参数**:

| 参数名 | 类型 | 必填 | 取值范围 | 说明 |
|--------|------|------|----------|------|
| type | int | 否 | 1-沪深京A股，2-沪深指数，3-港股，4-ETF基金，5-沪深可转债 | 标的类型 |
| code | String | 否 | - | 股票代码，仅支持单个股票获取，不支持批量参数，不支持ALL参数。 |
| fq | String | 否 | 1-不复权，2-前复权，3-后复权，4-等比前复权，5-等比后复权 | 复权类型 |
| period | String | 否 | 1m-1分钟线，5m-5分钟线，15m-15分钟线，30m-30分钟线，1h-1小时线，1d-日线，1w-周线，1mon-月线，1q-季度线，1y-年线 | K线周期 |
| startDate | String | 否 | - | 开始日期 |
| endDate | String | 否 | - | 结束日期 |
| inReal | int | 否 | 1-开盘价，2-收盘价，3-最高价，4-最低价，5-成交量，6-成交额 | 数据标签 |
| optInTimePeriod | int | 否 | - | 移动平均线周期 |
| scale | int | 否 | - | 精度（保留几位小数位） |
| round | int | 否 | - | 舍入方式，参考：https://www.cnblogs.com/yingchen/p/5459501.html |
| token | String | 是 | - | 登录网站获取token |

**输出参数**:

| 参数名 | 类型 | 说明 |
|--------|------|------|
| time | long | 交易日期 |
| open | float | 开盘价 |
| close | float | 收盘价 |
| high | float | 最高价 |
| low | float | 最低价 |
| volume | float | 成交量（手） |
| amount | float | 成交额（元） |
| output | float | 指标计算值 |


### 139. stoch指标

**描述**: STOCH是随机指标（KDJ指标），英文名称是Stochastic Oscillator。该指标是一种用来测量价格相对于其价格范围的位置的技术指标。

**标签**: 金融指标

**权限要求**: 青铜版、白银版、黄金版、至尊版

**接口地址**: `http://api.xtick.top/doc/stoch`

**请求示例**:
```
http://api.xtick.top/doc/stoch?type=1&code=000001&fq=1&period=1d&startDate=2026-05-16&endDate=2026-05-16&optInFastK_Period=9&optInSlowK_Period=5&optInSlowK_MAType=2&optInSlowD_Period=5&optInSlowD_MAType=2&scale=3&round=1&token=123456789
```

**输入参数**:

| 参数名 | 类型 | 必填 | 取值范围 | 说明 |
|--------|------|------|----------|------|
| type | int | 否 | 1-沪深京A股，2-沪深指数，3-港股，4-ETF基金，5-沪深可转债 | 标的类型 |
| code | String | 否 | - | 股票代码，仅支持单个股票获取，不支持批量参数，不支持ALL参数。 |
| fq | String | 否 | 1-不复权，2-前复权，3-后复权，4-等比前复权，5-等比后复权 | 复权类型 |
| period | String | 否 | 1m-1分钟线，5m-5分钟线，15m-15分钟线，30m-30分钟线，1h-1小时线，1d-日线，1w-周线，1mon-月线，1q-季度线，1y-年线 | K线周期 |
| startDate | String | 否 | - | 开始日期 |
| endDate | String | 否 | - | 结束日期 |
| optInFastK_Period | int | 否 | - | 快速移动平均线K周期 |
| optInSlowK_Period | int | 否 | - | 慢速移动平均线K周期 |
| optInSlowK_MAType | int | 否 | 1-简单移动平均线SMA，2-指数移动平均线EMA，3-加权移动平均线WMA，4-双指数移动平均线DEMA，5-三重指数移动平均线TEMA，6-三重移动平均线TRIMA，7-考夫曼自适应移动平均线KAMA，8-自适应移动平均线MAMA，9-三重移动平均线T3 | 慢速移动平均线K类型 |
| optInSlowD_Period | int | 否 | - | 慢速移动平均线D周期 |
| optInSlowD_MAType | int | 否 | 1-简单移动平均线SMA，2-指数移动平均线EMA，3-加权移动平均线WMA，4-双指数移动平均线DEMA，5-三重指数移动平均线TEMA，6-三重移动平均线TRIMA，7-考夫曼自适应移动平均线KAMA，8-自适应移动平均线MAMA，9-三重移动平均线T3 | 慢速移动平均线D类型 |
| scale | int | 否 | - | 精度（保留几位小数位） |
| round | int | 否 | - | 舍入方式，参考：https://www.cnblogs.com/yingchen/p/5459501.html |
| token | String | 是 | - | 登录网站获取token |

**输出参数**:

| 参数名 | 类型 | 说明 |
|--------|------|------|
| time | long | 交易日期 |
| open | float | 开盘价 |
| close | float | 收盘价 |
| high | float | 最高价 |
| low | float | 最低价 |
| volume | float | 成交量（手） |
| amount | float | 成交额（元） |
| k | float | 指标计算值 |
| d | float | 指标计算值 |


### 140. stochf指标

**描述**: STOCHF是随机振荡指标（Stochastic Fast）。

**标签**: 金融指标

**权限要求**: 青铜版、白银版、黄金版、至尊版

**接口地址**: `http://api.xtick.top/doc/stochf`

**请求示例**:
```
http://api.xtick.top/doc/stochf?type=1&code=000001&fq=1&period=1d&startDate=2026-05-16&endDate=2026-05-16&optInFastK_Period=5&optInFastD_Period=3&optInFastD_MAType=1&scale=3&round=1&token=123456789
```

**输入参数**:

| 参数名 | 类型 | 必填 | 取值范围 | 说明 |
|--------|------|------|----------|------|
| type | int | 否 | 1-沪深京A股，2-沪深指数，3-港股，4-ETF基金，5-沪深可转债 | 标的类型 |
| code | String | 否 | - | 股票代码，仅支持单个股票获取，不支持批量参数，不支持ALL参数。 |
| fq | String | 否 | 1-不复权，2-前复权，3-后复权，4-等比前复权，5-等比后复权 | 复权类型 |
| period | String | 否 | 1m-1分钟线，5m-5分钟线，15m-15分钟线，30m-30分钟线，1h-1小时线，1d-日线，1w-周线，1mon-月线，1q-季度线，1y-年线 | K线周期 |
| startDate | String | 否 | - | 开始日期 |
| endDate | String | 否 | - | 结束日期 |
| optInFastK_Period | int | 否 | - | 快速移动平均线K周期 |
| optInFastD_Period | int | 否 | - | 慢速移动平均线D周期 |
| optInFastD_MAType | int | 否 | 1-简单移动平均线SMA，2-指数移动平均线EMA，3-加权移动平均线WMA，4-双指数移动平均线DEMA，5-三重指数移动平均线TEMA，6-三重移动平均线TRIMA，7-考夫曼自适应移动平均线KAMA，8-自适应移动平均线MAMA，9-三重移动平均线T3 | 慢速移动平均线D类型 |
| scale | int | 否 | - | 精度（保留几位小数位） |
| round | int | 否 | - | 舍入方式，参考：https://www.cnblogs.com/yingchen/p/5459501.html |
| token | String | 是 | - | 登录网站获取token |

**输出参数**:

| 参数名 | 类型 | 说明 |
|--------|------|------|
| time | long | 交易日期 |
| open | float | 开盘价 |
| close | float | 收盘价 |
| high | float | 最高价 |
| low | float | 最低价 |
| volume | float | 成交量（手） |
| amount | float | 成交额（元） |
| k | float | 指标计算值 |
| d | float | 指标计算值 |


### 141. stochrsi指标

**描述**: 随机相对强弱指标，Stochastic Relative Strength Index (STOCHRSI)。

**标签**: 金融指标

**权限要求**: 青铜版、白银版、黄金版、至尊版

**接口地址**: `http://api.xtick.top/doc/stochrsi`

**请求示例**:
```
http://api.xtick.top/doc/stochrsi?type=1&code=000001&fq=1&period=1d&startDate=2026-05-16&endDate=2026-05-16&inReal=2&optInTimePeriod=14&optInFastK_Period=5&optInFastD_Period=3&optInFastD_MAType=1&scale=3&round=1&token=123456789
```

**输入参数**:

| 参数名 | 类型 | 必填 | 取值范围 | 说明 |
|--------|------|------|----------|------|
| type | int | 否 | 1-沪深京A股，2-沪深指数，3-港股，4-ETF基金，5-沪深可转债 | 标的类型 |
| code | String | 否 | - | 股票代码，仅支持单个股票获取，不支持批量参数，不支持ALL参数。 |
| fq | String | 否 | 1-不复权，2-前复权，3-后复权，4-等比前复权，5-等比后复权 | 复权类型 |
| period | String | 否 | 1m-1分钟线，5m-5分钟线，15m-15分钟线，30m-30分钟线，1h-1小时线，1d-日线，1w-周线，1mon-月线，1q-季度线，1y-年线 | K线周期 |
| startDate | String | 否 | - | 开始日期 |
| endDate | String | 否 | - | 结束日期 |
| inReal | int | 否 | 1-开盘价，2-收盘价，3-最高价，4-最低价，5-成交量，6-成交额 | 数据标签 |
| optInTimePeriod | int | 否 | - | 移动平均线周期 |
| optInFastK_Period | int | 否 | - | 快速移动平均线K周期 |
| optInFastD_Period | int | 否 | - | 慢速移动平均线D周期 |
| optInFastD_MAType | int | 否 | 1-简单移动平均线SMA，2-指数移动平均线EMA，3-加权移动平均线WMA，4-双指数移动平均线DEMA，5-三重指数移动平均线TEMA，6-三重移动平均线TRIMA，7-考夫曼自适应移动平均线KAMA，8-自适应移动平均线MAMA，9-三重移动平均线T3 | 慢速移动平均线D类型 |
| scale | int | 否 | - | 精度（保留几位小数位） |
| round | int | 否 | - | 舍入方式，参考：https://www.cnblogs.com/yingchen/p/5459501.html |
| token | String | 是 | - | 登录网站获取token |

**输出参数**:

| 参数名 | 类型 | 说明 |
|--------|------|------|
| time | long | 交易日期 |
| open | float | 开盘价 |
| close | float | 收盘价 |
| high | float | 最高价 |
| low | float | 最低价 |
| volume | float | 成交量（手） |
| amount | float | 成交额（元） |
| k | float | 指标计算值 |
| d | float | 指标计算值 |


### 144. t3指标

**描述**: T3是三重移动平均线，全称是Triple Exponential Moving Average。它是指数移动平均线（EMA）的一种改进版本。T3指标通过使用三次平滑来减少EMA的滞后性，并提供更快的响应速度。

**标签**: 金融指标

**权限要求**: 青铜版、白银版、黄金版、至尊版

**接口地址**: `http://api.xtick.top/doc/t3`

**请求示例**:
```
http://api.xtick.top/doc/t3?type=1&code=000001&fq=1&period=1d&startDate=2026-05-16&endDate=2026-05-16&inReal=2&optInTimePeriod=20&optInVFactor=1&scale=3&round=1&token=123456789
```

**输入参数**:

| 参数名 | 类型 | 必填 | 取值范围 | 说明 |
|--------|------|------|----------|------|
| type | int | 否 | 1-沪深京A股，2-沪深指数，3-港股，4-ETF基金，5-沪深可转债 | 标的类型 |
| code | String | 否 | - | 股票代码，仅支持单个股票获取，不支持批量参数，不支持ALL参数。 |
| fq | String | 否 | 1-不复权，2-前复权，3-后复权，4-等比前复权，5-等比后复权 | 复权类型 |
| period | String | 否 | 1m-1分钟线，5m-5分钟线，15m-15分钟线，30m-30分钟线，1h-1小时线，1d-日线，1w-周线，1mon-月线，1q-季度线，1y-年线 | K线周期 |
| startDate | String | 否 | - | 开始日期 |
| endDate | String | 否 | - | 结束日期 |
| inReal | int | 否 | 1-开盘价，2-收盘价，3-最高价，4-最低价，5-成交量，6-成交额 | 数据标签 |
| optInTimePeriod | int | 否 | - | 移动平均线周期 |
| optInVFactor | double | 否 | - | va系数 |
| scale | int | 否 | - | 精度（保留几位小数位） |
| round | int | 否 | - | 舍入方式，参考：https://www.cnblogs.com/yingchen/p/5459501.html |
| token | String | 是 | - | 登录网站获取token |

**输出参数**:

| 参数名 | 类型 | 说明 |
|--------|------|------|
| time | long | 交易日期 |
| open | float | 开盘价 |
| close | float | 收盘价 |
| high | float | 最高价 |
| low | float | 最低价 |
| volume | float | 成交量（手） |
| amount | float | 成交额（元） |
| output | float | 指标计算值 |


### 147. tema指标

**描述**: TEMA是三重指数移动平均线，全程为Triple Exponential Moving Average。它是一种技术分析指标，用于平滑价格数据并识别趋势的变化。TEMA通过多次平滑价格数据来减少价格波动的影响，从而更准确地识别趋势的变化。

**标签**: 金融指标

**权限要求**: 青铜版、白银版、黄金版、至尊版

**接口地址**: `http://api.xtick.top/doc/tema`

**请求示例**:
```
http://api.xtick.top/doc/tema?type=1&code=000001&fq=1&period=1d&startDate=2026-05-16&endDate=2026-05-16&inReal=2&optInTimePeriod=20&scale=3&round=1&token=123456789
```

**输入参数**:

| 参数名 | 类型 | 必填 | 取值范围 | 说明 |
|--------|------|------|----------|------|
| type | int | 否 | 1-沪深京A股，2-沪深指数，3-港股，4-ETF基金，5-沪深可转债 | 标的类型 |
| code | String | 否 | - | 股票代码，仅支持单个股票获取，不支持批量参数，不支持ALL参数。 |
| fq | String | 否 | 1-不复权，2-前复权，3-后复权，4-等比前复权，5-等比后复权 | 复权类型 |
| period | String | 否 | 1m-1分钟线，5m-5分钟线，15m-15分钟线，30m-30分钟线，1h-1小时线，1d-日线，1w-周线，1mon-月线，1q-季度线，1y-年线 | K线周期 |
| startDate | String | 否 | - | 开始日期 |
| endDate | String | 否 | - | 结束日期 |
| inReal | int | 否 | 1-开盘价，2-收盘价，3-最高价，4-最低价，5-成交量，6-成交额 | 数据标签 |
| optInTimePeriod | int | 否 | - | 移动平均线周期 |
| scale | int | 否 | - | 精度（保留几位小数位） |
| round | int | 否 | - | 舍入方式，参考：https://www.cnblogs.com/yingchen/p/5459501.html |
| token | String | 是 | - | 登录网站获取token |

**输出参数**:

| 参数名 | 类型 | 说明 |
|--------|------|------|
| time | long | 交易日期 |
| open | float | 开盘价 |
| close | float | 收盘价 |
| high | float | 最高价 |
| low | float | 最低价 |
| volume | float | 成交量（手） |
| amount | float | 成交额（元） |
| output | float | 指标计算值 |


### 148. trima指标

**描述**: TRIMA是三重指数平均线，全称为Triangular Moving Average。它是一种平滑的移动平均线，通过将价格数据进行多次平均处理来消除噪音和波动。

**标签**: 金融指标

**权限要求**: 青铜版、白银版、黄金版、至尊版

**接口地址**: `http://api.xtick.top/doc/trima`

**请求示例**:
```
http://api.xtick.top/doc/trima?type=1&code=000001&fq=1&period=1d&startDate=2026-05-16&endDate=2026-05-16&inReal=2&optInTimePeriod=20&scale=3&round=1&token=123456789
```

**输入参数**:

| 参数名 | 类型 | 必填 | 取值范围 | 说明 |
|--------|------|------|----------|------|
| type | int | 否 | 1-沪深京A股，2-沪深指数，3-港股，4-ETF基金，5-沪深可转债 | 标的类型 |
| code | String | 否 | - | 股票代码，仅支持单个股票获取，不支持批量参数，不支持ALL参数。 |
| fq | String | 否 | 1-不复权，2-前复权，3-后复权，4-等比前复权，5-等比后复权 | 复权类型 |
| period | String | 否 | 1m-1分钟线，5m-5分钟线，15m-15分钟线，30m-30分钟线，1h-1小时线，1d-日线，1w-周线，1mon-月线，1q-季度线，1y-年线 | K线周期 |
| startDate | String | 否 | - | 开始日期 |
| endDate | String | 否 | - | 结束日期 |
| inReal | int | 否 | 1-开盘价，2-收盘价，3-最高价，4-最低价，5-成交量，6-成交额 | 数据标签 |
| optInTimePeriod | int | 否 | - | 移动平均线周期 |
| scale | int | 否 | - | 精度（保留几位小数位） |
| round | int | 否 | - | 舍入方式，参考：https://www.cnblogs.com/yingchen/p/5459501.html |
| token | String | 是 | - | 登录网站获取token |

**输出参数**:

| 参数名 | 类型 | 说明 |
|--------|------|------|
| time | long | 交易日期 |
| open | float | 开盘价 |
| close | float | 收盘价 |
| high | float | 最高价 |
| low | float | 最低价 |
| volume | float | 成交量（手） |
| amount | float | 成交额（元） |
| output | float | 指标计算值 |


### 149. trix指标

**描述**: TRIX金融指标的中文名称是三重指数平滑平均线，英文名称是Triple Exponential Moving Average (TRIX)。TRIX是一种技术分析指标，用于衡量资产价格的趋势强度。

**标签**: 金融指标

**权限要求**: 青铜版、白银版、黄金版、至尊版

**接口地址**: `http://api.xtick.top/doc/trix`

**请求示例**:
```
http://api.xtick.top/doc/trix?type=1&code=000001&fq=1&period=1d&startDate=2026-05-16&endDate=2026-05-16&inReal=2&optInTimePeriod=20&scale=3&round=1&token=123456789
```

**输入参数**:

| 参数名 | 类型 | 必填 | 取值范围 | 说明 |
|--------|------|------|----------|------|
| type | int | 否 | 1-沪深京A股，2-沪深指数，3-港股，4-ETF基金，5-沪深可转债 | 标的类型 |
| code | String | 否 | - | 股票代码，仅支持单个股票获取，不支持批量参数，不支持ALL参数。 |
| fq | String | 否 | 1-不复权，2-前复权，3-后复权，4-等比前复权，5-等比后复权 | 复权类型 |
| period | String | 否 | 1m-1分钟线，5m-5分钟线，15m-15分钟线，30m-30分钟线，1h-1小时线，1d-日线，1w-周线，1mon-月线，1q-季度线，1y-年线 | K线周期 |
| startDate | String | 否 | - | 开始日期 |
| endDate | String | 否 | - | 结束日期 |
| inReal | int | 否 | 1-开盘价，2-收盘价，3-最高价，4-最低价，5-成交量，6-成交额 | 数据标签 |
| optInTimePeriod | int | 否 | - | 移动平均线周期 |
| scale | int | 否 | - | 精度（保留几位小数位） |
| round | int | 否 | - | 舍入方式，参考：https://www.cnblogs.com/yingchen/p/5459501.html |
| token | String | 是 | - | 登录网站获取token |

**输出参数**:

| 参数名 | 类型 | 说明 |
|--------|------|------|
| time | long | 交易日期 |
| open | float | 开盘价 |
| close | float | 收盘价 |
| high | float | 最高价 |
| low | float | 最低价 |
| volume | float | 成交量（手） |
| amount | float | 成交额（元） |
| output | float | 指标计算值 |


### 150. truerange指标

**描述**: 称为真实波幅，英文名称为True Range。它是一种衡量价格波动性的指标。

**标签**: 金融指标

**权限要求**: 青铜版、白银版、黄金版、至尊版

**接口地址**: `http://api.xtick.top/doc/truerange`

**请求示例**:
```
http://api.xtick.top/doc/truerange?type=1&code=000001&fq=1&period=1d&startDate=2026-05-16&endDate=2026-05-16&scale=3&round=1&token=123456789
```

**输入参数**:

| 参数名 | 类型 | 必填 | 取值范围 | 说明 |
|--------|------|------|----------|------|
| type | int | 否 | 1-沪深京A股，2-沪深指数，3-港股，4-ETF基金，5-沪深可转债 | 标的类型 |
| code | String | 否 | - | 股票代码，仅支持单个股票获取，不支持批量参数，不支持ALL参数。 |
| fq | String | 否 | 1-不复权，2-前复权，3-后复权，4-等比前复权，5-等比后复权 | 复权类型 |
| period | String | 否 | 1m-1分钟线，5m-5分钟线，15m-15分钟线，30m-30分钟线，1h-1小时线，1d-日线，1w-周线，1mon-月线，1q-季度线，1y-年线 | K线周期 |
| startDate | String | 否 | - | 开始日期 |
| endDate | String | 否 | - | 结束日期 |
| scale | int | 否 | - | 精度（保留几位小数位） |
| round | int | 否 | - | 舍入方式，参考：https://www.cnblogs.com/yingchen/p/5459501.html |
| token | String | 是 | - | 登录网站获取token |

**输出参数**:

| 参数名 | 类型 | 说明 |
|--------|------|------|
| time | long | 交易日期 |
| open | float | 开盘价 |
| close | float | 收盘价 |
| high | float | 最高价 |
| low | float | 最低价 |
| volume | float | 成交量（手） |
| amount | float | 成交额（元） |
| output | float | 指标计算值 |


### 153. ultosc指标

**描述**: ULTOSC称为综合摆动指标，英文名称：Ultimate Oscillator (ULTOSC)。综合摆动指标是一种多周期的技术指标，用于衡量市场买卖力量的强弱。它通过将短期、中期和长期的价格波动进行加权平均，以提供更全面的市场信号。

**标签**: 金融指标

**权限要求**: 青铜版、白银版、黄金版、至尊版

**接口地址**: `http://api.xtick.top/doc/ultosc`

**请求示例**:
```
http://api.xtick.top/doc/ultosc?type=1&code=000001&fq=1&period=1d&startDate=2026-05-16&endDate=2026-05-16&optInTimePeriod1=7&optInTimePeriod2=14&optInTimePeriod3=28&scale=3&round=1&token=123456789
```

**输入参数**:

| 参数名 | 类型 | 必填 | 取值范围 | 说明 |
|--------|------|------|----------|------|
| type | int | 否 | 1-沪深京A股，2-沪深指数，3-港股，4-ETF基金，5-沪深可转债 | 标的类型 |
| code | String | 否 | - | 股票代码，仅支持单个股票获取，不支持批量参数，不支持ALL参数。 |
| fq | String | 否 | 1-不复权，2-前复权，3-后复权，4-等比前复权，5-等比后复权 | 复权类型 |
| period | String | 否 | 1m-1分钟线，5m-5分钟线，15m-15分钟线，30m-30分钟线，1h-1小时线，1d-日线，1w-周线，1mon-月线，1q-季度线，1y-年线 | K线周期 |
| startDate | String | 否 | - | 开始日期 |
| endDate | String | 否 | - | 结束日期 |
| optInTimePeriod1 | int | 否 | - | 移动平均线周期1 |
| optInTimePeriod2 | int | 否 | - | 移动平均线周期2 |
| optInTimePeriod3 | int | 否 | - | 移动平均线周期3 |
| scale | int | 否 | - | 精度（保留几位小数位） |
| round | int | 否 | - | 舍入方式，参考：https://www.cnblogs.com/yingchen/p/5459501.html |
| token | String | 是 | - | 登录网站获取token |

**输出参数**:

| 参数名 | 类型 | 说明 |
|--------|------|------|
| time | long | 交易日期 |
| open | float | 开盘价 |
| close | float | 收盘价 |
| high | float | 最高价 |
| low | float | 最低价 |
| volume | float | 成交量（手） |
| amount | float | 成交额（元） |
| output | float | 指标计算值 |


### 156. willr指标

**描述**: WILLR指标的中文名称为威廉指标(WR)，英文名称为Williams' %R(W%R)。该指标通过测量价格在给定时间周期内的相对变动程度，来判断市场的超买和超卖情况。

**标签**: 金融指标

**权限要求**: 青铜版、白银版、黄金版、至尊版

**接口地址**: `http://api.xtick.top/doc/willr`

**请求示例**:
```
http://api.xtick.top/doc/willr?type=1&code=000001&fq=1&period=1d&startDate=2026-05-16&endDate=2026-05-16&optInTimePeriod=10&scale=3&round=1&token=123456789
```

**输入参数**:

| 参数名 | 类型 | 必填 | 取值范围 | 说明 |
|--------|------|------|----------|------|
| type | int | 否 | 1-沪深京A股，2-沪深指数，3-港股，4-ETF基金，5-沪深可转债 | 标的类型 |
| code | String | 否 | - | 股票代码，仅支持单个股票获取，不支持批量参数，不支持ALL参数。 |
| fq | String | 否 | 1-不复权，2-前复权，3-后复权，4-等比前复权，5-等比后复权 | 复权类型 |
| period | String | 否 | 1m-1分钟线，5m-5分钟线，15m-15分钟线，30m-30分钟线，1h-1小时线，1d-日线，1w-周线，1mon-月线，1q-季度线，1y-年线 | K线周期 |
| startDate | String | 否 | - | 开始日期 |
| endDate | String | 否 | - | 结束日期 |
| optInTimePeriod | int | 否 | - | 移动平均线周期 |
| scale | int | 否 | - | 精度（保留几位小数位） |
| round | int | 否 | - | 舍入方式，参考：https://www.cnblogs.com/yingchen/p/5459501.html |
| token | String | 是 | - | 登录网站获取token |

**输出参数**:

| 参数名 | 类型 | 说明 |
|--------|------|------|
| time | long | 交易日期 |
| open | float | 开盘价 |
| close | float | 收盘价 |
| high | float | 最高价 |
| low | float | 最低价 |
| volume | float | 成交量（手） |
| amount | float | 成交额（元） |
| output | float | 指标计算值 |


### 157. wma指标

**描述**: WMA指标是一种移动平均线指标，全称为Weighted Moving Average。它是一种加权平均线指标，与简单移动平均线（SMA）不同，WMA在计算平均值时给予较近期的数据更高的权重。

**标签**: 金融指标

**权限要求**: 青铜版、白银版、黄金版、至尊版

**接口地址**: `http://api.xtick.top/doc/wma`

**请求示例**:
```
http://api.xtick.top/doc/wma?type=1&code=000001&fq=1&period=1d&startDate=2026-05-16&endDate=2026-05-16&inReal=2&optInTimePeriod=20&scale=3&round=1&token=123456789
```

**输入参数**:

| 参数名 | 类型 | 必填 | 取值范围 | 说明 |
|--------|------|------|----------|------|
| type | int | 否 | 1-沪深京A股，2-沪深指数，3-港股，4-ETF基金，5-沪深可转债 | 标的类型 |
| code | String | 否 | - | 股票代码，仅支持单个股票获取，不支持批量参数，不支持ALL参数。 |
| fq | String | 否 | 1-不复权，2-前复权，3-后复权，4-等比前复权，5-等比后复权 | 复权类型 |
| period | String | 否 | 1m-1分钟线，5m-5分钟线，15m-15分钟线，30m-30分钟线，1h-1小时线，1d-日线，1w-周线，1mon-月线，1q-季度线，1y-年线 | K线周期 |
| startDate | String | 否 | - | 开始日期 |
| endDate | String | 否 | - | 结束日期 |
| inReal | int | 否 | 1-开盘价，2-收盘价，3-最高价，4-最低价，5-成交量，6-成交额 | 数据标签 |
| optInTimePeriod | int | 否 | - | 移动平均线周期 |
| scale | int | 否 | - | 精度（保留几位小数位） |
| round | int | 否 | - | 舍入方式，参考：https://www.cnblogs.com/yingchen/p/5459501.html |
| token | String | 是 | - | 登录网站获取token |

**输出参数**:

| 参数名 | 类型 | 说明 |
|--------|------|------|
| time | long | 交易日期 |
| open | float | 开盘价 |
| close | float | 收盘价 |
| high | float | 最高价 |
| low | float | 最低价 |
| volume | float | 成交量（手） |
| amount | float | 成交额（元） |
| output | float | 指标计算值 |


---

## 注意事项

1. 所有接口都需要携带token参数进行认证
2. 日期格式统一为: yyyy-MM-dd (如: 2026-01-01)
3. 股票代码格式: 6位数字 (如: 000001)
4. 部分接口支持批量查询，多个股票代码用逗号分隔
5. 请遵守API调用频率限制，避免频繁请求

---
name: xtick
description: XTick股票实时数据API技能，提供沪深京A股、ETF、指数、港股等金融数据的获取能力，包括行情数据、财务数据、技术指标、热点数据等85+接口，支持WebSocket实时数据推送
---

# XTick 股票数据技能

## 📋 概述

XTick是一个专业的股票实时数据API平台，提供全面、准确、稳定的金融数据服务。本技能帮助AI助手通过自然语言交互获取股票数据。

**核心能力**:
- 📊 实时行情数据（K线、分钟线、Tick数据）
- 💰 财务报表数据（资产负债表、利润表、现金流量表）
- 📈 技术指标计算（MACD、KDJ、RSI等100+指标）
- 🔥 市场热点追踪（龙虎榜、资金流向、连板天梯）
- 🎯 量化因子数据（涨速、换手率、市盈率等）
- 📰 新闻资讯聚合（主流财经媒体）
- ⚡ **WebSocket实时推送**（全市场实时数据订阅）

## 🚀 快速开始

### 1. 环境准备

```bash
# 安装Python环境（推荐3.7+）
# 安装依赖包
pip install requests pandas
```

### 2. 获取Token

1. 访问 [XTick官网](http://www.xtick.top/user/account) 注册账号
2. 登录后在个人中心获取API Token
3. 将Token配置到代码中使用

### 3. WebSocket快速开始（新增）

**环境准备**:

```bash
# 安装WebSocket依赖
pip install websocket-client requests
```

**基础示例：订阅个股Tick数据**

```python
import json
import urllib
from xtick.scripts.XTickWebSocketClient import XTickWebSocketClient
from xtick.scripts.Config import Config

# 定义要订阅的股票代码列表
auth_codes = ["000001.SZ", "600000.SH"]  # 平安银行、浦发银行

# 构建用户信息JSON
user_info = json.dumps({
    "token": Config.TOKEN,
    "authCodes": auth_codes
})

# URL编码用户信息
user_encoded = urllib.parse.quote(user_info)
endpoint_uri = f"ws://ws.xtick.top/ws/{user_encoded}"

# 创建并启动WebSocket客户端
xTickClient = XTickWebSocketClient(endpoint_uri)
xTickClient.start()  # 开始接收实时数据
```

**高级示例：多市场订阅**

```python
# 订阅多个市场的股票（需要相应权限）
auth_codes = [
    "000001.SZ",   # 深交所A股
    "600000.SH",   # 上交所A股
    "00001.HK",    # 港交所港股
    "920001.BJ",   # 北交所A股
    "000001.SH",   # 上证指数
    "510300.SH"    # 上交所ETF
]

user_info = json.dumps({
    "token": Config.TOKEN,
    "authCodes": auth_codes
})

user_encoded = urllib.parse.quote(user_info)
endpoint_uri = f"ws://ws.xtick.top/ws/{user_encoded}"

xTickClient = XTickWebSocketClient(endpoint_uri)
xTickClient.start()
```

**支持的订阅类型**:

- **个股Tick数据**: `000001.SZ`, `600000.SH` - 按股票代码订阅（推荐方式）
- **全市场Tick**: `tick.SZ.1`, `tick.SH.1`, `tick.BJ.1`, `tick.HK.3`
- **指数Tick**: `tick.SZ.10`, `tick.SH.10`
- **ETF Tick**: `tick.SZ.20`, `tick.SH.20`
- **分钟K线（实时）**: `minute.SZ.1`, `minute.SH.1`, `minute.BJ.1`, `minute.HK.3`
- **量化因子（实时）**: `quant.time.1`
- **量化因子（每分钟）**: `quant.data.1`
- **竞价数据**: `bid.1` - 集合竞价期间数据

**WebSocket客户端实现详解**:

```python
import json
import urllib
import websocket
from xtick.scripts.Config import Config
from xtick.scripts.util import XTickUtil

class XTickWebSocketClient(object):
    def __init__(self, url):
        self.url = url
        self.ws = None

    def on_open(self, ws):
        """
        WebSocket连接建立时的回调函数
        
        参数:
            ws: WebSocketApp对象
        """
        print('A new WebSocketApp is opened and subscribed!')

    def on_data(self, ws, string, type, continue_flag):
        """
        接收数据时的回调函数
        
        参数:
            ws: WebSocketApp对象
            string: 从服务器接收的数据（utf-8字符串或二进制数据）
            type: 数据类型，可能是 ABNF.OPCODE_TEXT 或 ABNF.OPCODE_BINARY
            continue_flag: continuation flag，如果为0表示数据继续
        """
        if type == websocket.ABNF.OPCODE_BINARY:
            # 处理二进制数据（可能经过gzip或zip压缩）
            packet = XTickUtil.process_data(string)
            print(f"Received data: {packet}")
        elif type == websocket.ABNF.OPCODE_TEXT:
            # 处理文本数据
            print(f"Received data: {string}")

    def on_error(self, ws, error):
        """
        发生错误时的回调函数
        
        参数:
            ws: WebSocketApp对象
            error: 异常对象
        """
        print(error)

    def on_close(self, ws, close_status_code, close_msg):
        """
        连接关闭时的回调函数
        
        参数:
            ws: WebSocketApp对象
            close_status_code: 关闭状态码
            close_msg: 关闭消息
        """
        print('The connection is closed!')
        # 可选：实现自动重连
        # import time
        # time.sleep(3)
        # self.start()

    def start(self):
        """启动WebSocket连接"""
        self.ws = websocket.WebSocketApp(
            self.url,
            on_open=self.on_open,
            on_data=self.on_data,
            on_error=self.on_error,
            on_close=self.on_close,
        )
        self.ws.run_forever()
```

**数据处理说明**:

XTickUtil模块提供自动解压缩功能，支持gzip和zip格式：
- 当接收到二进制数据（`OPCODE_BINARY`）时，自动调用 `XTickUtil.process_data()` 处理
- 检测数据压缩格式（通过文件头字节判断）
- 如果是gzip格式，使用gzip解压
- 如果是zip格式，从zip中提取JSON文件
- 否则直接解码为UTF-8字符串
- 处理后的数据为JSON格式，可直接解析使用

**注意事项**:

1. **权限限制**：新用户默认可以订阅北交所的tick行情数据，具体权限请参考XTick官网
2. **网络连接**：当前使用非加密连接（`ws://`），确保网络稳定
3. **数据量控制**：推荐使用个股代码订阅而非全市场订阅，避免性能问题
4. **错误处理**：建议在 `on_close` 回调中实现自动重连逻辑
5. **Token安全**：妥善保管API Token，建议存储在环境变量或配置文件中

**常见问题**:

- **Q: 连接失败怎么办？**  
  A: 检查Token是否正确、网络连接是否正常、防火墙是否阻止WebSocket连接

- **Q: 为什么我的连接立即关闭了？**  
  A: 可能Token无效、authCodes格式错误或包含无权访问的代码，检查 `on_error` 回调中的错误信息

- **Q: 如何实现断线重连？**  
  A: 在 `on_close` 回调中添加重连逻辑（见上方代码注释）

- **Q: 如果创建WebSocket客户端失败怎么办？**  
  A: 参考官方开源项目获取完整示例代码：
  - Skill项目: https://github.com/xticktop/skills
  - Python版项目: https://github.com/xticktop/DemoXtickPythonSkill
  - Java版项目: https://github.com/xticktop/DemoXtickJava
  - 主项目: https://github.com/xticktop/xtick

详细文档请参考: [WebSocket接入指南](references/websocket.md)

### 4. Python HTTP API调用示例

```python
from xtick.scripts.api import XTickMarketApi

# 配置Token
token = "your_token_here"
# 获取平安银行日K线数据
result = XTickMarketApi.getKlineMarket(
    type=1,           # 沪深京A股
    code="000001",    # 股票代码
    fq="1",           # 不复权
    period="1d",      # 日线
    startDate="2025-01-01",
    endDate="2026-01-01",
    token=token
)
print(result)
```

## 🔑 权限等级说明

XTick采用分级权限制度，不同等级可访问的接口不同：

| 等级 | 名称 | 可访问接口 |
|------|------|-----------|
| 1 | 青铜版 | 基础行情数据、财务数据、技术指标 |
| 2 | 白银版 | + 盯盘数据（五档行情、成交统计） |
| 3 | 黄金版 | + 核心数据（竞价数据、除权停牌） |
| 4 | 至尊版 | + 热点数据（龙虎榜、资金流向、新闻） |
| 5 | 量化版 | + 量化因子（全市场实时推送） |

## 📝 参数格式规范

### 通用参数

- **token**: 必填，从官网获取的认证令牌
- **type**: 标的类型
  - `1`: 沪深京A股
  - `2`: 沪深指数
  - `3`: 港股
  - `4`: ETF基金
  - `5`: 沪深可转债
- **code**: 股票代码，6位数字（如 `000001`），部分接口支持 `all` 获取全市场数据
- **日期格式**: `yyyy-MM-dd`（如 `2026-01-01`）
- **复权类型 (fq)**:
  - `1`: 不复权
  - `2`: 前复权
  - `3`: 后复权
  - `4`: 等比前复权
  - `5`: 等比后复权
- **K线周期 (period)**:
  - 分钟线: `1m`, `5m`, `15m`, `30m`, `1h`
  - 日线及以上: `1d`, `1w`, `1mon`, `1q`, `1y`

## 📚 接口分类概览

### 1. 行情数据接口 (8个接口)

- **股票列表**: 按照股票池获取股票代码，包括沪深京A股、港股、沪深指数、ETF、可转债几类数据。... [青铜版、白银版、黄金版、至尊版]
- **交易日历**: 获取A股交易日历，包含交易所交易日历和个股交易日历。交易所是指上交所、深交所、北交所的交易日历。数据从2020年开始。... [青铜版、白银版、黄金版、至尊版]
- **分钟数据-实时接口**: 提供日内一分钟实时数据，这个分钟接口调取数据会更快。... [青铜版、白银版、黄金版、至尊版]
- **行情数据-通用接口**: 行情数据包括1分钟K线、5分钟K线、15分钟K线、30分钟K线、1小时K线、日K线、周K线、季度K线、年K线。支持复权数... [青铜版、白银版、黄金版、至尊版]
- **股东数**: 股东数，数据范围：2001年-至今... [青铜版、白银版、黄金版、至尊版]
- **财务指标**: 财务指标，数据范围：2007年-至今。... [青铜版、白银版、黄金版、至尊版]
- **十大股东**: 十大股东，数据范围：公司上市-至今... [青铜版、白银版、黄金版、至尊版]
- **十大流通股东**: 十大流通股东，数据范围：2004年-至今... [青铜版、白银版、黄金版、至尊版]

### 2. 盯盘数据接口 (6个接口)

- **龙虎榜-历史数据**: 龙虎榜详情历史数据，盘后更新数据。... [白银版、黄金版、至尊版]
- **日K线-实时数据**: 获取盘中实时日K线数据。支持批量参数，支持ALL参数。... [白银版、黄金版、至尊版]
- **分钟K线-实时数据**: 获取盘中分钟K线实时数据。支持批量参数，支持ALL参数。... [白银版、黄金版、至尊版]
- **买卖五档-实时数据**: 获取盘中买卖五档盘口实时数据，Tick实时数据。支持批量参数，支持ALL参数。... [白银版、黄金版、至尊版]
- **买卖五档-历史数据**: 获取盘中买卖五档历史数据，盘后更新数据。... [白银版、黄金版、至尊版]
- **成交统计-实时接口**: 按交易日，获取全市场成交额统计，包括科创板、创业板、北证、沪深两市等成交额统计。... [白银版、黄金版、至尊版]

### 3. 核心数据接口 (8个接口)

- **竞价数据-实时接口**: 获取沪深京股票交易日盘中实时竞价数据，竞价时间段：9:15-9:25。每次调用接口返回最新竞价数据。... [黄金版、至尊版]
- **核心指标-实时接口**: 获取沪深京股票交易日盘中实时指标数据，包括涨速、换手率、市盈率、市净率、涨幅、均价、涨停板等数据。... [黄金版、至尊版]
- **除权变更数据**: 股票除权除息历史数据，可以获取有复权变化的股票数据。可以按单个股票获取个股除权除息历史记录，也可以使用all参数，获取全... [黄金版、至尊版]
- **停牌数据**: 停牌股票历史数据，盘后更新。... [黄金版、至尊版]
- **ST数据**: ST股票历史数据，数据从2022年3月开始，盘后更新。... [黄金版、至尊版]
- **涨跌停价格**: 获取股票涨跌停历史数据，盘后更新。如果需要盘中实时获取涨停板、跌停板、炸板数据，请参考[连板天梯-实时接口]... [黄金版、至尊版]
- **分笔数据**: 股票分时成交数据接口，盘后更新。... [黄金版、至尊版]
- **分价数据**: 股票分价成交数据接口，盘后更新。... [黄金版、至尊版]

### 4. 短线热点接口 (10个接口)

- **连板天梯-实时接口**: 获取沪深京股票交易日盘中盘中涨停板、跌停板、炸板数据。梯队完整度是超短的重要指标。盘中实时更新。如果获取盘中实时数据，则... [至尊版]
- **市场情绪-实时接口**: 市场情绪，短线选手复盘必备工具。如果获取盘中实时数据，则tradeDate参数设置为当天交易日。... [至尊版]
- **资金流向-实时接口**: 获取沪深京股票交易日盘中资金流数据。盘中实时更新。... [至尊版]
- **竞价数据-历史接口**: 竞价历史数据，该接口仅保留集合竞价期间的最后一条竞价数据和开盘数据。... [黄金版、至尊版]
- **竞价详情-实时接口**: 开盘集合竞价阶段，个股的所有竞价信息。当天竞价完成后，9:25更新完数据。请求一次，获取交易日当天全市场竞价详情数据，请... [黄金版、至尊版]
- **新闻资讯-实时接口**: 获取财联社、新浪财经、格隆汇、华尔街见闻、凤凰网、同花顺、东方财富、雪球等主流金融平台资讯信息，跟随市场热点、核心。盘中... [至尊版]
- **日内分时-实时接口**: 获取股票盘中日内分时数据，保留了价格在每个时间点的变化细节，股价全天的波动轨迹。盘中实时更新。... [至尊版]
- **概念板块成分股数据**: 获取概念板块、地域板块、行业板块数据，以及概念板块下对应的成分股数据。... [至尊版]
- **股票关联概念板块数据**: 获取个股关联的概念板块、地域板块、行业板块数据。... [至尊版]
- **增量更新**: 提供交易日当天全市场增量数据的更新，是收盘后的历史数据，是为了方便大家能快速的获取全市场数据，节省大家获取数据的时间，不... [黄金版、至尊版]

### 8. 量化因子接口 (2个接口)

- **量化因子-实时接口**: 获取沪深京股票交易日盘中因子指标数据，实时推送，包括涨速、换手率、市盈率、市净率等。支持数据全推。... [量化版]
- **量化因子-历史接口**: 获取沪深京股票交易日盘中因子指标历史数据，盘后更新。... [量化版]

### 9. 金融指标接口 (51个接口)

- **ad指标**: AD指标（Accumulation/Distribution）是一种用于量化分析股票、期货或其他金融资产的技术指标。它主... [青铜版、白银版、黄金版、至尊版]
- **adosc指标**: ADOSC指标是一种技术分析指标，全称为累积/派发指标（Accumulation/Distribution Oscill... [青铜版、白银版、黄金版、至尊版]
- **adx指标**: ADX指标（Average Directional Movement Index）是一种技术分析指标，用于衡量市场趋势的... [青铜版、白银版、黄金版、至尊版]
- **adxr指标**: ADXR指标（Average Directional Movement Index Rating）是根据ADX指标（Av... [青铜版、白银版、黄金版、至尊版]
- **apo指标**: APO（Absolute Price Oscillator）指标是一种技术分析指标，用于衡量股票价格的变动幅度。它计算了... [青铜版、白银版、黄金版、至尊版]
- **aroon指标**: AROON指标的中文名称是“阿隆指标”，该指标是一种技术分析指标，用于衡量价格趋势的强度和趋势的方向。阿隆指标由两条线组... [青铜版、白银版、黄金版、至尊版]
- **aroonosc指标**: AroonOsc指标的名称是Aroon Oscillator（阿隆振荡器），它是Aroon指标的衍生指标。... [青铜版、白银版、黄金版、至尊版]
- **atr指标**: ATR称为真实波动幅度指标，英文名称为Average True Range。ATR指标是一种衡量市场波动性的技术指标，它... [青铜版、白银版、黄金版、至尊版]
- **bbands指标**: BBANDS称为布林带，英文名称为Bollinger Bands。布林带是一种基于统计学原理的技术分析指标，由约翰·布林... [青铜版、白银版、黄金版、至尊版]
- **bop指标**: BOP指标的名称是Balance of Power，也称为能量平衡指标。... [青铜版、白银版、黄金版、至尊版]
- **cci指标**: CCI指标的全称是“商品通道指数”（Commodity Channel Index），它是一种技术分析指标，用于评估商品... [青铜版、白银版、黄金版、至尊版]
- **cmo指标**: CMO指标的名称：Chande Momentum Oscillator（CMO，钱德动量振荡器）。... [青铜版、白银版、黄金版、至尊版]
- **dema指标**: DEMA指标是一种双指数移动平均线，全称为Double Exponential Moving Average。DEMA指... [青铜版、白银版、黄金版、至尊版]
- **dx指标**: 动向指标(DMI)，英文名称是Directional Movement Index。动向指标（Dx）是一种技术分析指标，... [青铜版、白银版、黄金版、至尊版]
- **ema指标**: EMA指标是指数移动平均线，全称为Exponential Moving Average。它是一种常用的技术分析工具，用于... [青铜版、白银版、黄金版、至尊版]
- **httrendline指标**: HTTRENDLINE称为趋势线，英文名称为HTTRENDLINE。该指标是一种基于趋势线的技术分析工具。... [青铜版、白银版、黄金版、至尊版]
- **kama指标**: KAMA是考夫曼自适应移动平均线，全称为Kaufman Adaptive Moving Average。是由Perry ... [青铜版、白银版、黄金版、至尊版]
- **macd指标**: MACD指标的中文名称为移动平均线收敛/发散指标，英文名称为Moving Average Convergence Div... [青铜版、白银版、黄金版、至尊版]
- **macdext指标**: MACDEXT指标的中文名称是MACD扩展，英文名称是MACD Extended。MACD扩展是基于移动平均线收敛背离（... [青铜版、白银版、黄金版、至尊版]
- **macdfix指标**: MACDFIX指标的中文名称为移动平均收敛/背离指标，英文名称为Moving Average Convergence D... [青铜版、白银版、黄金版、至尊版]
- **mama指标**: MAMA是MESA自适应移动平均线，全称为MESA Adaptive Moving Average。它是根据价格的移动平... [青铜版、白银版、黄金版、至尊版]
- **mfi指标**: MFI指标的中文名称是资金流量指标，英文名称是Money Flow Index。MFI指标是一种衡量资金流入和流出的指标... [青铜版、白银版、黄金版、至尊版]
- **midpoint指标**: MIDPOINT是一种基于价格的技术指标，用于衡量价格趋势的中点。... [青铜版、白银版、黄金版、至尊版]
- **minusdi指标**: MINUSDI指标中文名称为负向动向指标，英文名称为Negative Directional Indicator。它是技... [青铜版、白银版、黄金版、至尊版]
- **minusdm指标**: MINUSDM指标的中文名称为负方向运动指标，英文名称为Minus Directional Movement Indic... [青铜版、白银版、黄金版、至尊版]
- **mom指标**: MOM金融指标的中文名称为动量指标，英文名称为Momentum Indicator。该指标是通过比较当前价格与一定期间前... [青铜版、白银版、黄金版、至尊版]
- **movingaverage指标**: MOVINGAVERAGE金融指标的中文名称为移动平均线，英文名称为Moving Average。该指标通过计算一段时间... [青铜版、白银版、黄金版、至尊版]
- **natr指标**: NATR称为归一化真实波动幅度，英文名称为Normalized Average True Range。该指标用于衡量市场... [青铜版、白银版、黄金版、至尊版]
- **obv指标**: OBV指标（On-Balance Volume）是一种量能指标，用于衡量成交量的变化趋势和预测价格趋势的强弱。OBV指标... [青铜版、白银版、黄金版、至尊版]
- **plusdi指标**: 正向移动方向指标，英文名称是Positive Directional Indicator。... [青铜版、白银版、黄金版、至尊版]
- **plusdm指标**: 正向动向变动指标，英文名称是Positive Directional Movement。... [青铜版、白银版、黄金版、至尊版]
- **ppo指标**: 价格振荡百分比指标，英文名称为Percentage Price Oscillator。... [青铜版、白银版、黄金版、至尊版]
- **roc指标**: ROC金融指标的中文名称是变动率，英文名称是Rate of Change，ROC指标是一种衡量价格变动速度的技。... [青铜版、白银版、黄金版、至尊版]
- **rocp指标**: ROCP指标的中文名称为变化率指标，英文名称为Rate of Change Percentage。... [青铜版、白银版、黄金版、至尊版]
- **rocr指标**: ROCR金融指标是指Rate of Change Ratio，其中文名称是变动率比率，英文名称是Rate of Chan... [青铜版、白银版、黄金版、至尊版]
- **rocr100指标**: ROCR100金融指标的中文名称为价格变动率，英文名称为Rate of Change Ratio 100，指标介绍为衡量... [青铜版、白银版、黄金版、至尊版]
- **rsi指标**: RSI指标是相对强弱指标，全称为Relative Strength Index。它是一种用于衡量市场超买超卖状态的技术指... [青铜版、白银版、黄金版、至尊版]
- **sar指标**: SAR称为抛物线指标，英文名称为Parabolic SAR (Stop and Reverse)。... [青铜版、白银版、黄金版、至尊版]
- **sarext指标**: SAREXT称为拓展停损点指标，英文名称为SAREXT (Extended Stop and Reverse Indic... [青铜版、白银版、黄金版、至尊版]
- **sma指标**: SMA指标是简单移动平均线，全称为Simple Moving Average。它是一种常用的技术分析指标，用于平滑价格数... [青铜版、白银版、黄金版、至尊版]
- **stoch指标**: STOCH是随机指标（KDJ指标），英文名称是Stochastic Oscillator。该指标是一种用来测量价格相对于... [青铜版、白银版、黄金版、至尊版]
- **stochf指标**: STOCHF是随机振荡指标（Stochastic Fast）。... [青铜版、白银版、黄金版、至尊版]
- **stochrsi指标**: 随机相对强弱指标，Stochastic Relative Strength Index (STOCHRSI)。... [青铜版、白银版、黄金版、至尊版]
- **t3指标**: T3是三重移动平均线，全称是Triple Exponential Moving Average。它是指数移动平均线（EM... [青铜版、白银版、黄金版、至尊版]
- **tema指标**: TEMA是三重指数移动平均线，全程为Triple Exponential Moving Average。它是一种技术分析... [青铜版、白银版、黄金版、至尊版]
- **trima指标**: TRIMA是三重指数平均线，全称为Triangular Moving Average。它是一种平滑的移动平均线，通过将价... [青铜版、白银版、黄金版、至尊版]
- **trix指标**: TRIX金融指标的中文名称是三重指数平滑平均线，英文名称是Triple Exponential Moving Avera... [青铜版、白银版、黄金版、至尊版]
- **truerange指标**: 称为真实波幅，英文名称为True Range。它是一种衡量价格波动性的指标。... [青铜版、白银版、黄金版、至尊版]
- **ultosc指标**: ULTOSC称为综合摆动指标，英文名称：Ultimate Oscillator (ULTOSC)。综合摆动指标是一种多周... [青铜版、白银版、黄金版、至尊版]
- **willr指标**: WILLR指标的中文名称为威廉指标(WR)，英文名称为Williams' %R(W%R)。该指标通过测量价格在给定时间周... [青铜版、白银版、黄金版、至尊版]
- **wma指标**: WMA指标是一种移动平均线指标，全称为Weighted Moving Average。它是一种加权平均线指标，与简单移动... [青铜版、白银版、黄金版、至尊版]

**总计**: 85个API接口

## 💡 常用场景示例

### 场景1: 获取股票行情数据

```python
# 获取单个股票的日K线数据
result = XTickMarketApi.getKlineMarket(
    type=1, code="000001", fq="1", period="1d",
    startDate="2025-01-01", endDate="2026-01-01",
    token=token
)

# 获取实时分钟数据
result = XTickMarketApi.getKlineMinute(
    type=1, code="000001", fq="1", token=token
)
```

### 场景2: 获取财务数据

```python
# 获取财务指标
result = XTickMarketApi.getCoreIndicator(
    code="000001",
    startDate="2023-01-01", endDate="2024-12-31",
    token=token
)

# 获取十大股东
result = XTickMarketApi.getTopHolder(
    code="000001",
    startDate="2023-01-01", endDate="2024-12-31",
    token=token
)
```

### 场景3: 获取技术指标

```python
from xtick.scripts.api import XTickIndicatorApi

# 计算MACD指标
result = XTickIndicatorApi.macd(
    type=1, code="000001", period="1d", fq="front",
    startDate="2025-01-01", endDate="2026-01-01",
    inReal=2, optInFastPeriod="12",
    optInSlowPeriod="26", optInSignalPeriod="9",
    token=token
)
```

### 场景4: 获取市场热点

```python
from xtick.scripts.api import XTickHotApi

# 获取龙虎榜数据
result = XTickHotApi.getLonghubangHistory(
    tradeDate="2026-01-15", token=token
)

# 获取资金流向
result = XTickHotApi.getMoneyFlow(
    type=1, code="000001",
    startDate="2026-01-01", endDate="2026-01-15",
    token=token
)
```

### 场景5: 获取量化因子

```python
from xtick.scripts.api import XTickQuantApi

# 获取全市场量化因子（需要量化版权限）
result = XTickQuantApi.getQuantDataRealtime(
    type=1, field="all", token=token
)
```

## 🛠️ Python API模块说明

XTick提供了以下Python API模块：

| 模块 | 说明 | 主要功能 |
|------|------|---------|
| `XTickMarketApi` | 行情数据接口 | 股票列表、交易日历、K线数据、财务数据 |
| `XTickWatchApi` | 盯盘数据接口 | 五档行情、成交统计、市场情绪 |
| `XTickCoreApi` | 核心数据接口 | 竞价数据、除权停牌、分笔分价 |
| `XTickHotApi` | 热点数据接口 | 龙虎榜、资金流向、新闻资讯 |
| `XTickQuantApi` | 量化因子接口 | 实时/历史量化因子数据 |
| `XTickIndicatorApi` | 金融指标接口 | MACD、KDJ、RSI等100+指标 |
| `XTickWebSocketClient` | **WebSocket客户端** | **实时数据订阅推送（新增）** |

## ⚠️ 注意事项

1. **Token安全**: 不要将Token硬编码在代码中，建议使用环境变量或配置文件
2. **频率限制**: 遵守API调用频率限制，避免频繁请求导致被封禁
3. **批量查询**: 部分接口支持批量查询（最多50个股票代码，用逗号分隔）
4. **时间跨度限制**: 分钟数据单次请求时间跨度不超过31天
5. **数据时效性**: 实时数据仅在交易时段更新，盘后数据为历史数据
6. **错误处理**: 建议添加异常处理，检查返回数据的有效性

## 📖 完整示例

完整的API调用示例请参考：
- [XTickStockApiClient.py](scripts/XTickStockApiClient.py) - 包含所有HTTP接口的调用示例
- [XTickWebSocketClient.py](scripts/XTickWebSocketClient.py) - WebSocket客户端完整示例
- [详细API文档](references/apidoc.md) - 完整的HTTP接口参数说明
- [WebSocket接入指南](references/websocket.md) - WebSocket实时数据订阅详解

## 🔗 相关链接

- **官方网站**: http://www.xtick.top/
- **在线文档**: http://www.xtick.top/apidoc
- **注册账号**: http://www.xtick.top/user/account
- **GitHub**: https://github.com/xticktop/xtick

## 🤖 AI助手使用提示

当用户询问股票相关数据时：

1. **识别意图**: 确定用户需要什么类型的数据（行情、财务、技术指标等）
2. **选择接口**: 
   - 历史数据或单次查询 → 使用HTTP API
   - 实时数据持续监控 → 使用WebSocket订阅
3. **确认参数**: 向用户确认必要的参数（股票代码、日期范围等）
4. **调用API**: 使用对应的Python API模块获取数据
5. **解释结果**: 用通俗易懂的语言解释返回的数据

**WebSocket使用场景**:
- 实时监控多只股票的价格变化
- 构建量化交易系统的实时数据源
- 盯盘工具开发（五档行情、成交统计）
- 实时捕捉涨停板、跌停板机会

**示例对话**:

```

用户: 帮我看看平安银行最近的股价走势

AI: 我将获取平安银行(000001)最近30天的日K线数据...

    [调用 getKlineMarket 接口]

    数据显示平安银行近期股价在X元到Y元之间波动...

```

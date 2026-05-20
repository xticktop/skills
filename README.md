# XTick Skill - 股票实时数据API技能

[![License](https://img.shields.io/badge/license-MIT-blue.svg)](LICENSE)
[![Python](https://img.shields.io/badge/python-3.7+-blue.svg)](https://www.python.org/)
[![APIs](https://img.shields.io/badge/apis-85+-green.svg)](xtick/references/apidoc.md)

## 📖 项目简介

XTick Skill是一个专业的股票实时数据API技能包，为AI助手（如Lingma、Claude Code、OpenCLAW等）提供自然语言交互获取金融数据的能力。

**核心特性**:
- ✅ **全面的数据覆盖**: 沪深京A股、ETF基金、主流指数、港股、可转债
- ✅ **丰富的数据类型**: 行情数据、财务数据、技术指标、热点数据、量化因子
- ✅ **双模式数据获取**: HTTP API查询 + WebSocket实时推送
- ✅ **85+ API接口**: 涵盖股票分析的各个维度
- ✅ **易于集成**: 提供Python SDK，开箱即用
- ✅ **AI友好**: 专为智能体设计的Skill规范文档

## 🎯 适用场景

- 📊 **量化交易**: 获取实时行情和技术指标进行策略回测
- 💼 **投资分析**: 查看财务报表、股东变化等基本面数据
- 🔥 **短线交易**: 追踪龙虎榜、资金流向、连板天梯等热点
- 📰 **资讯监控**: 聚合主流财经媒体新闻
- 🤖 **AI应用**: 为智能投顾、聊天机器人提供数据支持

## 🚀 快速开始

### 前置要求

- Python 3.7+
- Node.js (可选，用于skills管理工具)
- XTick账号和Token ([免费注册](http://www.xtick.top/user/account))

### 安装方法

#### 方法1: 直接复制（推荐）

```bash
# 克隆或下载本项目
git clone https://github.com/xticktop/skills.git
# 将 xtick 目录复制到你的 skills 目录
cp -r skills/xtick /your/skills/directory/
```

#### 方法2: 使用 npx skills 命令

```bash
# 从GitHub安装
npx skills add https://github.com/xticktop/skills.git --skill xtick

# 从Gitee安装（国内加速）
npx skills add https://gitee.com/xtick/skills.git --skill xtick
```

### 配置Token

编辑 `xtick/scripts/Config.py` 文件：

```python
class Config:
    SERVER_URL = "http://api.xtick.top"
    TOKEN = "your_token_here"  # 替换为你的Token
```

### 运行示例

```bash
# 进入项目目录
cd DemoXtickPythonSkill

# 安装依赖
pip install requests pandas websocket-client

# 运行HTTP API完整示例
python xtick/scripts/XTickStockApiClient.py

# 运行WebSocket客户端示例（实时数据接收）
python xtick/scripts/XTickWebSocketClient.py
```

## 📚 功能概览

XTick提供6大类共85+个HTTP API接口，以及WebSocket实时数据推送：

### ⚡ WebSocket实时推送（新增）

**特点**: 持续接收实时数据，无需频繁请求，毫秒级延迟

**支持的订阅类型**:
- **个股Tick**: `000001.SZ`, `600000.SH` - 按股票代码订阅（推荐）
- **全市场Tick**: `tick.SZ.1`, `tick.SH.1`, `tick.BJ.1`, `tick.HK.3`
- **指数Tick**: `tick.SZ.10`, `tick.SH.10`
- **ETF Tick**: `tick.SZ.20`, `tick.SH.20`
- **分钟K线（实时）**: `minute.SZ.1`, `minute.SH.1`, `minute.BJ.1`, `minute.HK.3`
- **量化因子（实时）**: `quant.time.1`
- **量化因子（每分钟）**: `quant.data.1`
- **竞价数据**: `bid.1` - 集合竞价期间数据

**使用场景**:
- 实时监控多只股票价格变化
- 构建量化交易系统数据源
- 开发盯盘工具（五档行情、成交统计）
- 捕捉涨停板/跌停板机会
- 实时获取市场情绪和资金流向

**快速开始**:

```python
import json
import urllib
from xtick.scripts.XTickWebSocketClient import XTickWebSocketClient
from xtick.scripts.Config import Config

# 订阅多个股票的实时Tick数据
auth_codes = ["000001.SZ", "600000.SH", "600519.SH"]

user_info = json.dumps({
    "token": Config.TOKEN,
    "authCodes": auth_codes
})

user_encoded = urllib.parse.quote(user_info)
endpoint_uri = f"ws://ws.xtick.top/ws/{user_encoded}"

# 创建并启动WebSocket客户端
xTickClient = XTickWebSocketClient(endpoint_uri)
xTickClient.start()  # 开始接收实时数据
```

详细文档: [WebSocket接入指南](xtick/references/websocket.md)

### 1️⃣ 行情数据接口 (8个)
- 股票列表、交易日历
- K线数据（分钟/日线/周线/月线等）
- 财务数据（股东数、财务指标、十大股东等）

### 2️⃣ 盯盘数据接口 (6个)
- 龙虎榜历史数据
- 实时日K线、分钟K线
- 买卖五档盘口、成交统计

### 3️⃣ 核心数据接口 (8个)
- 竞价数据（实时/历史）
- 核心指标（涨速、换手率、市盈率等）
- 除权变更、停牌数据、ST数据
- 涨跌停价格、分笔数据、分价数据

### 4️⃣ 短线热点接口 (10个)
- 连板天梯（涨停/跌停/炸板）
- 市场情绪指标
- 资金流向分析
- 新闻资讯聚合
- 日内分时数据
- 概念板块数据
- 增量更新接口

### 5️⃣ 量化因子接口 (2个)
- 实时量化因子（全市场推送）
- 历史量化因子数据

### 6️⃣ 金融指标接口 (50+个)
- 趋势指标: MACD、ADX、AROON等
- 震荡指标: KDJ、RSI、CCI、WR等
- 成交量指标: OBV、AD、ADOSC等
- 波动率指标: ATR、BOLL等
- 移动平均线: SMA、EMA、WMA等

## 💡 使用示例

### 示例1: 获取股票K线数据

```python
from xtick.scripts.api import XTickMarketApi

token = "your_token"
# 获取平安银行最近30天的日K线
result = XTickMarketApi.getKlineMarket(
    type=1,              # 沪深京A股
    code="000001",       # 股票代码
    fq="1",              # 不复权
    period="1d",         # 日线
    startDate="2025-12-01",
    endDate="2025-12-31",
    token=token
)
print(result)
```

### 示例2: 计算技术指标

```python
from xtick.scripts.api import XTickIndicatorApi

# 计算MACD指标
result = XTickIndicatorApi.macd(
    type=1, code="000001", period="1d", fq="front",
    startDate="2025-01-01", endDate="2025-12-31",
    inReal=2,  # 使用收盘价
    optInFastPeriod="12",
    optInSlowPeriod="26",
    optInSignalPeriod="9",
    token=token
)
```

### 示例3: 获取市场热点

```python
from xtick.scripts.api import XTickHotApi

# 获取昨日龙虎榜数据
result = XTickHotApi.getLonghubangHistory(
    tradeDate="2025-12-30", token=token
)

# 获取资金流向
result = XTickHotApi.getMoneyFlow(
    type=1, code="000001",
    startDate="2025-12-01", endDate="2025-12-31",
    token=token
)
```

### 示例4: WebSocket实时数据订阅（新增）

**基础示例：订阅个股Tick数据**

```python
import json
import urllib
from xtick.scripts.XTickWebSocketClient import XTickWebSocketClient
from xtick.scripts.Config import Config

# 订阅多个股票的实时Tick数据
auth_codes = ["000001.SZ", "600000.SH", "600519.SH"]

user_info = json.dumps({
    "token": Config.TOKEN,
    "authCodes": auth_codes
})

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

**其他订阅类型示例**:

```python
# 订阅整个市场的tick数据
auth_codes = ["tick.SZ.1", "tick.SH.1"]

# 订阅分钟K线数据（实时推送）
auth_codes = ["minute.SZ.1", "minute.SH.1"]

# 订阅量化因子数据（实时推送）
auth_codes = ["quant.time.1"]

# 订阅集合竞价数据
auth_codes = ["bid.1"]
```

**更多订阅类型请参考**: [WebSocket接入指南](xtick/references/websocket.md)

### 示例5: AI助手自然语言交互

当在支持Skills的AI助手中加载本技能后，可以直接用自然语言询问：

```

👤 用户: 帮我看看贵州茅台最近的股价走势

🤖 AI: [自动调用API获取数据并分析]

   贵州茅台(600519)近30日股价表现:

   - 最高价: XXX元

   - 最低价: XXX元

   - 涨跌幅: X.XX%

   - 技术分析: MACD金叉...

```

## 🔑 权限等级

XTick采用分级权限制度：

| 等级 | 名称 | 可访问接口 | 适用人群 |
|------|------|-----------|---------|
| 1 | 青铜版 | 基础行情、财务数据、技术指标 | 个人投资者 |
| 2 | 白银版 | + 盯盘数据（五档行情） | 活跃交易者 |
| 3 | 黄金版 | + 核心数据（竞价、除权） | 专业投资者 |
| 4 | 至尊版 | + 热点数据（龙虎榜、新闻） | 短线高手 |
| 5 | 量化版 | + 量化因子（全市场推送） | 量化团队 |

> 💡 提示: 在官网注册后可免费试用，升级请[联系官方](http://www.xtick.top/)

## 📁 项目结构

```
DemoXtickPythonSkill/
├── xtick/
│   ├── SKILL.md                    # Skill技能文档（AI助手使用）
│   ├── references/
│   │   ├── apidoc.md               # 详细API接口文档
│   │   └── websocket.md            # WebSocket接入指南（新增）
│   └── scripts/
│       ├── Config.py               # 配置文件（Token设置）
│       ├── XTickStockApiClient.py  # HTTP API完整调用示例
│       ├── XTickWebSocketClient.py # WebSocket客户端示例（新增）
│       ├── api/
│       │   ├── XTickMarketApi.py   # 行情数据API
│       │   ├── XTickWatchApi.py    # 盯盘数据API
│       │   ├── XTickCoreApi.py     # 核心数据API
│       │   ├── XTickHotApi.py      # 热点数据API
│       │   ├── XTickQuantApi.py    # 量化因子API
│       │   ├── XTickIndicatorApi.py # 金融指标API
│       │   └── XTickWebSocketApi.py # WebSocket API封装
│       └── util/
│           ├── XTickUtil.py        # 工具函数
│           └── DataConvert.py      # 数据转换
├── README.md                       # 项目说明文档
└── requirements.txt                # Python依赖
```

## 📖 文档说明

- **[SKILL.md](xtick/SKILL.md)**: Skill技能文档，教AI助手如何使用XTick API
- **[apidoc.md](xtick/references/apidoc.md)**: 详细的HTTP API接口文档，包含所有参数说明
- **[websocket.md](xtick/references/websocket.md)**: WebSocket实时数据订阅指南（新增）
- **[XTickStockApiClient.py](xtick/scripts/XTickStockApiClient.py)**: HTTP API完整代码示例
- **[XTickWebSocketClient.py](xtick/scripts/XTickWebSocketClient.py)**: WebSocket客户端完整示例（新增）

## ⚠️ 注意事项

1. **Token安全**: 不要将Token提交到版本控制系统，建议使用环境变量
2. **频率限制**: 遵守API调用频率限制，避免被封禁
3. **数据时效性**: 实时数据仅在交易时段（9:30-15:00）更新
4. **批量查询**: 部分接口支持批量查询，最多50个股票代码
5. **时间跨度**: 分钟数据单次请求不超过31天
6. **错误处理**: 建议添加异常处理机制

## ❓ 常见问题

**Q: 如何获取Token？**

A: 访问 [XTick官网](http://www.xtick.top/user/account) 注册账号，登录后在个人中心获取。

**Q: HTTP API和WebSocket有什么区别？**

A: 
- **HTTP API**: 适合单次查询、历史数据获取，按需请求
- **WebSocket**: 适合实时监控、持续数据流，一次连接持续接收

**Q: 什么时候使用WebSocket？**

A: 以下场景推荐使用WebSocket:
- 需要实时监控多只股票的价格变化
- 构建量化交易系统，需要持续的数据源
- 开发盯盘工具，需要五档行情实时更新
- 希望减少API调用次数，降低服务器压力

**Q: 支持哪些编程语言？**

A: 目前提供Python SDK，其他语言可直接调用HTTP API或使用WebSocket协议。

**Q: 数据更新频率是多少？**

A: 
- HTTP实时数据: 约2-3秒更新一次
- WebSocket实时数据: 毫秒级推送，几乎无延迟
- 财务数据: 盘后更新

**Q: 有调用次数限制吗？**

A: 不同权限等级有不同的调用限额，详见官网说明。WebSocket连接数也有限制。

**Q: WebSocket连接断开怎么办？**

A: 建议在`on_close`回调中实现自动重连机制，参考[WebSocket接入指南](xtick/references/websocket.md)中的示例。

**Q: 可以用于商业用途吗？**

A: 请联系官方获取商业授权。

## 🛠️ 技术支持

- **官方网站**: http://www.xtick.top/
- **在线文档**: http://www.xtick.top/apidoc
- **问题反馈**: [GitHub Issues](https://github.com/xticktop/skills/issues)
- **邮箱联系**: support@xtick.top

## 🔗 相关资源

- [Python SDK源码](https://github.com/xticktop/xtick)
- [API在线文档](http://www.xtick.top/apidoc)
- [Skills管理工具](https://github.com/skills/skills)

## 📄 许可证

本项目采用 MIT 许可证 - 详见 [LICENSE](LICENSE) 文件

> ⚠️ **免责声明**: 本项目仅供学习和研究使用，不构成投资建议。股市有风险，投资需谨慎。

## 🙏 致谢

感谢所有为XTick项目做出贡献的开发者和用户！

---

**Made with ❤️ by XTick Team**

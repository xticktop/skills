# XTick WebSocket 接入指南

## 概述

XTick平台提供了WebSocket接口，用于实时接收股票行情数据。本文档将指导您如何创建WebSocket客户端并接收实时数据。

## 环境准备

### 依赖安装

确保已安装以下Python库：

```bash
pip install websocket-client requests
```

**核心依赖说明：**
- `websocket-client`: WebSocket客户端库，用于建立和维护WebSocket连接
- `requests`: HTTP请求库，XTickUtil中使用（可选，仅在使用数据处理工具时需要）

### 项目结构

XTick WebSocket客户端依赖以下模块：

```
xtick/
├── scripts/
│   ├── Config.py          # 配置文件，包含TOKEN和服务器地址
│   ├── XTickWebSocketClient.py  # WebSocket客户端主文件
│   └── util/
│       └── XTickUtil.py   # 数据处理工具，支持gzip/zip解压缩
```

### 配置文件（Config.py）

创建或修改 `xtick/scripts/Config.py` 文件：

```python
class Config:
    SERVER_URL = "http://api.xtick.top"  # API调用服务地址
    TOKEN = "your_token_here"  # 登录XTick官网获取token
```

**注意：** 请将 `your_token_here` 替换为您从XTick官网获取的实际Token。

### 获取Token

1. 访问 [XTick官网](http://www.xtick.top/)
2. 注册账号并登录
3. 在个人中心获取您的API Token

## WebSocket客户端实现

### 基本结构

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
            continue_flag:  continuation flag，如果为0表示数据继续
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

### 连接配置

```python
# 定义要订阅的股票代码列表
auth_codes = ["000001.SZ", "600000.SH"]  # 示例：平安银行、浦发银行

# 构建用户信息JSON
user_info = json.dumps({
    "token": Config.TOKEN,  # 从配置文件获取Token
    "authCodes": auth_codes
})

# URL编码用户信息
user_encoded = urllib.parse.quote(user_info)

# 构建WebSocket端点URI
endpoint_uri = f"ws://ws.xtick.top/ws/{user_encoded}"

# 创建并启动客户端
xTickClient = XTickWebSocketClient(endpoint_uri)
xTickClient.start()
```

## 数据订阅类型

### 订阅格式说明

订阅类别格式：`period.market.type`

- **period**: 数据类别（tick, bid, quant.data, quant.time, minute, kline等）
- **market**: 市场标识（SZ-深交所, SH-上交所, BJ-北交所, HK-港交所）
- **type**: 数据类型（1-A股, 3-港股, 10-指数, 20-ETF）

### 支持的订阅类型

#### Tick数据订阅
- `000001.SZ` - 按个股代码订阅tick数据（推荐方式）
- `tick.SZ.1` - 深交所A股tick数据
- `tick.SH.1` - 上交所A股tick数据
- `tick.BJ.1` - 北交所A股tick数据
- `tick.HK.3` - 港交所港股tick数据
- `tick.SZ.10` - 深交所指数tick数据
- `tick.SH.10` - 上交所指数tick数据
- `tick.SZ.20` - 深交所ETF tick数据
- `tick.SH.20` - 上交所ETF tick数据

#### 竞价数据订阅
- `bid.1` - 沪深京A股集合竞价期间竞价数据

#### 量化因子数据订阅
- `quant.data.1` - 沪深京A股量化因子数据（推送频率：每分钟）

#### 分钟K线数据订阅（实时推送）
- `minute.SZ.1` - 深交所A股1分钟K线
- `minute.SH.1` - 上交所A股1分钟K线
- `minute.BJ.1` - 北交所A股1分钟K线
- `minute.HK.3` - 港交所港股1分钟K线
- `minute.SZ.10` - 深交所指数1分钟K线
- `minute.SH.10` - 上交所指数1分钟K线
- `minute.SZ.20` - 深交所ETF 1分钟K线
- `minute.SH.20` - 上交所ETF 1分钟K线

#### K线数据订阅（定时推送）
- `kline.1m.1` - 沪深京A股1分钟K线（每分钟推送）
- `kline.1m.10` - 沪深京指数1分钟K线（每分钟推送）
- `kline.1m.20` - 沪深京ETF 1分钟K线（每分钟推送）
- `kline.1m.3` - 港股1分钟K线（每分钟推送）

## 数据处理

### 自动解压缩功能

XTickUtil模块提供了自动解压缩功能，支持gzip和zip格式的数据：

```python
# 位于 xtick/scripts/util/XTickUtil.py
@staticmethod
def process_data(data: bytes):
    compression_format = detect_compression_format(data)
    if compression_format == "gzip":
        return process_gzip_data(data)
    elif compression_format == "zip":
        return process_zip_data(data)
    else:
        return data.decode('utf-8')
```

**工作原理：**
1. 检测数据压缩格式（通过文件头字节判断）
2. 如果是gzip格式，使用gzip解压
3. 如果是zip格式，从zip中提取JSON文件
4. 否则直接解码为UTF-8字符串

**在WebSocket中的应用：**
- 当接收到二进制数据（`OPCODE_BINARY`）时，自动调用 `XTickUtil.process_data()` 处理
- 处理后的数据为JSON格式，可直接解析使用

## 完整示例

### 基础示例：订阅个股Tick数据

```python
if __name__ == "__main__":
    # 选择要订阅的股票（推荐按个股代码订阅）
    auth_codes = ["000001.SZ", "600000.SH"]  # 平安银行、浦发银行
    
    user_info = json.dumps({
        "token": Config.TOKEN,
        "authCodes": auth_codes
    })
    
    user_encoded = urllib.parse.quote(user_info)
    endpoint_uri = f"ws://ws.xtick.top/ws/{user_encoded}"
    
    xTickClient = XTickWebSocketClient(endpoint_uri)
    xTickClient.start()
```

### 高级示例：多市场订阅

```python
if __name__ == "__main__":
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

### 其他订阅类型示例

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

## 注意事项

1. **权限限制**：
   - 新用户可能只能订阅部分市场的数据
   - 默认可以订阅北交所的tick行情数据
   - 具体权限请参考XTick官网说明或联系管理员升级

2. **网络连接**：
   - 确保网络稳定
   - 当前使用非加密连接（`ws://`）
   - WebSocket连接断开后需要重新建立连接

3. **数据量控制**：
   - 合理选择订阅的股票数量，避免过多订阅导致性能问题
   - 推荐使用个股代码订阅（如 `000001.SZ`）而非全市场订阅（如 `tick.SZ.1`）

4. **错误处理**：
   - 在生产环境中应添加更完善的错误处理和重连机制
   - 建议在 `on_error` 和 `on_close` 回调中实现自动重连逻辑

5. **Token安全**：
   - 妥善保管您的API Token，不要泄露给他人
   - 建议将Token存储在环境变量或配置文件中，不要硬编码在代码里
   - 定期更换Token以提高安全性

6. **数据处理**：
   - 接收到的数据可能是gzip或zip压缩格式
   - 使用 `XTickUtil.process_data()` 自动处理解压缩
   - 解压后的数据为JSON格式，可根据需要进行解析和处理

## 常见问题

### Q: 连接失败怎么办？
A: 检查以下几点：
- Token是否正确
- 网络连接是否正常
- 防火墙是否阻止了WebSocket连接
- 参考官方示例代码进行排查

### Q: 如何修改订阅的股票？
A: 修改`auth_codes`列表中的股票代码，然后重新启动客户端

### Q: 数据格式是什么？
A: 数据以JSON格式传输，可能经过gzip或zip压缩，XTickUtil会自动解压

### Q: 为什么我的连接立即关闭了？
A: 可能的原因：
- Token无效或已过期
- authCodes格式错误或包含无权访问的代码
- 网络连接问题
- 检查 `on_error` 回调中的错误信息

### Q: 如何实现断线重连？
A: 可以在 `on_close` 回调中添加重连逻辑：
```python
def on_close(self, ws, close_status_code, close_msg):
    print('The connection is closed!')
    # 等待3秒后重连
    import time
    time.sleep(3)
    self.start()  # 重新启动连接
```

### Q: 如果创建WebSocket客户端失败怎么办？
A: 可以参考以下官方开源项目获取完整示例代码：
- **Skill项目**: [https://github.com/xticktop/skills](https://github.com/xticktop/skills)
- **Python版项目**: [https://github.com/xticktop/DemoXtickPythonSkill](https://github.com/xticktop/DemoXtickPythonSkill)
- **Java版项目**: [https://github.com/xticktop/DemoXtickJava](https://github.com/xticktop/DemoXtickJava)
- **主项目**: [https://github.com/xticktop/xtick](https://github.com/xticktop/xtick)

## 相关资源

### 官方项目
- **Skill项目**: [https://github.com/xticktop/skills](https://github.com/xticktop/skills) - AI助手技能集合
- **Python版示例**: [https://github.com/xticktop/DemoXtickPythonSkill](https://github.com/xticktop/DemoXtickPythonSkill) - Python完整示例
- **Java版示例**: [https://github.com/xticktop/DemoXtickJava](https://github.com/xticktop/DemoXtickJava) - Java完整示例
- **主项目**: [https://github.com/xticktop/xtick](https://github.com/xticktop/xtick) - XTick核心项目

### 文档与支持
- [XTick API文档](https://ccn9lag3l54q.feishu.cn/wiki/ABenwEvDOiShYrkaLAJcFY5gnZf)
- [XTick官方网站](http://www.xtick.top/) - 注册获取Token
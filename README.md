# XTick Skill

[XTick](https://tushare.pro) 金融数据获取 Skill，包括沪深京A股、ETF基金、主流指数、港股等股票实时行情数据接口、丰富的财务报表数据以及100多个金融指标数据接口。

XTick 官方提供的SKILL工具包。

## 安装

### 1. 安装依赖

* 安装nodejs（如果需要skills管理本地包-npx命令）， https://nodejs.cn/download/

### 2. 安装 XTick Skill

可以通过下面几种个方法（任何一种都可以）：

* 将 xtick 目录复制到本地的 skills 目录

* 通过skills，安装github上的源码包

```bash
npx skills add https://github.com/xticktop/skills.git --skill xtick
```

* 通过skills，安装gitee上的源码包

```bash
npx skills add https://gitee.com/xtick/skills.git --skill xtick
```

## 使用方法

安装后并在本地智能体中加载该技能，之后可以用自然语言直接交流。

支持 claude code, openclaw, trae 等所有的通用智能体。

### 交互文本示例

**获取最新全市场行情数据**：

```
获取当天全市场行情数据
```

**获取单个股票数据**：

```
获取平安银行最近 30 天的股价数据
```

**财务分析**：

```
查看工商银行最近的财务报表，重点观察“总资产”和“总利润”
```

**指数数据**：

```
获取上证指数最近 10 天的股价数据行情数据
```


### 工具权限

- `Bash(python:*)`: 允许执行 Python 代码
- `Read`: 允许读取接口文档

## API 限制说明

[XTick 官方文档](http://www.xtick.top/apidoc)

**注意**: 本项目仅供学习和研究使用，请勿用于商业用途。使用时请遵守 XTick 的使用条款。
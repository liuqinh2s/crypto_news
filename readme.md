# 📡 币圈消息雷达

每小时自动扫描全网加密货币消息，AI 筛选交易信号。

## 功能特点

- **全覆盖信息源**：CoinDesk、The Block、CoinTelegraph 等英文媒体 + 金色财经、PANews、律动 BlockBeats 等中文媒体
- **交易所公告**：Binance、OKX、Bitget 公告 API 实时抓取
- **链上数据**：Whale Alert 大额转账、恐惧贪婪指数
- **宏观经济**：美联储/CPI/利率相关加密新闻
- **AI 智能筛选**：基于交易决策导向，给出看多/看空/中性信号
- **每小时更新**：GitHub Actions 自动执行，数据实时推送
- **暗色主题**：默认深色主题，适合交易员使用

## 快速开始

### 1. 安装依赖

```bash
pip3 install -r requirements.txt
```

### 2. 配置环境变量

```bash
cp .env.example .env
# 编辑 .env 填入 API Key
```

### 3. 运行

```bash
# 一键抓取 + AI 筛选
python3 scripts/generate_report.py

# 或分步执行
python3 scripts/fetch_news.py    # 抓取新闻
python3 scripts/ai_filter.py    # AI 筛选
python3 scripts/build.py        # 构建网站数据
```

### 4. 本地预览

```bash
python3 -m http.server 8080 -d site
# 打开 http://localhost:8080
```

## 项目结构

```
crypto-news/
├── .github/workflows/
│   └── hourly-report.yml       # GitHub Actions 每小时定时任务
├── config/
│   ├── feeds.json              # 币圈信息源配置
│   └── ai.json                 # AI 提供商配置
├── prompts/
│   ├── filter_news.md          # AI 筛选系统 prompt
│   └── filter_news_user.md     # AI 筛选用户 prompt 模板
├── scripts/
│   ├── fetch_news.py           # 新闻抓取
│   ├── ai_filter.py            # AI 筛选
│   ├── generate_report.py      # 兼容入口
│   └── build.py                # 构建网站数据
├── site/
│   ├── index.html              # 前端页面
│   ├── style.css               # 样式
│   └── app.js                  # 前端逻辑
├── reports/                    # 生成的报告
├── logs/                       # 日志
├── .env.example
├── requirements.txt
├── package.json
└── readme.md
```

## 数据流

```
RSS/交易所API/链上数据 → fetch_news.py → reports/{date}-{hour}-raw.json
                                                    ↓
                                        ai_filter.py + prompts/
                                                    ↓
                                        reports/{date}-{hour}.json
                                                    ↓
                                              build.py
                                                    ↓
                                    site/data/ (JSON + all-data.js + latest.json)
                                                    ↓
                                        GitHub Pages (site/)
```

## AI 筛选标准

### 优先级一：直接影响行情
- 交易所上币/下架公告
- 重大监管动态（SEC、ETF）
- 巨鲸异动
- 项目方重大事件

### 优先级二：中期影响
- 宏观经济数据
- 技术升级/里程碑
- 机构动向

### 优先级三：市场情绪
- 恐惧贪婪指数极值
- KOL 集体信号
- 资金费率异常

## 输出字段

每条筛选后的消息包含：

| 字段 | 说明 |
|------|------|
| title | 15字以内标题 |
| summary | 200-300字摘要 |
| signal | bullish / bearish / neutral |
| impact_coins | 受影响币种 ["BTC", "ETH", ...] |
| impact_level | critical / major / moderate |
| urgency | immediate / hours / days |
| sources | 来源链接 |

## 与 Bitget Bot 联动

- `site/data/latest.json` 提供最新一期报告的快速访问入口
- JSON 中的 `impact_coins`、`signal`、`urgency` 字段可直接被 bot 读取
- 通过 GitHub raw URL 获取 reports/ 下的 JSON 文件

## 部署

1. Fork 本仓库
2. 在 Settings → Secrets 中添加：
   - `DEEPSEEK_API_KEY`
   - `ZHIPU_API_KEY`（备选）
   - `KIMI_API_KEY`（备选）
3. 在 Settings → Pages 中启用 GitHub Pages（Source: GitHub Actions）
4. Actions 会每小时自动运行

## License

MIT

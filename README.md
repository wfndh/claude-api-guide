# Claude API & GPT API 国内使用指南

> 整理了国内使用Claude / GPT API的完整方案，包括接入教程、
> 价格对比、避坑指南，持续更新。

---

## 目录

- [为什么需要转售渠道](#为什么需要转售渠道)
- [价格对比](#价格对比)
- [快速接入](#快速接入)
- [模型选择建议](#模型选择建议)
- [常见问题](#常见问题)
- [渠道信息](#渠道信息)

---

## 为什么需要转售渠道

官方API国内使用有两个门槛：

1. **支付问题** — 需要境外信用卡，国内双币卡大概率绑不上
2. **网络问题** — 国内IP被风控概率很高，账号容易被封

转售渠道解决了这两个问题：
- 支持支付宝、微信直接付款
- 接口已做好中转，国内网络直连稳定

---

#### 价格表

> 综合成本约为官方公开价格的 **10%~20%**，按量计费，用多少扣多少

### Claude 系列

| 模型 | 输入 | 输出 | 缓存写入 | 缓存读取 | 官方参考价(输入/输出) |
|------|------|------|---------|---------|-------------------|
| claude-opus-4-7 | $0.80 | $4.00 | $1.00 | $0.08 | $5 / $25 |
| claude-opus-4-6 | $0.80 | $4.00 | $1.00 | $0.08 | $5 / $25 |
| claude-sonnet-4-6 | $0.48 | $2.40 | $0.60 | $0.05 | $3 / $15 |
| claude-haiku-4-5 | $0.16 | $0.80 | $0.20 | $0.02 | $1 / $5 |

### GPT 系列

| 模型 | 输入 | 输出 | 缓存写入 | 缓存读取 | 官方参考价(输入/输出) |
|------|------|------|---------|---------|-------------------|
| gpt-5.5 | $0.40 | $2.40 | $0.40 | $0.04 | $5 / $30 |
| gpt-5.4 | $0.20 | $1.20 | $0.25 | $0.02 | $2.50 / $15 |
| gpt-5.3-codex | $0.14 | $1.12 | $0.17 | $0.01 | $1.75 / $14 |

*以上为每百万Token计费，所有价格以实际结算为准*

### 关于"原生官转"

市面上有很多低端中转方案（残血版、混血版、kiro反代，逆向方案），
响应质量和稳定性与官方有明显差距。

本渠道为**原生官转**：
- 模型即原版服务，非精简版/非阉割版
- 上下文、输出质量与官方一致
- 满足高并发与长时调用需求
- 支持开票，适合企业用户

---

## 快速接入

### 安装依赖

```bash
pip install anthropic
```

### 基础调用

```python
import anthropic

client = anthropic.Anthropic(
    api_key="your_api_key",       # 渠道提供的Key
    base_url="your_base_url"      # 渠道提供的接口地址
)

response = client.messages.create(
    model="claude-3-5-sonnet-20241022",
    max_tokens=1024,
    messages=[
        {"role": "user", "content": "你好"}
    ]
)

print(response.content[0].text)
```

### 流式输出

```python
with client.messages.stream(
    model="claude-4-6-sonnet-20241022",
    max_tokens=1024,
    messages=[{"role": "user", "content": "写一段自我介绍"}]
) as stream:
    for text in stream.text_stream:
        print(text, end="", flush=True)
```

### OpenAI 兼容格式（很多项目直接支持）

```python
from openai import OpenAI

client = OpenAI(
    api_key="your_api_key",
    base_url="your_base_url"
)

response = client.chat.completions.create(
    model="claude-3-5-sonnet-20241022",
    messages=[{"role": "user", "content": "你好"}]
)

print(response.choices[0].message.content)
```

---

## 模型选择建议
日常写作 / 总结 / 翻译
└── Claude 4.6 Sonnet ← 首选，性价比最高
复杂代码 / 深度分析 / 长文档处理
└── Claude 4.6 Sonnet 也够用，预算充足再考虑 Opus
简单分类 / 批量处理 / 快速响应
└── Claude 4.5 Haiku ← 最便宜，适合高频调用
图片理解 / 多模态任务
└── Claude 4.6 Sonnet（支持vision）
---

## 常见问题

**Q：API Key安全吗，会不会泄露？**  
A：Key只在你本地使用，不要上传到公开的GitHub仓库即可。
建议用环境变量存储：
```python
import os
api_key = os.environ.get("CLAUDE_API_KEY")
```

**Q：调用失败怎么排查？**
AuthenticationError  → Key填错或已过期
RateLimitError       → 调用频率超限，加 time.sleep(1)
APIConnectionError   → 网络问题，检查base_url是否正确
BadRequestError      → 请求格式错误，检查messages结构
**Q：token怎么计算？**  
- 中文：约1个token = 1.5个汉字
- 英文：约1个token = 4个字符
- 估算公式：`总字数 ÷ 1.5 ÷ 1000000 × 单价`

---

## 渠道信息

本仓库示例代码使用的API来自长期稳定的转售渠道。

- 支持模型：Claude 全系列 / GPT-5.5 / GPT-5.4 /GPT-5.3
- 付款方式：支付宝 / 微信 / USDT  
- 价格：官方定价 1/5 左右
- 客服响应：工作日 9:00-23:00

**联系方式：** 电报搜索频道 `@gpt_claude_token`
              微信:smksdn

---

## 更新记录

- 2026-05 初始版本，整理常用接入方式
- 持续更新中...

---

*如果这个仓库对你有帮助，欢迎 Star ⭐*

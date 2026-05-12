# GitHub 一周热榜摘要文档
**时间范围**: 2026 年 3 月 3 日 - 3 月 10 日  
**生成日期**: 2026 年 3 月 10 日

---

## 📊 核心数据概览

| 指标 | 数值 |
|------|------|
| GitHub AI 相关仓库总数 | 430 万+ |
| 同比增长率 | 178% |
| 本周最热项目 Stars | 263k (OpenClaw) |
| 分析项目数量 | ~100 个 trending 项目 |

---

## 🔥 TOP 10 热门项目

### 1. 🏆 OpenClaw (263k ⭐)
- **仓库**: `openclaw/openclaw`
- **语言**: TypeScript
- **简介**: 2026 年增长最快的开源项目，60 天内从 0 到 250k stars
- **核心功能**: 
  - 本地运行的个人 AI 助手
  - 连接 50+ 集成 (WhatsApp, Slack, Discord, iMessage 等)
  - 自动编写新技能扩展能力
  - 数据完全本地化，不离开用户设备
- **本周动态**: 创始人加入 OpenAI，项目将转交开源基金会

### 2. Ollama (162k ⭐)
- **仓库**: `ollama/ollama`
- **语言**: Go
- **简介**: 本地 LLM 运行框架，一键部署
- **核心功能**:
  - 支持 Llama, Mistral, Gemma, DeepSeek 等模型
  - 单命令启动：`ollama run deepseek-r1`
  - macOS/Windows 桌面应用
  - 完全离线运行，无 API 费用

### 3. n8n (150k ⭐)
- **仓库**: `n8n-io/n8n`
- **简介**: 工作流自动化平台，AI 原生集成
- **核心功能**:
  - 400+ 集成，可视化无代码界面
  - LangChain 集成构建 AI 代理
  - 自托管，公平代码许可
  - 支持自定义 AI 代理自动化

### 4. Dify (130k ⭐)
- **仓库**: `langgenius/dify`
- **语言**: TypeScript
- **简介**: 生产级 AI 代理工作流开发平台
- **核心功能**:
  - 可视化工作流构建器
  - RAG 管道管理
  - 多模型支持 (OpenAI, Anthropic 等)
  - MCP (Model Context Protocol) 集成

### 5. System Prompts Collection (122k ⭐)
- **简介**: 收集主流 AI 工具系统提示词的文档仓库
- **现象**: 纯文档项目获得 122k stars，反映开发者对 AI 黑盒透明化需求

### 6. Shannon (31k ⭐)
- **简介**: 自主 AI 渗透测试工具
- **增长**: 单月增长 21,665 stars
- **功能**: 自动执行漏洞利用并收集证据

### 7. Claude Code
- **仓库**: `anthropics/claude-code`
- **简介**: Anthropic 终端 AI 编程助手
- **功能**:
  - 全代码库上下文理解
  - 多文件重构、测试生成、Git 操作
  - 自然语言驱动的复杂任务执行

### 8. DeepSeek-V3
- **仓库**: `deepseek-ai/DeepSeek-V3`
- **简介**: 开源权重模型，性能对标 GPT-4
- **技术亮点**:
  - MoE (Mixture-of-Experts) 架构
  - 128K token 上下文
  - 蒸馏推理链训练技术
  - 免费商用

### 9. Open WebUI (124k ⭐)
- **仓库**: `open-webui/open-webui`
- **下载量**: 2.82 亿+
- **简介**: 自托管 AI 平台，ChatGPT 风格界面
- **功能**:
  - 连接 Ollama、OpenAI 兼容 API
  - 内置 RAG 推理引擎
  - 语音/视频通话支持
  - 企业级 SSO、RBAC、审计日志

### 10. RAGFlow (70k ⭐)
- **仓库**: `infiniflow/ragflow`
- **简介**: 开源 RAG 引擎，AI 代理能力集成
- **功能**:
  - 文档摄入、向量索引、查询规划
  - 工具调用代理
  - 引用追踪、多步推理
  - 企业知识库、合规 AI 应用

---

## 🚀 其他值得关注的项目

| 项目 | Stars | 简介 |
|------|-------|------|
| `karpathy/autoresearch` | 18.6k | AI 代理自动运行单 GPU nanochat 训练研究 |
| `yologdev/yoyo-evolve` | 500 | 自我进化的编程代理，每天自动提交改进 |
| `duanyytop/agents-radar` | 313 | 追踪 Claude Code、Codex、Gemini CLI、OpenClaw 生态 |
| `dataelement/Clawith` | 145 | OpenClaw 团队版，多代理协作平台 |
| `bleuropa/loomkin` | 154 | Elixir 构建的多代理团队协作框架 |

---

## 📈 五大核心趋势

### 1️⃣ Chatbot 已死，代理时代来临
**关键发现**: 约 100 个 trending 项目中，几乎无传统"聊天机器人"，全部为 AI 代理

**代理架构标准模式**:
```
while goal_not_achieved:
    plan = LLM("analyze current state, decide next action")
    result = execute_tool(plan)
    observe = LLM("analyze result")
    if failure:
        adjust_plan()
```

**代表项目**: OpenClaw (100+ 预配置技能), Shannon (自动渗透测试), Claude Code

---

### 2️⃣ 本地化 AI 回归
**驱动力**: 
- API 成本过高 (全天候云代理可达 $50-100/天)
- 数据隐私考量
- 定制化需求

**代表项目**:
- Ollama: 单命令本地部署
- llama.cpp: 高质量 CPU 推理
- Open WebUI: 大规模本地界面封装
- OpenClaw: 本地优先，模型无关

---

### 3️⃣ "核心 + 技能"架构胜出
**模式**: 稳定核心引擎 + 社区贡献技能

```
[Agent Core]
  ├── Gmail skill
  ├── GitHub PR skill
  ├── Scraping skill
  └── Domain-specific skill
```

**数据**:
- OpenClaw ClawHub: 5,700+ 社区技能
- HuggingFace: 官方代理技能仓库
- MCP (Model Context Protocol): AI 工具"USB 标准"

---

### 4️⃣ 黑盒透明化需求
**现象**: 系统提示词收集仓库获 122k stars

**跨工具提示词结构趋同**:
```
Identity
Capabilities + tool rules
Constraints
Output format
Domain knowledge
Recovery rules
Planning protocol
```

**目的**: 改进规划质量、错误恢复、工具使用可靠性

---

### 5️⃣ API 成本优化战争
**关键项目**:
- `PageIndex`: 移除向量数据库 + 嵌入依赖
- `Tokenomics`: 缓存 + 预算路由 + 在线优化
- `save-llm-api-cost`: 对话压缩为事实增量
- `free-llm-api-resources`: 免费 API 资源汇总

**核心信息**: 成本架构已成为核心产品特性

---

## 🔮 未来预测

1. **MCP 服务器爆发** - 尤其是领域专用服务器
2. **AI 安全自动化崛起** - 随 AI 生成代码量增长
3. **成本优化层整合** - 统一基础设施产品出现
4. **非技术用户代理** - 下一个爆发类别

---

## 💡 关键洞察

> AI 已从"对话工具"演变为"工作同事"，现在所有人都在优化这个"雇佣成本"。

**开源 AI 不再是实验品** — 这是最重要开发者工具创新的诞生地。

---

## 📋 分类汇总

### 个人 AI 代理
- OpenClaw, Claude Code, Google Gemini CLI

### 编码代理
- Claude Code, autoresearch, yoyo-evolve

### 安全自动化
- Shannon, agents-radar

### 本地 LLM 推理
- Ollama, Open WebUI, DeepSeek-V3

### 工作流构建器
- n8n, Langflow, Dify

### RAG/搜索
- RAGFlow, PageIndex

### MCP 生态
- 各类 MCP 服务器

### 系统提示分析
- System Prompts Collection

### Token 优化
- Tokenomics, save-llm-api-cost

### 浏览器自动化
- 各类 Playwright/Selenium 增强工具

---

## 📞 资源链接

| 类别 | 链接 |
|------|------|
| GitHub Trending | https://github.com/trending |
| OpenClaw | https://github.com/openclaw/openclaw |
| Ollama | https://github.com/ollama/ollama |
| ByteByteGo 分析 | https://blog.bytebytego.com/p/top-ai-github-repositories-in-2026 |
| DEV 社区分析 | https://dev.to/ji_ai/what-100-trending-github-projects-tell-us-about-where-ai-is-actually-going-4977 |

---

*文档由 AI 生成 | 数据来源：GitHub Trending, ByteByteGo, DEV Community*

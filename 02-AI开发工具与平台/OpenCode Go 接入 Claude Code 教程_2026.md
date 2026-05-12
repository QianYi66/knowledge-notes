---
created: 2026-05-08
source: "OpenCode Go 接入 Claude Code 教程_2026.docx"
type: full-conversion
---

# OpenCode Go 接入 Claude Code 教程_2026

> 原始文件: `OpenCode Go 接入 Claude Code 教程_2026.docx` (40 KB) — 位于 `C:\Users\24835\实习积累知识集合`

---

OpenCode Go 接入 Claude Code
完整接入教程与集成方案总结 (2026)
编制日期：2026年05月08日
三大集成方案 + 详细配置步骤 + 排错指南
# 前言
OpenCode Go 是 OpenCode 平台推出的低成本订阅计划（首月$5，之后$10/月），提供可靠的开源编程模型访问，包括 GLM-5/5.1、Kimi K2.5/K2.6、MiMo-V2 系列、Qwen3 系列、MiniMax M2.5/M2.7、DeepSeek V4 Pro/Flash等。Claude Code 是 Anthropic 官方终端 AI 编码工具，原生使用 Anthropic API。本教程总结当前最新的三种 OpenCode Go 接入 Claude Code 的方法。
# 方法对比总览
# 方案一：opencode-go-cli（推荐·功能最全）
opencode-go-cli 是目前功能最完整的方案。它通过本地 Bun 代理桥接 Claude Code 的 Anthropic 协议到多个 Provider 后端，支持 OpenCode Go、OpenAI、Qwen、Z.ai 四种 Provider，并在一个流程中处理认证、模型选择、代理启动和 Claude Code 启动。
## 前置条件
- Bun 运行时环境
- Claude Code 已安装并在 PATH 中
- OpenCode Go API key（从 opencode.ai/auth 获取）
- (可选) ChatGPT Plus/Pro 账号 - OpenAI 方案用
- (可选) Chromium 浏览器 - Z.ai 登录用
## 安装步骤
## 使用方式
## 配置说明
全局配置存储在 ~/.opencode-go-cli/config.json：
## 高级功能
- WebSearch 拦截：通过本地 SearXNG 提供搜索能力
- 代理端口自动回退：首选端口被占用时自动切换
- Claude Code 子代理模型共享注入
- statusLine 安装：opencode-go --install-statusline
- 状态行调试：opencode-go --statusline-debug-on / --debug-show
- 自定义权限模式：--permission-mode 参数
- 模型列表刷新：opencode-go --list --refresh-models
## 工作原理
Stage 1: CLI 解析配置和认证 → 启动本地代理 → 按需启动 SearXNG → 启动 Claude Code
Claude Code 通过环境变量 ANTHROPIC_BASE_URL=http://localhost:PORT 连接到本地代理，代理将请求转换为 Provider 对应格式并实时流式返回。
# 方案二：oc-go-cc（Go 代理·性能最佳）
oc-go-cc 是一个 Go CLI 代理，专为实现 OpenCode Go 订阅与 Claude Code 的无缝集成。它拦截 Claude Code 的 Anthropic API 请求，转换为 OpenAI 格式，转发到 OpenCode Go 端点。Claude Code 以为在跟 Anthropic 通信——但实际请求走的是便宜的开源模型。
## 前置条件
- OpenCode Go 订阅 + API key（必须）
- Go 1.21+（仅从源码编译时需要）
## 安装步骤
## 配置示例
配置文件路径：~/.config/oc-go-cc/config.json
## 启动与配置 Claude Code
## 模型路由机制
## 高级特性
- 透明代理：Claude Code 发送 Anthropic 格式，自动转为 OpenAI 格式再转回来
- 模型路由：根据请求类型自动选择不同模型
- 降级链：主模型失败自动切换备用模型
- 熔断器：跟踪模型健康状态，跳过故障模型
- 实时 SSE 流式传输
- 工具调用转换：Anthropic tool_use ↔ OpenAI function calling
- Token 精确计数（tiktoken cl100k_base）
- 后台运行 & 开机自启: oc-go-cc autostart enable
- MiniMax 模型自动路由到 Anthropic 兼容端点
## CLI 命令速查
# 方案三：Anthropic 可兼容代理中继（最简方式）
如果你已经有兼容 Anthropic API 协议的中继站（如 CC Club、New API 等），可以直接通过配置 opencode.json 的 provider baseURL 实现接入。
## 配置方式
编辑 ~/.config/opencode/opencode.json：
## 验证方法
- 重启 OpenCode
- 执行 /models，确认能看到中继模型
- 开新会话测试对话

# 常见问题与排错
# OpenCode Go 可用模型一览表
# 选择建议
选择建议：
① 想使用多种 Provider（OpenCode Go + OpenAI + Qwen）→ 选择 opencode-go-cli
② 只使用 OpenCode Go + Claude Code 单一场景→ 选择 oc-go-cc （性能更优）
③ 已有中继站 API→ 选择方法三配置
以下是该文档的适用范围和使用建议：
- 适用于想在 Claude Code 中使用开源低成本模型的开发者
- 提供了三种不同集成方式，可根据实际情况选择
- OpenCode Go 订阅为首月$5，之后$10 / 月，可使用 12+ 个开源模型
- 所有方式都需要 OpenCode Go 的 API Key，同时也支持其他提供商的认证方式

本教程基于 2026 年 4/5 月最新信息编写，事务由 GitHub 社区开源项目提供实现支持

---


### 表格 1

| 方法  | 工具名             | 语言     | 原理                          | 适用场景                                          | 项目地址                                 |
|-----|-----------------|--------|-----------------------------|-----------------------------------------------|--------------------------------------|
| 方案一 | opencode-go-cli | Bun/JS | 本地代理转 Anthropic↔Provider 协议 | 多 Provider 统一接入(OpenCode Go/OpenAI/Qwen/Z.ai) | github.com/Lordymine/opencode-go-cli |
| 方案二 | oc-go-cc        | Go     | 透明代理转换 Anthropic↔OpenAI 格式  | 专注 OpenCode Go + Claude Code 场景               | github.com/samueltuyizere/oc-go-cc   |
| 方案三 | OpenCode 代理中继   | N/A    | 通过 Anthropic 兼容代理 baseURL   | 已有 Claude 中继站点的用户                             | open-code.ai 中文站                     |



### 表格 2

| npm install -g opencode-go-cli |
|--------------------------------|



### 表格 3

| # 初始化配置（交互式） opencode-go --setup |
|----------------------------------|



### 表格 4

| # 交互式启动 opencode-go  # 直接指定模型 opencode-go --model minimax-m2.7 opencode-go --provider opencode --model kimi-k2.5 opencode-go --provider openai --model gpt-5.2-codex opencode-go --provider qwen --model qwen3-coder-plus opencode-go --provider zai --model glm-4.7  # 仅启动代理（不启动Claude Code） opencode-go --proxy --port 8080 |
|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|



### 表格 5

| {   "provider": "opencode",   "apiKey": "sk-opencode-...",   "lastModel": "minimax-m2.7",   "proxyPort": 8080 } |
|-----------------------------------------------------------------------------------------------------------------|



### 表格 6

| # 安装（推荐） go install github.com/samueltuyizere/oc-go-cc@latest  # 初始化配置 oc-go-cc init |
|--------------------------------------------------------------------------------------|



### 表格 7

| {   "api_key": "${OC_GO_CC_API_KEY}",   "host": "127.0.0.1",   "port": 3456,   "models": {     "default": { "provider": "opencode-go", "model_id": "kimi-k2.5" },     "background": { "provider": "opencode-go", "model_id": "qwen3.5-plus" },     "think": { "provider": "opencode-go", "model_id": "glm-5.1" },     "long_context": { "provider": "opencode-go", "model_id": "minimax-m2.7" }   },   "fallbacks": {     "default": [{ "provider": "opencode-go", "model_id": "glm-5" }]   } } |
|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|



### 表格 8

| # 启动代理 oc-go-cc serve  # 后台运行 oc-go-cc serve -b  # 配置 Claude Code cd export ANTHROPIC_BASE_URL=http://127.0.0.1:3456 export ANTHROPIC_AUTH_TOKEN=unused  # 启动 Claude Code claude |
|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|



### 表格 9

| 场景   | 触发条件                       | 配置键                 | 默认模型         |
|------|----------------------------|---------------------|--------------|
| 默认   | 标准聊天                       | models.default      | kimi-k2.5    |
| 思考模式 | 含 think/plan/reason 内容     | models.think        | glm-5.1      |
| 长上下文 | Token 超过 context_threshold | models.long_context | minimax-m2.7 |
| 后台任务 | 文件读取/目录列表/grep 模式          | models.background   | qwen3.5-plus |



### 表格 10

| oc-go-cc serve                    启动代理 oc-go-cc serve -b                   后台运行 oc-go-cc serve --port 8080          自定义端口 oc-go-cc stop                       停止代理 oc-go-cc status                     检查状态 oc-go-cc init                       初始化配置 oc-go-cc validate                   验证配置 oc-go-cc models                     列出模型 oc-go-cc autostart enable/disable   开机自启 |
|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|



### 表格 11

| {   "$schema": "https://opencode.ai/config.json",   "model": "ccodezh/claude-sonnet-4-6",   "provider": {     "ccodezh": {       "npm": "@ai-sdk/anthropic",       "name": "CC中继站",       "options": {         "baseURL": "https://api.your-relay.com/v1",         "apiKey": "你的中继站API key"       },       "models": {         "claude-sonnet-4-6": { "name": "Claude Sonnet 4.6" },         "claude-opus-4-6": { "name": "Claude Opus 4.6" }       }     }   } } |
|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|



### 表格 12

| 现象                        | 原因                                | 解决方案                                                      |
|---------------------------|-----------------------------------|-----------------------------------------------------------|
| {'提示连接超时', 9}             | {9, '基础网络问题或代理端点错误'}              | {9, '检查网络连接，尝试 curl -I http://127.0.0.1:3456/health'}     |
| {9, '模型不可用'}              | {9, '提供商未配置或当前档案没有该模型'}           | {9, 'opencode-go --list 查看可用模型列表，或刷新缓存 --refresh-models'} |
| {'Claude Code 返回 401', 9} | {'API Key 无效或账户余额不足', 9}          | {9, '重新登录 opencode-go --setup，或更新 OC_GO_CC_API_KEY'}      |
| {'工具调用失败', 9}             | {'Anthropic vs OpenAI 工具命名适配', 9} | {'确保使用最新版本的 proxy，巵具调用已被假动转换', 9}                         |
| {9, '状态行显示不正确'}           | {9, 'statusLine 未正确配置'}           | {'运行 opencode-go --install-statusline 重新配置', 9}           |



### 表格 13

| 模型名称              | 类型     | 适用场景      |
|-------------------|--------|-----------|
| GLM-5.1           | 推理/思考  | 复杂推理、思考模式 |
| GLM-5             | 通用/思考  | 复杂思考、对话   |
| Kimi K2.5         | 通用     | 默认全能模型    |
| Kimi K2.6         | 通用     | K2.5 升级版  |
| MiMo-V2-Pro       | 通用/高性能 | 高性能推理     |
| MiMo-V2.5         | 通用     | 均衡选择      |
| Qwen3.6 Plus      | 通用     | 通用任务      |
| Qwen3.5 Plus      | 通用     | 快速轻量      |
| MiniMax M2.7      | 长上下文   | 长文本处理     |
| MiniMax M2.5      | 长上下文   | 上下文窗口扩展   |
| DeepSeek V4 Pro   | 推理/编程  | 高性能推理编程   |
| DeepSeek V4 Flash | 快速     | 轻量快速响应    |


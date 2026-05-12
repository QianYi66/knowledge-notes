---
created: 2026-05-08
source: "ocmonitor 高效命令速查表.docx"
type: full-conversion
---

# ocmonitor 高效命令速查表

> 原始文件: `ocmonitor 高效命令速查表.docx` (16 KB) — 位于 `C:\Users\24835\实习积累知识集合`

---

**✅ ocmonitor 高效命令速查表（知识库版）**
以下是我把我们对话中所有**有效、高效、实用**的命令整合总结的版本（基于最新版 README + 实际使用效果）。
直接复制保存到你的笔记/Notion/Obsidian 即可，按优先级排序，最常用的放最前面。
### 1. 核心总览命令（每天必用）
```bash
ocmonitor sessions                # 默认分组显示（主代理 + 子代理 workflow）
ocmonitor sessions --no-group     # 平铺显示（接近你第一张老图的表格样式）
ocmonitor sessions --no-group --theme dark   # 深色模式更清晰
```
### 2. 实时监控仪表盘（最推荐！）
```bash
ocmonitor live                    # 实时刷新（每5秒），显示当前成本、tokens、输出速度
ocmonitor live --interval 10      # 改成10秒刷新
ocmonitor live --theme dark       # 深色模式
ocmonitor live --pick             # 交互式选择具体 session
```
### 3. 模型缓存命中率核心命令（你已经会算了）
```bash
ocmonitor model glm-4.7           # 查看具体模型（支持模糊匹配）
ocmonitor model kimi-k2.5-free
ocmonitor model minimax-m2.5-free
ocmonitor model glm-4.7 -f json   # 输出JSON（方便脚本批量算缓存命中率）
```
**缓存命中率公式**（你已经自己算好了，留在这里做备忘）：
```
命中率 = (Cache Read ÷ (Input Tokens + Cache Read)) × 100%
```
### 4. 其他高效统计命令
```bash
ocmonitor agents                  # 列出所有检测到的代理（build、sisyphus、librarian 等）
ocmonitor projects                # 按项目统计成本和 tokens（超实用）
ocmonitor daily --breakdown       # 每日报告 + 每个模型明细
ocmonitor weekly --breakdown      # 每周报告
ocmonitor monthly                 # 月度报告
```
### 5. 进阶/导出命令（偶尔用）
```bash
ocmonitor config show             # 查看当前配置
ocmonitor export sessions --format csv   # 导出成 CSV（方便 Excel 分析）
ocmonitor export sessions --format json
```
### 使用小技巧（知识库必记）
- **路径自动识别**：大多数命令不用加路径（会 auto-detect OpenCode 的 SQLite）。
- **模糊匹配**：`ocmonitor model glm`、`ocmonitor model kimi` 都能自动匹配。
- **主题切换**：随时加 `--theme dark` 更舒服。
- **一键重装最新版**（如果想更新）：
```bash
uv tool uninstall ocmonitor
uv tool install git+https://github.com/Shlomob/ocmonitor-share.git
```
把上面整个表格复制保存就行！以后直接 `Ctrl+F` 搜索就能用。
如果你以后想再加「按模型批量算缓存命中率的 Python 一行脚本」或者「导出到 Excel 的自动化命令」，随时告诉我，我再给你补充进知识库～
现在你的 ocmonitor 工具箱已经完整了，省钱监控一把抓！🚀
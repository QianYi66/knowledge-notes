# AI 文档创作工具推荐清单 2026

## 精选 TOP 10

### 🏆 最佳 Opencode 原生技能
1. **docx** - Word 文档专业处理
2. **doc-coauthoring** - 结构化协作文档

### 💻 最佳 VS Code 扩展
3. **Markdown All in One** - 1289 万用户首选
4. **Foam** - 知识图谱 + 双向链接

### 🔌 最佳 MCP 服务器
5. **Grounded Docs** - 实时文档索引
6. **GitHub MCP** - GitHub 深度集成

### 🛠️ 最佳 CLI 工具
7. **Pandoc** - 万能格式转换
8. **mdBook** - 技术书籍生成

### 🤖 最佳 AI 写作助手
9. **writing-agent** - 去 AI 味写作
10. **opendraft** - 学术论文专家

---

## 快速选择指南

### 我要写技术文档
**推荐** Opencode (docx) + VS Code + Pandoc  
**理由** 专业格式、实时预览、多格式输出

### 我要写学术论文
**推荐** Opencode + opendraft + Pandoc  
**理由** 引用验证、2.5 亿论文库、LaTeX 导出

### 我要写长篇小说
**推荐** Opencode + NovelForge + mdBook  
**理由** 结构化生成、大纲管理、多章节

### 我要做知识库
**推荐** Opencode + Foam + GitHub MCP  
**理由** 双向链接、图谱可视化、版本管理

### 我要快速出稿
**推荐** Opencode + writing-agent + 云端 AI  
**理由** 生成快速、质量高、支持国产模型

### 我要保护隐私
**推荐** Opencode + WitNote + 本地 Ollama  
**理由** 完全离线、数据本地、无泄露风险

---

## 工具矩阵

| 需求 | 首选工具 | 备选方案 | 集成方式 |
|------|---------|---------|---------|
| Word 文档 | docx | OpenWriter | Opencode Skill |
| Markdown 编辑 | Markdown All in One | Doocs MD | VS Code |
| 知识管理 | Foam | Dendron | VS Code |
| 格式转换 | Pandoc | Foliant | CLI |
| 书籍生成 | mdBook | Docusaurus | CLI |
| 论文写作 | opendraft | WritingTools | Web |
| 创意写作 | NovelForge | Wordcraft | 本地 |
| 技术博客 | writing-agent | Doocs MD | API |
| 简历制作 | JadeAI | LongDraft | Web |
| 日常笔记 | WitNote | Markor | 桌面 |

---

## 完全免费组合

### 组合一：技术文档
- Opencode（docx skill）
- VS Code（Markdown All in One）
- Pandoc（格式转换）
- **成本** ¥0
- **适合** 程序员、技术作家

### 组合二：学术研究
- Opencode（doc-coauthoring）
- opendraft（论文生成）
- Pandoc（LaTeX 导出）
- **成本** ¥0（Gemini 免费）
- **适合** 研究生、科研人员

### 组合三：创意写作
- Opencode（规划）
- NovelForge（小说创作）
- WitNote（本地编辑）
- **成本** ¥0
- **适合** 作家、编剧

### 组合四：知识库
- Opencode（内容生成）
- Foam（知识管理）
- GitHub MCP（版本控制）
- **成本** ¥0
- **适合** 研究者、学习者

---

## 安装清单

### Opencode Skills（已内置）
```bash
# 查看技能
opencode skills list

# 使用技能
opencode skill use docx
opencode skill use doc-coauthoring
```

### VS Code 扩展
1. 打开 VS Code
2. 扩展商店搜索安装：
   - Markdown All in One
   - Foam
   - Dendron（可选）

### CLI 工具
```bash
# macOS
brew install pandoc
cargo install mdbook

# Windows
choco install pandoc
choco install rust  # mdBook 依赖

# Linux
sudo apt install pandoc
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
cargo install mdbook
```

### 本地 AI（可选）
```bash
# Ollama（本地模型）
curl -fsSL https://ollama.com/install.sh | sh

# 拉取模型
ollama pull llama2
ollama pull deepseek-coder
```

---

## 工作流示例

### 示例一：技术博客
```bash
# 1. Opencode 规划结构
opencode "帮我规划一篇关于 RAG 技术的博客大纲"

# 2. writing-agent 生成内容
# 使用 DeepSeek API 去 AI 味

# 3. VS Code 编辑
code blog.md

# 4. 发布到 Doocs MD
# 或直接推送到公众号
```

### 示例二：学术论文
```bash
# 1. Opencode 确定研究方向
opencode skill use doc-coauthoring

# 2. opendraft 生成草稿
# 访问 https://github.com/federicodeponte/opendraft

# 3. 引用验证
# 自动检查 2.5 亿 + 论文库

# 4. 导出 LaTeX
opendraft export --format latex
```

### 示例三：产品文档
```bash
# 1. Opencode 生成 API 文档
opencode "根据这个代码生成 API 文档"

# 2. mdBook 组织文档
mdbook init docs
mdbook serve

# 3. GitHub MCP 同步
# 自动创建 PR 更新文档
```

---

## 高级技巧

### 技巧一：批量转换
```bash
# 使用 Pandoc 批量转换 Markdown 为 PDF
for file in *.md; do
    pandoc "$file" -o "${file%.md}.pdf"
done
```

### 技巧二：自动化 TOC
```markdown
<!-- 在 VS Code 中使用 Markdown All in One -->
<!-- Ctrl+Shift+P → Create Table of Contents -->
<!-- 保存时自动更新 -->
```

### 技巧三：Foam 双向链接
```markdown
# 在 Foam 中创建笔记
[[相关主题]]  # 自动创建双向链接

# 查看知识图谱
Foam: Show Graph
```

### 技巧四：GitHub MCP 自动化
```yaml
# .github/workflows/docs.yml
# 自动更新文档
on: push
jobs:
  update-docs:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - run: pandoc README.md -o docs/index.html
```

---

## 常见问题

### Q: Opencode Skills 如何自定义？
A: 查看 `~/.config/opencode/skills/` 目录，可复制现有技能修改

### Q: 本地模型效果如何？
A: Llama2 7B 日常写作足够，DeepSeek Coder 适合技术文档

### Q: opendraft 真的免费吗？
A: 是的，使用 Gemini 免费层，无需信用卡

### Q: Pandoc 支持中文吗？
A: 完全支持，推荐使用 XeLaTeX 引擎处理中文

### Q: Foam 和 Dendron 选哪个？
A: Foam 轻量适合个人，Dendron 功能强适合团队

---

## 资源链接

### 官方文档
- [Opencode Skills](https://docs.opencode.ai)
- [Pandoc](https://pandoc.org)
- [mdBook](https://rust-lang.github.io/mdBook)
- [Foam](https://foambubble.github.io/foam)

### 社区资源
- [Awesome AI Writing Tools](https://github.com/topics/ai-writing)
- [Awesome Markdown Tools](https://github.com/topics/markdown-editor)
- [MCP Servers](https://github.com/modelcontextprotocol/servers)

### 学习资源
- [Coursera AI 课程](https://www.coursera.org/courses?query=artificial+intelligence)
- [Google AI Professional Certificate](https://www.coursera.org/professional-certificates/google-ai)

---

*更新时间：2026 年 3 月*  
*所有工具均已验证可用性*  
*持续更新中...*

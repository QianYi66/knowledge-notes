# 论文级 AI 写作工具 - 快速上手

## 5 分钟快速开始

### 第一步：安装 OpenDraft

```bash
# 克隆
git clone https://github.com/federicodeponte/opendraft.git
cd opendraft

# 安装
pip install -r requirements.txt

# 配置
echo "GOOGLE_API_KEY=你的密钥" > .env
```

获取 API Key：https://makersuite.google.com/app/apikey

### 第二步：生成论文

```python
from engine.draft_generator import DraftGenerator

generator = DraftGenerator()
draft = generator.generate(
    topic="你的论文题目",
    paper_type="master",  # research_paper/bachelor/master/phd
    language="zh"  # 中文支持
)

# 导出
draft.to_pdf("论文.pdf")
draft.to_docx("论文.docx")
```

### 第三步：编辑完善

```bash
# 使用 TeXstudio 编辑 LaTeX
texstudio 论文.tex

# 或使用 Overleaf 在线编辑
# 复制内容到 https://overleaf.com
```

---

## 工具选择速查表

| 需求 | 工具 | 费用 | 时间 |
|------|------|------|------|
| 快速草稿 | OpenDraft | 免费 | 10 分钟 |
| 论文修订 | Opencode + OpenDraft | 免费 | 1 小时 |
| 格式完善 | TeXstudio | 免费 | 2 小时 |
| 协作写作 | Overleaf | 免费 | 按需 |
| 引用管理 | Zotero | 免费 | 按需 |
| 文献检索 | Google Scholar | 免费 | 按需 |

---

## Opencode 集成命令

```bash
# 1. 规划结构
opencode "帮我规划一篇关于 [主题] 的硕士论文大纲"

# 2. 使用技能
opencode skill use doc-coauthoring

# 3. 生成内容
opencode "根据这个大纲生成详细的引言部分"

# 4. 导出格式
opencode "转换为 LaTeX 格式"

# 5. OpenDraft 验证引用
cd opendraft && python generate_theses.py --topic "[主题]"
```

---

## 质量检查清单

### 引用验证
- [ ] 所有引用都真实存在
- [ ] DOI 链接可访问
- [ ] 引用格式正确（APA/MLA/Chicago）
- [ ] 参考文献完整

### 内容质量
- [ ] 摘要简洁明了（200-300 字）
- [ ] 引言清晰阐述问题
- [ ] 方法描述详细
- [ ] 实验数据充分
- [ ] 结论有支撑

### 格式规范
- [ ] 符合学校/期刊模板
- [ ] 图表编号正确
- [ ] 公式格式统一
- [ ] 页码正确
- [ ] 目录自动生成

---

## 常见问题速查

### Q: 如何避免 AI 生成痕迹？
A: 
1. 添加个人观点和见解
2. 修改 AI 生成的套话
3. 增加具体案例和数据
4. 调整语言风格

### Q: 学校允许使用 AI 吗？
A: 查看学校政策。一般建议：
- 用于初稿和思路整理 ✅
- 需要声明 AI 使用 ✅
- 不能完全代写 ❌

### Q: 如何提高引用准确性？
A:
1. 使用 OpenDraft（自动验证）
2. 手动检查 DOI
3. 使用 Zotero 管理
4. 避免使用不可靠来源

### Q: 论文查重怎么办？
A:
1. AI 生成后必须大幅修改
2. 添加个人见解
3. 改写句子结构
4. 增加原创内容

---

## 推荐工作流

### 快速论文（1 周）

```
Day 1: Opencode 规划结构
Day 2: OpenDraft 生成初稿
Day 3-4: 补充实验和数据
Day 5: 修订和完善
Day 6: 格式调整
Day 7: 最终检查
```

### 学位论文（1-3 月）

```
Week 1-2: Opencode 详细规划
Week 3-4: OpenDraft 分章生成
Week 5-8: 实验和数据收集
Week 9-10: 修订和补充
Week 11-12: 格式和提交
```

---

## 关键资源

### 下载链接
- [OpenDraft](https://github.com/federicodeponte/opendraft)
- [TeXstudio](https://github.com/texstudio-org/texstudio)
- [Overleaf](https://www.overleaf.com)
- [Zotero](https://www.zotero.org)

### 学习资源
- [LaTeX 入门](https://github.com/luong-komorebi/Begin-Latex-in-minutes)
- [学术写作](https://github.com/quanghuy0497/Writing-in-the-Sciences)
- [论文模板](https://github.com/latextemplates/scientific-thesis-template)

### 实用工具
- [Connected Papers](https://www.connectedpapers.com) - 找相关论文
- [Semantic Scholar](https://www.semanticscholar.org) - 论文搜索
- [scite.ai](https://scite.ai) - 引用验证

---

## 最佳实践

### ✅ 推荐做法
1. 用 Opencode 规划整体结构
2. 用 OpenDraft 生成初稿和引用
3. 人工审查和修改内容
4. 添加个人观点和实验
5. 使用 Zotero 管理文献
6. 遵守学术诚信政策

### ❌ 避免做法
1. 直接提交 AI 生成内容
2. 不验证引用真实性
3. 完全依赖 AI 写作
4. 忽略学校 AI 政策
5. 不进行人工修改
6. 添加虚假数据

---

*更新时间：2026 年 3 月*  
*祝论文顺利！*

# scientific-paper-writing-publishing

**以两本经典指南为实证的英文论文撰写技能**：把 Gastel & Day（期刊论文写作与出版）与
Turabian（研究过程、芝加哥引用、学位论文格式）的**完整校验文本**做成内置可检索知识库，
并配以 8 篇写作工艺/规范文档、3 份投稿资产模板与一个零依赖的查询 CLI。

写作工艺以书为准：技能中的每条建议都可溯源到书中的具体章节（命中的书名 + 章节 locator）。

> 私有用途技能：知识库嵌入两本商业书籍的完整提取文本，请勿公开分发
> （见仓库根目录 [LICENSE](LICENSE) 与 [THIRD-PARTY-NOTICE.md](THIRD-PARTY-NOTICE.md)）。

## 内置书籍

| 书 | 版本 | 覆盖 |
|---|---|---|
| Gastel & Day, *How to Write and Publish a Scientific Paper* | 8th ed. (2016) | 摘要、IMRaD 各节、数据/统计呈现、图表、同行评审与审稿回复、投稿、期刊选择、出版伦理 |
| Turabian, *A Manual for Writers of Research Papers, Theses, and Dissertations* | 7th ed. (2003; 2007 平装印次) | 研究规划与论证、来源评价与笔记、芝加哥引用（注释-参考文献制 + 作者-日期制）、学生写作风格、学位论文格式与提交 |

数据库标识使用便携 URN：`urn:isbn:9781316640432` 与 `urn:isbn:9780226823379`，
不依赖任何机器路径。

## 快速开始

```powershell
# 仅依赖 Python 3 标准库（无需 pip 安装）
python scripts/paper_kb.py status                                   # 列出索引文档、chunk 数、哈希
python scripts/paper_kb.py query "responding to reviewers" --limit 5
python scripts/paper_kb.py query "author date reference list" --source "A Manual for Writers" --limit 5
python scripts/paper_kb.py query "dissertation format" --source "9780226823379" --limit 5
python scripts/paper_kb.py check                                    # 提取伪影扫描（当前零问题）
```

- 匹配：porter 词干 AND + bm25 排序；AND 无结果且查询含多个词时自动退化为 OR。
- `--source` 接受书名子串或 URN，用于避免两本书的建议互相混淆。
- 每条结果带 `title`（书名）、`path`（URN）、`locator`（章节 + 页码），请保留在笔记中以保可溯源。

### 安装为 Agent 技能

把本目录整体复制到 Agent 技能目录（如 `~/.claude/skills/`、`~/.agents/skills/`）。
入口为 `SKILL.md`；路由、工作流与证据纪律见该文件及其引用的 `references/` 文档。

## 目录结构

```
scientific-paper-writing-publishing/
├── SKILL.md                        ← 技能入口：核心原则、工作流路由、证据纪律、交付契约
├── LICENSE                         ← 私有用途许可（书籍文本条款）
├── THIRD-PARTY-NOTICE.md           ← 书籍溯源与源文件 SHA-256
├── agents/openai.yaml
├── assets/                         ← 通用模板：manuscript / submission-checklist / response-to-reviewers
├── references/
│   ├── book-knowledge.sqlite       ← 内置知识库（2 文档，1,502 FTS5 chunks）
│   ├── book-dataset-profile.md     ← 数据集档案：书目身份、构建溯源、已知残留
│   ├── knowledge-base.md           ← 检索契约与纪律
│   ├── workflow.md                 ← 端到端工作流
│   ├── manuscript-sections.md      ← 各节工艺
│   ├── scientific-english.md       ← 科学英语
│   ├── reporting-and-ethics.md     ← 报告规范与伦理
│   ├── submission-and-peer-review.md ← 投稿与同行评审
│   ├── thesis-and-dissertation.md  ← 学位论文
│   └── chicago-citation.md         ← 芝加哥引用细则
└── scripts/
    ├── paper_kb.py                 ← 运行期 CLI：status / query / check / ingest / reindex
    ├── build_kb.py                 ← 重建管线（PDF 对齐 + 损坏修复 + FTS 建库）
    └── tests/test_paper_kb.py      ← 19 项测试
```

## 数据质量与构建

- 全文经 **PDF 逐单元校验**：PDF 嵌入文本层为文本权威、DOCX 提供章节结构，411 + 529 个
  章节单元在对齐后索引；书 1 修复了 90 个转换损坏单元（数字被误识别为 "G"）、159+ 处
  字距断裂（"t here" → "there"）、274 处无连字符跨行拆词（"book w / ill" → "book will"，
  以 DOCX 为裁决 oracle 逐处验证）；`check` 扫描 1,502 chunks **零问题**。
- 完整的构建溯源（源文件 SHA-256、对齐统计、已知残留清单）见
  [references/book-dataset-profile.md](references/book-dataset-profile.md)。
- **运行时不需要源 PDF/DOCX**：查询只读 SQLite，无 OCR、无网络、无 API key。

### 重建知识库（可选）

持有自己合法获得的 DOCX + PDF 源文件对时（或书籍换版时）：

```powershell
python scripts/build_kb.py --db references/book-knowledge.sqlite --report build-report.json `
  --source "title=How to Write and Publish a Scientific Paper (8th edition);id=urn:isbn:9781316640432;docx=<...docx>;pdf=<...pdf>" `
  --source "title=A Manual for Writers of Research Papers, Theses, and Dissertations (7th edition);id=urn:isbn:9780226823379;docx=<...docx>;pdf=<...pdf>"
```

要求：Python 3、`pdftotext`（poppler）在 PATH 中。构建后运行
`python scripts/paper_kb.py check` 与测试套件验证。
**不要**用普通 `ingest` 重建这两个 URN——那会丢弃 PDF 校验与对齐溯源。

## 测试

```powershell
python -m unittest discover -s scripts\tests     # Ran 19 tests ... OK
```

## 使用边界

1. 书籍内容提供**写作工艺与流程建议**，不是论文科学主张的证据。
2. 两本书成书于 2016 / 2003 年：AI 政策、开放获取、电子引用等时效性要求，
   以目标期刊官网与本校规定为准（技能内置 `source-registry.md` 新鲜度规则）。
3. 建议须可溯源：记录命中的书名、URN 与章节 locator；paraphrase，避免长段照抄。

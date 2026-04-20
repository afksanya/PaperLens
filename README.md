# PaperLens — Academic Paper Reading Skill for Claude

**中文** | [日本語](#日本語) | [English](#english)

---

## 中文

一个让 Claude 阅读学术论文 PDF、生成结构化阅读笔记的 Claude Code Skill。笔记格式为 Obsidian 兼容的 Markdown，支持单篇精读和多篇综述两种模式。

### 功能特性

**单篇笔记模式**
- 自动读取 PDF（支持长论文分批读取）
- 生成标准化阅读笔记，包含：
  - YAML frontmatter（兼容 Obsidian Dataview 插件）
  - 一句话总结
  - 背景与动机
  - 核心贡献
  - 方法（支持 LaTeX 公式）
  - 实验结果（含具体数字）
  - 局限性
  - 延伸阅读（含 `[[wiki链接]]`）

**综述模式**
- 读取文件夹中的多篇论文
- 生成带对比表格的综述笔记
- 方法对比 + 实验结果对比
- 研究趋势与开放问题
- APA 格式参考文献

**输出风格**
- 启动时询问笔记语言：中文 / 日本語 / English，三选一
- 模型名、数据集名、指标名等技术术语始终保留英文
- 内容具体：有数字、有数据集名，不泛泛而谈
- 默认只在聊天中展示，需要时才保存为 `.md` 文件

### 安装

此 skill 适用于 [Claude Code](https://claude.ai/code) 及 Obsidian 的 [Claudian 插件](https://github.com/YishenTu/claudian)。

**第一步：安装 Skill**

从 [Releases](../../releases) 页面下载最新的 `PaperLens.skill` 文件，然后在 Claude Code 中运行：

```
/skills install PaperLens.skill
```

或手动复制：

```bash
cp -r PaperLens/ ~/Library/Application\ Support/Claude/skills/
```

**第二步：安装斜杠指令（可选但推荐）**

```bash
cp commands/PaperLens.md ~/.claude/commands/
```

安装后即可直接用 `/paperlens` 触发，无需自然语言描述。

### 使用方法

**方式一：斜杠指令（推荐）**

```bash
/paperlens /path/to/paper.pdf                        # 单篇精读
/paperlens /path/to/paper1.pdf /path/to/paper2.pdf   # 多篇综述
/paperlens /path/to/folder/                          # 整个文件夹综述
/paperlens                                           # 无参数，提示输入路径
```

**方式二：自然语言触发**

在 Claude Code 或 Obsidian Claudian 插件中直接用自然语言触发：

```
帮我读一下这篇论文，生成阅读笔记：/path/to/paper.pdf
```

```
对这几篇论文做个综述（需要 APA 参考文献）：
- /path/to/paper1.pdf
- /path/to/paper2.pdf
- /path/to/paper3.pdf
```

```
帮我读 /path/to/paper.pdf，生成笔记后保存到 Obsidian
```

**搭配 Zotero 使用**

如果你用 Zotero 管理文献，PDF 通常存储在 `~/Zotero/storage/` 下，可以直接引用：

```
帮我读这篇论文：~/Zotero/storage/XXXXXXXX/Author - Year - Title.pdf
```

### 笔记示例

<details>
<summary>单篇笔记示例（点击展开）</summary>

```markdown
---
title: "Attention Is All You Need"
authors: ["Vaswani, Ashish", "Shazeer, Noam"]
year: 2017
venue: "NeurIPS 2017"
tags: [paperlens, transformer, NLP, attention]
date: 2024-01-15
type: paper-note
arxiv: "1706.03762"
---

# Attention Is All You Need

**作者**: Vaswani et al. | **发表**: NeurIPS 2017 | **arXiv**: 1706.03762

## 一句话总结
提出完全基于 self-attention 的 Transformer 架构，抛弃 RNN/CNN，在机器翻译任务上以更少计算量达到 SOTA。

## 背景与动机 (Motivation)
RNN 序列建模存在长程依赖问题和无法并行化的缺陷；CNN 虽可并行但捕捉远距离依赖需要多层叠加。

## 核心贡献 (Core Contributions)
- **架构**: 提出 Transformer，encoder-decoder 结构完全基于 attention mechanism
- **机制**: 引入 multi-head attention 和 positional encoding

## 方法 (Methodology)
...
```

</details>

### Obsidian 集成

生成的笔记完全兼容 Obsidian：

- **YAML frontmatter**：支持 Dataview 插件查询（按年份、标签、类型筛选论文）
- **`[[wiki链接]]`**：综述笔记和延伸阅读中使用，可在 Obsidian 图谱中看到论文关联
- **Markdown 格式**：标准 CommonMark，LaTeX 公式通过 Obsidian 的 MathJax 渲染

推荐搭配 [Claudian](https://github.com/YishenTu/claudian) 插件在 Obsidian 内直接使用。

### 贡献

欢迎提交 Issue 和 Pull Request！常见改进方向：
- 支持更多引用格式（Chicago、Vancouver 等）
- 针对特定领域（医学、法律、经济）的定制笔记模板
- 与其他笔记工具（Logseq、Notion）的集成优化

---

## 日本語

**PaperLens** は、学術論文の PDF を読み込み、Obsidian 対応の Markdown 形式で構造化された読書メモを生成する Claude Code Skill です。一本の論文の精読メモと、複数論文のサーベイメモの両方に対応しています。

### 機能

**単論文メモモード**
- PDF をネイティブ読み込み（長い論文はバッチ処理）
- 以下の構成で標準化されたメモを生成：
  - YAML frontmatter（Obsidian Dataview 対応）
  - 一言まとめ
  - 背景と動機
  - 主な貢献
  - 手法（LaTeX 数式対応）
  - 実験結果（具体的な数値付き）
  - 限界
  - 関連文献（`[[wiki リンク]]` 付き）

**サーベイモード**
- フォルダ内の複数論文を一括読み込み
- 比較表付きサーベイメモを生成
- 手法比較・実験結果比較
- 研究動向とオープン課題
- APA 形式の参考文献

**出力スタイル**
- 起動時に中文 / 日本語 / English の 3 言語から選択可能
- 内容は具体的：数値・データセット名を明記、曖昧な表現を使わない
- デフォルトはチャット内表示のみ、必要なときだけ `.md` ファイルに保存

### インストール

[Claude Code](https://claude.ai/code) および Obsidian の [Claudian プラグイン](https://github.com/YishenTu/claudian) で動作します。

**ステップ 1：Skill のインストール**

[Releases](../../releases) から最新の `PaperLens.skill` をダウンロードし、Claude Code で実行：

```
/skills install PaperLens.skill
```

または手動でコピー：

```bash
cp -r PaperLens/ ~/Library/Application\ Support/Claude/skills/
```

**ステップ 2：スラッシュコマンドのインストール（任意・推奨）**

```bash
cp commands/PaperLens.md ~/.claude/commands/
```

インストール後は `/paperlens` で直接呼び出せます。

### 使い方

**方法 1：スラッシュコマンド（推奨）**

```bash
/paperlens /path/to/paper.pdf                        # 単論文メモ
/paperlens /path/to/paper1.pdf /path/to/paper2.pdf   # 複数論文サーベイ
/paperlens /path/to/folder/                          # フォルダ一括サーベイ
/paperlens                                           # 引数なし → パスを入力
```

**方法 2：自然言語での呼び出し**

Claude Code または Obsidian の Claudian プラグインで自然言語でも起動できます：

```
この論文を読んでメモを作成してください：/path/to/paper.pdf
```

```
これらの論文のサーベイメモを書いてください（APA 参考文献付き）：
- /path/to/paper1.pdf
- /path/to/paper2.pdf
- /path/to/paper3.pdf
```

```
/path/to/paper.pdf を読んで、メモを Obsidian vault に保存してください
```

**Zotero との連携**

Zotero で文献管理をしている場合、PDF は通常 `~/Zotero/storage/` 以下に保存されています：

```
この論文を読んでください：~/Zotero/storage/XXXXXXXX/Author - Year - Title.pdf
```

### Obsidian 連携

生成されたメモは Obsidian に完全対応：

- **YAML frontmatter**：Dataview プラグインでクエリ可能（年・タグ・種別でフィルタリング）
- **`[[wiki リンク]]`**：サーベイメモや関連文献で使用 — Obsidian グラフビューで論文間の関係を可視化
- **標準 Markdown**：LaTeX 数式は Obsidian の MathJax でレンダリング

[Claudian](https://github.com/YishenTu/claudian) プラグインと組み合わせて Obsidian 内で直接使うのがおすすめです。

### コントリビューション

Issue や Pull Request を歓迎します！改善のアイデア例：
- 他の引用形式のサポート（Chicago、Vancouver など）
- 分野別ノートテンプレート（医学・法律・経済など）
- 他のノートツール（Logseq、Notion）への対応

---

## English

**PaperLens** is a Claude Code Skill that reads academic paper PDFs and generates structured reading notes in Obsidian-compatible Markdown. Supports both single-paper notes and multi-paper survey notes.

### Features

**Single Paper Mode**
- Reads PDFs natively (batched reading for long papers)
- Generates standardized notes with:
  - YAML frontmatter (Obsidian Dataview compatible)
  - One-sentence summary
  - Background & motivation
  - Core contributions
  - Methodology (with LaTeX formulas)
  - Experimental results (with specific numbers)
  - Limitations
  - Further reading (with `[[wiki links]]`)

**Survey Mode**
- Reads multiple papers from a folder
- Generates a survey note with comparison tables
- Methodology comparison + results comparison
- Research trends and open problems
- APA-formatted references

**Output Style**
- Asks for preferred language at startup: 中文 / 日本語 / English
- Technical terms (model names, dataset names, metrics) always kept in English
- Specific: numbers, dataset names, no vague statements
- Shows in chat by default; saves to `.md` file only when asked

### Installation

PaperLens works with [Claude Code](https://claude.ai/code) and the Obsidian [Claudian plugin](https://github.com/YishenTu/claudian).

**Step 1: Install the Skill**

Download the latest `PaperLens.skill` from [Releases](../../releases), then in Claude Code run:

```
/skills install PaperLens.skill
```

Or manually:

```bash
cp -r PaperLens/ ~/Library/Application\ Support/Claude/skills/
```

**Step 2: Install the Slash Command (optional but recommended)**

```bash
cp commands/PaperLens.md ~/.claude/commands/
```

This enables the `/paperlens` command for explicit invocation.

### Usage

**Option 1: Slash command (recommended)**

```bash
/paperlens /path/to/paper.pdf                        # single paper
/paperlens /path/to/paper1.pdf /path/to/paper2.pdf   # survey
/paperlens /path/to/folder/                          # survey from folder
/paperlens                                           # no args → prompts for path
```

**Option 2: Natural language**

You can also trigger PaperLens by describing what you want:

```
Read this paper and generate notes: /path/to/paper.pdf
```

```
Write a survey note for these papers (with APA references):
- /path/to/paper1.pdf
- /path/to/paper2.pdf
- /path/to/paper3.pdf
```

```
Read /path/to/paper.pdf, generate notes and save to my Obsidian vault
```

**Using with Zotero**

If you manage papers with Zotero, PDFs are typically stored under `~/Zotero/storage/`:

```
Read this paper: ~/Zotero/storage/XXXXXXXX/Author - Year - Title.pdf
```

### Obsidian Integration

Generated notes are fully Obsidian-compatible:

- **YAML frontmatter**: Supports Dataview queries (filter papers by year, tag, type)
- **`[[wiki links]]`**: Used in survey notes and further reading — visible in Obsidian graph view
- **Standard Markdown**: LaTeX formulas rendered via Obsidian's MathJax

Pairs well with the [Claudian](https://github.com/YishenTu/claudian) plugin for in-Obsidian usage.

### Contributing

Issues and pull requests are welcome! Potential improvements:
- More citation formats (Chicago, Vancouver, etc.)
- Domain-specific note templates (medicine, law, economics)
- Integration with other note-taking tools (Logseq, Notion)

### License

MIT

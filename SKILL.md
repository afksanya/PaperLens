---
name: PaperLens
description: >
  Use this skill whenever the user wants to read, understand, summarize, or take notes on academic
  papers or research papers — especially when one or more PDF files are involved. Trigger on phrases
  like "帮我读这篇论文", "给我生成笔记", "総説を書いて", "論文を読んで", "read this paper", "generate notes",
  "帮我写综述", "survey these papers", or any time a PDF of a research paper is mentioned. Also
  trigger when the user points to a folder containing papers and wants a summary or comparison.
  Handles BOTH single-paper notes AND multi-paper survey notes with references.
  Do NOT trigger for PDF manipulation (merging, splitting, OCR) — use the pdf skill for those.
---

# PaperLens — Academic Paper Notes

## Step 0: Language Selection

**Before doing anything else**, greet the user with this exact prompt so speakers of all three
languages can understand it:

> 请选择笔记语言 / メモの言語を選んでください / Please choose your note language:
>
> 1. 中文
> 2. 日本語
> 3. English

Wait for the user's reply, then set `LANG` to one of: `zh`, `ja`, `en`.

If the user's original message already makes their language obvious (e.g., they wrote entirely
in Japanese, or said "in English please"), skip the prompt and infer the language directly.

All subsequent output — note body, confirmation messages, questions — must use `LANG`.
Technical terms (model names, dataset names, metric names) are always kept in English regardless
of `LANG`.

---

## Step 1: Clarify Mode

Two modes are available:

| Mode | Trigger | Output |
|------|---------|--------|
| **Single Paper** | One PDF path given | One structured reading note |
| **Survey** | Multiple PDFs or a folder path | Summary cards + one survey note with APA references |

If unclear, ask in `LANG`:
- zh: "你是想给单篇论文生成笔记，还是对多篇论文做综述？"
- ja: "一本の論文のメモを作成しますか、それとも複数の論文のサーベイを書きますか？"
- en: "Should I generate notes for a single paper, or write a survey across multiple papers?"

---

## Step 2: Read the Papers

Use the `Read` tool with the PDF path. The tool handles PDFs natively.

- **≤ 20 pages**: read in one call
- **> 20 pages**: read in batches (`pages: "1-20"`, `"21-40"`, …), covering at minimum:
  abstract, introduction, methodology, experiments/results, conclusion, limitations
- **Survey / folder**: run `ls "<folder>"/*.pdf` via Bash to list files, then read each

If no path given, ask in `LANG`:
- zh: "请提供论文的 PDF 文件路径或所在文件夹路径。"
- ja: "論文の PDF ファイルパスまたはフォルダパスを教えてください。"
- en: "Please provide the PDF path or folder path."

---

## Mode A: Single Paper Notes

Output in chat first. Save to file only if the user explicitly asks.

### Template — zh (中文)

```markdown
---
title: "[论文完整标题]"
authors: ["[Last, First]"]
year: YYYY
venue: "[Venue, Year]"
tags: [paperlens, [领域], [方法]]
date: YYYY-MM-DD
type: paper-note
arxiv: "[ID or N/A]"
lang: zh
---

# [论文完整标题]
**作者**: ... | **发表**: Venue, Year | **arXiv**: ...

## 一句话总结
[一句话：用什么方法解决了什么问题，达到了什么效果——要有具体数字]

## 背景与动机 (Motivation)
[现有方法的不足，本文想填补的空白，1–3 句]

## 核心贡献 (Core Contributions)
- **[方法/数据集/理论/系统]**: [描述]

## 方法 (Methodology)
[核心技术路线；重要公式用 LaTeX，如 $\mathcal{L} = ...$]

## 实验结果 (Experiments & Results)
- **数据集**: ...
- **主要结论**: [关键数字]

## 局限性 (Limitations)
[论文自述的局限；若未提及则注明]

## 延伸阅读
- [[相关论文]] — [一句话理由]
```

### Template — ja (日本語)

```markdown
---
title: "[論文タイトル]"
authors: ["[Last, First]"]
year: YYYY
venue: "[Venue, Year]"
tags: [paperlens, [分野], [手法]]
date: YYYY-MM-DD
type: paper-note
arxiv: "[ID or N/A]"
lang: ja
---

# [論文タイトル]
**著者**: ... | **発表**: Venue, Year | **arXiv**: ...

## 一言まとめ
[一文で：どんな手法で何の問題を解決し、どんな成果を得たか——具体的な数値を含めること]

## 背景と動機 (Motivation)
[既存手法の課題、この論文が埋めるギャップ、1–3文]

## 主な貢献 (Core Contributions)
- **[手法/データセット/理論/システム]**: [説明]

## 手法 (Methodology)
[核心的な技術アプローチ；重要な数式は LaTeX で、例：$\mathcal{L} = ...$]

## 実験結果 (Experiments & Results)
- **データセット**: ...
- **主な結果**: [具体的な数値]

## 限界 (Limitations)
[論文が認める限界；言及がなければその旨を記載]

## 関連文献
- [[関連論文タイトル]] — [一言でなぜ読む価値があるか]
```

### Template — en (English)

```markdown
---
title: "[Full Paper Title]"
authors: ["[Last, First]"]
year: YYYY
venue: "[Venue, Year]"
tags: [paperlens, [field], [method]]
date: YYYY-MM-DD
type: paper-note
arxiv: "[ID or N/A]"
lang: en
---

# [Full Paper Title]
**Authors**: ... | **Published**: Venue, Year | **arXiv**: ...

## Summary
[One sentence: what method solves what problem, achieving what result — include specific numbers]

## Background & Motivation
[What gap does this paper fill? What are the shortcomings of prior work? 1–3 sentences]

## Core Contributions
- **[Method/Dataset/Theory/System]**: [description]

## Methodology
[Core technical approach; important formulas in LaTeX, e.g. $\mathcal{L} = ...$]

## Experiments & Results
- **Datasets**: ...
- **Key findings**: [specific numbers]

## Limitations
[Acknowledged limitations; note if the paper doesn't discuss them]

## Further Reading
- [[Related Paper Title]] — [one-line reason to read it]
```

### File Saving (When Asked)

File name: `<short-english-title>-notes.md` (lowercase, hyphens, no punctuation).
Location: vault root or `Papers/` folder if it exists — check with `ls` first.
Confirm in `LANG` after saving.

---

## Mode B: Multi-Paper Survey Notes

### Summary Cards

For each paper, generate a compact card in `LANG`:

**zh**
```markdown
## [[论文标题]] (Year)
**作者/Venue** | **核心贡献**: ... | **方法关键词**: ... | **主要结果**: ...
```

**ja**
```markdown
## [[論文タイトル]] (Year)
**著者/Venue** | **主な貢献**: ... | **手法キーワード**: ... | **主な結果**: ...
```

**en**
```markdown
## [[Paper Title]] (Year)
**Authors/Venue** | **Key contribution**: ... | **Method keywords**: ... | **Main result**: ...
```

### Survey Note Template

Adapt section headings and body text to `LANG`. The structure is the same for all three:

1. Overview table (paper | method | dataset | key metric)
2. Methodology Comparison
3. Results Comparison
4. Research Trends & Open Problems
5. References (APA format, always in English regardless of `LANG`)

YAML frontmatter:
```yaml
title: "[Topic] — [Survey / 综述 / サーベイ]"
papers: ["[[Title1]]", "[[Title2]]"]
tags: [paperlens, survey, [field]]
date: YYYY-MM-DD
type: survey-note
lang: [zh|ja|en]
```

### APA Reference Rules (always English)

- **Journal**: `Last, F. I. (Year). Title. *Journal*, *Vol*(Issue), pp. https://doi.org/...`
- **Conference**: `Last, F. I. (Year). Title. In *Proceedings of Conference* (pp. x–x).`
- **Preprint**: `Last, F. I. (Year). *Title*. arXiv preprint arXiv:XXXX.XXXXX.`

---

## Output Quality Rules (All Modes, All Languages)

- **Specific over vague**: write "F1 improved by 1.8 points on GLUE" not "achieved good results"
- **Keep English terms**: model names, dataset names, metric names stay in English even in zh/ja notes
- **Wiki links**: use `[[Title]]` for cross-references so Obsidian graph shows connections
- **Honest**: if a section of the PDF is unclear or missing, say so — never fabricate

---
name: paper-notes
description: >
  Use this skill whenever the user wants to read, understand, summarize, or take notes on academic
  papers or research papers — especially when one or more PDF files are involved. Trigger on phrases
  like "帮我读这篇论文", "给我生成笔记", "总结一下这篇paper", "read this paper", "generate notes",
  "帮我写综述", "对这几篇论文做个综述", "survey these papers", or any time a PDF of a research paper
  is mentioned. Also trigger when the user points to a folder containing papers and wants a summary
  or comparison. This skill handles BOTH single-paper notes AND multi-paper survey notes with references.
  Do NOT trigger for PDF manipulation (merging, splitting, OCR) — use the pdf skill for those.
---

# Paper Notes — 学术论文笔记生成

## Overview: Two Modes

This skill has two modes. Decide which to use based on input:

| 模式 | 触发条件 | 输出 |
|------|---------|------|
| **单篇笔记** | 用户给出一个 PDF 路径 | 一份结构化阅读笔记 |
| **综述模式** | 用户给出多个 PDF 路径，或指向一个含多篇论文的文件夹 | 每篇论文的简短摘要卡 + 一份综述笔记（含 APA 参考文献） |

If it's unclear, ask: "你是想给单篇论文生成笔记，还是对多篇论文做综述？"

---

## Step 1: Read the Papers

Use the `Read` tool with the PDF file path. The tool handles PDFs natively.

**Single paper:**
- Pages ≤ 20: read in one call (omit `pages` parameter)
- Pages > 20: read in batches of 20 (e.g., `pages: "1-20"`, then `"21-40"`, etc.)
  Focus on: abstract, introduction, methodology, experiments/results, conclusion, limitations.
  You may skim appendices and dense reference lists.

**Multiple papers / folder:**
- If a folder path is given, use `Bash` to list PDF files: `ls "<folder_path>"/*.pdf`
- Read each PDF in sequence, extracting key metadata first (title, authors, venue, year from the
  first 2–3 pages), then reading the full paper.
- For very long papers (>30 pages) in survey mode, focusing on abstract + intro + conclusion +
  key results is acceptable — note this in the output.

If no path is given, ask: "请提供论文的 PDF 文件路径或所在文件夹路径。"

---

## Mode A: Single Paper Notes

### Note Template

Output in chat first. Save to file only if the user asks (says "保存"/"存文件"/"save to file").

````markdown
---
title: "[论文完整标题]"
authors: ["[Author1 Last, First]", "[Author2 Last, First]"]
year: [YYYY]
venue: "[会议/期刊全称, Year]"
tags: [paper-notes, [领域标签], [方法标签]]
date: [今天日期 YYYY-MM-DD]
type: paper-note
arxiv: "[arXiv ID 如有]"
---

# [论文完整标题]

**作者**: [姓名] | **发表**: [Venue, Year] | **arXiv**: [ID or N/A]

---

## 一句话总结
[一句中文，具体说清楚"谁用什么方法解决了什么问题，达到了什么效果"]

## 背景与动机 (Motivation)
[这篇论文想解决什么问题？现有方法有何不足？1–3 句话]

## 核心贡献 (Core Contributions)
- **[贡献类型：方法/数据集/理论/系统]**: [具体描述]
- [多个贡献各占一行]

## 方法 (Methodology)
[核心技术路线，关键模块或算法步骤。
重要公式用行内 LaTeX，例如：损失函数为 $\mathcal{L} = \mathcal{L}_{ce} + \lambda \mathcal{L}_{kl}$。
聚焦"怎么做的"，不必复述每个细节。]

## 实验结果 (Experiments & Results)
- **数据集**: [名称]
- **主要结论**: [关键数字，例如 "比 baseline X 提升 2.3% top-1 accuracy on ImageNet"]

## 局限性 (Limitations)
[论文自述的局限，或读者容易发现的缺陷。若论文未提及，注明"论文未显式讨论局限性"。]

## 延伸阅读
- [[相关论文标题]] — [一句话说明为何值得看]
- [技术概念名称] — [简要说明]
````

### File Saving (When Asked)

- File name: `<短英文标题>-notes.md`（去掉标点，空格换成 `-`，全小写）
- Location: user's vault root or a `Papers/` folder if it exists — check with `ls` first
- After writing, confirm: "✓ 已保存到 `Papers/<filename>.md`"

---

## Mode B: Multi-Paper Survey Notes

Generate two outputs:

### Output 1: Paper Summary Cards (摘要卡)

For each paper, a compact summary card (shown inline in the survey note via wiki links):

````markdown
## [[论文标题]] (Year)
**作者**: ... | **Venue**: ...
**核心贡献**: [1–2 句]
**方法关键词**: [技术术语, 用英文]
**主要结果**: [关键数字或结论]
````

### Output 2: Survey Note (综述笔记)

````markdown
---
title: "[综述主题] 综述笔记"
papers: ["[[论文标题1]]", "[[论文标题2]]"]
tags: [survey, [领域标签]]
date: [今天日期 YYYY-MM-DD]
type: survey-note
---

# [综述主题] — 文献综述

## 背景
[这组论文研究的共同问题或领域]

## 论文总览

| 论文 | 方法 | 数据集 | 核心指标 |
|------|------|--------|---------|
| [[论文标题1]] | [方法名] | [数据集] | [数字] |
| [[论文标题2]] | ... | ... | ... |

## 主要方法对比 (Methodology Comparison)
[各方法的共同点与差异，可以按技术路线分组比较]

## 实验结果对比 (Results Comparison)
[在相同/相近数据集上的性能对比，指出各方法的优劣]

## 研究趋势与开放问题
[这个方向的发展脉络，还有哪些问题没解决]

## 参考文献 (References)

[1] Last, F., & Last, F. (Year). *Title of the paper*. *Venue/Journal*, volume(issue), pages. https://doi.org/...

[2] ...
````

### APA Reference Formatting Rules

Extract from each PDF: authors, year, title, venue/journal, volume/issue/pages if available, DOI or arXiv URL.

Format:
- **Journal**: `Last, F. I., & Last, F. I. (Year). Title of article. *Journal Name*, *Vol*(Issue), pages. https://doi.org/xxx`
- **Conference**: `Last, F. I., & Last, F. I. (Year). Title of paper. In *Proceedings of Conference Name* (pp. xxx–xxx).`
- **Preprint (arXiv)**: `Last, F. I., & Last, F. I. (Year). *Title*. arXiv preprint arXiv:XXXX.XXXXX.`

Use `[N]` citation markers in the survey body text when referencing a specific paper.

### File Saving for Survey Mode

If the user asks to save:
- Individual paper notes: `Papers/<short-title>-notes.md`
- Survey note: `Papers/Surveys/<topic>-survey.md`
- Create directories if they don't exist (use `Bash`: `mkdir -p "Papers/Surveys"`)

---

## Output Style (Both Modes)

- **双语**: 章节标题括号内标注英文；技术术语首次出现保留英文原词（如 attention、fine-tuning、RLHF）
- **具体**: 写 "在 GLUE benchmark 上 F1 提升 1.8 分" 而非 "取得了好效果"
- **诚实**: 如果某部分 PDF 读不清楚或信息缺失，如实说明，不要编造
- **Wiki 链接**: 综述中对其他已知论文用 `[[论文标题]]` 格式，方便在 Obsidian 图谱中建立关联

# CREATE_PRESENTATION

Builds a ready-to-present slide deck from the wiki. One Markdown file per paper section, saved under `wiki/pages/presentation/`. Each file is self-contained: a human can open any single file and have everything needed to present that section.

---

## Source material (read in this order)

1. `wiki/index.md` — discover which pages and glossary entries exist
2. `wiki/pages/digest-*.md` — section map and key findings (primary scaffold, if it exists)
3. `wiki/pages/deep-dive-*.md` — one per section, in paper order (preferred source for slide content)
4. `wiki/pages/annotation-*.md` — fallback if no deep-dive exists for a section
5. `wiki/pages/presentation-outline.md` — if it exists, use as a structure guide; never delete it
6. Relevant glossary entries — for precise wording of technical terms

If neither a deep-dive nor an annotation exists for a section, generate slides directly from the paper (cite page numbers). Use `> [!caution] Based on paper reading — no wiki annotation yet` to flag these.

---

## Output

- **Folder**: `wiki/pages/presentation/`
- **One file per paper section**: `wiki/pages/presentation/section-<slug>.md`
- **Index file**: `wiki/pages/presentation/index.md` — flat ordered list of all section files

All output files must begin with standard wiki frontmatter (see CLAUDE.md § Document metadata).

---

## Slide count per section

| Section type | Slides |
|---|---|
| Short (< 1 paper page) | 1 |
| Standard (1–3 pages) | 1–2 |
| Major section (Methods, Results) | 2–3 |

Hard limit: 3 slides per section file. When a section is too dense, keep the most important claim and cut the rest.

---

## File template

```markdown
---
CreatedAt: YYYY-MM-DD
LastUpdateAt: YYYY-MM-DD
LastReviewAt: null
ReviewerIds:
  - admin
OwnerIds:
  - admin
IssueNotes: null
GeneratedBy: claude-sonnet-4-6
readOrderIndex: 0
---

# SectionName — part 1

*One-sentence headline: the single most important claim this slide makes.*

- Supporting point 1
- Supporting point 2
- Supporting point 3 (optional)

![[prj.home/OriginalPaper.Structural.GPCR.DeNovoProteinLigand.btag027.pdf#page=N]]
*Figure N — brief description (p. N).*

# SectionName — part 2  *(omit if not needed)*

*One-sentence headline.*

- Supporting point 1
- Supporting point 2

# SectionName — part 3  *(omit if not needed)*

...

# Presenter Notes

Script covering all slide parts in order. For each part write 3–5 sentences:
- What is the main argument?
- Why does it matter to this audience?
- What analogy or example makes it concrete?
- What question might the audience ask, and what is the answer?

Link to glossary terms using [[wiki-links]] throughout.
```

---

## Mandatory elements per section file

| Element | Requirement |
|---|---|
| Frontmatter | All fields from CLAUDE.md § Document metadata |
| Headline claim | Italic sentence immediately after every `#` slide heading |
| Bullets | 2–3 fragments per slide; no full sentences |
| Figure | At least one PDF-page embed or link per file; italicised caption required |
| Presenter Notes | One `# Presenter Notes` block at the end covering all parts |

---

## Figures

Follow the imagery conventions from CLAUDE.md:

- **Embed** key figures inline: `![[prj.home/OriginalPaper.Structural.GPCR.DeNovoProteinLigand.btag027.pdf#page=N]]`
- **Link** secondary figures: `[[prj.home/OriginalPaper.Structural.GPCR.DeNovoProteinLigand.btag027.pdf#page=N|Figure N – caption]]`
- Always follow with a caption: `*Figure N — brief description (p. N).*`

---

## Callouts

Use sparingly — at most one per slide body:

- `> [!Example] Analogy` — for a non-expert analogy that makes a concept tangible
- `> [!caution] Limitation` — for a genuine limitation the presenter must acknowledge
- `> [!caution] Based on paper reading — no wiki annotation yet` — when a slide has no wiki backing

---

## Figure slides

Generate one file per paper figure: `wiki/pages/presentation/figure-<slug>.md`.

Each file contains a **single slide** dedicated to that figure. Structure:

```markdown
# Figure N — [short title]

*One sentence: the single claim this figure proves.*

- Bullet interpreting panel / row / column A
- Bullet interpreting the key trend or comparison
- Bullet on the implication for the paper's argument

![[prj.home/OriginalPaper...pdf#page=N]]
*Figure N — full caption as given in the paper (p. N).*

# Presenter Notes
Script: describe what is shown, walk through panels in order, name the key number to read out loud, and pre-answer the most likely audience question about this figure.
```

**Rules for figure slides:**
- Always reproduce the paper's figure caption verbatim in the italicised caption line.
- Walk through every panel (A, B, C…) in the presenter notes.
- Name at least one specific number (a data value, a slope, a count) in the bullet points.
- If the figure is a scatter plot, note both the trend and the scatter.

---

## Table slides

Generate one file per paper table: `wiki/pages/presentation/table-<slug>.md`.

Each file contains a **single slide** that teaches the audience how to read the table and what to take from it:

```markdown
# Table N — [short title]

*One sentence: what question this table answers and what the answer is.*

- How to read the table: columns are X; rows are Y; the key metric is Z
- The most important number or comparison (cite exact value)
- What this proves for the paper's central claim

[[prj.home/OriginalPaper...pdf#page=N|Table N, p. N]]
*Table N — brief description (p. N).*

# Presenter Notes
Script: explain column headers before showing data, call out the headline number, then immediately connect it back to the broader argument. Anticipate "how do we know this is meaningful?" and answer it.
```

**Rules for table slides:**
- Always explain what each column means before citing numbers.
- Cite at least two specific data cells by value.
- Explicitly connect the table's numbers to the paper's central claim.
- Use a labelled link (not embed) for tables — they are secondary references; the slide text carries the interpretation.

---

## Prerequisite slides

Generate one file per PREREQUISITE-tagged glossary entry: `wiki/pages/presentation/prereq-<slug>.md`.

These slides appear **before** the main section slides in the index. They equip the audience with the background needed to follow the methods.

Each file follows the standard section template (1–2 slides maximum). Pull content from the glossary entry; do not reproduce the entry verbatim — distil it to what a presenter actually needs to say out loud:

```markdown
# [Concept name] (Background)

*One sentence: why this concept matters for understanding AI-MCLig.*

- Core definition in plain language
- The key property that AI-MCLig exploits
- One concrete number or example from the paper

[figure or equation reference if relevant]

# Presenter Notes
Script pitched at a mixed audience. Include the analogy from the glossary entry if one exists. Anticipate the question a biologist (not a computer scientist) would ask.
```

**Ordering:** list prerequisite slides in the index by `readOrderIndex` (most negative first). If two entries share the same index, alphabetical order.

---

## Post-generation checklist

1. Create or update `wiki/pages/presentation/index.md` with a flat ordered list of all section files and one-line summaries.
2. Update `wiki/index.md` — add or update a **Presentation** section listing all generated files.
3. Append to `wiki/log.md`:

```
YYYY-MM-DD  CREATE_PRESENTATION  Generated N section files in wiki/pages/presentation/
```

---

## Rules

- Never modify anything in `prj.home/`.
- Never delete `wiki/pages/presentation-outline.md` — presentation section files supplement it, they do not replace it.
- Keep slide language simple: assume a mixed audience that includes non-specialists.
- Every technical term that has a glossary entry must be linked with `[[term]]`.
- All math must be LaTeX (`$...$` inline, `$$...$$` display).

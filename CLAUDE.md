# Paper Study Wiki — AI-MCLig

A structured knowledge base for deep-reading a single research paper.
Maintained by Claude Code; curated and directed by the human.

**Paper:** *De Novo Protein–Ligand Design Including Protein Flexibility and Conformational Adaptation* (btag027)
**Location:** `prj.home/OriginalPaper.Structural.GPCR.DeNovoProteinLigand.btag027.pdf`
**Notes:** `prj.home/Note.OriginalPaper.Structural.GPCR.DeNovoProteinLigand.btag027.md`

---

## Purpose

This wiki supports five goals, each with its own workflow:

1. **Prerequisite mapping** — identify and define every concept the reader needs before the paper makes sense
2. **Article annotation** — paragraph-level supporting notes for each section of the paper
3. **Presentation outline** — a slide-by-slide plan for presenting the paper to an audience
4. **Demonstration plan** — a hands-on activity or live demo tied to the paper's method
5. **Critical analysis** — weaknesses of arguments, thin explanations, open questions, and possible flaws

---

## Folder structure

```
prj.home/                    -- original paper + initial notes (immutable — never modify)
  OriginalPaper.*.pdf        -- the source paper
  Note.OriginalPaper.*.md    -- initial reading notes

wiki/pages/                  -- full analysis pages maintained by Claude
wiki/glossary/               -- atomic entries: one file per term, concept, or prerequisite
wiki/index.md                -- table of contents for the entire wiki
wiki/log.md                  -- append-only record of all operations
```

---

## Commands

### `/DIGEST_FOR_SUMMARY`

Produces a full structured digest of the paper covering: At a Glance, Background, Methods overview, Key findings, Limitations, Why it matters, Presentation plan, Demonstration plan, and a forward-looking roadmap. Saves the result to `wiki/pages/digest-<paper-slug>.md`.

Full instructions live in `.claude/commands/DIGEST_FOR_SUMMARY.md`.

### `/DEEP_DIVE_DIGEST <file or section>`

Generates a granular reading aid for a specific file or section. Produces: an orientation frame, a prerequisite checklist, a paragraph-by-paragraph annotation, dedicated unpacking of every formula and figure, a reconstructed argument chain, subtle-points commentary, open questions, within-paper and outside connections, and self-test questions. Saves to `wiki/pages/deep-dive-<slug>.md`.

Full instructions live in `.claude/commands/DEEP_DIVE_DIGEST.md`.

### `/LINT_GLOSSARY`

Audits every file in `wiki/glossary/`: validates frontmatter fields, checks type tags, reassigns `readOrderIndex` values to reflect the actual dependency graph, and reports any structural problems. Index values **may change** during this operation. The command never touches content — only metadata and structure.

Full instructions live in `.claude/commands/LINT_GLOSSARY.md`.

### `/CREATE_PRESENTATION`

Builds a ready-to-present slide deck from the wiki. Reads deep-dive pages (or annotation pages as fallback) in paper order and generates one Markdown file per section under `wiki/pages/presentation/`. Each file contains 1–3 slides with headline claims, bullet points, figure embeds, and a full presenter-notes script. Also creates `wiki/pages/presentation/index.md` and updates `wiki/index.md` and `wiki/log.md`.

Full instructions live in `.claude/commands/CREATE_PRESENTATION.md`.

---

## Workflows

### 1. Prerequisite mapping

When the user asks to map prerequisites, or when a concept blocks understanding of the paper:

1. Read the paper section (or the user's question) to identify assumed knowledge
2. For each prerequisite term or concept, check `wiki/glossary/` — create an entry if missing
3. Tag prerequisite entries with `PREREQUISITE` in addition to their type tag
4. Link every prerequisite entry back to the paper section(s) that assume it
5. Update `wiki/index.md` with new glossary entries
6. Append to `wiki/log.md`

### 2. Article annotation

When the user asks to annotate a section or paragraph of the paper:

1. Read the specified section from the PDF (cite page numbers)
2. Write a supporting note page in `wiki/pages/` named `annotation-<section-slug>.md`
3. For every concept used, link to its glossary entry (`[[term]]`)
4. Embed relevant figures from the paper using PDF page anchors (see **Imagery**)
5. Mark open questions with a `> [!question]` callout
6. Mark thin or poorly explained points with a `> [!caution]` callout and note what is missing
7. Update `wiki/index.md` and `wiki/log.md`

### 3. Presentation outline

When the user asks to outline the presentation:

1. Read `prj.home/Note.OriginalPaper.*.md` section 7 (Presentation plan) as a starting point
2. Write or update `wiki/pages/presentation-outline.md`
3. For each slide, specify: **title**, **key point**, **supporting evidence** (figure or table reference), **speaker notes** (one sentence)
4. Use `> [!Example]` callouts for analogies intended for a non-expert audience
5. Update `wiki/index.md` and `wiki/log.md`

### 4. Demonstration plan

When the user asks to outline the demonstration:

1. Read `prj.home/Note.OriginalPaper.*.md` section 8 (Demonstration plan) as a starting point
2. Write or update `wiki/pages/demonstration-plan.md`
3. Structure as numbered steps with expected outcomes per step
4. Flag any compute or dependency requirements prominently with `> [!caution]`
5. Update `wiki/index.md` and `wiki/log.md`

### 5. Critical analysis

When the user asks to analyse weaknesses, gaps, or thin arguments:

1. Read the relevant section(s) of the paper
2. Write or update `wiki/pages/critical-analysis.md`
3. Organise findings under: **Methodological weaknesses**, **Argument gaps**, **Thin explanations**, **Open questions**
4. Use `> [!danger]` for a claim that appears unsupported or potentially wrong
5. Use `> [!question]` for genuine open questions the paper does not answer
6. Cite the exact paper page for every finding
7. Update `wiki/index.md` and `wiki/log.md`

---

## Document metadata

Every wiki page and glossary entry must open with an Obsidian YAML frontmatter block. This system tracks AI provenance and outstanding human work.

### Fields

| Field | Type | Set by | Default | Notes |
|-------|------|--------|---------|-------|
| `CreatedAt` | `YYYY-MM-DD` | AI or human | — | Set once at creation; never changed |
| `LastUpdateAt` | `YYYY-MM-DD` | AI or human | = `CreatedAt` | Update every time the file is meaningfully changed |
| `LastReviewAt` | `YYYY-MM-DD` or `null` | Human only | `null` | AI must never set this field |
| `ReviewerIds` | list of strings | Human only | `[admin]` | AI must never set or modify this field |
| `OwnerIds` | list of strings | Human only | `[admin]` | AI must never set or modify this field |
| `IssueNotes` | string or `null` | Human only | `null` | Describes what the issue is |
| `GeneratedBy` | string or `null` | AI only | `null` (human-written) | AI sets this to its model ID (e.g. `claude-sonnet-4-6`) when it creates or substantially rewrites a file |
| `readOrderIndex` | integer | AI | — | Reading-order hint; see **readOrderIndex** section below |

### Purpose

- `LastReviewAt: null` + `GeneratedBy: <model>` = AI-generated, never human-reviewed → priority for human review queue.
- `IssueNotes` != null = known problem; human should read and fix.
- `LastReviewAt` populated = a human has validated the content.
- `readOrderIndex` encodes where each entry sits in the reader's learning path (see below).

### readOrderIndex

`readOrderIndex` is an integer that tells the reader **when** to engage with an entry relative to others.

| Value | Meaning |
|-------|---------|
| Negative (e.g. `-2`, `-1`) | **Prerequisite** — background knowledge that must be in place before reading any part of the paper. More negative = more foundational (a foundation-of-foundations). These entries should also carry the `PREREQUISITE` tag. |
| `0` | **Entry point** — can be tackled as soon as all prerequisite entries are understood; no further glossary dependencies beyond prerequisites. |
| Positive (e.g. `1`, `2`, `3`) | **Progressive** — builds on lower-indexed entries; the reader should attempt these only after grasping everything at a lower index. Higher values = deeper into the material. |

**Rules for AI assignment:**
- Multiple entries may share the same value; that means they are at the same conceptual layer and can be read in any order among themselves.
- Gaps between values are allowed — use them freely to leave room for insertions.
- Set a new entry's index to `max(readOrderIndex of all entries it depends on) + 1`. If it has no dependencies, use `0` (or a negative value if it is prerequisite background knowledge).
- `readOrderIndex` values **may be reassigned** during `/LINT_GLOSSARY` as the dependency graph becomes clearer. This is expected and intentional.

### AI rules for metadata

- **On file creation**: set `CreatedAt`, `LastUpdateAt` (= today), `GeneratedBy` (= current model ID), and `readOrderIndex` (see assignment rules above). Leave all human-only fields at their defaults.
- **On file update**: set `LastUpdateAt` to today. Do not touch `LastReviewAt`, `ReviewerIds`, `OwnerIds`, `IssueNotes`.
- **Never** set `LastReviewAt` to a date — even if the user says "this looks good." Only a human explicitly editing that field counts as a review.

---

## Page format

Every wiki page should follow this structure:

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

# Page Title

**Summary**: One to two sentences describing this page.

**Paper section**: Which section(s) of the paper this page covers (e.g. §3.2, p. 4–5).

---

Main content goes here. Use clear headings and short paragraphs.

Link to related concepts using [[wiki-links]] throughout the text.

Embed relevant figures from the paper inline, immediately after the paragraph that references them.

## Related pages

- [[related-concept-1]]
- [[related-concept-2]]

## Other sources
External material covering the same topic: links, book chapters, YouTube videos, papers.

## Test yourself
Flashcards, Q&A, mnemonics useful for exam or presentation preparation.
```

---

## Glossary entry format

Each file in `wiki/glossary/` is an atomic note about a single term or concept. The filename is the entry's title (spaces are allowed — e.g., `Induced fit.md`).

Every entry must begin with Obsidian frontmatter:

```yaml
---
tags:
  - __DEFINITION   # or __CONCEPT, __EXECUTABLE, PREREQUISITE
  - prerequisite   # add this tag if the concept must be understood before reading the paper
CreatedAt: YYYY-MM-DD
LastUpdateAt: YYYY-MM-DD
LastReviewAt: null
ReviewerIds:
  - admin
OwnerIds:
  - admin
IssueNotes: null
GeneratedBy: claude-sonnet-4-6
readOrderIndex: -1   # negative = prerequisite; 0 = entry point; positive = progressive
---
```

### Types

| Tag | When to use | Content |
|-----|-------------|---------|
| `__DEFINITION` | A specific term needing a precise definition | Concise definition; add graphics or links when relevant |
| `__CONCEPT` | A broader idea requiring brief explanation | Short explanation; links to graphics or resources welcome |
| `__EXECUTABLE` | A problem or exercise the learner should solve | Brief strategy to approach and solve the challenge |
| `PREREQUISITE` | Background knowledge assumed by the paper | Used alongside one of the above types; marks entry as foundational |

### Rules for glossary entries

- One entry per file — never combine multiple terms.
- Filename = the exact term or question as it would appear in the paper or an exam.
- Graphics and wiki-links (`[[page-name]]`) are welcome.
- Do not add the standard page-format headers — the frontmatter is sufficient.

### Optional elements

**Footnotes** — use Obsidian footnote syntax (`[^1]` in text, `[^1]: ...` at the end) for tangential jargon the reader may not know but that would clutter the main definition.

**# TLDR** — add a `# TLDR` section when the core idea can be captured in one or two sentences and there is a real chance the reader will grasp "enough" from that alone. Omit when the entry is already brief or a shortcut would mislead.

---

## Imagery

Wiki pages should be enriched with visuals whenever they aid understanding.

### Standalone images (`.png`, `.jpg`, `.svg`)

```
![description](../../raw/filename.png)
```

### PDF figures and tables — two forms

Use **embed** to render the PDF page inline in Obsidian:

```
![[prj.home/OriginalPaper.Structural.GPCR.DeNovoProteinLigand.btag027.pdf#page=N]]
```

Use a **labelled link** when you want a clickable reference within a sentence:

```
[[prj.home/OriginalPaper.Structural.GPCR.DeNovoProteinLigand.btag027.pdf#page=N|Figure N – caption]]
```

Both forms open the PDF at the correct page. The `!` prefix embeds; without it you get an inline link.

### Placement and captioning rules

- Place visuals inline, immediately after the paragraph that references them.
- Always follow every embed or link with a one-line italicised caption: `*Figure N — brief description (p. N).*`
- Prefer figures that show a process, structure, or comparison.
- Use the embed form for key figures; use the link form for supplementary tables or secondary references.

---

## Obsidian callouts (block-quotes)

Use Obsidian callouts from `block-quote types.md` to improve readability. Choose the type that best matches the content:

| Callout | Syntax | When to use |
|---------|--------|-------------|
| Info | `> [!Info] Title` | Background context, neutral supplementary information |
| Summary | `> [!Summary] Title` | TLDR or recap of a longer explanation |
| Hint | `> [!Hint] Title` | Study tip, mnemonic, or shortcut for understanding |
| Example | `> [!Example] Title` | Concrete example or analogy |
| Quote | `> [!Quote] Title` | Direct quotation from the paper |
| Question | `> [!question] Title` | Open question not answered in the paper, or a study prompt |
| Caution | `> [!caution] Title` | Limitation, oversimplification, or missing detail |
| Danger | `> [!danger] Title` | Unsupported claim, potential error, or serious methodological gap |

**Rules:**
- Use callouts sparingly — one or two per page maximum. They mark exceptions, not the norm.
- Never use a callout as a substitute for good prose. Write the explanation first; add the callout only if it adds distinct value.
- `> [!question]` and `> [!danger]` in annotation pages feed directly into the **Critical analysis** workflow.

---

## Formulas and mathematics

Whenever a formula, equation, or mathematical expression is relevant, write it in LaTeX:

- Inline: `$...$` — for variables or short expressions within a sentence (e.g. $\Delta G = -RT \ln K_d$)
- Display (block): `$$...$$` — for standalone equations that deserve their own line:

$$\text{score} = 0.8 \cdot \text{Chai-1} - 0.1 \cdot \text{SA} + 0.05 \cdot \text{ESOL} + 0.05 \cdot \text{QED}$$

Obsidian renders both forms natively with MathJax. Always define every symbol in the surrounding text.

---

## Citation rules

- Every factual claim must reference the paper page or an external source.
- Paper citations: `[[prj.home/OriginalPaper.Structural.GPCR.DeNovoProteinLigand.btag027.pdf#page=N|p. N]]`
- External citations: inline URL or author–year shorthand with a full reference in **Other sources**.
- If two sources disagree, note the contradiction explicitly.
- If a claim has no source, mark it with `> [!caution] Needs verification`.

---

## Question answering

When the user asks a question:

1. Read `wiki/index.md` to find relevant pages
2. Read those pages and synthesise an answer
3. Cite specific wiki pages and paper sections in your response
4. If the answer is not in the wiki, say so clearly — then offer to add it
5. If the answer is valuable, file it as a new wiki page or glossary entry

---

## Lint

When the user asks to lint or audit the wiki:

- Check for contradictions between pages
- Find orphan pages (no inbound wiki-links from other pages)
- Identify concepts mentioned in pages that lack their own glossary entry
- Check that all pages follow the page format above
- Check that all files have the required metadata fields: `CreatedAt`, `LastUpdateAt`, `GeneratedBy`, `readOrderIndex`
- Flag any file where `GeneratedBy` is set and `LastReviewAt` is null — AI-generated and unreviewed
- Report findings as a numbered list with suggested fixes

For glossary-specific linting — including `readOrderIndex` reassignment, type tag validation, and dependency graph checks — use `/LINT_GLOSSARY` instead. That command may change `readOrderIndex` values across all glossary entries; this is expected behaviour.

---

## Rules

- **Never modify** anything in `prj.home/`
- Always update `wiki/index.md` and `wiki/log.md` after any changes
- Keep page filenames lowercase with hyphens (e.g. `annotation-methods.md`)
- Write in clear, plain language
- When uncertain about how to categorise something, ask the user
- All formulas and mathematical expressions must be written in LaTeX (`$...$` inline, `$$...$$` display)
- All syntax must be valid Obsidian markdown: use `[[wiki-links]]`, `[[file#page=N|label]]`, `[^footnotes]`, and `> [!callout]` as appropriate — not raw HTML or non-standard markdown

You are producing a **reading aid** for the file or section specified in the prompt after the command keyword. The reader wants to understand this material thoroughly, not just summarise it.

Read the target material in full before writing anything. Then produce the sections below in order. Save the result as `wiki/pages/deep-dive-<slug>.md` using the standard page frontmatter.

All formulas must use LaTeX (`$...$` inline, `$$...$$` display). Use Obsidian callouts (`> [!Info]`, `> [!caution]`, `> [!question]`, `> [!Hint]`, `> [!danger]`) where they improve clarity. All cross-references must use `[[wiki-links]]`.

---

## 0. Orientation
*One short paragraph.* Tell the reader what they are about to read, where it sits in the overall paper or document, and what single idea they should keep in mind as they work through it. This is the frame — write it so the reader can return to it if they get lost.

## 1. What you need before reading this
List every term, concept, or piece of background knowledge that the section assumes. For each item:
- State the term in bold
- Give a one-sentence plain-language definition
- Link to the glossary entry if one exists: `[[term]]`
- Flag it with `> [!caution] Prerequisite` if it is likely to block understanding if missed

## 2. Paragraph-by-paragraph annotation
Work through the target text sequentially. For each paragraph or logical block:

**[¶N – short label]**
- **What it says:** one sentence paraphrase
- **Why it matters:** one sentence on its role in the argument or pipeline
- **Plain-language expansion:** 2–4 sentences unpacking any jargon, implicit reasoning, or assumed knowledge
- If the paragraph contains a formula, unpack it immediately below using the format in §3
- If the paragraph is thin or makes an unsupported claim, add `> [!caution]` or `> [!danger]` with a specific note

Do not skip any paragraph. Do not merge unrelated paragraphs. If a paragraph is genuinely trivial (e.g. a transitional sentence), a single bullet is sufficient.

## 3. Formulas and figures unpacked
For every formula or figure that appears in the target material, produce a dedicated block:

**Formula / Figure [label or equation number]**
- **What it expresses:** one sentence on the mathematical or visual claim
- **Every symbol defined:** table or bullet list — symbol, name, units or range, intuition
- **Worked example or intuition:** a concrete case (plug in numbers, or describe what the figure would look like for a simple input) that makes the abstraction tangible
- **Connection:** how this formula or figure supports the argument of its paragraph

$$\text{example: score} = 0.8 \cdot \text{Chai-1} - 0.1 \cdot \text{SA} + 0.05 \cdot \text{ESOL} + 0.05 \cdot \text{QED}$$

## 4. The argument in one diagram
Reconstruct the logical flow of the section as a short chain:

**Premise → step → step → conclusion**

Use plain prose, not bullet points. This forces synthesis: if the chain cannot be written coherently, identify where the gap is and flag it with `> [!question]`.

## 5. What is easy to miss
2–5 observations about subtle points, implicit assumptions, or details that a fast reader would skip but that are important for deep understanding. Each item should be 2–3 sentences. Use `> [!Hint]` for positive insights and `> [!caution]` for traps.

## 6. Open questions
Bullet list of genuine questions this section raises but does not answer. Distinguish:
- **Author's own caveats** — things the authors acknowledge are unresolved
- **Reader's questions** — gaps or tensions a critical reader would notice that the authors do not flag

Use `> [!question]` for anything that warrants follow-up in the wiki.

## 7. Connections
How does this section connect to the rest of the paper or to outside knowledge? Produce two short lists:

**Within the paper**
- Forward links: what later sections depend on what was established here
- Backward links: what earlier results or definitions this section builds on

**Outside the paper**
- 2–3 connections to adjacent concepts, methods, or fields — each with one sentence explaining the link and a `[[wiki-link]]` if a glossary entry exists

## 8. Test yourself
5–8 questions a reader should be able to answer after studying this section. Mix levels:
- **Recall** — define a term, state a result
- **Comprehension** — explain in your own words, paraphrase a formula
- **Application** — given a new input, predict what the method would do
- **Critical** — identify a weakness, propose an alternative

Format as a numbered list. Do not provide answers inline — answers belong in a collapsible block or a separate glossary entry so the reader can self-test honestly.

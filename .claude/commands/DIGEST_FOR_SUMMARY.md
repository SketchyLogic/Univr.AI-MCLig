Produce a structured digest of the paper in `prj.home/`. Follow the required sections below in order. Write in clear, plain language. All formulas must use LaTeX (`$...$` inline, `$$...$$` display). Use Obsidian callouts (`> [!Info]`, `> [!caution]`, etc.) where they improve clarity. Save the result as `wiki/pages/digest-<paper-slug>.md` using the standard page frontmatter.

---

## 1. At a Glance
- **Type:** Original Research | Application Note | Review
- **Field:** (e.g. Structural Bioinformatics – GPCR; Systems Biology – Cell Fate)
- **Core question:** One sentence — what problem does the paper address?
- **Core answer:** One sentence — what is the main finding or contribution?

## 2. Background you need
2–4 short paragraphs covering the biological and computational context a student must grasp *before* the paper makes sense. Include why the problem matters and what was done before this work.

## 3. What they did (Methods overview)
Plain-language walkthrough of the key methods and pipeline steps. Skip implementation details; focus on *what* was done and *why* each step was necessary. Use a numbered list if there is a clear sequence.

## 4. Key findings
Bullet list of the 3–6 most important results. For each, add one sentence explaining *why* it matters or what it implies.

## 5. Limitations and open questions
2–4 bullet points on what the paper does not address, caveats the authors acknowledge, or questions a critical reader should have.

## 6. Why it matters
2–3 sentences: significance for the field, potential applications, or how it connects to adjacent work.

## 7. Presentation plan
A suggested structure for a 10–15 minute student presentation on this paper. Format as a numbered slide outline, each slide with:
- A suggested title
- Bullet points of content or talking points
- A note on any figure from the paper worth showing and why

Aim for 6–9 slides. Prioritise narrative flow: hook the audience with the problem before introducing the solution. Avoid slide-by-slide method recaps — build toward the key result.

## 8. Demonstration plan
If one or more ideas in the paper can be verified, explored, or illustrated using a freely available tool, dataset, or simple code snippet, describe a concrete hands-on activity. Include:
- **Goal:** what the student will observe or confirm
- **Tool / resource:** name and where to find it (e.g. a web server, a public dataset, a Python library)
- **Steps:** numbered, actionable instructions a student can follow in under 30 minutes
- **Expected outcome:** what a successful run looks like and how it connects back to the paper's claims

If no meaningful demonstration is feasible (e.g. the paper requires proprietary data or weeks of compute), state that clearly and suggest the closest available alternative (e.g. a related tutorial or toy dataset).

## 9. Continue from here
A forward-looking roadmap for a student who wants to go deeper. Structure it as three tiers:

**Go deeper into this paper**
2–3 specific things to do with the paper itself: re-read a section with fresh eyes, reproduce a figure, trace a cited claim back to its source, or run the authors' own code/data.

**Next papers to read**
3–5 concrete follow-up readings — direct citations from the paper or papers that cite it — each with one sentence explaining what it adds or challenges relative to this work. Prefer papers that are freely accessible.

**Research or development directions**
2–4 open directions a student could pursue: an unresolved question the paper raises, a method that could be applied to a new dataset, a tool that could be extended, or a biological question the findings motivate. Be specific — name the dataset, organism, tool, or question rather than giving generic advice.

# Wiki Operation Log

Append-only. Each entry records what changed, who or what changed it, and why.

---

## 2026-05-11

### [1] readOrderIndex field added to CLAUDE.md
**By:** claude-sonnet-4-6
**What changed:** Added `readOrderIndex` integer field to the Document metadata specification in `CLAUDE.md`. Added semantics table (negative = prerequisite; 0 = entry point; positive = progressive), assignment rules (max of linked entries + 1), and updated all frontmatter examples in the Page format and Glossary entry format sections. Updated AI rules to set `readOrderIndex` on file creation.
**Why:** User requested a dependency-graph-aware reading order mechanism for glossary entries and wiki pages.

---

### [2] /LINT_GLOSSARY command created
**By:** claude-sonnet-4-6
**What changed:** Created `.claude/commands/LINT_GLOSSARY.md` specifying the 7-step audit workflow (read files, build dependency graph, reassign readOrderIndex, check structure, write files, report, log). Added `/LINT_GLOSSARY` entry to the Commands section of `CLAUDE.md`.
**Why:** User requested a dedicated command for auditing the glossary dependency graph and reassigning `readOrderIndex` values.

---

### [3] LINT_GLOSSARY executed on prj.glossary/ (23 files)
**By:** claude-sonnet-4-6
**Scope:** All 23 files in `prj.glossary/`
**What changed:**
- Added standard Obsidian frontmatter (CreatedAt, LastUpdateAt, LastReviewAt, ReviewerIds, OwnerIds, IssueNotes, GeneratedBy, readOrderIndex, tags) to 19 files that had no frontmatter
- Replaced non-standard frontmatter in 4 files: `Ring Local Minima in MC.md`, `AI-MCLig Validation Strategy.md`, `Dice Similarity vs Chai-1 Scoring.md`, `MC Search Components.md`
- Assigned readOrderIndex values per dependency graph analysis

**readOrderIndex assignments:**
- Metropolis criterion: −3 (most foundational, no dependencies)
- AlphaFold, Binding site, GPCR, Induced fit, Monte Carlo simulation, pLDDT, SMILES: −2
- BRICS decomposition, De novo drug design, Molecular docking, Molecular fingerprint, Molecular scaffold, QED (drug-likeness), Synthetic accessibility score: −1
- Boltz-2, Bromodomain, Chai-1, MMGBSA: 0
- Dice Similarity vs Chai-1 Scoring: 1
- Ring Local Minima in MC, MC Search Components: 2
- AI-MCLig Validation Strategy: 3

**Structural problems reported (not auto-fixed):**
1. `Molecular fingerprint.md`: broken wiki-links `[[Tanimoto similarity]]`, `[[ECFP4]]`
2. `Molecular scaffold.md`: broken wiki-links `[[ScaffoldGraph]]`, `[[HierS algorithm]]`
3. `MC Search Components` ↔ `AI-MCLig Validation Strategy`: circular wiki-link dependency; readOrderIndex assigned by logical tie-breaking
4. All 23 entries: `LastReviewAt: null` — AI-generated, no human review

**Note:** CLAUDE.md specifies `wiki/glossary/` as the glossary directory; actual project uses `prj.glossary/`. LINT_GLOSSARY was applied to `prj.glossary/`.

---

## 2026-05-12

### [6] New glossary entry: MC Atomic Moves in SMILES
**By:** claude-sonnet-4-6
**What changed:** Created `prj.glossary/MC Atomic Moves in SMILES.md` (readOrderIndex: 3). Entry shows each of the 9 MC chemical operations as a concrete before → after SMILES transformation, with a summary table, an explanatory callout on Kekulé vs aromatic notation, links to `[[SMILES]]`, `[[MC Search Components]]`, `[[Ring Local Minima in MC]]`, and `[[BRICS decomposition]]`, and 5 self-test questions. Added a Glossary section to `wiki/index.md` listing the new entry.
**Why:** User requested a concept note integrating the glossary with SMILES-level illustrations of all 9 atomic moves.

---

### [4] DEEP_DIVE_DIGEST executed for all paper sections, figures, and tables
**By:** claude-sonnet-4-6
**Scope:** Full paper — 8 sections, 3 figures, 3 tables (14 files total)
**What changed:** Created 14 new files in `wiki/pages/`:

| File | Coverage | readOrderIndex |
|------|----------|----------------|
| `deep-dive-abstract.md` | Abstract — claims, scope, key terms | 0 |
| `deep-dive-introduction.md` | Introduction — problem framing, gap, key claims | 0 |
| `deep-dive-methods-mc-simulation.md` | MC algorithm: 9 operations, Eq. 1, reset, fragment-based MC | 1 |
| `deep-dive-methods-scoring.md` | Scoring: Eq. 2, symbol table, normalisation, bias-term interactions | 1 |
| `deep-dive-methods-mmgbsa.md` | MMGBSA protocol: MD setup, force fields, 750-frame sampling | 1 |
| `deep-dive-results-recovery.md` | Recovery experiment: Table 1, Figure 1, dice scores, ring failures | 2 |
| `deep-dive-results-de-novo.md` | De novo results: Figure 2, Figure 3, flexibility RMSD, fragment MC | 2 |
| `deep-dive-conclusion.md` | Conclusion: summary of claims, limitations, future directions | 2 |
| `deep-dive-figure-1.md` | Figure 1: Chai-1 vs affinity scatter plots, linear fits for 3 targets | 2 |
| `deep-dive-figure-2.md` | Figure 2: ligand evolution A–L (bromodomain + Pim-1, steps 0→2000) | 2 |
| `deep-dive-figure-3.md` | Figure 3: score trajectory, MMGBSA early steps, pocket RMSD | 2 |
| `deep-dive-table-1.md` | Table 1: recovery results, 8/10 exact recovery, ring failure analysis | 2 |
| `deep-dive-table-2.md` | Table 2: generated compounds, 12 runs × 7 metrics, cross-table analysis | 2 |
| `deep-dive-table-3.md` | Table 3: 16 reference binders, MMGBSA ranges, cross-comparison to Table 2 | 2 |

Each file follows the 8-section DEEP_DIVE_DIGEST format: Orientation, Prerequisites, Content annotation, Formulas/figures unpacked, Argument diagram, Easy-to-miss points, Open questions, Self-test questions.

---

### [5] wiki/index.md and wiki/log.md created
**By:** claude-sonnet-4-6
**What changed:** Created `wiki/index.md` (table of contents for all wiki pages and glossary entries, with readOrderIndex values and reading order recommendation) and `wiki/log.md` (this file, append-only operation record).
**Why:** CLAUDE.md protocol requires updating `wiki/index.md` and `wiki/log.md` after any changes to the wiki.

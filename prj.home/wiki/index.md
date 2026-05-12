---
CreatedAt: 2026-05-11
LastUpdateAt: 2026-05-11
LastReviewAt: null
ReviewerIds:
  - admin
OwnerIds:
  - admin
IssueNotes: null
GeneratedBy: claude-sonnet-4-6
readOrderIndex: 0
---

# Wiki Index — AI-MCLig Paper Study

**Paper:** *De Novo Protein–Ligand Design Including Protein Flexibility and Conformational Adaptation* (btag027)

This index covers every file in `wiki/pages/` and every entry in `prj.glossary/`. `readOrderIndex` values indicate the recommended reading order: negative = prerequisite background knowledge; 0 = accessible after prerequisites; positive = builds on earlier entries (higher = later).

---

## Deep-Dive Pages (`wiki/pages/`)

These are comprehensive reading aids — one per section, figure, and table of the paper.

### Abstract and Introduction

| Page | Summary | readOrderIndex |
|------|---------|----------------|
| [[deep-dive-abstract]] | Full annotation of the abstract: claims, scope, and what to verify | 0 |
| [[deep-dive-introduction]] | Paragraph-by-paragraph annotation of the Introduction: problem framing, gap, and claims | 0 |

### Methods

| Page | Summary | readOrderIndex |
|------|---------|----------------|
| [[deep-dive-methods-mc-simulation]] | The MC algorithm: 9 operations, Eq. 1 (Metropolis), fragment-based variant, reset mechanism | 1 |
| [[deep-dive-methods-scoring]] | The scoring function: Eq. 2 full symbol table, normalisation, bias-term interactions | 1 |
| [[deep-dive-methods-mmgbsa]] | MMGBSA validation protocol: MD setup, force fields, 750-frame sampling, igb=5 | 1 |

### Results

| Page | Summary | readOrderIndex |
|------|---------|----------------|
| [[deep-dive-results-recovery]] | Recovery experiment analysis: Table 1 statistics, Figure 1 Chai-1 correlation fits | 2 |
| [[deep-dive-results-de-novo]] | De novo generation analysis: Figure 2 ligand evolution, Figure 3 trajectories, flexibility RMSD | 2 |
| [[deep-dive-conclusion]] | Conclusion annotation: summary of claims, limitations, future directions | 2 |

### Figures

| Page | Summary | readOrderIndex |
|------|---------|----------------|
| [[deep-dive-figure-1]] | Figure 1 reading aid: Chai-1 vs experimental affinity scatter plots for three targets, linear fits | 2 |
| [[deep-dive-figure-2]] | Figure 2 reading aid: ligand evolution panels A–L (bromodomain and Pim-1, steps 0→2000) | 2 |
| [[deep-dive-figure-3]] | Figure 3 reading aid: score trajectory (Panel A), MMGBSA trajectory (Panel B), pocket RMSD (Panel C) | 2 |

### Tables

| Page | Summary | readOrderIndex |
|------|---------|----------------|
| [[deep-dive-table-1]] | Table 1 reading aid: recovery experiment — dice scores, ring failure analysis, 8/10 success rate | 2 |
| [[deep-dive-table-2]] | Table 2 reading aid: generated compounds — all 12 runs across 4 targets, column-by-column annotation | 2 |
| [[deep-dive-table-3]] | Table 3 reading aid: reference experimental binders — 16 compounds, MMGBSA ranges, cross-comparison | 2 |


---

## Glossary (`prj.glossary/`) — selected entries

| Entry | Summary | readOrderIndex |
|-------|---------|----------------|
| [[MC Atomic Moves in SMILES]] | Before → after SMILES pairs for all 9 MC operations | 3 |

*For the full 24-entry glossary listing, browse `prj.glossary/` directly. All entries carry `readOrderIndex` values; negative = prerequisite background, 0 = entry point, positive = progressive.*

---

## Known structural issues (from LINT_GLOSSARY 2026-05-11)

- **Broken wiki-links** in `Molecular fingerprint.md`: `[[Tanimoto similarity]]`, `[[ECFP4]]` — entries do not exist
- **Broken wiki-links** in `Molecular scaffold.md`: `[[ScaffoldGraph]]`, `[[HierS algorithm]]` — entries do not exist
- **Circular dependency**: `MC Search Components` ↔ `AI-MCLig Validation Strategy` — readOrderIndex assigned by logical tie-breaking; not auto-fixed
- **All 23 glossary entries** have `LastReviewAt: null` — AI-generated, no human review yet

---

## Reading order recommendation

1. Start with all entries at readOrderIndex ≤ −2 (foundational prerequisites)
2. Read entries at readOrderIndex = −1 (near-paper prerequisites)
3. Read `deep-dive-abstract.md` and `deep-dive-introduction.md`
4. Read entries at readOrderIndex = 0 (Boltz-2, Bromodomain, Chai-1, MMGBSA)
5. Read Methods deep-dives (readOrderIndex = 1)
6. Read glossary entries at readOrderIndex ≥ 1 (Dice Similarity, Ring Local Minima, etc.)
7. Read Results and Figure/Table deep-dives (readOrderIndex = 2)
8. Read `deep-dive-conclusion.md`

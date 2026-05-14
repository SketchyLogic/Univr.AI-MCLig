---
CreatedAt: 2026-05-14
LastUpdateAt: 2026-05-14
LastReviewAt: null
ReviewerIds:
  - admin
OwnerIds:
  - admin
IssueNotes: null
GeneratedBy: claude-sonnet-4-6
readOrderIndex: 0
---

# Presentation 2 — Slide Deck Index

Full paper walk-through, one file per section. Present files in the order listed below.

---

## Introduction

| File                                              | Slides | Summary                                                                             |
| ------------------------------------------------- | ------ | ----------------------------------------------------------------------------------- |
| [[wiki/pages/presentation2/section-introduction]] | 2      | Drug design problem; rigid protein assumption; Chai-1 as a flexibility-aware oracle |

## Materials and Methods

| File                                                       | Slides | Summary                                                                |
| ---------------------------------------------------------- | ------ | ---------------------------------------------------------------------- |
| [[wiki/pages/presentation2/section-methods-mc-simulation]] | 2      | 9 chemical operations; Metropolis criterion; two-stage search protocol |
| [[wiki/pages/presentation2/section-methods-fragment-mc]]   | 1      | BRICS fragment recombination; coarser moves; β = 5                     |
| [[wiki/pages/presentation2/section-methods-scoring]]       | 1      | Composite score: 0.8·Chai-1 − 0.1·SA + 0.05·ESOL + 0.05·QED            |
| [[wiki/pages/presentation2/section-methods-md-mmgbsa]]     | 1      | 20 ns MD + MMGBSA validation pipeline; igb = 5; 750 frames             |

## Results and Discussion

| File | Slides | Summary |
|---|---|---|
| [[wiki/pages/presentation2/section-results-recovery]] | 2 | Dice-similarity recovery test; 8/11 exact; ring local minima |
| [[wiki/pages/presentation2/section-results-chai1-affinity]] | 1 | Chai-1 vs. experimental affinity; Figure 1; average trend positive, scatter large |
| [[wiki/pages/presentation2/section-results-denovo-atomistic]] | 2 | From benzene to drug-like ligand; Figure 2; MMGBSA overlaps known binders; Figure 3 |
| [[wiki/pages/presentation2/section-results-fragment-mc]] | 1 | Recovery + de novo with fragments; target-specific fragments outperform random |

## Conclusions

| File | Slides | Summary |
|---|---|---|
| [[wiki/pages/presentation2/section-conclusions]] | 1 | Central claim; 4 targets validated; bottleneck is oracle; future: better AI = better search |

---

**Total slides: 13** across 10 section files.

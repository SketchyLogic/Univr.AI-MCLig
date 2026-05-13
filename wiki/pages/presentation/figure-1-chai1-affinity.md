---
CreatedAt: 2026-05-13
LastUpdateAt: 2026-05-13
LastReviewAt: null
ReviewerIds:
  - admin
OwnerIds:
  - admin
IssueNotes: null
GeneratedBy: claude-sonnet-4-6
readOrderIndex: 0
---

# Figure 1 — Chai-1 Score vs. Binding Affinity

*Higher Chai-1 confidence predicts better binding affinity on average — but the scatter is too large for individual predictions to be reliable.*

- Three targets tested: bromodomain (A), Pim-1 kinase (B), p38 kinase (C)
- Trend lines are consistently negative (better affinity = more negative kcal/mol), confirming the average correlation: e.g., bromodomain fit: $y = -7.80x + 0.74$
- Critical caveat: individual data points scatter widely at any given Chai-1 value — the score guides a population, not a single prediction

![[prj.home/OriginalPaper.Structural.GPCR.DeNovoProteinLigand.btag027.pdf#page=5]]
*Figure 1 — Illustration of the correlation between the experimental ligand-binding affinity and the Chai-1 confidence score for different ligands of the bromodomain complex (A), the serine/threonine-protein kinase pim-1 complex (B), and the p38 map kinase complex (C) (p. 5).*

# Presenter Notes

**Walking through Figure 1**

Show the figure and immediately orient the audience: x-axis is the Chai-1 confidence score (0 to 1, higher = more confident prediction); y-axis is experimental binding affinity in kcal/mol (more negative = tighter binding). Each dot is a known ligand for that target taken from PDB. The right-hand panel in each row is the same data averaged into bins with ±SD error bars.

**The trend**

The red trend line slopes downward in all three cases — confirming that on average, a compound with a higher Chai-1 score tends to bind more tightly. This is the justification for using Chai-1 as the scoring oracle. However, the left-hand scatter panels make the limitation immediately visible: at any fixed Chai-1 score, binding affinities span several kcal/mol. A compound scoring 0.85 might be a 5 nM binder or a 1 µM binder. The score is a guide for a *search*, not a prediction of a specific compound's potency.

**The Autodock comparison**

The paper also tested Autodock Vina as an alternative scoring function (Figure 7 in supplementary). Autodock Vina did not correlate better with affinity than Chai-1, and in practice produced worse de novo compounds. This is why Chai-1 was retained as the primary oracle.

**Anticipated question**: "If the scatter is so large, how can you trust Chai-1 to find binders?" Answer: you don't need to trust individual scores — you just need the search to move uphill on average. Even a noisy uphill gradient leads somewhere useful over thousands of steps.

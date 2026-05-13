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

# Table 2 — Generated Compound Scores

*Three independent MC runs on each of four targets all produce drug-like compounds with high Chai-1 scores and favourable MMGBSA and Boltz-2 binding estimates.*

- Columns: SMILES | composite score | Chai-1 | SA | ESOL | QED | MMGBSA (kcal/mol) | Boltz-2 (kcal/mol)
- Chai-1 range across all 12 compounds: 0.837–0.919 — consistently high
- MMGBSA range: −19 kcal/mol (bromodomain run 3) to −57 kcal/mol (β-1 receptor run 3) — broad but uniformly favourable
- Three independent runs per target give structurally different SMILES but similarly good scores — confirming the result is not a single lucky run

[[prj.home/OriginalPaper.Structural.GPCR.DeNovoProteinLigand.btag027.pdf#page=7|Table 2, p. 7 — generated compound scores]]
*Table 2 — final generated compounds (SMILES) and scores for the atomistic-step MC method across four targets (p. 7).*

# Presenter Notes

**How to read Table 2**

Orient the audience to the columns before showing the numbers. "Score" is the composite value used during the MC run (equation 2). The next four columns are its components: Chai-1 (the binding signal), SA (synthesis complexity — lower = easier), ESOL (solubility — more negative = more soluble), QED (drug-likeness, 0–1). The last two columns, MMGBSA and Boltz-2, are post-hoc validation scores computed independently of Chai-1.

**What the numbers show**

For the bromodomain, the three runs give Chai-1 scores of 0.902, 0.919, and 0.918 — very close to each other, suggesting the search reproducibly finds the same high-scoring region. MMGBSA values are −27, −30, and −19 kcal/mol — the third run is less favourable, but still in a range associated with real binding.

For p38 kinase, Chai-1 scores are slightly lower (0.874–0.877) and MMGBSA values deeper (−34 to −42 kcal/mol). For Pim-1 kinase, MMGBSA reaches −32 to −34 kcal/mol. For the β-1 adrenergic receptor (the GPCR), one run achieves −57 kcal/mol MMGBSA — unusually favourable, consistent with the highly flexible pocket that Chai-1 can model well.

**The key argument**

These numbers mean nothing in isolation. Their significance comes from comparison with Table 3 — the experimental binders. Point forward to that comparison: "The question is whether these values are in the range we expect for real drugs. We'll see that they are."

**Diversity across runs**

A subtle but important point: the three SMILES strings for each target are chemically different (look at the bromodomain entries — they share some features but are distinct molecules). This is the stochastic nature of MC at work. The simulation does not converge to a single global optimum; it finds different valid solutions each time. In practice this is useful: you get three candidate structures, all good, all different, giving medicinal chemistry more to work with.

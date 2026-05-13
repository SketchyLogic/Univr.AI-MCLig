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

# De Novo Generation Results — part 1: Generated Compounds Score Like Known Binders

*The final compounds produced by AI-MCLig have MMGBSA and Boltz-2 scores in the same range as experimentally validated binders for all four targets.*

- Chai-1 scores for generated compounds: 0.85–0.92 (Table 2, p. 7)
- [[MMGBSA]] scores: −19 to −57 kcal/mol — typical range for high-affinity binders
- Boltz-2 predicted IC50: consistently favourable, agreeing with Chai-1
- Table 3 (p. 8) shows experimental binders for the same targets: MMGBSA −20 to −47 kcal/mol — same ballpark

[[prj.home/OriginalPaper.Structural.GPCR.DeNovoProteinLigand.btag027.pdf#page=7|Table 2, p. 7 — generated compound scores]]
*Table 2, p. 7 — AI-MCLig output compounds with Chai-1, SA, ESOL, QED, MMGBSA, and Boltz-2 scores.*

[[prj.home/OriginalPaper.Structural.GPCR.DeNovoProteinLigand.btag027.pdf#page=8|Table 3, p. 8 — experimental binder reference scores]]
*Table 3, p. 8 — experimentally validated binders for the same four targets, for direct comparison.*

# De Novo Generation Results — part 2: Protein Flexibility Is Captured

*The binding pocket moves during simulation — more for kinases and the GPCR than for the rigid bromodomain — matching known experimental biology.*

- Figure 3C: pocket RMSD of non-hydrogen atoms within 5 Å of the native ligand, over 2000 MC steps
- Bromodomain: low, stable RMSD — as expected for a rigid reading-domain pocket
- Pim-1 kinase and p38 kinase: higher and more variable RMSD — consistent with experimentally observed conformational variability
- β-1 adrenergic receptor (GPCR): flexible 7-helix bundle, larger pocket rearrangements
- Score improvement trajectory (Figure 3A): rapid gain in first 250 steps, gradual improvement thereafter

![[prj.home/OriginalPaper.Structural.GPCR.DeNovoProteinLigand.btag027.pdf#page=9]]
*Figure 3 — (A) average Chai-1 score trajectory; (B) MMGBSA score for first 200 steps; (C) pocket RMSD over 2000 steps, for bromodomain, p38, and Pim-1 (p. 9).*

# Presenter Notes

**Part 1 — Tables 2 and 3**

Show Table 2 and Table 3 side by side, or point to them sequentially. The argument is straightforward: the compounds AI-MCLig produces — entirely novel structures, never seen before — score just as well as the known drugs that required years of medicinal chemistry to discover. Chai-1 scores above 0.85 are consistently achieved. MMGBSA scores in the −20 to −57 kcal/mol range are in line with high-affinity binders from the literature. Boltz-2 — an independent AI affinity model the authors use as a second opinion — agrees. None of this proves the generated compounds will work in a cell or in an animal. But it is strong computational evidence that they are worth taking to a lab.

**Part 2 — Figure 3 and protein flexibility**

Figure 3C is the key evidence that protein flexibility is genuinely being modelled, not just claimed. For the bromodomain — a small, rigid reading domain — the pocket barely moves (RMSD consistently below 0.5 Å). For the kinases and the GPCR, the pocket RMSD fluctuates up to 1–2 Å during the simulation. This is not noise; it is the protein responding to each new candidate ligand. It matches the experimentally known biology: the bromodomain is a lock that barely flexes; kinases and GPCRs are famously dynamic. AI-MCLig captures this difference automatically, with no special configuration per target. Figure 3A shows the score trajectory: a rapid early rise as obvious improvements are found, then slower progress as the search refines details. This is exactly what you want from a [[Monte Carlo simulation]] — fast initial convergence, then fine-tuning.

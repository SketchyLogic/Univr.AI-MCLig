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

# De Novo Generation — Atomistic-Step MC — part 1

*From benzene to a drug-like compound in 2 000 steps: AI-MCLig generates novel ligands whose MMGBSA and Boltz-2 scores match experimentally known binders.*

- Applied to four targets: bromodomain, [[p38 kinase]], [[Pim-1 kinase]], β-1 adrenergic receptor (GPCR)
- 2 000 MC steps, $\beta = 50$, starting from benzene; **3 independent runs per target** (~20 h each on NVIDIA RTX4090)
- Final Chai-1 scores: **0.85–0.92** across all 12 compounds (Table 2)
- MMGBSA scores: **−19 to −57 kcal/mol** — in the same range as experimentally validated high-affinity binders (Table 3)

[[prj.home/OriginalPaper.Structural.GPCR.DeNovoProteinLigand.btag027.pdf#page=6]]
*Figure 2 — Ligand evolution over MC steps for the bromodomain (A–I) and Pim-1 kinase (J–L). (A–F) Stick model at 0, 20, 40, 60, 80, 100 steps. (G–I) Van der Waals spheres at step 0 (benzene), 200, and 2 000. Note the ligand fills the pocket by step 2 000 (p. 6).*

# De Novo Generation — Atomistic-Step MC — part 2

*The score trajectory rises fast then plateaus; protein pocket RMSD reveals target-specific flexibility — confirming the method handles both rigid and flexible pockets.*

- Score improves rapidly in the **first 250 steps**, then more gradually (Figure 3A)
- Pocket RMSD (Figure 3C): **bromodomain** stays low (~0.5 Å) — rigid pocket; **p38 kinase** and **Pim-1 kinase** show larger fluctuations (0.5–2.0 Å) — flexible pockets adapting to each candidate ligand
- Boltz-2 independent validation (Table 2): assigns favourable predicted binding scores to all 12 generated compounds — consistent with Chai-1 results
- Cleanup phase: 100 additional steps optimising SA, ESOL, QED with Chai-1 threshold constraint — improves drug-likeness without losing binding score

[[prj.home/OriginalPaper.Structural.GPCR.DeNovoProteinLigand.btag027.pdf#page=9]]
*Figure 3 — (A) Average score per 50 steps for bromodomain, p38, and Pim-1 kinase. (B) MMGBSA score for first 200 steps. (C) Pocket RMSD vs. MC step — bromodomain stays rigid; kinases show larger conformational variation (p. 9).*

# Presenter Notes

**Part 1 — The Main Result**

This is the centrepiece of the paper. Show Figure 2. Panel A–F shows the ligand at MC steps 0, 20, 40, 60, 80, and 100 as a stick model inside the bromodomain. At step 0 it is just benzene — a tiny six-carbon ring floating in the pocket. By step 100 it has grown substituents. By step 2 000 (panel I), the ligand fills the pocket snugly. Panels G–I show van der Waals surface spheres — the growth in molecular volume is striking. Panels J–L repeat the same view for Pim-1 kinase.

Now point to Table 2. Three independent runs were performed for each of the four proteins, giving 12 compounds total. All 12 have Chai-1 scores between 0.85 and 0.92, SA scores around 4–5 (moderate complexity), and MMGBSA scores between −19 and −57 kcal/mol. Then show Table 3: the experimental binders for the same targets, with MMGBSA scores calculated the same way, are in the range −20 to −47 kcal/mol. The ranges overlap — the generated compounds are scoring like known drugs.

**Part 2 — Score Trajectory and Pocket Flexibility**

Figure 3A is critical. The score starts around 0.4–0.5 (benzene has low confidence as a binder) and rises rapidly in the first 250 steps — this is when the molecule is first discovering what the pocket wants. After 250 steps, improvements come more gradually as the molecule is already a good fit and finding ways to improve further is harder.

Figure 3C is the protein flexibility result the paper's title promises. For the bromodomain (blue), the pocket RMSD stays below 0.5 Å throughout — this target has a rigid, well-defined pocket. For p38 kinase and Pim-1 kinase (orange and green), the RMSD fluctuates between 0.5 and 2 Å, tracking the conformational adaptation of the pocket around each new candidate ligand. This happens automatically, with no special treatment — because Chai-1 rebuilds the complex from scratch at every step.

Likely question: "Are the three runs' outputs similar or diverse?" They are structurally diverse — three different molecules, all with good scores. This is consistent with the known observation that many chemically distinct ligands can bind the same pocket. UMAP analysis in supplementary Figure 14 confirms the generated compounds are close to clusters of known binders in chemical space, despite not resembling each other.

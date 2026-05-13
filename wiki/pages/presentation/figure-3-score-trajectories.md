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

# Figure 3 — Score Trajectories and Pocket Flexibility

*The simulation converges rapidly in the first 250 steps, then refines gradually — and the protein pocket moves more for flexible targets than rigid ones.*

- Panel A (Chai-1 score, 2000 steps): all three targets start ~0.4–0.6, rise sharply to ~0.75–0.85 within the first 250 steps, then plateau and improve only gradually; bromodomain plateaus highest (~0.90)
- Panel B (MMGBSA, first 200 steps): three independent runs per target; values improve rapidly from ~−10 to −25 kcal/mol in ~50 steps, then fluctuate; confirms Chai-1 trend with an independent metric
- Panel C (Pocket RMSD, 2000 steps): bromodomain stays consistently below ~0.5 Å; p38 and Pim-1 fluctuate up to ~1–2 Å — protein flexibility is captured automatically per target

![[prj.home/OriginalPaper.Structural.GPCR.DeNovoProteinLigand.btag027.pdf#page=9]]
*Figure 3 — (A) Average score per 50 steps (100-step running window) for one design run per target. (B) MMGBSA score for the first 200 MC steps of three runs per target. (C) Root-mean-square deviation (RMSD) of non-hydrogen pocket atoms (within 5 Å of native ligand) over 2000 steps (p. 9).*

# Presenter Notes

**Panel A — convergence behaviour**

The score trajectory in panel A is encouraging for two reasons. First, the rapid early improvement (first 250 steps) shows that the MC search finds a useful region of chemical space quickly — benzene is a reasonable starting point and the algorithm escapes it fast. Second, the gradual improvement thereafter shows that the search is not just stuck in a local trap: it continues to refine the compound across the full 2000 steps. An audience member might ask: "why not run longer?" The authors ran 2000 steps partly due to compute constraints (~20 h per run), and partly because the marginal improvement after step 1000 is small.

**Panel B — MMGBSA as an independent check**

MMGBSA is a physics-based scoring method (molecular dynamics + continuum solvent model) that is entirely independent of Chai-1. The fact that MMGBSA scores also improve rapidly in the first 200 steps, and reach the −20 to −40 kcal/mol range, validates that the Chai-1-guided search is genuinely finding better-binding compounds — not just finding structures that happen to look good to Chai-1 while being physically unreasonable.

**Panel C — the flexibility story**

This panel is worth highlighting explicitly. The bromodomain line stays flat and low — the pocket barely moves during the simulation. The bromodomain is a small, rigid reading domain, and this matches its experimental behaviour: different ligands bind to it with very similar protein conformations. The kinase lines (p38, Pim-1) fluctuate more — 0.5–2.0 Å RMSD. This is also consistent with experimental data: kinases are known to adopt different conformations with different inhibitors (DFG-in vs. DFG-out, etc.). AI-MCLig captures this target-dependent flexibility automatically, without any special configuration, simply because Chai-1 re-predicts the complex from scratch at every step.

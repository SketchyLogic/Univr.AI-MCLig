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

# Correlation of Chai-1 Confidence Score and Ligand Binding Affinity

*Chai-1 pLDDT is not a direct affinity measurement — but its average trend is positive, and that is enough to guide a Monte Carlo search uphill.*

- Three protein targets evaluated: bromodomain, [[Pim-1 kinase]], [[p38 kinase]]
- Experimental binding affinities (kcal/mol) from PDBbind (Liu et al. 2017) compared to Chai-1 pLDDT scores
- **Key result**: no direct point-to-point correlation; but the **average trend** across binned Chai-1 scores is consistently positive for all three targets

[[prj.home/OriginalPaper.Structural.GPCR.DeNovoProteinLigand.btag027.pdf#page=5]]
*Figure 1 — Chai-1 confidence score vs. experimental binding affinity (kcal/mol) for (A) bromodomain, (B) Pim-1 kinase, (C) p38 kinase. Left panels: individual data points (large scatter). Right panels: binned averages ± SD. Fit lines: A: y = −7.80x + 0.74; B: y = −14.20x + 3.91; C: y = −9.95x + 1.06 (p. 5).*

> [!caution] The oracle is imperfect — and this is the main bottleneck
> A single molecule with a high Chai-1 score may have weak experimental affinity. The method works statistically across many compounds, not deterministically on individual ones. This is the primary limitation the authors acknowledge.

# Presenter Notes

**Reading Figure 1**

Show Figure 1 and walk through it systematically. Each row (A, B, C) is a different protein target. The left panel for each shows individual data points — each point is a known ligand with a measured binding affinity (y-axis, in kcal/mol; more negative = tighter binding) and a Chai-1 pLDDT score (x-axis). The scatter is large — you can find high-Chai-1 molecules with weak affinity, and low-Chai-1 molecules with strong affinity.

The right panel is the key: it bins the Chai-1 scores into intervals and plots the average affinity ± standard deviation within each bin. The red line is the linear fit. For all three proteins, the slope is negative — meaning higher Chai-1 score corresponds, on average, to more negative (tighter) binding affinity. For Pim-1 kinase, the slope is steepest (−14.20 kcal/mol per unit Chai-1), suggesting Chai-1 is most informative there.

**The critical argument: good enough to guide a search**

The authors make a specific and important claim: they are not saying Chai-1 measures affinity accurately. They are saying it is a *good enough oracle* to walk uphill in chemical space. A Monte Carlo search doesn't need a perfect score function — it only needs one where better scores are more likely to correspond to better molecules than worse scores. That is what Figure 1 demonstrates. ^65a0bf

**AutoDock Vina comparison (mentioned in text, p. 4)**

The authors also tested AutoDock Vina as an alternative oracle. Its correlation with experimental affinity was similar to Chai-1's and did not improve when combined with Chai-1. This rules out the obvious follow-up ("just use docking") and justifies the choice of Chai-1 as the primary scoring function.

Likely question: "Will better AI models fix this?" Yes — and the authors explicitly say so. As structure predictors improve, the pLDDT-affinity correlation will sharpen, and the quality of the AI-MCLig search will improve proportionally without changing any other part of the pipeline.

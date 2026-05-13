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

# AI Structure Prediction as a Scoring Oracle

*[[Chai-1]] rebuilds the entire protein–ligand complex from scratch at every step — making protein flexibility free.*

- Input: protein amino acid sequence + ligand [[SMILES]] string
- Output: full 3D complex + [[pLDDT]] confidence score (0–1)
- Key property: because the complex is predicted anew each time, the protein pocket adapts to each candidate ligand
- Practical use here: higher [[pLDDT]] → on average better binding affinity (Figure 1)

![[prj.home/OriginalPaper.Structural.GPCR.DeNovoProteinLigand.btag027.pdf#page=5]]
*Figure 1 — Chai-1 confidence score vs. experimental binding affinity for bromodomain (A), Pim-1 kinase (B), and p38 kinase (C) ligands (p. 5). Average trend is positive; individual scatter is large.*

> [!caution] The scoring is imperfect
> Figure 1 shows a positive average trend but substantial scatter. Chai-1 confidence is a proxy for affinity, not a direct measure. The authors acknowledge this as the main bottleneck of the approach.

# Presenter Notes

**Part 1 — The Oracle Idea**

The key insight of the paper is the repurposing of an AI structure predictor as a scoring oracle. Chai-1 — like AlphaFold3 — takes two inputs: the amino acid sequence of a protein and the SMILES string of a small molecule. Within a few seconds it outputs a predicted 3D structure of the complex, together with a confidence score called pLDDT. Normally, pLDDT measures how reliable the structure prediction is. But the authors notice something useful: on average, a higher pLDDT score corresponds to a more stable, tightly bound complex. Crucially, because Chai-1 predicts the structure from scratch each time, it implicitly repositions the protein residues around each new ligand — the pocket is free to adapt. Protein flexibility comes for free, just by calling the predictor.

**Addressing the scatter in Figure 1**

Show Figure 1. Point out the trend line, then immediately point out the scatter. A single data point with a high Chai-1 score might have a mediocre experimental affinity. The authors are transparent about this: Chai-1 confidence is an imperfect oracle. But it is good enough to guide a search — you don't need a perfect score to walk uphill in chemical space. As AI predictors improve, so will the quality of this oracle.

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

# Scoring of Compounds During MC Simulations

*The score is not just Chai-1 — three drug-property biases steer the search toward synthesisable, soluble, drug-like molecules.*

- Final composite score (Equation 2, p. 3):

$$\text{score} = 0.8 \cdot \text{Chai-1} - 0.1 \cdot \text{SA} + 0.05 \cdot \text{ESOL} + 0.05 \cdot \text{QED}$$

- **Chai-1** (weight 0.8): [[pLDDT]] confidence of the predicted complex — proxy for binding affinity
- **SA** (weight −0.1): [[Synthetic accessibility score]] — penalises hard-to-synthesise molecules; capped at a maximum threshold (a ligand must be complex enough to fill the pocket)
- **ESOL** (weight +0.05): estimated aqueous solubility (Delaney 2004) — positive contribution favours water-soluble compounds
- **QED** (weight +0.05): quantitative estimate of drug-likeness (Bickerton et al. 2012, without MW factor) — favours Lipinski-compliant molecules

[[prj.home/OriginalPaper.Structural.GPCR.DeNovoProteinLigand.btag027.pdf#page=3]]
*p. 3 — Equation (2) and justification for each term's contribution.*

# Presenter Notes

**Why not just Chai-1?**

Chai-1 alone would happily suggest molecules that bind tightly but are impossible to synthesise, insoluble in water, or too large to be orally bioavailable. The three auxiliary terms are guards against these failure modes.

Walk through each term with its rationale:

**SA (synthetic accessibility)**: The SA score from Ertl & Schuffenhauer 2009 quantifies how easy a molecule is to make in the lab, on a scale from 1 (easy) to 10 (very hard). It is subtracted — higher SA means harder to synthesise, so it lowers the total score. Importantly, there is a threshold below which SA is capped: the method does not penalise molecules for being complex up to a point, because some complexity is necessary to fill a protein pocket. The weight of 0.1 is small enough that it never overrides a large Chai-1 improvement.

**ESOL (solubility)**: Predicted aqueous solubility. A molecule must dissolve in water to be useful as a drug. Small positive contribution (0.05) nudges the search away from highly hydrophobic compounds.

**QED (drug-likeness)**: A composite score combining molecular weight, logP, hydrogen bond donors/acceptors, polar surface area, and several other Lipinski-related properties. The molecular weight factor is excluded because the search starts from benzene and weight grows naturally; penalising it early would stall the search.

**The weights were empirically chosen**: the authors found that smaller Chai-1 contributions (e.g. 0.5) often failed to maximize this score. 0.8 was found to work consistently across all four tested targets.

Likely question: " " Technically yes, but incorporating it in the score ensures the search trajectory itself stays in drug-like space — avoiding wasted steps exploring regions the filter would discard.

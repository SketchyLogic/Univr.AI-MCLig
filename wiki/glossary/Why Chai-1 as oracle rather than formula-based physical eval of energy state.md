---
tags:
  - __CONCEPT
CreatedAt: 2026-05-14
LastUpdateAt: 2026-05-14
LastReviewAt: null
ReviewerIds:
  - admin
OwnerIds:
  - admin
IssueNotes: null
GeneratedBy: claude-sonnet-4-6
readOrderIndex: 2
---

# Why Chai-1 as oracle rather than formula-based physical eval of energy state?

The AI-MCLig pipeline calls [[Chai-1]] at every Monte Carlo step instead of a classical physics-based scoring function. This is a deliberate architectural choice driven by two bottlenecks that physics-based methods hit hard: **speed** and **protein flexibility**.

## What the alternative would look like

A *formula-based physical evaluation* means computing the free energy of the protein–ligand complex using molecular mechanics (MM): summing bond stretches, angle bends, torsion potentials, van der Waals contacts, and electrostatics. More rigorous variants (MM-GBSA, MM-PBSA, free energy perturbation) layer an implicit or explicit solvent model on top and use short molecular dynamics (MD) trajectories to sample conformational space.

These methods are physically well-grounded — they derive from force fields (e.g. AMBER, CHARMM, OPLS) that approximate quantum mechanical interactions with classical equations.

## Why they break down in a Monte Carlo search loop

| Property                      | Classical physics / MM-GBSA                                             | Chai-1                                                              |
| ----------------------------- | ----------------------------------------------------------------------- | ------------------------------------------------------------------- |
| **Time per evaluation**       | Seconds to hours (MD needed for accuracy)                               | ~5–15 seconds                                                       |
| **Protein flexibility**       | Requires explicit MD or ensemble docking; expensive                     | Free — structure is re-predicted from scratch each call             |
| **New ligand scaffolds**      | Need atom-type parameters; may not be in force field                    | Input is a SMILES string; model generalises across scaffolds        |
| **Correlation with affinity** | Good when calibrated on similar ligands; fails on novel scaffolds       | Moderate overall; correlates with $K_d$ on average (Figure 1, p. 5) |
| **Implementation cost**       | Significant: force field assignment, protonation, MD setup per molecule | Single API call; protein sequence + SMILES in, pLDDT out            |

A Monte Carlo run in AI-MCLig makes thousands of scoring calls. Even a fast 30-second MM evaluation per step would make a 1000-step run cost ~8 hours. Chai-1 reduces that to minutes.

## The flexibility argument is the deeper one

Classical docking fixes the protein in a single conformation (or samples a small pre-computed ensemble). Any ligand that would require the pocket to rearrange is penalised or missed entirely. ==This is the *induced fit* problem. Running MD to relax the pocket around each candidate ligand is theoretically correct but prohibitively expensive in a generative loop.==

Chai-1 sidesteps the problem structurally: because it predicts the full complex from the amino acid sequence each time, it produces a pocket geometry that is specific to the current ligand candidate. Protein flexibility is encoded implicitly in the model weights learned from millions of structural data points — no extra simulation required.

> [!caution] The oracle is imperfect
> The Chai-1 pLDDT score is a *proxy* for affinity, not a rigorous free energy. Figure 1 shows the positive average trend is noisy — a single molecule with high pLDDT can have weak experimental affinity. Classical methods with careful calibration can be more precise on well-characterised targets; Chai-1 trades precision for speed and generality.

## Why this matters for the paper's argument

==The authors are not claiming Chai-1 is better than MM-GBSA at *measuring* binding energy. They are claiming it is good enough to *guide a search* — you only need the oracle to rank candidates correctly often enough that the Monte Carlo walk drifts uphill.== As foundation models improve, the oracle quality improves with them, and the computational cost of the search stays constant.

## Related pages

- [[Score change Δs]]
- [[Metropolis acceptance criterion]]
- [[Monte Carlo simulation]]
- [[Scoring formula (AI-MCLig)]]

## Other sources

- Chai-1 technical report: https://www.chaidiscovery.com/blog/introducing-chai-1
- Shoichet, B.K. (2004) "Virtual screening of chemical libraries", *Nature* 432 — context on classical docking scoring functions
- Wang et al. (2019) "End-Point Binding Free Energy Calculation with MM/PBSA and MM/GBSA" — review of physics-based alternatives

## Test yourself

- Name two reasons why running MM-GBSA at every MC step is impractical.
- What input does Chai-1 need, and what does it output?
- Why does re-predicting the full complex handle protein flexibility "for free"?
- What would it mean for the search if the oracle were only weakly correlated with affinity? When would the search still succeed?

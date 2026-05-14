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

# MC Simulation Approach — part 1

*AI-MCLig grows a ligand from benzene by applying random chemical operations, accepting or rejecting each change based on how much it improves the Chai-1 score.*

- Start from benzene (the simplest aromatic scaffold) as the initial [[SMILES]]
- At each step: apply one of 9 chemical operations, chosen with tuned probabilities (via [[RDKit]])
- Rebuild the full protein–ligand complex with [[Chai-1]] from scratch → get new [[pLDDT]] score
- Accept/reject using the [[Metropolis acceptance criterion]]: $P(\Delta s) = \exp(-\beta \cdot \Delta s)$

[[prj.home/OriginalPaper.Structural.GPCR.DeNovoProteinLigand.btag027.pdf#page=2]]
*p. 2 — The 9 chemical operations and their standard probabilities used in the atomistic MC search.*

# MC Simulation Approach — part 2

*Nine chemical operations cover the full range of atom-level edits; their probabilities were empirically tuned on three protein targets.*

| Operation                           | Probability | Purpose             |
| ----------------------------------- | ----------- | ------------------- |
| Add small chemical group (CH₃, OH…) | 0.3         | Grow substituents   |
| Remove atom                         | 0.2         | Prune               |
| Change atom type                    | 0.1         | Alter polarity      |
| Add atom in chain                   | 0.15        | Extend backbone     |
| Change bond type (single↔double)    | 0.025       | Adjust unsaturation |
| Form ring                           | 0.025       | Add ring constraint |
| Break ring                          | 0.05        | Escape ring traps   |
| Turn ring aromatic                  | 0.05        | Stabilise aromatic  |
| Rearrange bonds                     | 0.1         | Escape local minima |

Two-stage protocol: **Stage 1** — 30 parallel simulations × 5 000 steps, sharing the best structure every 500 steps. **Stage 2** — single simulation × 10 000 steps, reset to best every 100 steps. $\beta = 50$ throughout for high selectivity.

# Presenter Notes

**Part 1 — The Search Loop**

Walk through the loop step by step. You start with benzene — just six carbons in a ring, the simplest possible aromatic molecule. At each Monte Carlo step, the algorithm picks one of nine chemical operations at random (with preset probabilities) and tries to apply it to the current molecule. The [[RDKit]] library enforces chemical validity — if the result is not a real molecule, the move is rejected outright. Otherwise, Chai-1 is called: it takes the protein amino acid sequence and the new ligand SMILES and returns a predicted 3D complex plus a pLDDT confidence score. The [[Score change Δs]] between old and new score determines acceptance via the Metropolis rule.

The $\beta$ parameter controls temperature. High $\beta$ (= 50 here) means the search is selective — it almost never accepts a worsening move. This is appropriate once the molecule has found a good pocket; you don't want to randomly destroy a good scaffold.

**Part 2 — The Nine Operations and the Two-Stage Protocol**

Each of the nine operations has a specific role. The "add small group" step (probability 0.3) is the workhorse — it grows substituents. "Remove atom" prunes back branches that don't help. "Change atom type" can turn a carbon into a nitrogen, changing hydrogen bonding. "Rearrange bonds" is the escape hatch from local minima — it reshuffles connectivity without changing atom count.

The two-stage protocol is the key engineering trick. Stage 1 runs 30 independent searches in parallel, sharing the current best molecule every 500 steps. This explores diverse regions of chemical space simultaneously. Stage 2 takes the best molecule found and refines it with a single longer run. Think of Stage 1 as a wide net cast across chemical space, and Stage 2 as a magnifying glass on the most promising catch.

Likely question: "Why benzene as start?" Because it is the simplest chemically valid aromatic compound and a realistic scaffold for drug-like molecules. Any more complex starting point would bias the search. 

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

# MC-Based Recovery of Target Ligands — part 1

*Proof-of-concept: replacing Chai-1 with dice similarity to a known ligand, the MC search rediscovers the target compound from benzene in most cases.*

- Validation strategy: swap the scoring oracle for **dice similarity** (atom-pair fingerprints, Carhart et al. 1985) to a known target compound — if the search works, it should recover the target
- Dice similarity = 1.0 means the MC-generated molecule is identical to the target; < 1.0 means stuck in a local minimum
- Starting point: benzene; targets taken from bromodomain ligands (PDBbind, Liu et al. 2017)
- Two-stage protocol: 30 parallel simulations × 5 000 steps (Stage 1) → single run × 10 000 steps (Stage 2); $\beta = 50$

[[prj.home/OriginalPaper.Structural.GPCR.DeNovoProteinLigand.btag027.pdf#page=4]]
*Table 1 — Target and recovered SMILES for atomistic MC; dice scores of 1.00 in most cases (p. 4).*

# MC-Based Recovery of Target Ligands — part 2

*Most targets are perfectly recovered; ring-heavy structures create local minima where the search gets stuck.*^072456

- **8 of 11** tested compounds: dice score = **1.00** — exact recovery
- **2 of 11**: dice score ≥ **0.905** — near-perfect, differing by one or two atoms at ring junctions
- **1 of 11**: dice score = **0.726** — a complex fused-ring compound (CCNC(=O)c…) where the ring closure step is hard to escape
- Root cause of failures: ring structures create **very rough score landscapes** with many local minima that require accepting a large score worsening to escape — the Metropolis rule with high $\beta$ rarely allows this
- Conclusion: the chemical search mechanism is correct; failure modes are in ring formation/breaking, not in the Metropolis framework

# Presenter Notes

**Part 1 — Why this validation matters**

Before asking "does Chai-1 guide the search to good binders?", the authors first ask a simpler question: "does the MC search mechanism work at all?" To test this in isolation, they replace Chai-1 with a score that has a known answer — dice similarity to a target ligand. If the mechanism is sound, the search should climb toward the target molecule and reach a dice score of 1.0. This decouples the quality of the oracle from the quality of the search.

Dice similarity is computed from atom-pair fingerprints — a bit vector encoding all pairs of atom types at given topological distances. Two identical molecules give a dice score of 1.0; structurally unrelated molecules give scores near 0. Starting from benzene (dice ≈ 0.1–0.3 against most drug-like targets), the search must traverse a very rough landscape.

**Part 2 — Reading Table 1**

Point to Table 1. The left column shows target SMILES; the right column shows the closest compound found. In 8 of 11 cases, the dice score is exactly 1.00 — the search literally reproduced the target molecule atom by atom. Two more are close (0.905, 0.907) — the final molecule differs by a single ring closure. Only one compound (dice = 0.726, a heavily fused polycyclic structure) proved genuinely difficult.

Explain the local minimum problem: forming a ring requires accepting two temporary worsening steps (adding a bond between two already-present atoms). With $\beta = 50$, the probability of accepting two successive worsening steps is extremely small. The search tends to find a non-ring analogue and stay there. This is a limitation of the move set, not of the Metropolis framework — a dedicated "ring-formation proposal" with adjusted acceptance probabilities could address it. ^baf092

Likely question: "Why not lower $\beta$ to escape local minima?" Lower $\beta$ means more exploration but less selectivity — the search would wander away from good structures as readily as it finds them. $\beta = 50$ was chosen as the empirically best balance. ^dd01c8

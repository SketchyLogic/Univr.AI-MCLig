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

# Validation: Can the Search Rediscover Known Compounds?

*When dice fingerprint similarity to a known ligand replaces Chai-1 as the score, the MC procedure reliably rebuilds the target compound from benzene.*

- Swap the scoring function: replace Chai-1 with [[RDKit]] atom-pair [[Dice Similarity vs Chai-1 Scoring|dice similarity]] to a known target ligand
- Start from benzene; run 2-stage protocol (30 parallel runs × 5000 steps, then single run × 10 000 steps)
- Table 1: 9 of 11 bromodomain targets recovered with dice score = 1.00; 2 complex ring compounds stuck at ~0.73–0.91
- Conclusion: the MC search engine works — failures are ligand complexity traps, not a flaw in the search logic

![[prj.home/OriginalPaper.Structural.GPCR.DeNovoProteinLigand.btag027.pdf#page=4]]
*Table 1 — dice similarity scores for MC recovery of 11 bromodomain target ligands (p. 4). Near-perfect recovery for 9/11 compounds.*

> [!caution] The two failures
> The two compounds that did not reach dice = 1.00 contain complex ring systems that create local minima in the discrete chemical graph. The paper notes this is a limitation of the move set, not of Chai-1. Rings are hard to escape once formed.

# Presenter Notes

**Part 1 — Why this test matters**

Before using Chai-1 as the oracle, the authors ask a simpler question: does the MC search engine even work? To test this, they replace the Chai-1 score with a pure chemical similarity metric — the dice fingerprint similarity between the current compound and a known target ligand. If the method works, it should be able to walk from benzene all the way to the known ligand in chemical space. This is a controlled test: the "right answer" is known in advance, and success means dice similarity = 1.00 (identical compound).

**Show Table 1**

Walk through the numbers. 9 out of 11 target ligands are perfectly recovered (dice = 1.00). The two failures — scores of 0.726 and 0.905 — involve complex polycyclic structures. The MC search got close but couldn't escape a nearby local minimum. This is an honest limitation: the ring-forming and ring-breaking operations are relatively rare (probability 0.025–0.05), so the search can get trapped in a ring configuration that resembles, but is not identical to, the target. The important takeaway: the search engine is validated. When it subsequently fails to reach a compound, the culprit is the scoring function (Chai-1 imprecision), not the search itself.

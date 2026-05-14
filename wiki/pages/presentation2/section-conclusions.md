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

# Conclusions

*AI-MCLig demonstrates that a simple Monte Carlo search scored by an AI structure predictor is sufficient to generate novel drug-like ligands with predicted binding energies comparable to known experimental binders.*

- **Central claim**: [[Chai-1]] used as a scoring oracle inside a [[Monte Carlo simulation]] loop enables *de novo* ligand generation with **full protein flexibility** — no rigid pocket assumption
- **Validated on 4 targets**: bromodomain, [[p38 kinase]], [[Pim-1 kinase]], β-1 adrenergic receptor (GPCR) — diverse target classes
- **MMGBSA and Boltz-2 scores** of generated compounds overlap with experimentally known high-affinity binders (Tables 2 & 3)
- **Complementary, not competing**: the approach supplements traditional docking and deep generative models; can be adapted to optimise side chains only, fix a scaffold, or use fragment recombination

[[prj.home/OriginalPaper.Structural.GPCR.DeNovoProteinLigand.btag027.pdf#page=10]]
*p. 10 — Conclusion section summarising the method's contributions and future directions.*

> [!caution] The bottleneck is the scoring oracle
> Chai-1 pLDDT correlates with affinity only on average — individual predictions can be misleading. As AI structure prediction improves, so does the quality of this approach without any change to the MC framework. The method's ceiling is set by the oracle.

# Presenter Notes

**Synthesising the argument**

Return to the opening analogy: designing a key for a lock that moves. The paper's answer is: use an AI model that sees both the key and the lock simultaneously — and let it rebuild the whole key-lock system from scratch at every attempt. The Monte Carlo loop handles the search; Chai-1 handles the physics; RDKit handles chemical validity; and three auxiliary terms (SA, ESOL, QED) keep the search in drug-like space.

**What was demonstrated**

Four things were shown: (1) the search mechanism works — it can recover known compounds when a perfect oracle is given. (2) Chai-1 is good enough as an oracle — its average correlation with affinity is sufficient to guide the search uphill. (3) The generated compounds pass an independent physics-based test — MMGBSA and Boltz-2 scores are in the range of known binders. (4) Protein flexibility is genuinely modelled — the pocket RMSD during simulation reflects the known flexibility profiles of each target.

**Honest limitations**

The authors are transparent about three limitations: compute cost (~20 h per run on a high-end GPU), the imperfect oracle (Chai-1 pLDDT scatter in Figure 1), and the SA score being an estimate (synthesisability must ultimately be confirmed by a chemist). None of these are unique to AI-MCLig — docking has the same oracle problem, and generative models have even larger compute costs at scale.

**The future trajectory**

The conclusion makes a prediction that is worth emphasising: this approach improves automatically as AI structure prediction improves. AlphaFold3 was already better than AlphaFold2; Chai-1 is better than AF3 for small molecules; Boltz-2 (used here for validation) may be better still. The MC framework and scoring formula remain unchanged — only the oracle is swapped. This is a significant architectural advantage over methods that are tightly coupled to a fixed model.

**Closing question for the audience**: "What would you use AI-MCLig for first — a target with known binders (use fragment MC) or a completely novel target with no known ligands (use atomistic MC)?" This prompts the audience to think about when each variant is appropriate, and ends the presentation on an interactive note.

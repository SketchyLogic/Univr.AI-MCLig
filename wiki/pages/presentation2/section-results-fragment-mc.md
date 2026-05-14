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

# Fragment-Based MC Simulations — Results

*Fragment-based MC recovers known ligands and generates novel compounds with comparable scores to the atomistic approach — and outperforms it when target-specific fragments are used.*

- **Recovery test**: known ligands decomposed via [[BRICS decomposition|BRICS]] and reassembled in 500 steps, $\beta = 5$ — all tested ligands recovered (though not always exactly, reflecting stochastic character)
- **Random fragments**: 50 fragments from ChEMBL, 1 000 steps — scores similar to atomistic MC; MMGBSA and Boltz-2 in the same range as Table 2
- **Target-specific fragments**: fragments from known high-scoring binders — **significantly higher average scores** than random fragments (supplementary Table 7)
- Score trajectory: starts ~0.7–0.8 (fragments already drug-like) and fluctuates with $\beta = 5$; MMGBSA shows similar trends to atomistic approach

[[prj.home/OriginalPaper.Structural.GPCR.DeNovoProteinLigand.btag027.pdf#page=9]]
*Figure 3 (panels A and B) — Score and MMGBSA trajectories for the fragment-based simulations show rapid initial improvement with larger fluctuations than the atomistic approach, consistent with the coarser step size and smaller β (p. 9).*

> [!caution] Fragment library is the limiting factor
> Randomly selected ChEMBL fragments may lack the chemical groups that complement the target pocket. Better performance requires target-specific fragments — which requires prior knowledge of the target's known binders.

# Presenter Notes

**How it differs from atomistic MC**

The fragment-based approach makes coarser moves — adding or removing an entire molecular fragment at once rather than one atom. This has two consequences. First, the score can change dramatically in a single step (a whole new chemical group enters or leaves the pocket). This is why $\beta = 5$ instead of 50 — a more exploratory regime to allow accepting temporary worsening moves. Second, the molecules generated tend to look more like known drugs from the start, because the BRICS fragments themselves come from drug-like molecules.

**The recovery test as internal validation**

Before testing de novo generation, the authors first ask: can the fragment MC search reconstruct a known ligand from its own pieces? The answer is yes in all cases — though the recovered compound is sometimes not exactly the target (a different fragment combination that yields a similarly scored structure). This is expected: multiple fragment combinations can fill the same pocket.

**Random vs. target-specific fragments**

This is the most practically important finding for the fragment approach. When fragments are drawn randomly from ChEMBL (a database of bioactive molecules broadly), the results match the atomistic approach. But when fragments are drawn specifically from known binders to the target protein, scores improve significantly. The interpretation: those fragments already encode complementary chemical groups — aromatic rings at specific positions, hydrogen bond donors/acceptors matching the pocket. Starting from them gives the search a much better prior.

**Complementarity to atomistic MC**

The two approaches are not alternatives — they target different use cases. Atomistic MC is maximally unbiased: you need only the protein sequence, nothing else. Fragment MC is most powerful when you have known binders available and want to explore their chemical neighbourhood. Together they cover the full spectrum from first-in-class design (no prior knowledge) to lead optimisation (existing active scaffold).

Likely question: "Could you combine both approaches?" Yes — one could alternate between atomistic and fragment moves in the same simulation, or use fragment moves early (to quickly reach a drug-like scaffold) and atomistic moves later (to fine-tune substituents). The paper does not test this but mentions it as a future direction.

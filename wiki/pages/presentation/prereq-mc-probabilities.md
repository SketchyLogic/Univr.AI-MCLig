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

# Background: MC Step Selection Probabilities

*At each MC step, one of nine chemical operations is chosen at random — with probabilities tuned empirically, not derived theoretically.*

- Add small group 30% · Remove atom 20% · Add atom in chain 15% · Change atom type 10% · Rearrange bonds 10% · Break ring 5% · Turn ring aromatic 5% · Change bond type 2.5% · Form ring 2.5%
- Skew toward atomic tweaks (75%) vs. ring operations (10%): small moves explore efficiently; ring changes are disruptive and destabilise the search if too frequent
- Chosen by trial-and-error on 3 targets (bromodomain, p38, Pim-1) — not proven globally optimal; may vary for other targets

[[prj.home/OriginalPaper.Structural.GPCR.DeNovoProteinLigand.btag027.pdf#page=2|MC simulation approach, p. 2 — step probabilities]]
*Methods, p. 2 — the nine chemical operations and their standard probabilities.*

> [!caution] Empirical, not optimal
> The authors explicitly note that the number of tested probability sets was limited by compute cost. The chosen set is the best *among those tested*, not guaranteed globally optimal. The paper acknowledges probabilities may need adjustment for other targets.

# Presenter Notes

**Why these probabilities matter**

The step probabilities determine how the simulation explores chemical space. They do not change *whether* the algorithm can reach a good solution — only *how efficiently* it gets there. Think of them as the gear ratios on a bicycle: the wrong gears don't stop you from climbing a hill, but the right ones make it much faster.

**The asymmetry between atom-level and ring-level operations**

Fifty per cent of steps are add/remove atom operations. Only 10% involve rings. This asymmetry reflects practical chemical intuition: atomic changes are small, reversible, and well-tolerated. Ring changes are structurally disruptive — forming or breaking a ring changes the scaffold of the molecule, not just a substituent. If the simulation spent 30% of its steps forming rings, it would destabilise every structure it built. The 2.5% probability for ring formation is also responsible for the two recovery failures in Table 1 — complex ring structures are hard to reach because the ring-forming move fires rarely.

**The 100-step reset**

Alongside the step probabilities, every 100 steps the simulation resets to the best structure found so far. This is a complementary safeguard: it ensures that an unlucky streak of accepted bad moves cannot permanently derail progress. The step probabilities govern exploration; the 100-step reset governs exploitation.

**How they were chosen**

The authors ran simulations with different probability combinations on three targets and measured which combination found good-scoring molecules fastest. This is hyperparameter tuning — the same process used to set learning rates in neural networks. It is pragmatic and standard. The honest caveat: this was done on only three targets, and the ideal probabilities for a new target (e.g., a GPCR with a very different pocket shape) might be different.

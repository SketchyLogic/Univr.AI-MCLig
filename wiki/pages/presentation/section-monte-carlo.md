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

# Monte Carlo Optimisation in Chemical Space

*A guided random walk: make a random chemical change, score it, keep it if it's better — or sometimes even if it's worse.*

- Chemical space is vast (~10^60 possible drug-like molecules) — exhaustive search is impossible
- [[Monte Carlo simulation]]: stochastic search that does not need gradients
- Acceptance rule: always accept improvements; accept worsening moves with probability $P(\Delta s) = \exp(-\beta \cdot \Delta s)$
- [[Beta parameter β]]: controls selectivity — high β → only accept near-improvements; low β → explore broadly

[[prj.home/OriginalPaper.Structural.GPCR.DeNovoProteinLigand.btag027.pdf#page=2|Equation (1), p. 2 — Metropolis acceptance criterion]]
*Equation (1), p. 2 — the [[Metropolis acceptance criterion]] that drives every MC step.*

> [!Example] The blind hiker analogy
> Imagine a hiker on a foggy mountain landscape, where altitude = binding score. The hiker cannot see the summit. They take a random step in any direction. If they go up, they always continue. If they go down, they flip a weighted coin — mostly they turn back, but occasionally they continue downhill to escape a local valley. Over thousands of steps, the hiker reliably reaches a high peak.

# Presenter Notes

**Part 1 — Why Monte Carlo?**

Before explaining AI-MCLig, it is worth pausing on why Monte Carlo is a sensible tool here. Chemical space — the set of all possible drug-like molecules — contains an estimated 10 to the power of 60 compounds. You cannot enumerate it. You cannot compute gradients over it because molecules are discrete graphs, not continuous vectors. Monte Carlo is ideal for this kind of search: it explores by making random moves, guided by a scoring function, without needing derivatives. The [[Metropolis acceptance criterion]] is the clever rule that makes it work: always accept improvements, but occasionally accept worsening moves too. That last part is critical — it lets the search escape local traps. The parameter β controls how willing the search is to accept bad moves. At high β the search is selective and converges fast; at low β it explores more broadly. The authors set β = 50 for the main Chai-1 scoring runs, which they found to give a reasonable acceptance rate of 0.1–0.2 per step.

**Addressing the analogy**

Use the blind hiker image explicitly if your audience is non-specialist. The key intuition is that uphill moves are always accepted and downhill moves are only sometimes accepted — so the hiker tends to climb while still being able to escape valleys. After thousands of steps, this simple rule reliably finds high-scoring compounds.

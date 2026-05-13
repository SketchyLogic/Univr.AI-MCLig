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

# Background: Monte Carlo Simulation

*A [[Monte Carlo simulation]] is a stochastic search method that finds good solutions in a vast space by making random moves — occasionally accepting worse moves to avoid getting permanently trapped.*

- Chemical space contains ~10^60 drug-like molecules — exhaustive search is impossible; gradients don't exist over discrete molecular graphs
- MC strategy: make a random change → score the result → keep it if better; sometimes keep it even if worse
- The "sometimes accept worse" rule is what separates MC from greedy search — it lets the algorithm escape local traps

[[prj.home/OriginalPaper.Structural.GPCR.DeNovoProteinLigand.btag027.pdf#page=2|MC simulation approach, p. 2]]
*Methods, p. 2 — introduction of the MC approach for chemical-space search.*

> [!Example] The blind hiker
> A hiker on a foggy mountain landscape wants to reach the highest peak but cannot see it. They take random steps. If a step goes up, they always continue. If a step goes down, they usually turn back — but sometimes they continue anyway, to avoid being trapped on a local hilltop. Over thousands of steps, this rule reliably reaches a high point.

# Presenter Notes

**Why MC and not something simpler?**

Explain the search problem first: chemical space is not like a smooth mathematical function where you can compute a gradient and follow it uphill. Molecules are discrete objects — graphs of atoms and bonds. Adding a methyl group is not an "infinitesimal step"; it is a discrete jump to a completely different point in chemical space. This rules out gradient-based optimisation (neural network training, etc.). MC is ideal here: it only needs a score function, not a derivative.

**The key insight: accepting bad moves**

The single most important thing to convey is why occasionally accepting a worse move is essential. If the algorithm only accepted improvements, it would get stuck in the first decent molecule it found — a local maximum — and never explore better regions further away. The analogy that usually lands best: the blind hiker who sometimes walks downhill to escape a valley. This is the core idea, and it is simple enough that a biologist or clinician in the audience will follow it.

**The name "Monte Carlo"**

If the audience asks about the name: it was coined in 1949 by physicists (Metropolis, von Neumann) who named it after the Monte Carlo casino, because the algorithm is based on random sampling — like gambling. It was originally developed to simulate atomic behaviour, and has since been applied to everything from financial modelling to protein folding.

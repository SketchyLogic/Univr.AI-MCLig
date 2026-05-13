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

# Background: Metropolis Criterion, Δs, and β

*The [[Metropolis acceptance criterion]] is the rule that decides whether to keep each proposed chemical change — governed by two numbers: the score change Δs and the selectivity parameter β.*

- Formula: $P(\Delta s) = \exp(-\beta \cdot \Delta s)$
- [[Score change Δs]]: $\Delta s = s_\text{new} - s_\text{current}$; negative = improvement (always accepted); positive = worsening (sometimes accepted)
- [[Beta parameter β]]: high β (= 50 for atomistic MC) → very selective, rejects most bad moves; low β (= 5 for fragment MC) → permissive, needed because fragment swaps cause larger score jumps

![[prj.home/OriginalPaper.Structural.GPCR.DeNovoProteinLigand.btag027.pdf#page=2]]
*Equation (1), p. 2 — the Metropolis acceptance criterion as used in AI-MCLig.*

# Presenter Notes

**Teaching the formula step by step**

Show the equation: $P(\Delta s) = \exp(-\beta \cdot \Delta s)$. Work through the two cases.

Case 1: the new molecule scores better. $s_\text{new} > s_\text{current}$, so $\Delta s < 0$. Then $-\beta \cdot \Delta s > 0$, and $\exp$ of a positive number is $> 1$. A probability greater than 1 is impossible, so the rule caps it at 1 — the move is **always** accepted. No coin flip.

Case 2: the new molecule scores worse. $\Delta s > 0$. Then $-\beta \cdot \Delta s < 0$, and $\exp$ of a negative number is between 0 and 1. We generate a random number and accept the move if it falls below this probability. The bigger the worsening, the lower the probability — so small downhill moves are frequently accepted, but large ones are almost always rejected.

**Making β concrete**

Give the concrete example from the paper. β = 50, move worsens score by 0.02: $P = \exp(-50 \times 0.02) = \exp(-1) \approx 0.37$. That's a 37% chance of accepting a move that made things slightly worse. β = 50, move worsens by 0.1: $P = \exp(-5) \approx 0.007$ — less than 1%. At β = 50 the simulation is quite selective. For fragment MC, β = 5 is used because fragment swaps cause score changes of 0.2 or more — at β = 50 those would almost never be accepted, making the search impossible to move.

**The β-temperature analogy**

β is the inverse of temperature in statistical physics. High β = low temperature = the system is "cold" and settles into a stable state quickly. Low β = high temperature = the system is "hot" and bounces around freely. This analogy is helpful for physics-literate audiences. For others, stick to "selectivity knob": turning β up makes the search pickier.

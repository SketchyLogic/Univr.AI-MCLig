---
tags:
  - __CONCEPT
  - PREREQUISITE
CreatedAt: 2026-05-12
LastUpdateAt: 2026-05-12
LastReviewAt: null
ReviewerIds:
  - admin
OwnerIds:
  - admin
IssueNotes: null
GeneratedBy: claude-sonnet-4-6
readOrderIndex: -3
---

# Monte Carlo simulation

A **Monte Carlo (MC) simulation** is a way of searching a large space of possibilities by making random moves and deciding — step by step — whether to keep each move or throw it away.

The name comes from the Monte Carlo casino: just as a gambler tries many random bets hoping to win, the algorithm tries many random changes hoping to find a better answer.

## How it works (plain language)

Imagine you are lost in hilly terrain at night and want to find the lowest valley (the best solution). You cannot see the whole landscape, so you take random steps. After each step you check: am I lower than before? If yes, you stay. If no, you *might* still stay — because sometimes you need to climb a small hill to reach a deeper valley on the other side.

That "sometimes stay even when things got worse" rule is what makes MC powerful. Without it, the search gets trapped on the nearest hilltop and never finds better solutions far away.

## In AI-MCLig

The "landscape" is chemical space — an enormous number of possible drug-like molecules. At each MC step the algorithm:

1. Takes the current molecule and randomly modifies it (adds/removes an atom, changes a bond, etc.)
2. Evaluates the new molecule with a scoring function
3. Decides whether to keep the new molecule using the [[Metropolis acceptance criterion]]

This continues for thousands of steps, gradually nudging the molecule toward structures that score well as protein binders.

> [!Info] Why random moves?
> Molecules that bind well to a protein pocket are extraordinarily rare among all possible molecules. Pure random search would never find them. MC is smarter: it biases the random walk toward better-scoring regions while occasionally accepting worse moves to avoid getting stuck.

## Related pages

- [[Metropolis acceptance criterion]]
- [[Score change Δs]]
- [[Beta parameter β]]

## Other sources

- [Wikipedia — Monte Carlo method](https://en.wikipedia.org/wiki/Monte_Carlo_method)
- Metropolis *et al.* 1953 — the original paper introducing the algorithm in physics

## Test yourself

- In one sentence, what problem does Monte Carlo simulation solve that pure greedy search cannot?
- Why does the algorithm sometimes accept a *worse* move?

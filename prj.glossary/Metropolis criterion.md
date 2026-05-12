---
tags:
  - CONCEPT
  - PREREQUISITE
CreatedAt: 2026-05-11
LastUpdateAt: 2026-05-11
LastReviewAt:
ReviewerIds:
  - admin
OwnerIds:
  - admin
IssueNotes:
GeneratedBy: claude-sonnet-4-6
readOrderIndex: -3
---

# Metropolis criterion

The acceptance rule in a Monte Carlo simulation that determines whether a proposed random change is kept or discarded. Introduced by Metropolis et al. (1953), it allows the simulation to occasionally accept worse moves, preventing it from getting permanently trapped in local optima.

**Rule:**
- If the new state is **better** (higher score): **always accept** it
- If the new state is **worse** by amount Δs: **accept with probability** exp(−β·Δs)

where β is the "inverse temperature" controlling selectivity:
- High β (cold) → rarely accept worse states → selective, converges quickly to a local optimum
- Low β (hot) → frequently accept worse states → broad exploration but slow convergence

**In AI-MCLig:** β = 50 is used in the main de novo simulations, providing high selectivity (the simulation mostly climbs toward better-scoring compounds but occasionally accepts small decreases to escape local minima).

**Analogy:** Imagine hiring workers who always accept a pay raise, but will occasionally accept a pay cut — with probability that depends on how big the cut is and how "desperate" they are (the temperature). This prevents everyone from ending up stuck in a dead-end job with a slightly higher salary than any other local option.

## TLDR
The probabilistic acceptance rule in MC simulation: always accept improvements, occasionally accept worsening moves to escape local optima; controlled by the β (inverse temperature) parameter.


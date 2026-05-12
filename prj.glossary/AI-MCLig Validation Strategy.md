---
tags:
  - CONCEPT
CreatedAt: 2026-05-11
LastUpdateAt: 2026-05-11
LastReviewAt:
ReviewerIds:
  - admin
OwnerIds:
  - admin
IssueNotes:
GeneratedBy: claude-sonnet-4-6
readOrderIndex: 3
---

# AI-MCLig Validation Strategy

Before running the full de novo design protocol, the authors needed to confirm that the MC search mechanism can actually navigate chemical space effectively. They did this with a **recovery experiment**: replace the Chai-1 scoring function with a dice similarity score pointing at a *known* compound, then check whether the MC can rediscover it starting from benzene.

This isolates the search mechanism from the scoring function (see [[MC Search Components]]). If it fails here, the problem is the chemical move set — not Chai-1. If it succeeds, the search engine is validated and Chai-1 can be trusted to guide it toward unknown binders.

## Stage 1 — Parallel exploration

- **30 independent simulations** run simultaneously, each for **5000 steps**
- Every **500 steps**, all 30 simulations are reset to the single best structure found across the entire pool
- **Purpose:** broad coverage of chemical space; prevents any one run from getting stuck in a local minimum while others have found better ground

## Stage 2 — Focused refinement

- A **single simulation** runs for **10,000 steps**
- Reset to best structure every **100 steps**
- β = 50 (very high selectivity — worse moves are almost never accepted)
- **Purpose:** tight convergence on the best candidate; essentially hill-climbing

## What the result tells you

- **Dice score = 1.0** → the MC successfully reconstructed that exact known compound from benzene. Search works for that chemical complexity.
- **Dice score < 1.0** → the MC got close but got stuck. Most likely cause: ring assembly requires coordinated multi-step moves that the current move set handles poorly. See [[Ring Local Minima in MC]].

In Table 1 of the paper, most targets were recovered perfectly. Two ring-heavy compounds scored 0.905 and 0.726 — revealing a specific limitation of the ring-forming operations, not a flaw in the overall approach.

## TLDR

Replace Chai-1 with "how similar are you to this known compound?" — if the MC finds it, the search works. Done in two stages: Stage 1 casts a wide net with 30 parallel runs; Stage 2 converges tightly on the best candidate.

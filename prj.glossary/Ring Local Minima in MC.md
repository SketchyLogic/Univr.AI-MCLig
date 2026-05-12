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
readOrderIndex: 2
---

# Ring Local Minima in MC

In the AI-MCLig Monte Carlo framework, building ring structures (especially fused or polycyclic systems) is disproportionately difficult compared to adding acyclic substituents. This creates **local minima** that trap the simulation before it can assemble the ring.

## Why it happens — step by step

Forming a ring requires a *sequence* of coordinated moves:

1. Add chain atoms (building toward ring closure)
2. Close the ring (a specific bond-formation move)
3. Make it aromatic (a separate operation)

Each intermediate state — a dangling open chain — typically scores *worse* than the previous acyclic structure. The compound looks like a half-built ring, which is neither a compact acyclic molecule nor a closed ring, and scores poorly on all criteria.

The Metropolis criterion allows accepting worse moves with probability exp(−β·Δs). But with high β (high selectivity), this probability is very low. The simulation tends to backtrack before the ring is complete and settles on a "good enough" acyclic compound instead.

## What this means in practice

In the recovery experiment (Table 1 of the paper), two target compounds with complex polycyclic ring systems achieved dice scores of 0.905 and 0.726 — the MC got close but could not assemble the exact ring system. This is **not** a failure of the Chai-1 scoring function (it was not even in use during this test) — it is a limitation of how the 9 chemical move operations handle ring assembly.

## Potential solutions the paper suggests

- Smarter MC moves that are ring-aware
- Higher-level fragment operations (the fragment-based MC protocol partly addresses this by adding/removing whole pre-formed ring fragments at once)

## Analogy

Imagine building a Lego loop by only being allowed to add or remove one brick at a time. Every time you extend the chain toward closure, the half-built loop looks worse than the previous compact structure — so you keep tearing it down. Getting the loop closed requires accepting a lucky streak of bad-looking intermediate states.

## TLDR

Rings need multi-step coordinated moves. Intermediate open-chain states score poorly, so the MC tends to backtrack rather than complete the ring. Ring-heavy targets score below 1.0 in the recovery test — a limitation of the move set, not the scoring function.

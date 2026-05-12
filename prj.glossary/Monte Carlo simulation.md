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
readOrderIndex: -2
---

# Monte Carlo simulation

A broad class of computational algorithms that use repeated random sampling to explore a complex system. In the context of AI-MCLig, a Monte Carlo (MC) simulation navigates chemical space: starting from a simple molecule, it makes random modifications at each step and decides whether to accept or reject each change based on a score.

**The Metropolis criterion** governs acceptance:
- If the new state is better (higher score): always accept
- If the new state is worse: accept with probability exp(−β·Δs), where Δs is the score difference and β is an "inverse temperature" parameter

A high β means the simulation is selective (rarely accepts worse states); a low β means it explores broadly. This balance prevents getting permanently stuck in local optima while still converging toward good solutions.

**Why MC for drug design?**
- Chemical space is "rugged" — small changes in structure can cause large changes in binding score
- Gradient-based methods require differentiable scoring functions; MC does not
- MC is simple to implement and easy to customise with new move types
#Presentation

**Analogy:** Imagine a hiker searching for the highest hill in a foggy landscape. At each step they move randomly, but tend to go uphill. Occasionally they descend briefly to avoid being trapped on a small local hill — that's the Metropolis acceptance criterion.

## TLDR
A stochastic search algorithm that explores complex spaces by random moves, accepting better states always and worse states probabilistically; used in AI-MCLig to explore chemical space.


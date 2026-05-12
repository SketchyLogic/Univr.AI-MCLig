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
readOrderIndex: -1
---

# De novo drug design

The computational generation of entirely new chemical structures (molecules) from scratch, tailored to bind a specific protein target, rather than screening existing chemical databases or making small modifications to known drugs.

**Motivation:** Chemical space (the set of all possible drug-like molecules) is astronomically large (~10^60 compounds). It is impossible to synthesise and test them all experimentally. De novo design aims to navigate this space intelligently, guided by a scoring function that estimates how well a generated molecule binds the target.

**Approaches:**
- **Fragment-based buildup:** Grow a molecule piece-by-piece from small chemical fragments
- **Generative models:** Train neural networks (VAEs, GANs, diffusion models) on known drugs and sample new molecules
- **Monte Carlo search:** Randomly modify molecules and accept improvements (the AI-MCLig approach)

**Key challenge:** Any design method is only as good as its scoring function. If the score correlates poorly with real binding affinity, the generated molecules look good computationally but fail experimentally.

## TLDR
Computationally generating new drug-like molecules from scratch for a target protein; the challenge is having a scoring function that accurately predicts real binding.


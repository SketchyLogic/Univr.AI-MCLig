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

# MC Search Components

The Monte Carlo search in AI-MCLig is built from two independent components that can be understood, tested, and improved separately.

**Component 1 — The search mechanism (chemical move set)**
A fixed set of 9 chemical operations that define what changes are possible at each step:
- Add a small chemical group (e.g. CH3, OH)
- Remove an atom
- Change atom type
- Add an atom in a chain
- Change bond type (single ↔ double)
- Form a ring
- Break a ring
- Make a ring aromatic (or reverse)
- Rearrange bonds

Each operation has a tuned probability. Together they define *how* the molecule can move through chemical space.

**Component 2 — The scoring function**
Evaluates the molecule after each move and decides whether to accept or reject it. In normal operation this is the Chai-1 composite score (see [[Dice Similarity vs Chai-1 Scoring]]). In the validation experiment it is replaced with the dice similarity score.

**Why separating them matters**
If the generated compounds are bad, it could be the search getting stuck, the score being a poor proxy for binding, or both. By swapping in a verifiable score (dice similarity to a known target), the authors can test the search mechanism in isolation. See [[AI-MCLig Validation Strategy]] for how this test is structured.

## Analogy

Think of a blind hiker navigating foggy terrain. The hiker's legs (search mechanism) determine what moves are physically possible. The altimeter (scoring function) says whether the last step went uphill. To test whether the legs work, replace the altimeter with a GPS pointing to a known summit — if the hiker reaches it, the legs are fine. That is exactly what the recovery experiment does.

## TLDR

MC search = random chemical moves (search mechanism) guided by a score (scoring function). The two are separable and can be validated independently. This separation is the key design insight behind the recovery experiment.


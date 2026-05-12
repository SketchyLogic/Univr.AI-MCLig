---
tags:
  - PREREQUISITE
  - DEFINITION
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

# Synthetic accessibility score

**SA score** — a heuristic estimate (0–10 scale) of how easy or difficult it would be for a trained organic chemist to synthesise a given molecule in a laboratory. It was developed by Ertl and Schuffenhauer (2009) and is available in the RDKit cheminformatics library.

**How it works:** The score combines:
- The frequency of molecular fragments in a database of commercially available compounds (common fragments = easier synthesis)
- A complexity penalty based on ring systems, stereocentres, and unusual features

Score ~1 = very easy to synthesise; score ~10 = extremely complex/difficult.

**In AI-MCLig:** The SA score is included as a bias term in the MC scoring function (negative contribution, so the simulation is nudged toward more synthesisable compounds). Importantly, the SA score is capped: the authors recognise that a ligand needs some complexity to fit a binding pocket, so enforcing a very low SA score would be counterproductive.

**Limitation:** The SA score is only an estimate. It does not account for target-specific chemistry, reagent availability, or practical laboratory constraints. Actual synthesisability must be confirmed by a medicinal chemist.

## TLDR
A 0–10 estimate of how easy a molecule is to synthesise; used in AI-MCLig to bias the MC simulation away from impractically complex structures.
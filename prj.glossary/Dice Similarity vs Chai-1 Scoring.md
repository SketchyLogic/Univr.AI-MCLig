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
readOrderIndex: 1
---

# Dice Similarity vs Chai-1 Scoring

In AI-MCLig, the scoring function is the engine that guides the Monte Carlo search. Two fundamentally different scores are used for two different purposes.

## Dice similarity score

- Measures **chemical similarity** between the current compound and a *known* reference molecule
- Ranges from 0 (nothing in common) to 1 (identical)
- Calculated from **atom-pair fingerprints** — a compact binary encoding of which chemical substructures are present in the molecule
- Closely related to (and produces the same ranking as) [[Tanimoto similarity]], the default similarity metric in cheminformatics; Dice values are systematically higher than Tanimoto for the same pair
- **Requires knowing the answer in advance**: you must already have a known binder to use as the target
- Used only in the **validation (recovery) experiment**: the MC is given a target and asked to rediscover it from benzene. Success is unambiguous — dice = 1.0 means exact recovery.

## Chai-1 confidence score (+ biases)

- Predicts how well a compound fits a protein binding pocket, using an AI structure predictor that rebuilds the full complex at every step
- **Does not require any known binder** — scores are generated purely from the protein sequence and the candidate ligand SMILES
- Combined with three property biases to steer toward drug-like compounds:
  - SA (synthetic accessibility)
  - ESOL (estimated solubility)
  - QED (drug-likeness)
- Used in the actual **de novo design runs**: the MC searches blindly for good binders

## The key distinction

Using dice similarity is a **controlled experiment** — the answer is known, success is measurable, and you are testing the search mechanism in isolation. Using Chai-1 is the **real challenge** — the answer is unknown, and success depends on the score being a meaningful proxy for binding affinity (which it only loosely is, as Figure 1 of the paper shows).

The validation experiment builds confidence that the search engine works correctly. Then Chai-1 is trusted to guide it toward genuinely novel compounds where no known answer exists.

## Analogy

Dice similarity is like giving someone a photograph of a specific face and asking them to find that person in a crowd — success is clear and measurable. Chai-1 scoring is like asking them to find "the person most likely to be a good conversationalist" — the search process is the same, but the criterion is subjective and imperfect. You would test the first before trusting the second.

## TLDR

Dice = "how close are you to this known molecule?" (needs a known answer, used for validation). Chai-1 = "how well does this fit the protein?" (works without any known binder, used for real design). The first validates the search; the second runs it for real.


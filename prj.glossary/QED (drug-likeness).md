---
tags:
  - DEFINITION
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

# QED (drug-likeness)

**Quantitative Estimate of Drug-likeness** — a score between 0 and 1 that measures how closely a small molecule resembles known oral drugs. It combines multiple molecular properties (molecular weight, lipophilicity, hydrogen bond donors/acceptors, polar surface area, number of rotatable bonds, aromaticity, and absence of undesirable functional groups) into a single number using a desirability function framework.

A QED score close to 1.0 indicates a highly drug-like molecule; scores below 0.3–0.4 suggest poor drug-like properties. The score was developed by Bickerton et al. (2012) to go beyond the simpler Lipinski "Rule of Five" heuristic.

**In the context of drug design:** QED is used as a bias term in AI-MCLig's scoring function to steer the Monte Carlo simulation toward generating molecules with drug-appropriate properties. In EPoCS, QED is used to colour the Pocket Atlas map, showing that some binding site clusters are inherently associated with more drug-like ligands.

**Analogy:** Like a checklist for whether a molecule "looks" like a drug — not whether it actually works, but whether it has the right size, shape, and chemistry to be absorbed and tolerated by the body.

## TLDR
A 0–1 score measuring how drug-like a molecule looks, based on its physicochemical properties; used to guide generative design and assess compound quality.

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

# BRICS decomposition

**Breaking Retrosynthetically Interesting Chemical Substructures** — a method for decomposing drug-like molecules into chemically meaningful fragments by breaking bonds at specific "retrosynthetically interesting" positions. The positions are chosen based on organic chemistry rules for how bonds can be formed (and therefore broken) in synthesis.

**Purpose:**
- Fragment-based drug design: build new molecules by recombining known fragments
- Combinatorial library generation
- In AI-MCLig: decompose known ligands into fragments, then use those fragments as building blocks in the fragment-based MC simulation

**How it works:**
1. Identify bonds in the molecule that match BRICS bond-breaking rules (16 types)
2. Break those bonds, leaving attachment points (placeholders)
3. The resulting pieces are the BRICS fragments

**Advantage in AI-MCLig:** Using fragments from known active ligands (rather than random ChEMBL fragments) significantly improves the MC simulation's performance, because the fragments are already known to be compatible with the target protein family.

## TLDR
A rule-based method for cutting drug-like molecules into chemically sensible fragments; used in AI-MCLig's fragment-based MC protocol to recombine known ligand pieces.

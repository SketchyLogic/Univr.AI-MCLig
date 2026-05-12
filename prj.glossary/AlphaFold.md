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
readOrderIndex: -2
---

# AlphaFold

An AI system developed by DeepMind (Google) that predicts the 3D structure of a protein from its amino acid sequence with high accuracy. AlphaFold2 (2021) solved the long-standing "protein folding problem" — achieving experimental-level accuracy on most proteins — and its predictions for virtually all known protein sequences are freely available in the AlphaFold Protein Structure Database.

AlphaFold3 (2024) extended the approach to predict complexes of proteins with other proteins, DNA, RNA, and small-molecule ligands. Related tools include RoseTTAFold, ESMFold, Chai-1, and Boltz-1.

**How it works (simplified):** AlphaFold uses a transformer-based neural network (the Evoformer) trained on known protein structures and evolutionary sequence data (multiple sequence alignments). It outputs 3D coordinates for every atom and a confidence score (pLDDT) for each residue.

**Relevance to these papers:** pyGOMoDo can import AlphaFold models as input for docking. AI-MCLig uses Chai-1 (an AlphaFold successor) as its scoring oracle. The EPoCS paper compares its approach to ESMFold.

## TLDR
AI system that predicts protein 3D structure from sequence; solved the protein folding problem in 2021; now extended to protein-ligand complexes.

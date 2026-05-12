---
tags:
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
readOrderIndex: 0
---

# Chai-1

An AI system developed by the Chai Discovery team (2024) for predicting the 3D structures of biomolecular complexes, including protein-protein, protein-ligand, protein-DNA, and protein-RNA interactions. It uses a diffusion-based architecture inspired by AlphaFold3 and takes protein sequences + ligand SMILES as input.

**Key properties relevant to AI-MCLig:**
- Can predict a protein-ligand complex structure in seconds to minutes
- Produces a **pLDDT confidence score** for each residue and atom — a measure of how confident the model is in its prediction at that location; high pLDDT at the binding interface correlates loosely with binding affinity
- The complex is rebuilt *from scratch* at each input, so the protein pocket can adopt different conformations for different ligands — implicitly modelling protein flexibility

**In AI-MCLig:** Chai-1 is used as the on-the-fly scoring oracle. At each MC step, the new ligand SMILES is fed to Chai-1 together with the protein sequence; the resulting complex and Chai-1 confidence score determine whether the MC move is accepted.

**Freely available:** Code at https://github.com/chaidiscovery/chai-lab

## TLDR
AI system for predicting protein-ligand complex structures from sequence + SMILES; used in AI-MCLig as a flexible scoring oracle that implicitly models protein conformational adaptation.


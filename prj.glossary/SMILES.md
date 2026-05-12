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

# SMILES

**Simplified Molecular Input Line Entry System** — a text-based notation for representing the structure of a chemical molecule as a string of characters. Each atom type, bond type, ring closure, and stereochemistry is encoded by specific characters and rules.

**Examples:**
- Benzene: `c1ccccc1`
- Aspirin: `CC(=O)Oc1ccccc1C(=O)O`
- Ethanol: `CCO`

**Why SMILES matters in computational chemistry:**
- Compact: a complex drug molecule can be described in a single line of text
- Machine-readable: any cheminformatics library (e.g. RDKit) can parse SMILES and compute molecular properties, generate 3D coordinates, or modify the structure
- Required by AI structure predictors (Chai-1, AlphaFold3) as input alongside the protein sequence

**In AI-MCLig:** Each candidate ligand is represented as a SMILES string. The MC operations (adding atoms, forming bonds, changing atom types) directly manipulate the SMILES string. A new SMILES is fed to Chai-1 at each step to rebuild the protein-ligand complex.

## TLDR
A text format for representing chemical structures as strings; used as the standard way to input and modify molecules in cheminformatics and AI structure predictors.


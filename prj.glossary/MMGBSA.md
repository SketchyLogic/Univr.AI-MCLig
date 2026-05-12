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

# MMGBSA

**Molecular Mechanics Generalised Born Surface Area** — a method for estimating the free energy of binding between a ligand and a protein from a molecular dynamics (MD) simulation. It is more accurate than simple docking scores but far less expensive than rigorous alchemical free energy calculations.

**How it works:**
1. Run a short MD simulation of the protein-ligand complex in explicit water
2. For each snapshot of the trajectory, calculate the binding free energy as:
   ΔG_bind = G(complex) − G(protein) − G(ligand)
   where each term is approximated by the molecular mechanics energy + a solvation term (Generalised Born implicit solvent + surface area for hydrophobic effects)
3. Average over snapshots

**Interpretation:** More negative MMGBSA values indicate stronger binding. Values between −15 and −50 kcal/mol are typical for drug-like compounds binding their targets. In AI-MCLig, the generated compounds show MMGBSA values in the range −19 to −57 kcal/mol, comparable to experimentally known binders.

**Limitation:** MMGBSA is still an approximation; errors of ±5 kcal/mol are common. It is most reliable for relative rankings within a closely related series of compounds.

## TLDR
An MD-based method for estimating protein-ligand binding free energy; more physically accurate than docking scores; used in AI-MCLig to validate generated compounds.


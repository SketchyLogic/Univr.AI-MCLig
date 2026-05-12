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
readOrderIndex: -2
---

# Induced fit

The phenomenon whereby a protein's binding site (pocket) changes its conformation (shape) when a ligand binds to it, adapting to better accommodate the ligand. This is in contrast to the "lock and key" model, which assumes a rigid receptor perfectly pre-shaped for its ligand.

Induced fit is widespread: studies show that protein pockets can shift by 1–3 Å (or more for flexible targets like kinases and GPCRs) upon ligand binding. Even small conformational changes in key residues can significantly affect docking predictions.

**Why it matters computationally:** Most docking programs assume a rigid protein. If the pocket changes shape when the ligand binds, a docked pose based on the apo (unbound) structure may be incorrect — the ligand may not fit the rigid structure but would fit the induced structure. This is the "rigid protein problem."

**AI-MCLig's solution:** By rebuilding the entire protein-ligand complex with Chai-1 at every MC step, the protein pocket is allowed to re-adapt for each new ligand conformation — implicitly modelling induced fit.

## TLDR

The reshaping of a protein's binding pocket upon ligand binding; ignored by most docking programs; AI-MCLig handles it implicitly by rebuilding the complex with Chai-1 at each MC step.
#Presentation

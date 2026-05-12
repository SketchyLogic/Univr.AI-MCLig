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
readOrderIndex: -1
---

# Molecular docking

A computational method that predicts the preferred binding pose and binding strength of a small molecule (ligand) within a protein's binding site. The protein structure is held fixed (or semi-flexible), and the ligand is systematically or stochastically positioned, rotated, and conformed inside the pocket. A scoring function estimates the binding energy of each pose; the best-scoring pose is the predicted binding mode.

Docking programs such as AutoDock Vina and rDOCK are widely used in drug discovery to virtually screen thousands of compounds against a target and prioritise candidates for experimental testing.

**Key limitation:** Most docking programs treat the protein as rigid. In reality, the binding pocket often changes shape when a ligand binds ([[Induced fit]]), which can cause docking to give wrong predictions for flexible targets.

**Analogy:** Molecular docking is like trying different keys (ligands) in a lock (protein pocket) and measuring how well each key fits — but assuming the lock never changes shape, which is not quite true.

## TLDR
Predicts how a small molecule fits into a protein pocket and estimates binding strength; widely used in drug discovery but limited by rigid-protein assumption.


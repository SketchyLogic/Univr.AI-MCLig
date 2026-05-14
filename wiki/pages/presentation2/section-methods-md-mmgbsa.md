---
CreatedAt: 2026-05-14
LastUpdateAt: 2026-05-14
LastReviewAt: null
ReviewerIds:
  - admin
OwnerIds:
  - admin
IssueNotes: null
GeneratedBy: claude-sonnet-4-6
readOrderIndex: 0
---

# Molecular Dynamics Simulations and MMGBSA Calculations

*MMGBSA provides a physics-based, force-field binding energy estimate to independently validate the AI-generated compounds.*

- Purpose: cross-validate final MC-generated ligands with a classical, force field–based method independent of [[Chai-1]]
- Pipeline: Chai-1 3D structure → antechamber force field assignment → AMBER TIP3P solvation → energy minimisation + MD → MMGBSA binding energy
- MD protocol: 2 000-step minimisation; heat to 300 K with positional restraints; 20 ns unrestrained MD (pmemd.cuda)
- MMGBSA scoring: first 2.5 ns discarded (equilibration); 750 frames from remaining trajectory; igb = 5 generalised Born model (Miller et al. 2012)

[[prj.home/OriginalPaper.Structural.GPCR.DeNovoProteinLigand.btag027.pdf#page=3]]
*p. 3 — Full MD and MMGBSA calculation protocol.*

> [!caution] MMGBSA is post-hoc validation, not part of the MC loop
> Running 20 ns MD per compound is far too slow to embed in the scoring function (~20 h per MC run already). MMGBSA results in Table 2 validate selected final compounds only.

# Presenter Notes

**Why MMGBSA at all?**

The MC simulation uses Chai-1 pLDDT as its primary score. But pLDDT is an AI confidence score — it is only loosely correlated with experimental binding affinity (Figure 1). To build confidence that the generated compounds are genuine binders, the authors validate a subset with MMGBSA: a classical physics-based method that estimates the free energy of binding using molecular mechanics force fields plus an implicit solvent model.

**Walk through the pipeline:**

1. The starting 3D coordinates for the protein–ligand complex come from Chai-1's predicted structure — not from a crystallographic structure.
2. The antechamber program assigns GAFF force field parameters to the ligand (handles the unusual atom types in generated compounds).
3. AMBER's tleap module solvates the complex in a TIP3P water box.
4. Energy minimisation removes any bad contacts from the Chai-1 prediction.
5. The system is heated to 300 K with restraints on non-hydrogen atoms, then released for 20 ns of unrestrained MD.
6. The first 2.5 ns are discarded as equilibration. The remaining trajectory (750 frames, sampled every ~23 ps) is processed by the MMGBSA tool.

**What igb = 5 means:** The generalised Born model with igb = 5 is the Onufriev–Bashford–Case variant, widely used for protein–ligand MMGBSA. It gives a reasonable balance of accuracy and speed.

**The reference range:** For high-affinity ligands in the targets studied here, MMGBSA energies of −25 to −45 kcal/mol are typical. The generated compounds fall in this range (Table 2), which is the key validation result.

Likely question: "Is MMGBSA accurate?" MMGBSA is semi-quantitative — it ranks binders reliably within a congeneric series but has significant absolute errors. It is used here as a relative validator, not as a precise affinity predictor.

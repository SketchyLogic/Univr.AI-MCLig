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

# Introduction — part 1

*Designing drugs means finding small molecules that bind to a specific protein — but proteins are not rigid locks.*

- Most drugs are small organic molecules that bind to a target protein and interfere with its function
- Rational design problem: find the molecule that fits the pocket best, from a nearly infinite chemical space
- Traditional docking and current generative ML methods share a critical assumption: the protein pocket is **fixed and rigid**

[[prj.home/OriginalPaper.Structural.GPCR.DeNovoProteinLigand.btag027.pdf#page=1]]
*Abstract and Introduction — the paper's central motivation: protein flexibility is neglected by virtually all current methods (p. 1).*

> [!Example] The moving lock analogy
> Designing a drug is like cutting a key for a lock — but the lock reshapes itself depending on which key is inserted. A key cut for the empty lock will not open it once it has adapted around a different key.

# Introduction — part 2

*AI structure predictors make protein flexibility available at every evaluation step — this is the paper's core insight.*

- [[AlphaFold]] successors (AlphaFold3, [[Chai-1]], Boltz-1) predict the full 3D protein–ligand complex from sequence + [[SMILES]] in seconds
- [[AlphaFold]] successors (AlphaFold3, [[Chai-1]], Boltz-1) predict the full 3D protein–ligand complex from sequence + [[SMILES]] in seconds
- Because the complex is predicted from scratch at each call, the pocket shape adapts to the current ligand — **induced fit comes for free**
- AI-MCLig repurposes [[Chai-1]] as a scoring oracle inside a [[Monte Carlo simulation]] loop: modify ligand → rebuild complex → score → accept/reject

# Presenter Notes

**Part 1 — The Problem**

Open by asking: what does it mean to design a drug? A drug is a small organic molecule that binds to a specific protein and changes its behaviour — inhibiting an enzyme, blocking a receptor, disrupting a protein–protein interaction. The computational challenge is finding such a molecule from a chemical space estimated at 10⁶⁰ possible drug-like structures.

The dominant computational tool for this is molecular docking: place the ligand inside the known 3D structure of the protein pocket and score how well it fits. The problem is that docking almost always treats the protein as a rigid body. In reality, proteins breathe — the binding pocket reshapes itself around the ligand, a phenomenon called induced fit. Kinases and GPCRs are notorious for this. Ignoring flexibility leads to false negatives: real binders score poorly because the rigid pocket doesn't match their shape.

**Part 2 — The Opportunity**

The arrival of AlphaFold3, Chai-1, and Boltz changed the landscape. These models take a protein amino acid sequence and a ligand SMILES string, and output a predicted 3D structure of the entire complex in seconds — including a confidence score called [[pLDDT]]. Because they predict from scratch each time, the pocket geometry is specific to the current ligand. The authors realised: if you call this model at every step of a chemical search, you get protein flexibility for free. That is the core idea of AI-MCLig.

Likely audience question: "Why not just run molecular dynamics to get flexibility?" Answer: MD is accurate but slow — tens of minutes to hours per ligand. In a search loop making thousands of evaluations, that is prohibitive. Chai-1 takes ~10 seconds per call.

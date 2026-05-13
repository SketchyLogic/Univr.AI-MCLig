---
CreatedAt: 2026-05-13
LastUpdateAt: 2026-05-13
LastReviewAt: null
ReviewerIds:
  - admin
OwnerIds:
  - admin
IssueNotes: null
GeneratedBy: claude-sonnet-4-6
readOrderIndex: 0
---

# Introduction — part 1: The Rigid Protein Problem

*Molecular docking — the dominant computational screening method — treats the protein as a frozen statue.*

- Docking: place a molecule into a fixed pocket, score how well it fits
- Problem: the pocket changes shape when different ligands bind ([[Induced fit]])
- Worst offenders: [[GPCR]]s and kinases — pharmacologically critical, highly flexible

[[prj.home/OriginalPaper.Structural.GPCR.DeNovoProteinLigand.btag027.pdf#page=1|Introduction, p. 1 — rigid pocket problem]]
*Introduction, p. 1 — the field context for the rigid-pocket limitation.*

# Introduction — part 2: Generative AI Has the Same Blind Spot

*Deep generative models (VAEs, diffusion networks) generate diverse ligands — but still score them against a rigid receptor.*

- Trained on millions of known protein–ligand structures (PDB)
- Can generate entirely new SMILES: diverse, drug-like, novel
- Fundamental limitation: the receptor conformation is fixed at training time — induced fit is not modelled

[[prj.home/OriginalPaper.Structural.GPCR.DeNovoProteinLigand.btag027.pdf#page=2|Introduction cont., p. 2 — generative AI context]]
*Introduction continued, p. 2 — generative AI methods and their shared rigidity assumption.*

> [!caution] The shared problem
> Both traditional docking and modern deep generative models share this gap. The paper argues it is not a failure of any one method — it is a systematic blind spot of the entire field.

# Presenter Notes

**Part 1 — Rigid Protein Problem**

Molecular docking has been the workhorse of computational drug discovery for decades. The idea is simple: take a protein crystal structure, identify the binding pocket, and score how well candidate molecules fit geometrically and chemically. The catch is that almost all docking programs assume the protein stays completely rigid. But proteins are not rigid. When a ligand binds, nearby residues rearrange — sometimes dramatically. This is called induced fit. Ignoring it means you're designing a key for the wrong version of the lock. The problem is especially severe for GPCRs and kinases, which are among the most important drug targets in medicine and which show the most conformational variability.

**Part 2 — Generative AI Has the Same Blind Spot**

The AI revolution in drug design has produced impressive new tools: variational autoencoders, generative adversarial networks, diffusion models. These can generate enormous numbers of novel drug-like molecules. However, even these state-of-the-art methods almost always score the generated ligands against a single, fixed receptor structure. The flexibility problem is swept under the rug. The authors' argument is clear in the introduction: the field has a systematic gap. AI-MCLig is their answer to it.

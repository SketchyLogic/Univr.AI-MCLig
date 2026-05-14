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

# Fragment-Based MC Simulation

*Instead of atom-level edits, the fragment-based variant recombines whole molecular building blocks — larger jumps through chemical space.*

- Known ligands are decomposed into [[BRICS decomposition|BRICS fragments]] using [[RDKit]]'s BRICS implementation (Degen et al. 2008)
- At each step: **add** a compatible fragment at a random placeholder position, or **remove** a fragment (replacing it with the placeholder)
- Fragment compatibility follows BRICS rules — only chemically valid connections are allowed
- Same [[Metropolis acceptance criterion]] as atomistic MC: $P(\Delta s) = \exp(-\beta \cdot \Delta s)$, with $\beta = 5$ (smaller → more exploratory) and reset to best every 50 steps

[[prj.home/OriginalPaper.Structural.GPCR.DeNovoProteinLigand.btag027.pdf#page=3]]
*p. 3 — Fragment-based MC simulation description and the two fragment operations.*

> [!caution] Fragment library quality matters
> Random fragments from ChEMBL produce results comparable to the atomistic approach. Target-specific fragments (from known binders) give significantly higher scores — but require prior knowledge of active compounds for the target.

# Presenter Notes

**The Fragment Idea**

The atomistic MC approach makes small changes one atom at a time. The fragment-based variant takes a different strategy: it operates at the level of whole molecular building blocks. The BRICS (Breaking of Retrosynthetically Interesting Chemical Substructures) method, implemented in RDKit, decomposes a known ligand into chemically meaningful fragments — pieces that could realistically be recombined by a medicinal chemist. During the MC simulation, at each step the algorithm either adds one of these fragments to the current molecule (at a random compatible attachment point) or removes a fragment (replacing it with a placeholder atom). Because each change adds or removes an entire fragment, the score landscape becomes rougher — a single step can dramatically improve or worsen the molecule. This is why $\beta = 5$ is used instead of 50: more worsening moves must be accepted to allow exploration.

**Two flavours were tested:**

First: known ligands for the target broken into fragments and recombined (500 steps). This essentially asks: can the method reassemble the known ligand from its own pieces? In all cases, compounds very similar to the known ligand were recovered — validating the fragment search.

Second: 50 randomly selected fragments from ChEMBL (1 000 steps). This tests de novo generation. The results were surprisingly similar to the atomistic approach, suggesting the fragment library diversity compensates for the coarser moves.

The key message: when target-specific fragments are available (from known binders), scores improve significantly. The fragment-based approach is therefore most powerful when some prior knowledge of the target exists — making it complementary rather than superior to the atomistic approach.

Likely question: "How many fragments are in the library?" In the random ChEMBL test, 50 fragments. This is deliberately small to keep the search manageable; a larger library would likely improve diversity of generated compounds.

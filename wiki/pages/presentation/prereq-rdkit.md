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

# Background: RDKit

*[[RDKit]] is the open-source cheminformatics toolkit that AI-MCLig relies on for every chemistry-specific operation — without it, the simulation would not know the rules of chemistry.*

- Role 1 (atomistic MC): applies each chemical change to the molecule and checks the result is a valid structure
- Role 2 (fragment MC): implements BRICS decomposition and recombination; enforces fragment compatibility rules
- Role 3 (scoring): computes the QED drug-likeness score (weight 0.05 in the composite score)
- Role 4 (validation): computes atom-pair fingerprints and Dice similarity for the recovery experiments

[[prj.home/OriginalPaper.Structural.GPCR.DeNovoProteinLigand.btag027.pdf#page=2|Methods, p. 2 — RDKit usage]]
*Methods, p. 2 — RDKit cited for chemical mutation, validity checking, and BRICS implementation.*

# Presenter Notes

**Why mention RDKit at all?**

RDKit is infrastructure — it doesn't get a figure or a table. But understanding its role prevents a common misconception: the MC simulation does not generate arbitrary SMILES strings and hope Chai-1 ignores the bad ones. Every proposed molecule is validated by RDKit before it is ever sent to Chai-1. A carbon with five bonds, or a broken ring, never reaches the oracle. This is why the simulation stays in chemically meaningful space throughout.

**The four roles, briefly**

Walk through the four roles as a simple list. The key ones to emphasise:

1. **Validity gatekeeper** — filters out chemically impossible structures at no computational cost. This is critical in a 2000-step loop where each Chai-1 call takes roughly 30 seconds.

2. **BRICS implementation** — provides the fragment decomposition and recombination used in the fragment-based MC variant. Without this, the authors would have had to implement their own fragmentation algorithm from scratch.

3. **QED scorer** — a single function call returns a 0–1 drug-likeness score. The paper notes that the molecular weight component of the standard QED formula is excluded (intentionally, to allow temporarily large intermediates during the search).

4. **Fingerprints + Dice similarity** — used only in the validation experiments (Table 1). Atom-pair fingerprints were chosen specifically because RDKit computes them very efficiently — fast enough for the inner loop of a simulation.

**SMILES**

If anyone in the audience asks: SMILES is the text notation used to represent molecules throughout the paper. `c1ccccc1` is benzene. RDKit reads and writes this format.

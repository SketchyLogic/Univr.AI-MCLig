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

# Table 1 — MC Recovery of Known Ligands

*When dice similarity replaces Chai-1 as the score, the MC search recovers 9 of 11 known bromodomain binders with perfect similarity (dice = 1.00) — validating the search engine independently of the oracle.*

- Columns: target SMILES (from PDB / literature) | final MC-generated SMILES | dice similarity score
- 9 of 11 targets: dice = 1.00 — exact recovery; the MC procedure literally rediscovered the known drug molecule
- 2 failures: one compound reached dice = 0.905, one reached only 0.726 — both are polycyclic structures where ring-forming moves created hard-to-escape local traps

[[prj.home/OriginalPaper.Structural.GPCR.DeNovoProteinLigand.btag027.pdf#page=4|Table 1, p. 4 — MC recovery results]]
*Table 1 — target and resulting compounds in SMILES representation for atomistic-step MC simulations (p. 4).*

> [!caution] What the failures mean
> The two imperfect recoveries (PDB entries 4a9i and 1a07) share complex fused-ring scaffolds. The MC move set uses ring-forming and ring-breaking operations with only 2.5–5% probability, making it hard to escape once a ring topology is fixed. This is a limitation of the move set, not of Chai-1.

# Presenter Notes

**How to read Table 1**

The left column is the target: a known bromodomain inhibitor from the PDB, shown as a SMILES string. The right column is what the MC simulation produced — starting from benzene, using only dice similarity (not Chai-1) as its guide. The dice score column measures how close the two are: 1.00 = identical molecules.

**Why this experiment is important**

This is a controlled sanity check. Before trusting Chai-1 as the oracle, the authors ask: is the MC search engine itself capable of finding a specific target compound in chemical space? By swapping in a perfect scoring function (dice similarity to the known answer), they isolate the search engine's performance. The answer is: yes — for 9 out of 11 targets, the algorithm rediscovered the exact molecule. Walk across the row for PDB entry 5mli: the target SMILES is `CNC(=O)c1ClC)C` and the final compound matches it perfectly at dice = 1.00. That compound was discovered in a simulation that started from benzene and made only atomic changes.

**The two failures**

For entry 4a9i (dice = 1.00 in the table = actually the one with score ~0.726 based on the CCNC compound, the lowest score) and 1a07 (CCCCN compound reaching 0.907), the simulation got close but not all the way. These ligands contain fused ring systems. Once the simulation forms a ring, breaking it requires a specific ring-breaking move that only fires 5% of the time — and the partially-assembled ring structure may score slightly better than the broken alternative, trapping the search. Point out that this does not undermine the main method: when Chai-1 is the oracle, the target is not a specific molecular graph but a region of chemical space with good binding properties. Ring traps are less problematic when you are searching for *any* good binder, not a specific one.

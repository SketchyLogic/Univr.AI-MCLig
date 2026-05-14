---
tags:
  - __CONCEPT
CreatedAt: 2026-05-14
LastUpdateAt: 2026-05-14
LastReviewAt: null
ReviewerIds:
  - admin
OwnerIds:
  - admin
IssueNotes: null
GeneratedBy: claude-sonnet-4-6
readOrderIndex: 1
---

# Ligand vs. binder

The two words are often used interchangeably in casual speech, but they encode different levels of certainty — and the distinction matters when reading the paper's claims.

## Ligand

*Ligand* (from Latin *ligare*, to bind) is a structural term: any molecule occupying or interacting with a binding site on a protein. It is neutral about affinity, specificity, or biological effect. A ligand may be:

- A substrate being processed by an enzyme
- A cofactor required for catalysis (e.g. ATP, NAD⁺)
- An inhibitor blocking a pocket
- A crystallographic small molecule present purely because it co-crystallised with the protein (solvent, buffer components)
- A computationally designed molecule that has never been tested

In structural biology, the word almost always means "the small molecule in the crystal structure", regardless of how tightly it binds. Databases like PDBbind store protein–**ligand** complexes precisely in this sense.

## Binder

*Binder* is an empirical term from drug discovery: a molecule that has been experimentally shown to associate with the target protein with **measurable affinity** (typically $K_d$ or IC₅₀ in the micromolar-to-nanomolar range). Being a binder implies:

1. The molecule was physically tested (biochemical assay, SPR, ITC, or similar)
2. A numeric affinity value exists
3. The binding is reproducible and above background

A binder is always a ligand (it occupies the binding site); a ligand is not necessarily a binder (it may bind negligibly or only under crystallographic conditions).

## The distinction in AI-MCLig

The paper uses both words carefully, and the difference carries real epistemological weight:

| Term in paper | What it means |
|---|---|
| "ligand binding pocket" | The structural cavity in the protein — no affinity claim |
| "known ligands" / "target ligands" | Molecules from PDBbind co-crystal structures — their affinity is known from experiment |
| "experimentally known binders" (Table 3) | Compounds with measured $K_d$ or IC₅₀ — the gold-standard reference |
| "generated ligands" / "putative binders" | Molecules produced by AI-MCLig — **not yet experimentally tested** |
| "potential binders" | Synonym for putative binders — computational prediction only |

The paper's central validation claim — that generated compounds have MMGBSA and Boltz-2 scores "in the same range as experimentally known binders" — is carefully worded: it says the scores are **comparable**, not that the compounds have been shown to bind. The leap from "putative binder" to "confirmed binder" requires a wet-lab experiment that the paper does not perform.

> [!caution] The paper never calls generated compounds "binders" without qualification
> Every generated compound is referred to as a "putative binder" or "potential binder". This hedging is scientifically correct — MMGBSA and Chai-1 scores are predictors, not measurements. A reader who conflates the two risks overstating the paper's conclusions.

## Why this matters for critical reading

When the paper says the generated compounds have "favorable binding scores comparable to experimentally known binders" (p. 6–8), it is making a **scoring comparison**, not a **binding claim**. The validation chain is:

$$\text{Chai-1 score} \xrightarrow{\text{loose correlation}} \text{affinity} \qquad \text{MMGBSA} \xrightarrow{\text{semi-quantitative}} \text{affinity} \qquad \text{Boltz-2} \xrightarrow{\text{model prediction}} \text{affinity}$$

None of these steps is a direct measurement. The chain holds statistically but can fail for individual compounds — exactly as Figure 1 shows for Chai-1 alone.

## Related pages

- [[Score change Δs]]
- [[Why Chai-1 as oracle rather than formula-based physical eval of energy state?]]
- [[Metropolis acceptance criterion]]

## Test yourself

- A molecule co-crystallised with a protein at a resolution of 2.0 Å: is it a ligand, a binder, or both? What additional information would you need?
- The paper generates compound SMILES with Chai-1 score 0.91. Can you call it a "binder"? Why or why not?
- Table 3 lists compounds with experimental binding affinities between −3.90 and −12.00 kcal/mol. What does it take for a molecule to appear in that table?

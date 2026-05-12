---
tags:
  - __CONCEPT
  - PREREQUISITE
CreatedAt: 2026-05-12
LastUpdateAt: 2026-05-12
LastReviewAt: null
ReviewerIds:
  - admin
OwnerIds:
  - admin
IssueNotes: null
GeneratedBy: claude-sonnet-4-6
readOrderIndex: -1
---

# RDKit

**RDKit** (Landrum 2023) is a free, open-source software toolkit for **cheminformatics** — the field of using computers to represent, manipulate, and analyse chemical molecules.

Think of it as a Swiss-army knife for chemistry software: it knows the rules of chemistry (which atoms can bond, what makes a molecule valid, how to measure similarity between two molecules) and exposes those rules as a programming library that other software can call. AI-MCLig relies on it heavily throughout its pipeline.

## What RDKit does in AI-MCLig

RDKit is called in four distinct roles. Each is described below.

---

### 1. Applying and validating chemical changes (atomistic MC, p. 2)

Every time the [[Monte Carlo simulation]] proposes a change to the current molecule — adding a group, removing an atom, flipping a bond — it uses RDKit's routines to:

1. **Make the change** in the molecule's internal representation.
2. **Check chemical validity**: Is the resulting structure actually a real molecule? Does every atom have a sensible number of bonds? Is the SMILES string well-formed?

Without this check, the simulation could easily drift into nonsensical structures (e.g. a carbon with five bonds) that Chai-1 cannot evaluate. RDKit acts as a filter that silently discards any chemically impossible modification before it wastes a Chai-1 prediction.

> [!Info] What is SMILES?
> SMILES (Simplified Molecular Input Line Entry System) is a compact text notation for molecules. For example, benzene is written `c1ccccc1`. RDKit reads, writes, and manipulates molecules in this format throughout AI-MCLig (Weininger 1988).

---

### 2. BRICS fragmentation and recombination (fragment-based MC, pp. 2–3)

In the fragment-based variant of AI-MCLig, molecules are not modified atom-by-atom. Instead, whole **chemical fragments** are swapped in or out. RDKit provides the implementation of the **BRICS** method (Degen *et al.* 2008) that makes this possible.

BRICS works in two directions:

- **Decomposition**: Break a known drug-like molecule into chemically meaningful building blocks by cutting at specific types of bonds. The pieces are the "fragment library."
- **Recombination**: Snap fragments back together at their attachment points to build new molecules.

RDKit also enforces **BRICS compatibility rules** — only fragment pairs that are chemically compatible (matching attachment-point types) are allowed to join. This prevents the simulation from producing structures that look assembled but are chemically nonsensical.

---

### 3. QED drug-likeness score (scoring, p. 3)

When scoring a candidate molecule, AI-MCLig adds a small penalty/reward for **drug-likeness** — how much the molecule resembles real drugs already approved or tested. RDKit computes this as the **QED score** (Quantitative Estimate of Drug-likeness), based on the study by Bickerton *et al.* (2012).

QED combines several molecular properties (molecular weight, number of rotatable bonds, presence of certain functional groups, etc.) into a single number between 0 and 1, where 1 is maximally drug-like. AI-MCLig uses this score with a small weight (coefficient 0.05) in the composite scoring formula:

$$\text{score} = 0.8 \cdot \text{Chai-1} - 0.1 \cdot \text{SA} + 0.05 \cdot \text{ESOL} + 0.05 \cdot \text{QED}$$

> [!caution] Molecular weight excluded
> The paper notes that the molecular weight factor in the standard QED formula is neglected. This is a deliberate choice: enforcing a strict weight limit during the MC search could prematurely cut off structures that are useful intermediates even if they are temporarily large.

---

### 4. Atom-pair fingerprints and Dice similarity (similarity-based MC, p. 3)

In the validation experiments, the authors replaced the Chai-1 score with a **molecular similarity score** to test whether the MC algorithm can recover a known target compound starting from benzene. RDKit provides both components needed:

- **Atom-pair fingerprints** (Carhart *et al.* 1985): A compact numerical "signature" of a molecule, encoding which pairs of atom types are present and how far apart they are in the molecule graph. Two molecules with similar fingerprints tend to have similar chemistry.
- **Dice similarity score**: A standard formula for comparing two fingerprints. A score of 1.0 means the two molecules are identical; 0.0 means completely dissimilar.

The authors specifically chose atom-pair fingerprints because they can be computed very efficiently in the tight inner loop of an MC simulation — RDKit's implementation is fast enough to run tens of thousands of similarity evaluations without becoming a computational bottleneck.

---

## Summary table

| Role | Where in AI-MCLig | What RDKit does |
|---|---|---|
| Chemical mutation engine | Atomistic MC, each step | Applies the change; validates the molecule |
| BRICS toolkit | Fragment-based MC | Decomposes molecules into fragments; enforces assembly rules |
| QED scorer | Scoring function (all MC) | Returns a 0–1 drug-likeness score |
| Fingerprint + similarity | Validation / similarity-based MC | Builds atom-pair fingerprints; computes Dice similarity |

## Related pages

- [[Monte Carlo simulation]]
- [[MC step selection probabilities]]
- [[Score change Δs]]

## Other sources

- [RDKit documentation](https://www.rdkit.org/docs/)
- Landrum G. (2023) *RDKit: Open-source cheminformatics* — the primary citation used in the paper
- Bickerton *et al.* (2012) *Nature Chemistry* 4, 90–98 — the QED score definition
- Degen *et al.* (2008) *ChemMedChem* — the BRICS fragmentation method

## Test yourself

- Name the four roles RDKit plays in AI-MCLig.
- Why does RDKit need to validate a molecule after each MC step?
- What is the difference between BRICS decomposition and BRICS recombination?
- Why were atom-pair fingerprints chosen over other similarity measures for the MC inner loop?

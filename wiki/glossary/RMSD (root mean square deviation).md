---
tags:
  - __DEFINITION
  - PREREQUISITE
CreatedAt: 2026-05-14
LastUpdateAt: 2026-05-14
LastReviewAt: null
ReviewerIds:
  - admin
OwnerIds:
  - admin
IssueNotes: null
GeneratedBy: claude-sonnet-4-6
readOrderIndex: -1
---

# RMSD (root mean square deviation)

RMSD is the standard way to answer the question: **"how much have these atoms moved?"** It collapses the 3D displacement of every atom in a set into a single number in ångströms (Å).

## The formula

Given $N$ atoms, each with a position in the reference structure $\mathbf{r}_i^{\text{ref}}$ and in the comparison structure $\mathbf{r}_i^{\text{comp}}$:

$$\text{RMSD} = \sqrt{\frac{1}{N} \sum_{i=1}^{N} \left| \mathbf{r}_i^{\text{comp}} - \mathbf{r}_i^{\text{ref}} \right|^2}$$

In plain English: compute the straight-line distance each atom moved, square all those distances, average them, then take the square root. The result is in Å (ångströms; 1 Å = 0.1 nm, roughly the diameter of a hydrogen atom).

## Reading RMSD values

| RMSD | Structural meaning |
|---|---|
| 0.0 Å | Identical structures — no movement |
| < 0.5 Å | Very similar — only thermal vibration |
| 0.5–1.5 Å | Noticeable but modest conformational change |
| 1.5–3.0 Å | Significant side-chain or loop rearrangement |
| > 3.0 Å | Large structural change; different conformational state |

These thresholds are rough guides; what counts as "significant" depends on the region of the protein being measured.

> [!Hint] The square root of the average of squares
> The "squaring" step ensures that large displacements count more than small ones — RMSD is sensitive to outliers. Two atoms moving 1 Å each gives the same RMSD as one atom moving √2 Å and one staying still.

## What it measures in AI-MCLig (Figure 3C)

The paper tracks **pocket RMSD**: the RMSD of all non-hydrogen protein atoms within 5 Å of the native ligand, measured against the starting structure at the beginning of the MC simulation.

- **Reference structure**: the Chai-1–predicted complex at MC step 0 (starting from benzene)
- **Comparison structure**: the Chai-1–predicted complex at each subsequent MC step
- **Atom set**: pocket residue atoms only — not the whole protein

A rising pocket RMSD means the protein pocket is physically rearranging as the ligand changes. A flat RMSD means the pocket stays rigid regardless of what ligand sits inside it.

### What Figure 3C shows

- **Bromodomain** (blue): RMSD stays near 0.0–0.5 Å throughout 2 000 steps → the pocket is essentially rigid; it barely notices which ligand is present
- **p38 kinase** and **Pim-1 kinase** (orange, green): RMSD fluctuates between 0.5 and 2.0 Å → the pocket actively reshapes around each candidate ligand

This is the paper's direct experimental evidence for *conformational adaptation* — the key feature the title ("including protein flexibility") promises. Because Chai-1 rebuilds the complex from scratch at every MC step, the pocket geometry is free to change, and Figure 3C shows that it does — in exactly the targets known experimentally to be flexible.

## Related pages

- [[Monte Carlo simulation]]
- [[Why Chai-1 as oracle rather than formula-based physical eval of energy state?]]
- [[Pim-1 kinase]]
- [[p38 kinase]]

## Test yourself

- Two structures have RMSD = 0.3 Å. Does that mean they are identical? What does it mean?
- The bromodomain pocket RMSD stays flat. Does that mean Chai-1 is not doing anything for bromodomain? Or does it mean something about the target itself?
- If one residue in the pocket moves by 4 Å and the other 19 residues do not move, roughly what is the pocket RMSD?

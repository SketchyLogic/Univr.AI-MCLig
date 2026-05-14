---
tags:
  - __DEFINITION
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

# p38 kinase

**p38 MAPK** (Mitogen-Activated Protein Kinase, MAPK14 for the α isoform) is a serine/threonine kinase that sits at the centre of the cellular stress-response and inflammatory signalling network. The "p38" name refers to the protein's 38 kDa molecular weight, first observed on a gel.

## Signalling role

p38 is activated by a kinase cascade in response to environmental stress (UV radiation, osmotic shock, hypoxia) and pro-inflammatory cytokines (TNF-α, IL-1β):

$$\text{MKK3/6} \xrightarrow{\text{phosphorylates}} \text{p38} \xrightarrow{\text{phosphorylates}} \text{MK2, MNK1, transcription factors}$$

Downstream effects include production of inflammatory cytokines, regulation of mRNA stability, and gene expression changes that drive inflammation and cell fate decisions. This makes p38 a major target for anti-inflammatory drug discovery; however, several clinical programs have been limited by toxicity at the systemic inhibition level.

Four isoforms exist (p38α, p38β, p38γ, p38δ); **p38α** is the most pharmacologically characterised and the one implicitly referenced throughout AI-MCLig.

## Structural flexibility and the DFG motif

p38 has two conformationally important regions:

1. **DFG motif** — three residues (Asp-Phe-Gly) at the entrance of the activation loop. In the **DFG-in** state, phenylalanine points into the ATP-binding cleft and the kinase is active. In the **DFG-out** state, phenylalanine swings away, opening a deep hydrophobic "back pocket" that many selective inhibitors exploit. This flip is a major source of conformational variability in p38 — and a primary reason that rigid-pocket docking methods fail.

2. **Glycine-rich P-loop** — loops over the ATP/inhibitor and is mobile; its exact position changes with different bound ligands.

These motions mean that the binding pocket can change significantly depending on which ligand is present, requiring a method that models protein flexibility explicitly — exactly the problem AI-MCLig addresses.

> [!Example] Classic p38 inhibitor
> SB203580 is the reference ATP-competitive p38α inhibitor used in hundreds of studies. It binds the DFG-in pocket and makes key contacts with the hinge residues. It is among the reference compounds used in Table 3 of the paper to benchmark generated compound scores.

## Role in AI-MCLig

p38 kinase is one of the four test targets in the paper (alongside [[Bromodomain]], [[Pim-1 kinase]], and the β-1 adrenergic receptor). As a flexible kinase, it exhibits larger binding-pocket RMSD during the [[Monte Carlo simulation]] than the rigid bromodomain, which the paper takes as evidence that the method correctly captures target flexibility. Generated compounds score at Chai-1 ≈ 0.85–0.92 and MMGBSA ≈ −25 to −57 kcal/mol — comparable to the known experimental binders in Table 3.

## Related pages

- [[Pim-1 kinase]]
- [[Bromodomain]]
- [[Monte Carlo simulation]]
- [[Induced fit]]

## Other sources

- [UniProt Q16539 (MK14_HUMAN)](https://www.uniprot.org/uniprot/Q16539)
- Cuenda & Rousseau (2007) *Biochim Biophys Acta* — comprehensive review of p38 MAPK biology and drug targeting
- ChEMBL target page for p38α (CHEMBL260): reference inhibitors and IC50 data

## Test yourself

- What is the DFG motif, and how does its conformational switch (DFG-in vs. DFG-out) affect ligand binding?
- Why has systemic p38 inhibition been clinically problematic despite strong target validation?
- What does a larger binding-pocket RMSD in the AI-MCLig simulation tell you about p38 compared with the bromodomain?

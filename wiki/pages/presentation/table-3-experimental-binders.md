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

# Table 3 — Experimental Binder Reference

*Known, experimentally validated binders for the same four targets show MMGBSA and Boltz-2 scores that overlap directly with the AI-MCLig generated compounds in Table 2.*

- Columns: PDB entry | protein | ligand SMILES | experimental binding affinity (kcal/mol) | MMGBSA (kcal/mol) | Boltz-2 (kcal/mol)
- Experimental MMGBSA range: −20 to −50 kcal/mol across all targets — fully overlaps Table 2 generated compound range of −19 to −57 kcal/mol
- Best known binder per target: bromodomain 5khm (−38 kcal/mol MMGBSA); p38 kinase 6ohd (−50 kcal/mol); Pim-1 kinase 4n70 (−46 kcal/mol); β-1 receptor 3zpr (−34 kcal/mol)

[[prj.home/OriginalPaper.Structural.GPCR.DeNovoProteinLigand.btag027.pdf#page=8|Table 3, p. 8 — experimental binder reference]]
*Table 3 — experimental binding affinities, MMGBSA scores, and Boltz-2 scores for ligands of the bromodomain, Pim-1 protein, p38 map kinase, and β-1 adrenergic receptor (p. 8).*

# Presenter Notes

**How to read Table 3**

This table is the baseline. Every row is a real ligand that has been measured in a lab: it has a crystal structure in the PDB and an experimentally determined binding affinity. The experimental affinity column (kcal/mol, converted from Kd or IC50) is the ground truth. The MMGBSA and Boltz-2 columns were computed the same way as in Table 2 — so the numbers are directly comparable.

**The comparison argument**

Show Tables 2 and 3 in sequence. For the bromodomain, Table 3 shows MMGBSA values ranging from −21 (5mli, a weak binder, Kd = −3.9 kcal/mol) to −39 kcal/mol (5khm, the tightest binder at −8.3 kcal/mol). Now look at Table 2 bromodomain: −19 to −30 kcal/mol. The overlap is clear. The generated compounds are not as deep as the best known bromodomain inhibitor (5khm at −39 kcal/mol), but they are solidly in the range of real binders.

For p38 kinase, the experimental binders reach MMGBSA down to −50 kcal/mol (6ohd, a very tight binder at −9.38 kcal/mol). Table 2 generated p38 compounds reach −34 to −42 kcal/mol — in the same range as the weaker known binders. Not as deep as the best, but consistent with real binding.

**Boltz-2 as a cross-check**

The Boltz-2 column is particularly compelling. This is a completely independent model trained on different data. For bromodomain experimental binders, Boltz-2 scores range from −7.93 to −9.68 kcal/mol. For AI-MCLig generated bromodomain compounds (Table 2): −7.76 to −9.40 kcal/mol. The agreement is striking. Boltz-2 and Chai-1 have never "talked to each other" — they agree because both are detecting genuine structural complementarity.

**What this does not prove**

Be honest with the audience: MMGBSA and Boltz-2 are still computational scores. Being in the same range as known binders is necessary but not sufficient evidence of real binding activity. The generated compounds have not been synthesised and tested. This is the standard limitation of all computational drug design — and it applies equally to traditional docking and generative AI methods.

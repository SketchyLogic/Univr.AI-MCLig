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

# Fragment-Based MC Variant

*Instead of atom-level edits, fragment-based MC recombines whole chemical building blocks — and it also produces high-scoring binders.*

- [[BRICS decomposition]]: break known ligands at retrosynthetically meaningful bonds into fragments
- MC steps: add a fragment at a placeholder position, or remove a fragment and restore the placeholder
- Two fragment libraries tested: random selection from ChEMBL (50 fragments); target-specific (fragments from known binders for the target)
- Key finding: target-specific fragments outperform random ones — prior knowledge of binders accelerates convergence

[[prj.home/OriginalPaper.Structural.GPCR.DeNovoProteinLigand.btag027.pdf#page=9|Fragment-based MC section, p. 9]]
*Fragment-based MC results, p. 9 — fragment recovery and de novo generation scores comparable to atomistic-step MC.*

> [!caution] Limitation of random fragments
> Random ChEMBL fragments may not contain the chemical groups best suited to a given target. Better fragment libraries require prior knowledge of the target's binding preferences — which is not always available for a truly novel target.

# Presenter Notes

**Part 1 — The Fragment Approach**

The second protocol replaces atom-level changes with fragment-level ones. The idea comes from fragment-based drug discovery, a well-established experimental method. Using the BRICS algorithm from RDKit, known ligands are decomposed into chemically meaningful building blocks. The MC simulation then works by randomly adding or removing one of these fragments at each step, rather than tweaking individual atoms. This makes each step a larger jump in chemical space — which requires a lower β (= 5 instead of 50) to maintain a reasonable acceptance rate.

**Why it matters**

The fragment approach was first tested as a recovery experiment: can it reassemble a known ligand from its own fragments? For all tested cases, the answer is yes — not always with the exact same SMILES, but with similar or identical structures. Then, de novo generation was tested using 50 random ChEMBL fragments. The generated compounds achieved comparable Chai-1 and MMGBSA scores to the atomistic-step runs. When target-specific fragments (from known binders) were used, scores improved further — confirming that domain knowledge, when available, makes the search faster and more effective. The two protocols are complementary: atomistic-step MC requires no prior knowledge; fragment-based MC rewards it.

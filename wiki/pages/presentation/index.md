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

# Presentation Index — AI-MCLig

Ordered list of all presentation slide files. Each file is self-contained.

> [!caution] Based on paper reading — no wiki annotation yet
> All files were generated directly from the paper (no deep-dive or annotation pages existed at generation time).

---

## Prerequisites (show before the main deck if audience needs background)

| File | Concept | Source |
|---|---|---|
| [[prereq-monte-carlo]] | Monte Carlo simulation | Glossary (index −3) |
| [[prereq-metropolis]] | Metropolis criterion, Δs, and β | Glossary (indices −1, 0, 1) |
| [[prereq-rdkit]] | RDKit and its four roles | Glossary (index −1) |
| [[prereq-mc-probabilities]] | MC step selection probabilities | Glossary (index 2) |

---

## Main section slides

| # | File | Title | Slides | Key figure / table |
|---|---|---|---|---|
| 1 | [[section-opening-hook]] | Opening Hook: A Key for a Moving Lock | 1 | p. 1 |
| 2 | [[section-introduction]] | Introduction: The Rigid Protein Problem | 2 | pp. 1–2 |
| 3 | [[section-ai-scoring-oracle]] | AI Structure Prediction as a Scoring Oracle | 1 | Figure 1 (p. 5) |
| 4 | [[section-monte-carlo]] | Monte Carlo Optimisation in Chemical Space | 1 | Eq. (1) (p. 2) |
| 5 | [[section-methods-pipeline]] | The AI-MCLig Pipeline | 3 | Figure 2 (p. 6) ★ |
| 6 | [[section-validation-recovery]] | Validation: Can the Search Rediscover Known Compounds? | 1 | Table 1 (p. 4) |
| 7 | [[section-results-de-novo]] | De Novo Generation Results | 2 | Tables 2–3, Figure 3 |
| 8 | [[section-fragment-based]] | Fragment-Based MC Variant | 1 | p. 9 |
| 9 | [[section-conclusion]] | Limitations and Future Directions | 1 | Figure 1 (p. 5) |

**Main deck total: 13 slides across 9 section files.**

---

## Figure deep-dives (use as backup slides or for detailed discussion)

| File | Figure | Page | Key claim |
|---|---|---|---|
| [[figure-1-chai1-affinity]] | Figure 1 — Chai-1 score vs. binding affinity | p. 5 | Average trend positive; large individual scatter |
| [[figure-2-ligand-evolution]] | Figure 2 — Ligand evolution over MC steps | p. 6 | Benzene → drug-like molecule filling the pocket ★ |
| [[figure-3-score-trajectories]] | Figure 3 — Score trajectories and pocket RMSD | p. 9 | Rapid early convergence; target-dependent flexibility |

---

## Table deep-dives (use as backup slides or for detailed discussion)

| File | Table | Page | Key claim |
|---|---|---|---|
| [[table-1-recovery]] | Table 1 — MC recovery of known ligands | p. 4 | 9/11 bromodomain ligands recovered with dice = 1.00 |
| [[table-2-generated-compounds]] | Table 2 — Generated compound scores | p. 7 | Chai-1 0.84–0.92; MMGBSA −19 to −57 kcal/mol |
| [[table-3-experimental-binders]] | Table 3 — Experimental binder reference | p. 8 | Generated compounds in the same MMGBSA range as known drugs |

---

## Recommended narrative arc

**Prerequisites** (if needed): MC → Metropolis/β → RDKit → probabilities

**Main deck**:
- 1–2: Frame the problem
- 3–4: Introduce the two ingredients
- 5: Show the method in action ★
- 6–7: Present the evidence
- 8: Fragment variant (brief)
- 9: Limitations + outlook

**Q&A backup slides**: figure-1, figure-3, table-1, table-2, table-3

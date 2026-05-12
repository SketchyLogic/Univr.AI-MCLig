---
CreatedAt: 2026-05-11
LastUpdateAt: 2026-05-11
LastReviewAt: null
ReviewerIds:
  - admin
OwnerIds:
  - admin
IssueNotes: null
GeneratedBy: claude-sonnet-4-6
readOrderIndex: 2
---

# Deep Dive: Table 2 — Generated Compounds from Atomistic-step MC

**Summary**: A reading aid for Table 2 (p. 7), which reports the final generated compounds from 12 de novo MC simulations (3 runs × 4 targets), with all scoring metrics: composite score, Chai-1, SA, ESOL, QED, MMGBSA, and Boltz-2. This is the central results table of the paper.

**Paper section**: Results §De novo generation of bound ligands using atomistic-step MC simulation, p. 7

---

## 0. Orientation

Table 2 is the paper's most important quantitative result — it is the answer to "can AI-MCLig generate drug-like binders?" For each of four targets, three independent MC runs produced a final compound. Each compound is characterised by seven numbers: the composite score (what the MC optimised), Chai-1 score (the dominant term), SA/ESOL/QED (the three bias terms), MMGBSA (independent physics-based validation), and Boltz-2 (independent AI validation). The key reading strategy: ignore the SMILES strings for now and read the number columns. If MMGBSA and Boltz-2 scores are in the same range as Table 3 (known binders), the paper's claim holds.

![[prj.home/OriginalPaper.Structural.GPCR.DeNovoProteinLigand.btag027.pdf#page=7]]
*Table 2 — Final generated compounds from atomistic-step MC for bromodomain, p38 kinase, Pim-1 kinase, and β-1 adrenergic receptor, with composite score, Chai-1, SA, ESOL, QED, MMGBSA, and Boltz-2 scores (p. 7).*

## 1. What you need before reading this

- **[[MMGBSA]]** — the physics-based binding free energy validator; more negative = stronger predicted binding.
- **[[Boltz-2]]** — the AI-based binding affinity validator; returns predicted log(IC50) converted to kcal/mol; more negative = higher potency.
- **[[Synthetic accessibility score]]** — the SA score; scale 1 (trivially easy) to 10 (impossible); reported raw (not normalised) in Table 2.
- **[[QED (drug-likeness)]]** — 0–1 score; higher = more drug-like.
- **[[SMILES]]** — the molecular representation in the second column; required to interpret the compound structures.
- **[[Chai-1]]** — the primary scoring oracle; its confidence score is the first column after SMILES.

> [!caution] Prerequisite
> Table 2 is meaningless without Table 3 as context. The claim that generated compounds are "comparable to known binders" requires comparing Table 2 numbers to Table 3 numbers. Read Table 3 in parallel with Table 2.

## 2. Column-by-column annotation

**[Column: Score]**
The composite score from Eq. 2: score = 0.8 × Chai-1 − 0.1 × SA(norm) + 0.05 × ESOL(norm) + 0.05 × QED. Ranges from 0.846 (p38 run 2) to 0.919 (bromodomain run 2). This is what the MC optimised — higher is better.

**[Column: Chai-1 score]**
The AI confidence score for the predicted protein–ligand complex. Ranges from 0.837 (p38 run 3) to 0.917 (bromodomain run 2). A score > 0.85 is associated with high-quality complex prediction; > 0.90 is exceptional.

**[Column: SA score]**
Raw (unnormalised) synthetic accessibility. Ranges from 4.347 (bromodomain run 1) to 4.956 (p38 run 2). Scale: 1–10; lower = easier to synthesise. All values fall in the 4.3–5.0 range — moderate complexity, reflecting the SA cap in the scoring function. Actual synthesisability must be confirmed by a medicinal chemist.

**[Column: ESOL score]**
Predicted solubility (logarithm of aqueous solubility). Values range from −1.687 to −4.240. The sign follows the logS convention (negative means log(S) in M). More negative = less soluble. Most compounds are in the −2 to −4 range, indicating moderate solubility — within the acceptable range for oral drugs (typically logS > −4).

**[Column: QED score]**
Drug-likeness score, 0–1. Ranges widely from 0.030 (p38 run 2 — notably very low) to 0.929 (bromodomain run 2). Most values are 0.7–0.9, indicating drug-like compounds. The low value for p38 run 2 (0.030) is notable — this compound may have unusual structural features that penalise QED (e.g. unusual functional groups, very high molecular weight, many rotatable bonds).

> [!danger] p38 run 2: QED = 0.030
> A QED of 0.03 is extremely low — equivalent to a compound far from typical drug space. Yet this compound has a high Chai-1 score (0.863) and reasonable MMGBSA (−41.73 kcal/mol). This suggests the MC sacrificed drug-likeness to achieve strong pocket contacts for this particular target. Such a compound would likely be deprioritised in real drug discovery.

**[Column: MMGBSA score (kcal/mol)]**
The mean MMGBSA binding free energy ± standard deviation from 750-frame MD trajectories. Ranges from −19.19 ± 0.13 (bromodomain run 3) to −56.57 ± 0.19 (β-1 receptor run 3). More negative = stronger predicted binding. The ± values are statistical precision (frame-to-frame variance), not accuracy.

**[Column: Boltz-2 score (kcal/mol)]**
Independent AI-predicted binding affinity converted from log(IC50). Ranges from −7.151 (Pim-1 run 3) to −10.226 (β-1 receptor run 1). More negative = higher predicted potency. Boltz-2 is calibrated to alchemical free energy calculations — its absolute values are more reliable than MMGBSA for cross-target comparison.

## 3. Target-by-target analysis

### Bromodomain (runs 1–3)

| Run | Score | Chai-1 | SA | ESOL | QED | MMGBSA | Boltz-2 |
|-----|-------|--------|-----|------|-----|--------|---------|
| 1 | 0.908 | 0.902 | 4.347 | −2.954 | 0.857 | −27.47 ± 0.16 | −8.484 |
| 2 | 0.919 | 0.917 | 4.405 | −4.240 | 0.929 | −29.79 ± 0.10 | −9.400 |
| 3 | 0.918 | 0.908 | 4.798 | −1.687 | 0.844 | −19.19 ± 0.13 | −7.763 |

Bromodomain shows the highest Chai-1 scores (0.902–0.917) and consistent QED values (~0.85–0.93). MMGBSA ranges from −19 to −30 kcal/mol. Compare to Table 3 bromodomain reference: −21 to −39 kcal/mol. **Overlap: yes for runs 1 and 2; run 3 (−19 kcal/mol) is at the boundary.**

> [!caution] Bromodomain run 3 MMGBSA (−19.19) is lower than the weakest reference binder
> Table 3 shows 5mli (weakest bromodomain reference binder) with MMGBSA = −20.71 kcal/mol. Run 3 at −19.19 kcal/mol is slightly weaker than even the weakest known binder. This is the marginal case — it may or may not be a genuine binder.

### p38 MAP kinase (runs 1–3)

| Run | Score | Chai-1 | SA | ESOL | QED | MMGBSA | Boltz-2 |
|-----|-------|--------|-----|------|-----|--------|---------|
| 1 | 0.885 | 0.874 | 4.623 | −2.986 | 0.844 | −40.20 ± 0.14 | −7.947 |
| 2 | 0.846 | 0.863 | 4.904 | −2.330 | 0.030 | −41.73 ± 0.29 | −7.790 |
| 3 | 0.877 | 0.872 | 4.578 | −3.684 | 0.720 | −34.01 ± 0.28 | −9.388 |

p38 shows lower Chai-1 scores (~0.86–0.87) than bromodomain but stronger MMGBSA (−34 to −42 kcal/mol). This reflects the deeper, more directional p38 binding pocket. Compare to Table 3 p38 reference: −35 to −50 kcal/mol. **Overlap: runs 1 and 2 fall within the reference range; run 3 (−34) is at the lower boundary.**

The QED anomaly in run 2 (0.030) warrants attention — see note above.

### Pim-1 kinase (runs 1–3)

| Run | Score | Chai-1 | SA | ESOL | QED | MMGBSA | Boltz-2 |
|-----|-------|--------|-----|------|-----|--------|---------|
| 1 | 0.860 | 0.843 | 4.698 | −2.311 | 0.659 | −34.23 ± 0.21 | −7.422 |
| 2 | 0.876 | 0.857 | 4.490 | −2.771 | 0.868 | −32.54 ± 0.14 | −8.750 |
| 3 | 0.854 | 0.837 | 4.956 | −3.500 | 0.754 | −31.55 ± 0.15 | −7.151 |

Pim-1 shows the lowest Chai-1 scores of the four targets (0.837–0.857), and MMGBSA scores of −31 to −34 kcal/mol. Compare to Table 3 Pim-1 reference: −30 to −46 kcal/mol. **Overlap: all three runs fall within the reference range (at the weaker end).**

### β-1 adrenergic receptor (runs 1–3)

| Run | Score | Chai-1 | SA | ESOL | QED | MMGBSA | Boltz-2 |
|-----|-------|--------|-----|------|-----|--------|---------|
| 1 | 0.849 | 0.838 | 4.490 | −2.943 | 0.724 | −32.16 ± 0.27 | −10.226 |
| 2 | 0.851 | 0.844 | 4.714 | −2.758 | 0.633 | −47.61 ± 0.52 | −8.007 |
| 3 | 0.875 | 0.865 | 4.871 | −3.785 | 0.818 | −56.57 ± 0.19 | −8.780 |

The GPCR target shows the widest range of MMGBSA scores (−32 to −57 kcal/mol) and the highest Boltz-2 score for run 1 (−10.226 kcal/mol). Compare to Table 3 β-1 receptor reference: only two known binders (3zpr: −34.36, 3zpq: −24.02). **Run 3 at −56.57 kcal/mol exceeds the reference range entirely.**

> [!danger] β-1 receptor run 3 MMGBSA (−56.57) is far outside the reference range
> With only two reference compounds in Table 3 (MMGBSA −24 to −34 kcal/mol), run 3 at −56.57 kcal/mol is 20 kcal/mol stronger than the strongest reference binder. This could mean: (a) a genuinely exceptional binder was generated, (b) MMGBSA overestimates for this compound, or (c) the two reference compounds are not representative of the full range of GPCR binders. The GPCR MMGBSA uncertainty (±0.52) is small compared to the discrepancy — this is not a precision issue.

## 4. Cross-table summary: generated vs. reference

| Target | Generated MMGBSA range | Reference MMGBSA range (Table 3) | Overlap |
|--------|----------------------|--------------------------------|---------|
| Bromodomain | −19 to −30 kcal/mol | −21 to −39 kcal/mol | Partial (2/3 runs) |
| p38 kinase | −34 to −42 kcal/mol | −35 to −50 kcal/mol | Yes (2/3 runs) |
| Pim-1 kinase | −31 to −35 kcal/mol | −30 to −46 kcal/mol | Yes (3/3 runs) |
| β-1 receptor | −32 to −57 kcal/mol | −24 to −34 kcal/mol | Partial (1–2/3) |

## 5. What is easy to miss

> [!caution] Score ≠ Chai-1 score — the difference is the bias terms
> Every row shows Score > 0.8 × Chai-1 because the ESOL and QED terms are positive and large enough to offset the SA penalty. A reader who only looks at the Score column is seeing the combined effect of binding prediction and three drug-property biases.

> [!Hint] The ±SD in MMGBSA is precision, not accuracy
> The ±0.10 to ±0.52 kcal/mol precision of MMGBSA is much smaller than its accuracy (~±5 kcal/mol for absolute binding affinity). The small SD means the calculation is internally consistent (low noise across frames) but does not mean the absolute value is accurate to ±0.15 kcal/mol.

> [!caution] SA scores above 4 are non-trivial to synthesise
> All SA scores in Table 2 are 4.3–5.0 (out of 10). While the SA cap keeps them from being extreme, values above 4 typically indicate compounds requiring 4–6 synthetic steps with non-trivial functional group manipulations. "Can be synthesised with reasonable effort" should be interpreted as: possible, but not trivial.

> [!Hint] Boltz-2 and MMGBSA generally agree in direction
> For most compounds, both Boltz-2 and MMGBSA predict reasonable binding (negative values). In cases where they disagree in magnitude (e.g. β-1 receptor run 2: MMGBSA −47.61 but Boltz-2 −8.007, while run 1 has MMGBSA −32.16 but Boltz-2 −10.226), the rank ordering differs. This reflects that the two methods capture different aspects of binding — MMGBSA is sensitive to electrostatics and solvation; Boltz-2 to learned features from experimental IC50 data.

## 6. Open questions

> [!question] Were any generated compounds eliminated before Table 2?
> Table 2 shows 3 runs per target. Were any runs discarded because of poor scores? If only the best 3 of N runs are shown, the reported numbers are optimistically biased.

> [!question] Can the p38 run 2 compound (QED = 0.030) be improved?
> The cleanup phase (100 steps optimising SA/ESOL/QED while preserving Chai-1) was described for some compounds. Was it applied to p38 run 2? If so, why is QED still 0.030?

> [!question] What are the actual structures of the generated compounds?
> The SMILES strings are provided but require chemical drawing tools (e.g. RDKit, ChemDraw) to visualise. The paper does not show 2D structures of the generated compounds. Are they reasonable drug-like scaffolds by visual inspection?

## 7. Connections

**Within the paper**
- Table 2 generated vs. Table 3 reference → the core validation claim of §De novo generation
- Table 2 MMGBSA scores → computed by protocol in §Molecular dynamics and MMGBSA calculations (Methods)
- Table 2 Boltz-2 scores → [[Boltz-2]] independently validates the MC-generated compounds
- Table 2 score column → Eq. 2 in §Scoring of compounds (can be verified row by row)

**Outside the paper**
- [[MMGBSA]] — the binding free energy method; Miller *et al.* 2012 is the primary reference.
- [[Boltz-2]] — Passaro *et al.* 2025; calibrated to alchemical free energy calculations.
- [[Synthetic accessibility score]] — Ertl & Schuffenhauer 2009; explains the SA values in the table.

## 8. Test yourself

1. **(Recall)** How many compounds appear in Table 2? How are they grouped?
2. **(Recall)** Which target shows the highest Chai-1 scores? Which shows the lowest MMGBSA scores (strongest predicted binding)?
3. **(Comprehension)** For bromodomain run 1: Score = 0.908, Chai-1 = 0.902. Verify that these are consistent with Eq. 2 using the SA, ESOL, and QED values in Table 2. (Assume SA is normalised: SA_norm = (10 − SA)/9.)
4. **(Comprehension)** The β-1 receptor run 3 MMGBSA (−56.57 kcal/mol) far exceeds the reference range in Table 3. Give two plausible explanations — one where this is a genuine result and one where it is an artefact.
5. **(Application)** A medicinal chemist wants to synthesise the bromodomain compound with the best balance of binding prediction and synthesisability. Which run (1, 2, or 3) would you recommend, and why?
6. **(Critical)** Table 2 shows only 3 runs per target. If the same MC protocol were run 30 times per target, what distribution of scores would you expect? Would the best 3 of 30 look the same as the 3 reported?
7. **(Critical)** Boltz-2 and MMGBSA sometimes disagree in rank ordering within a target (e.g. β-1 receptor run 1 vs run 2). When two validated methods disagree, which should be trusted more for predicting experimental binding affinity, and why?

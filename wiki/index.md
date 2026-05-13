# Wiki index — AI-MCLig

Table of contents for the entire wiki. Update after every change.

---

## Glossary

### Prerequisites (readOrderIndex < 0)

| Entry | Type | Index | Notes |
|---|---|---|---|
| [[Monte Carlo simulation]] | CONCEPT + PREREQUISITE | -3 | Core algorithm; read first |
| [[Exponential function]] | DEFINITION + PREREQUISITE | -2 | Math building block for acceptance formula |
| [[RDKit]] | CONCEPT + PREREQUISITE | -1 | Cheminformatics toolkit; 4 roles in AI-MCLig |
| [[Metropolis acceptance criterion]] | CONCEPT + PREREQUISITE | -1 | The core decision rule P(Δs) = exp(−β·Δs) |

### Entry point (readOrderIndex = 0)

| Entry | Type | Index | Notes |
|---|---|---|---|
| [[Score change Δs]] | DEFINITION | 0 | Δs in the Metropolis formula |

### Progressive (readOrderIndex > 0)

| Entry | Type | Index | Notes |
|---|---|---|---|
| [[Beta parameter β]] | DEFINITION | 1 | β selectivity/temperature parameter |
| [[MC step selection probabilities]] | CONCEPT | 2 | How change types are chosen each step; empirical tuning |

---

## Pages

### Presentation

**Index**: [[wiki/pages/presentation/index]]

*Section slides (13 slides):*

| File | Title | Slides |
|---|---|---|
| [[wiki/pages/presentation/section-opening-hook]] | Opening Hook: A Key for a Moving Lock | 1 |
| [[wiki/pages/presentation/section-introduction]] | Introduction: The Rigid Protein Problem | 2 |
| [[wiki/pages/presentation/section-ai-scoring-oracle]] | AI Structure Prediction as a Scoring Oracle | 1 |
| [[wiki/pages/presentation/section-monte-carlo]] | Monte Carlo Optimisation in Chemical Space | 1 |
| [[wiki/pages/presentation/section-methods-pipeline]] | The AI-MCLig Pipeline | 3 |
| [[wiki/pages/presentation/section-validation-recovery]] | Validation: Can the Search Rediscover Known Compounds? | 1 |
| [[wiki/pages/presentation/section-results-de-novo]] | De Novo Generation Results | 2 |
| [[wiki/pages/presentation/section-fragment-based]] | Fragment-Based MC Variant | 1 |
| [[wiki/pages/presentation/section-conclusion]] | Limitations and Future Directions | 1 |

*Figure deep-dives:*

| File | Figure |
|---|---|
| [[wiki/pages/presentation/figure-1-chai1-affinity]] | Figure 1 — Chai-1 score vs. binding affinity |
| [[wiki/pages/presentation/figure-2-ligand-evolution]] | Figure 2 — Ligand evolution over MC steps |
| [[wiki/pages/presentation/figure-3-score-trajectories]] | Figure 3 — Score trajectories and pocket RMSD |

*Table deep-dives:*

| File | Table |
|---|---|
| [[wiki/pages/presentation/table-1-recovery]] | Table 1 — MC recovery of known ligands |
| [[wiki/pages/presentation/table-2-generated-compounds]] | Table 2 — Generated compound scores |
| [[wiki/pages/presentation/table-3-experimental-binders]] | Table 3 — Experimental binder reference |

*Prerequisite slides:*

| File | Concept |
|---|---|
| [[wiki/pages/presentation/prereq-monte-carlo]] | Monte Carlo simulation |
| [[wiki/pages/presentation/prereq-metropolis]] | Metropolis criterion, Δs, and β |
| [[wiki/pages/presentation/prereq-rdkit]] | RDKit |
| [[wiki/pages/presentation/prereq-mc-probabilities]] | MC step selection probabilities |

---

## Stubs wanted

- `Scoring formula (AI-MCLig)` — referenced by [[Score change Δs]]; covers equation (2): Chai-1, SA, ESOL, QED terms

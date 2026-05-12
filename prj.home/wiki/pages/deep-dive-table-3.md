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

# Deep Dive: Table 3 — Reference Experimental Binders

**Summary**: A reading aid for Table 3 (p. 8), which lists experimentally validated ligands for the four target proteins with their experimental binding affinities, MMGBSA scores, and Boltz-2 scores. This table is the reference baseline against which Table 2 generated compounds are judged.

**Paper section**: Results §De novo generation of bound ligands using atomistic-step MC simulation, p. 7–8

---

## 0. Orientation

Table 3 is the **ground truth table** — it contains compounds with experimentally measured binding affinities (from ITC, SPR, or inhibition assays), whose crystal structures are in the PDB. For each known binder, the same MMGBSA and Boltz-2 calculations performed on generated compounds (Table 2) were repeated — ensuring a fair comparison. The strategy: if generated compounds score similarly on MMGBSA and Boltz-2 as these validated drugs, they are plausible binders. Read Table 3 as the answer key: Table 2 compounds that overlap the Table 3 score range pass; those outside fail.

![[prj.home/OriginalPaper.Structural.GPCR.DeNovoProteinLigand.btag027.pdf#page=8]]
*Table 3 — Experimental binding affinities, MMGBSA scores, and Boltz-2 scores for known ligands of the four target proteins (p. 8).*

## 1. What you need before reading this

- **[[MMGBSA]]** — the physics-based binding free energy method; same protocol used for Table 2 and Table 3, enabling direct comparison.
- **[[Boltz-2]]** — AI-predicted affinity (log IC50 → kcal/mol); same model applied to both tables.
- **[[Bromodomain]]** — the first target; 5 reference compounds.
- **[[GPCR]]** — the β-1 adrenergic receptor; only 2 reference compounds (weakest validation baseline).
- **[[Molecular docking]]** — for context: the experimental binding affinities (column 4) are the gold standard that docking scores are also trying to predict.

> [!caution] Prerequisite
> Table 3 is meaningful only when read alongside Table 2. Without knowing which score ranges correspond to known binders, the Table 2 numbers have no reference frame. The two tables must be read as a pair.

## 2. Row-by-row annotation

### Bromodomain (5 reference compounds: 5mli, 4o7a, 4hbw, 5igk, 5khm)

| PDB | Exp. affinity | MMGBSA | Boltz-2 |
|-----|-------------|--------|---------|
| 5mli | −3.90 kcal/mol | −20.71 ± 0.13 | −7.930 |
| 4o7a | −4.72 kcal/mol | −23.11 ± 0.13 | −9.521 |
| 4hbw | −5.32 kcal/mol | −25.23 ± 0.10 | −8.97 |
| 5igk | −7.38 kcal/mol | −33.77 ± 0.12 | −9.682 |
| 5khm | −8.30 kcal/mol | −38.68 ± 0.18 | −9.428 |

Five reference compounds spanning a ~4 kcal/mol range of experimental affinity (−3.90 to −8.30 kcal/mol), corresponding to roughly a 1000-fold range of $K_d$ values. MMGBSA spans −21 to −39 kcal/mol; Boltz-2 spans −7.93 to −9.68 kcal/mol. Note that 5mli is a weak binder (−3.90 kcal/mol), consistent with its weaker MMGBSA (−20.71). The correlation direction is correct within the bromodomain set: stronger experimental affinity → more negative MMGBSA.

**Comparison to Table 2 generated compounds:**
- Generated Chai-1 scores (0.902–0.917) exceed what typical known binders might achieve — reflecting that AI-MCLig optimises Chai-1 directly.
- Generated MMGBSA (−19 to −30 kcal/mol): bromodomain run 1 (−27.47) and run 2 (−29.79) fall comfortably within the Table 3 range; run 3 (−19.19) is below the weakest reference.
- Generated Boltz-2 (−7.76 to −9.40): all within the Table 3 range (−7.93 to −9.68). ✓

### p38 MAP kinase (5 reference compounds: 3iw8, 3iw7, 6m95, 3l8s, 6ohd)

| PDB | Exp. affinity | MMGBSA | Boltz-2 |
|-----|-------------|--------|---------|
| 3iw8 | −4.87 kcal/mol | −41.17 ± 0.29 | −7.812 |
| 3iw7 | −5.64 kcal/mol | −35.39 ± 0.14 | −6.701 |
| 6m95 | −6.85 kcal/mol | −37.48 ± 0.17 | −7.943 |
| 3l8s | −7.00 kcal/mol | −44.22 ± 0.18 | −10.531 |
| 6ohd | −9.38 kcal/mol | −50.18 ± 0.15 | −10.862 |

The p38 reference set spans a wider affinity range (−4.87 to −9.38 kcal/mol, a 10,000-fold $K_i$ range) and shows MMGBSA from −35 to −50 kcal/mol. The MMGBSA is systematically more negative for p38 than bromodomain — consistent with p38's larger, deeper ATP-binding pocket providing more extensive ligand contacts. Note that MMGBSA rank does not perfectly match experimental affinity rank (3iw8 at −4.87 exp. affinity has −41.17 MMGBSA, stronger than 3iw7 at −5.64 but −35.39 MMGBSA) — reflecting MMGBSA's known ranking errors within a closely related compound series.

> [!caution] MMGBSA rank ≠ experimental affinity rank
> For p38, 3iw8 has lower experimental affinity (−4.87) but higher MMGBSA (−41.17) than 3iw7 (−5.64 exp., −35.39 MMGBSA). This inversion shows MMGBSA cannot reliably rank individual compounds within a narrow affinity range. The method is more reliable for broad comparisons (known binders vs. non-binders).

**Comparison to Table 2 generated compounds:**
- Generated MMGBSA (−34 to −42 kcal/mol): all three runs fall within or at the boundary of the Table 3 range (−35 to −50). ✓
- Generated Boltz-2 (−7.79 to −9.39): spans the reference range (−6.70 to −10.86). ✓

### Pim-1 kinase (6 reference compounds: 4xh6, 5kcx, 4xhk, 5v82, 4n70, plus one more)

| PDB | Exp. affinity | MMGBSA | Boltz-2 |
|-----|-------------|--------|---------|
| 4xh6 | −5.57 kcal/mol | −30.22 ± 0.14 | −7.502 |
| 5kcx | −6.70 kcal/mol | −31.24 ± 0.22 | −9.378 |
| 4xhk | −8.30 kcal/mol | −34.81 ± 0.11 | −10.540 |
| 5v82 | −10.15 kcal/mol | −38.31 ± 0.10 | −12.372 |
| 4n70 | −12.00 kcal/mol | −46.36 ± 0.23 | −12.872 |

The Pim-1 set has the widest experimental affinity range (−5.57 to −12.00 kcal/mol, covering 100,000-fold in $K_i$). 4n70 at −12.00 kcal/mol is the strongest binder in the entire Table 3 dataset, and its MMGBSA (−46.36) and Boltz-2 (−12.872) are also the most negative. This dataset validates MMGBSA and Boltz-2 across a broad affinity range for Pim-1.

**Comparison to Table 2 generated compounds:**
- Generated MMGBSA (−31 to −34 kcal/mol): within the lower end of reference range (−30 to −46). ✓
- Generated Boltz-2 (−7.15 to −8.75): within the reference range (−7.50 to −12.87), at the lower end. The generated compounds appear weaker than the most potent Pim-1 binders (5v82, 4n70) by both metrics.

### β-1 adrenergic receptor (2 reference compounds: 3zpr, 3zpq)

| PDB | Exp. affinity | MMGBSA | Boltz-2 |
|-----|-------------|--------|---------|
| 3zpr | −6.65 kcal/mol | −34.36 ± 0.17 | −8.461 |
| 3zpq | −7.17 kcal/mol | −24.02 ± 0.22 | −9.795 |

Only two reference compounds for the GPCR target. This is the weakest validation baseline in the paper — any comparison to Table 2 generated compounds is highly uncertain because two data points cannot define the range of MMGBSA scores for this target. Note the internal inconsistency: 3zpq has stronger experimental affinity (−7.17 vs −6.65 kcal/mol) but *weaker* MMGBSA (−24.02 vs −34.36 kcal/mol). This is a larger MMGBSA ranking error than seen for the other targets.

> [!danger] Two reference compounds are insufficient to establish a reference range
> "The MMGBSA scores for the four test proteins and different ligands correlate qualitatively with experimentally known binding affinity" — but for the β-1 receptor, two compounds with an internal MMGBSA ranking error do not constitute a correlation. The GPCR validation is the weakest part of Table 3.

**Comparison to Table 2 generated compounds:**
- Generated MMGBSA (−32 to −57 kcal/mol): runs 1 and possibly 2 are within or near reference range; run 3 (−56.57) far exceeds it.
- Generated Boltz-2 (−8.01 to −10.23): above the reference range (−8.46 to −9.80). Run 1 at −10.226 exceeds the strongest reference (3zpq: −9.795).

## 3. Formulas and figures unpacked

**Converting experimental affinity to $K_d$:**

$$\Delta G = RT \ln K_d \quad \Rightarrow \quad K_d = e^{\Delta G / RT}$$

At 300 K, $RT \approx 0.596$ kcal/mol.

| $\Delta G$ (kcal/mol) | $K_d$ (approximate) |
|----------------------|-------------------|
| −4 | ~1 µM |
| −6 | ~30 nM |
| −8 | ~1 nM |
| −10 | ~30 pM |
| −12 | ~1 pM |

The five Pim-1 reference compounds (−5.57 to −12.00 kcal/mol) span the range from micromolar binders to subpicomolar binders — an enormous experimental affinity range. The 4n70 compound at −12.00 kcal/mol would be classified as an ultra-potent inhibitor.

**MMGBSA error analysis:**

| Target | MMGBSA range | Average ±SD | MMGBSA precision |
|--------|-------------|-------------|-----------------|
| Bromodomain | −20.71 to −38.68 | ±0.10 to ±0.18 | Very high |
| p38 | −35.39 to −50.18 | ±0.14 to ±0.29 | High |
| Pim-1 | −30.22 to −46.36 | ±0.10 to ±0.23 | High |
| β-1 receptor | −24.02 to −34.36 | ±0.17 to ±0.22 | High |

All precisions are very small (< 0.5 kcal/mol) relative to the between-compound differences (10–20 kcal/mol). MMGBSA can reliably distinguish compounds within the same target — it is the accuracy relative to experiment that is the issue.

## 4. The argument in one diagram

**Known binders (Table 3) with measured $\Delta G$** → **Same MMGBSA/Boltz-2 protocol applied** → **Reference score ranges established per target** → **Generated compounds (Table 2) scored the same way** → **If Table 2 scores fall within Table 3 ranges → generated compounds are plausible binders** → **Result: partial to full overlap on all four targets**

## 5. What is easy to miss

> [!caution] The reference compounds are not the best possible binders
> The five bromodomain reference compounds (Table 3) span −3.90 to −8.30 kcal/mol. The best known bromodomain inhibitors (e.g. JQ1) have sub-nanomolar affinities, corresponding to ~−12 to −14 kcal/mol. The Table 3 reference set represents moderate-to-good binders, not the state-of-the-art. This means the comparison standard is not the highest bar possible.

> [!Hint] MMGBSA and Boltz-2 independently validate each other within Table 3
> Within the Pim-1 reference set, both MMGBSA and Boltz-2 correctly rank the weakest binder (4xh6) and the strongest binder (4n70) — they agree on the direction. This mutual consistency within Table 3 increases confidence that both methods are measuring something real, even if their absolute accuracy is limited.

> [!caution] β-1 receptor reference is too sparse to be informative
> The two β-1 receptor compounds show an MMGBSA ranking reversal (stronger experimental affinity → weaker MMGBSA). This undermines any claim about generated compound validation for the GPCR target based solely on MMGBSA overlap.

> [!Hint] Boltz-2 scores are more consistent across targets than MMGBSA
> MMGBSA scores differ systematically between targets (bromodomain: −20 to −39; p38: −35 to −50) because they depend on the specific pocket environment and simulation conditions. Boltz-2 scores are more consistent across targets (range −6.7 to −12.9 for all Table 3 compounds), reflecting that Boltz-2 uses the same learned model across targets. This makes Boltz-2 more suitable for cross-target comparisons.

## 6. Open questions

> [!question] Why only 2 β-1 receptor reference compounds?
> The β-1 adrenergic receptor has many known inhibitors (carvedilol, bisoprolol, metoprolol, nebivolol) with well-characterised binding affinities. Why were only 2 selected? The likely answer is PDBbind availability — crystal structures of the exact complex + measured affinity are required, and this may limit options for GPCRs.

> [!question] Could better reference compounds change the conclusion?
> If Table 3 included the most potent known binders for each target (rather than moderate binders from PDBbind), the MMGBSA reference range would shift to much more negative values (e.g. −50 to −80 kcal/mol for Pim-1's picomolar binders). Generated compounds at −31 to −35 kcal/mol would then be outside the reference range. The conclusion that Table 2 overlaps Table 3 is partly a consequence of which reference compounds were chosen.

> [!question] Are these reference compounds active in the same binding mode?
> All reference compounds are assumed to bind the same orthosteric site. If some are allosteric binders, comparing their MMGBSA to generated orthosteric compounds would be misleading.

## 7. Connections

**Within the paper**
- Table 3 → Table 2 comparison: the core validation claim of §De novo generation
- Table 3 Boltz-2 scores → evidence that Boltz-2 is a discriminating validator (it correctly distinguishes strong from weak binders within each target set)
- Table 3 experimental affinities → justification for using Boltz-2 (calibrated to experimental data) rather than relying only on MMGBSA

**Outside the paper**
- [[MMGBSA]] — used identically on Table 2 and Table 3 compounds; the comparison is only valid because the protocol is consistent.
- [[Boltz-2]] — AI affinity predictor calibrated on experimental IC50 data; its accuracy on diverse targets is characterised in Passaro *et al.* 2025.
- [[Bromodomain]] — the first target; JQ1 (a well-known bromodomain inhibitor not in Table 3) would be an informative additional reference compound.

## 8. Test yourself

1. **(Recall)** How many reference compounds are listed for each target? Which target has the fewest?
2. **(Recall)** What is the experimental affinity range for the Pim-1 reference compounds? Convert the weakest (4xh6, −5.57 kcal/mol) and strongest (4n70, −12.00 kcal/mol) to approximate $K_d$ values.
3. **(Comprehension)** For the bromodomain, the MMGBSA of reference compounds ranges from −21 to −39 kcal/mol. Generated compound (Table 2) run 3 has MMGBSA = −19.19 kcal/mol. Does this compound pass or fail the comparison criterion? Justify.
4. **(Comprehension)** For p38 kinase, 3iw8 has −4.87 kcal/mol experimental affinity but −41.17 kcal/mol MMGBSA, while 3iw7 has −5.64 kcal/mol experimental affinity but −35.39 kcal/mol MMGBSA. Why does MMGBSA rank them in the wrong order? What does this tell you about MMGBSA limitations?
5. **(Application)** You are choosing between two generated compounds for synthesis: both have the same Chai-1 score (0.88), but compound A has MMGBSA = −35 kcal/mol (within the Pim-1 reference range) and Boltz-2 = −7.2 kcal/mol (at the lower edge of reference range), while compound B has MMGBSA = −28 kcal/mol (below reference range) and Boltz-2 = −11.5 kcal/mol (above reference range). Which do you prioritise for synthesis?
6. **(Critical)** The β-1 receptor reference set shows an MMGBSA ranking reversal (stronger affinity → weaker MMGBSA). Does this undermine the MMGBSA comparison for Table 2 GPCR compounds? What would you need to restore confidence in the GPCR validation?
7. **(Critical)** The reference compounds in Table 3 span −3.90 to −12.00 kcal/mol experimental affinity. If the paper had selected only the strongest binders (−10 to −12 kcal/mol), the MMGBSA reference range would be much more negative. Would the generated compounds in Table 2 still "overlap"? What does this reveal about the validation strategy?

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

# Deep Dive: Results — MC-Based Recovery and Chai-1 Correlation

**Summary**: A reading aid for the first two Results subsections: the recovery experiment that validates the search mechanism (Table 1), and the correlation analysis showing Chai-1 confidence is a useful but noisy proxy for binding affinity (Figure 1).

**Paper section**: Results and discussion §MC-based recovery of target ligands from simple starting compounds and §Correlation of Chai-1 confidence score and ligand binding affinity, pp. 3–5

---

## 0. Orientation

This is the **validation section** — before showing that AI-MCLig can find new binders, the authors must answer two prerequisite questions: (1) can the MC search engine navigate chemical space well enough to rediscover a known compound, and (2) is the Chai-1 confidence score a meaningful proxy for real binding affinity? The answers are: (1) mostly yes, with a specific failure mode for ring-heavy compounds; and (2) yes on average, but with substantial individual scatter. Keep the phrase "validated but imperfect" in mind — the method works, but the limitation documented here (Chai-1 correlation) is the central weakness carried through the rest of the paper.

## 1. What you need before reading this

- **[[AI-MCLig Validation Strategy]]** — the two-stage recovery protocol (30 parallel runs × 5000 steps, then 1 run × 10000 steps); read this before Table 1 makes sense.
- **[[Dice Similarity vs Chai-1 Scoring]]** — the difference between the two scoring functions: dice = known-answer test, Chai-1 = real design; critical for understanding why recovery uses dice, not Chai-1.
- **[[Molecular fingerprint]]** — the atom-pair fingerprint encoding used to compute the dice similarity score.
- **[[Ring Local Minima in MC]]** — explains the specific failure mode for two compounds in Table 1.
- **[[pLDDT]]** — the Chai-1 confidence score being correlated with experimental affinity in Figure 1.
- **[[Bromodomain]]** — one of the three test proteins for the correlation analysis.

> [!caution] Prerequisite
> Table 1 is incomprehensible without knowing what a dice score measures and why a recovery experiment tests the search mechanism rather than the scoring function. Read [[AI-MCLig Validation Strategy]] and [[Dice Similarity vs Chai-1 Scoring]] first.

## 2. Paragraph-by-paragraph annotation

### §MC-based recovery

**[¶1 — Purpose of the recovery test]**
- **What it says:** Before using Chai-1 for de novo design, the MC search mechanism must be validated independently; this is done using dice similarity to a known compound instead of Chai-1, so the search is tested in isolation.
- **Why it matters:** Establishes the logical separation: recovery tests the search; Chai-1 tests the scoring. Only if both are validated can the full pipeline be trusted.
- **Plain-language expansion:** The dice similarity score measures how chemically similar the current compound is to a known target compound, using atom-pair fingerprints (Carhart *et al.* 1985). If the MC can start from benzene and recover the known target compound (dice = 1.0), the search mechanism is verified — it can navigate chemical space. This is a "control experiment with a known answer": success is unambiguous.

**[¶2 — Two-stage recovery protocol]**
- **What it says:** For atom-based simulations, the recovery used two stages: Stage 1 = 30 simulations × 5000 steps, reset to best every 500 steps; Stage 2 = 1 simulation × 10,000 steps, reset every 100 steps, β = 50.
- **Why it matters:** The two stages reflect parallel exploration (Stage 1) followed by focused convergence (Stage 2).
- **Plain-language expansion:** Stage 1 casts a wide net: 30 independent simulations each explore different trajectories through chemical space, and every 500 steps they all reset to the single best structure found across all 30 — preventing any one run from monopolising a local optimum. Stage 2 takes the best structure from Stage 1 and refines it with a longer, tighter run. The β = 50 provides high selectivity for the convergence stage. This two-stage design reflects an understanding that chemical space is "rugged" — the parallel exploration phase prevents early premature convergence.

**[¶3 — Results: mostly dice = 1.0]**
- **What it says:** For most targets, the approach succeeds in producing exactly the desired target compound (dice = 1.0); two more complicated compounds got stuck in local minima.
- **Why it matters:** The success cases validate the search; the failure cases (0.905 and 0.726) reveal a specific, diagnosable limitation — ring assembly.
- **Plain-language expansion:** Table 1 shows 10 target compounds. Eight achieve dice = 1.00, confirming perfect recovery. The two that fail (6fnx at 0.905, 2yek at 0.726) are ring-heavy compounds — polycyclic systems requiring multiple coordinated ring-forming operations. See [[Ring Local Minima in MC]] for the step-by-step explanation of why rings are hard: intermediate open-chain states score poorly, so the MC tends to backtrack before completing the ring assembly. This is a limitation of the chemical move set, not of the scoring function (dice is used here, not Chai-1).

> [!Hint] The failures are informative
> The two failed recoveries are not embarrassing — they reveal precisely what the method cannot do (assemble complex polycyclic ring systems). A method that fails instructively is more useful than one that fails randomly. The fragment-based MC protocol partly addresses this limitation by adding pre-formed rings as single-step operations.

**[¶4 — β factor acceptance rate]**
- **What it says:** β = 50 leads to a reasonable acceptance rate of 0.1–0.2 for chemical changes, selecting "those few that lead in the right direction."
- **Why it matters:** Quantifies how selective the simulation is — only 10–20% of proposed moves are accepted.
- **Plain-language expansion:** Acceptance rate of 10–20% means 80–90% of random moves are rejected — either because they worsen the score by too much (Metropolis rejection) or because they produce chemically invalid SMILES (rdkit rejection). This selectivity is what makes the simulation converge toward good structures rather than wandering randomly. The β = 50 value was tuned to achieve this acceptance rate empirically.

**[¶5 — Fragment-based recovery]**
- **What it says:** The fragment-based MC successfully reassembled all tested ligand compounds (Table 3, supplementary).
- **Why it matters:** Confirms the fragment-based protocol also has a working search mechanism.
- **Plain-language expansion:** For fragment-based recovery, the target ligand was decomposed into BRICS fragments, a random fragment was used as the starting structure, and the simulation reassembled the remaining fragments. All tested compounds were recovered, but "not always exactly the same" — reflecting the stochastic character of compound generation. The fragment approach sidesteps the ring local minima problem by adding ring-containing fragments in one step.

### §Correlation of Chai-1 confidence and binding affinity

**[¶6 — Correlation study design]**
- **What it says:** Three proteins (bromodomain, Pim-1, p38) with many known experimental binding affinities and Chai-1 scores were analysed; data taken from PDBbind (Liu *et al.* 2017).
- **Why it matters:** Before using Chai-1 as a binding oracle, its correlation with real affinity must be established — the entire validity of the de novo design rests on this.
- **Plain-language expansion:** PDBbind contains crystal structures of protein–ligand complexes with experimentally measured binding affinities ($K_i$, $K_d$, or $\text{IC}_{50}$ converted to kcal/mol). For each complex, the authors re-ran Chai-1 on the protein sequence + ligand SMILES and obtained a confidence score, then compared it to the experimental value. This gives a direct measurement of how well Chai-1 confidence tracks binding affinity on a diverse set of compounds.

**[¶7 — Correlation results and interpretation]**
- **What it says:** While there is no direct correlation, a high Chai-1 score does typically correspond to a high average binding affinity; Figure 1 shows the scatter plots and binned averages with error bars; the correlation is far from ideal.
- **Why it matters:** This is the central caveat of the entire paper — the scoring function works on average but has substantial noise at the individual compound level.
- **Plain-language expansion:** Figure 1 shows three scatter plots (A: bromodomain, B: Pim-1, C: p38) with Chai-1 confidence on the x-axis and experimental binding affinity (kcal/mol, more negative = stronger binding) on the y-axis. The right panels show binned averages with ±SD. The linear fits:
  - Bromodomain: $y = -7.80x + 0.74$ (slope: −7.80 kcal/mol per unit Chai-1)
  - Pim-1: $y = -14.20x + 3.91$ (slope: −14.20 kcal/mol per unit Chai-1)
  - p38: $y = -9.95x + 1.06$ (slope: −9.95 kcal/mol per unit Chai-1)
  All slopes are negative (correct sign: higher Chai-1 → more negative affinity = stronger binding), but the scatter is large. See [[deep-dive-figure-1]] for full analysis.

**[¶8 — Autodock Vina comparison]**
- **What it says:** Autodock Vina scoring was also tested; its correlation with affinity was similar but generally worse than Chai-1; combining Autodock and Chai-1 scores did not improve the correlation significantly.
- **Why it matters:** Demonstrates that Chai-1 is at least as good as a standard docking scoring function, justifying its use as the primary oracle.
- **Plain-language expansion:** The comparison to Autodock Vina (a widely used, validated docking program) establishes that Chai-1 is not being used because it is theoretically ideal but because it is empirically competitive with established tools while also handling protein flexibility. The combined score attempt (supplementary Figure 7) shows that naive combination does not help — suggesting the two scores capture correlated information rather than complementary features.

## 3. Formulas and figures unpacked

**Table 1: Recovery results**

Table 1 lists 10 target SMILES (from PDBbind, bromodomain/Pim-1/p38 complexes) and the MC-generated closest compound after the two-stage recovery procedure.

| Key observation | Example |
|----------------|---------|
| Dice = 1.00 (exact recovery) | 8 out of 10 targets |
| Dice < 1.00 (partial recovery) | 6fnx: 0.905; 2yek: 0.726 |

- The two partial recoveries (4lzs-based 6fnx and 2yek) involve polycyclic aromatic ring systems.
- Dice = 0.905 means approximately 90.5% structural overlap by atom-pair fingerprint — the compound is close but not identical.
- Dice = 0.726 for 2yek is the weakest recovery — the compound structure diverges significantly at the ring system level.

**Figure 1: Chai-1 vs experimental binding affinity correlation**

See [[deep-dive-figure-1]] for the full panel-by-panel unpacking. Key numbers:

| Target | Linear fit slope | Interpretation |
|--------|-----------------|----------------|
| Bromodomain (A) | −7.80 kcal/mol per Chai-1 unit | Moderate trend |
| Pim-1 kinase (B) | −14.20 kcal/mol per Chai-1 unit | Steeper trend |
| p38 kinase (C) | −9.95 kcal/mol per Chai-1 unit | Intermediate |

The large scatter in all three panels means that for any individual compound, a high Chai-1 score does not guarantee strong experimental binding — it only increases the *average* probability of strong binding.

## 4. The argument in one diagram

**Question 1: Does the MC search work?** → **Test: replace Chai-1 with dice similarity to known compound** → **Result: 8/10 exact recovery, 2/10 near-miss (ring systems)** → **Conclusion: search works; ring-complex compounds face a move-set limitation**

**Question 2: Is Chai-1 a valid proxy for binding affinity?** → **Test: correlate Chai-1 scores with experimental affinities from PDBbind** → **Result: significant scatter, but average trend is correct (negative slope)** → **Conclusion: Chai-1 is usable but imperfect; maximising it increases the probability of finding a binder, not a guarantee**

## 5. What is easy to miss

> [!caution] Recovery tests the search, not the scoring — this is easy to conflate
> Many readers see Table 1 and assume it shows that AI-MCLig can find compounds similar to known binders. It shows only that the MC search mechanism can navigate chemical space when guided by a verifiable score. The Chai-1 scoring is not evaluated in Table 1 at all.

> [!Hint] The Pim-1 slope is steeper than the others
> The Pim-1 linear fit slope (−14.20) is nearly twice the bromodomain slope (−7.80). This means Chai-1 is more discriminating for Pim-1 — a small change in Chai-1 score corresponds to a larger change in affinity. This may reflect properties of the Pim-1 binding pocket (deeper, more directional contacts) rather than anything about the method.

> [!caution] The binned average plots (right panels of Figure 1) are what matter
> The raw scatter plots (left panels) show enormous variation and could be dismissed as noise. The binned average plots — grouping compounds by Chai-1 score range and computing mean ± SD — show that the trend is real and statistically significant. The trend being visible in binned data but noisy in individual data means Chai-1 is useful for screening populations of compounds, not for ranking individual ones.

> [!caution] The correlation is not used to calibrate the score
> The correlation analysis is presented as evidence that Chai-1 is "reasonable" to use — it does not produce a calibration curve or a corrected score. The raw Chai-1 confidence score is used in Eq. 2 without any recalibration. This is a methodological simplification that could introduce systematic bias.

## 6. Open questions

- **Author's own caveat:** "It is not yet clear how well it correlates with experimental binding affinities for known complexes." The authors explicitly acknowledge Chai-1 as the main bottleneck.

> [!question] Would a different pLDDT-derived score perform better?
> Chai-1 produces pLDDT per atom — the confidence score used here is likely an average or interface-specific aggregation. Different aggregation strategies (e.g. minimum confidence at the binding interface vs. overall average) might correlate better with affinity.

> [!question] Are the three targets representative?
> The correlation was measured on bromodomain, Pim-1, and p38 — all relatively well-structured, soluble kinase/epigenetic targets. The correlation may be weaker for GPCRs or intrinsically disordered proteins.

> [!question] What happens at very high Chai-1 scores?
> Figure 1 shows data up to Chai-1 ≈ 0.92. The de novo generated compounds in Table 2 reach up to 0.917. Is the trend linear beyond the range shown in Figure 1?

## 7. Connections

**Within the paper**
- Table 1 recovery results → [[Ring Local Minima in MC]] → fragment-based MC (partial solution to the ring problem)
- Figure 1 correlation → the central justification for using Chai-1 in all subsequent de novo simulations
- The "imperfect but useful" conclusion from Figure 1 → the MMGBSA and Boltz-2 validations in Table 2 (needed precisely because Chai-1 is not enough on its own)

**Outside the paper**
- [[Dice Similarity vs Chai-1 Scoring]] — the conceptual distinction between the two scoring modes
- [[Molecular fingerprint]] — the atom-pair fingerprint underlying the dice score
- [[pLDDT]] — the confidence score from AlphaFold-class models; understanding what pLDDT measures explains why it correlates imperfectly with binding affinity

## 8. Test yourself

1. **(Recall)** What is the two-stage recovery protocol? How many simulations in Stage 1, and how many steps?
2. **(Recall)** What dice scores were achieved for the two failed recovery cases? What kind of compounds were they?
3. **(Comprehension)** Why does the recovery experiment use dice similarity score instead of Chai-1 score? What would you learn if Chai-1 were used instead?
4. **(Comprehension)** Explain why a large scatter in the Chai-1 vs. affinity scatter plot does not necessarily mean Chai-1 is useless as a scoring function. What does the binned average plot show that the scatter plot alone does not?
5. **(Comprehension)** Write the linear regression equations for bromodomain, Pim-1, and p38. If a compound has Chai-1 = 0.85, what is the predicted mean binding affinity for each target?
6. **(Application)** You are running a recovery experiment on a target compound with three fused aromatic rings. Based on this section, what outcome do you predict, and why?
7. **(Critical)** The Autodock Vina comparison shows Chai-1 and Autodock have similar correlation with affinity. Does this mean Chai-1 adds no value over standard docking? What specific advantage does Chai-1 provide that the correlation analysis does not capture?
8. **(Critical)** The paper concludes that "maximising the Chai-1 score is a reasonable approach for finding ligands with potentially high binding affinities." Is this justified by Figure 1? What additional evidence would make this claim stronger?

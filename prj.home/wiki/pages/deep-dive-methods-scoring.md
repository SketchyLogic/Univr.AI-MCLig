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
readOrderIndex: 1
---

# Deep Dive: Methods — Scoring of Compounds During MC Simulations

**Summary**: A reading aid for the scoring subsection, which defines the composite score (Eq. 2) combining Chai-1 confidence with three drug-property biases, and explains why each component has its specific weight and capping rule.

**Paper section**: Materials and methods §Scoring of compounds during MC simulations, p. 3

---

## 0. Orientation

This section answers: **how is a candidate ligand scored at each MC step?** The answer is a weighted sum with one dominant term (Chai-1 confidence, weight 0.8) and three small corrective biases (SA, ESOL, QED). The biases do not override the main score — they steer the simulation away from pathological compounds (synthesise-impossibly-complex, insoluble, un-druglike) while letting Chai-1 determine what "good binding" means. Understanding the weights and the SA cap is essential for interpreting every result table in the paper — a compound with Score ≠ Chai-1 has been penalised by at least one bias term.

## 1. What you need before reading this

- **[[Chai-1]]** — the AI structure predictor; its pLDDT-like confidence score for the protein–ligand complex is the dominant term in the scoring function.
- **[[pLDDT]]** — the per-residue/per-atom confidence score produced by AlphaFold-class models; the Chai-1 confidence score used here is directly related to this.
- **[[Synthetic accessibility score]]** — the SA score (0–10), a heuristic estimate of synthesis difficulty; lower = easier to make.
- **[[QED (drug-likeness)]]** — a 0–1 score measuring how drug-like a molecule looks based on physicochemical properties.
- **[[Monte Carlo simulation]]** — the optimisation framework within which this scoring function guides compound evolution.

> [!caution] Prerequisite
> If you do not know what the Chai-1 confidence score measures, the dominant term (0.8 × Chai-1) will be opaque. Read [[Chai-1]] and [[pLDDT]] before this section.

## 2. Paragraph-by-paragraph annotation

**[¶1 — Score is mostly Chai-1]**
- **What it says:** The score for whether a change is kept is mostly based on the Chai-1 confidence score; three biases enforce desired properties.
- **Why it matters:** Sets the hierarchy: Chai-1 dominates; biases are corrections, not co-equal terms.
- **Plain-language expansion:** "Mostly based on" is quantified in Eq. 2: 0.8 is the Chai-1 weight vs. the summed bias weights of 0.2. In practice, because Chai-1 scores span a narrow range (~0.4–0.92 in this paper), the bias terms can have a non-trivial effect near the boundary of the Metropolis acceptance decision — a small nudge toward a better SA score can tip an otherwise-borderline move from rejection to acceptance.

**[¶2 — SA bias]**
- **What it says:** A synthetic accessibility (SA) bias is added to ensure compounds can be reasonably synthesised; the SA contribution is maximal below a threshold, because a ligand needs some complexity to fit a binding pocket.
- **Why it matters:** Without the SA cap, the simulation would generate trivially simple molecules (low SA = easy to make but too small to fill a pocket).
- **Plain-language expansion:** The SA score (Ertl and Schuffenhauer 2009) runs from ~1 (trivially simple) to ~10 (impossibly complex). The score used in Eq. 2 is normalized to [0, 1]. **The cap is critical**: instead of always penalising complexity, the SA contribution is "considered maximal below a threshold." Concretely, once the SA score is below a set ceiling, the bias no longer worsens with increasing complexity. This prevents the simulation from converging on tiny, featureless molecules (like benzene itself) just because they are easy to synthesise. Compound complexity is necessary for binding site complementarity.

> [!caution] The SA cap is easy to miss
> Many readers interpret the SA term as a simple penalty for complexity. It is not. It is a *floor constraint*: "don't go too hard to make, but don't worry about complexity beyond a reasonable threshold." This asymmetric treatment is why Table 2 shows SA scores in the 4.3–5.0 range — the simulation accepts some synthetic difficulty because simple molecules cannot fill the binding pocket.

**[¶3 — ESOL (solubility) bias]**
- **What it says:** An ESOL (Estimated Solubility) bias ensures resulting molecules are soluble in water.
- **Why it matters:** Insoluble compounds are irrelevant as drugs regardless of binding affinity — they cannot be administered or delivered to the target.
- **Plain-language expansion:** ESOL (Delaney 2004) predicts the logarithm of aqueous solubility from molecular structure. Normalised to [0, 1], a higher ESOL score means more soluble. Its weight (0.05) is small — the simulation is not strongly pushed toward highly soluble compounds, but is nudged away from the extremely insoluble end. Table 2 shows ESOL scores of −1.7 to −4.2 kcal/mol for generated compounds, which the authors consider in a reasonable range.

**[¶4 — QED bias]**
- **What it says:** A drug-likeness (QED) bias steers toward molecules with drug-appropriate properties; the molecular weight factor in the standard QED formula is neglected.
- **Why it matters:** Drug-likeness captures a cluster of properties (size, polarity, functional group composition) that correlate with oral bioavailability.
- **Plain-language expansion:** [[QED (drug-likeness)]] was developed by Bickerton *et al.* 2012. The standard implementation includes a molecular weight term — the authors deliberately remove it ("neglecting molecular weight factor") to avoid biasing the simulation toward small molecules. This is consistent with the SA cap logic: the simulation should not be driven toward simplicity just because simple molecules score better on size-based metrics. QED scores in Table 2 range from 0.03 to 0.93, with most in the 0.7–0.9 range.

**[¶5 — The complete score formula]**
- **What it says:** The final score is: score = 0.8 × Chai-1 − 0.1 × SA + 0.05 × ESOL + 0.05 × QED, with SA and ESOL normalised to [0, 1].
- **Why it matters:** This is the complete quantitative definition of "good" for the MC simulation — every accepted compound has been judged by this formula at each step.
- **Plain-language expansion:** See §3 below for the full symbol table and worked example. Note the sign convention: SA is *subtracted* (negative coefficient) because the SA score is high for hard-to-synthesise compounds (and the simulation should penalise those); ESOL and QED are *added* (positive coefficients) because high values are desirable. The factor of 0.8 for Chai-1 was chosen because "a simulation with a smaller contribution often fails to maximise this score" — meaning if Chai-1's weight is too low, the MC wanders into drug-like but non-binding territory.

**[¶6 — Weight justification]**
- **What it says:** The Chai-1 contribution is set to 0.8 because smaller contributions cause the simulation to fail at maximising Chai-1; the SA contribution of 0.1 is enough to keep SA below the threshold "in almost all cases"; QED and ESOL make only small contributions but keep compounds in a reasonable range.
- **Why it matters:** The weights were empirically found, not derived theoretically — understanding this limits how much the specific numbers should be trusted.
- **Plain-language expansion:** This is a manual weight-tuning result, not a derived optimum. The supplementary material includes simulations with different score compositions to show the current combination is better than alternatives — but it is tested on the same targets used for the main results. The weights may not generalise to targets with very different pocket characteristics (e.g. extremely hydrophobic or extremely charged binding sites).

## 3. Formulas and figures unpacked

**Formula: composite scoring function (Eq. 2)**

$$\text{score} = 0.8 \cdot \text{Chai-1} - 0.1 \cdot \text{SA} + 0.05 \cdot \text{ESOL} + 0.05 \cdot \text{QED}$$

| Symbol | Name | Range (normalised) | Coefficient | Sign logic |
|--------|------|-------------------|-------------|------------|
| Chai-1 | AI complex confidence score | ~0.4–0.92 in paper | +0.8 | Higher = more confident = better binding; maximise |
| SA | synthetic accessibility | 0–1 (after normalisation from 1–10) | −0.1 | High SA = hard to make; penalise |
| ESOL | estimated solubility | 0–1 (after normalisation) | +0.05 | High ESOL = more soluble; reward |
| QED | drug-likeness | 0–1 | +0.05 | High QED = more drug-like; reward |

**Worked example:**
A candidate compound has: Chai-1 = 0.88, SA (normalised) = 0.45, ESOL (normalised) = 0.60, QED = 0.82.

$$\text{score} = 0.8 \times 0.88 - 0.1 \times 0.45 + 0.05 \times 0.60 + 0.05 \times 0.82$$
$$= 0.704 - 0.045 + 0.030 + 0.041 = 0.730$$

Compare to a version of the same compound where SA is improved (normalised SA drops to 0.30):

$$\text{score} = 0.704 - 0.030 + 0.030 + 0.041 = 0.745$$

The SA improvement raises the score by 0.015 — enough to tip a borderline Metropolis acceptance. For $\beta = 50$ and $\Delta s = 0.015$: $\exp(-50 \times 0.015) = \exp(-0.75) \approx 0.47$ — a near coin-flip rather than near-certain rejection.

**Connection to Table 2:** In Table 2, Score and Chai-1 score are both reported. The difference between them (Score − 0.8 × Chai-1) reflects the combined effect of the three bias terms. For bromodomain run 1: Score = 0.908, Chai-1 = 0.902; the bias contribution = 0.908 − 0.8 × 0.902 = 0.908 − 0.722 = 0.186. Checking: −0.1 × SA + 0.05 × ESOL + 0.05 × QED = −0.1 × 4.347 (unnormalised) ≈ ... (exact SA normalisation not given; the bias contribution is clearly positive, suggesting ESOL and QED dominate SA penalty for this compound).

> [!Info] SA normalisation
> The paper says "SA and ESOL scores being normalized to also lie between zero and one." The original SA score is 1–10; the normalisation maps 1 → 1 (easy = good = 1) and 10 → 0 (hard = bad = 0). Table 2 reports raw SA scores (4.3–5.0 range), not normalised.

## 4. The argument in one diagram

**Chai-1 confidence = necessary but insufficient** → **Without biases: simulation generates hard-to-make, insoluble, non-drug-like binders** → **SA cap: allow complexity up to a threshold** → **ESOL bias: stay soluble** → **QED bias: stay drug-like** → **Composite score: optimise binding while satisfying practical constraints**

## 5. What is easy to miss

> [!caution] The SA cap is asymmetric
> The SA term does not penalise every increase in complexity — it only penalises complexity above a cap value. This means a very simple molecule gets *zero* SA penalty, but a very complex molecule does. The effect is a soft floor on complexity rather than a soft ceiling. Most readers assume it is a penalty for being hard to make, full stop.

> [!Hint] Weight sum does not equal 1
> The weights (0.8, −0.1, 0.05, 0.05) sum to 0.8 in magnitude for the positive terms. The score is not a probability or a normalised sum. It is a linear combination whose range depends on the input ranges. The Chai-1 confidence score is the dominant driver; the biases are perturbative corrections.

> [!caution] QED molecular weight factor removed
> Standard QED includes a molecular weight desirability function that penalises large molecules. Removing it means the simulation can generate larger compounds than standard QED would prefer. This is intentional but means generated compounds may be larger than the "optimal drug" defined by Lipinski's rules.

> [!question] ESOL vs measured solubility
> ESOL is a predicted solubility from SMILES, not a measured value. Predicted solubility has significant errors (reported RMSE ≈ 0.5–1 log units). Using a predicted quantity as a bias adds uncertainty on top of uncertainty.

## 6. Open questions

- **Author's own caveat:** "Further work may be required to ensure the structures resulting from the simulation are reasonable" regarding SA scores. The SA score is explicitly described as only an estimate.

> [!question] Are the weights universal?
> The score composition was tuned on three targets. A polar pocket might need a higher ESOL weight; a small shallow pocket might need a lower SA cap. Are these weights the right defaults for a new target?

> [!question] Why not include shape complementarity?
> Traditional scoring functions include terms for shape complementarity and hydrogen bonding geometry. The Chai-1 confidence score is a holistic proxy — but could adding explicit geometric terms improve performance?

> [!question] What happens during the cleanup phase?
> The paper later describes a "cleanup phase" (100 steps optimising only SA, ESOL, QED with the constraint that Chai-1 never drops below threshold − 0.02). How much does this change the scores reported in Table 2?

## 7. Connections

**Within the paper**
- Eq. 2 is the target that the Metropolis criterion (Eq. 1) tries to maximise — both equations define the MC algorithm together.
- SA, ESOL, and QED scores for each generated compound are reported in Table 2 — cross-reference these numbers with the formula to verify the bias magnitudes.
- The cleanup phase (§De novo generation, Results) uses a variant of this score with Chai-1 weight effectively zero and biases dominant.

**Outside the paper**
- [[Synthetic accessibility score]] — Ertl & Schuffenhauer 2009; explains the fragment-frequency-based SA calculation.
- [[QED (drug-likeness)]] — Bickerton *et al.* 2012; the original desirability function paper.
- [[pLDDT]] — the per-residue confidence score from which Chai-1's complex confidence is derived; understanding pLDDT helps interpret what Chai-1 actually measures.

## 8. Test yourself

1. **(Recall)** Write Eq. 2 from memory. What are the four terms, their weights, and their signs?
2. **(Recall)** Why is the SA score subtracted rather than added? What range does it take before normalisation?
3. **(Comprehension)** Explain the SA cap in plain English. Why does removing the cap make the simulation converge on simple molecules?
4. **(Comprehension)** Compound A: Chai-1 = 0.90, SA(norm) = 0.50, ESOL(norm) = 0.40, QED = 0.85. Compound B: Chai-1 = 0.87, SA(norm) = 0.10, ESOL(norm) = 0.80, QED = 0.90. Which has the higher score? By how much?
5. **(Application)** You apply AI-MCLig to a membrane protein that requires a hydrophobic, low-solubility ligand. Which bias term is most likely to conflict with this, and how would you adjust it?
6. **(Critical)** The QED molecular weight factor was removed. Under what circumstances could this cause the simulation to generate compounds that would fail in vivo even if they bind the target in silico?
7. **(Critical)** The bias weights were tuned on the same three targets used for Results. What statistical concern does this raise, and how would you address it in a future study?

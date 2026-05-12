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

# Deep Dive: Figure 1 — Chai-1 Confidence vs Experimental Binding Affinity

**Summary**: A reading aid for Figure 1 (p. 5), which shows the empirical correlation between the Chai-1 confidence score and experimental ligand-binding affinity for three target proteins — the foundational justification for using Chai-1 as the MC scoring oracle.

**Paper section**: Results §Correlation of Chai-1 confidence score and ligand binding affinity, p. 4–5

---

## 0. Orientation

Figure 1 is the most important figure in the paper for critical reading. It answers the question: **is the Chai-1 confidence score worth maximising?** The answer is nuanced — on average, higher Chai-1 does predict stronger binding, but the scatter is enormous. The figure has six panels: for each of three targets (bromodomain, Pim-1, p38), there is a raw scatter plot (left) and a binned average ± SD plot (right). The right panels are the key evidence — they show the average trend is real even though individual predictions are noisy. Keep in mind: this figure is the sole empirical basis for trusting Chai-1 as an oracle throughout the rest of the paper.

![[prj.home/OriginalPaper.Structural.GPCR.DeNovoProteinLigand.btag027.pdf#page=5]]
*Figure 1 — Chai-1 confidence score vs experimental binding affinity for bromodomain (A), Pim-1 kinase (B), and p38 kinase (C). Left panels: raw scatter; right panels: binned averages ± SD with linear fit (p. 5).*

## 1. What you need before reading this

- **[[Chai-1]]** — the AI system producing the confidence score on the x-axis.
- **[[pLDDT]]** — the per-atom confidence metric from AlphaFold-class models; the Chai-1 confidence score is derived from this.
- **[[Bromodomain]]** — the epigenetic reader used as Panel A.
- **[[Molecular docking]]** — for context: the correlation of Autodock Vina with affinity is compared to Chai-1 in the surrounding text.

> [!caution] Prerequisite
> If you do not understand what experimental binding affinity (kcal/mol) measures — specifically that more negative = stronger binding — the y-axis will be confusing. A value of −10 kcal/mol is stronger binding than −5 kcal/mol.

## 2. Panel-by-panel annotation

**[Panel A left — Bromodomain scatter]**
- **What it shows:** ~150 data points (individual known bromodomain ligands from PDBbind) with Chai-1 confidence on x-axis (~0.65–0.92) and experimental $\Delta G$ (kcal/mol, approximately −2 to −9) on y-axis.
- **What it means:** The scatter is large. Points at the same Chai-1 score (e.g. 0.80) span a ~4 kcal/mol range of binding affinity. Many individual predictions would be wrong.
- **Critical observation:** There are several compounds with Chai-1 ≈ 0.90 and affinity of only −5 to −6 kcal/mol — the AI is very confident, but the compound binds weakly. This is the "overconfident prediction" regime.

**[Panel A right — Bromodomain binned average]**
- **What it shows:** Data grouped into Chai-1 score bins; mean ± SD for each bin; linear fit $y = -7.80x + 0.74$.
- **What it means:** The average trend is clearly negative (correct sign). At Chai-1 = 0.70, mean affinity ≈ −4.7 kcal/mol; at Chai-1 = 0.90, mean affinity ≈ −6.3 kcal/mol. The SD bars (error bars) are large — typically ±1 to ±2 kcal/mol — confirming substantial uncertainty per bin.
- **Critical observation:** The correlation is weak but real. A Chai-1 score of 0.90 predicts ~1.6 kcal/mol stronger binding than 0.70 on average — this is $K_d$ difference of ~15-fold. Useful for screening populations; unreliable for individual ranking.

**[Panel B left — Pim-1 kinase scatter]**
- **What it shows:** Fewer data points (~40–50) with wider affinity range (−4 to −12 kcal/mol), Chai-1 from ~0.55–0.87.
- **What it means:** The scatter is even larger than bromodomain in absolute terms. Several very high affinity binders (< −10 kcal/mol) exist across the entire Chai-1 range.
- **Critical observation:** There are compounds with Chai-1 < 0.70 and affinity < −10 kcal/mol — the AI is not confident, yet the compound binds very strongly. This represents the worst-case failure mode of using Chai-1 as a proxy.

**[Panel B right — Pim-1 binned average]**
- **What it shows:** Linear fit $y = -14.20x + 3.91$; steeper slope than bromodomain.
- **What it means:** The Pim-1 slope (−14.20) is nearly twice the bromodomain slope (−7.80). This means Chai-1 is more discriminating for Pim-1 — a 0.1 unit change in Chai-1 predicts ~1.4 kcal/mol change in affinity for Pim-1 vs. ~0.78 kcal/mol for bromodomain. The SD bars are very large, reflecting the small number of data points.

> [!caution] Pim-1 has few reference points
> With ~40–50 data points spread across 4–5 bins, each binned average in Panel B may represent only 8–10 compounds. The uncertainty of the binned mean is therefore much larger than the SD bars suggest. The steeper slope should be interpreted with caution — it may partly reflect sampling noise.

**[Panel C left — p38 kinase scatter]**
- **What it shows:** ~30–40 data points, Chai-1 from ~0.5–0.9, affinity from −5 to −10 kcal/mol.
- **What it means:** The smallest dataset of the three. High scatter.

**[Panel C right — p38 binned average]**
- **What it shows:** Linear fit $y = -9.95x + 1.06$; slope intermediate between bromodomain and Pim-1.
- **What it means:** At Chai-1 = 0.80, predicted mean affinity = −9.95 × 0.80 + 1.06 = −6.90 kcal/mol. Consistent with the known range of p38 binders.

## 3. Formulas and figures unpacked

**Linear regression fits (right panels)**

Each right panel shows a fit of the form $y = ax + b$, where $y$ = experimental binding affinity (kcal/mol) and $x$ = Chai-1 confidence score.

| Target | Slope $a$ | Intercept $b$ | Δaffinity per 0.1 Chai-1 |
|--------|-----------|---------------|--------------------------|
| Bromodomain (A) | −7.80 | +0.74 | 0.78 kcal/mol |
| Pim-1 kinase (B) | −14.20 | +3.91 | 1.42 kcal/mol |
| p38 kinase (C) | −9.95 | +1.06 | 1.00 kcal/mol |

**Worked example — Bromodomain:**
A compound scores Chai-1 = 0.87 (typical for a generated compound in Table 2).
Predicted mean affinity: $-7.80 \times 0.87 + 0.74 = -6.79 + 0.74 = -6.05$ kcal/mol.
Expected range (±1 SD): roughly −4 to −8 kcal/mol.
Compare to known bromodomain binders in Table 3: −3.90 to −8.30 kcal/mol — the prediction is within range.

**What the figure does NOT show:**
- No $R^2$ or $p$-value is reported in the paper. The correlation is presented visually without a formal statistical coefficient. This limits quantitative comparison.
- No confidence interval around the fit lines is shown, only ±SD of the binned data.

## 4. The argument in one diagram

**Hypothesis: higher Chai-1 → stronger average binding** → **Test: correlate Chai-1 scores of 100–200 known ligands with experimental $\Delta G$ from PDBbind** → **Result: scatter plots show large individual variance; binned averages show negative trend (correct direction)** → **Conclusion: Chai-1 is a valid but noisy population-level proxy; maximising it increases probability of finding high-affinity compounds but does not guarantee it for any individual compound**

## 5. What is easy to miss

> [!Hint] The right panels (binned averages) are the key evidence
> Many readers focus on the left (scatter) panels and conclude "no correlation." The right panels show the average trend is real. The distinction matters: the method works at the population level (if you generate many compounds and take high-Chai-1 ones, you get a higher average affinity), not at the individual level.

> [!caution] No R² is reported
> The paper presents linear fits but does not report $R^2$, Pearson $r$, or $p$-value for any of the three correlations. This makes it impossible to quantitatively compare the correlations across targets or to assess statistical significance.

> [!caution] The Chai-1 score range in Figure 1 does not fully overlap with the de novo results
> The generated compounds in Table 2 reach Chai-1 = 0.917. Figure 1 shows data up to Chai-1 ≈ 0.92 for bromodomain (Panel A), but only to ~0.87 for Pim-1 (Panel B) and ~0.90 for p38 (Panel C). The correlation at the very high end of the Chai-1 range (the regime where generated compounds live) has the fewest data points and the largest uncertainty.

> [!Hint] The slope difference between targets carries real information
> The steeper slope for Pim-1 (−14.20 vs. −7.80 for bromodomain) could reflect genuine differences in how binding confidence maps to affinity for each target. Pim-1 has a deeper, more directional ATP-binding pocket; tight binding requires very precise contacts that Chai-1 may capture more accurately.

## 6. Open questions

> [!question] What is the R² for each correlation?
> The paper does not report formal correlation coefficients. Computing these from the raw data (available in the supplementary) would allow quantitative comparison across targets and with Autodock Vina.

> [!question] Does the Autodock correlation look the same?
> The text mentions Autodock Vina was "similarly tested" (supplementary Figure 6) and found to have a "similar" but generally worse correlation. Similar in what sense — similar R², similar slope, or similar visual appearance?

> [!question] What happens for GPCR targets?
> Figure 1 shows only bromodomain, Pim-1, and p38. The β-1 adrenergic receptor is used in the de novo design (Table 2) but does not appear in Figure 1. Is the Chai-1 correlation for GPCRs equally reliable?

## 7. Connections

**Within the paper**
- Figure 1 → justification for Eq. 2 scoring function (Chai-1 weight = 0.8)
- Figure 1 → the central caveat of the Conclusion: "only the average confidence score correlates"
- Figure 1 → the motivation for MMGBSA and Boltz-2 as independent validators in Table 2

**Outside the paper**
- [[pLDDT]] — the per-atom confidence score underlying Chai-1's complex confidence; understanding pLDDT's definition (predicted distance difference test) clarifies why it is not a direct binding affinity predictor.
- [[Molecular docking]] — Autodock Vina is compared to Chai-1 here; the comparison shows both are equally imperfect.

## 8. Test yourself

1. **(Recall)** What are the linear fit equations for each of the three panels? What do the slopes mean in words?
2. **(Recall)** Why does the y-axis use negative values for stronger binding? What does a binding affinity of −7 kcal/mol correspond to physically?
3. **(Comprehension)** Why are the right panels (binned averages) more informative than the left panels (scatter plots) for assessing whether Chai-1 is a valid scoring oracle?
4. **(Comprehension)** Compute the predicted mean binding affinity for Pim-1 kinase for a compound with Chai-1 = 0.86. What range of affinities would you expect for individual compounds at this Chai-1 score, given the scatter?
5. **(Application)** You generate 50 compounds using AI-MCLig on a new kinase, all with Chai-1 scores between 0.80 and 0.90. Based on Figure 1 trends, what experimental binding affinity range would you predict for these compounds on average?
6. **(Critical)** No R² or p-value is reported. Sketch what the R² might be for each panel based on the visual scatter. Is the correlation strong enough to rank individual compounds? What is it strong enough for?
7. **(Critical)** Compound X has Chai-1 = 0.91 (high, exceeding most of Figure 1's range) and MMGBSA = −15 kcal/mol (weaker than most reference binders in Table 3). How do you reconcile these two signals? Which do you trust more, and why?

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

# Deep Dive: Figure 3 — Score Trajectory, MMGBSA, and Pocket RMSD Over MC Steps

**Summary**: A reading aid for Figure 3 (p. 9), a three-panel time-series figure that tracks (A) the composite score trajectory over 2000 steps, (B) the MMGBSA score over the first 200 steps, and (C) the pocket RMSD over 2000 steps — together providing the quantitative dynamics of the MC simulation.

**Paper section**: Results §De novo generation of bound ligands using atomistic-step MC simulation, p. 9

---

## 0. Orientation

Figure 3 answers three questions about the simulation dynamics: (A) Does the score converge, and how fast? (B) Is the MMGBSA improving in parallel with the MC score? (C) Does the protein pocket actually move — and how much, and for which targets? The three panels are read together: Panel A shows the primary optimisation signal; Panel B provides early independent validation that the MC score tracks real binding; Panel C provides the direct physical evidence for protein flexibility handling. The single idea: **all three panels agree — rapid early improvement, gradual later refinement, and more flexibility for kinases/GPCR than for bromodomain**.

![[prj.home/OriginalPaper.Structural.GPCR.DeNovoProteinLigand.btag027.pdf#page=9]]
*Figure 3 — (A) Average score vs MC step (running window, 100 steps) for bromodomain (blue), p38 (orange), Pim-1/serine (green). (B) MMGBSA score at each re-evaluation during the first 200 MC steps. (C) Pocket RMSD (non-hydrogen atoms within 5 Å of native ligand) vs MC step (p. 9).*

## 1. What you need before reading this

- **[[Monte Carlo simulation]]** — the algorithm generating the step-by-step trajectory; each x-axis unit is one accepted MC step.
- **[[MMGBSA]]** — the binding free energy estimation in Panel B; more negative = stronger binding.
- **[[Metropolis criterion]]** — the acceptance rule governing whether a step is recorded; the trajectory shows only accepted states, not all proposed states.
- **[[Bromodomain]]** — the rigid test case (blue line in A and C).
- **[[GPCR]]** — the most flexible test case (but labelled "Serine" or Pim-1 in the figure — see note in §5).

> [!caution] Prerequisite
> The y-axis of Panel B (MMGBSA score in kcal/mol) requires knowing the sign convention: more negative = stronger predicted binding. Without this, the downward trend visible in Panel B could be misread as worsening performance.

## 2. Panel-by-panel annotation

**[Panel A — Average score vs MC step (0–2000)]**
- **What it shows:** Three lines (Bromo = blue, P38 = orange, Serine/Pim-1 = green), each showing the running-window average (100-step window) of the composite score (Eq. 2) over 2000 MC steps.
- **What it means:** All three targets show the same qualitative pattern: rapid rise from ~0.4 (benzene baseline) to ~0.7–0.8 within the first 250 steps, then slower improvement toward a plateau near 0.85–0.92.
- **Detailed observations:**
  - *Early phase (0–250 steps):* Score rises steeply. This reflects the large gains available when adding the first few substituents to benzene — any addition that fills a pocket contact improves the score substantially.
  - *Late phase (250–2000 steps):* Score improves more gradually. Most major pocket contacts are already occupied; further improvements require fine-tuning (changing atom types, adjusting bond orders) for smaller marginal gains.
  - *Target differences:* Bromodomain (blue) tends to plateau slightly higher and faster than p38 (orange) and Pim-1 (green) — consistent with the bromodomain having a more rigid, well-defined pocket that is easier for Chai-1 to evaluate confidently.
  - *Fluctuations:* Even with the 100-step running window, fluctuations remain visible. These reflect the stochastic acceptance of slightly worse compounds (Metropolis criterion), which are then reversed when a better step is found.

**[Panel B — MMGBSA score at every 10 MC steps (0–200)]**
- **What it shows:** MMGBSA scores (kcal/mol) recalculated at every 10th MC step for the first 200 steps on three targets. Three separate lines (colours as in A).
- **What it means:** The MMGBSA independently confirms the MC score's trajectory — the binding free energy improves in the first 200 steps in parallel with the Chai-1 composite score.
- **Detailed observations:**
  - *Starting point (~step 0):* MMGBSA starts around −10 to −15 kcal/mol (benzene or near-benzene in the pocket — weak binding consistent with benzene's low potency).
  - *Improvement trend:* For all three targets, the MMGBSA becomes more negative (stronger binding) over the first 200 steps. Bromodomain reaches approximately −25 to −30 kcal/mol; p38 and Pim-1 show wider fluctuations but similar trend.
  - *Large fluctuations:* MMGBSA values fluctuate by 10–20 kcal/mol between adjacent time points. This reflects the coarse sampling of MD trajectories (short MD runs for each intermediate compound) and the inherent noise of MMGBSA on individual structures.
  - *Why only 200 steps:* Computing MMGBSA at every 10th step for 2000 steps would require 200 MD simulations — computationally prohibitive. The first 200 steps are shown as a representative validation window.

> [!Info] Panel B validates Panel A early in the simulation
> The parallel improvement in MMGBSA (Panel B) and composite score (Panel A) during the first 200 steps is the key evidence that the MC is not just optimising an arbitrary proxy — it is genuinely improving predicted binding free energy. This is the main justification for trusting the longer simulation (Panel A) beyond where MMGBSA is computed.

**[Panel C — Pocket RMSD vs MC step (0–2000)]**
- **What it shows:** RMSD (Å) of non-hydrogen protein atoms within 5 Å of the native ligand, versus MC step. Three targets; one design run per target.
- **What it means:** The pocket RMSD measures how much the binding pocket moves during the simulation relative to the starting structure. Higher RMSD = more conformational change = more protein flexibility.
- **Detailed observations:**
  - *Bromodomain (blue):* RMSD stays low, typically 0.3–0.5 Å throughout 2000 steps. The pocket barely moves. This confirms the bromodomain is a rigid pocket — the simulation's flexibility handling is not needed here, but it also does not hurt.
  - *p38 kinase (orange):* RMSD reaches 1.0–1.5 Å at several points and shows more variation. Consistent with experimental data showing p38's activation loop and DFG motif can flex in different ligand-bound states.
  - *Pim-1 kinase (green):* RMSD reaches up to ~2.0 Å at some steps. The most flexible of the three shown. Pim-1 is known to have a glycine-rich P-loop and a hinge region that accommodate diverse ligands with different spatial demands.
  - *Interpretation:* The RMSD is not monotonically increasing — it fluctuates up and down as different ligands induce different conformations. This is the expected behaviour of induced fit: each accepted compound induces a slightly different pocket geometry, and the simulation explores these variations.

## 3. Formulas and figures unpacked

**RMSD formula (Panel C)**

$$\text{RMSD} = \sqrt{\frac{1}{N} \sum_{i=1}^{N} |r_i(t) - r_i(0)|^2}$$

where $N$ = number of non-hydrogen pocket atoms, $r_i(t)$ = position of atom $i$ at MC step $t$, $r_i(0)$ = position at the reference structure (Chai-1 prediction from the starting benzene complex).

| Symbol | Name | Intuition |
|--------|------|-----------|
| $N$ | number of pocket atoms | ~50–150 depending on pocket size |
| $r_i(t)$ | atomic position at step $t$ | changes as each MC step rebuilds the complex |
| RMSD | root-mean-square deviation | average atomic displacement; 1 Å ≈ one C–C bond length |

**Significance of RMSD values:**
- < 0.5 Å: essentially rigid (bromodomain-level)
- 0.5–1.5 Å: modest flexibility (p38-level)
- > 1.5 Å: significant flexibility (Pim-1-level; GPCR would be expected here too)
- > 3 Å: large domain-level rearrangement (not seen in this paper)

**Panel A score range interpretation:**

| Score range | Interpretation |
|-------------|---------------|
| 0.4 (benzene start) | Very low confidence — poor pocket fit |
| 0.70–0.80 (after ~250 steps) | Reasonable fit — major contacts established |
| 0.85–0.92 (final) | High confidence — good pocket complementarity |

From Figure 1 linear fits: a Chai-1 increase from 0.70 to 0.90 (bromodomain) predicts mean affinity improvement of $-7.80 \times 0.20 = -1.56$ kcal/mol. This corresponds roughly to a 14-fold improvement in $K_d$.

## 4. The argument in one diagram

**Panel A: score rises rapidly then plateaus** → **Panel B (first 200 steps): MMGBSA improves in parallel** → **Conclusion: MC score and MMGBSA track each other → early convergence is real** → **Panel C: pocket RMSD shows bromodomain stays rigid; kinases flex up to 2 Å** → **Conclusion: the method models protein flexibility where it exists (kinases), without forcing it where it does not (bromodomain)**

All three panels together: AI-MCLig works quantitatively, validates independently, and demonstrably handles flexibility.

## 5. What is easy to miss

> [!caution] "Serine" in the figure label refers to the serine/threonine kinase (Pim-1)
> The figure legend labels the green line "Serine" — this is shorthand for "serine/threonine-protein kinase Pim-1," not for the bromodomain or p38. This label can confuse readers who expect "Bromo," "P38," and "β-1 receptor" (the four main targets).

> [!caution] Panel B covers only 200 of 2000 steps
> MMGBSA is not tracked for the full simulation in Panel B — only the first 200 steps are shown. This means the final generated compounds in Table 2 (at step 2000) were subjected to separate full MD simulations, not tracked step-by-step. The Panel B validation applies to early convergence, not the endpoint quality.

> [!Hint] The plateau in Panel A has a physical interpretation
> The plateau at steps 250–2000 does not mean the simulation has stopped finding improvements — it means the improvements are small (the compound is already well-fitted) and partially offset by Metropolis-accepted worsening moves. The 100-step running window smooths this out but cannot eliminate the fundamental stochasticity.

> [!Hint] Panel C RMSD is relative to the starting structure, not a crystal
> The reference structure for RMSD is the Chai-1-predicted complex from benzene, not a crystal structure. RMSD from Chai-1 start is not the same as RMSD from the experimental crystal structure. The pocket could move significantly from the crystal while showing low RMSD from the simulation start — or vice versa.

## 6. Open questions

> [!question] Why is the β-1 adrenergic receptor not shown in Figure 3?
> The GPCR is the fourth test target, but Figure 3 shows only three. The supplementary data (Figure 8/9/10) presumably includes GPCR trajectories. The omission means the most flexible target's dynamics are not shown in the main text — which is where flexibility handling is most relevant.

> [!question] How do the three independent runs compare in Panel A?
> Figure 3A shows one design run per target. The three independent runs per target reported in Table 2 may show different convergence trajectories. Showing all three would reveal the variance in convergence.

> [!question] Does RMSD in Panel C reflect induced fit or just thermal fluctuation?
> The RMSD in Panel C changes with each MC step because each step generates a new Chai-1-predicted complex. But Chai-1 predictions have inherent structural variance (the model is stochastic). How much of the RMSD signal is induced-fit response to the ligand vs. Chai-1 prediction variance for the same compound?

## 7. Connections

**Within the paper**
- Panel A plateau behaviour → explanation in text: "improvements become increasingly difficult" → motivation for future smarter MC moves
- Panel B (MMGBSA of early steps) → Table 2 (MMGBSA of final steps, separate full MD) — the two use the same method but different simulation lengths
- Panel C (pocket RMSD) → text discussion of protein flexibility → direct evidence for the paper's central claim

**Outside the paper**
- [[Metropolis criterion]] — the acceptance rule governing the trajectory shape in Panel A; the fluctuations visible even with a 100-step window reflect probabilistic acceptance of worsening moves.
- [[MMGBSA]] — the method behind Panel B; understanding its noise level (~±5 kcal/mol for absolute values) explains the large fluctuations in Panel B.
- [[Induced fit]] — the physical phenomenon Panel C is designed to quantify.

## 8. Test yourself

1. **(Recall)** What does each of the three panels in Figure 3 measure? What is plotted on the x- and y-axes of each?
2. **(Recall)** What are the approximate starting and ending scores in Panel A for the bromodomain? What does each value imply about pocket fitting?
3. **(Comprehension)** Why does the score in Panel A rise steeply in the first 250 steps and then plateau? What chemical process does the steep rise correspond to?
4. **(Comprehension)** Panel B shows MMGBSA improving in parallel with the composite score during the first 200 steps. Why is this parallelism important for the paper's conclusions?
5. **(Comprehension)** The bromodomain RMSD in Panel C stays below ~0.5 Å. Why does the method still produce good results for the bromodomain if the protein is not flexing? Does protein flexibility need to be large for the method to work?
6. **(Application)** You apply AI-MCLig to a new target and observe that Panel A scores plateau at 0.75 after 2000 steps (much lower than the 0.85–0.92 seen in the paper). Panel C shows RMSD consistently above 3 Å. What might these observations suggest about the target or the simulation?
7. **(Critical)** Panel B covers only the first 200 of 2000 steps. The final compounds in Table 2 were generated over 2000 steps but MMGBSA was only computed separately (not at each step). Is it valid to assume the MMGBSA continues to improve after step 200, given Panel B shows improvement only in the first 200?
8. **(Critical)** The RMSD in Panel C is computed relative to the Chai-1 prediction from benzene, not relative to a crystal structure. Under what conditions could this give a misleading picture of how much the pocket has moved from its experimentally observed conformation?

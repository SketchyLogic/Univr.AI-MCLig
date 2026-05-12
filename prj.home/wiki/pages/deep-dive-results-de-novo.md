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

# Deep Dive: Results — De Novo Generation and Fragment-based MC

**Summary**: A reading aid for the core positive results: AI-MCLig generating novel drug-like compounds on four targets with MMGBSA and Boltz-2 scores matching known binders, plus the protein flexibility analysis (RMSD) and the fragment-based MC variant results.

**Paper section**: Results and discussion §De novo generation of bound ligands using atomistic-step MC simulation and §Fragment-based MC simulations, pp. 5–9

---

## 0. Orientation

This is the payoff section — the section the entire paper is built to support. The key claim: AI-MCLig generates compounds that score as well as experimentally validated binders, across four pharmacologically relevant target proteins, using two independent scoring schemes (MMGBSA and Boltz-2) that were not used during design. A secondary result confirms that protein flexibility genuinely changes during the simulation (Figure 3C RMSD data), which justifies the central design choice of rebuilding the complex at each step. The fragment-based MC results round out the section as a complementary protocol with similar performance. The single idea to hold: **scores of generated compounds overlap with scores of known binders on the same targets — that overlap is the validation**.

## 1. What you need before reading this

- **[[Chai-1]]** — the scoring oracle during design; its confidence score is what is being maximised.
- **[[MMGBSA]]** — the molecular-dynamics-based independent validator; scores in Table 2 come from this.
- **[[Boltz-2]]** — the AI-based independent validator; scores in Table 2 also come from this.
- **[[Bromodomain]]** — the rigid-pocket test case.
- **[[GPCR]]** — the most flexible test case (β-1 adrenergic receptor).
- **[[BRICS decomposition]]** — the fragment decomposition used in the fragment-based protocol.
- **[[SMILES]]** — each generated compound is reported as a SMILES string in Table 2.

> [!caution] Prerequisite
> Table 2 is the central result — but it is incomprehensible without knowing what MMGBSA and Boltz-2 measure and why scores in the range −20 to −57 kcal/mol are meaningful. Read [[MMGBSA]] and [[Boltz-2]] before this section.

## 2. Paragraph-by-paragraph annotation

**[¶1 — Four targets and simulation setup]**
- **What it says:** Atomistic-step MC was applied to the bromodomain, serine/threonine-kinase proteins (p38 and Pim-1), and the β-1 adrenergic receptor (a 7-helix GPCR); each simulation consisted of 2000 MC steps, β = 50, starting from benzene; three independent MC runs were performed per target.
- **Why it matters:** Establishes the experimental design: 4 targets × 3 runs = 12 compound series; the diversity of target types (rigid epigenetic reader, two kinases, GPCR) is the key to generality.
- **Plain-language expansion:** The four targets span a range of structural and pharmacological complexity: (a) Bromodomain — a relatively rigid pocket that binds acetylated lysine residues; (b) p38 MAP kinase — an ATP-binding kinase with some flexibility; (c) Pim-1 serine/threonine kinase — similar to p38; (d) β-1 adrenergic receptor — a [[GPCR]] with a transmembrane helical bundle, the most flexible of the four. Three independent runs per target each start from a different random seed, producing three structurally distinct compounds per target that should all score well. Diversity of the three compounds within a target is expected (stochastic search = different endpoints) and is part of the result — the method does not converge on one solution.

**[¶2 — Figure 2: ligand evolution]**
- **What it says:** Figure 2 illustrates the progressive chemical changes of the ligand structure over the first 100 MC steps for the bromodomain (A–F sticks, G–I van der Waals spheres) and Pim-1 (J–L van der Waals spheres); the method progressively fills the protein pocket.
- **Why it matters:** Figure 2 is the most visually compelling result — it shows the method in action, not just its endpoint.
- **Plain-language expansion:** Panels A–F show the ligand growing from benzene (step 0) through intermediate structures at steps 20, 40, 60, 80, to a more complex compound at step 100 — each panel shows the ligand in a stick representation inside the bromodomain pocket (protein shown as green cartoon). Panels G–I switch to van der Waals sphere representation, showing how the starting benzene barely occupies the pocket (G) while the 200-step compound (H) and the 2000-step final compound (I) fill it much more completely. Panels J–L show the same progression for Pim-1, demonstrating the approach generalises. See [[deep-dive-figure-2]] for the full panel-by-panel analysis.

**[¶3 — Compounds reach high Chai-1 scores]**
- **What it says:** The finally generated compounds are bound to the target protein with a high Chai-1 confidence score (Table 2); scores improve rapidly within the first 250 steps, then more gradually.
- **Why it matters:** Confirms that the MC optimisation converges and produces compounds the scoring oracle rates highly.
- **Plain-language expansion:** Table 2 shows Chai-1 scores of 0.837–0.917 for generated compounds. The score trajectory (Figure 3A) shows rapid improvement initially — the simulation quickly escapes the low-scoring benzene and adds substituents that fill key pocket contacts — then more gradual gains as the chemical space of further improvements becomes sparser. The plateau behaviour reflects diminishing returns: after the first few hundred steps, the simulation is already near the local optimum. This is consistent with the acceptance rate of 10–20% for an already-good compound leaving little room for improvement.

**[¶4 — MMGBSA validation: comparable to known binders]**
- **What it says:** The generated compounds show MMGBSA scores in Table 2 (−19 to −57 kcal/mol) comparable to experimentally validated ligands for each target (Table 3); the compounds reach this range for all four test proteins.
- **Why it matters:** This is the primary validation claim — two independent numbers (Chai-1 and MMGBSA) agree, and MMGBSA overlaps with experimentally confirmed binders.
- **Plain-language expansion:** Table 2 (generated) vs. Table 3 (known): for bromodomain, generated MMGBSA = −19 to −30 kcal/mol; known binders = −20 to −39 kcal/mol. For p38, generated = −34 to −42 kcal/mol; known = −35 to −50 kcal/mol. For Pim-1, generated = −31 to −35 kcal/mol; known = −30 to −46 kcal/mol. For β-1 receptor, generated = −32 to −57 kcal/mol; known = −24 to −34 kcal/mol (only two reference compounds). The ranges overlap in all cases, supporting the claim that generated compounds are potential binders. Note: overlap is not identity — some generated compounds score lower than the best known binders.

> [!caution] β-1 receptor MMGBSA comparison is weak
> Table 3 has only two known β-1 receptor binders (3zpr: −34.36, 3zpq: −24.02 kcal/mol). The generated compounds reach −56.57 kcal/mol (run 3), which is outside the range of reference compounds entirely. This could indicate the generated compound is an exceptional binder — or that the MMGBSA calculation for this flexible GPCR has larger errors than for the rigid targets.

**[¶5 — Protein flexibility during simulation (RMSD)]**
- **What it says:** The variation in the protein structure depends significantly on the protein target; no major structural changes for bromodomain; larger variations for kinases and GPCR (Figure 3C).
- **Why it matters:** Directly validates the claim that the method accounts for protein flexibility — the pocket actually moves during simulation.
- **Plain-language expansion:** Figure 3C plots RMSD of non-hydrogen pocket residues (within 5 Å of native ligand) versus MC step. Bromodomain RMSD stays below ~0.5 Å — confirming it is rigid. p38 and Pim-1 reach 1–2 Å RMSD — moderate flexibility consistent with kinase literature. The pocket adapts differently for different generated ligands, which is exactly the induced-fit behaviour the method is designed to model. Even for the bromodomain, small changes (~0.3 Å) can be critical for correct placement evaluation — rigid docking would miss these.

**[¶6 — Binding mode analysis: LigPlot and UMAP]**
- **What it says:** Generated compounds for the bromodomain form similar hydrogen bond and contact patterns to known binders; UMAP analysis shows generated ligands are in all cases close to clusters representing known compounds in chemical space.
- **Why it matters:** Shows not just that scores match, but that the binding mechanism is similar — the generated compounds interact with the protein in the same way as known drugs.
- **Plain-language expansion:** LigPlot+ (Laskowski and Swindells 2011) generates 2D diagrams of protein–ligand interactions (hydrogen bonds, hydrophobic contacts). The authors found all MC-generated bromodomain compounds make similar hydrogen bonds to protein residues and similar contacts to surrounding chains — despite being chemically different from known binders. This suggests the method finds real pocket interactions, not just high-confidence Chai-1 structures without meaningful contacts. The UMAP (McInnes *et al.* 2018) analysis maps all compounds into a 2D chemical space projection: generated ligands cluster near known binders for all four targets, confirming chemical diversity without departing from druglike chemical space.

**[¶7 — Cleanup phase]**
- **What it says:** A 100-step cleanup phase was added: score = only SA + ESOL + QED, with the constraint that Chai-1 never drops below threshold − 0.02; SA, ESOL, QED scores improve without major Chai-1 penalty.
- **Why it matters:** The cleanup phase post-processes the best compound to improve its drug-like properties without sacrificing binding.
- **Plain-language expansion:** After the main simulation, the best compound may still have avoidable synthetic complexity or poor solubility. The cleanup phase optimises only the practical property scores for 100 more steps, accepting any change that keeps Chai-1 within 0.02 of the maximum found during the main simulation. The threshold (−0.02) was set to the starting Chai-1 minus 0.02 — a soft floor on binding quality. This is a post-processing step, not part of the main MC; it does not change the binding mode but refines the compound's drug-like character.

### §Fragment-based MC simulations

**[¶8 — Fragment-based recovery and random fragments]**
- **What it says:** Known ligands decomposed into BRICS fragments and recombined in a 500-step simulation (β = 5) successfully recovered target compounds; 50 random ChEMBL fragments were tested in 1000-step simulations; results are as good as atomistic-step MC.
- **Why it matters:** Shows fragment-based MC is a validated alternative with complementary strengths.
- **Plain-language expansion:** Recovery with known-ligand fragments: 500 steps, β = 5. The stochastic nature means the exact compound is not always recovered but very similar ones are — consistent with the fragment-MC not being a deterministic assembly. Random ChEMBL fragments: fragments drawn randomly from a public chemical database rather than specifically selected for the target. The key finding: performance was "as favorable as for the atomistic-step MC simulations" — the method is robust to the fragment source. This is slightly surprising: random fragments should perform worse than target-specific ones, but the MC optimisation compensates.

**[¶9 — Target-specific fragments improve performance]**
- **What it says:** Using fragments specifically decomposed from known binders for the selected target protein resulted in significantly improved scores compared to random fragments.
- **Why it matters:** Demonstrates that prior knowledge can be incorporated to accelerate the search — a practical design handle for real drug discovery contexts.
- **Plain-language expansion:** Target-specific fragments come from decomposing known active ligands for that specific target. They already contain chemical substructures that the target recognises — so the MC starts with a head start. Comparing Table 7 (supplementary, target-specific) to Table 6 (supplementary, random fragments): the scores are significantly better with target-specific fragments. Generated compounds have MMGBSA and Boltz-2 scores in the range of known experimental binders. This makes sense: if you decompose known binders and recombine their pieces, you recover something resembling those binders — but because BRICS decomposition and MC recombination are stochastic, you also generate novel combinations.

> [!Hint] Target-specific fragments = hybrid of structure-based and ligand-based design
> Using known binder fragments is not purely de novo design — it is fragment-based drug design guided by prior knowledge. The novelty is that these fragments are recombined by an MC loop scored by an AI oracle that handles protein flexibility. This is a genuine bridge between classical fragment-based drug design and AI-based structure prediction.

## 3. Formulas and figures unpacked

**Score trajectory (Figure 3A conceptual)**

The Chai-1 + bias composite score starts at ~0.4 (benzene), rises sharply in the first ~250 steps, then plateaus near 0.85–0.92. The rapid rise reflects that adding any ring substituent to benzene dramatically improves pocket contacts; subsequent improvements require more precise chemical tuning.

The plateau slope differs by target — bromodomain plateaus faster (rigid pocket, easier to fill) while GPCR plateaus later and at a slightly lower level (flexible pocket, harder to navigate).

**MMGBSA trajectory (Figure 3B conceptual)**

For the first 200 steps, MMGBSA (recalculated at every 10th step for three targets) shows a generally improving trend, with significant fluctuations — the stochastic nature of MC means individual structures can be worse than the local best, even though the accepted trend is upward. The bromodomain reaches −25 to −30 kcal/mol quickly; p38 and Pim-1 show wider fluctuations.

**RMSD vs step (Figure 3C)**

The pocket RMSD values tell the protein flexibility story quantitatively:
- Bromodomain: RMSD ≈ 0.3–0.5 Å — essentially rigid
- p38 kinase: RMSD up to ~1.5 Å — moderate flexibility
- Pim-1 kinase: RMSD up to ~2.0 Å — highest flexibility

For reference: 1 Å ≈ the covalent radius of carbon; a 2 Å RMSD means key residues have shifted by about one bond length. This is enough to change whether a ligand fits.

## 4. The argument in one diagram

**Start benzene on 4 targets** → **2000 MC steps, β = 50, Chai-1 scoring** → **Chai-1 scores 0.84–0.92 for all runs** → **Pocket RMSD shows flexibility was modelled** → **MMGBSA scores overlap with known binder range (Table 3)** → **Boltz-2 scores also favorable** → **Binding mode similar to known binders (LigPlot, UMAP)** → **Conclusion: AI-MCLig generates plausible novel binders on diverse targets**

Fragment-based arm: **BRICS fragments from ChEMBL or known binders** → **1000-step simulation** → **Similar performance to atomistic-step; target-specific fragments significantly better**

## 5. What is easy to miss

> [!caution] Three independent runs per target is small statistics
> The paper reports 3 compounds per target (3 independent MC runs). For a stochastic method, 3 runs gives a rough sense of outcome diversity but is not a large sample. Reporting mean ± SD of 3 makes the uncertainty hard to evaluate.

> [!Hint] Figure 3C is the evidence for the key mechanistic claim
> The entire paper's claim about "protein flexibility and conformational adaptation" rests primarily on Figure 3C (RMSD data). The claim is that the method handles flexibility — the evidence is that the pocket RMSD changes during the simulation. This is necessary but not sufficient: the RMSD might change due to random protein fluctuations, not specifically induced by the ligand.

> [!caution] UMAP is a visualisation, not a proof
> The UMAP analysis shows generated ligands are in clusters near known binders. UMAP is a dimensionality reduction technique that distorts distances in non-linear ways. "Close to clusters" in UMAP space does not have a quantitative meaning in terms of actual chemical similarity.

> [!caution] β-1 receptor has the fewest reference compounds
> Table 3 has only 2 known β-1 receptor binders (3zpr, 3zpq) — the weakest validation baseline. The MMGBSA of the best generated compound (−56.57 kcal/mol) is much stronger than either reference, which could indicate superior binding or simply simulation error on this flexible target.

## 6. Open questions

- **Author's own caveat:** "Even with these improvements, the SA score is only an estimate and does not guarantee that resulting structures can be synthesised with reasonable effort."

> [!question] Would experimental testing confirm the generated compounds bind?
> The paper validates in silico using MMGBSA and Boltz-2 — no experimental binding measurement (ITC, SPR, fluorescence polarization) is reported. Without this, the claim that generated compounds are "potential binders" is entirely computational.

> [!question] Is the UMAP result meaningful for the GPCR?
> UMAP clustering near known binders means more for the bromodomain (5 reference compounds) than for the β-1 receptor (2 reference compounds). Can you cluster meaningfully near just two points?

> [!question] Why not compare to a rigid-docking control?
> The paper does not show a side-by-side comparison of AI-MCLig results with a rigid-docking approach on the same targets. Such a comparison would directly demonstrate the advantage of the flexibility-aware approach.

## 7. Connections

**Within the paper**
- Table 2 (generated) vs. Table 3 (reference) — the core comparison; this entire section builds to this cross-table comparison.
- Figure 3A (score trajectory) → Figure 3B (MMGBSA trajectory) → Figure 3C (RMSD) — together these show the simulation converges, validates independently, and models flexibility.
- Fragment-based results → Methods §Fragment-based MC → [[BRICS decomposition]].

**Outside the paper**
- [[MMGBSA]] — the physics-based validation method; Miller *et al.* 2012 is the Amber implementation reference.
- [[Boltz-2]] — AI-based affinity predictor; Passaro *et al.* 2025 for accuracy characterisation.
- [[GPCR]] — the β-1 adrenergic receptor is a 7-transmembrane helix bundle; its structural complexity makes it a stringent test case.

## 8. Test yourself

1. **(Recall)** How many MC steps, runs, and targets are used in the atomistic-step de novo design? What is β?
2. **(Recall)** What are the MMGBSA score ranges for generated compounds (Table 2) and known binders (Table 3) for the bromodomain?
3. **(Comprehension)** Explain what Figure 3C (RMSD vs step) shows and why it is evidence for the method's flexibility advantage.
4. **(Comprehension)** Why does the score trajectory in Figure 3A rise quickly then plateau? What does the plateau imply about the difficulty of further improvement?
5. **(Application)** You run three AI-MCLig simulations on a new target and get Chai-1 scores of 0.85, 0.87, and 0.89 but MMGBSA scores of −12, −15, and −11 kcal/mol. Known binders have MMGBSA scores of −35 to −45 kcal/mol. How do you interpret this discrepancy?
6. **(Application)** You want to run fragment-based MC on a kinase target. Should you use random ChEMBL fragments or target-specific fragments? Justify your choice based on the paper's findings.
7. **(Critical)** The UMAP analysis shows generated ligands cluster near known binders. A sceptic argues this just shows the generated compounds are "drug-like," not that they specifically bind this target. How would you respond?
8. **(Critical)** The β-1 receptor generated compound (run 3) shows MMGBSA = −56.57 kcal/mol, much stronger than the two reference compounds in Table 3 (−34, −24 kcal/mol). Is this more likely a genuine discovery or an artefact? What experiment would distinguish the two?

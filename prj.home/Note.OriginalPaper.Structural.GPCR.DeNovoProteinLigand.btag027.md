# Note: De Novo Protein–Ligand Design Including Protein Flexibility and Conformational Adaptation (AI-MCLig)

## 1. At a Glance

- **Type:** Original Research
- **Field:** Structural Bioinformatics – De Novo Drug Design
- **Core question:** Can a Monte Carlo search through chemical space, scored by an AI structure predictor that fully rebuilds the protein at every step, generate novel drug-like ligands for a flexible protein target?
- **Core answer:** Yes — AI-MCLig uses [[Chai-1]] to rebuild the protein-ligand complex at each [[Monte Carlo simulation]] step, allowing both ligand and protein to adapt, and produces novel compounds with binding scores comparable to experimentally known binders on four test targets.

---

## 2. Background you need

**The drug discovery starting point**
Most drugs are small organic molecules that bind tightly to a specific protein target and interfere with its function. Finding such molecules is the central challenge of drug discovery. One approach is *de novo* design: generating entirely new molecules from scratch, computationally, rather than screening millions of existing compounds.

**The rigid protein problem**
Traditional [[Molecular docking]] places a small molecule into a protein's binding pocket and scores how well it fits. A major limitation: most docking programs treat the protein as rigid. In reality, proteins are flexible — when a ligand binds, the pocket changes shape to accommodate it (this is called [[Induced fit]]). Ignoring this leads to incorrect predictions, especially for pharmacologically important targets like kinases and [[GPCR]]s.

**AI structure predictors as a new scoring tool**
[[AlphaFold]] and its successors (AlphaFold3, [[Chai-1]], Boltz-1) can predict the 3D structure of a protein-ligand complex directly from the protein sequence and a ligand description in [[SMILES]] notation. Crucially, because these models predict structure from scratch each time, they implicitly model protein flexibility — the pocket "re-adjusts" for each new ligand. The Chai-1 model produces a confidence score ([[pLDDT]]) for each complex, which correlates loosely with binding affinity.

**Monte Carlo optimisation**
[[Monte Carlo simulation]] is a stochastic search method: make a random change, evaluate the result, and accept or reject based on a probabilistic rule. Over many steps it converges toward good solutions without needing gradients. It is well-suited for rough, multi-modal landscapes — like chemical space, where similar structures can have very different binding affinities.

---


## 3. What they did (Methods overview)

The method separates into two independent components: the [[MC Search Components|chemical move set and the scoring function]].

1. **Start from benzene** — the simplest aromatic compound — as the initial ligand [[SMILES]].
2. **At each MC step, randomly modify the compound** using one of 9 chemical operations (add small group, remove atom, change atom type, add atom in chain, change bond type, form ring, break ring, turn ring aromatic, rearrange bonds). Each operation has a tuned probability.
3. **Rebuild the entire protein-ligand complex using [[Chai-1]]** from scratch at every step: the protein sequence + ligand SMILES → Chai-1 → 3D complex + confidence score.
4. **Score the new complex:**
   `score = 0.8 × Chai-1 − 0.1 × SA + 0.05 × ESOL + 0.05 × QED`
   where SA = [[Synthetic accessibility score]], ESOL = predicted solubility, [[QED (drug-likeness)]] = drug-likeness. These biases steer the simulation toward synthesisable, soluble, drug-like molecules.
5. **Accept or reject** the new compound using the Metropolis criterion: always accept improvements; accept worsening moves with probability exp(−β·Δs), where β controls selectivity.
6. **Second protocol (fragment-based):** Instead of atom-level changes, recombine whole molecular fragments using the [[BRICS decomposition]] method (decompose known ligands into fragments, then add/remove fragments in MC steps).
7. **Validate** final compounds with [[MMGBSA]] (molecular dynamics-based binding energy estimate) and [[Boltz-2]] (an independent AI affinity predictor).

---



## 4. Key findings

- **Recovery test passes:** When [[Dice Similarity vs Chai-1 Scoring|dice similarity]] to a target ligand is used as the score (instead of Chai-1), the MC procedure successfully rediscovers the target compound in most cases — a two-stage protocol described in [[AI-MCLig Validation Strategy]] (Table 1: dice scores = 1.0 or near-1.0), confirming the chemical search works correctly.
- **Generated compounds score like known binders:** For all 4 targets (bromodomain, p38 kinase, Pim-1 kinase, β-1 adrenergic receptor), the final Chai-1 scores (0.85–0.92) and [[MMGBSA]] scores (−19 to −57 kcal/mol) are in the same range as experimentally validated binders in Table 3.
- **Protein flexibility matters:** The binding pocket RMSD during simulation is small for the [[Bromodomain]] (rigid pocket) but larger for the kinases and GPCR, matching known experimental variability. The method handles both.
- **Fragment-based MC also works:** Decomposing known ligands into [[BRICS decomposition]] fragments and running fragment-level MC produces compounds with similarly good scores.
- **Boltz-2 validation agrees:** The independent Boltz-2 affinity model assigns favourable predicted IC50 values to the MC-generated compounds, consistent with the Chai-1 scores.

---

## 5. Limitations and open questions

- Each MC run takes ~20 hours on an NVIDIA RTX4090 (2000 steps); computationally expensive for routine screening.
- The Chai-1 confidence score is only loosely correlated with experimental binding affinity (Figure 1 shows average trends but substantial scatter) — the scoring function is the main bottleneck.
- The [[Synthetic accessibility score]] is an estimate; the actual synthesisability of generated compounds must be validated by a chemist.
- The fragment library for fragment-based MC is randomly selected from ChEMBL; better results may come from target-specific fragment libraries, which requires prior knowledge of known binders.

---

## 6. Why it matters

AI-MCLig demonstrates that AI structure predictors like Chai-1 can function as on-the-fly scoring oracles in generative drug design, enabling protein flexibility to be modelled for free at each MC step. This is a conceptually distinct approach from both traditional rigid-pocket docking and deep generative models (VAEs, diffusion models): it is simple, interpretable, and easily adapted to specific needs (e.g. optimising side chains only, or fixing part of the molecule). As AI complex predictors improve, so does the quality of this approach.

---

## 7. Presentation plan

1. **"Designing a key for a lock that moves"** — open with the drug design analogy, then immediately complicate it: the lock (protein pocket) changes shape depending on the key. Why does this matter?
2. **The rigid protein problem in docking** — one slide: what docking does, what induced fit is, why neglecting it causes errors. Highlight GPCRs and kinases as notorious examples.
3. **AI structure prediction as a new tool** — Chai-1 / AlphaFold3: from sequence + SMILES → 3D complex in seconds. Show a schematic of the prediction.
4. **Monte Carlo optimisation** — the intuition: a random walk in chemical space that is guided toward good-scoring compounds. Analogy: a blind hiker on a landscape who keeps moving uphill.
5. **AI-MCLig pipeline** — walk through Figure 2 (snapshots of the ligand evolving over MC steps); show how benzene grows into a complex drug-like molecule over 100 steps. This is the most visually compelling result.
6. **Validation results** — show Table 2 vs. Table 3: the generated compounds' MMGBSA scores overlap with known experimental binders. Emphasise this as the key evidence.
7. **Fragment-based variant** — briefly explain BRICS decomposition; show that this approach also works and is complementary.
8. **Limitations and future** — the scoring gap, compute cost, and what better AI scoring would unlock.

> **Figure to show:** Figure 2 (ligand evolution over MC steps — visually striking, shows the method in action).

---

## 8. Demonstration plan

**Goal:** Run a small-scale AI-MCLig simulation and observe how a compound evolves over MC steps.

**Tool / resource:** AI-MCLig source code at `https://github.com/JakobAgamia/AI-MCLig` and Zenodo dataset at `https://doi.org/10.5281/zenodo.17800140`

**Steps:**
1. Clone the repository and follow the installation instructions (Python environment with rdkit, Chai-1 dependency).
2. Start with the provided bromodomain example (smallest target, rigid pocket, fastest to run).
3. Run a short simulation (100–200 steps) to observe the score trajectory (Figure 8 equivalent) — does the Chai-1 score improve monotonically or fluctuate?
4. Inspect the SMILES of the final compound and paste it into a molecular viewer (e.g. ChemDraw or Avogadro) to visualise the structure.
5. Calculate the QED score of the generated compound using rdkit: `from rdkit.Chem import QED; QED.qed(mol)`. Compare to the QED of a known bromodomain inhibitor (e.g. JQ1, SMILES available from ChEMBL).
6. If compute permits: run 3 independent simulations and compare the final compounds — do they converge to similar structures?

**Expected outcome:** The Chai-1 score should rise from ~0.4 (benzene) to ~0.85–0.90 within 200 steps. The final compound should visually resemble a drug-like aromatic molecule with substituents filling the bromodomain pocket. The three independent runs should give structurally diverse but similarly-scoring compounds.

---

## 9. Continue from here

**Go deeper into this paper**
- Inspect Table 1 carefully: identify the two cases where the dice score did not reach 1.0 (the cyclic/complex compounds). Understand [[Ring Local Minima in MC|why ring structures create local minima]] in the MC landscape — and why this is a limitation of the move set, not of Chai-1 (see [[AI-MCLig Validation Strategy]]).
- Run the Boltz-2 model (available at `https://github.com/jwohlwend/boltz`) on one of the generated SMILES from Table 2 and verify the predicted IC50 is consistent with the paper.
- Compare the MC score trajectory in Figure 3A for bromodomain vs. Pim-1 kinase — what does the difference in plateau height tell you about the difficulty of each target?

**Next papers to read**
- **Chai Discovery 2024** (bioRxiv) — the Chai-1 paper: understand how this model predicts protein-ligand complexes and what pLDDT measures.
- **Tang et al. 2024a** (Brief Bioinform) — review of generative AI for de novo drug design; provides context for where AI-MCLig sits in the landscape of methods.
- **Ślędzand Caflisch 2018** (Curr Opin Struct Biol) — protein structure-based drug design overview; foundational reading for understanding the field AI-MCLig is entering.
- **Passaro et al. 2025** (preprint) — Boltz-2: the independent validator used in the paper; understand its accuracy and limitations.

**Research or development directions**
- Apply AI-MCLig to a GPCR target of your choice (e.g. the adenosine A2A receptor, which has well-characterised ligands and a flexible binding pocket); compare generated compounds to known A2A antagonists.
- Explore replacing Chai-1 with Boltz-1 (open-weight alternative) as the scoring oracle and benchmark whether the diversity and quality of generated compounds changes.
- Implement a fragment library derived from known GPCR ligands (available from GPCRdb) and test whether target-specific fragments accelerate convergence in the fragment-based MC protocol.

---

## 10. Glossary

- [[AI-MCLig Validation Strategy]]
- [[AlphaFold]]
- [[BRICS decomposition]]
- [[Boltz-2]]
- [[Bromodomain]]
- [[Chai-1]]
- [[De novo drug design]]
- [[Dice Similarity vs Chai-1 Scoring]]
- [[GPCR]]
- [[Induced fit]]
- [[MC Search Components]]
- [[Metropolis criterion]]
- [[MMGBSA]]
- [[Molecular docking]]
- [[Monte Carlo simulation]]
- [[pLDDT]]
- [[QED (drug-likeness)]]
- [[Ring Local Minima in MC]]
- [[SMILES]]
- [[Synthetic accessibility score]]


# Riflessioni e domande
1. Searching for maximum affinity ligand-protein is not always the functional requirements. Vedi es. emoglobina-ossigeno. How is this taken in consideration?
2. Chiedere al prof una coppia proteina ligando da testare
3. define: dice similarity to a known target molecule
4. why it is called "dice similarity"?
5. come fanno a sapere che il compond usato per validare è 'il migliore'? I risultati non uguali a dice sono effettivamente sempre peggiori?
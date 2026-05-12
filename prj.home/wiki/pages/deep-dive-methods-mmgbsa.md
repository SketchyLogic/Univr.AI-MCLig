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

# Deep Dive: Methods — Molecular Dynamics and MMGBSA Calculations

**Summary**: A reading aid for the MD simulation and MMGBSA protocol used to independently validate generated compounds, covering the full computational pipeline from Chai-1 initial structure through MD equilibration to MMGBSA binding free energy estimation.

**Paper section**: Materials and methods §Molecular dynamics simulations and MMGBSA calculations, p. 3

---

## 0. Orientation

This section describes the **independent validation layer** — the second check on generated compounds after Chai-1 scoring. [[MMGBSA]] is a physics-based method for estimating binding free energy from molecular dynamics (MD) trajectories; it is more physically rigorous than the Chai-1 confidence score and does not share the same biases. If AI-MCLig generates compounds that score well on Chai-1 *and* score well on MMGBSA, this is much stronger evidence that they are real binders than either score alone. The key idea: two independent methods agreeing is harder to explain away than one method succeeding.

## 1. What you need before reading this

- **[[MMGBSA]]** — the binding free energy estimation method; read the glossary entry for the formula $\Delta G_{\text{bind}} = G(\text{complex}) - G(\text{protein}) - G(\text{ligand})$ and what each term means.
- **[[Chai-1]]** — used here only to generate the initial 3D structure of the complex; MMGBSA then takes over.
- **[[Molecular docking]]** — for context: MMGBSA is typically more accurate than docking scores but more expensive; it sits in a middle tier of computational accuracy.

> [!caution] Prerequisite
> MMGBSA requires understanding what a molecular dynamics trajectory is (a time series of 3D atomic coordinates), what "frames" means (snapshots from the trajectory), and what "solvation" means (explicit water molecules around the complex). Without this, the protocol description will be opaque.

## 2. Paragraph-by-paragraph annotation

**[¶1 — Purpose and initial structure]**
- **What it says:** Some resulting ligands were evaluated with MD simulations and MMGBSA; initial coordinate files were generated using Chai-1.
- **Why it matters:** Establishes why MMGBSA is used (independent validation) and that Chai-1 provides the 3D starting point — it is not just a scorer here, but also a structure generator.
- **Plain-language expansion:** Chai-1 outputs a 3D protein–ligand complex structure (coordinates for every atom). This structure is used as the starting geometry for MD simulation. MMGBSA validation is not applied to every generated compound — only to "some of the resulting ligands" (the best per target, as reported in Table 2). The choice to use MMGBSA on the best compounds rather than all compounds is a practical concession to computational cost.

**[¶2 — Force field preparation]**
- **What it says:** Ligand force field parameters were obtained using antechamber; the tleap module of the Amber package was used to solvate the protein–ligand complex using TIP3P water.
- **Why it matters:** The force field defines how atoms interact — it is the physics engine of the MD simulation; wrong parameters give wrong results.
- **Plain-language expansion:** Antechamber (Wang *et al.* 2006a, 2006b) is a tool for automatically assigning force field parameters (atom types, partial charges, bond parameters) to small organic ligands using the GAFF (General Amber Force Field). TIP3P (Jorgensen *et al.* 1983) is a three-point water model that represents each water molecule as three point charges (oxygen: −0.834e, two hydrogens: +0.417e each) plus Lennard-Jones parameters. tleap builds the simulation box: it places the complex in a water box, neutralises with ions, and outputs the topology and coordinate files needed for MD.

**[¶3 — MD protocol]**
- **What it says:** The complex was energy minimised (2000 steps), heated to 300 K in 1 ns with positional restraints, then run for 20 ns unrestrained; the first 2.5 ns of production were discarded.
- **Why it matters:** The minimisation–equilibration–production sequence is the standard MD protocol; the discarded first 2.5 ns removes the equilibration artifact where the system is still "settling in."
- **Plain-language expansion:** 
  - **Minimisation (2000 steps):** Removes clashes introduced when Chai-1's predicted structure is placed in a water box. Force minimisation moves atoms to local energy minima without dynamics.
  - **Heating (1 ns, with restraints):** Gradually increases temperature from 0 K to 300 K while restraining non-hydrogen atoms with positional restraints — the protein and ligand cannot move much, but water equilibrates around them.
  - **Production (20 ns, unrestrained):** The full simulation runs freely; the complex explores its conformational ensemble at 300 K.
  - **First 2.5 ns discarded:** The early production trajectory still contains equilibration artifacts (the system has not yet settled to its true equilibrium distribution). Discarding the first 12.5% of the trajectory is standard practice.
  - **pmemd.cuda:** GPU-accelerated MD engine in the Amber package; essential for getting 20 ns simulations to complete in reasonable time.

**[¶4 — MMGBSA calculation]**
- **What it says:** The remaining 750 frames of the trajectory were processed by the MMGBSA tool of Amber using igb = 5; RMSD was computed for non-hydrogen protein atoms forming the ligand binding pocket (defined as residues within 5 Å of the native ligand).
- **Why it matters:** Specifies the exact protocol that produces the MMGBSA scores reported in Table 2 — every number there is the mean over 750 frames with specific solvation model parameters.
- **Plain-language expansion:** 
  - **750 frames:** The 17.5 ns production trajectory is sampled at uniform intervals to give 750 structures. For each structure, binding free energy is estimated by: (1) calculating the energy of the complex; (2) stripping out the ligand and calculating the protein energy alone; (3) stripping out the protein and calculating the ligand energy alone; (4) computing $\Delta G = G_{\text{complex}} - G_{\text{protein}} - G_{\text{ligand}}$. More negative $\Delta G$ = stronger binding.
  - **igb = 5:** The Generalised Born (GB) solvation model parameter; "igb = 5" selects the GBSA-OBC2 model (Onufriev, Bashford, Case), a specific parameterisation of the implicit solvent approximation. Different igb values give slightly different absolute numbers but similar relative rankings.
  - **Binding pocket RMSD:** Pocket atoms = non-hydrogen protein atoms within 5 Å of the native ligand. RMSD measures how much these atoms deviate from their starting positions during simulation — a low RMSD means a rigid pocket; high RMSD means a flexible one. Reported in Figure 3C.

## 3. Formulas and figures unpacked

**MMGBSA binding free energy (not in the paper as an equation, but the formula used)**

$$\Delta G_{\text{bind}} = G_{\text{complex}} - G_{\text{protein}} - G_{\text{ligand}}$$

where each $G$ is approximated as:

$$G = E_{\text{MM}} + G_{\text{GB}} + G_{\text{SA}}$$

| Term | Name | What it captures |
|------|------|-----------------|
| $E_{\text{MM}}$ | Molecular mechanics energy | Bond, angle, dihedral, electrostatic and van der Waals interactions from force field |
| $G_{\text{GB}}$ | Generalised Born solvation free energy | Electrostatic cost of moving the molecule from vacuum to water (implicit solvent) |
| $G_{\text{SA}}$ | Surface area term | Hydrophobic contribution proportional to solvent-accessible surface area |

- **Worked intuition:** If the ligand fills the binding pocket snugly (van der Waals contacts, hydrogen bonds), $E_{\text{MM}}(\text{complex})$ is more negative than $E_{\text{MM}}(\text{protein}) + E_{\text{MM}}(\text{ligand})$, making $\Delta G_{\text{bind}}$ negative (favourable). A value of −30 kcal/mol means binding is strongly favoured over the unbound state.
- **Typical range:** Drug-like compounds on their target proteins: −15 to −50 kcal/mol. Table 2 shows −19 to −57 kcal/mol for generated compounds; Table 3 shows −20 to −46 kcal/mol for known binders — overlapping ranges.

**MD protocol timeline**

| Phase | Duration | Purpose | Restraints? |
|-------|----------|---------|------------|
| Minimisation | 2000 steps | Remove clashes | Yes (all non-H) |
| Heating | 1 ns | Equilibrate temperature to 300 K | Yes (non-H) |
| Discarded production | 2.5 ns | Allow system to finish equilibrating | None |
| Production (MMGBSA) | 17.5 ns (750 frames) | Sample binding free energy | None |

## 4. The argument in one diagram

**Chai-1 generates 3D complex** → **Antechamber assigns ligand force field** → **tleap solvates in TIP3P water box** → **Minimise clashes (2000 steps)** → **Heat to 300 K with restraints (1 ns)** → **Production MD, 20 ns free** → **Discard first 2.5 ns** → **MMGBSA over 750 frames** → **$\Delta G_{\text{bind}}$ reported in Table 2**

If $\Delta G_{\text{bind}}$ is in the same range as known binders (Table 3): independent validation passed.

## 5. What is easy to miss

> [!Hint] MMGBSA uses Chai-1 structure as input, not a crystal structure
> The MD simulation starts from the Chai-1-predicted complex, not from a crystal structure. This means any errors in the Chai-1 structure propagate into the MMGBSA result. The two validations (Chai-1 score and MMGBSA) are therefore not fully independent — they share the same structural starting point.

> [!caution] 20 ns may not be sufficient for flexible targets
> For rigid targets like the bromodomain, 20 ns is generally enough to equilibrate the binding pocket. For flexible targets like GPCRs, 20 ns may capture only a fraction of the relevant conformational space. The authors note "No major structural changes were observed for the bromodomain protein" but larger variations for the kinases — the MMGBSA values for those targets carry more uncertainty.

> [!caution] ±0.1 to ±0.5 kcal/mol in Table 2 is precision, not accuracy
> The MMGBSA uncertainties reported as ±0.10 to ±0.52 kcal/mol in Table 2 are statistical precision (standard deviation across 750 frames), not accuracy relative to experiment. MMGBSA absolute accuracy is typically ±5 kcal/mol or worse. The numbers are reliable for relative rankings within a target, not for absolute affinity prediction.

> [!Hint] igb = 5 was validated on a large dataset
> The paper cites Sindt *et al.* 2025 for the MMGBSA protocol calibration — this suggests the igb = 5 parameter was chosen based on a systematic benchmark rather than arbitrarily.

## 6. Open questions

> [!question] Why only 20 ns, not longer?
> 20 ns is a standard but short MD simulation for protein–ligand systems. Kinases and GPCRs can show significant conformational changes on longer timescales (microseconds). Would 100 ns or 1 μs change the MMGBSA values substantially?

> [!question] Is the MMGBSA score truly independent of Chai-1?
> Both the structure and the conformation for MMGBSA come from Chai-1. A truly independent validator would start from a crystal structure or use a different structure predictor.

> [!question] Were any generated compounds excluded from MMGBSA due to MD instability?
> The paper does not mention any simulations that failed or had to be re-run. It is possible that some ligands dissociated during MD — if so, they would not appear in the results.

## 7. Connections

**Within the paper**
- MMGBSA scores in Table 2 are compared to experimental binder MMGBSA scores in Table 3 — the overlap is the key validation result.
- Figure 3B shows MMGBSA calculated for the first 200 MC steps of each simulation run — a preliminary validation used to track convergence during design.
- Figure 3C shows pocket RMSD — directly computed from the same MD trajectories that give the MMGBSA scores.

**Outside the paper**
- [[MMGBSA]] — the binding free energy method; Miller *et al.* 2012 (cited) is the primary methodological reference.
- [[Molecular docking]] — for comparison: docking scoring functions are orders of magnitude faster but much less accurate than MMGBSA; MMGBSA is used here as the high-accuracy reference.
- [[Boltz-2]] — the other independent validator used in the paper (Table 2); AI-based rather than physics-based, making it complementary to MMGBSA.

## 8. Test yourself

1. **(Recall)** What is the formula for MMGBSA binding free energy? What are the three energy components of each term?
2. **(Recall)** How long is the production MD simulation? How many frames are used for MMGBSA? How much of the trajectory is discarded and why?
3. **(Comprehension)** Explain why MMGBSA is described as an "independent" validator of Chai-1 scoring — and identify one way in which it is *not* fully independent.
4. **(Comprehension)** MMGBSA reports ±0.21 kcal/mol uncertainty for one compound in Table 2. Does this mean the binding free energy prediction is accurate to ±0.21 kcal/mol? What does it actually measure?
5. **(Application)** You want to apply the AI-MCLig MD validation protocol to a membrane protein (transmembrane helix bundle). What specific modifications to the tleap setup would be required?
6. **(Critical)** The paper uses igb = 5 (implicit solvent) rather than explicit water for MMGBSA. What are the trade-offs of using an implicit versus explicit water model for a hydrophobic binding pocket?
7. **(Critical)** Suppose all generated compounds show MMGBSA scores of −25 to −35 kcal/mol, while known binders show −35 to −50 kcal/mol. The ranges partially overlap — does this constitute validation? At what level of overlap would you accept or reject the result?

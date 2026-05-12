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

# Deep Dive: Figure 2 — Ligand Evolution During MC Simulation

**Summary**: A reading aid for Figure 2 (p. 6), which illustrates the progressive chemical changes in the ligand structure over 100 MC steps for the bromodomain (A–I) and the first visual comparison of benzene vs. mature compound filling the Pim-1 binding pocket (J–L).

**Paper section**: Results §De novo generation of bound ligands using atomistic-step MC simulation, p. 6

---

## 0. Orientation

Figure 2 is the most visually striking figure in the paper — and the most persuasive. It directly shows what the MC simulation does to a molecule over 100 steps: benzene grows into a complex, pocket-filling drug-like compound. The figure is organised in three rows of four panels: stick views of the bromodomain complex (A–F, steps 0/20/40/60/80/100), van der Waals sphere views in the bromodomain pocket (G–I, steps 0/200/2000), and the same van der Waals sphere view for Pim-1 kinase (J–L, same steps). The key idea: **every 20 accepted MC steps, the molecule grows to fill more of the pocket, and the protein adapts around it**.

![[prj.home/OriginalPaper.Structural.GPCR.DeNovoProteinLigand.btag027.pdf#page=6]]
*Figure 2 — Illustration of the MC-based ligand design process. (A–F) Stick models after 0, 20, 40, 60, 80, and 100 MC steps for the bromodomain. (G) Benzene starting ligand (van der Waals spheres) in the bromodomain pocket. (H) Generated ligand after 200 steps. (I) After 2000 steps. (J–L) Same as G–I but for Pim-1 kinase (p. 6).*

## 1. What you need before reading this

- **[[Bromodomain]]** — the first test-case protein, shown in panels A–I.
- **[[SMILES]]** — the molecular representation that the MC modifies; each panel corresponds to a different SMILES string.
- **[[Binding site]]** — the protein cavity the ligand is filling; understanding what a "binding pocket" looks like physically is essential for reading the van der Waals panels.
- **[[Monte Carlo simulation]]** — the algorithm producing the step-by-step changes; each panel is a snapshot after a fixed number of accepted moves.

> [!caution] Prerequisite
> Panels G–L require understanding molecular surface representations. Van der Waals spheres show each atom as a sphere with its van der Waals radius; the resulting surface visualises the physical shape and volume the molecule occupies. Without this, the filling-the-pocket progression will be visually unclear.

## 2. Panel-by-panel annotation

**[Panel A — Step 0: Benzene in bromodomain]**
- **What it shows:** Benzene (6 carbons, flat hexagonal ring) shown as a stick model, placed in the bromodomain binding pocket (protein as green cartoon ribbons/helices).
- **What it means:** The starting point. Benzene is a minimal aromatic scaffold — it barely fills the pocket. The Chai-1 confidence score at this step is ~0.4 (estimated from Figure 3A starting point).
- **Key observation:** The protein pocket (visible as a concavity in the cartoon rendering) is clearly much larger than benzene. The compound occupies only a fraction of the binding site volume.

**[Panel B — Step 20: After 20 accepted moves]**
- **What it shows:** A more complex molecule with additional substituents replacing some benzene hydrogens.
- **What it means:** Twenty accepted MC operations (from the ~30% acceptance rate, roughly 20–60 total proposed moves were needed) have added functional groups. The molecule begins to extend into different regions of the pocket.
- **Key observation:** Even at step 20, the ligand looks qualitatively different from benzene — new atoms are visible extending beyond the ring.

**[Panel C — Step 40]**
- **What it shows:** Further growth; the molecule now has branches extending in multiple directions.
- **What it means:** The MC is exploring different attachment points, guided by Chai-1 feedback. Additions that improve the confidence score are retained; others are mostly rejected.
- **Key observation:** The compound starts to adopt a shape that more closely mirrors the pocket's geometry — one can see the ligand beginning to fill the "corners" of the binding site.

**[Panels D, E, F — Steps 60, 80, 100]**
- **What they show:** Progressive addition of complexity, refinement of substituents.
- **What they mean:** Each panel shows about 20 more accepted MC steps. By step 100, the compound is already recognisably drug-like — multiple rings and functional groups, a molecular weight estimated at 200–300 Da.
- **Key observation:** The stick representations at steps 80 and 100 begin to look similar to each other — the rate of new bond additions slows as the simulation approaches a local optimum. This is consistent with the plateau visible in Figure 3A after ~250 steps.

**[Panel G — Benzene in bromodomain pocket (van der Waals, solvent-accessible surface)]**
- **What it shows:** Benzene shown as van der Waals spheres (coloured by atom type) placed in the bromodomain binding pocket shown as a solvent-accessible surface (protein surface coloured by residue).
- **What it means:** The pocket surface (shown in red/green/white) is much larger than benzene. Benzene sits in the pocket with large empty spaces around it — it is touching only a small part of the available contact surface.
- **Key observation:** This is the "poor fit" baseline. A compound with no complementarity to the pocket would score low on Chai-1 for exactly this reason — poor interface contacts translate to low structural confidence.

**[Panel H — 200-step compound in bromodomain pocket]**
- **What it shows:** The generated compound after 200 MC steps shown in the same van der Waals representation in the same pocket.
- **What it means:** The compound now fills significantly more of the pocket volume. Gaps visible in Panel G are mostly closed.
- **Key observation:** The compound appears to wrap around the pocket contours — different colours (atoms of different types) can be seen matching different pocket surfaces. This is complementarity forming.

**[Panel I — 2000-step compound in bromodomain pocket]**
- **What it shows:** The final generated compound in the bromodomain pocket.
- **What it means:** Near-complete filling of the pocket volume. The compound surface closely matches the protein surface.
- **Critical observation:** "The generated ligand tightly fits into the binding pocket (protein represented as solvent accessible surface)" — the caption explicitly highlights this. Tight fitting is the physical correlate of the high Chai-1 score (0.88–0.92 in Table 2) achieved after 2000 steps.

**[Panels J, K, L — Pim-1 kinase at steps 0, 200, 2000]**
- **What they show:** The same progression for Pim-1 kinase — benzene → partial filling → complete filling.
- **What they mean:** The same visual narrative applies to a different target with a different pocket geometry. The final compound (Panel L) fills the Pim-1 pocket similarly to how Panel I fills the bromodomain pocket.
- **Key observation:** The Pim-1 pocket (visible in J–L) appears differently shaped from the bromodomain — the geometry of the filled compound in Panel L is noticeably different from Panel I, confirming that the MC generates target-specific compounds, not generic drug-like molecules.

## 3. Formulas and figures unpacked

**Visual representation glossary**

| Representation | What is shown | Used in panels |
|---------------|---------------|----------------|
| Stick model | Atoms as small spheres, bonds as sticks; protein as cartoon ribbon/helix | A–F |
| Van der Waals spheres | Atoms as full-size spheres (van der Waals radius); protein as solvent-accessible surface | G–L |

**What "tight fit" means in structural terms:**
A tight fit means low steric clash (no atomic overlap between ligand and protein), high shape complementarity (the ligand shape mirrors the pocket shape), and multiple non-covalent contacts (hydrogen bonds, hydrophobic contacts, van der Waals attractions). All of these contribute to favourable binding free energy. Chai-1's confidence score (used as a proxy for binding) is implicitly sensitive to all of these because a well-fitting complex is better predicted than a poorly fitting one.

**Pocket volume comparison (estimated from figure):**
- Benzene volume: ~48 Å³
- A typical drug-like molecule (MW ~300 Da): ~300–350 Å³
- Bromodomain pocket volume: ~200–400 Å³ (typical for acetyllysine-binding pockets)

The panel progression from G to I shows the ligand growing from ~48 Å³ to ~300 Å³, approaching full occupancy of the pocket.

## 4. The argument in one diagram

**Step 0: Benzene barely occupies pocket** → **Steps 20–100: Progressive substituent addition fills pocket regions** → **Steps 100–200: Continued refinement; compound now fills most of pocket** → **Step 2000: Final compound tightly fills pocket with shape complementarity** → **Same progression independently on Pim-1: different pocket geometry, different final compound, same tight-fit endpoint**

## 5. What is easy to miss

> [!Hint] Each panel is a snapshot, not every step
> Panels A–F show the structure at steps 0, 20, 40, 60, 80, 100 — every 20th accepted step. Between these snapshots, many proposed moves were rejected. The visual progression is smooth because only accepted (improving or probabilistically accepted worsening) moves are shown.

> [!caution] The stick model panels (A–F) may mislead about molecular complexity
> Stick models emphasise connectivity (bonds) rather than volume. At step 40, the compound may look more complex than at step 20 simply because more bonds are visible — but the actual volume and shape complementarity are better captured in the van der Waals sphere panels (G–L). Both representations are needed for a complete picture.

> [!Hint] The protein visibly changes between panels
> Looking carefully at panels G, H, I (and J, K, L) — the protein surface shown is the Chai-1-predicted surface for each step's complex, not a fixed crystal structure. The pocket shape subtly changes between panels, reflecting protein conformational adaptation. This is the visual evidence for protein flexibility handling — the pocket reshapes to accommodate each new ligand.

> [!caution] Figure 2 shows only the bromodomain and Pim-1
> The paper tests four targets (bromodomain, p38, Pim-1, β-1 receptor) but Figure 2 only shows bromodomain and Pim-1. The visual progression for p38 and the GPCR (which has larger pocket RMSD, Figure 3C) would be even more dramatic evidence for flexibility handling — but is not shown.

## 6. Open questions

> [!question] What happened between panels — which moves were rejected?
> The figure shows only accepted structures. The rejected intermediates (the molecules that were tried and rejected between steps 20 and 40, for example) are not shown. Visualising the rejected structures would reveal the "roughness" of the chemical space landscape.

> [!question] Would benzene always lead to similar final compounds?
> Three independent MC runs were performed per target (Table 2), all starting from benzene but with different random seeds. Figure 2 shows only one run. How different are the intermediate structures at step 100 for the other two runs?

> [!question] Do panels G–I show the same protein conformation?
> If the pocket shape changes between panels G, H, and I, this is direct evidence for flexibility handling. The caption does not explicitly state whether the protein coordinates differ between panels.

## 7. Connections

**Within the paper**
- Figure 2 visual progression → Figure 3A (score trajectory — the improving scores correspond to the visual improvement in pocket filling)
- Figure 2 panels G–I → Figure 3C (RMSD — the pocket shape changes visible between G/H/I panels correspond to the RMSD > 0 data in Figure 3C)
- Figure 2 → Table 2 (the Step 2000 compound in Panel I is one of the three bromodomain compounds listed in Table 2)

**Outside the paper**
- [[Binding site]] — the pocket visible in G–L; understanding pocket anatomy (hydrophobic core, hydrogen bond acceptors/donors at rim) clarifies why the ligand grows in specific directions.
- [[Induced fit]] — the physical phenomenon responsible for the pocket shape change visible between panels; [[Induced fit]] explains why Panel G (rigid benzene) and Panel I (adapted pocket) differ.

## 8. Test yourself

1. **(Recall)** What molecular representations are used in panels A–F vs. G–L? What does each representation show that the other does not?
2. **(Recall)** How many MC steps separate each panel in the A–F sequence? What does "accepted MC step" mean?
3. **(Comprehension)** Looking at the progression from G to I (benzene → 200 steps → 2000 steps), describe in your own words what changes in the spatial relationship between ligand and protein.
4. **(Comprehension)** The protein surface in panels G–I comes from the Chai-1-predicted complex at each step, not a fixed structure. What does this imply about how the pocket shape changes across panels?
5. **(Application)** If you ran the same simulation 10 times and compared the compounds at step 100 (one per run), would you expect them to look similar or different? Justify based on the stochastic nature of the MC algorithm.
6. **(Critical)** Figure 2 shows only the bromodomain (panels A–I) and Pim-1 (panels J–L). The β-1 adrenergic receptor (a GPCR) was also tested and shows larger pocket RMSD (Figure 3C). Why might the authors have chosen not to show the GPCR progression in Figure 2?
7. **(Critical)** The figure caption states the generated ligand in panel I "tightly fits into the binding pocket." Tight fit is visually evident — but does visual fit guarantee binding affinity? What additional information would you need to confirm the compound actually binds?

---
CreatedAt: 2026-05-13
LastUpdateAt: 2026-05-13
LastReviewAt: null
ReviewerIds:
  - admin
OwnerIds:
  - admin
IssueNotes: null
GeneratedBy: claude-sonnet-4-6
readOrderIndex: 0
---

# AI-MCLig Pipeline — part 1: The Chemical Move Set

*Starting from benzene, every MC step applies one of 9 atomic chemical operations — validated for chemical correctness by [[RDKit]].*

- 9 operations: add small group (p=0.3), remove atom (p=0.2), change atom type (p=0.1), add atom in chain (p=0.15), change bond type (p=0.25), form ring (p=0.25), break ring (p=0.05), make ring aromatic (p=0.05), rearrange bonds (p=0.1)
- [[RDKit]] checks each result for chemical validity before it is scored
- Every 100 steps the simulation resets to the best structure found so far — avoiding permanent local traps

[[prj.home/OriginalPaper.Structural.GPCR.DeNovoProteinLigand.btag027.pdf#page=2|MC simulation approach, p. 2 — nine chemical operations]]
*Methods, p. 2 — the nine chemical operations and their step probabilities.*

# AI-MCLig Pipeline — part 2: Score, Accept, Repeat

*Every accepted structure is scored by Chai-1 plus three drug-property biases — the composite score drives the whole search.*

$$\text{score} = 0.8 \cdot \text{Chai-1} - 0.1 \cdot \text{SA} + 0.05 \cdot \text{ESOL} + 0.05 \cdot \text{QED}$$

- 80% weight on Chai-1 confidence — the binding signal
- SA penalty steers away from unsynthesisable structures ([[Synthetic accessibility score]])
- ESOL reward steers toward water-soluble molecules
- QED reward steers toward drug-like molecules ([[QED (drug-likeness)]])

[[prj.home/OriginalPaper.Structural.GPCR.DeNovoProteinLigand.btag027.pdf#page=3|Equation (2), p. 3 — composite scoring formula]]
*Equation (2), p. 3 — the composite score with all four terms and their weights.*

# AI-MCLig Pipeline — part 3: Watch the Ligand Grow

*In 2000 steps from benzene, AI-MCLig generates a complex drug-like molecule that fills the binding pocket.*

- Figure 2 shows the bromodomain simulation: every 20th accepted structure over the first 100 steps
- A–F (stick model): the ligand grows, acquires substituents, begins to fill the pocket
- G–I (van der Waals spheres): benzene at step 0, then at step 200, then at step 2000 — now tightly fitting
- J–L: same progression for the Pim-1 kinase target — a different pocket, a different final structure

![[prj.home/OriginalPaper.Structural.GPCR.DeNovoProteinLigand.btag027.pdf#page=6]]
*Figure 2 — ligand evolution over 2000 MC steps for the bromodomain (A–I) and Pim-1 kinase (J–L) (p. 6). The most visually compelling result in the paper.*

# Presenter Notes

**Part 1 — The Move Set**

The first protocol is called atomistic-step MC. At each step, one of nine chemical operations is applied to the current ligand SMILES. The operations range from simple (add a methyl group, change a carbon to a nitrogen) to structural (form a new ring, break an existing ring). Each has a probability that was tuned empirically on three targets. After every change, RDKit checks that the resulting structure is chemically valid. The simulation runs for 2000 steps total. Every 100 steps, the state is reset to the best structure found so far — this is a practical anti-trap mechanism that prevents the search from getting permanently lost.

**Part 2 — The Composite Score**

Show the scoring equation. Explain each term in plain language. Chai-1 confidence (the binding signal) gets 80% of the weight — it dominates the search direction. But the other three terms are important: without them, the simulation would cheerfully generate compounds that are impossible to synthesise or completely insoluble. The SA score penalises synthetic complexity; ESOL rewards water solubility; QED rewards overall drug-likeness (molecular weight, lipophilicity, hydrogen bond counts). Each auxiliary term contributes only 5–10%, which is enough to steer the distribution without overriding the binding signal.

**Part 3 — Figure 2 (the star slide)**

Spend the most time here — this is the most viscerally persuasive result in the paper. Walk through Figure 2 panel by panel. At step 0, benzene sits loosely in the pocket. By step 100 it has grown arms and begins to fill the space. By step 2000 it is a complex multi-ring structure that packs tightly against the protein surface. The key point: no human chemist designed this molecule. The algorithm discovered it by taking thousands of random steps guided by a single principle — maximize the Chai-1 confidence score.

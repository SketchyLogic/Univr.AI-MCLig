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

# Figure 2 — Ligand Evolution Over MC Steps

*From a single benzene ring to a complex drug-like molecule that fills the binding pocket — 2000 steps, no human design.*

- A–F (stick model, bromodomain): every 20th accepted structure from steps 0–100; the ligand visibly grows and branches
- G–I (van der Waals spheres, bromodomain): benzene at step 0 (G), partial fill at step 200 (H), tight fit at step 2000 (I)
- J–L (van der Waals, Pim-1 kinase): same progression for a different, more flexible target — different final structure, same dense pocket fill

![[prj.home/OriginalPaper.Structural.GPCR.DeNovoProteinLigand.btag027.pdf#page=6]]
*Figure 2 — Illustration of the MC-based ligand design process. (A–F) Generated ligands (stick model) after 0, 20, 40, 60, 80, and 100 MC steps for the bromodomain ligand binding pocket. (G) Benzene (van der Waals spheres) start ligand placed in the bromodomain binding pocket. (H) Same as (G) with the generated ligand after 200 MC steps. (I) Same as (G) but after 2000 MC steps. Note that the generated ligand tightly fits into the binding pocket. (J–L) Same as (G–I) but for the serine/threonine Pim-1 kinase target (p. 6).*

> [!Example] What to emphasise
> Panel I vs. G is the payoff image: benzene is a tiny molecule rattling loosely in a large pocket; by step 2000, the generated compound fills every contour of that pocket. This is what 2000 evaluations of Chai-1 achieve — without any human chemist guiding the process.

# Presenter Notes

**This is the star slide of the presentation. Spend the most time here.**

Start by reminding the audience where we are in the narrative: we have a scoring oracle (Chai-1) and a search engine (Monte Carlo). Figure 2 shows what happens when you run that combination for 2000 steps.

**Walking through the panels**

Panel A: benzene — 6 carbons, a flat ring, essentially no substituents. It sits loosely in the bromodomain pocket (green cartoon). Panel B (20 steps later): a short chain has appeared on one side. Panel C–F: substituents grow in different directions; the molecule begins to adopt a 3D shape that matches the pocket geometry. None of these intermediate structures were hand-drawn — each is the result of a random chemical move that happened to score better than what came before.

Now shift to panels G–I. G is benzene again, shown as van der Waals spheres so you can see the physical size. The protein surface (solvent-accessible surface) wraps around it, but there is clearly a lot of empty space in the pocket. H is step 200: the compound is already larger, filling some of the space. I is step 2000: the generated compound packs tightly against every wall of the pocket. There is virtually no empty space. This is exactly the kind of shape complementarity that a medicinal chemist would spend months optimising.

Panels J–L repeat this story for Pim-1 kinase — a different, more flexible target. The final structure (L) looks nothing like panel I, because the Pim-1 pocket has a different geometry. This demonstrates that the method adapts automatically to each target.

**The key message**: the algorithm doesn't know chemistry, biology, or pharmacology. It only knows "score this, accept or reject." And yet it produces structures that would pass as reasonable drug design starting points.

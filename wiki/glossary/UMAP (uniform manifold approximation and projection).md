---
tags:
  - __CONCEPT
CreatedAt: 2026-05-14
LastUpdateAt: 2026-05-14
LastReviewAt: null
ReviewerIds:
  - admin
OwnerIds:
  - admin
IssueNotes: null
GeneratedBy: claude-sonnet-4-6
readOrderIndex: 2
---

# UMAP (uniform manifold approximation and projection)

UMAP is a **dimensionality reduction** algorithm: it takes data that lives in a very high-dimensional space and compresses it into 2D (or 3D) while trying to preserve the neighbourhood structure — points that were close together in high dimensions should remain close together in the projection.

# TLDR

UMAP squashes a cloud of high-dimensional molecular fingerprints into a 2D map. Points near each other on the map are chemically similar. The paper uses it to ask: "do the AI-generated compounds land in the same chemical neighbourhood as known binders?" They do.

## Why high dimensions are a problem

A molecule can be described as a **fingerprint**: a binary vector with thousands of dimensions, where each bit encodes the presence or absence of a particular substructure. For example, atom-pair fingerprints (used in the recovery test) encode all pairs of atom types at all topological distances. Two molecules with similar fingerprints are chemically similar; two with very different fingerprints are chemically distant.

You cannot visualise a 2 048-dimensional space. UMAP reduces it to two axes that have no direct chemical meaning but preserve the global shape of the data: dense clusters in the high-dimensional space appear as dense clusters in the 2D plot.

## How UMAP works (intuitively)

1. For each point, identify its nearest neighbours in the high-dimensional space — the points most similar to it.
2. Build a graph where nearby points are connected by edges, with edge weight proportional to proximity.
3. Find a 2D layout of the same points that preserves, as much as possible, the same graph structure — close neighbours in high dimensions stay close in 2D.

The "uniform manifold" part refers to the mathematical assumption that the data lies on a lower-dimensional surface (manifold) embedded in the high-dimensional space. The algorithm learns that surface and flattens it.

> [!caution] UMAP axes have no interpretable units
> The x and y coordinates in a UMAP plot are not chemical properties. Distance between clusters is meaningful (more separated = more different), but the absolute position and orientation of the plot are arbitrary. Never read off a number from a UMAP axis.

## In AI-MCLig: supplementary Figure 14

The paper uses UMAP via the **ChemPlot** library (Cihan Sorkun et al. 2022) to visualise the chemical space of all compounds in the study. The input fingerprints are molecular descriptors computed from SMILES strings.

The plot contains two sets of points:

| Point set | What it represents |
|---|---|
| Known binders (from PDBbind / Table 3) | Experimentally confirmed ligands for each target — the reference population |
| Generated compounds (from Table 2) | The 12 AI-MCLig outputs across 4 targets — 3 runs each |

**What Figure 14 shows**: the generated compounds land *near* clusters of known binders in the 2D UMAP projection — meaning they occupy a similar region of chemical space — despite being structurally diverse from each other (three independent runs produce three different molecules).

## Why this matters for the paper's argument

This result closes a potential gap in the validation chain. MMGBSA and Boltz-2 scores show the generated compounds *score like* known binders. But one could object: "maybe the scoring is miscalibrated and the compounds are just chemically strange outliers that happen to get high scores."

The UMAP analysis answers that objection: the generated compounds are not outliers in chemical space. They cluster near known drugs for the same target. This means:

1. The MC search found its way to the same chemical neighbourhood that medicinal chemistry has found through decades of experimental work — starting from benzene.
2. The structural diversity between the three independent runs (they look different from each other) but shared neighbourhood with known binders (they land near the same cluster) is consistent with the known observation that many chemically distinct scaffolds can bind the same pocket.

> [!Info] Chemical space is vast — clustering near known binders is not trivial
> The drug-like chemical space contains an estimated $10^{60}$ molecules. Landing near known binders after a 2 000-step search from benzene is a meaningful positive signal — even if it doesn't prove experimental binding.

^e19b01

## Related pages

- [[RMSD (root mean square deviation)]]
- [[Ligand vs binder]]
- [[Score change Δs]]
- [[Monte Carlo simulation]]

## Other sources

- McInnes et al. (2018) — the original UMAP paper: *UMAP: Uniform Manifold Approximation and Projection for Dimension Reduction*
- Cihan Sorkun et al. (2022) — ChemPlot: the Python library used in the paper for chemical space visualisation (*Chem Methods* 2:e202200005)

## Test yourself

- In a UMAP plot, two generated compounds land far from each other but both land near a cluster of known binders. What does this tell you about their chemical relationship?
- Why would it be wrong to say "compound A has a higher UMAP score than compound B"?
- The paper runs three independent MC simulations per target and gets three structurally diverse compounds. They all appear near the same UMAP cluster. What does this suggest about the MC search landscape?

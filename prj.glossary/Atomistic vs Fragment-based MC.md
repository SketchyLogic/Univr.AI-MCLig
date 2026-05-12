---
tags:
  - CONCEPT
CreatedAt: 2026-05-11
LastUpdateAt: 2026-05-11
LastReviewAt:
ReviewerIds:
  - admin
OwnerIds:
  - admin
IssueNotes:
GeneratedBy: claude-sonnet-4-6
readOrderIndex: 3
---

#Presentation
# Atomistic vs Fragment-based MC

AI-MCLig defines **two alternative MC protocols** for exploring chemical space. They share the same scoring function and the same [[Metropolis criterion]], but differ in the *granularity* of the moves and in every parameter that depends on that granularity. The two protocols are run independently — they are not interleaved in a single simulation.

## At a glance

| | Atomistic-step MC | Fragment-based MC |
|---|---|---|
| Move set | 9 atom/bond operations (see [[MC Search Components]]) | 2 fragment operations (add / remove) |
| Building block | A single atom or bond | A whole [[BRICS decomposition\|BRICS fragment]] |
| Fragment source | — | rdkit BRICS decomposition of known ligands (or random ChEMBL) |
| Starting compound | Benzene | A random fragment of the target ligand |
| MC steps | 2000 (de novo) / 5000–10 000 (recovery) | 10 000 |
| $\beta$ (Metropolis selectivity) | 50 | 5 |
| Reset to best structure | Every 100th step | Every 50th step |
| Step probabilities | Tuned per operation (e.g. add group .3, remove atom .2, …) | Add fragment .6, remove fragment .4 |

## Move sets compared

**Atomistic-step MC** uses 9 small chemical edits — add small group, remove atom, change atom type, add atom in chain, change bond type, form ring, break ring, turn ring aromatic, rearrange bonds. Each edit nudges the molecule by one atom or one bond at a time, producing a smooth-ish trajectory through chemical space.

**Fragment-based MC** uses only two operations, defined by the BRICS grammar:
- **Add fragment** — pick a placeholder (open attachment point) on the current molecule at random; attach a BRICS-compatible fragment there.
- **Remove fragment** — pick a bond between two fragments at random; cut it and restore the placeholder that was there before the bond was formed.

The compatibility rules from rdkit's BRICS implementation ensure that only chemically sensible junctions are ever created.

## Why the parameters differ

The fragment moves are **much larger jumps** in chemical space than the atomistic ones. A single fragment swap can change molecular weight by tens of Daltons and rewire the pharmacophore, whereas an atomistic move shifts one atom.

This forces three coupled changes in the fragment protocol:

1. **$\beta$ drops from 50 → 5.** With $\beta = 50$ the [[Metropolis criterion]] becomes very selective; almost no fragment swap would survive the inevitable transient score drop, and the search would freeze. A lower $\beta$ keeps the acceptance rate in the usable 0.1–0.2 range despite the larger $\Delta s$ per step.
2. **Reset frequency increases (100 → 50 steps).** Because each accepted step can be drastically destructive, the simulation snaps back to the best-so-far structure twice as often to avoid drifting into bad regions of chemical space.
3. **Starting point changes (benzene → a fragment of the target).** Benzene is too small to receive a useful fragment in the first move; starting from a real fragment gives the search a workable seed.

## When to use which

- **Atomistic-step MC** is the *default de novo* protocol — no prior knowledge of binders required. Used for the four target proteins in Table 2.
- **Fragment-based MC** assumes you already have known ligands (or a curated fragment library) and want to recombine their chemically meaningful pieces. It excels when the goal is to stay close to a known chemotype while exploring variants.

## Caveats

> [!caution] The two protocols are not mixed
> AI-MCLig does **not** combine atomistic and fragment moves in a single run. They are separate experiments. A hybrid scheme is a plausible future direction but is not what the paper evaluates.

> [!caution] Fragment library quality matters
> The paper notes (§ Limitations) that fragments drawn at random from ChEMBL give worse results than fragments derived from known binders of the target family. The fragment protocol's success therefore depends on having relevant ligands already.


> [!danger] What about recursive MC guidati da pdf di brics/diffusionModel? 😱
> 
> Cosa c'è tra atomistic e bricks? Se al posto di valutare solo la temperatura di uno step potessi valutare la temperatura di un set di step a depth x? (es. valutare il net gain dopo 3 steps)
> 
> Quale range di x consiglierebbe di esplorare un biologo molecolare?
> 
> #Research 



## Analogy

If atomistic MC is a sculptor chipping at clay one fingerprint at a time, fragment-based MC is a child rearranging LEGO bricks. The clay version reaches any shape but slowly; the LEGO version makes huge leaps but only between configurations the brick set allows. The MC parameters ($\beta$, reset frequency) are the equivalent of how patient the artist is with bad-looking intermediate steps.

## TLDR

Two alternative protocols sharing the same score and acceptance rule. **Atomistic-step MC** edits one atom/bond at a time, starts from benzene, uses $\beta=50$, runs 2000 steps. **Fragment-based MC** adds/removes whole BRICS fragments, starts from a known-ligand fragment, drops $\beta$ to 5 and resets twice as often because each move is a much larger jump in chemical space.

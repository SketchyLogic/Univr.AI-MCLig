---
tags:
  - __CONCEPT
CreatedAt: 2026-05-12
LastUpdateAt: 2026-05-12
LastReviewAt: null
ReviewerIds:
  - admin
OwnerIds:
  - admin
IssueNotes: null
GeneratedBy: claude-sonnet-4-6
readOrderIndex: 2
---

# MC step selection probabilities

At every step of the [[Monte Carlo simulation]], the algorithm must pick **which type of chemical change** to attempt. It does this randomly, but not uniformly — each change type has an assigned probability that reflects how often it should be tried.

The nine change types and their standard probabilities (p. 2) are:

| Change type | Probability |
|---|---|
| Add a small chemical group (e.g. CH₃, OH) | 30% |
| Remove an atom | 20% |
| Add an atom in a chain (between two atoms) | 15% |
| Change an atom type | 10% |
| Rearrange bonds | 10% |
| Break up a ring | 5% |
| Turn a ring aromatic (or vice versa) | 5% |
| Change a bond from single to double (or vice versa) | 2.5% |
| Form a new ring | 2.5% |

These sum to 100%, so exactly one change type is picked per step.

## How were these numbers derived?

They were **not derived mathematically** — there is no formula or theory that tells you the "correct" probability for each change type. Instead, the authors found them by trial and error:

1. They ran simulations with many different probability combinations.
2. They measured which combination found good-scoring molecules fastest.
3. They picked the winner.

This process was carried out on three protein targets: the bromodomain, p38 map kinase, and Pim-1 kinase. The winning combination was then used for all subsequent simulations.

> [!caution] Limited search, not guaranteed optimal
> The authors explicitly note that running simulations is time-consuming, so only a limited number of probability combinations were tested. The chosen set is the best *among those tested* — not proven to be globally optimal. They also note that ideal probabilities may differ across protein targets, and plan to investigate this in future work (p. 2).

## Why is this not a problem in practice?

The probabilities determine **how efficiently** the simulation explores chemical space, not **whether** it can reach the right answer at all. Even with suboptimal probabilities the MC algorithm will still find good molecules — it will just take more steps to get there. The chosen set was empirically the most efficient among the tested options.

## Why is the distribution uneven?

The skew toward adding/removing atoms (50% combined) and away from ring operations (10% combined) reflects practical chemical intuition:

- **Atom-level changes are cheap and reversible.** Adding or removing a single atom or group makes small, incremental moves through chemical space. These are the "fine adjustment" moves.
- **Ring operations are rare and structurally disruptive.** Forming or breaking a ring changes the molecule's scaffold, which is a much bigger jump. Attempting them too often would destabilise the search.
- **The simulation starts from benzene** — a molecule that already has a ring — so ring-building operations need not be frequent from the outset.

> [!Example] Analogy: tuning a radio
> Think of the step probabilities like the dials on a radio. Most of the time you want small, fine adjustments (high probability for atomic tweaks). Occasionally you want to jump to a completely different frequency (low probability for ring changes). The mix of dial-turns found most efficient was calibrated by experimenting, not derived from the physics of radio waves.

## The 100-step reset: a complementary safeguard

Alongside the step probabilities, every 100 MC steps the simulation resets the current molecule to the **best structure found so far**. This is a separate mechanism from the probabilities: it ensures that an unlucky streak of accepted bad moves (which the [[Metropolis acceptance criterion]] occasionally allows) cannot permanently derail progress. The simulation always returns to its best known position before exploring further.

## Related pages

- [[Monte Carlo simulation]]
- [[Metropolis acceptance criterion]]
- [[Beta parameter β]]
- [[Score change Δs]]

## Test yourself

- Why couldn't the authors simply calculate the optimal probabilities from first principles?
- If ring-forming probability were raised to 30%, what might go wrong?
- What is the purpose of the 100-step reset, and how does it differ from the step probabilities?
- The paper says results might vary for other protein targets. What does that imply about generalising these probabilities?

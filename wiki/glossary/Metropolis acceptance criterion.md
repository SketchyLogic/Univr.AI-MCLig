---
tags:
  - __CONCEPT
  - PREREQUISITE
CreatedAt: 2026-05-12
LastUpdateAt: 2026-05-12
LastReviewAt: null
ReviewerIds:
  - admin
OwnerIds:
  - admin
IssueNotes: null
GeneratedBy: claude-sonnet-4-6
readOrderIndex: -1
---

# Metropolis acceptance criterion

The **Metropolis acceptance criterion** is the rule that a [[Monte Carlo simulation]] uses to decide whether to keep or discard each randomly proposed change.

In AI-MCLig it appears as equation (1) on p. 2:

$$P(\Delta s) = \exp(-\beta \cdot \Delta s)$$

where:
- $P(\Delta s)$ is the **probability of accepting** the newly proposed molecule
- $\Delta s$ is the **change in score** — new score minus current score (see [[Score change Δs]])
- $\beta$ is the **selectivity parameter** — controls how picky the algorithm is (see [[Beta parameter β]])
- $\exp$ is the **exponential function** (see [[Exponential function]])

## The two-case rule (plain language)

**Case 1 — the new molecule scores better** ($\Delta s < 0$, i.e. score went up):
The move is *always* accepted. No coin flip needed.

**Case 2 — the new molecule scores worse** ($\Delta s > 0$, i.e. score went down):
The move is accepted with probability $P = \exp(-\beta \cdot \Delta s)$, which is a number between 0 and 1. The bigger the worsening, or the higher $\beta$ is, the lower this probability becomes — making the simulation less likely to go "uphill".

> [!Example] Coin-flip analogy
> Imagine you rolled a score-change of +0.02 and $\beta = 50$. The formula gives $P = \exp(-50 \times 0.02) = \exp(-1) \approx 0.37$. In practice: generate a random number between 0 and 1; if it is below 0.37, accept the move; otherwise reject it. That is literally the entire decision rule.

## Why accept bad moves at all?

Chemical space is full of local score traps. If the algorithm only ever accepted improvements, it would get stuck in the first decent molecule it found and never escape to explore genuinely better regions. Occasionally accepting a slightly worse molecule lets the search "jump" over barriers — the same reason a hiker sometimes walks uphill to reach a lower valley on the other side.

## Where this formula comes from

The criterion was introduced by Metropolis *et al.* (1953) to simulate the behaviour of atoms at a given temperature. The probability $\exp(-\beta \cdot \Delta s)$ exactly satisfies **detailed balance** — a mathematical requirement ensuring the simulation samples molecules in a statistically principled way (more probable states are visited more often). AI-MCLig borrows this criterion to sample chemical space rather than physical configurations.

![[prj.home/OriginalPaper.Structural.GPCR.DeNovoProteinLigand.btag027.pdf#page=2]]
*Equation (1), p. 2 — the Metropolis acceptance criterion as used in AI-MCLig.*

## Related pages

- [[Monte Carlo simulation]]
- [[Score change Δs]]
- [[Beta parameter β]]
- [[Exponential function]]
- [[Scoring formula (AI-MCLig)]]

## Other sources

- Metropolis *et al.* (1953) *J. Chem. Phys.* 21, 1087 — original paper
- [Wikipedia — Metropolis–Hastings algorithm](https://en.wikipedia.org/wiki/Metropolis%E2%80%93Hastings_algorithm)

## Test yourself

- Write out the two cases of the criterion without looking at the formula.
- If $\beta = 50$ and a move worsens the score by 0.1, what is the acceptance probability? Is it high or low?
- What would the criterion reduce to if $\beta = 0$? What would that mean for the simulation?

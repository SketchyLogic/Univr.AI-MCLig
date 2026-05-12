---
tags:
  - __DEFINITION
CreatedAt: 2026-05-12
LastUpdateAt: 2026-05-12
LastReviewAt: null
ReviewerIds:
  - admin
OwnerIds:
  - admin
IssueNotes: null
GeneratedBy: claude-sonnet-4-6
readOrderIndex: 1
---

# Beta parameter β

$\beta$ (Greek letter *beta*) is a **tuning knob** in the [[Metropolis acceptance criterion]] that controls how picky the simulation is when deciding whether to accept a worse molecule.

$$P(\Delta s) = \exp(-\beta \cdot \Delta s)$$

# TLDR

Large $\beta$ → very selective, only accepts clearly better molecules. Small $\beta$ → permissive, accepts many moves even if they worsen the score.

## Physical intuition: "temperature"

$\beta$ is borrowed from statistical physics, where it represents *inverse temperature* ($\beta = 1/T$). The analogy translates directly:

| Temperature analogy | MC simulation behaviour |
|---|---|
| **High temperature** (low $\beta$) | Molecules bounce around freely, accepting many bad moves — broad, unfocused search |
| **Low temperature** (high $\beta$) | Molecules settle into the nearest good configuration — focused, selective search |

In AI-MCLig the authors use $\beta = 50$ for the de novo design simulations (p. 5). This is a *high* $\beta$ value, making the simulation very selective: it aggressively pursues score improvements and rarely wastes steps on clearly worse molecules.

## Concrete effect on probability

For a move that worsens the score by $\Delta s = 0.1$:

| $\beta$ value | $P(\Delta s)$ | Meaning |
|---|---|---|
| 5 | $\exp(-0.5) \approx 0.61$ | 61% chance of accepting the worse molecule |
| 50 | $\exp(-5) \approx 0.007$ | 0.7% chance — almost always rejected |
| 5 (fragment MC) | same as above | Fragment-based MC uses $\beta = 5$ because fragment swaps cause larger score jumps |

> [!Info] Why different β for fragment vs atom-based MC?
> Adding or removing whole chemical fragments causes large, abrupt score changes compared to changing a single atom. A large $\beta$ with large $\Delta s$ would give near-zero acceptance for almost every fragment move. The authors use $\beta = 5$ in fragment-based MC to keep a reasonable acceptance rate (p. 4).

## Related pages

- [[Metropolis acceptance criterion]]
- [[Score change Δs]]
- [[Monte Carlo simulation]]

## Other sources

- [Wikipedia — Metropolis–Hastings algorithm](https://en.wikipedia.org/wiki/Metropolis%E2%80%93Hastings_algorithm) — full derivation of β in statistical sampling

## Test yourself

- If $\beta$ is doubled, does the simulation become more or less selective?
- Why do atom-based and fragment-based MC use different $\beta$ values?
- What would happen if $\beta = 0$? (Hint: what is $\exp(0)$?)

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
readOrderIndex: 0
---

# Score change Δs

$\Delta s$ (read: *delta s*) is the **difference in score** between the newly proposed molecule and the molecule the simulation currently holds.

$$\Delta s = s_{\text{new}} - s_{\text{current}}$$

The Greek letter $\Delta$ (delta) universally means "change in" or "difference". Here $s$ stands for *score*.

## What the sign tells you

| Value of $\Delta s$ | Meaning |
|---|---|
| **Negative** ($\Delta s < 0$) | The new molecule scores *better* — the new score is higher than the old one[^1] |
| **Zero** ($\Delta s = 0$) | No change in score |
| **Positive** ($\Delta s > 0$) | The new molecule scores *worse* — the new score is lower than the old one |

## How it feeds into the formula

$\Delta s$ is the central input to the [[Metropolis acceptance criterion]]:

$$P(\Delta s) = \exp(-\beta \cdot \Delta s)$$

- If $\Delta s$ is negative (better move): $-\beta \cdot \Delta s$ is positive → $\exp$ of a positive number is $> 1$ → probability is capped at 1 → the move is **always accepted**.
- If $\Delta s$ is positive (worse move): $-\beta \cdot \Delta s$ is negative → $\exp$ of a negative number is between 0 and 1 → the move is **sometimes accepted**, with lower probability the worse the move is.

## What counts as "score" in AI-MCLig?

The score $s$ is itself a composite number combining four signals (p. 3):

$$\text{score} = 0.8 \cdot \text{Chai-1} - 0.1 \cdot \text{SA} + 0.05 \cdot \text{ESOL} + 0.05 \cdot \text{QED}$$

So $\Delta s$ measures the net improvement or worsening across all four criteria at once. See [[Scoring formula (AI-MCLig)]] for a full breakdown of each term.

[^1]: The scoring convention in AI-MCLig is that **higher score = better molecule**, so a *negative* $\Delta s$ means improvement.

## Related pages

- [[Metropolis acceptance criterion]]
- [[Beta parameter β]]
- [[Scoring formula (AI-MCLig)]]

## Test yourself

- If the new molecule has score 0.85 and the current one has score 0.80, what is $\Delta s$? Is this a good or bad move?
- Will a move with $\Delta s = -0.05$ always, sometimes, or never be accepted?

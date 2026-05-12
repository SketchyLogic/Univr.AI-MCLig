---
tags:
  - __DEFINITION
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
readOrderIndex: -2
---

# Exponential function

The **exponential function** $\exp(x)$ — also written $e^x$ — is a mathematical function where the output shrinks or grows very rapidly as the input changes.

## The key number: $e \approx 2.718$

$e$ is a special mathematical constant (like $\pi$). It appears naturally wherever growth or decay is proportional to the current amount (populations, radioactive decay, probability).

## Why it matters here

In the formula $P(\Delta s) = \exp(-\beta \cdot \Delta s)$, the exponential converts a *score difference* (which could be any number) into a *probability* (which must be between 0 and 1).

Two important properties make $\exp$ perfect for this job:

| Input to $\exp$ | Output |
|---|---|
| Very negative number (e.g. $-10$) | Very close to 0 (nearly impossible) |
| Zero | Exactly 1 (certainty) |
| Positive number | Greater than 1 (but we cap this at 1 in practice) |

So: when a new molecule scores much worse ($\Delta s$ is large and positive → argument is very negative), $\exp$ gives a probability near zero — meaning the move is almost never accepted. When the score barely changes, $\exp$ gives a probability near 1 — meaning the move is almost always accepted.

## Intuition: the "dial"

Think of $\exp(-x)$ as a dial that converts "how bad is this move?" into "how often do we still try it?":

- A tiny worsening → dial reads ~90% → almost always try it
- A moderate worsening → dial reads ~20% → occasionally try it  
- A huge worsening → dial reads ~0.1% → almost never try it

This smooth, continuous behaviour is exactly what the [[Metropolis acceptance criterion]] needs.

## Related pages

- [[Metropolis acceptance criterion]]
- [[Beta parameter β]]
- [[Score change Δs]]

## Test yourself

- What is $\exp(0)$? What does that mean for acceptance probability?
- If the argument to $\exp$ is very negative, is the probability high or low?

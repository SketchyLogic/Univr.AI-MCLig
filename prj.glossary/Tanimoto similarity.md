---
tags:
  - DEFINITION
  - PREREQUISITE
CreatedAt: 2026-05-11
LastUpdateAt: 2026-05-11
LastReviewAt:
ReviewerIds:
  - admin
OwnerIds:
  - admin
IssueNotes:
GeneratedBy: claude-sonnet-4-6
readOrderIndex: -1
---

# Tanimoto similarity

A coefficient that measures how similar two molecules are by comparing their [[Molecular fingerprint|molecular fingerprints]]. It is the most widely used similarity metric in cheminformatics — when someone says "molecules X and Y are 0.7 similar," Tanimoto is almost always the metric implied.

## Definition

For two molecules represented by bit-vector fingerprints $A$ and $B$:

$$T(A, B) = \frac{|A \cap B|}{|A \cup B|} = \frac{c}{a + b - c}$$

where $a$ is the number of bits set in $A$, $b$ the number set in $B$, and $c$ the number set in **both**. The metric ranges from $0$ (no shared features) to $1$ (identical fingerprints).

For **count** fingerprints (where bits store occurrence counts rather than 0/1), the same idea generalises to the *Tanimoto coefficient on counts*:

$$T(A, B) = \frac{\sum_i \min(A_i, B_i)}{\sum_i \max(A_i, B_i)}$$

## Interpretation

| Tanimoto   | Typical meaning                                                 |
| ---------- | --------------------------------------------------------------- |
| > 0.85     | Same chemical series — variants of one scaffold                 |
| 0.5 – 0.85 | Related but distinct molecules; might share a [[Pharmacophore]] |
| 0.3 – 0.5  | Loosely related — same broad chemical class                     |
| < 0.3      | Effectively unrelated for medicinal-chemistry purposes          |

The thresholds depend strongly on the fingerprint used (ECFP4 gives lower values than MACCS for the same molecules). 

> [!danger] Tanimoto score depends on the fingerprint method used
> A bare Tanimoto number is meaningless without naming the fingerprint that produced it.

## Tanimoto vs Dice

AI-MCLig uses **Dice similarity** on atom-pair fingerprints in its [[AI-MCLig Validation Strategy|validation experiment]], not Tanimoto. The two are closely related but not identical:

$$D(A, B) = \frac{2|A \cap B|}{|A| + |B|} = \frac{2c}{a + b}$$

Both range $[0, 1]$ and produce **the same ranking** of compound similarities — if Dice says X is more similar to the target than Y, Tanimoto will agree. But the absolute values differ:

$$D = \frac{2T}{1 + T}$$

So $T = 0.5 \Leftrightarrow D = 0.667$, and Dice values are always equal to or larger than Tanimoto for the same pair. Dice is slightly more forgiving — it weights shared features more heavily relative to features that differ. That can be useful in MC search where you want to credit partial progress generously. For pure similarity reporting in the literature, Tanimoto remains the default.

## Why it matters in AI-MCLig

Even though the paper's scoring uses Dice, Tanimoto is the standard the reader will encounter everywhere else (ChEMBL searches, similarity-based virtual screening papers, the rdkit documentation). Knowing the conversion lets you compare numbers across sources without confusion.

> [!Hint] Quick mental conversion
> If a paper reports a Tanimoto of $T$, the equivalent Dice is roughly $\frac{2T}{1+T}$ — and a Tanimoto of 0.5 is "moderately similar" while a Dice of 0.5 is "rather unrelated."

## TLDR

The default chemical-similarity metric: ratio of shared fingerprint bits to the union of bits. Ranks compound pairs the same way Dice does, but yields lower numbers. AI-MCLig itself uses Dice (closely related), but Tanimoto is the cross-reference standard.

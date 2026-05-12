---
CreatedAt: 2026-05-11
LastUpdateAt: 2026-05-11
LastReviewAt: null
ReviewerIds:
  - admin
OwnerIds:
  - admin
IssueNotes: null
GeneratedBy: claude-sonnet-4-6
readOrderIndex: 2
---

# Deep Dive: Table 1 — Recovery Experiment Results

**Summary**: A reading aid for Table 1 (p. 4), which reports the results of the MC-based recovery experiment — starting from benzene and recovering 10 known target ligands using dice similarity as the score. The table is the primary evidence that the MC search mechanism works.

**Paper section**: Results §MC-based recovery of target ligands from simple starting compounds, p. 3–4

---

## 0. Orientation

Table 1 is a validation table, not a results table — it answers "does the search engine work?" not "does the method find good binders?" Each row is one recovery experiment: the MC starts from benzene and tries to rediscover a known ligand (column 1) by optimising dice similarity. The output (column 2) is the closest compound the MC found. Column 3 (dice score) is the measure of success: 1.00 = perfect recovery; < 1.00 = partial recovery. Eight out of ten rows reach 1.00; two rows (6fnx and 2yek) are partial recoveries. The two failures are informative — they reveal a specific, diagnosable weakness of the move set.

![[prj.home/OriginalPaper.Structural.GPCR.DeNovoProteinLigand.btag027.pdf#page=4]]
*Table 1 — Target and resulting compounds in SMILES for atomistic-step MC simulations, with final dice score (p. 4).*

## 1. What you need before reading this

- **[[Dice Similarity vs Chai-1 Scoring]]** — what a dice score measures and why recovery uses dice not Chai-1.
- **[[Molecular fingerprint]]** — the atom-pair fingerprint encoding underlying the dice score.
- **[[AI-MCLig Validation Strategy]]** — the two-stage protocol (30 × 5000 steps, then 1 × 10,000 steps) that produced these results.
- **[[Ring Local Minima in MC]]** — explains why two rows have dice < 1.00.
- **[[SMILES]]** — the notation used in both columns; you need to be able to read SMILES to compare target vs. generated compound.

> [!caution] Prerequisite
> Without understanding what a dice score of 1.00 means (exact chemical identity by atom-pair fingerprint) and why two compounds might match at 0.905 vs. 1.00, the table is just numbers. Read [[Dice Similarity vs Chai-1 Scoring]] first.

## 2. Row-by-row annotation

**[Rows 1–3 — Three bromodomain ligands from 4lzs, 5mli, 3vc4]**
All three achieve dice score = 1.00 (exact recovery).

The target SMILES (e.g. `CNC(=O)c1[nH]c(c(c1CC)C(=O)C` for 4lzs) represent known bromodomain inhibitors from PDBbind. The generated SMILES are chemically identical — the MC successfully reconstructed these compounds from benzene. These targets represent relatively simple compounds with one or two rings and common functional groups (amide, ketone) — achievable via the standard 9-move set without needing complex coordinated ring assembly.

> [!Hint] 4lzs is a simpler compound
> The 4lzs-based compound has fewer rings and a shorter chain than the 6fnx-based compound. This explains why 4lzs recovers perfectly while 6fnx (the complex polycyclic compound) does not — the move set handles acyclic elaboration better than multi-ring assembly.

**[Row 4 — 6fnx-based compound: dice = 0.905]**
This is the first partial recovery. The target SMILES (`CCn1cnc2c1(O)[nH]N=C(c(@@H]1N=C(c(@@H)c(@@H)c(@@H)c1` — simplified) contains a polycyclic ring system with multiple fused rings. The generated SMILES is similar but not identical — the ring system topology differs.

Dice score 0.905 means the atom-pair fingerprint of the generated compound overlaps with the target at 90.5%. The missing 9.5% corresponds to specific atomic features (bond types, ring membership bits) that differ in the ring assembly region.

> [!danger] 0.905 vs. 1.00 for a ring-heavy compound
> The failure to achieve 1.00 here is not random — it reflects a systematic limitation of the MC move set. Forming a fused polycyclic ring system requires a sequence of ring-forming moves where each intermediate state (open chain extending toward ring closure) scores lower than the preceding compact structure. The MC backtracks rather than completing the ring. See [[Ring Local Minima in MC]] for the mechanism.

**[Row 5 — 2yek-based compound: dice = 0.726]**
The lowest dice score in the table. The 2yek target has an extremely complex polycyclic ring system with three or more fused rings. The generated compound (dice = 0.726) has captured the general chemical class but diverges significantly from the ring topology of the target. This represents the most severe ring local minimum trap in the dataset.

> [!caution] 0.726 does not mean the method failed completely
> A dice score of 0.726 means 72.6% structural overlap. The compound is not random — it resembles the target in most of its atom-pair fingerprint features, meaning the MC found a good chemical neighbourhood even if it could not assemble the exact ring system. In a de novo design context (not a recovery context), a compound with 72.6% similarity to a known binder would likely still have significant binding affinity.

**[Rows 6–10 — Remaining five compounds]**
All achieve dice score = 1.00. These include:

| PDB code | Comment |
|---------|---------|
| 4a9i | Single ring, acyclic substituents |
| 3iw7 | Sulfonamide substituent, two rings |
| 1di9 | Heteroaromatic with chlorine |
| 1mnc | Complex acyclic chain |
| 1a07 | Relatively large acyclic compound |

The diversity of successful recoveries (different functional groups, different ring counts, different molecular weights) demonstrates that the move set can reach a wide variety of chemical structures from benzene — the two failures are specifically ring-assembly related, not a general limitation.

## 3. Formulas and figures unpacked

**Dice similarity score**

$$\text{dice}(A, B) = \frac{2 \cdot |A \cap B|}{|A| + |B|}$$

where $A$ and $B$ are the sets of atom-pair fingerprint bits set to 1 for the generated and target compounds respectively.

| Symbol | Name | Intuition |
|--------|------|-----------|
| $A$ | fingerprint bits for generated compound | which structural features the generated compound has |
| $B$ | fingerprint bits for target compound | which structural features the target compound has |
| $A \cap B$ | bits set in both | common structural features |
| dice | Dice similarity | 0 = nothing in common; 1 = identical by fingerprint |

**Why dice, not Tanimoto?**
The paper uses atom-pair fingerprints with dice similarity. Tanimoto (Jaccard) similarity is more common for Morgan/ECFP fingerprints. Dice weights matching bits by 2/(|A|+|B|) rather than 1/(|A∪B|), giving slightly higher similarity values for molecules of different sizes. Carhart *et al.* 1985 is cited for atom-pair fingerprints.

**Table summary statistics:**

| Category | Count | Fraction |
|----------|-------|----------|
| Perfect recovery (dice = 1.00) | 8 | 80% |
| Near-recovery (0.90 ≤ dice < 1.00) | 1 (6fnx: 0.905) | 10% |
| Partial recovery (dice < 0.90) | 1 (2yek: 0.726) | 10% |

## 4. The argument in one diagram

**Replace Chai-1 with dice similarity to known compound** → **Run two-stage MC (Stage 1: 30 runs × 5000 steps; Stage 2: 1 run × 10,000 steps) from benzene** → **8/10 targets: perfect recovery (dice = 1.00) → search mechanism validated** → **2/10 targets: partial recovery (dice 0.905 and 0.726) → specific failure mode: ring assembly local minima** → **Diagnosis: not a scoring failure (dice is used, not Chai-1) — a move-set limitation for polycyclic systems**

## 5. What is easy to miss

> [!Hint] The table proves search, not scoring
> Table 1 uses dice similarity, not Chai-1. The Chai-1 score plays no role in generating these results. Therefore, Table 1 proves only that the MC chemical search engine works — it says nothing about whether Chai-1 is a good oracle. This is by design; the two-component validation (search first, then scoring) is the paper's explicit approach.

> [!caution] The targets come from three different proteins
> The footnote states: "The target SMILES correspond to bromodomain ligands extract from PDBbind (Liu *et al.* 2017)" — but the caption says "target(SMILES) from dB" suggesting these are from three of the four tested proteins (bromodomain, Pim-1, p38). The diversity of source proteins means the recovery test spans a range of compound types, strengthening the generality claim.

> [!caution] Only the best recovery is reported, not the full distribution
> The dice score in column 3 is "final closest compound" — the single best result from the two-stage protocol. The variance across Stage 1's 30 runs is not shown. It is unknown whether the 30 Stage 1 simulations gave widely varying or consistently similar results for the two failed cases.

> [!Hint] Dice = 1.00 requires exact fingerprint match, not exact SMILES
> Dice = 1.00 means the atom-pair fingerprint is identical between generated and target compound. This allows for some differences in SMILES representation (equivalent but differently written structures). However, for most practical purposes, dice = 1.00 means the same molecule was found.

## 6. Open questions

> [!question] What were the dice scores for the 30 Stage 1 simulations?
> Table 1 shows only the final best result. Were all 30 Stage 1 runs clustered near the reported score, or did they show wide variance? High variance would suggest the landscape is rugged; low variance would suggest convergence.

> [!question] Would the ring-heavy cases recover with more steps?
> The Stage 2 simulation uses 10,000 steps and resets every 100 steps. Would increasing to 50,000 steps allow 6fnx (0.905) or 2yek (0.726) to achieve dice = 1.00? The paper does not address this.

> [!question] Would the fragment-based MC recover 6fnx and 2yek?
> The fragment-based MC adds pre-formed ring fragments in single steps, bypassing the ring local minima problem. The paper mentions "all tested ligand compounds were successfully reassembled" for fragment-based recovery — does this include 6fnx and 2yek?

## 7. Connections

**Within the paper**
- Table 1 → [[Ring Local Minima in MC]] — the two partial recovery cases directly illustrate the ring local minima mechanism.
- Table 1 → [[AI-MCLig Validation Strategy]] — the two-stage protocol that produced these results.
- Table 1 recovery success → justification for using the same MC in de novo design (Table 2).

**Outside the paper**
- [[Molecular fingerprint]] — the atom-pair fingerprint encoding that the dice score is based on.
- [[Dice Similarity vs Chai-1 Scoring]] — the conceptual distinction between validation score (dice) and design score (Chai-1).

## 8. Test yourself

1. **(Recall)** What does a dice score of 1.00 mean? What does 0.726 mean?
2. **(Recall)** How many compounds were perfectly recovered? Which two were not, and what property distinguishes them from the successful recoveries?
3. **(Comprehension)** Explain why Table 1 uses dice similarity instead of Chai-1 as the scoring function. What would Table 1 tell you if Chai-1 were used instead?
4. **(Comprehension)** The two failed recovery cases have dice scores of 0.905 and 0.726. Based on [[Ring Local Minima in MC]], explain the mechanistic reason for these failures.
5. **(Application)** You want to recover a target compound with four fused aromatic rings (a tetracyclic compound). Based on Table 1, what dice score would you predict? Which protocol would you use (atomistic-step or fragment-based) and why?
6. **(Critical)** Table 1 shows 80% perfect recovery on 10 compounds. Is 10 compounds a sufficiently large validation set to claim the search mechanism is robust? What would a more comprehensive validation look like?
7. **(Critical)** Only the best result from the two-stage protocol is reported. If the Stage 1 runs showed widely varying dice scores (e.g. 0.3–1.0) before Stage 2 refinement, would this change your interpretation of the recovery success? Why?

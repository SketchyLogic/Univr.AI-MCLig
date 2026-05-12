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
readOrderIndex: 1
---

# Deep Dive: Methods — MC Simulation Approach and Fragment-based MC

**Summary**: A reading aid for the two MC simulation protocols — atomistic-step and fragment-based — covering the 9 chemical operations, the Metropolis acceptance rule (Eq. 1), the reset mechanism, and the BRICS fragment protocol.

**Paper section**: Materials and methods §MC simulation approach and §Fragment-based MC simulation, pp. 2–3

---

## 0. Orientation

This section answers one question: **how does the molecule change at each step?** There are two completely separate answers: (1) the atomistic-step MC, which modifies the molecule one atom, bond, or small group at a time; and (2) the fragment-based MC, which adds or removes whole chemical fragments at once. Both share the same acceptance rule (Metropolis criterion) and the same scoring oracle (Chai-1), but they differ in granularity, β values, reset schedules, and what they are good at. Read both together so the contrast is clear — you cannot understand the Results section without knowing which protocol generated which results.

## 1. What you need before reading this

- **[[Monte Carlo simulation]]** — the general algorithm: make a random change, evaluate, accept or reject by a probabilistic rule; the framework both protocols operate within.
- **[[Metropolis criterion]]** — the specific acceptance rule used: always accept improvements; accept worsening moves with probability $\exp(-\beta \cdot \Delta s)$.
- **[[SMILES]]** — the text-based molecular representation; all chemical operations in this paper modify the SMILES string.
- **[[BRICS decomposition]]** — the fragment decomposition method used in the second protocol; used to cut known ligands into reusable fragments.
- **[[Chai-1]]** — the AI oracle that evaluates each MC step; called at every accepted tentative structure.

> [!caution] Prerequisite
> The 9 chemical operations only make sense if you understand SMILES notation. Without it, terms like "change atom type," "form a ring," and "change bond type" are abstract. Read [[SMILES]] first.

## 2. Paragraph-by-paragraph annotation

**[¶1 — MC framework and rdkit]**
- **What it says:** The MC simulation modifies a current compound structure using elementary chemical steps via the rdkit package; SMILES and protein sequence are used with Chai-1 for AI-based rebuilding and scoring; the score includes a Chai-1 confidence score, SA bias, solubility bias, and drug-likeness bias.
- **Why it matters:** Orients the reader to the software stack (rdkit for chemistry, Chai-1 for structure prediction) and previews the scoring components.
- **Plain-language expansion:** rdkit (Landrum 2023) is an open-source cheminformatics library that can parse, modify, and validate SMILES strings. Its role here is to (a) perform the chemical modifications, (b) check that the modified structure is chemically valid, and (c) compute SA, ESOL, and QED scores. Chai-1 does only the 3D structure prediction and confidence scoring — all other molecular manipulation happens in rdkit.

**[¶2 — Metropolis acceptance]**
- **What it says:** The new structure is accepted if the score improves; otherwise accepted with probability $P(\Delta s) = \exp(-\beta \cdot \Delta s)$, where $\Delta s$ is the score difference and $\beta$ controls selectivity.
- **Why it matters:** This is the heart of the MC algorithm — it prevents the simulation from getting permanently stuck in local optima.
- **Plain-language expansion:** See §3 below for full formula unpacking. $\beta$ acts as an "inverse temperature": high $\beta$ (cold) = rarely accept bad moves = greedy hill-climbing; low $\beta$ (hot) = often accept bad moves = broad exploration. The paper uses $\beta = 50$ for atomistic-step simulations, which is "very selective" — the simulation climbs toward better compounds but occasionally accepts small decreases to escape local traps. Every 100th step the simulation is reset to the best structure found so far, adding a second mechanism to avoid permanent local trapping.

**[¶3 — The 9 chemical operations]**
- **What it says:** Nine chemical operations are available, each with a tuned probability; together they define all possible one-step moves in chemical space.
- **Why it matters:** These operations determine what molecules can be reached from a given starting point and what the simulation's "step size" is in chemical space.
- **Plain-language expansion:** (See §3 for the probability table.) The nine operations cover: grow the molecule (add group, add atom in chain, add ring), shrink the molecule (remove atom, break ring), modify the molecule (change atom type, change bond type, make ring aromatic, rearrange bonds). Some are marked "not strictly necessary but helpful to avoid local minima" — these are escape mechanisms, not primary drivers of complexity growth. Probabilities are non-uniform and were tuned by testing on three target proteins (bromodomain, p38, Pim-1).

**[¶4 — Reset mechanism]**
- **What it says:** Every 100th step, the structure is reset to the best found so far; this is described as "atomistic-step MC simulation."
- **Why it matters:** The reset prevents long excursions away from the best compound and ensures the simulation exploits as well as explores.
- **Plain-language expansion:** Without resets, a bad accepted move early in the simulation could strand the simulation in a poor region of chemical space. The reset to best structure combines the Metropolis random walk with a periodic exploitation step — a hybrid that converges faster than pure random walk but is less greedy than pure hill-climbing.

**[¶5 — Fragment-based MC concept]**
- **What it says:** The fragment-based protocol recombines molecular fragments using BRICS rules; fragments are added or removed using the same Metropolis criterion; the two steps are "Adding fragments" and "Removing fragments."
- **Why it matters:** Introduces the alternative protocol, which operates at coarser chemical granularity and is better suited for quickly assembling complex ring systems.
- **Plain-language expansion:** [[BRICS decomposition]] cuts a drug-like molecule at specific bond types to produce chemically meaningful fragments with attachment points. The fragment-based MC draws fragments from a library (either random ChEMBL fragments or target-specific fragments from known binders) and attaches or removes them at valid positions. Because a single MC step can add or remove a whole ring system, this protocol explores structural diversity faster than the atomistic approach — but with less fine-grained control over individual atoms.

**[¶6 — Fragment-based parameters]**
- **What it says:** $\beta = 5$ (lower than atomistic-step's $\beta = 50$); reset every 50th step; addition probability 0.6, removal probability 0.4; a random fragment of the target ligand is used as the starting structure for recovery tests.
- **Why it matters:** The lower $\beta$ is required because fragment additions/removals cause larger score changes than atom-level modifications — a lower selectivity threshold is needed to avoid rejecting all fragment additions.
- **Plain-language expansion:** With $\beta = 50$ and a large score change $\Delta s$, the acceptance probability $\exp(-50 \cdot \Delta s)$ would be essentially 0 for any fragment addition that worsens the score even slightly — the simulation would get stuck immediately. $\beta = 5$ allows the simulation to accept worse structures with much higher probability, enabling it to traverse the larger score barriers that fragment-level changes introduce. The reset every 50th step (more frequent than the 100th-step reset in atomistic-step MC) compensates for the increased randomness.

## 3. Formulas and figures unpacked

**Formula: Metropolis acceptance probability (Eq. 1)**

$$P(\Delta s) = \exp(-\beta \cdot \Delta s)$$

- **What it expresses:** The probability of accepting a move that *worsens* the score by $\Delta s$.

| Symbol | Name | Range/Units | Intuition |
|--------|------|-------------|-----------|
| $P(\Delta s)$ | acceptance probability | [0, 1] | probability the worse move is kept |
| $\Delta s$ | score decrease | > 0 (worsening) | how much worse the new structure is |
| $\beta$ | inverse temperature | > 0 (dimensionless) | selectivity: high = rarely accept bad moves |

- **Worked example:** Atomistic-step MC with $\beta = 50$. A proposed atom change worsens the score by $\Delta s = 0.02$. Acceptance probability: $\exp(-50 \times 0.02) = \exp(-1) \approx 0.37$ — a 37% chance of accepting. If $\Delta s = 0.1$: $\exp(-50 \times 0.1) = \exp(-5) \approx 0.007$ — less than 1%. So small worsening moves are occasionally accepted (helping escape local optima), but large worsening moves are almost always rejected.
- **Fragment-based contrast:** With $\beta = 5$ and $\Delta s = 0.1$: $\exp(-5 \times 0.1) = \exp(-0.5) \approx 0.61$ — the same score drop is accepted 61% of the time. This reflects the coarser granularity of fragment moves.
- **Connection:** This formula is the only equation in the MC simulation section. It governs every single step of both MC protocols. The choice of $\beta$ is one of the most important hyperparameters — see §5 for what happens when $\beta$ is wrong.

**The 9 chemical operations (probability table)**

| Operation | Standard probability | Purpose |
|-----------|---------------------|---------|
| Adding small chemical groups (CH₃, OH) | 0.3 | Main growth driver |
| Removing atoms | 0.2 | Pruning; enables backtracking |
| Changing atom types | 0.1 | Fine-tuning heteroatom composition |
| Adding atoms in chain | 0.15 | Extends linkers; helps avoid local minima |
| Changing bond types (single ↔ double) | 0.025 | Modulates unsaturation |
| Forming rings | 0.025 | Ring assembly |
| Breaking up rings | 0.05 | Escapes ring local minima ([[Ring Local Minima in MC]]) |
| Turning rings aromatic | 0.05 | Converts nonaromatic to aromatic and vice versa |
| Rearranging bonds | 0.1 | Structural rearrangement; helps avoid local minima |

Note: probabilities sum to 1.0. ✓

> [!Hint] Two operations are explicitly labelled escape mechanisms
> "Adding atoms in chain" and "Rearranging bonds" are each described as "not strictly necessary to reach all chemical structures, but helpful to avoid local minima." Their existence reflects a deliberate design decision: the move set was tuned not just for coverage but for navigability of the landscape. Removing them would theoretically still allow reaching any SMILES but would trap the simulation more often.

## 4. The argument in one diagram

**Start from benzene (SMILES: c1ccccc1)** → **At each step, randomly pick one of 9 operations with tuned probability** → **Apply via rdkit, check validity** → **Score new structure via Chai-1 + biases** → **Accept if better; accept with probability $\exp(-\beta \Delta s)$ if worse** → **Every 100th step, reset to best found** → **After 2000 steps, output the best compound**

Fragment-based variant: same flow but with "add fragment" and "remove fragment" replacing the 9 operations, $\beta = 5$ instead of 50, reset every 50th step.

## 5. What is easy to miss

> [!caution] β was tuned on only three targets
> The paper states that step probabilities "were determined by testing different probability sets on three targets (bromodomain, p38 map kinase, and Pim-1 kinase)." These are the same targets later used in the Results section — so the hyperparameters and the evaluation share data. This means the probability set is optimised for exactly the tested cases. Performance on new targets may differ.

> [!Hint] The reset to best is as important as the Metropolis criterion
> The periodic reset (every 100th step for atomistic, every 50th for fragment-based) is a critical design choice that the paper describes in one sentence. It implements an "iterated hill-climbing" strategy on top of the Metropolis walk: explore randomly, then collapse back to the current best before exploring again. This is a well-known technique in stochastic optimisation but the paper does not name it.

> [!caution] rdkit validity checking is a hidden filter
> The paper says rdkit "includes routines to ensure the chemical validity of the modified compound." This means many proposed MC moves are silently rejected before scoring because the modified SMILES is chemically invalid (e.g. an atom with too many bonds). The effective step acceptance rate is lower than the Metropolis formula suggests, because some steps never reach scoring at all.

> [!caution] Ring formation is underweighted
> Ring-forming operations (forming rings + breaking rings + aromatisation) have a combined probability of 0.025 + 0.05 + 0.05 = 0.125. Yet rings are central to most drug-like compounds. This is exactly why ring-containing target compounds are hard to recover (Table 1, [[Ring Local Minima in MC]]): not only do intermediate open-chain states score poorly, but ring operations are also relatively rare.

## 6. Open questions

- **Author's own caveat:** "Optimal parameter sets might also vary for different protein targets which will be investigated in future studies." — The current probabilities are a best-guess from three specific targets, not a principled optimisation.

> [!question] Why nine operations and not more?
> The 9 operations are presented without justification of completeness. Is this set sufficient to reach all drug-like structures from benzene? The paper claims the set can reach "most structures" but does not prove completeness.

> [!question] Why benzene as the starting structure?
> Benzene is chosen as the simplest aromatic compound. But aromatic rings are not universal in drugs — could starting from a non-aromatic scaffold change the outcome? The paper does not test alternative starting structures.

> [!question] Is the 100-step reset schedule principled?
> The reset period (100 steps for atomistic, 50 steps for fragment) is presented as a given, not derived. How sensitive are results to this schedule?

## 7. Connections

**Within the paper**
- The 9 operations and their ring-forming difficulty → [[Ring Local Minima in MC]] → Table 1 (the two failed recovery cases)
- β and reset schedule → Figure 3A (score trajectory showing rapid initial rise followed by gradual improvement)
- Fragment-based MC parameters → Results §Fragment-based MC simulations

**Outside the paper**
- [[Metropolis criterion]] — the mathematical foundation for the acceptance rule; Metropolis *et al.* 1953 is the original paper.
- [[Monte Carlo simulation]] — the broader algorithmic framework; the "simulated annealing" variant is closely related but uses a decreasing β schedule, which AI-MCLig does not.
- [[BRICS decomposition]] — the fragment decomposition method; Degen *et al.* 2008 is the original reference.

## 8. Test yourself

1. **(Recall)** List all 9 chemical operations and their approximate probabilities. Which two are described as escape mechanisms?
2. **(Recall)** What is the β value for atomistic-step MC? For fragment-based MC? Why are they different?
3. **(Recall)** How often is the structure reset to the best found so far in each protocol?
4. **(Comprehension)** A proposed MC move worsens the score by Δs = 0.05. Compute the acceptance probability for (a) atomistic-step MC with β = 50 and (b) fragment-based MC with β = 5.
5. **(Comprehension)** Why does the fragment-based protocol use a lower β than the atomistic-step protocol? What would happen if you used β = 50 for the fragment protocol?
6. **(Application)** The simulation is at step 847. The current best compound has score 0.87. The proposed atom change gives score 0.83 (worse by 0.04). Should the move be accepted? Compute the probability and state the criterion.
7. **(Critical)** The step probabilities were tuned on the same three targets used in the Results section. Is this a methodological problem? What would a more rigorous evaluation protocol look like?
8. **(Critical)** Ring-forming operations have a combined probability of 12.5%. Two target compounds in the recovery test were not recovered perfectly (Table 1). Are these facts related? What would you change to improve ring recovery?

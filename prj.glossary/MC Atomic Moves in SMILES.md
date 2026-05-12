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
readOrderIndex: 3
---

# MC Atomic Moves in SMILES

Each of the 9 chemical operations in AI-MCLig's Monte Carlo search modifies the candidate molecule's [[SMILES]] string through a specific, well-defined transformation. This entry shows every operation as a concrete before → after SMILES pair, highlighting exactly which characters in the string change.

Starting molecule used throughout: **benzene** (`c1ccccc1`), the paper's default seed structure.

See [[MC Search Components]] for the probability weights and the role of each operation in the search strategy.

---

## The 9 operations

### 1 · Adding small chemical groups — p = 0.30

```
c1ccccc1          →   Cc1ccccc1        (add –CH₃; benzene → toluene)
c1ccccc1          →   Oc1ccccc1        (add –OH;  benzene → phenol)
```

**What changes in SMILES:** A new atom token (`C`, `O`, `N`, `F`, …) is prepended or appended as a substituent. No ring-closure digits are touched — the group hangs off the existing structure as a terminal atom or branch.

---

### 2 · Removing atoms — p = 0.20

```
Cc1ccccc1         →   c1ccccc1         (remove –CH₃; toluene → benzene)
```

**What changes:** The atom token is deleted from the string. Any atoms attached only to the removed atom disappear with it.

---

### 3 · Changing atom types — p = 0.10

```
c1ccccc1          →   c1ccncc1         (C → N; benzene → pyridine)
CCO               →   CCN              (O → N; ethanol → ethylamine)
```

**What changes:** One atom token is replaced by a different element symbol. The connectivity (bonds, ring digits, branches) remains identical; only the element label changes.

---

### 4 · Adding atoms in chain — p = 0.15

```
Cc1ccccc1         →   CCc1ccccc1       (extend methyl to ethyl; toluene → ethylbenzene)
CCN               →   CCCN             (extend chain by one C; ethylamine → propylamine)
```

**What changes:** An extra atom token is inserted into an existing chain, lengthening a linker or side chain by one atom. No new branching or ring closure is introduced.

---

### 5 · Changing bond types (single ↔ double) — p = 0.025

```
CC                →   C=C              (ethane → ethylene)
C1CCCCC1          →   C1CCCC=C1        (cyclohexane → cyclohex-1-ene)
```

**What changes:** An implicit single bond becomes an explicit `=` double bond (or vice versa). In non-aromatic ring atoms this means inserting or removing the `=` character between two adjacent tokens.

---

### 6 · Forming rings — p = 0.025

```
CCCCc1ccccc1      →   C1CCc2ccccc21    (n-butylbenzene → tetralin)
CCCC              →   C1CCC1           (butane → cyclobutane)
```

**What changes:** A pair of matching ring-closure digits is added — one to the atom that will form one end of the new bond and one to the atom at the other end. The number itself (e.g. `1`) appears twice in the string and encodes the new ring bond.

---

### 7 · Breaking up rings — p = 0.05

```
C1CCCCC1          →   CCCCCC           (cyclohexane → hexane)
C1CCC1            →   CCCC             (cyclobutane → butane)
```

**What changes:** The matching ring-closure digit pair is removed, deleting the bond it encoded and opening the ring to a chain.

---

### 8 · Turning rings aromatic (and reverse) — p = 0.05

```
C1=CC=CC=C1       →   c1ccccc1         (Kekulé benzene → aromatic benzene)
C1=CN=CC=C1       →   c1ccncc1         (Kekulé pyridine → aromatic pyridine)
c1ccncc1          →   C1=CN=CC=C1      (reverse: aromatic → Kekulé form)
```

**What changes:** Uppercase ring-atom tokens (`C`, `N`) become lowercase (`c`, `n`), which in SMILES signals aromatic membership. Explicit `=` double bonds in the Kekulé form are removed because aromaticity makes them implicit. The reverse operation re-introduces them.

> [!Info] Kekulé vs aromatic SMILES
> SMILES allows the same benzene ring to be written two ways: the **Kekulé form** uses alternating explicit single and double bonds (`C1=CC=CC=C1`), while the **aromatic form** uses lowercase letters (`c1ccccc1`). RDKit treats both as identical internally — the "make aromatic" operation is a notation switch, not a change in electron count. It matters for ring-containing MC steps because intermediate states may only arise in one form.

---

### 9 · Rearranging bonds — p = 0.10

```
CCCC              →   CC(C)C           (n-butane → isobutane)
c1ccccc1CC=O      →   CC(=O)c1ccccc1   (phenylacetaldehyde → acetophenone)
```

**What changes:** Branch parentheses `(...)` are introduced, moved, or removed to redirect the connectivity graph. The same set of atoms is present but the topology differs — a linear chain becomes branched, or a functional group migrates from one attachment point to another.

---

## Summary table

| # | Operation | Prob. | SMILES change |
|---|-----------|-------|---------------|
| 1 | Add small group | 0.30 | New atom token added outside ring digits |
| 2 | Remove atom | 0.20 | Atom token (and any only-attached atoms) deleted |
| 3 | Change atom type | 0.10 | One element symbol replaced (e.g. `c` → `n`) |
| 4 | Add atom in chain | 0.15 | Atom token inserted into an existing chain |
| 5 | Change bond type | 0.025 | `=` inserted or removed between two tokens |
| 6 | Form ring | 0.025 | Matching ring-closure digit pair added |
| 7 | Break ring | 0.05 | Ring-closure digit pair removed |
| 8 | Make aromatic | 0.05 | Uppercase ↔ lowercase ring atoms; `=` appears/disappears |
| 9 | Rearrange bonds | 0.10 | Branch parentheses `(...)` introduced, moved, or removed |

Probabilities sum to 1.00. ✓

---

## TLDR

Every MC move is a local edit to a SMILES string: add/remove tokens, swap element symbols, insert/remove `=` characters, toggle ring-closure digits, or shift branch parentheses. RDKit checks the result for chemical validity before scoring.

---

## Related entries

- [[SMILES]] — full syntax reference; required reading before this entry
- [[MC Search Components]] — probabilities, purpose, and the two-component design
- [[Ring Local Minima in MC]] — why operations 6–8 (ring moves) are especially hard for the search
- [[BRICS decomposition]] — fragment-based MC uses a coarser version of these operations at the fragment level

## Test yourself

1. Write the SMILES for phenol two different ways: starting from benzene via operation 1, and by changing atom type on an existing oxygen compound.
2. What single SMILES edit converts pyridine (`c1ccncc1`) to pyrimidine (two nitrogens at positions 1 and 3)?
3. Cyclohexane is `C1CCCCC1`. Apply operation 5 once to get cyclohex-2-ene. Write the SMILES.
4. Why does making a ring aromatic (operation 8) not change the molecular formula, only the SMILES notation?
5. Which operation would you use to convert `CC(=O)N` (acetamide) to `CC(=O)O` (acetic acid)? Write both SMILES and identify the change.

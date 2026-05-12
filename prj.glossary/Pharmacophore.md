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

# Pharmacophore

The **3D arrangement of abstract chemical features** that a molecule must present in order to bind a given protein target and trigger (or block) its biological response. A pharmacophore is *not* a specific molecule — it is a pattern that many different molecules can satisfy.

The IUPAC definition: *"an ensemble of steric and electronic features that is necessary to ensure the optimal supramolecular interactions with a specific biological target structure and to trigger (or to block) its biological response."*

## The features

A pharmacophore is described in terms of feature *types* placed at specific 3D positions, not specific atoms. The standard feature alphabet:

| Feature | What it represents | Typical chemical groups |
|---|---|---|
| H-bond donor | An atom that can give a hydrogen | –OH, –NH, –NH₂ |
| H-bond acceptor | An atom with a lone pair | C=O, ether O, pyridine N |
| Hydrophobic | A non-polar volume | alkyl chains, aromatic rings |
| Aromatic ring | A π-system available for stacking | phenyl, pyridyl, furyl |
| Positive ionizable | A group that protonates at physiological pH | amines |
| Negative ionizable | A group that deprotonates | carboxylates, phosphates |

Two molecules that look chemically different — for instance an amide vs. a sulfonamide — can present the *same* pharmacophore if their donor/acceptor/hydrophobic features sit at the same relative 3D positions. This is why pharmacophore models can find "scaffold hops": chemically novel compounds that hit the same target.

## Why it matters in AI-MCLig

The paper uses the word in the [[Atomistic vs Fragment-based MC]] discussion: a fragment swap can *"rewire the pharmacophore"*, meaning the spatial pattern of donors/acceptors/aromatic centres is reshuffled in one step. An atomistic move changes one feature at most; a fragment move can change several at once. This is the underlying reason the fragment protocol jumps so much farther in chemical space and needs a much lower $\beta$ in the [[Metropolis criterion]].

## Pharmacophore vs related ideas

- **vs. scaffold ([[Molecular scaffold]])** — the scaffold is the *actual core structure* of a molecule (a graph of atoms); the pharmacophore is the *abstract pattern of features* that core presents to the protein. Many scaffolds can share one pharmacophore.
- **vs. fingerprint ([[Molecular fingerprint]])** — fingerprints encode 2D topological patterns for similarity search; pharmacophores encode 3D feature geometry for biological recognition.
- **vs. binding mode** — the binding mode is how *one specific molecule* sits in the pocket; the pharmacophore is the geometric requirement that *any* binder must satisfy.

## TLDR

The abstract 3D pattern of chemical features (donors, acceptors, aromatics, charges, hydrophobes) that a molecule must present to bind a target. Distinct from the molecule itself: different scaffolds can satisfy the same pharmacophore.


#Question features of the interaction at binding site, is the MD somehow encoded? (pharmacophore)
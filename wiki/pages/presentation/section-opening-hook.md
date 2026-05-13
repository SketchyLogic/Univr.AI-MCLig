---
CreatedAt: 2026-05-13
LastUpdateAt: 2026-05-13
LastReviewAt: null
ReviewerIds:
  - admin
OwnerIds:
  - admin
IssueNotes: null
GeneratedBy: claude-sonnet-4-6
readOrderIndex: 0
---

# Opening Hook: A Key for a Moving Lock

*Drugs are keys — but the lock changes shape every time you insert one.*

- A drug is a small molecule that binds tightly to a protein and changes its function
- Finding such molecules is the central challenge of drug discovery
- The complication: proteins are flexible — the binding pocket **adapts** when a ligand arrives

![[prj.home/OriginalPaper.Structural.GPCR.DeNovoProteinLigand.btag027.pdf#page=1]]
*Figure — title and abstract (p. 1). The paper's central claim: full protein flexibility at every design step.*

> [!Example] Analogy for the audience
> Imagine designing a key for a lock — but the lock is made of rubber. A rigid key designed from a frozen mould will fit poorly once the lock flexes into its natural shape. AI-MCLig designs the key while the lock is already moving.

# Presenter Notes

**Part 1 — Opening Hook**

Open with the analogy directly: "Imagine you need to design a key, but the lock changes shape every time you put a key in." Pause. "That is the protein–ligand binding problem." Most drugs are small organic molecules that must physically fit into a pocket on a protein surface and stay there long enough to change the protein's behaviour. Finding those molecules computationally — *de novo* design — is one of the central goals of modern drug discovery. The twist this paper addresses: virtually every existing computational method assumes the protein pocket is rigid, like a fixed mould. But proteins are flexible. The pocket adapts. Today's talk is about a new approach that lets the protein move throughout the entire design process.

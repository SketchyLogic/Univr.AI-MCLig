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
readOrderIndex: -2
---

# pLDDT

**predicted Local Distance Difference Test** — a per-residue (or per-atom) confidence score produced by AI structure prediction models like AlphaFold, Chai-1, and Boltz-1. It estimates how accurately the model has predicted the local structure at each position.

**Scale:** 0–100
- >90: very high confidence; the predicted structure is likely to be very accurate
- 70–90: confident
- 50–70: low confidence; structural details may be wrong
- <50: very low confidence; the region is likely disordered or the model is guessing

**In protein-ligand contexts:** When Chai-1 or AlphaFold3 predicts a protein-ligand complex, pLDDT scores are generated for both protein residues and ligand atoms. A high pLDDT at the binding interface suggests the model is confident about the predicted binding mode. In AI-MCLig, the Chai-1 confidence score (related to pLDDT) is used as a proxy for binding affinity — imperfectly, but enough to guide the MC search.

#Question is finding high affinity the functional reqr? (es emoglobina-ossigeno | toxic molecules)
#Question stessa domanda per 'low energy states' (es prioni)
#Question i dettagli sull'ambiente di reazione possono essere sempre approssimati?
#Research esempi di proteine che si comportano in maniera significativamente diversa in ambienti diversi senza perdere funzionalità (trovare organismo con ciclo di vita strano)

**Limitation:** pLDDT measures model confidence, not experimental accuracy. A high pLDDT means the model is self-consistent, not that it is correct. For regions without training data (novel folds, unusual ligands), pLDDT can be misleadingly high.

## TLDR
A 0–100 confidence score per residue from AlphaFold/Chai-1; high values indicate the model is confident in its local structure prediction; used as a binding affinity proxy in AI-MCLig.



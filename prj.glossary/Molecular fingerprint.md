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

# Molecular fingerprint

A **molecular fingerprint** is a compact numerical representation of a molecule's structural features, encoded as a fixed-length bit-vector (a string of 0s and 1s) or a count vector. Each position in the vector represents a particular structural feature; a "1" means the molecule contains that feature, "0" means it does not.

Fingerprints are used for:
- **Similarity searching:** Compare two molecules by computing [[Tanimoto similarity]] between their fingerprint vectors — fast enough to screen millions of compounds in seconds.
- **Machine learning:** Fingerprints serve as numerical feature vectors for training models to predict activity, toxicity, or other properties.
- **Substructure filtering:** Some fingerprint types can confirm whether a given substructure is present in a molecule.

**Common types:**
- **[[ECFP4]]** (Extended Connectivity Fingerprint): encodes circular neighborhoods of each atom; the most widely used in drug discovery.
- **MACCS keys:** 166 predefined structural keys (specific substructures); simpler but less information-rich.
- **RDKit fingerprint:** a topological path-based fingerprint.
- **Morgan fingerprint:** equivalent to ECFP; produced by the Morgan algorithm.

**Limitation:** Fingerprints are 2D descriptions — they do not capture 3D shape or the spatial arrangement of functional groups. Two molecules with similar 3D pharmacophores can have very different fingerprints if their atom connectivity differs.

#Research Fingerpinting di molecular dynamics. (Molecular flipbook). Da dove si possono raccogliere i dati? Come definire la struttura dati? Array<fingerprints+angles>?
## TLDR
A binary or count vector encoding the structural features of a molecule; enables fast similarity searching and machine learning but does not capture 3D geometry.


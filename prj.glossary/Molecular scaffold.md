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

# Molecular scaffold

A **molecular scaffold** (also called the "core" or "ring system") is the central framework of a drug-like molecule — typically the fused or connected ring system from which side chains and functional groups hang. When chemists talk about the "scaffold" of a drug, they mean the rigid backbone that defines its overall shape and connectivity.

Scaffold definition is not entirely trivial. The most common computational definition, implemented in tools like [[ScaffoldGraph]] and the [[HierS algorithm]], decomposes a molecule recursively: ring systems are identified, linkers (chains connecting rings) are separated from side chains, and a hierarchy of scaffolds is produced from the full ring assembly down to individual rings. This hierarchy is useful because "scaffold hopping" can happen at different levels — replacing a full bicyclic system or just swapping one ring within it.

**Why scaffolds matter in drug discovery:**
- They strongly influence 3D shape, rigidity, and how the molecule fits a binding pocket.
- They are often the subject of patents — a different scaffold can mean freedom to operate.
- They determine many physicochemical properties: solubility, metabolic sites, and oral bioavailability are all scaffold-dependent.

**Analogy:** The scaffold of a molecule is like the chassis of a car — it defines the basic geometry and many performance characteristics, while the side chains are like the body panels and accessories attached on top.

## TLDR
The central ring system of a drug-like molecule; swapping it for a different one is called scaffold hopping and is a key strategy in drug design.


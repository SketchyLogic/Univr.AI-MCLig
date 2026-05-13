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

# Limitations and Future Directions

*AI-MCLig is simple, flexible, and complementary to existing methods — but it is only as good as its scoring oracle.*

- **Compute cost**: ~20 h per 2000-step run on an NVIDIA RTX4090 — not yet suitable for routine high-throughput screening
- **Imperfect oracle**: Chai-1 confidence score only loosely correlates with binding affinity (Figure 1 scatter — p. 5)
- **Synthesisability**: SA score is an estimate; generated structures require chemist review before synthesis
- **Fragment library quality**: random ChEMBL fragments are generic; target-specific libraries need prior knowledge of known binders

![[prj.home/OriginalPaper.Structural.GPCR.DeNovoProteinLigand.btag027.pdf#page=5]]
*Figure 1 — the scatter in Chai-1 vs. experimental affinity (p. 5) illustrates the primary bottleneck: oracle precision.*

> [!Example] The upside of limitations
> Each limitation points directly to a research direction. Better AI oracles (Boltz-2, AF3) → better scoring. Faster structure prediction → shorter runs. Smarter move sets that avoid ring traps → faster convergence. AI-MCLig is a platform, not just a single paper.

# Presenter Notes

**Part 1 — The limitations, honestly**

The authors are admirably transparent about the weaknesses of their approach. The compute cost is real: 20 hours on a high-end GPU per run means this is not yet a tool you can deploy at industrial screening scale. The scoring oracle — Chai-1 confidence — is genuinely imperfect: Figure 1 shows that even at the same Chai-1 score, experimental binding affinities can vary by several kcal/mol. The SA score is a heuristic; it flags likely synthesis problems but cannot guarantee that a compound is actually synthesisable. And the fragment library is only as good as the fragments you put into it.

**Part 2 — Why this is still exciting**

Every one of these limitations is a known, tractable problem with an obvious research direction. Chai-1 will improve; AlphaFold3 and Boltz-2 are already more accurate. Compute costs will fall as hardware improves and as the prediction models are distilled into faster versions. Smarter MC move sets — designed specifically to handle ring systems better — could close the gap on the two recovery failures. The broader point: this paper demonstrates a new *concept* — using an AI structure predictor as a live, flexible scoring oracle inside a generative loop. That concept will outlast any particular implementation. As the authors note in the conclusion, AI-MCLig is complementary to docking and to deep generative models. It fills a specific gap — rapid, flexible-pocket, de novo design with no training required — and it does so with a method that is transparent, modifiable, and open-source.

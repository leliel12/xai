# Causal Validity and Stability of Attribution Methods in Language Models
*A reproducible, comparative evaluation on open-weight models*

**Course:** Explainable AI — Prof. Juan B. Cabral, FAMAF–UNC · **Final Integrative Project — Proposal (draft)**

## Members
Individual work. **Horacio Brizuela**.

## Objective
**Research question:** *do frontier mechanistic attribution methods produce explanations that are simultaneously more causally valid and more stable than classical input-attribution baselines, and is there a measurable tension between the two properties?*

**Working hypothesis:** causal validity and stability do not move together — methods that better localise the components a model actually uses are not necessarily the most stable under benign perturbations. As a secondary contribution we propose, and empirically justify, a **stability index** for attribution methods.

A methodological premise frames the design: mechanistic interpretability requires white-box access (weights and architecture), so closed frontier models cannot be examined from the outside. We therefore study **open-weight models as accessible stand-ins**, and include a closed model (Claude Opus, queried as a black box) only as a reference point that makes the access boundary explicit — itself a finding worth stating.

## Experimental proposal
- **Models — white-box:** GPT-2 small and Gemma-2-2B (open weights). **Reference — black-box:** Claude Opus via API, for contrast only.
- **Tasks:** controlled prompt sets built from clean/corrupted minimal pairs (Indirect Object Identification; multi-hop factual recall), so that the causal target is known by construction.
- **Methods compared:** gradient saliency (baseline); contrastive saliency (Yin & Neubig, 2022); sparse-autoencoder features; and attribution-graph circuits (Anthropic's open-source *circuit-tracer*).
- **Causal validity:** measured by activation patching and ablation (Meng et al., 2022; Wang et al., 2022) — whether the components an explanation highlights causally carry the prediction, quantified as recovery of the clean logit difference.
- **Stability:** dispersion of the recovered attribution under transcoder re-seeding, prompt paraphrase and token perturbation, summarised by the proposed stability index.
- **Reproducibility:** the whole pipeline runs in Google Colab (free and Pro tiers); code and cached attribution graphs are released with the paper.

## Plan of activities (tentative, ~6 weeks)
- **W1** — Reproduce the toolchain; load models; build the controlled prompt sets.
- **W2** — White-box baselines on GPT-2 (patching + contrastive saliency); fix the metrics.
- **W3** — Attribution graphs on Gemma-2-2B (*circuit-tracer*); extract SAE features.
- **W4** — Stability sweep (re-seeding, paraphrase, perturbation); compute the stability index.
- **W5** — Analysis: comparative tables, controlled statistics, ablations.
- **W6** — Write-up (ACM template, ≤12 pp) and online oral defence.

## Link to the course
The project sits squarely in mechanistic interpretability (circuits, sparse autoencoders, activation patching) and extends the contrastive-explanation thread of Lecture 24. It instantiates two of the suggested experiment axes at once: a critical, comparative evaluation of XAI methods, and a new metric with empirical justification. A natural continuation (2027) is to test whether the stability ordering observed on small open models transfers to larger, reasoning-capable open models.

## References
- Yin, K., & Neubig, G. (2022). *Interpreting Language Models with Contrastive Explanations.* EMNLP 2022. arXiv:2202.10419.
- Meng, K., Bau, D., Andonian, A., & Belinkov, Y. (2022). *Locating and Editing Factual Associations in GPT.* NeurIPS 2022. arXiv:2202.05262.
- Wang, K., Variengien, A., Conmy, A., Shlegeris, B., & Steinhardt, J. (2022). *Interpretability in the Wild: a Circuit for Indirect Object Identification in GPT-2 Small.* arXiv:2211.00593.
- Cunningham, H., Ewart, A., Riggs, L., Huben, R., & Sharkey, L. (2023). *Sparse Autoencoders Find Highly Interpretable Features in Language Models.* arXiv:2309.08600.
- Ameisen, E., Lindsey, J., et al. (2025). *Circuit Tracing: Revealing Computational Graphs in Language Models.* Transformer Circuits Thread, Anthropic.
- *CIRCUS: Circuit Consensus under Uncertainty via Stability Ensembles.* (2026). arXiv:2603.00523.

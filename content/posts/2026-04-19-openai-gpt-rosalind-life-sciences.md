---
title: "OpenAI Introduces GPT-Rosalind, a Domain Model for Life Sciences Research"
date: 2026-04-19
originalDate: 2026-04-16
draft: false
tags: ["openai", "models"]
---

OpenAI [released GPT-Rosalind](https://openai.com/index/introducing-gpt-rosalind/), a reasoning model built for biology, drug discovery, and translational medicine — the first entry in a dedicated life sciences model series. The model is tuned for reasoning over molecules, proteins, genes, pathways, and disease biology, and for multi-step workflows like literature review, sequence-to-function interpretation, experimental planning, and data analysis. It's named after [Rosalind Franklin](https://en.wikipedia.org/wiki/Rosalind_Franklin), whose X-ray diffraction work helped establish the structure of DNA.

Access is gated. GPT-Rosalind ships as a research preview in ChatGPT, Codex, and the API for qualified U.S. Enterprise customers through a trusted-access program that screens for beneficial use, governance, and biosecurity controls. In parallel, OpenAI is publishing a free [Life Sciences research plugin](https://openai.com/index/introducing-gpt-rosalind/) for Codex on GitHub that connects any mainline model to over 50 public multi-omics databases, literature sources, and biology tools. Launch partners include Amgen, Moderna, the Allen Institute, Thermo Fisher Scientific, and Dyno Therapeutics; Los Alamos National Laboratory is a research partner on AI-guided protein and catalyst design.

On reported benchmarks, GPT-Rosalind scores 0.751 Pass@1 on [BixBench](https://bixbench.ai/) (bioinformatics and data analysis), ahead of GPT-5.4 (0.732), Grok 4.2 (0.698), and Gemini 3.1 Pro (0.550). It outperforms GPT-5.4 on 6 of 11 LABBench2 tasks. In a joint evaluation with Dyno Therapeutics on unpublished RNA sequences, best-of-ten submissions placed above the 95th percentile of 57 human AI-bio experts on prediction and around the 84th percentile on sequence generation.

Key points:

- **Domain-specific frontier model** — GPT-Rosalind is OpenAI's first life sciences model, not a general model with fine-tuning; optimized for scientific reasoning and tool use
- **Gated access** — research preview limited to qualified U.S. Enterprise customers under a trusted-access program with biosecurity screening
- **Free Codex plugin** — separate Life Sciences research plugin works with mainline models and connects to 50+ public scientific databases and tools
- **Benchmark leader** — 0.751 Pass@1 on BixBench, beats GPT-5.4 on 6/11 LABBench2 tasks, scores above the 95th percentile of human experts on an RNA prediction task
- **Preview pricing** — usage during the preview does not consume existing credits or tokens, subject to abuse guardrails

## References

1. [Introducing GPT-Rosalind for life sciences research](https://openai.com/index/introducing-gpt-rosalind/) — OpenAI announcement (April 16, 2026)
2. [BixBench](https://bixbench.ai/) — benchmark for real-world bioinformatics and data analysis tasks
3. [Rosalind Franklin](https://en.wikipedia.org/wiki/Rosalind_Franklin) — biochemist whose DNA research the model is named after

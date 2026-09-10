---
title: "API Reference"
description: "Reference documentation for LeMat-Synth's core Python classes and functions across the pipeline, data models, transformers, configuration, and evaluation metrics."
summary: ""
date: 2025-10-28T00:00:00+00:00
lastmod: 2025-10-28T00:00:00+00:00
draft: false
weight: 570
toc: true
seo:
  title: "API Reference"
  description: "Reference documentation for LeMat-Synth's core Python classes and functions across the pipeline, data models, transformers, configuration, and evaluation metrics."
  canonical: ""
  noindex: false
---

This section documents the core Python classes and functions that make up LeMat-Synth: the end-to-end pipeline orchestrator, the Pydantic data models it produces, the extractor and transformer components, the configuration objects, and the metrics and LLM judges used for evaluation.

This reference is a static snapshot generated from the LeMat-Synth source code and may drift from the latest release. For the most current API, see the [LeMaterial/lematerial-llm-synthesis](https://github.com/LeMaterial/lematerial-llm-synthesis) source repository.

- [Pipeline]({{< ref "/lemat-synth/api/pipeline" >}}) — The `SynthesisPerformancePipeline` orchestrator and its result models
- [Data Models]({{< ref "/lemat-synth/api/models" >}}) — Pydantic models for the synthesis ontology, papers, and performance/plot data
- [Transformers]({{< ref "/lemat-synth/api/transformers" >}}) — Material, synthesis, PDF, and plot data extractors, plus performance linking
- [Configuration]({{< ref "/lemat-synth/api/configuration" >}}) — Plot filter configuration, the LLM registry, and DSPy utilities
- [Metrics & Judges]({{< ref "/lemat-synth/api/metrics" >}}) — LLM judges and metrics used to evaluate extraction quality
{.no-bullets}

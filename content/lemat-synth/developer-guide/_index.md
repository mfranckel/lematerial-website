---
title: "Developer Guide"
description: "How LeMat-Synth's pipeline is structured, how human annotations are produced and consumed, and how to configure or extend it."
summary: ""
date: 2025-10-28T00:00:00+00:00
lastmod: 2025-10-28T00:00:00+00:00
draft: false
weight: 560
toc: true
seo:
  title: "Developer Guide"
  description: "How LeMat-Synth's pipeline is structured, how human annotations are produced and consumed, and how to configure or extend it."
  canonical: ""
  noindex: false
---

This section is for developers who want to contribute to or extend LeMat-Synth. It covers how the pipeline and repository are structured, how the human-annotated ground-truth evaluation set is built and consumed, and how to configure or swap the LLMs and other components that power each stage.

- [Architecture]({{< ref "/lemat-synth/developer-guide/architecture" >}}) — Pipeline stages, repository layout, and where to start when contributing a feature
- [Annotations]({{< ref "/lemat-synth/developer-guide/annotations" >}}) — The human-verified ground-truth evaluation set and how to add to it
- [Configuration & Models]({{< ref "/lemat-synth/developer-guide/configuration" >}}) — Hydra configuration, available LLM models, and extending the config
{.no-bullets}

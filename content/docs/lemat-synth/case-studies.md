---
title: "Case Studies"
description: "Three end-to-end case studies: thermocatalysis, superconductors, and porous materials."
summary: ""
date: 2025-10-28T00:00:00+00:00
lastmod: 2025-10-28T00:00:00+00:00
draft: false
weight: 540
toc: true
seo:
  title: "LeMat-Synth Case Studies"
  description: "Three end-to-end case studies showing LeMat-Synth applied to thermocatalysis, superconductors, and porous materials."
  canonical: ""
  noindex: false
---

LeMat-Synth ships with three fully worked case studies that demonstrate how to adapt the pipeline to different scientific domains.

| Domain | What is extracted | Key technique |
|--------|-------------------|---------------|
| [Thermocatalysis](#thermocatalysis) | Synthesis + NH₃-conversion curves | Multi-VLM benchmark |
| [Superconductors](#superconductors) | Synthesis + critical temperature *T*c | Text and geometric extraction |
| [Porous materials](#porous-materials) | Synthesis + adsorption isotherms | MOFs, zeolites, COFs |

---

## Thermocatalysis

**Goal:** Extract NH₃ decomposition synthesis procedures and conversion-versus-temperature performance curves from catalysis papers.

The workflow is split into two phases to enable efficient multi-model benchmarking:

1. **Slow phase (cached):** Synthesis extraction using an LLM. Results are cached so this runs once per paper regardless of how many VLMs you evaluate.
2. **Fast phase:** VLM-based figure extraction, run separately for each model you want to benchmark. Because synthesis extraction is cached, you can swap in a new VLM without re-processing all papers.

```python
from lemat_synth.case_studies.thermocatalysis import run_thermocatalysis

results = run_thermocatalysis(
    papers_dir="papers/thermocatalysis/",
    llm_model="gemini/gemini-1.5-flash",
    vlm_models=["gemini/gemini-1.5-pro", "anthropic/claude-3-5-sonnet"],
)
```

---

## Superconductors

**Goal:** Identify superconductor papers, extract synthesis procedures, and determine the critical temperature *T*c from text and from resistivity plots (ρ(*T*) or *R*(*T*)).

The pipeline:

1. Filters papers by the keyword `"resistivity"`.
2. Uses a VLM to verify that a ρ(*T*) or *R*(*T*) plot is present in the figure set.
3. Extracts *T*c via both text parsing and geometric analysis of the resistivity curve.

```python
from lemat_synth.case_studies.superconductors import run_superconductors

results = run_superconductors(
    papers_dir="papers/superconductors/",
    model="gemini/gemini-1.5-flash",
)
```

---

## Porous materials

**Goal:** Extract synthesis procedures and adsorption isotherms for MOFs, zeolites, and COFs.

The pipeline:

1. Identifies adsorption isotherm plots by axis labels (e.g. `"Pressure"`, `"Uptake"`) and units.
2. Extracts framework variants (e.g. HKUST-1, ZIF-8) and links them to their synthesis records.
3. Produces per-material JSON outputs with linking annotations.

```python
from lemat_synth.case_studies.porous_materials import run_porous_materials

results = run_porous_materials(
    papers_dir="papers/porous_materials/",
    model="gemini/gemini-1.5-flash",
    material_classes=["MOF", "zeolite", "COF"],
)
```

---

## Extending to a new domain

All three case studies follow the same four-component pattern, which you can reuse for any domain:

1. **`PlotFilterConfig`** — define axis label and unit patterns that identify relevant figures.
2. **Material extraction prompt** — write an LLM instruction that identifies domain-specific materials.
3. **Optional metric extractor** — extract a scalar metric from text or a figure.
4. **Output writer** — specify the JSON or CSV schema for your domain.

See the [Quick Start]({{< ref "/docs/lemat-synth/quickstart" >}}) and `notebooks/07_custom_domain.ipynb` for a step-by-step walkthrough.

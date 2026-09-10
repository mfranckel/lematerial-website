---
title: "Dataset Access"
description: "How to load and use the published LeMat-Synth dataset from HuggingFace."
summary: ""
date: 2025-10-28T00:00:00+00:00
lastmod: 2025-10-28T00:00:00+00:00
draft: false
weight: 530
toc: true
seo:
  title: "LeMat-Synth Dataset Access"
  description: "How to load and use the published LeMat-Synth dataset from HuggingFace."
  canonical: ""
  noindex: false
---

The LeMat-Synth corpus is published on HuggingFace and can be loaded without running the extraction pipeline yourself.

**Datasets:**

- [LeMat-Synth](https://huggingface.co/datasets/LeMaterial/LeMat-Synth) — structured synthesis records and performance data
- [LeMat-Synth-Papers](https://huggingface.co/datasets/LeMaterial/LeMat-Synth-Papers) — the underlying paper collection used to build the dataset

## Authenticate with HuggingFace

Some subsets require a HuggingFace account. Install the CLI and log in:

```bash
pip install huggingface_hub
huggingface-cli login
```

## Load with the `datasets` library

```python
from datasets import load_dataset

ds = load_dataset("LeMaterial/LeMat-Synth")
print(ds)
```

No API key or cost is required for the published dataset.

## Schema

Each record contains:

| Field | Type | Description |
|-------|------|-------------|
| `paper_id` | `str` | Unique paper identifier |
| `material` | `str` | Extracted material name |
| `synthesis_method` | `str` | Synthesis technique (e.g. wet impregnation) |
| `reagents` | `list` | Reagents with name, amount, and purity |
| `steps` | `list` | Ordered synthesis steps with conditions |
| `performance` | `dict` | Extracted metric name, value, unit, and conditions |
| `quality_scores` | `dict` | Per-dimension LLM confidence scores |
| `source_url` | `str` | Link to the original paper |

## Contributing annotations

The project actively solicits human annotations to measure and improve extraction quality. A Streamlit annotation app is available in the repository:

```bash
streamlit run app/annotator.py
```

See the [GitHub repository](https://github.com/LeMaterial/lematerial-llm-synthesis) for details.

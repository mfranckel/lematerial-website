---
title: "Dataset Access"
description: "Load the published LeMat-Synth dataset from HuggingFace without running the extraction pipeline."
summary: ""
date: 2025-10-28T00:00:00+00:00
lastmod: 2025-10-28T00:00:00+00:00
draft: false
weight: 523
toc: true
seo:
  title: "Dataset Access"
  description: "Load the published LeMat-Synth dataset from HuggingFace without running the extraction pipeline."
  canonical: ""
  noindex: false
---

LeMat-Synth publishes two datasets on the HuggingFace Hub. Both are **gated** —
access is granted automatically, but you must request it once and be
authenticated.

| Dataset | Contents | Use it for |
|---|---|---|
| [**LeMat-Synth**](https://huggingface.co/datasets/LeMaterial/LeMat-Synth) | Extracted synthesis procedures and figures, one row per synthesis | Using the published data directly — no extraction, no API keys, no cost |
| [**LeMat-Synth-Papers**](https://huggingface.co/datasets/LeMaterial/LeMat-Synth-Papers) | The intermediate corpus: ~81k papers as text, one row per paper | Finding papers to run the pipeline on, or reproducing the extraction |

{{< callout context="tip" title="Tip" >}}
If your question is *"has anyone already extracted this?"*, start with
**LeMat-Synth** and [Tutorial 1]({{< ref "/lemat-synth/tutorials" >}}). For a great many use
cases that is the entire job, and it costs nothing.
{{< /callout >}}

---

## Get access

**1 — Request access.** Open either dataset page while signed in to HuggingFace
and accept the terms. Approval is automatic and immediate.

**2 — Install the HuggingFace CLI.**

```bash
pip install -U "huggingface_hub"    # or, on macOS: brew install hf
```

**3 — Authenticate.**

```bash
hf auth login
```

Alternatively, put a token from
[huggingface.co/settings/tokens](https://huggingface.co/settings/tokens) into
`.env` as `HF_TOKEN=hf_…` — the CLI, the notebooks and the Hydra deployment
scripts all read it from there.

---

## Load it

```python
from datasets import load_dataset

synth = load_dataset("LeMaterial/LeMat-Synth", split="train")
print(synth[0]["synthesis"])
```

[Tutorial 1]({{< ref "/lemat-synth/tutorials" >}}) goes further — slicing the dataset by
synthesis method, material category and judge score, and turning a row back into
a `GeneralSynthesisOntology` object.

[Tutorial 2]({{< ref "/lemat-synth/tutorials" >}}) covers `LeMat-Synth-Papers`: filtering the
corpus by category and keyword, with whole-word matching and an optional LLM
relevance filter, to assemble your own input set.

---

## Using the corpus as pipeline input

The Hydra deployment scripts read `LeMat-Synth-Papers` directly — it is the
default data loader:

```bash
uv run examples/scripts/deployment/extract_synthesis_procedure_from_text.py
uv run ... data_loader.number_of_samples=10          # cap the run
uv run ... data_loader.architecture.split=chemrxiv   # pick a split
```

See [Configuration & Models]({{< ref "/lemat-synth/developer-guide/configuration" >}}#changing-the-data-source)
for the other data loaders (local text folders, the annotation subset).

---

## Troubleshooting

{{< callout context="note" title="GatedRepoError or a 401" >}}
You are either not authenticated
(`hf auth login`) or the access request has not been accepted on the dataset
page. Both datasets are gated separately — accepting one does not grant the
other.
{{< /callout >}}

More in [Troubleshooting]({{< ref "/lemat-synth/user-guide/troubleshooting" >}}).

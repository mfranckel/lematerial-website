---
title: "Quick Start"
description: "Install LeMat-Synth and run your first extraction in minutes."
summary: ""
date: 2025-10-28T00:00:00+00:00
lastmod: 2025-10-28T00:00:00+00:00
draft: false
weight: 520
toc: true
seo:
  title: "LeMat-Synth Quick Start"
  description: "Install LeMat-Synth and run your first extraction in minutes."
  canonical: ""
  noindex: false
---

## Requirements

- Python 3.11+
- [uv](https://docs.astral.sh/uv/) package manager

## Installation

Clone the repository and install dependencies:

```bash
git clone https://github.com/LeMaterial/lematerial-llm-synthesis.git
cd lematerial-llm-synthesis
uv venv -p 3.11 --seed
uv sync && uv pip install -e .
```

## API keys

Create a `.env` file with keys for your chosen LLM provider:

```bash
# Gemini (free tier available)
GEMINI_API_KEY=your_key_here

# Anthropic
ANTHROPIC_API_KEY=your_key_here

# Mistral
MISTRAL_API_KEY=your_key_here

# OpenAI
OPENAI_API_KEY=your_key_here
```

On macOS/Linux, source the file before running:

```bash
source .env
```

## CLI usage

Extract synthesis data from a single paper:

```bash
lemat-synth extract paper.pdf
```

Process a folder of papers in batch:

```bash
lemat-synth extract papers/ --output results/
```

## Python API

Use the `DspySynthesisExtractor` class for programmatic access:

```python
from lemat_synth import DspySynthesisExtractor

extractor = DspySynthesisExtractor(model="gemini/gemini-1.5-flash")

result = extractor.extract("paper.pdf")
print(result.materials)          # list of extracted materials
print(result.synthesis_steps)    # structured procedure
print(result.performance_data)   # extracted plot values
print(result.quality_scores)     # confidence scores
```

## Dataset access (no extraction needed)

If you want to explore the pre-built corpus without running the pipeline yourself, head to [Dataset Access]({{< ref "/docs/lemat-synth/dataset" >}}).

## Interactive notebooks

Seven Jupyter notebooks walk you through the pipeline from data exploration to extending it for new domains. They are available in the [`notebooks/`](https://github.com/LeMaterial/lematerial-llm-synthesis/tree/main/notebooks) directory of the repository.

| Notebook | Description | Needs API key |
|----------|-------------|---------------|
| `01_explore_dataset.ipynb` | Load and explore the HuggingFace dataset | No |
| `02_single_paper.ipynb` | Run extraction on one PDF | Yes |
| `03_batch_extraction.ipynb` | Process a folder of papers | Yes |
| `04_thermocatalysis.ipynb` | Thermocatalysis case study | Yes |
| `05_superconductors.ipynb` | Superconductor case study | Yes |
| `06_porous_materials.ipynb` | MOFs and zeolites case study | Yes |
| `07_custom_domain.ipynb` | Extend the pipeline to a new domain | Yes |

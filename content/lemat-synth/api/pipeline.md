---
title: "Pipeline"
description: "Reference for SynthesisPerformancePipeline, the end-to-end orchestrator that chains material extraction, synthesis extraction, judging, and performance linking."
summary: ""
date: 2025-10-28T00:00:00+00:00
lastmod: 2025-10-28T00:00:00+00:00
draft: false
weight: 571
toc: true
seo:
  title: "Pipeline"
  description: "Reference for SynthesisPerformancePipeline, the end-to-end orchestrator that chains material extraction, synthesis extraction, judging, and performance linking."
  canonical: ""
  noindex: false
---

The `SynthesisPerformancePipeline` is the main orchestrator for end-to-end extraction. It chains material extraction, synthesis extraction, judge evaluation, and optional figure/performance linking.

## SynthesisPerformancePipeline

### `SynthesisPerformancePipeline`

```python
class SynthesisPerformancePipeline(material_extractor, synthesis_extractor, judge=None, linking_judge=None, plot_extractor=None, series_linker=None, plot_filter_config=None, figure_segmenter='dino', florence_repo_id='amayuelas/plot-visualization-florence-2-lora-32')
```

End-to-end pipeline: Paper → Materials → Synthesis → Performance Linking.

This pipeline processes scientific papers to extract:

1. Materials synthesized in the paper
2. Detailed synthesis procedures for each material
3. Performance data from plots, linked to specific materials

The pipeline is modular - each component can be customized or replaced.

Initialize the pipeline.

**Parameters:**

| Name | Type | Description | Default |
| --- | --- | --- | --- |
| `material_extractor` |  | Extractor for identifying materials in paper | *required* |
| `synthesis_extractor` |  | Extractor for synthesis procedures | *required* |
| `judge` |  | Optional judge for evaluating synthesis quality | `None` |
| `linking_judge` |  | Optional judge for evaluating linking quality | `None` |
| `plot_extractor` |  | Optional plot extractor (e.g. ClaudeLinePlotDataExtractor). | `None` |
| `series_linker` | `SeriesMaterialLinker \| None` | Optional linker for matching series to materials | `None` |
| `plot_filter_config` | `PlotFilterConfig \| None` | Optional config for filtering plots | `None` |
| `figure_segmenter` | `str` | Backend for figure segmentation, `"dino"` (default) or `"florence"`. | `'dino'` |
| `florence_repo_id` | `str` | HuggingFace LoRA repo used when `figure_segmenter="florence"`. | `'amayuelas/plot-visualization-florence-2-lora-32'` |

#### Methods

##### `extract_materials(paper_text)`

```python
def extract_materials(paper_text)
```

Step 1: Extract list of materials from paper text.

**Parameters:**

| Name | Type | Description | Default |
| --- | --- | --- | --- |
| `paper_text` | `str` | Full paper text | *required* |

**Returns:**

| Type | Description |
| --- | --- |
| `list[str]` | List of material names |

##### `extract_synthesis(paper_text, material)`

```python
def extract_synthesis(paper_text, material)
```

Step 2: Extract synthesis procedure for a single material.

**Parameters:**

| Name | Type | Description | Default |
| --- | --- | --- | --- |
| `paper_text` | `str` | Full paper text | *required* |
| `material` | `str` | Material name to extract synthesis for | *required* |

**Returns:**

| Type | Description |
| --- | --- |
| `tuple[GeneralSynthesisOntology, Any]` | Tuple of (synthesis ontology, evaluation result or None) |

##### `extract_figures(markdown_text)`

```python
def extract_figures(markdown_text)
```

Step 3: Extract and classify figures from markdown.

**Parameters:**

| Name | Type | Description | Default |
| --- | --- | --- | --- |
| `markdown_text` | `str` | Markdown text with embedded base64 images | *required* |

**Returns:**

| Type | Description |
| --- | --- |
| `list[FigureInfo]` | List of quantitative figure info objects |

##### `extract_plot_data(figures, paper_text, si_text='')`

```python
def extract_plot_data(figures, paper_text, si_text='')
```

Step 4: Extract data from quantitative plots.

**Parameters:**

| Name | Type | Description | Default |
| --- | --- | --- | --- |
| `figures` | `list[FigureInfo]` | List of FigureInfo for quantitative figures | *required* |
| `paper_text` | `str` | Full paper text for context | *required* |
| `si_text` | `str` | Supplementary information text | `''` |

**Returns:**

| Type | Description |
| --- | --- |
| `tuple[list[ExtractedLinePlotData], list[FigureInfo]]` | Tuple of (list of plot data, list of corresponding figures) |

##### `link_performance(materials, plots, figures)`

```python
def link_performance(materials, plots, figures)
```

Step 5: Link plot series to materials.

**Parameters:**

| Name | Type | Description | Default |
| --- | --- | --- | --- |
| `materials` | `list[str]` | List of material names | *required* |
| `plots` | `list[ExtractedLinePlotData]` | List of extracted plot data | *required* |
| `figures` | `list[FigureInfo]` | List of corresponding figure info | *required* |

**Returns:**

| Type | Description |
| --- | --- |
| `tuple[list[PlotMaterialMapping], LinkingStats]` | Tuple of (list of mappings, linking statistics) |

##### `process_paper(paper, skip_figures=False)`

```python
def process_paper(paper, skip_figures=False)
```

Process a single paper through the full pipeline.

**Parameters:**

| Name | Type | Description | Default |
| --- | --- | --- | --- |
| `paper` | `Paper` | Paper object with text content | *required* |
| `skip_figures` | `bool` | If True, skip figures and performance linking | `False` |

**Returns:**

| Type | Description |
| --- | --- |
| `PipelineResult \| None` | PipelineResult or None if processing failed |

##### `process_paper_async(paper, semaphore, skip_figures=False)`

```python
def process_paper_async(paper, semaphore, skip_figures=False)
```

Process one paper with concurrent LLM calls (asyncio + semaphore).

Same as `process_paper` but runs independent LLM calls in parallel:

- Materials: one call, then synthesis+judge per material in parallel
- Plot extraction: one call per figure in parallel
- Linking: one call per plot in parallel

**Parameters:**

| Name | Type | Description | Default |
| --- | --- | --- | --- |
| `paper` | `Paper` | Paper object with text content | *required* |
| `semaphore` | `Semaphore` | Cap on concurrent LLM calls | *required* |
| `skip_figures` | `bool` | If True, skip figures and performance linking | `False` |

**Returns:**

| Type | Description |
| --- | --- |
| `PipelineResult \| None` | PipelineResult or None if processing failed |

##### `save_results(result, output_dir)`

```python
def save_results(result, output_dir)
```

Save pipeline results to disk.

**Parameters:**

| Name | Type | Description | Default |
| --- | --- | --- | --- |
| `result` | `PipelineResult` | PipelineResult to save | *required* |
| `output_dir` | `str` | Base output directory | *required* |

## Result models

### `PipelineResult`

```python
class PipelineResult(BaseModel)
```

Bases: `BaseModel`

Complete result from the synthesis + performance pipeline.

### `SynthesisWithPerformanceEntry`

```python
class SynthesisWithPerformanceEntry(BaseModel)
```

Bases: `BaseModel`

A material's synthesis procedure with linked performance data.

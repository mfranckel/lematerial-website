---
title: "Metrics & Judges"
description: "Reference for LeMat-Synth's evaluation metrics and LLM judges: the synthesis judge, linking judge, and figure extraction metric."
summary: ""
date: 2025-10-28T00:00:00+00:00
lastmod: 2025-10-28T00:00:00+00:00
draft: false
weight: 575
toc: true
seo:
  title: "Metrics & Judges"
  description: "Reference for LeMat-Synth's evaluation metrics and LLM judges: the synthesis judge, linking judge, and figure extraction metric."
  canonical: ""
  noindex: false
---

## Synthesis judge

### `DspyGeneralSynthesisJudge`

```python
class DspyGeneralSynthesisJudge(lm, enable_reasoning_traces=False, confidence_threshold=0.7, signature=None, retry_temperatures=None)
```

Bases: `SynthesisJudgeInterface`

Enhanced DSPy module for evaluating GeneralSynthesisOntology extraction quality against source synthesis text.

Implements a two-level fallback chain for robust structured output.

Within each strategy, temperature is escalated on validation failures. API-level format errors (400/unsupported) skip immediately to the next strategy without wasting temperature retries.

Initialize the unified synthesis judge.

**Parameters:**

| Name | Type | Description | Default |
| --- | --- | --- | --- |
| `signature` | `type[Signature] \| None` | DSPy signature for evaluation | `None` |
| `lm` | `LM` | Language model for evaluation | *required* |
| `enable_reasoning_traces` | `bool` | Whether to include detailed reasoning | `False` |
| `confidence_threshold` | `float` | Minimum confidence threshold for reliable | `0.7` |
| `retry_temperatures` | `list[float] \| None` | Temperatures to try per strategy on content | `None` |

#### Methods

##### `forward(input)`

```python
def forward(input)
```

Evaluate extracted GeneralSynthesisOntology against source text.

Tries each format strategy in order. Within a strategy, retries at escalating temperatures on content-validation failures. API-level format errors skip immediately to the next strategy.

**Parameters:**

| Name | Type | Description | Default |
| --- | --- | --- | --- |
| `input` | `tuple[str, str] \| tuple[str, str, str]` | Tuple of (source_text, extracted_ontology_json) or (source_text, extracted_ontology_json, target_material) | *required* |

**Returns:**

| Type | Description |
| --- | --- |
| `GeneralSynthesisEvaluation` | Comprehensive evaluation of the ontology extraction |

### `GeneralSynthesisEvaluation`

```python
class GeneralSynthesisEvaluation(BaseModel)
```

Bases: `BaseModel`

Complete evaluation of GeneralSynthesisOntology extraction quality.

### `GeneralSynthesisEvaluationScore`

```python
class GeneralSynthesisEvaluationScore(BaseModel)
```

Bases: `BaseModel`

Evaluation scores for GeneralSynthesisOntology extraction quality. Scores are on a scale of 1.0 (poor) to 5.0 (excellent) with 0.5 increments.

### `make_general_synthesis_judge_signature`

```python
def make_general_synthesis_judge_signature(signature_name='GeneralSynthesisJudgeSignature', instructions=None, source_text_description='Original synthesis text for ontology extraction evaluation.', extracted_ontology_description='JSON representation of extracted GeneralSynthesisOntology.', target_material_description='Target material for synthesis context.', evaluation_description='Comprehensive evaluation of ontology extraction quality. CRITICAL: populate ALL fields — reasoning, confidence_level, all seven *_score and *_reasoning pairs inside scores, and scores.overall_reasoning. Omitting any field is invalid.')
```

Create a DSPy signature for GeneralSynthesisOntology evaluation.

**Parameters:**

| Name | Type | Description | Default |
| --- | --- | --- | --- |
| `signature_name` | `str` | Name of the signature class | `'GeneralSynthesisJudgeSignature'` |
| `instructions` | `str \| None` | Custom instructions for the evaluation | `None` |
| `source_text_description` | `str` | Description for source text input | `'Original synthesis text for ontology extraction evaluation.'` |
| `extracted_ontology_description` | `str` | Description for ontology JSON input | `'JSON representation of extracted GeneralSynthesisOntology.'` |
| `target_material_description` | `str` | Description for target material input | `'Target material for synthesis context.'` |
| `evaluation_description` | `str` | Description for evaluation output | `'Comprehensive evaluation of ontology extraction quality. CRITICAL: populate ALL fields — reasoning, confidence_level, all seven *_score and *_reasoning pairs inside scores, and scores.overall_reasoning. Omitting any field is invalid.'` |

**Returns:**

| Type | Description |
| --- | --- |
| `type[Signature]` | DSPy signature class for ontology evaluation |

## Linking judge

### `DspyLinkingJudge`

```python
class DspyLinkingJudge(lm, enable_reasoning_traces=False, confidence_threshold=0.7, signature=None)
```

Bases: `LinkingJudgeInterface`

DSPy module for evaluating synthesis-to-performance linking quality.

The judge receives:

1. The full paper text (source of truth).
2. The extracted synthesis ontologies (JSON list).
3. The extracted plot data (JSON list).
4. The linking output mapping syntheses to plot series (JSON).

It produces a `LinkingEvaluation` with four criterion scores (1-5 in 0.5 increments), nine failure-mode flags, and supporting reasoning.

#### Methods

##### `forward(input)`

```python
def forward(input)
```

Evaluate linking output against the paper and extracted data.

**Parameters:**

| Name | Type | Description | Default |
| --- | --- | --- | --- |
| `input` | `tuple[str, str, str, str]` | Tuple of (source_text, synthesis_json, plot_data_json, linking_output_json) | *required* |

**Returns:**

| Type | Description |
| --- | --- |
| `LinkingEvaluation` | A `LinkingEvaluation` instance. |

### `LinkingEvaluation`

```python
class LinkingEvaluation(BaseModel)
```

Bases: `BaseModel`

Complete evaluation of synthesis-to-performance linking quality.

### `make_linking_judge_signature`

```python
def make_linking_judge_signature(signature_name='LinkingJudgeSignature', instructions=None, source_text_description='Full paper text for linking evaluation.', synthesis_json_description='JSON list of extracted synthesis ontologies.', plot_data_json_description='JSON list of extracted plot data with series and coordinates.', linking_output_json_description='JSON linking output mapping syntheses to plot series.', evaluation_description='Comprehensive evaluation of linking quality.')
```

Factory for creating a customised LinkingJudge DSPy signature.

Follows the same pattern as `make_general_synthesis_judge_signature`.

## Figure extraction metric

### `FigureExtractionMetric`

```python
class FigureExtractionMetric(LinePlotExtractionMetric)
```

Bases: `LinePlotExtractionMetric`

#### Methods

##### `__call__(preds, refs, error_metric='rmse')`

```python
def __call__(preds, refs, error_metric='rmse')
```

Compute average RMSE or MAE across all matching series. For each series, it uses normalized-to-axis-sclae nearest-neighbor matching to find the closest points in the ground truth data to the extracted points from the LLM output. And then computes the error metric (RMSE or MAE) based on these matches.

##### `compute_scale(ground_truth)`

```python
def compute_scale(ground_truth)
```

Compute normalization scales for x and y.

##### `pointwise_rmse(extracted_coords, gt_coords, x_scale, y_scale)`

```python
def pointwise_rmse(extracted_coords, gt_coords, x_scale, y_scale)
```

Compute RMSE using nearest-neighbor matching for one series.

##### `pointwise_mae(extracted_coords, gt_coords, x_scale, y_scale)`

```python
def pointwise_mae(extracted_coords, gt_coords, x_scale, y_scale)
```

Compute MAE using nearest-neighbor matching for one series.

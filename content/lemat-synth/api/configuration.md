---
title: "Configuration"
description: "Reference for LeMat-Synth's configuration objects: plot filter configuration, the LLM registry, and DSPy setup utilities."
summary: ""
date: 2025-10-28T00:00:00+00:00
lastmod: 2025-10-28T00:00:00+00:00
draft: false
weight: 574
toc: true
seo:
  title: "Configuration"
  description: "Reference for LeMat-Synth's configuration objects: plot filter configuration, the LLM registry, and DSPy setup utilities."
  canonical: ""
  noindex: false
---

## PlotFilterConfig

`PlotFilterConfig` controls which plots are considered relevant for performance linking. Use the factory class methods to get pre-configured instances for your domain.

### `PlotFilterConfig`

```python
class PlotFilterConfig(BaseModel)
```

Bases: `BaseModel`

Configuration for filtering plots based on axis characteristics.

This allows domain-specific customization of what constitutes a "relevant" plot for performance data extraction.

**Attributes:**

| Name | Type | Description |
| --- | --- | --- |
| `x_axis_labels` | `list[str]` | Labels that indicate a relevant x-axis (case-insensitive) |
| `x_axis_units` | `list[str]` | Units that indicate a relevant x-axis (case-insensitive) |
| `y_axis_keywords` | `list[str]` | Keywords in y-axis label indicating performance metrics |
| `y_axis_units` | `list[str]` | Units that suggest performance data (e.g., "%") |
| `require_y_keyword_with_percentage` | `bool` | If True, % unit alone is not enough; the label must also contain a y_axis_keyword |
| `filter_x_axis` | `bool` | Whether to apply x-axis filtering |
| `filter_y_axis` | `bool` | Whether to apply y-axis filtering |

#### Methods

##### `is_relevant_x_axis(label, unit)`

```python
def is_relevant_x_axis(label, unit)
```

Check if x-axis indicates a relevant plot.

**Parameters:**

| Name | Type | Description | Default |
| --- | --- | --- | --- |
| `label` | `str \| None` | X-axis label (e.g., "Temperature") | *required* |
| `unit` | `str \| None` | X-axis unit (e.g., "°C") | *required* |

**Returns:**

| Type | Description |
| --- | --- |
| `bool` | True if x-axis matches configured criteria |

##### `is_relevant_y_axis(label, unit)`

```python
def is_relevant_y_axis(label, unit)
```

Check if y-axis indicates a performance metric.

**Parameters:**

| Name | Type | Description | Default |
| --- | --- | --- | --- |
| `label` | `str \| None` | Y-axis label (e.g., "Conversion") | *required* |
| `unit` | `str \| None` | Y-axis unit (e.g., "%") | *required* |

**Returns:**

| Type | Description |
| --- | --- |
| `bool` | True if y-axis matches configured criteria |

##### `for_catalysis()`

```python
def for_catalysis()
```

Factory method for catalysis domain (default configuration).

##### `for_electrochemistry()`

```python
def for_electrochemistry()
```

Factory method for electrochemistry domain.

##### `for_superconductivity()`

```python
def for_superconductivity()
```

Factory method for superconductivity domain (R(T) plots).

##### `for_coverage()`

```python
def for_coverage()
```

Factory method for porous materials (adsorption isotherm plots).

##### `no_filter()`

```python
def no_filter()
```

Factory method that disables all filtering (link all plots).

## LLM registry

### `LLMConfig`

```python
class LLMConfig(model, api_key=None, api_base=None, extra_kwargs=None)
```

A configuration for an LLM to instantiate with dspy. Includes the model name, and optional API key name in the environment (e.g. "OPENAI_API_KEY") and base URL. The latter is needed to call external providers with the OpenAI API. In DSPy, you can use dozens of LLM providers supported by LiteLLM. Simply follow their instructions for which {PROVIDER}_API_KEY to set and how to write pass the {provider_name}/{model_name} to the constructor.

**Parameters:**

| Name | Type | Description | Default |
| --- | --- | --- | --- |
| `model` | `str` | The name of the model to instantiate. | *required* |
| `api_key` | `str \| None` | The name of the environment variable containing the API key. | `None` |
| `api_base` | `str \| None` | The base URL of the API. | `None` |
| `extra_kwargs` | `dict \| None` | addtl model-specific parameters (e.g., thinking mode). | `None` |

### `LLMRegistry`

```python
class LLMRegistry(configs)
```

A registry of LLMs to instantiate with dspy.

**Parameters:**

| Name | Type | Description | Default |
| --- | --- | --- | --- |
| `configs` | `Mapping[str, LLMConfig]` | A mapping of model names to LLM configurations. | *required* |

## DSPy utilities

### `get_llm_from_name`

```python
def get_llm_from_name(llm_name, model_kwargs=None, system_prompt=None)
```

Get a dspy.LM from a given LLM name with cost tracking capabilities.

**Parameters:**

| Name | Type | Description | Default |
| --- | --- | --- | --- |
| `llm_name` | `str` | The name of the LLM to get. cf. LLM_REGISTRY | *required* |
| `model_kwargs` | `dict \| None` | A dictionary of model kwargs to pass to the LLM. | `None` |
| `system_prompt` | `str \| None` | A system prompt to inject at the start of every call. | `None` |

**Returns:**

| Type | Description |
| --- | --- |
| `LM` | A dspy.LM object with cost tracking capabilities. |

### `configure_dspy`

```python
def configure_dspy(lm, model_kwargs=None, system_prompt=None)
```

Configure dspy with a selected LLM with cost tracking.

**Parameters:**

| Name | Type | Description | Default |
| --- | --- | --- | --- |
| `lm` | `str` | LLM key to configure (cf. LLM_REGISTRY). | *required* |
| `model_kwargs` | `dict \| None` | Additional model kwargs (e.g., {"temperature": 0.7}). | `None` |
| `system_prompt` | `str \| None` | A system prompt to inject at the start of every call. | `None` |

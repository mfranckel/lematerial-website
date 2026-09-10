---
title: "Transformers"
description: "Reference for LeMat-Synth's extractor and transformer classes: material and synthesis extraction, PDF extraction, plot data extraction, and performance linking."
summary: ""
date: 2025-10-28T00:00:00+00:00
lastmod: 2025-10-28T00:00:00+00:00
draft: false
weight: 573
toc: true
seo:
  title: "Transformers"
  description: "Reference for LeMat-Synth's extractor and transformer classes: material and synthesis extraction, PDF extraction, plot data extraction, and performance linking."
  canonical: ""
  noindex: false
---

All extractors inherit from `ExtractorInterface[T, R]`. Each implements a `forward(input)` method (synchronous) and gets an `aforward(input)` method (async) for free.

## Base interface

### `ExtractorInterface`

```python
class ExtractorInterface(Module, Generic[T, R])
```

Bases: `Module`, `Generic[T, R]`

Generic interface for an extractor that takes an input of type T and returns an output of type R.

## Material extraction

### `DspyTextExtractor`

```python
class DspyTextExtractor(signature, lm, retry_temperatures=None)
```

Bases: `MaterialExtractorInterface`

A text extractor that uses dspy to extract any arbitrary text from the publication text.

Implements temperature escalation retry on failures to improve robustness against transient validation errors.

Initialize the extractor with a dspy signature and language model.

**Parameters:**

| Name | Type | Description | Default |
| --- | --- | --- | --- |
| `signature` | `Signature` | The dspy signature specifying input/output fields. | *required* |
| `lm` | `LM` | The language model to use for prediction. | *required* |
| `retry_temperatures` | `list[float] \| None` | Temperatures to try on failures. Defaults to [0.0, 0.3, 0.5]. | `None` |

#### Methods

##### `forward(input)`

```python
def forward(input)
```

Extract text from the given str using the language model and signature.

Retries at escalating temperatures on failure.

**Parameters:**

| Name | Type | Description | Default |
| --- | --- | --- | --- |
| `input` | `str` | The str from which to extract text. | *required* |

**Returns:**

| Name | Type | Description |
| --- | --- | --- |
| `str` | `str` | The extracted text from the str. |

### `make_dspy_text_extractor_signature`

```python
def make_dspy_text_extractor_signature(signature_name='DspyTextExtractorSignature', instructions='Extract the synthesis paragraph from the publication text.', input_description='The publication text to extract the synthesis paragraph from.', output_name='synthesis_paragraph', output_description='The extracted synthesis paragraph.')
```

Create a dspy signature for extracting text from publication text.

**Parameters:**

| Name | Type | Description | Default |
| --- | --- | --- | --- |
| `signature_name` | `str` | Name of the signature. | `'DspyTextExtractorSignature'` |
| `instructions` | `str` | Instructions for the signature. | `'Extract the synthesis paragraph from the publication text.'` |
| `input_description` | `str` | Description for the publication text input. | `'The publication text to extract the synthesis paragraph from.'` |
| `output_name` | `str` | Name of the output field. | `'synthesis_paragraph'` |
| `output_description` | `str` | Description for the output field. | `'The extracted synthesis paragraph.'` |

**Returns:**

| Type | Description |
| --- | --- |
| `type[Signature]` | dspy.Signature: The constructed dspy signature for text extraction. |

## Synthesis extraction

### `DspySynthesisExtractor`

```python
class DspySynthesisExtractor(signature, lm, retry_temperatures=None)
```

Bases: `SynthesisExtractorInterface`

Extractor that uses dspy to extract a structured synthesis ontology for a specific material from the entire paper text.

Implements temperature escalation retry and bare-JSON recovery on failures to improve robustness without changing the happy-path behavior.

Initialize the extractor with a dspy signature and language model.

**Parameters:**

| Name | Type | Description | Default |
| --- | --- | --- | --- |
| `signature` | `Signature` | The dspy signature specifying input/output fields. | *required* |
| `lm` | `LM` | The language model to use for prediction. | *required* |
| `retry_temperatures` | `list[float] \| None` | Temperatures to try on failures. Defaults to [0.0, 0.3, 0.5]. | `None` |

#### Methods

##### `forward(input)`

```python
def forward(input)
```

Extract a structured synthesis ontology for a specific material from the given paper text.

Retries at escalating temperatures on failure. Attempts bare-JSON recovery before escalating. Falls back to a minimal ontology if all attempts are exhausted.

**Parameters:**

| Name | Type | Description | Default |
| --- | --- | --- | --- |
| `input` | `tuple[str, str]` | Tuple of (paper_text, material_name). | *required* |

**Returns:**

| Name | Type | Description |
| --- | --- | --- |
| `GeneralSynthesisOntology` | `GeneralSynthesisOntology` | The structured synthesis ontology for the specific material. |

### `make_dspy_synthesis_extractor_signature`

```python
def make_dspy_synthesis_extractor_signature(signature_name='DspySynthesisExtractorSignature', instructions='Extract structured synthesis for a specific material from the paper. Output only a valid JSON with the structured_synthesis field.', paper_text_description='Complete paper text to search for the material synthesis procedure.', material_name_description='The name of the specific material to extract synthesis for.', output_name='structured_synthesis', output_description='The extracted structured synthesis for specific material as a JSON.')
```

Create signature for extracting a materials-specific synthesis ontology.

**Parameters:**

| Name | Type | Description | Default |
| --- | --- | --- | --- |
| `signature_name` | `str` | Name of the signature. | `'DspySynthesisExtractorSignature'` |
| `instructions` | `str` | Instructions for the signature. | `'Extract structured synthesis for a specific material from the paper. Output only a valid JSON with the structured_synthesis field.'` |
| `paper_text_description` | `str` | Description for the paper text input. | `'Complete paper text to search for the material synthesis procedure.'` |
| `material_name_description` | `str` | Description for material name input. | `'The name of the specific material to extract synthesis for.'` |
| `output_name` | `str` | Name of the output field. | `'structured_synthesis'` |
| `output_description` | `str` | Description for the output field. | `'The extracted structured synthesis for specific material as a JSON.'` |

**Returns:**

| Type | Description |
| --- | --- |
| `type[Signature]` | dspy.Signature: The dspy signature for synthesis extraction. |

## PDF extraction

### `DoclingPDFExtractor`

```python
class DoclingPDFExtractor(pipeline='standard', table_mode='accurate', add_page_images=False, use_gpu=True, scale=2.0, format='markdown')
```

Bases: `PdfExtractorInterface`

An extractor for extracting content from PDF files using the Docling library.

This class provides functionality to convert PDF files into various formats such as Markdown, doctags, JSON, or tokens. It supports different modes for handling images within the PDF, including embedding, referencing, or using placeholders. The extractor can be configured with various options such as pipeline type, table extraction mode, GPU usage, and scaling.

**Attributes:**

| Name | Type | Description |
| --- | --- | --- |
| `pipeline` | `str` | The pipeline to use for PDF processing (default: "standard"). |
| `table_mode` | `str` | The mode for table extraction (default: "accurate"). |
| `add_page_images` | `bool` | Whether to include page images in the output (default: False). |
| `use_gpu` | `bool` | Whether to use GPU for processing (default: True). |
| `scale` | `float` | The scaling factor for images (default: 2.0). |
| `format` | `str` | The output format. Options are "markdown", "doctags", "json", or "tokens" (default: "markdown"). |

**Methods:**

```
extract_to_markdown(pdf_data: bytes) -> str:
    Converts a PDF file to Markdown format. Supports different image
    modes and raises a ValueError if an invalid image mode is provided.
```

#### Methods

##### `forward(input)`

```python
def forward(input)
```

Extracts text and figures from a PDF and returns them as markdown with embedded figures.

**Parameters:**

| Name | Type | Description | Default |
| --- | --- | --- | --- |
| `pdf_data` |  | The PDF data as bytes. | *required* |

**Returns:**

| Type | Description |
| --- | --- |
| `str` | The extracted text as markdown with embedded figures. |

### `MistralPDFExtractor`

```python
class MistralPDFExtractor(structured=False, mistral_api_key=None, max_retries=3, retry_base_delay=2.0)
```

Bases: `PdfExtractorInterface`

A PDF extractor that uses the Mistral OCR API to extract content from PDF files and optionally convert it to Markdown format. This extractor supports embedding images as data URIs and can return structured JSON output if required.

**Attributes:**

| Name | Type | Description |
| --- | --- | --- |
| `structured` | `bool` | Determines whether the output should be structured JSON. |
| `embed_images` | `bool` | Indicates whether images should be embedded as data URIs in the Markdown output. |
| `mistral_api_key` | `str` | The API key for authenticating with the Mistral OCR API. If not provided, it will be fetched from the environment variable `MISTRAL_API_KEY`. |
| `mistral_api_client` | `Mistral` | The client instance for interacting with the Mistral OCR API. |

**Methods:**

| Name | Description |
| --- | --- |
| `extract_to_markdown(pdf_data: bytes) -> str` | Extracts content from a PDF file and converts it to Markdown format. Optionally embeds images as data URIs and supports structured JSON output. |

#### Methods

##### `forward(input)`

```python
def forward(input)
```

Extracts text and figures from a PDF and returns them as markdown with embedded figures.

**Parameters:**

| Name | Type | Description | Default |
| --- | --- | --- | --- |
| `pdf_data` |  | The PDF data as bytes. | *required* |

**Returns:**

| Type | Description |
| --- | --- |
| `str` | The extracted text as markdown with embedded figures. |

## Plot data extraction

### `ClaudeLinePlotDataExtractor`

```python
class ClaudeLinePlotDataExtractor(model_name, prompt=resources.LINE_CHART_PROMPT_WITH_CONTEXT, max_tokens=1024, temperature=0.0, use_figure_context=True)
```

Bases: `LinePlotDataExtractorInterface`

#### Methods

##### `get_cost()`

```python
def get_cost()
```

Get cumulative cost from Claude client.

##### `reset_cost()`

```python
def reset_cost()
```

Reset costs in Claude client.

### `LiteLLMPlotDataExtractor`

```python
class LiteLLMPlotDataExtractor(model, prompt=resources.LINE_CHART_PROMPT_WITH_CONTEXT, max_tokens=8192, temperature=0.0, api_key=None, api_base=None, extra_kwargs=None, retry_temperatures=None)
```

Bases: `LinePlotDataExtractorInterface`

Plot data extractor using litellm — works with any vision model.

Uses the same prompt and parsing logic as `ClaudeLinePlotDataExtractor`, but routes API calls through litellm for multi-provider support.

## Performance linking

### `SeriesMaterialLinker`

```python
class SeriesMaterialLinker(lm, prompt_template=DEFAULT_MATCHING_PROMPT)
```

Bases: `PerformanceLinkingInterface`

LLM-based transformer for matching plot series names to material names.

This transformer uses an LLM to semantically match series names from plots (e.g., "575", "Ni/Al2O3", "Sample A") to the actual material names extracted from the paper (e.g., "Mo2(C,N)Tx-575", "10%Ni/Al2O3").

**Attributes:**

| Name | Type | Description |
| --- | --- | --- |
| `lm` |  | DSPy language model for making predictions |
| `prompt_template` |  | Template for the matching prompt |

Initialize the linker.

**Parameters:**

| Name | Type | Description | Default |
| --- | --- | --- | --- |
| `lm` | `LM` | DSPy language model instance | *required* |
| `prompt_template` | `str` | Prompt template with placeholders for materials, series_names, context, plot_title, x_axis_label, x_axis_unit, y_axis_label, y_axis_unit | `DEFAULT_MATCHING_PROMPT` |

#### Methods

##### `forward(input)`

```python
def forward(input)
```

Match plot series names to material names using LLM.

**Parameters:**

| Name | Type | Description | Default |
| --- | --- | --- | --- |
| `input` | `LinkingInput` | LinkingInput containing materials, series names, context, and plot metadata | *required* |

**Returns:**

| Type | Description |
| --- | --- |
| `list[SeriesMapping]` | List of validated SeriesMapping objects |

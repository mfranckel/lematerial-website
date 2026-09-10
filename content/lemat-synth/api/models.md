---
title: "Data Models"
description: "Reference for the Pydantic data models used across LeMat-Synth: the synthesis ontology, paper models, and performance/plot models."
summary: ""
date: 2025-10-28T00:00:00+00:00
lastmod: 2025-10-28T00:00:00+00:00
draft: false
weight: 572
toc: true
seo:
  title: "Data Models"
  description: "Reference for the Pydantic data models used across LeMat-Synth: the synthesis ontology, paper models, and performance/plot models."
  canonical: ""
  noindex: false
---

All output data is represented as [Pydantic](https://docs.pydantic.dev) models. You can serialise any model to JSON with `.model_dump()` and parse from a dict with `.model_validate(data)`.

## Core synthesis ontology

### `GeneralSynthesisOntology`

```python
class GeneralSynthesisOntology(BaseModel)
```

Bases: `BaseModel`

Comprehensive synthesis ontology for structured synthesis procedures.

### `ProcessStep`

```python
class ProcessStep(BaseModel)
```

Bases: `BaseModel`

### `Material`

```python
class Material(BaseModel)
```

Bases: `BaseModel`

### `Equipment`

```python
class Equipment(BaseModel)
```

Bases: `BaseModel`

### `Conditions`

```python
class Conditions(BaseModel)
```

Bases: `BaseModel`

## Paper models

### `Paper`

```python
class Paper(BaseModel)
```

Bases: `BaseModel`

### `PaperWithSynthesisOntologies`

```python
class PaperWithSynthesisOntologies(Paper)
```

Bases: `Paper`

### `SynthesisEntry`

```python
class SynthesisEntry(BaseModel)
```

Bases: `BaseModel`

## Performance / plot models

### `MaterialPerformanceData`

```python
class MaterialPerformanceData(BaseModel)
```

Bases: `BaseModel`

All performance data for a single material, aggregated across plots.

### `MaterialPlotEntry`

```python
class MaterialPlotEntry(BaseModel)
```

Bases: `BaseModel`

One plot series linked to a material, with its coordinate data.

### `PlotMaterialMapping`

```python
class PlotMaterialMapping(BaseModel)
```

Bases: `BaseModel`

All series-to-material mappings for a single plot.

### `SeriesMapping`

```python
class SeriesMapping(BaseModel)
```

Bases: `BaseModel`

A single mapping from a plot series name to a material name.

### `LinkingStats`

```python
class LinkingStats(BaseModel)
```

Bases: `BaseModel`

Statistics about plot linking for summary output.

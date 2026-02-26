# Dataset | Atom Classification

A "Dataset" maps to [Dataset](https://schema.org/Dataset). Keep this atom minimal with dataset name and canonical URL.

## Data Structure

### Atom Data

```json
{
  "@context": "https://schema.org/",
  "@type": "Dataset",
  "name": "<dataset_name>",
  "url": "<canonical_dataset_url_if_needed>",
  "sameAs": ["<canonical_reference_url_if_needed>"]
}
```

### Atom Classification

```json
{
  "type": "Dataset",
  "data": {
    "@context": "https://schema.org/",
    "@type": "Dataset",
    "name": "<dataset_name>",
    "url": "<canonical_dataset_url_if_needed>",
    "sameAs": ["<canonical_reference_url_if_needed>"]
  },
  "meta": {
    "pluginId": "dataset",
    "provider": "opengraph",
    "fetchedAt": "<iso_datetime>",
    "sourceUrl": "<source_url_if_available>"
  }
}
```

### Example

```json
{
  "type": "Dataset",
  "data": {
    "@context": "https://schema.org/",
    "@type": "Dataset",
    "name": "Global Surface Temperature",
    "url": "https://example.org/datasets/global-surface-temperature",
    "sameAs": ["https://doi.org/10.1234/example-dataset"]
  },
  "meta": {
    "pluginId": "dataset",
    "provider": "opengraph",
    "fetchedAt": "2026-02-26T12:00:00Z",
    "sourceUrl": "https://example.org/datasets/global-surface-temperature"
  }
}
```

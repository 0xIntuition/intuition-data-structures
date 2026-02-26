# TVSeries | Atom Classification

A "TVSeries" maps to [TVSeries](https://schema.org/TVSeries). Keep this atom minimal: `name` is required, and optional fields are only for disambiguation.

## Data Structure

### Atom Data

```json
{
  "@context": "https://schema.org/",
  "@type": "TVSeries",
  "name": "<series_title>",
  "startDate": "<start_date_if_needed>",
  "endDate": "<end_date_if_needed>",
  "sameAs": ["<canonical_url_if_needed>"]
}
```

### Atom Classification

```json
{
  "type": "TVSeries",
  "data": {
    "@context": "https://schema.org/",
    "@type": "TVSeries",
    "name": "<series_title>",
    "startDate": "<start_date_if_needed>",
    "endDate": "<end_date_if_needed>",
    "sameAs": ["<canonical_url_if_needed>"]
  },
  "meta": {
    "pluginId": "tv-series",
    "provider": "<provider_name>",
    "fetchedAt": "<iso_datetime>",
    "sourceUrl": "<source_url_if_available>"
  }
}
```

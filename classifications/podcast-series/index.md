# PodcastSeries | Atom Classification

A "PodcastSeries" maps to [PodcastSeries](https://schema.org/PodcastSeries). Keep this atom minimal with series name and canonical URL.

## Data Structure

### Atom Data

```json
{
  "@context": "https://schema.org/",
  "@type": "PodcastSeries",
  "name": "<podcast_series_name>",
  "url": "<canonical_series_url_if_needed>",
  "sameAs": ["<canonical_reference_url_if_needed>"]
}
```

### Atom Classification

```json
{
  "type": "PodcastSeries",
  "data": {
    "@context": "https://schema.org/",
    "@type": "PodcastSeries",
    "name": "<podcast_series_name>",
    "url": "<canonical_series_url_if_needed>",
    "sameAs": ["<canonical_reference_url_if_needed>"]
  },
  "meta": {
    "pluginId": "podcast-series",
    "provider": "opengraph",
    "fetchedAt": "<iso_datetime>",
    "sourceUrl": "<source_url_if_available>"
  }
}
```

### Example

```json
{
  "type": "PodcastSeries",
  "data": {
    "@context": "https://schema.org/",
    "@type": "PodcastSeries",
    "name": "Bankless",
    "url": "https://www.bankless.com/podcast",
    "sameAs": ["https://open.spotify.com/show/example"]
  },
  "meta": {
    "pluginId": "podcast-series",
    "provider": "opengraph",
    "fetchedAt": "2026-02-26T12:00:00Z",
    "sourceUrl": "https://www.bankless.com/podcast"
  }
}
```

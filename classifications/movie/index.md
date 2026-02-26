# Movie | Atom Classification

A "Movie" maps to [Movie](https://schema.org/Movie). Keep this atom minimal: `name` is required, and optional fields are only for disambiguation.

## Data Structure

### Atom Data

```json
{
  "@context": "https://schema.org/",
  "@type": "Movie",
  "name": "<movie_title>",
  "datePublished": "<release_date_if_needed>",
  "sameAs": ["<canonical_url_if_needed>"]
}
```

### Atom Classification

```json
{
  "type": "Movie",
  "data": {
    "@context": "https://schema.org/",
    "@type": "Movie",
    "name": "<movie_title>",
    "datePublished": "<release_date_if_needed>",
    "sameAs": ["<canonical_url_if_needed>"]
  },
  "meta": {
    "pluginId": "movie",
    "provider": "<provider_name>",
    "fetchedAt": "<iso_datetime>",
    "sourceUrl": "<source_url_if_available>"
  }
}
```

# AggregateRating | Atom Classification

An "AggregateRating" maps to [AggregateRating](https://schema.org/AggregateRating). Keep this atom minimal with stable rating aggregates.

## Data Structure

### Atom Data

```json
{
  "@context": "https://schema.org/",
  "@type": "AggregateRating",
  "ratingValue": "<average_rating>",
  "reviewCount": "<review_count>",
  "bestRating": "<best_rating_if_needed>",
  "worstRating": "<worst_rating_if_needed>"
}
```

### Atom Classification

```json
{
  "type": "AggregateRating",
  "data": {
    "@context": "https://schema.org/",
    "@type": "AggregateRating",
    "ratingValue": "<average_rating>",
    "reviewCount": "<review_count>",
    "bestRating": "<best_rating_if_needed>",
    "worstRating": "<worst_rating_if_needed>"
  },
  "meta": {
    "pluginId": "aggregate-rating",
    "provider": "opengraph",
    "fetchedAt": "<iso_datetime>",
    "sourceUrl": "<source_url_if_available>"
  }
}
```

### Example

```json
{
  "type": "AggregateRating",
  "data": {
    "@context": "https://schema.org/",
    "@type": "AggregateRating",
    "ratingValue": "4.7",
    "reviewCount": "1320",
    "bestRating": "5",
    "worstRating": "1"
  },
  "meta": {
    "pluginId": "aggregate-rating",
    "provider": "opengraph",
    "fetchedAt": "2026-02-26T12:00:00Z",
    "sourceUrl": "https://example.com/products/wallet"
  }
}
```

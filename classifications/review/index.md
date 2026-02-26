# Review | Atom Classification

A "Review" maps to [Review](https://schema.org/Review). Keep this atom minimal with review text and a stable target reference.

## Data Structure

### Atom Data

```json
{
  "@context": "https://schema.org/",
  "@type": "Review",
  "name": "<review_title_if_needed>",
  "reviewBody": "<review_text>",
  "itemReviewed": "<review_target_identifier_if_needed>",
  "sameAs": ["<canonical_url_if_needed>"]
}
```

### Atom Classification

```json
{
  "type": "Review",
  "data": {
    "@context": "https://schema.org/",
    "@type": "Review",
    "name": "<review_title_if_needed>",
    "reviewBody": "<review_text>",
    "itemReviewed": "<review_target_identifier_if_needed>",
    "sameAs": ["<canonical_url_if_needed>"]
  },
  "meta": {
    "pluginId": "review",
    "provider": "opengraph",
    "fetchedAt": "<iso_datetime>",
    "sourceUrl": "<source_url_if_available>"
  }
}
```

### Example

```json
{
  "type": "Review",
  "data": {
    "@context": "https://schema.org/",
    "@type": "Review",
    "name": "Great wallet UX",
    "reviewBody": "Very smooth onboarding and transaction flow.",
    "itemReviewed": "https://example.com/products/wallet",
    "sameAs": ["https://example.com/reviews/wallet-123"]
  },
  "meta": {
    "pluginId": "review",
    "provider": "opengraph",
    "fetchedAt": "2026-02-26T12:00:00Z",
    "sourceUrl": "https://example.com/reviews/wallet-123"
  }
}
```

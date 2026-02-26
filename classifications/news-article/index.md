# NewsArticle | Atom Classification

A "NewsArticle" maps to [NewsArticle](https://schema.org/NewsArticle). Keep this atom minimal with durable publication identity fields.

## Data Structure

### Atom Data

```json
{
  "@context": "https://schema.org/",
  "@type": "NewsArticle",
  "headline": "<headline>",
  "datePublished": "<date_published_if_needed>",
  "url": "<canonical_article_url_if_needed>",
  "sameAs": ["<canonical_reference_url_if_needed>"]
}
```

### Atom Classification

```json
{
  "type": "NewsArticle",
  "data": {
    "@context": "https://schema.org/",
    "@type": "NewsArticle",
    "headline": "<headline>",
    "datePublished": "<date_published_if_needed>",
    "url": "<canonical_article_url_if_needed>",
    "sameAs": ["<canonical_reference_url_if_needed>"]
  },
  "meta": {
    "pluginId": "news-article",
    "provider": "opengraph",
    "fetchedAt": "<iso_datetime>",
    "sourceUrl": "<source_url_if_available>"
  }
}
```

### Example

```json
{
  "type": "NewsArticle",
  "data": {
    "@context": "https://schema.org/",
    "@type": "NewsArticle",
    "headline": "Intuition Launches v1",
    "datePublished": "2026-02-26",
    "url": "https://example.com/news/intuition-launches-v1",
    "sameAs": ["https://example.com/news/intuition-launches-v1"]
  },
  "meta": {
    "pluginId": "news-article",
    "provider": "opengraph",
    "fetchedAt": "2026-02-26T12:00:00Z",
    "sourceUrl": "https://example.com/news/intuition-launches-v1"
  }
}
```

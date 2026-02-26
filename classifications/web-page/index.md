# WebPage | Atom Classification

A "WebPage" maps to [WebPage](https://schema.org/WebPage). Keep this atom minimal with page `name` and canonical `url`.

## Data Structure

### Atom Data

```json
{
  "@context": "https://schema.org/",
  "@type": "WebPage",
  "name": "<page_title>",
  "url": "<canonical_page_url>",
  "isPartOf": "<website_name_or_url_if_needed>"
}
```

### Atom Classification

```json
{
  "type": "WebPage",
  "data": {
    "@context": "https://schema.org/",
    "@type": "WebPage",
    "name": "<page_title>",
    "url": "<canonical_page_url>",
    "isPartOf": "<website_name_or_url_if_needed>"
  },
  "meta": {
    "pluginId": "web-page",
    "provider": "opengraph",
    "fetchedAt": "<iso_datetime>",
    "sourceUrl": "<source_url_if_available>"
  }
}
```

### Example

```json
{
  "type": "WebPage",
  "data": {
    "@context": "https://schema.org/",
    "@type": "WebPage",
    "name": "Brad Pitt",
    "url": "https://en.wikipedia.org/wiki/Brad_Pitt",
    "isPartOf": "https://en.wikipedia.org"
  },
  "meta": {
    "pluginId": "web-page",
    "provider": "opengraph",
    "fetchedAt": "2026-02-26T12:00:00Z",
    "sourceUrl": "https://en.wikipedia.org/wiki/Brad_Pitt"
  }
}
```

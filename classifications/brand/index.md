# Brand | Atom Classification

A "Brand" maps to [Brand](https://schema.org/Brand). Keep this atom minimal with `name` and optional canonical references.

## Data Structure

### Atom Data

```json
{
  "@context": "https://schema.org/",
  "@type": "Brand",
  "name": "<brand_name>",
  "url": "<official_website_if_needed>",
  "sameAs": ["<canonical_url_if_needed>"]
}
```

### Atom Classification

```json
{
  "type": "Brand",
  "data": {
    "@context": "https://schema.org/",
    "@type": "Brand",
    "name": "<brand_name>",
    "url": "<official_website_if_needed>",
    "sameAs": ["<canonical_url_if_needed>"]
  },
  "meta": {
    "pluginId": "brand",
    "provider": "company-profile",
    "fetchedAt": "<iso_datetime>",
    "sourceUrl": "<source_url_if_available>"
  }
}
```

### Example

```json
{
  "type": "Brand",
  "data": {
    "@context": "https://schema.org/",
    "@type": "Brand",
    "name": "Patagonia",
    "url": "https://www.patagonia.com",
    "sameAs": ["https://www.wikidata.org/wiki/Q223269"]
  },
  "meta": {
    "pluginId": "brand",
    "provider": "company-profile",
    "fetchedAt": "2026-02-26T12:00:00Z",
    "sourceUrl": "https://www.patagonia.com"
  }
}
```

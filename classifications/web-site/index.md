# WebSite | Atom Classification

A "WebSite" maps to [WebSite](https://schema.org/WebSite). Keep this atom minimal: use site `name` and canonical `url` for durable identity.

## Data Structure

### Atom Data

```json
{
  "@context": "https://schema.org/",
  "@type": "WebSite",
  "name": "<site_name>",
  "url": "<canonical_site_url>"
}
```

### Atom Classification

```json
{
  "type": "WebSite",
  "data": {
    "@context": "https://schema.org/",
    "@type": "WebSite",
    "name": "<site_name>",
    "url": "<canonical_site_url>"
  },
  "meta": {
    "pluginId": "web-site",
    "provider": "opengraph",
    "fetchedAt": "<iso_datetime>",
    "sourceUrl": "<source_url_if_available>"
  }
}
```

### Example

```json
{
  "type": "WebSite",
  "data": {
    "@context": "https://schema.org/",
    "@type": "WebSite",
    "name": "Wikipedia",
    "url": "https://www.wikipedia.org"
  },
  "meta": {
    "pluginId": "web-site",
    "provider": "opengraph",
    "fetchedAt": "2026-02-26T12:00:00Z",
    "sourceUrl": "https://www.wikipedia.org"
  }
}
```

# LocalBusiness | Atom Classification

A "LocalBusiness" maps to [LocalBusiness](https://schema.org/LocalBusiness). Keep this atom minimal with business name and location disambiguation.

## Data Structure

### Atom Data

```json
{
  "@context": "https://schema.org/",
  "@type": "LocalBusiness",
  "name": "<business_name>",
  "address": "<address_if_needed>",
  "telephone": "<telephone_if_needed>",
  "url": "<official_website_if_needed>",
  "sameAs": ["<canonical_url_if_needed>"]
}
```

### Atom Classification

```json
{
  "type": "LocalBusiness",
  "data": {
    "@context": "https://schema.org/",
    "@type": "LocalBusiness",
    "name": "<business_name>",
    "address": "<address_if_needed>",
    "telephone": "<telephone_if_needed>",
    "url": "<official_website_if_needed>",
    "sameAs": ["<canonical_url_if_needed>"]
  },
  "meta": {
    "pluginId": "local-business",
    "provider": "places",
    "fetchedAt": "<iso_datetime>",
    "sourceUrl": "<source_url_if_available>"
  }
}
```

### Example

```json
{
  "type": "LocalBusiness",
  "data": {
    "@context": "https://schema.org/",
    "@type": "LocalBusiness",
    "name": "Blue Bottle Coffee",
    "address": "66 Mint St, San Francisco, CA",
    "telephone": "+14152222222",
    "url": "https://bluebottlecoffee.com",
    "sameAs": ["https://maps.google.com/?cid=example"]
  },
  "meta": {
    "pluginId": "local-business",
    "provider": "places",
    "fetchedAt": "2026-02-26T12:00:00Z",
    "sourceUrl": "https://maps.google.com/?cid=example"
  }
}
```

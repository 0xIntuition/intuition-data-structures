# Service | Atom Classification

A "Service" maps to [Service](https://schema.org/Service). Keep this atom minimal: `name` is required, and optional fields are only for disambiguation.

## Data Structure

### Atom Data

```json
{
  "@context": "https://schema.org/",
  "@type": "Service",
  "name": "<service_name>",
  "provider": "<provider_name_if_needed>",
  "areaServed": "<area_served_if_needed>",
  "sameAs": ["<canonical_url_if_needed>"]
}
```

### Atom Classification

```json
{
  "type": "Service",
  "data": {
    "@context": "https://schema.org/",
    "@type": "Service",
    "name": "<service_name>",
    "provider": "<provider_name_if_needed>",
    "areaServed": "<area_served_if_needed>",
    "sameAs": ["<canonical_url_if_needed>"]
  },
  "meta": {
    "pluginId": "service",
    "provider": "<provider_name>",
    "fetchedAt": "<iso_datetime>",
    "sourceUrl": "<source_url_if_available>"
  }
}
```

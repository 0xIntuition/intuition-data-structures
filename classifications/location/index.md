# Location | Atom Classification

A "Location" maps to [Place](https://schema.org/Place). Keep this atom minimal: `name` is required, and optional fields are only for disambiguation.

## Data Structure

### Atom Data

```json
{
  "@context": "https://schema.org/",
  "@type": "Place",
  "name": "<location_name>",
  "address": "<address_if_needed>",
  "sameAs": ["<canonical_url_if_needed>"]
}
```

### Atom Classification

```json
{
  "type": "Place",
  "data": {
    "@context": "https://schema.org/",
    "@type": "Place",
    "name": "<location_name>",
    "address": "<address_if_needed>",
    "sameAs": ["<canonical_url_if_needed>"]
  },
  "meta": {
    "pluginId": "location",
    "provider": "<provider_name>",
    "fetchedAt": "<iso_datetime>",
    "sourceUrl": "<source_url_if_available>"
  }
}
```

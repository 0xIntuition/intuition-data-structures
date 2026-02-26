# Company | Atom Classification

A "Company" maps to [Organization](https://schema.org/Organization). Keep this atom minimal: `name` is required, and optional fields are only for disambiguation.

## Data Structure

### Atom Data

```json
{
  "@context": "https://schema.org/",
  "@type": "Organization",
  "name": "<company_name>",
  "url": "<official_website_if_needed>",
  "sameAs": ["<canonical_url_if_needed>"]
}
```

### Atom Classification

```json
{
  "type": "Organization",
  "data": {
    "@context": "https://schema.org/",
    "@type": "Organization",
    "name": "<company_name>",
    "url": "<official_website_if_needed>",
    "sameAs": ["<canonical_url_if_needed>"]
  },
  "meta": {
    "pluginId": "company",
    "provider": "<provider_name>",
    "fetchedAt": "<iso_datetime>",
    "sourceUrl": "<source_url_if_available>"
  }
}
```

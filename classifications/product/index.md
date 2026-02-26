# Product | Atom Classification

A "Product" maps to [Product](https://schema.org/Product). Keep this atom minimal: `name` is required, and optional fields are only for disambiguation.

## Data Structure

### Atom Data

```json
{
  "@context": "https://schema.org/",
  "@type": "Product",
  "name": "<product_name>",
  "brand": "<brand_name_if_needed>",
  "sku": "<sku_if_needed>",
  "gtin": "<gtin_if_needed>",
  "sameAs": ["<canonical_url_if_needed>"]
}
```

### Atom Classification

```json
{
  "type": "Product",
  "data": {
    "@context": "https://schema.org/",
    "@type": "Product",
    "name": "<product_name>",
    "brand": "<brand_name_if_needed>",
    "sku": "<sku_if_needed>",
    "gtin": "<gtin_if_needed>",
    "sameAs": ["<canonical_url_if_needed>"]
  },
  "meta": {
    "pluginId": "product",
    "provider": "<provider_name>",
    "fetchedAt": "<iso_datetime>",
    "sourceUrl": "<source_url_if_available>"
  }
}
```

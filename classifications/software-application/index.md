# SoftwareApplication | Atom Classification

A "SoftwareApplication" maps to [SoftwareApplication](https://schema.org/SoftwareApplication). Keep this atom minimal with `name` and a canonical app reference.

## Data Structure

### Atom Data

```json
{
  "@context": "https://schema.org/",
  "@type": "SoftwareApplication",
  "name": "<application_name>",
  "applicationCategory": "<category_if_needed>",
  "operatingSystem": "<operating_system_if_needed>",
  "url": "<canonical_app_url_if_needed>"
}
```

### Atom Classification

```json
{
  "type": "SoftwareApplication",
  "data": {
    "@context": "https://schema.org/",
    "@type": "SoftwareApplication",
    "name": "<application_name>",
    "applicationCategory": "<category_if_needed>",
    "operatingSystem": "<operating_system_if_needed>",
    "url": "<canonical_app_url_if_needed>"
  },
  "meta": {
    "pluginId": "software-application",
    "provider": "opengraph",
    "fetchedAt": "<iso_datetime>",
    "sourceUrl": "<source_url_if_available>"
  }
}
```

### Example

```json
{
  "type": "SoftwareApplication",
  "data": {
    "@context": "https://schema.org/",
    "@type": "SoftwareApplication",
    "name": "Notion",
    "applicationCategory": "Productivity",
    "operatingSystem": "Web",
    "url": "https://www.notion.so"
  },
  "meta": {
    "pluginId": "software-application",
    "provider": "opengraph",
    "fetchedAt": "2026-02-26T12:00:00Z",
    "sourceUrl": "https://www.notion.so"
  }
}
```

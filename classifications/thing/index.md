# Thing | Atom Classification

A "Thing" is a broad category for any physical object or tangible item that exists in the real world. It encompasses a vast range of objects that can be perceived by the senses, touched, or interacted with physically.

## Data Structure

```json
{
  "@context": "https://schema.org/",
  "@type": "Thing",
  "name": "<thing_name>",
  "description": "<thing_description>"
}
```

### Atom Classification

```json
{
  "type": "Thing",
  "data": {
    "@context": "https://schema.org/",
    "@type": "Thing",
    "name": "<thing_name>",
    "description": "<thing_description>"
  },
  "meta": {
    "pluginId": "thing",
    "provider": "wikidata",
    "fetchedAt": "<iso_datetime>",
    "sourceUrl": "<wikidata_url_if_available>"
  }
}
```

### Example

```json
{
  "type": "Thing",
  "data": {
    "@context": "https://schema.org/",
    "@type": "Thing",
    "name": "Apple",
    "description": "A round fruit with red or green skin and crisp white flesh."
  },
  "meta": {
    "pluginId": "thing",
    "provider": "wikidata",
    "fetchedAt": "2026-02-25T10:00:15-08:00",
    "sourceUrl": "https://www.wikidata.org/wiki/Q89"
  }
}
```

# Person | Atom Classification

A "Person" is an individual human identity. Keep person atoms minimal and store only the name plus one canonical reference when disambiguation is needed.

## Data Structure

### Atom Data

```json
{
  "@context": "https://schema.org/",
  "@type": "Person",
  "givenName": "<person_first_name>",
  "familyName": "<person_last_name>"
}
```

### Atom Classification

```json
{
  "type": "Person",
  "data": {
    "@context": "https://schema.org/",
    "@type": "Person",
    "givenName": "<person_first_name>",
    "familyName": "<person_last_name>",
  },
  "meta": {
    "pluginId": "person",
    "provider": "wikidata",
    "fetchedAt": "<iso_datetime>",
    "sourceUrl": "<wikidata_url_if_available>"
  }
}
```

### Example

```json
{
  "type": "Person",
  "data": {
    "@context": "https://schema.org/",
    "@type": "Person",
    "givenName": "Vitalik",
    "familyName": "Buterin"
  },
  "meta": {
    "pluginId": "person",
    "provider": "wikidata",
    "fetchedAt": "2026-02-25T10:00:15-08:00",
    "sourceUrl": "https://www.wikidata.org/wiki/Q20011958"
  }
}
```


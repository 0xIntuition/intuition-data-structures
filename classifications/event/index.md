# Event | Atom Classification

An "Event" maps to [Event](https://schema.org/Event). Keep this atom minimal: `name` is required, and optional fields are only for disambiguation.

## Data Structure

### Atom Data

```json
{
  "@context": "https://schema.org/",
  "@type": "Event",
  "name": "<event_name>",
  "startDate": "<start_date_if_needed>",
  "location": "<location_name_if_needed>",
  "sameAs": ["<canonical_url_if_needed>"]
}
```

### Atom Classification

```json
{
  "type": "Event",
  "data": {
    "@context": "https://schema.org/",
    "@type": "Event",
    "name": "<event_name>",
    "startDate": "<start_date_if_needed>",
    "location": "<location_name_if_needed>",
    "sameAs": ["<canonical_url_if_needed>"]
  },
  "meta": {
    "pluginId": "event",
    "provider": "<provider_name>",
    "fetchedAt": "<iso_datetime>",
    "sourceUrl": "<source_url_if_available>"
  }
}
```

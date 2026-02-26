# MusicGroup | Atom Classification

A "MusicGroup" is an artist or band identity. Keep this atom minimal: `name` is required, and optional fields are only for disambiguation.

## Data Structure

### Atom Data

```json
{
  "@context": "https://schema.org/",
  "@type": "MusicGroup",
  "name": "<artist_or_group_name>"
}
```

### Atom Classification

```json
{
  "type": "MusicGroup",
  "data": {
    "@context": "https://schema.org/",
    "@type": "MusicGroup",
    "name": "<artist_or_group_name>"
  },
  "meta": {
    "pluginId": "song",
    "provider": "musicbrainz",
    "fetchedAt": "<iso_datetime>",
    "sourceUrl": "<musicbrainz_url_if_available>"
  }
}
```

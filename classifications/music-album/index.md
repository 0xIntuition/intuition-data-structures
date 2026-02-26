# MusicAlbum | Atom Classification

A "MusicAlbum" is a collection release of songs. Keep this atom minimal: `name` is required, and optional fields are only for disambiguation.

## Data Structure

### Atom Data

```json
{
  "@context": "https://schema.org/",
  "@type": "MusicAlbum",
  "name": "<album_name>",
  "byArtist": "<artist_name_if_needed>"
}
```

### Atom Classification

```json
{
  "type": "MusicAlbum",
  "data": {
    "@context": "https://schema.org/",
    "@type": "MusicAlbum",
    "name": "<album_name>",
    "byArtist": "<artist_name_if_needed>"
  },
  "meta": {
    "pluginId": "song",
    "provider": "musicbrainz",
    "fetchedAt": "<iso_datetime>",
    "sourceUrl": "<musicbrainz_url_if_available>"
  }
}
```

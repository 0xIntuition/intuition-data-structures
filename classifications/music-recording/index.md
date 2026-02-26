# MusicRecording | Atom Classification

A "MusicRecording" is an individual track. Keep this atom minimal: `name` is required, and optional fields are only for disambiguation.

## Data Structure

### Atom Data

```json
{
  "@context": "https://schema.org/",
  "@type": "MusicRecording",
  "name": "<song_name>",
  "byArtist": "<artist_name_if_needed>",
  "inAlbum": "<album_name_if_needed>"
}
```

### Atom Classification

```json
{
  "type": "MusicRecording",
  "data": {
    "@context": "https://schema.org/",
    "@type": "MusicRecording",
    "name": "<song_name>",
    "byArtist": "<artist_name_if_needed>",
    "inAlbum": "<album_name_if_needed>"
  },
  "meta": {
    "pluginId": "song",
    "provider": "musicbrainz",
    "fetchedAt": "<iso_datetime>",
    "sourceUrl": "<musicbrainz_url_if_available>"
  }
}
```

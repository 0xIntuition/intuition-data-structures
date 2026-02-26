# VideoObject | Atom Classification

A "VideoObject" maps to [VideoObject](https://schema.org/VideoObject). Keep this atom minimal: `name` is required, and optional fields are only for disambiguation.

## Data Structure

### Atom Data

```json
{
  "@context": "https://schema.org/",
  "@type": "VideoObject",
  "name": "<video_title>",
  "description": "<video_description_if_needed>",
  "contentUrl": "<video_content_url_if_needed>",
  "sameAs": ["<canonical_url_if_needed>"]
}
```

### Atom Classification

```json
{
  "type": "VideoObject",
  "data": {
    "@context": "https://schema.org/",
    "@type": "VideoObject",
    "name": "<video_title>",
    "description": "<video_description_if_needed>",
    "contentUrl": "<video_content_url_if_needed>",
    "sameAs": ["<canonical_url_if_needed>"]
  },
  "meta": {
    "pluginId": "video-object",
    "provider": "<provider_name>",
    "fetchedAt": "<iso_datetime>",
    "sourceUrl": "<source_url_if_available>"
  }
}
```

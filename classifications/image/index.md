# ImageObject | Atom Classification

"ImageObject" is an image. Keep image atoms minimal with just a name and a canonical source URL.

## Data Structure

### Atom Data
 
```json
{
  "@context": "https://schema.org/",
  "@type": "ImageObject",
  "name": "<image_name>",
  "url": "<image_url>",
  "caption": "<image_caption>",
  "keywords": "<image_keywords>"
}
```

### Atom Classification

```json
{
  "type": "ImageObject",
  "data": {
    "@context": "https://schema.org/",
    "@type": "ImageObject",
    "name": "<image_name>",
    "url": "<image_url>",
    "caption": "<image_caption>",
    "keywords": "<image_keywords>"
  },
  "meta": {
    "pluginId": "image",
    "provider": "github",
    "fetchedAt": "<iso_datetime>",
    "sourceUrl": "<image_url>"
  }
}
```

### Example

```json
{
  "type": "ImageObject",
  "data": {
    "@context": "https://schema.org/",
    "@type": "ImageObject", 
    "name": "knowledge-graph",
    "url": "https://github.com/0xintuition/intuition-v2/blob/main/assets/images/knowledge-graph.png",
    "caption": "The world is filled with knowledge.",
    "keywords": ["knowledge-graph"]
  },
  "meta": {
    "pluginId": "image",
    "provider": "github",
    "fetchedAt": "2026-02-25T10:00:15-08:00",
    "sourceUrl": "https://github.com/0xintuition/intuition-v2"
  }
}
```
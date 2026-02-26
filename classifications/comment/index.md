# Comment | Atom Classification

A "Comment" maps to [Comment](https://schema.org/Comment). Keep this atom minimal with comment text and a stable target reference.

## Data Structure

### Atom Data

```json
{
  "@context": "https://schema.org/",
  "@type": "Comment",
  "text": "<comment_text>",
  "about": "<target_identifier_if_needed>",
  "dateCreated": "<date_created_if_needed>",
  "sameAs": ["<canonical_url_if_needed>"]
}
```

### Atom Classification

```json
{
  "type": "Comment",
  "data": {
    "@context": "https://schema.org/",
    "@type": "Comment",
    "text": "<comment_text>",
    "about": "<target_identifier_if_needed>",
    "dateCreated": "<date_created_if_needed>",
    "sameAs": ["<canonical_url_if_needed>"]
  },
  "meta": {
    "pluginId": "comment",
    "provider": "social-media-posting",
    "fetchedAt": "<iso_datetime>",
    "sourceUrl": "<source_url_if_available>"
  }
}
```

### Example

```json
{
  "type": "Comment",
  "data": {
    "@context": "https://schema.org/",
    "@type": "Comment",
    "text": "This launch thread is incredibly useful.",
    "about": "https://example.com/posts/launch-thread",
    "dateCreated": "2026-02-26",
    "sameAs": ["https://example.com/posts/launch-thread#comment-42"]
  },
  "meta": {
    "pluginId": "comment",
    "provider": "social-media-posting",
    "fetchedAt": "2026-02-26T12:00:00Z",
    "sourceUrl": "https://example.com/posts/launch-thread#comment-42"
  }
}
```

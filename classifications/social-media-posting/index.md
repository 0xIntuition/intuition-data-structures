# SocialMediaPosting | Atom Classification

"SocialMediaPosting" is a defined term. Uses fields defined in the [SocialMediaPosting](https://schema.org/SocialMediaPosting) class.

## Data Structure

### Atom Data
 
```json
{
  "@context": "https://schema.org/",
  "@type": "SocialMediaPosting",
  "name": "<article_name>",
  "text": "<article_text>",
  "url": "<article_url>"
}
```

### Atom Classification

```json
{
  "type": "SocialMediaPosting",
  "data": {
    "@context": "https://schema.org/",
    "@type": "SocialMediaPosting",
    "name": "<article_name>",
    "text": "<article_text>",
    "url": "<article_url>"
  },
  "meta": {
    "pluginId": "article",
    "provider": "dictionary",
    "fetchedAt": "<iso_datetime>",
    "sourceUrl": "<article_url>"
  }
}
```

### Example

```json
{
  "type": "SocialMediaPosting",
  "data": {
    "@context": "https://schema.org/",
    "@type": "SocialMediaPosting", 
    "name": "Intuition Is Taking Over The World",
    "text": "Intuition, the onchain knowledge graph, is taking over the world.",
    "url": "https://www.nytimes.com/2026/02/24/technology/intuition-is-taking-over-the-world.html"
  },
  "meta": {
    "pluginId": "article",
    "provider": "dictionary",
    "fetchedAt": "2026-02-25T10:00:15-08:00",
    "sourceUrl": "https://www.nytimes.com/2026/02/24/technology/intuition-is-taking-over-the-world.html"
  }
}
```
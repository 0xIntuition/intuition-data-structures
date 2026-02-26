# Article | Atom Classification

"Article" is a defined term. Uses fields defined in the [Article](https://schema.org/Article) class.

## Data Structure

### Atom Data
 
```json
{
  "@context": "https://schema.org/",
  "@type": "Article",
  "headline": "<article_headline>",
  "description": "<article_description>",
  "url": "<article_url>"
}
```

### Atom Classification

```json
{
  "type": "Article",
  "data": {
    "@context": "https://schema.org/",
    "@type": "Article",
    "headline": "<article_headline>",
    "description": "<article_description>",
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
  "type": "Article",
  "data": {
    "@context": "https://schema.org/",
    "@type": "Article", 
    "headline": "Intuition Is Taking Over The World",
    "description": "Intuition, the onchain knowledge graph, is taking over the world.",
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
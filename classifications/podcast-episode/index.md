# PodcastEpisode | Atom Classification

A "PodcastEpisode" maps to [PodcastEpisode](https://schema.org/PodcastEpisode). Keep this atom minimal with episode name and canonical URL.

## Data Structure

### Atom Data

```json
{
  "@context": "https://schema.org/",
  "@type": "PodcastEpisode",
  "name": "<episode_name>",
  "url": "<canonical_episode_url_if_needed>",
  "partOfSeries": "<series_name_or_url_if_needed>",
  "datePublished": "<date_published_if_needed>"
}
```

### Atom Classification

```json
{
  "type": "PodcastEpisode",
  "data": {
    "@context": "https://schema.org/",
    "@type": "PodcastEpisode",
    "name": "<episode_name>",
    "url": "<canonical_episode_url_if_needed>",
    "partOfSeries": "<series_name_or_url_if_needed>",
    "datePublished": "<date_published_if_needed>"
  },
  "meta": {
    "pluginId": "podcast-episode",
    "provider": "opengraph",
    "fetchedAt": "<iso_datetime>",
    "sourceUrl": "<source_url_if_available>"
  }
}
```

### Example

```json
{
  "type": "PodcastEpisode",
  "data": {
    "@context": "https://schema.org/",
    "@type": "PodcastEpisode",
    "name": "The Future of Onchain Reputation",
    "url": "https://example.com/podcast/episodes/onchain-reputation",
    "partOfSeries": "https://example.com/podcast",
    "datePublished": "2026-02-26"
  },
  "meta": {
    "pluginId": "podcast-episode",
    "provider": "opengraph",
    "fetchedAt": "2026-02-26T12:00:00Z",
    "sourceUrl": "https://example.com/podcast/episodes/onchain-reputation"
  }
}
```

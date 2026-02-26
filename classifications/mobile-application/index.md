# MobileApplication | Atom Classification

A "MobileApplication" maps to [MobileApplication](https://schema.org/MobileApplication). Keep this atom minimal with app name and platform disambiguation.

## Data Structure

### Atom Data

```json
{
  "@context": "https://schema.org/",
  "@type": "MobileApplication",
  "name": "<application_name>",
  "operatingSystem": "<ios_or_android_or_both>",
  "applicationCategory": "<category_if_needed>",
  "downloadUrl": "<store_url_if_needed>"
}
```

### Atom Classification

```json
{
  "type": "MobileApplication",
  "data": {
    "@context": "https://schema.org/",
    "@type": "MobileApplication",
    "name": "<application_name>",
    "operatingSystem": "<ios_or_android_or_both>",
    "applicationCategory": "<category_if_needed>",
    "downloadUrl": "<store_url_if_needed>"
  },
  "meta": {
    "pluginId": "mobile-application",
    "provider": "opengraph",
    "fetchedAt": "<iso_datetime>",
    "sourceUrl": "<source_url_if_available>"
  }
}
```

### Example

```json
{
  "type": "MobileApplication",
  "data": {
    "@context": "https://schema.org/",
    "@type": "MobileApplication",
    "name": "Spotify",
    "operatingSystem": "iOS, Android",
    "applicationCategory": "Music",
    "downloadUrl": "https://apps.apple.com/app/spotify/id324684580"
  },
  "meta": {
    "pluginId": "mobile-application",
    "provider": "opengraph",
    "fetchedAt": "2026-02-26T12:00:00Z",
    "sourceUrl": "https://apps.apple.com/app/spotify/id324684580"
  }
}
```

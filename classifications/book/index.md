# Book | Atom Classification

A "Book" maps to [Book](https://schema.org/Book). Keep this atom minimal: `name` is required, and optional fields are only for disambiguation.

## Data Structure

### Atom Data

```json
{
  "@context": "https://schema.org/",
  "@type": "Book",
  "name": "<book_title>",
  "author": "<author_if_needed>",
  "isbn": "<isbn_if_needed>",
  "sameAs": ["<canonical_url_if_needed>"]
}
```

### Atom Classification

```json
{
  "type": "Book",
  "data": {
    "@context": "https://schema.org/",
    "@type": "Book",
    "name": "<book_title>",
    "author": "<author_if_needed>",
    "isbn": "<isbn_if_needed>",
    "sameAs": ["<canonical_url_if_needed>"]
  },
  "meta": {
    "pluginId": "book",
    "provider": "<provider_name>",
    "fetchedAt": "<iso_datetime>",
    "sourceUrl": "<source_url_if_available>"
  }
}
```

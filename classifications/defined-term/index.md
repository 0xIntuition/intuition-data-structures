# DefinedTerm | Atom Classification

"DefinedTerm" is a defined term. Uses fields defined in the [DefinedTerm](https://schema.org/DefinedTerm) class.

## Data Structure

### Atom Data
 
```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "<defined_term_name>",
  "description": "<defined_term_description>"
}
```

### Atom Classification

```json
{
  "type": "DefinedTerm",
  "data": {
    "@context": "https://schema.org/",
    "@type": "DefinedTerm",
    "name": "<defined_term_name>",
    "description": "<defined_term_description>"
  },
  "meta": {
    "pluginId": "defined-term",
    "provider": "dictionary",
    "fetchedAt": "<iso_datetime>",
    "sourceUrl": "<defined_term_url>"
  }
}
```

### Example

```json
{
  "type": "DefinedTerm",
  "data": {
    "@context": "https://schema.org/",
    "@type": "DefinedTerm", 
    "name": "Knowledge Graph",
    "description": "Structured, semantic network that organizes data by connecting entities—such as people, places, or concepts—using relationship"
  },
  "meta": {
    "pluginId": "defined-term",
    "provider": "dictionary",
    "fetchedAt": "2026-02-25T10:00:15-08:00",
    "sourceUrl": "https://github.com/0xintuition/intuition-v2"
  }
}
```
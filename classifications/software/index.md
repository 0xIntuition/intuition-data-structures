# Software | Atom Classification

"Software" is an application, package, or code project. Keep software atoms minimal with just a name and a canonical source URL (GitHub or another primary repository).

## Data Structure

### Atom Data
 
```json
{
  "@context": "https://schema.org/",
  "@type": "SoftwareSourceCode",
  "name": "<software_name>",
  "codeRepository": "<github_or_source_url>"
}
```

### Atom Classification

```json
{
  "type": "SoftwareSourceCode",
  "data": {
    "@context": "https://schema.org/",
    "@type": "SoftwareSourceCode",
    "name": "<software_name>",
    "codeRepository": "<github_or_source_url>"
  },
  "meta": {
    "pluginId": "software",
    "provider": "github",
    "fetchedAt": "<iso_datetime>",
    "sourceUrl": "<github_or_source_url>"
  }
}
```

### Example

```json
{
  "type": "SoftwareSourceCode",
  "data": {
    "@context": "https://schema.org/",
    "@type": "SoftwareSourceCode",
    "name": "intuition-data-structure",
    "codeRepository": "https://github.com/0xintuition/intuition-data-structure"
  },
  "meta": {
    "pluginId": "software",
    "provider": "github",
    "fetchedAt": "2026-02-25T10:00:15-08:00",
    "sourceUrl": "https://github.com/0xintuition/intuition-data-structure"
  }
}
```
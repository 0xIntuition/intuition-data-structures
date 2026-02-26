# JobPosting | Atom Classification

A "JobPosting" maps to [JobPosting](https://schema.org/JobPosting). Keep this atom minimal with role title and hiring organization.

## Data Structure

### Atom Data

```json
{
  "@context": "https://schema.org/",
  "@type": "JobPosting",
  "title": "<job_title>",
  "hiringOrganization": "<organization_name>",
  "jobLocation": "<location_if_needed>",
  "datePosted": "<date_posted_if_needed>",
  "url": "<canonical_job_url_if_needed>"
}
```

### Atom Classification

```json
{
  "type": "JobPosting",
  "data": {
    "@context": "https://schema.org/",
    "@type": "JobPosting",
    "title": "<job_title>",
    "hiringOrganization": "<organization_name>",
    "jobLocation": "<location_if_needed>",
    "datePosted": "<date_posted_if_needed>",
    "url": "<canonical_job_url_if_needed>"
  },
  "meta": {
    "pluginId": "job-posting",
    "provider": "opengraph",
    "fetchedAt": "<iso_datetime>",
    "sourceUrl": "<source_url_if_available>"
  }
}
```

### Example

```json
{
  "type": "JobPosting",
  "data": {
    "@context": "https://schema.org/",
    "@type": "JobPosting",
    "title": "Senior Protocol Engineer",
    "hiringOrganization": "Intuition Labs",
    "jobLocation": "Remote",
    "datePosted": "2026-02-26",
    "url": "https://example.com/jobs/senior-protocol-engineer"
  },
  "meta": {
    "pluginId": "job-posting",
    "provider": "opengraph",
    "fetchedAt": "2026-02-26T12:00:00Z",
    "sourceUrl": "https://example.com/jobs/senior-protocol-engineer"
  }
}
```

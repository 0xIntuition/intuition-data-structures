# Twitter Profile | Atom Enrichment

This document defines canonical enrichment payloads for the `twitter-profile` provider in `@0xintuition/atom-enrichment`.

## Provider Mapping

- **Provider directory:** `packages/atom-enrichment/src/plugins/providers/twitter-profile`
- **Canonical schema source:** `packages/atom-enrichment/src/plugins/providers/twitter-profile/schema.ts`
- **Classification registry source:** `packages/atom-enrichment/src/classifications/defaults.ts`
- **Plugin source:** _No provider `index.ts` implementation in this worktree (schema-only prototype)._
- **Plugin id:** `twitter-profile`
- **Artifact type(s):** `twitter-profile`
- **Classification slug(s):** `twitter-profile`
- **Runtime:** `server`

## Data Structure

### X/Twitter Profile Data (`twitter-profile`)

Profile metadata for an X/Twitter account.

```json
{
  "username": "<string>",
  "name": "<string_if_available>",
  "bio": "<string_if_available>",
  "profileImageUrl": "<https_url_if_available>",
  "followers": "<number_if_available>",
  "following": "<number_if_available>",
  "tweetCount": "<number_if_available>",
  "verified": "<boolean_if_available>",
  "joinedAt": "<string_if_available>"
}
```

### X/Twitter Profile Artifact

```json
{
  "artifact_type": "twitter-profile",
  "data": {
    "username": "<string>",
    "name": "<string_if_available>",
    "bio": "<string_if_available>",
    "profileImageUrl": "<https_url_if_available>",
    "followers": "<number_if_available>",
    "following": "<number_if_available>",
    "tweetCount": "<number_if_available>",
    "verified": "<boolean_if_available>",
    "joinedAt": "<string_if_available>"
  },
  "meta": {
    "pluginId": "twitter-profile",
    "provider": "twitter-profile",
    "fetchedAt": "<iso_datetime>",
    "sourceUrl": "<source_url_if_available>"
  }
}
```

# Atom Enrichment Data Structures

This directory documents provider-level context payloads used by `@0xintuition/atom-enrichment`.

Enrichment answers a different question from classification: "What additional context can we attach to this atom right now?"

## Why Enrichment Is Offchain

Enrichment data is intentionally offchain because it is mutable and provider-dependent. API responses change, metadata goes stale, and providers can be swapped or expanded over time.

Keeping enrichment offchain allows:

- refresh without rewriting atom identity
- provider-specific evolution without onchain migrations
- richer UX and search context while preserving minimal onchain atoms

## Onchain vs Offchain

| Layer | Purpose | Typical Data |
| --- | --- | --- |
| Onchain Classification (`../classifications`) | Stable identity contract | minimal type-safe identity payload |
| Offchain Enrichment (this folder) | Dynamic contextual contract | API payloads, logos/media, prices, summaries, metrics |

## How Enrichment Flows

1. Start from a classified atom (`type` + minimal identity).
2. Select provider plugins for that atom/input.
3. Fetch upstream data and normalize it into canonical artifact shapes.
4. Emit artifacts under a shared envelope (`artifact_type`, `data`, `meta`).
5. Compose these artifacts at read time with onchain atom identity.

## Canonical Source of Truth

- Provider implementations: `packages/atom-enrichment/src/plugins/providers/*/index.ts`
- Provider schemas: `packages/atom-enrichment/src/plugins/providers/*/schema.ts`
- Classification registry bindings: `packages/atom-enrichment/src/classifications/defaults.ts`

## Common Artifact Envelope

```json
{
  "artifact_type": "<classification_slug>",
  "data": {},
  "meta": {
    "pluginId": "<plugin_slug>",
    "provider": "<provider_name>",
    "fetchedAt": "<iso_datetime>",
    "sourceUrl": "<source_url_if_available>",
    "confidence": "<number_0_to_1_if_available>",
    "fromCache": "<boolean_if_available>",
    "cachedAt": "<iso_datetime_if_available>"
  }
}
```

## Provider Docs

- [AI Entities](./ai-entities/index.md)
- [AI Summary](./ai-summary/index.md)
- [Apple Music](./apple-music/index.md)
- [arXiv](./arxiv/index.md)
- [Brand](./brand/index.md)
- [CoinGecko](./coingecko/index.md)
- [Color Palette](./color-palette/index.md)
- [Company Profile](./company-profile/index.md)
- [Crossref](./crossref/index.md)
- [Crunchbase](./crunchbase/index.md)
- [Dictionary](./dictionary/index.md)
- [ENS](./ens/index.md)
- [Etherscan](./etherscan/index.md)
- [Favicon](./favicon/index.md)
- [Geocode](./geocode/index.md)
- [GitHub](./github/index.md)
- [ISBN](./isbn/index.md)
- [Microdata](./microdata/index.md)
- [MusicBrainz](./musicbrainz/index.md)
- [NFT Metadata](./nft-metadata/index.md)
- [NPM](./npm/index.md)
- [oEmbed](./oembed/index.md)
- [OpenGraph](./opengraph/index.md)
- [Places](./places/index.md)
- [Product Listing](./product-listing/index.md)
- [Pubmed](./pubmed/index.md)
- [Reddit Post](./reddit-post/index.md)
- [Screenshot](./screenshot/index.md)
- [Spotify](./spotify/index.md)
- [TMDB](./tmdb/index.md)
- [Twitter Profile](./twitter-profile/index.md)
- [Vimeo](./vimeo/index.md)
- [Wikidata](./wikidata/index.md)
- [Wikipedia](./wikipedia/index.md)
- [YouTube](./youtube/index.md)

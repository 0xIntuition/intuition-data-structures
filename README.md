# Intuition Data Structures

This package defines the canonical data contracts for atom classification and atom enrichment in Intuition.

If you are new to this area, the key idea is:

- store minimal identity onchain
- attach rich metadata offchain

## Why This Matters

Atoms are the core identity primitives in the Intuition graph. If atoms contain too many mutable fields (images, long descriptions, changing URLs), they become stale and expensive to maintain.

Instead, this package enforces minimal atom payloads that are durable over time. Rich context still exists, but as enrichment artifacts that can be refreshed independently.

## Design Principles

1. Minimal by default: keep only the smallest viable identity payload in base atoms.
2. Durable over complete: prefer fields that are stable over time.
3. Separate identity and context:
   - onchain atom = identity
   - offchain enrichment = context
4. Flat taxonomy: each classification type is a top-level category in this repo.
5. Extensible by artifacts: new metadata sources should not require changing core atom identity.

## Onchain vs Offchain

| Layer | Purpose | Typical Data |
| --- | --- | --- |
| On-chain Atom | Stable identity | `name`, canonical type, minimal disambiguators |
| Off-chain Enrichment | Rich context for UX/search | images, descriptions, social links, provider-specific fields |

## What Is In This Folder

- `classifications/`
  - one folder per classification type
  - each file defines:
    - minimal `Atom Data`
    - `Atom Classification` envelope
    - an example payload
- `enrichment/`
  - provider-specific enrichment schemas
  - shared artifact envelope conventions

## URL to Atom to Enrichment Flow

### 1. Input URL

```text
https://en.wikipedia.org/wiki/Brad_Pitt
```

### 2. Classification Output (Minimal)

```json
{
  "type": "Person",
  "data": {
    "@context": "https://schema.org/",
    "@type": "Person",
    "name": "Brad Pitt",
    "sameAs": ["https://www.wikidata.org/wiki/Q35332"]
  }
}
```

### 3. Onchain Atom

Only the minimal atom data is committed onchain.

### 4. Offchain Enrichment Artifact

```json
{
  "artifact_type": "person",
  "data": {
    "image": "https://upload.wikimedia.org/example.jpg",
    "description": "American actor and film producer"
  },
  "meta": {
    "pluginId": "wikidata",
    "provider": "wikidata",
    "fetchedAt": "2026-02-26T12:00:00Z",
    "sourceUrl": "https://www.wikidata.org/wiki/Q35332",
    "confidence": 0.98
  }
}
```

### 5. Query-Time Composition

Clients compose:

- stable atom identity from onchain data
- latest enrichment artifacts from backend services

This provides rich UX without bloating atom payloads.

## Flat Classification Catalog

Each type below is a first-class top-level classification:

- [Thing](./classifications/thing/index.md) (`Thing`)
- [Person](./classifications/person/index.md) (`Person`)
- [Company](./classifications/company/index.md) (`Organization`)
- [Location](./classifications/location/index.md) (`Place`)
- [Product](./classifications/product/index.md) (`Product`)
- [Service](./classifications/service/index.md) (`Service`)
- [Software](./classifications/software/index.md) (`SoftwareSourceCode`)
- [Music Recording](./classifications/music-recording/index.md) (`MusicRecording`)
- [Music Album](./classifications/music-album/index.md) (`MusicAlbum`)
- [Music Group](./classifications/music-group/index.md) (`MusicGroup`)
- [Book](./classifications/book/index.md) (`Book`)
- [Article](./classifications/article/index.md) (`Article`)
- [News Article](./classifications/news-article/index.md) (`NewsArticle`)
- [Social Media Posting](./classifications/social-media-posting/index.md) (`SocialMediaPosting`)
- [Comment](./classifications/comment/index.md) (`Comment`)
- [Review](./classifications/review/index.md) (`Review`)
- [Aggregate Rating](./classifications/aggregate-rating/index.md) (`AggregateRating`)
- [Image](./classifications/image/index.md) (`ImageObject`)
- [Video Object](./classifications/video-object/index.md) (`VideoObject`)
- [Movie](./classifications/movie/index.md) (`Movie`)
- [TV Series](./classifications/tv-series/index.md) (`TVSeries`)
- [Podcast Series](./classifications/podcast-series/index.md) (`PodcastSeries`)
- [Podcast Episode](./classifications/podcast-episode/index.md) (`PodcastEpisode`)
- [Event](./classifications/event/index.md) (`Event`)
- [Web Site](./classifications/web-site/index.md) (`WebSite`)
- [Web Page](./classifications/web-page/index.md) (`WebPage`)
- [Brand](./classifications/brand/index.md) (`Brand`)
- [Software Application](./classifications/software-application/index.md) (`SoftwareApplication`)
- [Mobile Application](./classifications/mobile-application/index.md) (`MobileApplication`)
- [Dataset](./classifications/dataset/index.md) (`Dataset`)
- [Job Posting](./classifications/job-posting/index.md) (`JobPosting`)
- [Local Business](./classifications/local-business/index.md) (`LocalBusiness`)
- [Defined Term](./classifications/defined-term/index.md) (`DefinedTerm`)
- [Ethereum Account](./classifications/ethereum-account/index.md) (`EthereumAccount`)
- [Ethereum Smart Contract](./classifications/ethereum-smart-contract/index.md) (`EthereumSmartContract`)
- [Ethereum ERC20](./classifications/ethereum-erc20/index.md) (`EthereumERC20`)

## Minimal Atom Examples

### Person

```json
{
  "@context": "https://schema.org/",
  "@type": "Person",
  "name": "Brad Pitt",
  "sameAs": ["https://www.wikidata.org/wiki/Q35332"]
}
```

### Company

```json
{
  "@context": "https://schema.org/",
  "@type": "Organization",
  "name": "OpenAI",
  "url": "https://openai.com"
}
```

### Software

```json
{
  "@context": "https://schema.org/",
  "@type": "SoftwareSourceCode",
  "name": "Bun",
  "codeRepository": "https://github.com/oven-sh/bun"
}
```

### Music Recording

```json
{
  "@context": "https://schema.org/",
  "@type": "MusicRecording",
  "name": "Bohemian Rhapsody",
  "byArtist": "Queen",
  "inAlbum": "A Night at the Opera"
}
```

### Ethereum Smart Contract

```json
{
  "chainId": "1",
  "address": "0xA0b86991c6218b36c1d19d4a2e9eb0ce3606eb48"
}
```

## Enrichment Artifact Envelope

All enrichment payloads follow this common envelope:

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

Provider examples:

- [AI Entities](./enrichment/ai-entities/index.md)
- [AI Summary](./enrichment/ai-summary/index.md)
- [Apple Music](./enrichment/apple-music/index.md)
- [arXiv](./enrichment/arxiv/index.md)
- [Brand](./enrichment/brand/index.md)
- [CoinGecko](./enrichment/coingecko/index.md)
- [Color Palette](./enrichment/color-palette/index.md)
- [Company Profile](./enrichment/company-profile/index.md)
- [Crossref](./enrichment/crossref/index.md)
- [Crunchbase](./enrichment/crunchbase/index.md)
- [Dictionary](./enrichment/dictionary/index.md)
- [ENS](./enrichment/ens/index.md)
- [Etherscan](./enrichment/etherscan/index.md)
- [Favicon](./enrichment/favicon/index.md)
- [Geocode](./enrichment/geocode/index.md)
- [GitHub](./enrichment/github/index.md)
- [ISBN](./enrichment/isbn/index.md)
- [Microdata](./enrichment/microdata/index.md)
- [MusicBrainz](./enrichment/musicbrainz/index.md)
- [NFT Metadata](./enrichment/nft-metadata/index.md)
- [NPM](./enrichment/npm/index.md)
- [oEmbed](./enrichment/oembed/index.md)
- [OpenGraph](./enrichment/opengraph/index.md)
- [Places](./enrichment/places/index.md)
- [Product Listing](./enrichment/product-listing/index.md)
- [Pubmed](./enrichment/pubmed/index.md)
- [Reddit Post](./enrichment/reddit-post/index.md)
- [Screenshot](./enrichment/screenshot/index.md)
- [Spotify](./enrichment/spotify/index.md)
- [TMDB](./enrichment/tmdb/index.md)
- [Twitter Profile](./enrichment/twitter-profile/index.md)
- [Vimeo](./enrichment/vimeo/index.md)
- [Wikidata](./enrichment/wikidata/index.md)
- [Wikipedia](./enrichment/wikipedia/index.md)
- [YouTube](./enrichment/youtube/index.md)

## Adding a New Classification

1. Create `classifications/<type-slug>/index.md`.
2. Keep required fields minimal and identity-focused.
3. Move mutable metadata to enrichment artifacts.
4. Include:
   - `Atom Data`
   - `Atom Classification`
   - one concrete example

# Atom Classification Data Structures

This directory defines the canonical identity contracts for atoms.

Classification answers one question: "What is this thing, at its smallest durable identity?"

## Why Classification Is Separate

Onchain data should be stable, minimal, and cheap to maintain. Classification docs enforce that by focusing on identity fields only (name, canonical type, and minimal disambiguators).

If mutable context (descriptions, images, social stats, market data, etc.) is mixed into base atom payloads, atoms become stale and expensive to keep correct.

## Onchain vs Offchain

| Layer | Purpose | Typical Data |
| --- | --- | --- |
| Onchain Classification (this folder) | Durable identity contract | canonical type, normalized minimal `Atom Data` |
| Offchain Enrichment (`../enrichment`) | Refreshable context contract | provider payloads, media, metrics, dynamic metadata |

## How Classification Is Used

1. Input is parsed and normalized into a classification type.
2. Minimal `Atom Data` is produced for that type.
3. The atom can be created/linked onchain from that stable payload.
4. Enrichment artifacts can then attach rich context without changing atom identity.

## Contract Shape

Each classification doc includes:

- minimal `Atom Data` (identity-first)
- `Atom Classification` envelope
- one concrete example payload

## Folder Convention

- `classifications/<type-slug>/index.md`
- One first-class type per folder (flat taxonomy, no umbrella folders)

## Available Types

- `thing`
- `person`
- `company`
- `location`
- `product`
- `service`
- `software`
- `music-recording`
- `music-album`
- `music-group`
- `book`
- `article`
- `news-article`
- `social-media-posting`
- `comment`
- `review`
- `aggregate-rating`
- `image`
- `video-object`
- `movie`
- `tv-series`
- `podcast-series`
- `podcast-episode`
- `event`
- `web-site`
- `web-page`
- `brand`
- `software-application`
- `mobile-application`
- `dataset`
- `job-posting`
- `local-business`
- `defined-term`
- `ethereum-account`
- `ethereum-smart-contract`
- `ethereum-erc20`

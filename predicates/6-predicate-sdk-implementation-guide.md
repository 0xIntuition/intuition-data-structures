# Predicate SDK Implementation Guide

## Overview

This document specifies how to implement enshrined predicates in the Intuition SDK. Predicate atoms use the **Schema.org DefinedTerm** classification — the same typed JSON-LD pattern used for every other atom type in the protocol (Person, Organization, DefinedTerm tags, etc.).

The SDK supports two storage strategies for predicate atom data:

| Strategy | Atom Data On-Chain | Best For |
|---|---|---|
| **Inline JSON** (Approach C) | The canonical DefinedTerm JSON itself | Self-describing atoms; no external dependencies |
| **IPFS URI** (Approach D) | An `ipfs://` URI pointing to the DefinedTerm JSON on IPFS | Rich metadata (i18n, examples, Schema.org mappings) embedded in the document |

Both strategies produce valid predicate atoms. Both produce deterministic atom IDs. They are different atoms with different IDs — a project chooses one strategy per predicate and uses it consistently.

See `4.predicate-architecture-first-principles.md` for the full trade-off analysis between these approaches.

---

## Naming Conventions

All predicate names use **base verb form** — the form that works with the `I` subject atom.

| Rule | Example | Why |
|---|---|---|
| Base verb form | `follow` not `follows` | Works with `(I, follow, Vitalik)` — the primary triple pattern |
| Lowercase | `has tag` not `Has Tag` | Consistent hashing; no case ambiguity |
| US English spelling | `favorite` not `favourite` | One canonical form per concept |
| Multi-word uses spaces | `bullish on` not `bullish-on` | Natural language readability |
| Exception: `has X` compounds | `has tag` not `have tag` | These are fixed labels, not conjugating verbs |

See `3.predicate-display-and-conjugation.md` for how the display layer handles conjugation (`follow` → `follows` when subject is singular).

---

## Strategy 1: Inline JSON (Approach C)

The DefinedTerm JSON is stored directly as the atom data on-chain. The atom is fully self-describing — anyone reading chain data can see the predicate's name and description without any external fetch.

### Canonical JSON Serialization

The SDK must produce **identical JSON bytes** for the same predicate across all environments. The atom ID is derived from the exact bytes of the atom data.

```typescript
/**
 * Produces canonical JSON for a DefinedTerm predicate atom.
 * Key order, whitespace, and encoding are deterministic.
 *
 * Keys are always in this order: @context, @type, name, description
 * No trailing whitespace, no pretty-printing, no BOM.
 */
function createPredicateAtomData(name: string, description: string): string {
  return JSON.stringify({
    "@context": "https://schema.org/",
    "@type": "DefinedTerm",
    "name": name,
    "description": description
  });
}

// Output (one line, deterministic):
// {"@context":"https://schema.org/","@type":"DefinedTerm","name":"follow","description":"Directional subscription or tracking of the object entity"}
```

This is the same serialization approach used for Person, Organization, and all other classified atoms. The canonical JSON serializer is already in the codebase.

### Atom Creation

```typescript
const followAtomData = createPredicateAtomData(
  "follow",
  "Directional subscription or tracking of the object entity"
);
const followAtomId = await createAtom(followAtomData);

// On-chain: ~120 bytes of JSON
// Atom ID: keccak256(ATOM_SALT, keccak256(toHex(followAtomData)))
```

### What a Developer Sees on Chain

```
atom data: {"@context":"https://schema.org/","@type":"DefinedTerm","name":"follow","description":"Directional subscription or tracking of the object entity"}
```

Readable directly. No IPFS fetch, no API call, no SDK required for basic comprehension.

### Trade-Offs

| Aspect | Assessment |
|---|---|
| Self-describing | Yes — name and description visible on-chain |
| External dependency | None |
| Gas cost | ~120 bytes per atom (trivial on L2) |
| Metadata richness | Limited to name + description. Extended metadata (i18n, Schema.org mappings, examples) must be linked separately via IPFS enrichment triples. |
| Description mutability | Frozen in the atom. Write carefully. |

---

## Strategy 2: IPFS URI (Approach D)

The DefinedTerm JSON is uploaded to IPFS first. The atom data stored on-chain is the `ipfs://` URI. The IPFS document can be richer than the inline approach — it can embed i18n labels, Schema.org action mappings, conjugation rules, and examples directly in the content-addressed document.

### IPFS Document Format

```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "follow",
  "description": "Directional subscription or tracking of the object entity",
  "inDefinedTermSet": "https://intuition.systems/predicates",
  "sameAs": ["https://schema.org/FollowAction"],
  "alternateName": [
    {"@language": "en", "@value": "follow"},
    {"@language": "es", "@value": "seguir"},
    {"@language": "fr", "@value": "suivre"},
    {"@language": "ja", "@value": "フォローする"},
    {"@language": "zh", "@value": "关注"}
  ],
  "additionalProperty": [
    {
      "@type": "PropertyValue",
      "name": "marketPattern",
      "value": "depositional"
    },
    {
      "@type": "PropertyValue",
      "name": "conjugates",
      "value": true
    },
    {
      "@type": "PropertyValue",
      "name": "i18n",
      "value": {
        "en": { "base": "follow", "thirdPerson": "follows", "displayName": "Follow" },
        "es": { "base": "seguir", "thirdPerson": "sigue", "displayName": "Seguir" },
        "fr": { "base": "suivre", "thirdPerson": "suit", "displayName": "Suivre" },
        "ja": { "base": "フォローする", "displayName": "フォロー" },
        "zh": { "base": "关注", "displayName": "关注" }
      }
    }
  ]
}
```

### Upload and Atom Creation

```typescript
// 1. Upload the DefinedTerm document to IPFS
const ipfsDocument = buildPredicateIpfsDocument("follow", {
  description: "Directional subscription or tracking of the object entity",
  sameAs: ["https://schema.org/FollowAction"],
  marketPattern: "depositional",
  conjugates: true,
  i18n: {
    en: { base: "follow", thirdPerson: "follows", displayName: "Follow" },
    es: { base: "seguir", thirdPerson: "sigue", displayName: "Seguir" },
    // ...
  }
});

const cid = await ipfsClient.add(JSON.stringify(ipfsDocument));
// cid = "bafyreif7x..."

// 2. Create the atom with the IPFS URI as atom data
const followAtomData = `ipfs://${cid}`;
const followAtomId = await createAtom(followAtomData);

// On-chain: ~60 bytes (just the URI string)
// Atom ID: keccak256(ATOM_SALT, keccak256(toHex("ipfs://bafyreif7x...")))
```

### What a Developer Sees on Chain

```
atom data: "ipfs://bafyreif7x..."
```

Opaque without an IPFS fetch. The developer must resolve the CID to read the DefinedTerm document. Once fetched, they see the full Schema.org object with name, description, i18n, and all metadata.

### IPFS Document Builder

```typescript
interface PredicateIpfsOptions {
  description: string;
  sameAs?: string[];
  marketPattern: 'depositional' | 'attributive' | 'comparative';
  conjugates: boolean;
  i18n: Record<string, {
    base: string;
    thirdPerson?: string;
    pastParticiple?: string;
    displayName: string;
    direction?: 'ltr' | 'rtl';
  }>;
}

function buildPredicateIpfsDocument(name: string, options: PredicateIpfsOptions): object {
  const doc: any = {
    "@context": "https://schema.org/",
    "@type": "DefinedTerm",
    "name": name,
    "description": options.description,
    "inDefinedTermSet": "https://intuition.systems/predicates",
  };

  if (options.sameAs?.length) {
    doc.sameAs = options.sameAs;
  }

  // Language tags via Schema.org alternateName
  const altNames = Object.entries(options.i18n)
    .filter(([lang]) => lang !== 'en')
    .map(([lang, labels]) => ({ "@language": lang, "@value": labels.base }));
  if (altNames.length) {
    doc.alternateName = altNames;
  }

  // Extended properties
  doc.additionalProperty = [
    { "@type": "PropertyValue", "name": "marketPattern", "value": options.marketPattern },
    { "@type": "PropertyValue", "name": "conjugates", "value": options.conjugates },
    { "@type": "PropertyValue", "name": "i18n", "value": options.i18n },
  ];

  return doc;
}
```

### Updating IPFS Documents

Adding translations or fixing metadata creates a new IPFS document with a new CID. The predicate atom itself is unchanged — only the enrichment link is updated:

```typescript
// Add Korean translations → new IPFS document → new CID
const updatedDoc = buildPredicateIpfsDocument("follow", {
  // ... same as before, plus:
  i18n: {
    // ... existing languages ...
    ko: { base: "팔로우하다", displayName: "팔로우" },
  }
});

const newCid = await ipfsClient.add(JSON.stringify(updatedDoc));
const newUriAtomId = await createAtom(`ipfs://${newCid}`);

// Link the predicate atom to the updated enrichment
await createTriple({
  subject: followAtomId,
  predicate: HAS_SOURCE,
  object: newUriAtomId
});
```

The predicate atom, its atom ID, and all existing triples are unaffected.

### Trade-Offs

| Aspect | Assessment |
|---|---|
| Self-describing | No — requires IPFS fetch to read name and description |
| External dependency | IPFS gateway required for resolution |
| Gas cost | ~60 bytes per atom (lower than inline) |
| Metadata richness | Unlimited — i18n, Schema.org mappings, examples all in one document |
| Description mutability | The IPFS doc is frozen (content-addressed), but you can link to a new version via triples |
| Availability risk | If IPFS pinning lapses and no gateway has the content, the predicate becomes opaque |

---

## Choosing a Strategy

| Consideration | Inline JSON (C) | IPFS URI (D) |
|---|---|---|
| You want self-describing atoms | **Yes** | No |
| You want rich embedded metadata | Limited | **Yes** |
| You want zero external dependencies | **Yes** | No |
| You want i18n in the atom document | No (off-chain) | **Yes** |
| You want lowest gas | No (~120 bytes) | **Yes** (~60 bytes) |
| You want easiest cold-start DX | **Yes** | No |
| You have reliable IPFS infrastructure | Either | **Required** |

### Recommended Approach

Both strategies can coexist in the same protocol. A pragmatic approach:

- **Use Inline JSON (C) for the predicate atom itself.** The predicate atom is self-describing on-chain. Anyone reading chain data understands it.
- **Use IPFS URI (D) for the enrichment link.** A separate `(predicate_atom, has source, ipfs://...)` triple points to the rich IPFS document with i18n, Schema.org mappings, and extended metadata.

This gives you self-describing atoms (C's strength) AND rich metadata (D's strength) without the downsides of either approach alone.

```
┌─ On-chain atom data (Inline JSON) ────────────────────────────────┐
│  {"@type":"DefinedTerm","name":"follow","description":"..."}      │
│  → Self-describing. Readable without external fetch.              │
├─ On-chain triple: market routing ─────────────────────────────────┤
│  (follow_atom, has type, depositional_atom)                       │
├─ On-chain triple: registry ───────────────────────────────────────┤
│  (predicate_registry, contain, follow_atom)                       │
├─ On-chain triple: enrichment link ────────────────────────────────┤
│  (follow_atom, has source, ipfs://bafyreif7x...)                  │
│  → Points to rich IPFS document with i18n, Schema.org, examples  │
└───────────────────────────────────────────────────────────────────┘
```

Alternatively, a project that prefers IPFS-native atoms can use Strategy 2 exclusively — the predicate atom data is the `ipfs://` URI, and the IPFS document is the source of truth.

---

## Predicate Definitions

The SDK exports predicate definitions for all 25 launch predicates. Each definition includes the `name`, `description`, and the atom data for both strategies.

```typescript
interface PredicateDefinition {
  name: string;
  description: string;
  marketPattern: 'depositional' | 'attributive' | 'comparative';
  conjugates: boolean;
  thirdPerson?: string;  // display form for singular subjects
}

export const PREDICATE_DEFS: Record<string, PredicateDefinition> = {
  // --- Curation and Collection ---
  contain: {
    name: "contain",
    description: "The subject collection includes the object as a member or entry",
    marketPattern: "attributive",
    conjugates: true,
    thirdPerson: "contains",
  },
  curatedBy: {
    name: "curated by",
    description: "The subject collection is maintained and organized by the object actor",
    marketPattern: "attributive",
    conjugates: false,
  },

  // --- Identity and Classification ---
  is: {
    name: "is",
    description: "Asserts identity, type membership, or definitional equivalence between subject and object",
    marketPattern: "attributive",
    conjugates: false,
  },
  hasTag: {
    name: "has tag",
    description: "Assigns a reusable tag or keyword to the subject for filtering and discovery",
    marketPattern: "attributive",
    conjugates: false,
  },
  hasType: {
    name: "has type",
    description: "Classifies the subject under a formal type or category",
    marketPattern: "attributive",
    conjugates: false,
  },

  // --- Social ---
  follow: {
    name: "follow",
    description: "Directional subscription or tracking of the object entity",
    marketPattern: "depositional",
    conjugates: true,
    thirdPerson: "follows",
  },
  like: {
    name: "like",
    description: "Lightweight positive signal toward the object entity",
    marketPattern: "depositional",
    conjugates: true,
    thirdPerson: "likes",
  },

  // --- Metadata ---
  hasDescription: {
    name: "has description",
    description: "Attaches a textual description to the subject entity",
    marketPattern: "attributive",
    conjugates: false,
  },
  url: {
    name: "url",
    description: "Links the subject to its canonical web address",
    marketPattern: "attributive",
    conjugates: false,
  },
  imgUrl: {
    name: "imgUrl",
    description: "Links the subject to an image URL for visual representation",
    marketPattern: "attributive",
    conjugates: false,
  },

  // --- Trust and Reputation ---
  trust: {
    name: "trust",
    description: "Positive trust assertion toward the object entity",
    marketPattern: "depositional",
    conjugates: true,
    thirdPerson: "trusts",
  },
  distrust: {
    name: "distrust",
    description: "Negative trust assertion toward the object entity",
    marketPattern: "depositional",
    conjugates: true,
    thirdPerson: "distrusts",
  },
  endorse: {
    name: "endorse",
    description: "Strong public support or vouching for the object's quality or legitimacy",
    marketPattern: "depositional",
    conjugates: true,
    thirdPerson: "endorses",
  },
  recommend: {
    name: "recommend",
    description: "Active recommendation of the object to others in the ecosystem",
    marketPattern: "depositional",
    conjugates: true,
    thirdPerson: "recommends",
  },
  vouchFor: {
    name: "vouch for",
    description: "Personal credibility stake on the object's identity, quality, or claims",
    marketPattern: "depositional",
    conjugates: true,
    thirdPerson: "vouches for",
  },

  // --- Sentiment and Opinion ---
  agreeWith: {
    name: "agree with",
    description: "Alignment with the object position, claim, or proposal",
    marketPattern: "depositional",
    conjugates: true,
    thirdPerson: "agrees with",
  },
  disagreeWith: {
    name: "disagree with",
    description: "Opposition to the object position, claim, or proposal",
    marketPattern: "depositional",
    conjugates: true,
    thirdPerson: "disagrees with",
  },
  bullishOn: {
    name: "bullish on",
    description: "Positive conviction about the object's future value or success",
    marketPattern: "depositional",
    conjugates: false,
  },
  bearishOn: {
    name: "bearish on",
    description: "Negative conviction about the object's future value or success",
    marketPattern: "depositional",
    conjugates: false,
  },

  // --- Attribution ---
  createdBy: {
    name: "created by",
    description: "The subject was originally created or brought into existence by the object actor",
    marketPattern: "attributive",
    conjugates: false,
  },
  sameAs: {
    name: "same as",
    description: "The subject and object refer to the same real-world entity across different representations",
    marketPattern: "attributive",
    conjugates: false,
  },
  linkedAccount: {
    name: "linked account",
    description: "Connects the subject identity to one of its platform account atoms",
    marketPattern: "attributive",
    conjugates: false,
  },
  hasCategory: {
    name: "has category",
    description: "Places the subject within a broader categorical grouping",
    marketPattern: "attributive",
    conjugates: false,
  },

  // --- Comparison ---
  betterThan: {
    name: "better than",
    description: "Subjective superiority claim: the subject is asserted as better than the object",
    marketPattern: "comparative",
    conjugates: false,
  },
  alternativeTo: {
    name: "alternative to",
    description: "The subject can serve as a substitute or competing option for the object",
    marketPattern: "comparative",
    conjugates: false,
  },
};
```

### Generating Atom Data for Either Strategy

```typescript
// Strategy C: Inline JSON
function getInlineAtomData(key: string): string {
  const def = PREDICATE_DEFS[key];
  return createPredicateAtomData(def.name, def.description);
}

// Strategy D: IPFS URI (after uploading)
async function getIpfsAtomData(key: string, ipfsClient: IpfsClient): Promise<string> {
  const def = PREDICATE_DEFS[key];
  const doc = buildPredicateIpfsDocument(def.name, {
    description: def.description,
    marketPattern: def.marketPattern,
    conjugates: def.conjugates,
    i18n: await loadI18nLabels(key), // from i18n registry
  });
  const cid = await ipfsClient.add(JSON.stringify(doc));
  return `ipfs://${cid}`;
}

// Pre-computed atom IDs (strategy-dependent)
export function computePredicateAtomId(
  key: string,
  strategy: 'inline' | 'ipfs',
  cid?: string
): string {
  if (strategy === 'inline') {
    return calculateAtomId(getInlineAtomData(key));
  } else {
    if (!cid) throw new Error('CID required for IPFS strategy');
    return calculateAtomId(`ipfs://${cid}`);
  }
}
```

### Convenience Exports

The SDK ships with pre-computed atom IDs for the chosen strategy. The project configures which strategy at build time.

```typescript
// For inline JSON strategy:
export const FOLLOW = calculateAtomId(getInlineAtomData('follow'));
export const LIKE = calculateAtomId(getInlineAtomData('like'));
export const TRUST = calculateAtomId(getInlineAtomData('trust'));
// ...

// For IPFS strategy (CIDs are fixed after initial upload):
// export const FOLLOW = calculateAtomId('ipfs://bafyreif7x...');
// export const LIKE = calculateAtomId('ipfs://bafyreig3m...');
// ...
```

---

## The `I` Atom

The `I` atom is not a predicate — it is a subject identifier. It remains a plain string atom.

```typescript
export const I_SUBJECT_DATA = 'I';
export const I_SUBJECT_ID = calculateAtomId(I_SUBJECT_DATA);
```

`I` is not a classified entity. It is a protocol-level identifier that resolves to the depositor's on-chain address.

---

## Creating Triples

The developer-facing API for creating triples is the same regardless of strategy. The SDK resolves predicate constants to atom IDs internally.

```typescript
// Depositional triple: user follows Vitalik
await createTriple({
  subject: I_SUBJECT_ID,
  predicate: FOLLOW,
  object: vitalikAtomId,
  deposit: parseEther('0.1'),
});

// Attributive triple: fact about Ethereum
await createTriple({
  subject: ethereumAtomId,
  predicate: CREATED_BY,
  object: vitalikAtomId,
  deposit: parseEther('0.01'),
});

// Collection curation: adding an item
await createTriple({
  subject: defiBlueChipsAtomId,
  predicate: CONTAIN,
  object: aaveAtomId,
  deposit: parseEther('0.01'),
});
```

---

## Reading Predicate Atom Data

How the atom data looks depends on which strategy was used to create the atom.

### Inline JSON Atoms

```typescript
const triple = await getTriple(tripleId);
const predicateData = JSON.parse(triple.predicate.atomData);

predicateData["@type"];      // "DefinedTerm"
predicateData.name;          // "follow"
predicateData.description;   // "Directional subscription or tracking..."
```

Same pattern as reading a Person or Organization atom.

### IPFS URI Atoms

```typescript
const triple = await getTriple(tripleId);
const atomData = triple.predicate.atomData;

// atomData = "ipfs://bafyreif7x..."
if (atomData.startsWith('ipfs://')) {
  const doc = await fetchFromIpfs(atomData);
  doc.name;          // "follow"
  doc.description;   // "Directional subscription or tracking..."
  doc.alternateName; // [{@language: "fr", @value: "suivre"}, ...]
}
```

The indexer/API can pre-resolve IPFS URIs and serve the document inline:

```json
{
  "predicate": {
    "atomId": "0x456...",
    "atomData": "ipfs://bafyreif7x...",
    "resolved": {
      "@type": "DefinedTerm",
      "name": "follow",
      "description": "Directional subscription or tracking of the object entity",
      "alternateName": [{"@language": "fr", "@value": "suivre"}]
    },
    "marketPattern": "depositional",
    "enshrined": true
  }
}
```

---

## On-Chain Bootstrap

### Atoms to Create

For each enshrined predicate, create one atom using the chosen strategy.

```typescript
// Using inline JSON:
for (const [key, def] of Object.entries(PREDICATE_DEFS)) {
  const atomData = createPredicateAtomData(def.name, def.description);
  const atomId = await createAtom(atomData);
  console.log(`${key}: ${atomId}`);
}

// Or using IPFS:
for (const [key, def] of Object.entries(PREDICATE_DEFS)) {
  const doc = buildPredicateIpfsDocument(def.name, { /* ... */ });
  const cid = await ipfsClient.add(JSON.stringify(doc));
  const atomId = await createAtom(`ipfs://${cid}`);
  console.log(`${key}: ${atomId} (ipfs://${cid})`);
}

// Also create the I atom
const iAtomId = await createAtom(I_SUBJECT_DATA);
```

Total: **26 atoms** (25 predicates + 1 `I` atom).

### Market Routing Triples

After atoms are created, create 2 triples per predicate to encode market pattern and registry membership. This is the same regardless of strategy.

```typescript
// Market pattern marker atoms
const depositionalId = await createAtom(createPredicateAtomData(
  "depositional",
  "Market pattern where depositors make first-person claims via the I-subject"
));
const attributiveId = await createAtom(createPredicateAtomData(
  "attributive",
  "Market pattern where the triple asserts a fact about a specific entity"
));
const comparativeId = await createAtom(createPredicateAtomData(
  "comparative",
  "Market pattern where the triple compares two entities"
));

// Predicate registry atom
const registryId = await createAtom(createPredicateAtomData(
  "predicate registry",
  "Canonical registry of enshrined Intuition predicates"
));

// Market routing + registry membership (2 triples per predicate)
const MARKET_MAP: Record<string, string> = {};
for (const [key, def] of Object.entries(PREDICATE_DEFS)) {
  const patternId = def.marketPattern === 'depositional' ? depositionalId
                  : def.marketPattern === 'attributive'  ? attributiveId
                  : comparativeId;
  await createTriple({ subject: PREDICATE_IDS[key], predicate: HAS_TYPE, object: patternId });
  await createTriple({ subject: registryId, predicate: CONTAIN, object: PREDICATE_IDS[key] });
}
```

### IPFS Enrichment Links (Optional, for Inline JSON Strategy)

If using inline JSON atoms, link each predicate to its rich IPFS enrichment document separately:

```typescript
for (const [key, def] of Object.entries(PREDICATE_DEFS)) {
  const ipfsDoc = buildPredicateIpfsDocument(def.name, { /* full i18n, Schema.org, etc */ });
  const cid = await ipfsClient.add(JSON.stringify(ipfsDoc));
  const uriAtomId = await createAtom(`ipfs://${cid}`);
  await createTriple({ subject: PREDICATE_IDS[key], predicate: HAS_SOURCE, object: uriAtomId });
}
```

Total on-chain: **~54 triples** (25 × 2 routing) + optionally **25 enrichment link triples**.

---

## Indexer Integration

The indexer handles both strategies.

### Parsing

```rust
fn resolve_predicate(atom_data: &[u8]) -> PredicateInfo {
    let data_str = String::from_utf8_lossy(atom_data);

    // Strategy D: IPFS URI
    if data_str.starts_with("ipfs://") {
        let doc = ipfs_fetch(&data_str);
        return PredicateInfo::from_ipfs_doc(doc);
    }

    // Strategy C: Inline JSON
    if let Ok(json) = serde_json::from_slice::<Value>(atom_data) {
        if json.get("@type").and_then(|t| t.as_str()) == Some("DefinedTerm") {
            return PredicateInfo::from_defined_term(json);
        }
    }

    // Fallback: plain string (legacy or custom predicate)
    PredicateInfo::from_string(data_str)
}
```

### Distinguishing Predicates from Tags

Both predicates and tags can be `@type: DefinedTerm`. The indexer distinguishes them by:

1. **Registry membership:** `(predicate_registry, contain, X)` → X is an enshrined predicate
2. **Positional context:** An atom in the predicate position of a triple is being used as a predicate

---

## Description Guidelines

For inline JSON atoms, the `description` field is frozen on-chain. For IPFS atoms, the description is frozen in the content-addressed document (but a new version can be linked).

### Rules

| Rule | Example |
|---|---|
| One sentence, present tense | "Directional subscription or tracking of the object entity" |
| Describe the relationship, not the UI | Not "When the user clicks follow" |
| No examples in the description | Examples go in documentation |
| No implementation details | Not "Creates a depositional vault" |
| Neutral voice | Not "I follow..." — describe the relationship abstractly |
| Under 120 characters | Short enough to display; long enough to be clear |

---

## Checklist

For each enshrined predicate:

- [ ] `name` follows naming conventions (base form, lowercase, US English)
- [ ] `description` follows description guidelines (one sentence, under 120 chars)
- [ ] Atom created using chosen strategy (inline JSON or IPFS URI)
- [ ] Atom ID matches across SDK, contract, and on-chain state
- [ ] Market routing triple created (`has type` → depositional/attributive/comparative)
- [ ] Registry membership triple created (`predicate_registry, contain, atom`)
- [ ] SDK constant exported
- [ ] Conjugation entry added to rendering helper
- [ ] IPFS enrichment document created (if using inline strategy, link via `has source`)
- [ ] IPFS document pinned on reliable infrastructure (if using IPFS strategy)

Global:

- [ ] `I` atom created and verified
- [ ] `predicate_registry` atom created
- [ ] `depositional`, `attributive`, `comparative` marker atoms created
- [ ] All atom IDs committed to a verification file in this repository
- [ ] Strategy decision documented (inline, IPFS, or hybrid)

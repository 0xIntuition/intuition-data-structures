# Predicate SDK Implementation Guide

## Overview

This document specifies how to implement enshrined predicates in the Intuition SDK using **Schema.org DefinedTerm atoms** — the same typed JSON-LD classification pattern used for every other atom type in the protocol (Person, Organization, DefinedTerm tags, etc.).

This is a first release, not a migration. No predicates are live on-chain yet. We are building this correctly from the start.

---

## Design Decision

Predicate atoms use the **DefinedTerm classification** that already exists in `classifications/defined-term/index.md`. A predicate IS a DefinedTerm — a named, described concept with a canonical definition.

The atom data for a predicate follows the same structure as any other classified atom:

```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "follow",
  "description": "Directional subscription or tracking of the object entity"
}
```

This means:
- Predicates are **architecturally consistent** with Person, Organization, and tag atoms
- Predicates are **self-describing** — any JSON-LD consumer can read them
- The classification pipeline, indexer, and API already handle DefinedTerm atoms
- No special-casing needed

See `predicate-architecture-first-principles.md` for the full rationale.

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

See `predicate-display-and-conjugation.md` for how the display layer handles conjugation (`follow` → `follows` when subject is singular).

---

## SDK Implementation

### 1. Canonical JSON Serialization

The SDK must produce **identical JSON bytes** for the same predicate across all environments. This is critical because the atom ID is derived from the exact bytes of the atom data.

```typescript
/**
 * Produces canonical JSON for a DefinedTerm predicate atom.
 * Key order, whitespace, and encoding are deterministic.
 *
 * Keys are always in this order: @context, @type, name, description
 * No trailing whitespace, no pretty-printing, no BOM.
 */
function createPredicateAtomData(name: string, description: string): string {
  // Canonical key order — never rearrange
  return JSON.stringify({
    "@context": "https://schema.org/",
    "@type": "DefinedTerm",
    "name": name,
    "description": description
  });
}

// Example output (one line, deterministic):
// {"@context":"https://schema.org/","@type":"DefinedTerm","name":"follow","description":"Directional subscription or tracking of the object entity"}
```

**This is the same serialization approach used for Person, Organization, and all other classified atoms.** The canonical JSON serializer is already in the codebase.

### 2. Predicate Constants

The SDK exports pre-computed atom data and atom IDs for all enshrined predicates.

```typescript
// --- Predicate atom data (canonical JSON) ---

export const PREDICATES = {
  // Curation and Collection
  contain: createPredicateAtomData(
    "contain",
    "The subject collection includes the object as a member or entry"
  ),
  curatedBy: createPredicateAtomData(
    "curated by",
    "The subject collection is maintained and organized by the object actor"
  ),

  // Identity and Classification
  is: createPredicateAtomData(
    "is",
    "Asserts identity, type membership, or definitional equivalence between subject and object"
  ),
  hasTag: createPredicateAtomData(
    "has tag",
    "Assigns a reusable tag or keyword to the subject for filtering and discovery"
  ),
  hasType: createPredicateAtomData(
    "has type",
    "Classifies the subject under a formal type or category"
  ),

  // Social
  follow: createPredicateAtomData(
    "follow",
    "Directional subscription or tracking of the object entity"
  ),
  like: createPredicateAtomData(
    "like",
    "Lightweight positive signal toward the object entity"
  ),

  // Metadata
  hasDescription: createPredicateAtomData(
    "has description",
    "Attaches a textual description to the subject entity"
  ),
  url: createPredicateAtomData(
    "url",
    "Links the subject to its canonical web address"
  ),
  imgUrl: createPredicateAtomData(
    "imgUrl",
    "Links the subject to an image URL for visual representation"
  ),

  // Trust and Reputation
  trust: createPredicateAtomData(
    "trust",
    "Positive trust assertion toward the object entity"
  ),
  distrust: createPredicateAtomData(
    "distrust",
    "Negative trust assertion toward the object entity"
  ),
  endorse: createPredicateAtomData(
    "endorse",
    "Strong public support or vouching for the object's quality or legitimacy"
  ),
  recommend: createPredicateAtomData(
    "recommend",
    "Active recommendation of the object to others in the ecosystem"
  ),
  vouchFor: createPredicateAtomData(
    "vouch for",
    "Personal credibility stake on the object's identity, quality, or claims"
  ),

  // Sentiment and Opinion
  agreeWith: createPredicateAtomData(
    "agree with",
    "Alignment with the object position, claim, or proposal"
  ),
  disagreeWith: createPredicateAtomData(
    "disagree with",
    "Opposition to the object position, claim, or proposal"
  ),
  bullishOn: createPredicateAtomData(
    "bullish on",
    "Positive conviction about the object's future value or success"
  ),
  bearishOn: createPredicateAtomData(
    "bearish on",
    "Negative conviction about the object's future value or success"
  ),

  // Attribution
  createdBy: createPredicateAtomData(
    "created by",
    "The subject was originally created or brought into existence by the object actor"
  ),
  sameAs: createPredicateAtomData(
    "same as",
    "The subject and object refer to the same real-world entity across different representations"
  ),
  linkedAccount: createPredicateAtomData(
    "linked account",
    "Connects the subject identity to one of its platform account atoms"
  ),
  hasCategory: createPredicateAtomData(
    "has category",
    "Places the subject within a broader categorical grouping"
  ),

  // Comparison
  betterThan: createPredicateAtomData(
    "better than",
    "Subjective superiority claim: the subject is asserted as better than the object"
  ),
  alternativeTo: createPredicateAtomData(
    "alternative to",
    "The subject can serve as a substitute or competing option for the object"
  ),
} as const;

// --- Pre-computed atom IDs ---

export const PREDICATE_IDS = Object.fromEntries(
  Object.entries(PREDICATES).map(([key, atomData]) => [
    key,
    calculateAtomId(atomData) // keccak256(ATOM_SALT, keccak256(toHex(atomData)))
  ])
) as Record<keyof typeof PREDICATES, string>;

// --- Convenience exports ---

export const FOLLOW = PREDICATE_IDS.follow;
export const LIKE = PREDICATE_IDS.like;
export const TRUST = PREDICATE_IDS.trust;
export const CONTAIN = PREDICATE_IDS.contain;
// ... etc
```

### 3. The `I` Atom

The `I` atom is a special case. It is a plain string atom (not a DefinedTerm) because it's not a predicate — it's a subject identifier.

```typescript
export const I_SUBJECT_DATA = 'I';
export const I_SUBJECT_ID = calculateAtomId(I_SUBJECT_DATA);
```

The `I` atom remains a plain string. Only predicates are DefinedTerm atoms. This is consistent: `I` is not a classified entity — it's a protocol-level identifier that resolves to the depositor's address.

### 4. Creating Triples

The developer-facing API for creating triples doesn't change. The SDK resolves predicate constants to atom IDs internally.

```typescript
// Depositional triple: user follows Vitalik
await createTriple({
  subject: I_SUBJECT_ID,
  predicate: FOLLOW,             // resolves to the DefinedTerm atom ID
  object: vitalikAtomId,
  deposit: parseEther('0.1'),
});

// Attributive triple: fact about Ethereum
await createTriple({
  subject: ethereumAtomId,
  predicate: PREDICATE_IDS.createdBy,
  object: vitalikAtomId,
  deposit: parseEther('0.01'),
});

// Stack curation: adding an item
await createTriple({
  subject: defiStackAtomId,
  predicate: CONTAIN,
  object: aaveAtomId,
  deposit: parseEther('0.01'),
});
```

### 5. Reading Predicate Atom Data

When reading triples from the indexer, predicate atoms are DefinedTerm JSON objects — the same shape as any other classified atom.

```typescript
const triple = await getTriple(tripleId);

// triple.predicate.atomData is a JSON string:
// {"@context":"https://schema.org/","@type":"DefinedTerm","name":"follow","description":"..."}

const predicateData = JSON.parse(triple.predicate.atomData);
console.log(predicateData.name);        // "follow"
console.log(predicateData.description); // "Directional subscription or tracking..."
console.log(predicateData["@type"]);    // "DefinedTerm"
```

This is identical to how you'd read a Person or Organization atom. No special-casing needed.

---

## On-Chain Bootstrap

### Atoms to Create

For each enshrined predicate, create one atom using the canonical JSON atom data.

```typescript
// Bootstrap script
for (const [key, atomData] of Object.entries(PREDICATES)) {
  const atomId = await createAtom(atomData);
  console.log(`${key}: ${atomId}`);
}

// Also create the I atom
const iAtomId = await createAtom(I_SUBJECT_DATA);
console.log(`I: ${iAtomId}`);
```

Total: **26 atoms** (25 predicates + 1 `I` atom).

### Market Routing Triples

After atoms are created, create 2 triples per predicate to encode market pattern and registry membership.

```typescript
// Market pattern atoms (create these first)
const depositionalAtomData = createPredicateAtomData(
  "depositional",
  "Market pattern where depositors make first-person claims via the I-subject"
);
const attributiveAtomData = createPredicateAtomData(
  "attributive",
  "Market pattern where the triple asserts a fact about a specific entity"
);
const comparativeAtomData = createPredicateAtomData(
  "comparative",
  "Market pattern where the triple compares two entities"
);

const depositionalId = await createAtom(depositionalAtomData);
const attributiveId = await createAtom(attributiveAtomData);
const comparativeId = await createAtom(comparativeAtomData);

// Predicate registry atom
const registryAtomData = createPredicateAtomData(
  "predicate registry",
  "Canonical registry of enshrined Intuition predicates"
);
const registryId = await createAtom(registryAtomData);

// Create routing triples
// Depositional predicates
for (const key of ['follow','like','trust','distrust','endorse','recommend',
                    'vouchFor','agreeWith','disagreeWith','bullishOn','bearishOn']) {
  await createTriple({ subject: PREDICATE_IDS[key], predicate: HAS_TYPE, object: depositionalId });
  await createTriple({ subject: registryId, predicate: CONTAIN, object: PREDICATE_IDS[key] });
}

// Attributive predicates
for (const key of ['contain','curatedBy','is','hasTag','hasType','hasDescription',
                    'url','imgUrl','createdBy','sameAs','linkedAccount','hasCategory']) {
  await createTriple({ subject: PREDICATE_IDS[key], predicate: HAS_TYPE, object: attributiveId });
  await createTriple({ subject: registryId, predicate: CONTAIN, object: PREDICATE_IDS[key] });
}

// Comparative predicates
for (const key of ['betterThan','alternativeTo']) {
  await createTriple({ subject: PREDICATE_IDS[key], predicate: HAS_TYPE, object: comparativeId });
  await createTriple({ subject: registryId, predicate: CONTAIN, object: PREDICATE_IDS[key] });
}
```

Total: **~54 triples** (25 × 2 routing + 2 × 2 for meta-predicates).

---

## Indexer Integration

The indexer already handles DefinedTerm atoms. For predicate atoms specifically:

### Parsing

```rust
// The indexer already does this for all atoms:
fn classify_atom(atom_data: &[u8]) -> AtomType {
    if let Ok(json) = serde_json::from_slice::<Value>(atom_data) {
        match json.get("@type").and_then(|t| t.as_str()) {
            Some("Person") => AtomType::Person,
            Some("Organization") => AtomType::Organization,
            Some("DefinedTerm") => AtomType::DefinedTerm,
            // ... etc
        }
    }
    // ...
}

// No new code needed to recognize predicate atoms.
// They're DefinedTerm atoms that happen to appear in the predicate position of triples.
```

### Distinguishing Predicates from Tags

Both predicates and tags are `@type: DefinedTerm`. The indexer distinguishes them by:

1. **Registry membership:** `(predicate_registry, contain, X)` → X is an enshrined predicate
2. **Positional context:** An atom appearing in the predicate position of a triple is being used as a predicate, regardless of its classification type

### API Response

```json
{
  "triple": {
    "subject": {
      "atomId": "0x123...",
      "type": "Person",
      "data": {"@type": "Person", "givenName": "Vitalik", "familyName": "Buterin"}
    },
    "predicate": {
      "atomId": "0x456...",
      "type": "DefinedTerm",
      "data": {"@type": "DefinedTerm", "name": "follow", "description": "Directional subscription..."},
      "marketPattern": "depositional",
      "enshrined": true
    },
    "object": {
      "atomId": "0x789...",
      "type": "Organization",
      "data": {"@type": "Organization", "name": "Ethereum Foundation"}
    }
  }
}
```

The `marketPattern` and `enshrined` fields are derived from the on-chain routing triples. Everything else comes from the atom data itself.

---

## Atom ID Verification

Before launch, verify that every predicate atom ID matches across SDK, contract, and on-chain state.

```typescript
// Verification script
for (const [key, atomData] of Object.entries(PREDICATES)) {
  const sdkId = calculateAtomId(atomData);
  const onChainId = await contract.computeAtomId(atomData);
  const createdId = await getAtomIdByData(atomData);

  assert(sdkId === onChainId, `SDK/contract mismatch for ${key}`);
  assert(sdkId === createdId, `SDK/on-chain mismatch for ${key}`);

  // Also verify the atom data is valid JSON
  const parsed = JSON.parse(atomData);
  assert(parsed["@type"] === "DefinedTerm");
  assert(parsed["name"] === PREDICATE_NAMES[key]);
}
```

---

## Description Guidelines

The `description` field in the atom data is **frozen on-chain**. Write it carefully.

### Rules

| Rule | Example |
|---|---|
| One sentence, present tense | "Directional subscription or tracking of the object entity" |
| Describe the relationship, not the UI | Not "When the user clicks follow" |
| No examples in the description | Examples go in documentation, not atom data |
| No implementation details | Not "Creates a depositional vault" |
| Neutral voice | Not "I follow..." — describe the relationship abstractly |
| Under 120 characters | Short enough to display; long enough to be clear |

### Extended Documentation

The frozen description is a baseline — enough for a developer reading chain data to understand the predicate. Extended documentation (examples, usage constraints, anti-patterns) belongs in:

1. **IPFS-hosted enrichment** (linked via `(predicate_atom, has source, ipfs://...)` triple)
2. **This repository's documentation files**
3. **API metadata responses**

---

## Checklist

For each enshrined predicate:

- [ ] `name` follows naming conventions (base form, lowercase, US English)
- [ ] `description` follows description guidelines (one sentence, under 120 chars)
- [ ] Canonical JSON serialization verified (key order: `@context`, `@type`, `name`, `description`)
- [ ] Atom ID matches across SDK, contract, and on-chain state
- [ ] Market routing triple created (`has type` → depositional/attributive/comparative)
- [ ] Registry membership triple created (`predicate_registry, contain, atom`)
- [ ] SDK constant exported
- [ ] Conjugation entry added to rendering helper (see `predicate-display-and-conjugation.md`)
- [ ] i18n entry added for English (see `predicate-i18n-strategy.md`)

Global:

- [ ] `I` atom created and verified
- [ ] `predicate_registry` atom created
- [ ] `depositional`, `attributive`, `comparative` marker atoms created
- [ ] All atom IDs committed to a verification file in this repository

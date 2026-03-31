# Predicate Display and Conjugation

## The Problem

Intuition predicates are on-chain atoms represented as Schema.org **DefinedTerm** JSON-LD documents. Each predicate's `name` field contains the canonical English base-form verb or phrase. The atom ID is derived from the full JSON document via `keccak256(ATOM_SALT, keccak256(data))`, making the predicate's on-chain identity permanent.

English verb conjugation creates a tension:

| Triple | Reads as | Grammatically correct? |
|---|---|---|
| `(I, follow, Vitalik)` | "I follow Vitalik" | Yes |
| `(Alice, follow, Vitalik)` | "Alice follow Vitalik" | No — should be "follows" |
| `(I, follows, Vitalik)` | "I follows Vitalik" | No — should be "follow" |
| `(Alice, follows, Vitalik)` | "Alice follows Vitalik" | Yes |

A predicate with `"name": "follow"` and a predicate with `"name": "follows"` produce different DefinedTerm JSON, different atom IDs, different vaults, and different markets. Using both fragments the graph.

This has real consequences:

1. **Market fragmentation.** Two predicate atoms for the same concept split every triple, vault, and deposit across two parallel markets.
2. **Permanence.** Once atoms exist on-chain, they cannot be merged.
3. **Partner divergence.** Without a clear rule, integration partners make different choices and the ecosystem fragments silently.

---

## The Options

### Option 1: Base Form (`follow`, `like`, `trust`)

Use the infinitive/base form — the verb form that works with "I".

| Context | Rendered | Grammar |
|---|---|---|
| `(I, follow, Vitalik)` | "I follow Vitalik" | Correct |
| `(Alice, follow, Vitalik)` | "Alice follow Vitalik" | Incorrect in English prose |
| `(developers, follow, Vitalik)` | "developers follow Vitalik" | Correct (plural subject) |

**Pros:**
- Works with `I`, which is the primary subject for depositional triples (the most common pattern)
- Works with plural subjects (`developers`, `auditors`, community-scoped subjects)
- Standard in ontology design — RDF, Wikidata, OWL all use base form for predicates

**Cons:**
- Reads awkwardly when a named third-person entity is the subject

### Option 2: Third-Person (`follows`, `likes`, `trusts`)

Use the conjugated form that works with named singular subjects.

| Context | Rendered | Grammar |
|---|---|---|
| `(I, follows, Vitalik)` | "I follows Vitalik" | Incorrect |
| `(Alice, follows, Vitalik)` | "Alice follows Vitalik" | Correct |
| `(developers, follows, Vitalik)` | "developers follows Vitalik" | Incorrect |

**Pros:**
- Reads naturally for attributive triples with named subjects

**Cons:**
- Grammatically wrong with `I` — the canonical subject for all depositional triples
- Grammatically wrong with plural subjects
- Goes against ontology conventions

### Option 3: Present Participle (`following`, `liking`, `trusting`)

| Context | Rendered | Grammar |
|---|---|---|
| `(I, following, Vitalik)` | "I following Vitalik" | Incorrect (missing "am") |
| `(Alice, following, Vitalik)` | "Alice following Vitalik" | Incorrect (missing "is") |

Grammatically wrong in both contexts. Non-standard in ontology design.

### Option 4: Relationship Noun (`follower-of`, `trust-in`)

| Context | Rendered | Grammar |
|---|---|---|
| `(I, follower-of, Vitalik)` | "I follower-of Vitalik" | Not a sentence |

Loses natural language readability — a core Intuition design goal.

### Option 5: Both Forms, Accept Fragmentation

Create both `follow` and `follows` as atoms. This fatally fragments the market — `(I, follow, Vitalik)` and `(Alice, follows, Vitalik)` are on different predicate atoms with different vaults. Defeats the purpose of canonical predicates.

---

## The Decision: Base Form

**Intuition uses base form (`follow`, `like`, `trust`, `endorse`, etc.) as the canonical `name` in the DefinedTerm predicate atom.**

The reasoning:

1. **`I` is the primary subject.** The depositional pattern (`I` as subject) is the most common and most important triple type. Base form is grammatically correct with `I`.

2. **Plural subjects work.** Community-scoped subjects like `developers`, `auditors`, `Ethereum community` are correct with base form.

3. **Ontology convention.** Every major knowledge graph system (RDF, Wikidata, OWL, SKOS) uses base form for predicates.

4. **One form, one atom, one market.** One canonical DefinedTerm means one atom ID means one vault per (subject, predicate, object) combination.

5. **Display is a separate concern.** The atom's `name` field is not what end users see. The display layer handles conjugation.

### Trade-Offs Accepted

- **Raw atom data reads awkwardly for third-person subjects.** A developer reading the DefinedTerm sees `"name": "follow"`, so triples with named subjects read as "Alice follow Bob." This is cosmetic, not functional.
- **Non-verb predicates are unaffected.** Predicates like `better than`, `bullish on`, `member of` do not conjugate. The tension only affects active-voice single verbs — roughly 22 out of 100 cataloged predicates.

---

## The Display Layer

The on-chain DefinedTerm stores the base form. The user sees the grammatically correct form. The display layer bridges the two.

### Rendering Rules

```
Given a triple (Subject, Predicate, Object):

1. If Subject = "I" (the I-atom)
   → render predicate name as base form
   → "I follow Vitalik"

2. If Subject = a singular named entity (Person, Organization, etc.)
   → render predicate name as third-person form
   → "Alice follows Vitalik"

3. If Subject = a plural or collective entity
   → render predicate name as base form
   → "developers follow Vitalik"

4. If predicate does not conjugate (phrase, adjective, passive construction)
   → render as-is regardless of subject
   → "Alice better than Bob", "I bullish on Ethereum"
```

### How It Works with DefinedTerm Atoms

Predicate atoms are DefinedTerm JSON-LD documents. The `name` field contains the base form. The SDK's predicate definitions include the conjugation metadata:

```typescript
// From PREDICATE_DEFS in the SDK (see 6.predicate-sdk-implementation-guide.md)
{
  follow: {
    name: "follow",           // base form — stored in the DefinedTerm atom
    description: "Directional subscription or tracking of the object entity",
    marketPattern: "depositional",
    conjugates: true,
    thirdPerson: "follows",   // display form for singular subjects
  },
  bullishOn: {
    name: "bullish on",
    description: "Positive conviction about the object's future value or success",
    marketPattern: "depositional",
    conjugates: false,         // no conjugation needed
  },
}
```

The `conjugates` and `thirdPerson` fields are display metadata. They are never stored in the on-chain atom — they exist in the SDK predicate definitions and in the IPFS enrichment documents (see `7.predicate-i18n-strategy.md`).

### SDK Rendering Function

The rendering function takes a predicate's `name` (extracted from the DefinedTerm atom data or from the SDK predicate definitions) and the subject context, and returns the display form:

```typescript
import { PREDICATE_DEFS } from '@intuition/predicates';

type SubjectContext = 'first-person' | 'singular' | 'plural';

/**
 * Renders the display form of a predicate name based on subject context.
 *
 * The predicate name is the base form from the DefinedTerm's `name` field.
 * This function conjugates it for display when the subject is a singular
 * named entity (e.g., "Alice follows Vitalik" instead of "Alice follow Vitalik").
 */
export function renderPredicate(
  predicateName: string,
  subjectContext: SubjectContext
): string {
  // Look up conjugation metadata from predicate definitions
  const def = Object.values(PREDICATE_DEFS)
    .find(d => d.name === predicateName);

  if (def?.conjugates && def.thirdPerson && subjectContext === 'singular') {
    return def.thirdPerson;
  }

  return predicateName; // base form for first-person, plural, or non-conjugating
}
```

Usage with DefinedTerm atom data:

```typescript
// Reading a triple from the indexer
const triple = await getTriple(tripleId);

// The predicate atom data is a DefinedTerm JSON document
const predicateData = JSON.parse(triple.predicate.atomData);
const predicateName = predicateData.name; // "follow"

// Determine subject context
const subjectContext = triple.subject.atomData === 'I'
  ? 'first-person'
  : 'singular'; // simplified — real logic checks for plural subjects too

// Render for display
const displayForm = renderPredicate(predicateName, subjectContext);
// "follow" for I-subject, "follows" for singular named subjects
```

### Rendering with IPFS Enrichment (i18n)

When the predicate has an IPFS enrichment document (linked via a `has source` triple or used directly as the atom data in the IPFS URI strategy), the enrichment contains conjugation and localization data for multiple languages:

```typescript
import { resolvePredicateEnrichment } from '@intuition/predicate-i18n';

/**
 * Renders a predicate for a specific locale and subject context.
 * Resolves the IPFS enrichment document for i18n labels.
 * Falls back to the DefinedTerm name if enrichment is unavailable.
 */
async function renderPredicateLocalized(
  predicateName: string,
  locale: string,
  subjectContext: SubjectContext
): Promise<string> {
  // Try to resolve i18n from IPFS enrichment
  const enrichment = await resolvePredicateEnrichment(predicateName);

  if (enrichment?.i18n?.[locale]) {
    const labels = enrichment.i18n[locale];
    if (subjectContext === 'singular' && labels.thirdPerson) {
      return labels.thirdPerson;
    }
    return labels.base;
  }

  // Fallback: English conjugation from SDK definitions
  return renderPredicate(predicateName, subjectContext);
}

// Examples:
await renderPredicateLocalized('follow', 'en', 'first-person');
// → "follow"

await renderPredicateLocalized('follow', 'en', 'singular');
// → "follows"

await renderPredicateLocalized('follow', 'fr', 'singular');
// → "suit" (from IPFS enrichment)

await renderPredicateLocalized('follow', 'ja', 'first-person');
// → "フォローする" (from IPFS enrichment)

await renderPredicateLocalized('bullish on', 'en', 'singular');
// → "bullish on" (does not conjugate)
```

See `6.predicate-sdk-implementation-guide.md` for the full SDK architecture and `7.predicate-i18n-strategy.md` for the IPFS enrichment document format.

### Rendering Pipeline

```
On-chain DefinedTerm atom: { "name": "follow", ... }
     ↓
Extract predicate name: "follow"
     ↓
Subject context: first-person / singular / plural
     ↓
Conjugation: "follow" → "follows" (if singular + conjugates)
     ↓
Localization: "follows" → "suit" (if locale = French, from IPFS enrichment)
     ↓
Display: "Alice suit Vitalik"
```

Each step is independent. Conjugation works without localization (English fallback). Localization works without conjugation (languages that don't conjugate for person). The pipeline degrades gracefully at every level.

---

## Affected Predicates

These predicates have verb forms that conjugate differently between base and third-person:

| Base Form (in DefinedTerm `name`) | Third-Person (display only) |
|---|---|
| `follow` | `follows` |
| `like` | `likes` |
| `endorse` | `endorses` |
| `trust` | `trusts` |
| `distrust` | `distrusts` |
| `vouch for` | `vouches for` |
| `block` | `blocks` |
| `report` | `reports` |
| `agree with` | `agrees with` |
| `disagree with` | `disagrees with` |
| `support` | `supports` |
| `oppose` | `opposes` |
| `contain` | `contains` |
| `use` | `uses` |
| `implement` | `implements` |
| `compete with` | `competes with` |
| `depend on` | `depends on` |
| `outperform` | `outperforms` |
| `supersede` | `supersedes` |
| `speak` | `speaks` |
| `teach` | `teaches` |
| `reward` | `rewards` |

These predicates are multi-word phrases, adjective-based, or passive constructions and do **not** conjugate:

| Predicate | Why it doesn't conjugate |
|---|---|
| `is` | Already works as both base and third-person |
| `has type`, `has tag`, `has category`, `has description`, `has source` | Fixed compound labels (see `has` anomaly below) |
| `same as`, `alias of`, `instance of`, `subclass of`, `member of` | Noun/adjective phrases |
| `employed by`, `affiliated with`, `created by`, `authored by` | Past participle — doesn't change |
| `audited by`, `verified by`, `certified by`, `governed by`, `regulated by` | Past participle |
| `backed by`, `pegged to`, `listed on`, `available on` | Past participle / adjective phrase |
| `compatible with`, `compliant with` | Adjective phrase |
| `better than`, `worse than`, `equivalent to`, `alternative to` | Adjective/noun phrase |
| `bullish on`, `bearish on`, `skeptical of`, `neutral on` | Adjective phrase |
| `expert in`, `linked account`, `predecessor of`, `successor of` | Noun phrase |
| `student of`, `mentor of`, `partner of` | Noun phrase |

The majority of predicates do not conjugate at all. The conjugation problem only affects active-voice single verbs — roughly 22 out of 100 cataloged predicates.

---

## The `has` Anomaly

The predicates `has type`, `has tag`, `has category`, `has description`, and `has source` use third-person `has` rather than base form `have`. This is an exception to the base-form rule.

Why keep `has` instead of `have type`, `have tag`, etc.:
- These predicates are almost exclusively used in attributive triples with named singular subjects: `(Ethereum, has type, blockchain)`. They are rarely used with `I`.
- `have type`, `have tag` would read oddly even with `I`: "I have type researcher" is not natural.
- These function as fixed compound labels, not as conjugating verbs — `has tag` reads as a single unit.

**Decision:** Keep `has X` predicates as-is. They are treated as non-conjugating compound labels in the DefinedTerm `name` field and in the rendering function (`conjugates: false`).

---

## Summary

| Principle | Rule |
|---|---|
| On-chain form | DefinedTerm `name` uses base form (`follow`, not `follows`) |
| Why | Works with `I` (primary subject), plural subjects, and ontology standards |
| Exception | `has X` predicates stay as-is (compound labels, `conjugates: false`) |
| Display | UI conjugates to third-person when subject is a singular named entity |
| SDK | `PREDICATE_DEFS` carries `conjugates` + `thirdPerson` metadata; `renderPredicate()` handles display |
| i18n | IPFS enrichment documents carry per-locale conjugation forms; `renderPredicateLocalized()` resolves them |
| Trade-off accepted | Raw DefinedTerm `name` reads awkwardly for `(Alice, follow, Bob)` — cosmetic, not functional |
| Further reading | `6.predicate-sdk-implementation-guide.md` for full SDK architecture; `7.predicate-i18n-strategy.md` for IPFS enrichment format |

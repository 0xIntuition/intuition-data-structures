# Predicate Internationalization Strategy

## Principle

**On-chain predicate atoms are DefinedTerm objects with an English canonical name. Localization data is a Schema.org object stored on IPFS and linked to the predicate atom via an `ipfs://` URI atom.**

This keeps the protocol layer language-neutral while making localization data content-addressed, decentralized, and verifiable — without fragmenting markets.

---

## Architecture

### Layer 1: On-Chain Predicate Atom (English, Immutable)

Every predicate atom is a DefinedTerm with an English `name` and `description`:

```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "follow",
  "description": "Directional subscription or tracking of the object entity"
}
```

This produces a deterministic atom ID. All users, all languages, all frontends use this same atom. There is one `follow` predicate, one vault per `(I, follow, Object)` triple, one unified market.

The English name is a **protocol identifier**, not a user-facing label. Users see localized labels resolved from the IPFS enrichment layer.

### Layer 2: IPFS Enrichment Object (Multilingual, Content-Addressed)

For each predicate, a **Schema.org DefinedTerm enrichment object** is created containing all localization data, conjugation rules, and extended metadata. This object is stored on IPFS.

#### IPFS Object Format

```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "follow",
  "description": "Directional subscription or tracking of the object entity",
  "inDefinedTermSet": "https://intuition.systems/predicates",
  "sameAs": ["https://schema.org/FollowAction"],
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
    }
  ],
  "alternateName": [
    {"@language": "en", "@value": "follow"},
    {"@language": "es", "@value": "seguir"},
    {"@language": "fr", "@value": "suivre"},
    {"@language": "de", "@value": "folgen"},
    {"@language": "ja", "@value": "フォローする"},
    {"@language": "zh", "@value": "关注"},
    {"@language": "ko", "@value": "팔로우하다"},
    {"@language": "ar", "@value": "متابعة"}
  ],
  "subjectOf": {
    "@type": "PropertyValue",
    "name": "i18n",
    "value": {
      "en": {
        "base": "follow",
        "thirdPerson": "follows",
        "pastParticiple": "followed",
        "displayName": "Follow"
      },
      "es": {
        "base": "seguir",
        "thirdPerson": "sigue",
        "pastParticiple": "seguido",
        "displayName": "Seguir"
      },
      "fr": {
        "base": "suivre",
        "thirdPerson": "suit",
        "pastParticiple": "suivi",
        "displayName": "Suivre"
      },
      "de": {
        "base": "folgen",
        "thirdPerson": "folgt",
        "pastParticiple": "gefolgt",
        "displayName": "Folgen"
      },
      "ja": {
        "base": "フォローする",
        "thirdPerson": "フォローする",
        "pastParticiple": "フォローした",
        "displayName": "フォロー"
      },
      "zh": {
        "base": "关注",
        "thirdPerson": "关注",
        "pastParticiple": "关注了",
        "displayName": "关注"
      },
      "ko": {
        "base": "팔로우하다",
        "thirdPerson": "팔로우하다",
        "pastParticiple": "팔로우한",
        "displayName": "팔로우"
      },
      "ar": {
        "base": "متابعة",
        "thirdPerson": "يتابع",
        "pastParticiple": "تابَعَ",
        "displayName": "متابعة",
        "direction": "rtl"
      }
    }
  }
}
```

This object is uploaded to IPFS, producing a content-addressed CID:

```
ipfs://bafyreif7x...   (the CID of the follow enrichment object)
```

#### Why Schema.org on IPFS

| Property | Benefit |
|---|---|
| Content-addressed | Same object always produces the same CID; verifiable |
| Decentralized | Any IPFS node can serve the content; no single point of failure |
| Schema.org compliant | Machine-readable by any JSON-LD consumer |
| Updateable without changing the predicate | New translations = new IPFS object = new CID, linked via a new triple. The predicate atom is unchanged. |
| Self-contained | One fetch gives you everything: description, Schema.org mapping, i18n labels, conjugation rules |

### Layer 3: On-Chain Link (Predicate Atom → IPFS URI Atom)

The IPFS CID is stored as an atom, and a triple links the predicate atom to it:

```
# Create an atom with the IPFS URI as its data
Atom data:   "ipfs://bafyreif7x..."
Atom ID:     keccak256(ATOM_SALT, keccak256(toHex("ipfs://bafyreif7x...")))

# Link the predicate atom to the IPFS enrichment
Triple:      (follow_atom, has source, ipfs_uri_atom)
```

This triple is created by the authority wallet (the same wallet that creates the market routing triples).

**The on-chain link chain:**

```
follow_atom  (on-chain DefinedTerm JSON)
    │
    ├── (follow_atom, has type, depositional_atom)      ← market routing
    ├── (predicate_registry, contain, follow_atom)       ← registry membership
    └── (follow_atom, has source, ipfs://bafyreif7x...)  ← enrichment with i18n
```

A developer can now:
1. Read the atom data → understand the predicate (English name + description)
2. Read the `has type` triple → know the market pattern
3. Read the `has source` triple → fetch the IPFS enrichment for i18n, Schema.org mapping, conjugation rules

### Layer 4: SDK Rendering API

The SDK fetches and caches the IPFS enrichment objects, then provides a rendering function:

```typescript
import { resolvePredicateI18n } from '@intuition/predicate-i18n';

type SubjectContext = 'first-person' | 'singular' | 'plural';

interface RenderOptions {
  locale: string;          // BCP 47 language tag: "en", "fr", "ja", etc.
  subjectContext: SubjectContext;
  form?: 'base' | 'thirdPerson' | 'pastParticiple' | 'displayName';
}

/**
 * Renders a predicate label for the given locale and subject context.
 *
 * Resolution order:
 * 1. Check in-memory cache (populated from IPFS enrichment objects)
 * 2. Fetch from API (which caches IPFS content)
 * 3. Fetch directly from IPFS gateway
 * 4. Fall back to the predicate atom's English name
 */
async function renderPredicate(
  predicateAtomId: string,
  options: RenderOptions
): Promise<string> {
  const enrichment = await resolvePredicateI18n(predicateAtomId);

  if (!enrichment) {
    // Fallback: read the predicate atom data directly
    const atomData = await getAtomData(predicateAtomId);
    const parsed = JSON.parse(atomData);
    return parsed.name; // English name from atom data
  }

  const localeLabels = enrichment.i18n[options.locale]
    || enrichment.i18n['en']; // fallback to English

  if (options.form === 'displayName') {
    return localeLabels.displayName;
  }

  if (enrichment.conjugates && options.subjectContext === 'singular') {
    return localeLabels.thirdPerson || localeLabels.base;
  }

  return localeLabels.base;
}

// Examples:
await renderPredicate(FOLLOW, { locale: 'en', subjectContext: 'first-person' });
// → "follow"  (for "I follow Vitalik")

await renderPredicate(FOLLOW, { locale: 'en', subjectContext: 'singular' });
// → "follows"  (for "Alice follows Vitalik")

await renderPredicate(FOLLOW, { locale: 'fr', subjectContext: 'singular' });
// → "suit"  (for "Alice suit Vitalik")

await renderPredicate(FOLLOW, { locale: 'ja', subjectContext: 'first-person' });
// → "フォローする"

await renderPredicate(FOLLOW, { locale: 'en', subjectContext: 'first-person', form: 'displayName' });
// → "Follow"  (for button labels)
```

### Resolution Flow

```
User's locale: French
UI needs to render: (I, follow, Vitalik)

1. SDK looks up FOLLOW atom ID in cache
2. Cache has IPFS enrichment object (fetched on init or first use)
3. Read i18n.fr.base → "suivre"
4. Subject is I → use base form
5. Render: "Je suivre Vitalik" → display layer composes the full sentence

If cache is cold:
1. SDK reads (follow_atom, has source, ipfs://bafyreif7x...) triple from API
2. Fetches IPFS object via gateway
3. Caches it
4. Proceeds as above

If IPFS is unavailable:
1. SDK reads the on-chain atom data: {"name": "follow", "description": "..."}
2. Falls back to English name: "follow"
3. Renders: "I follow Vitalik" (English fallback)
```

---

## Updating Translations

Adding a new language or fixing a translation does NOT change the predicate atom. It creates a new IPFS enrichment object and updates the on-chain link:

```
1. Add Korean translations to the enrichment JSON
2. Upload to IPFS → new CID: ipfs://bafyreig9k...
3. Create new IPFS URI atom: "ipfs://bafyreig9k..."
4. Create new triple: (follow_atom, has source, ipfs://bafyreig9k...)
   (from authority wallet)

The old triple (follow_atom, has source, ipfs://bafyreif7x...) persists on-chain.
The indexer/API resolves to the latest by:
  - Most recent triple from authority wallet, OR
  - Explicit versioning: (new_ipfs_atom, supersede, old_ipfs_atom)
```

The predicate atom, its atom ID, and all existing triples using it are **completely unaffected**. No market fragmentation. No migration needed.

---

## IPFS Enrichment: Full Predicate Set

At launch, create one IPFS enrichment object per predicate, plus one **master index** object:

### Master Index

```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTermSet",
  "name": "Intuition Enshrined Predicates",
  "description": "Canonical predicate catalog for the Intuition protocol",
  "url": "https://intuition.systems/predicates",
  "hasDefinedTerm": [
    "ipfs://bafyreif7x...",
    "ipfs://bafyreig3m...",
    "ipfs://bafyreih8q..."
  ]
}
```

This index is itself stored on IPFS and linked on-chain:

```
(predicate_registry_atom, has source, ipfs://bafyrei_index...)
```

A developer can:
1. Read the `predicate_registry` atom
2. Follow the `has source` triple to the IPFS index
3. Fetch the index → get CIDs for all predicate enrichments
4. Fetch each enrichment → get full i18n data for every predicate

This is the fully decentralized path. No API, no SDK, just chain data + IPFS.

---

## Language-Specific Concerns

### CJK Languages (Chinese, Japanese, Korean)

- **No person-based conjugation.** Japanese verbs conjugate for tense and politeness but not for person. Chinese and Korean don't conjugate verbs for person at all. The `thirdPerson` field may be identical to `base` — this is correct.
- **Word boundaries.** CJK text doesn't use spaces between words. Triple rendering may need a different template: instead of `"Subject predicate Object"`, use `"Subject は Object を フォローする"` (Japanese) with particles.
- **Character width.** CJK characters are typically full-width. UI layouts should account for this in predicate dropdowns and labels.

### RTL Languages (Arabic, Hebrew)

- **Text direction.** Triple rendering must handle mixed directionality. The subject "Vitalik" (LTR) + predicate "يتابع" (RTL) + object "إيثيريوم" (RTL) requires bidi rendering.
- **Verb conjugation complexity.** Arabic conjugates for person, number, and gender. The enrichment's i18n block can include `masculineSingular`, `feminineSingular`, `plural` forms. The Schema.org object structure is flexible enough to accommodate this without changing the overall schema.

### Gendered Languages (French, German, Spanish)

- **Grammatical gender.** Some predicates may render differently based on the subject's gender. The i18n block can include gender-specific forms where needed. For most predicates the base form is sufficient.

### Agglutinative Languages (Turkish, Finnish, Korean)

- **Suffixing.** These languages build meaning through suffixes. The i18n block should include a `template` field for languages where word order differs:

```json
"tr": {
  "base": "takip etmek",
  "thirdPerson": "takip ediyor",
  "displayName": "Takip Et",
  "template": "{subject} {object}'ı {predicate}"
}
```

---

## What to Do at Launch

### Minimum Viable i18n

1. **English enrichment objects on IPFS.** Create IPFS enrichment objects for all 25 launch predicates with English labels and conjugation rules. Other languages can be empty or omitted.

2. **On-chain links.** Create `(predicate_atom, has source, ipfs://...)` triples for each predicate.

3. **SDK rendering function.** Ship `renderPredicate()` with IPFS enrichment resolution. English works from atom data fallback even without IPFS.

4. **Fallback chain.** If IPFS is unavailable, fall back to the English `name` from the on-chain atom data. Never show an error or empty string.

### Post-Launch Expansion

5. **Community translation contribution.** Open a translation process (Crowdin, Transifex, or GitHub PRs). When a language is added:
   - Update the IPFS enrichment object for affected predicates
   - Upload new version to IPFS
   - Create new `has source` triple on-chain

6. **Priority languages.** Spanish, French, Chinese, Japanese, Korean, Arabic, Portuguese, German — prioritize by user base.

7. **RTL and CJK templates.** Once translations exist for RTL and CJK languages, add `template` fields to the i18n block.

---

## What NOT to Do

| Anti-Pattern | Why It's Wrong |
|---|---|
| Create locale-specific predicate atoms | `follow` (EN), `suivre` (FR), `关注` (ZH) = three atoms, three markets, fragmented graph |
| Put translations in the on-chain atom data | Frozen and expensive; can't add languages later without new atom |
| Let builders translate predicates independently | Inconsistent translations across apps; user confusion |
| Hardcode English labels in frontends | Use the rendering function; labels come from IPFS enrichment |
| Require all translations before launch | English-first is fine; translations are additive via IPFS updates |
| Translate the `I` atom | `"I"`, `"Je"`, `"私"` would be different atoms. `I` is a protocol identifier. |
| Skip the IPFS link | The `(predicate, has source, ipfs://...)` triple is what makes i18n decentralized and verifiable |

---

## The Full Stack

```
┌──────────────────────────────────────────────────────────┐
│  Frontend: renderPredicate(atomId, locale, context)      │  ← what users see
├──────────────────────────────────────────────────────────┤
│  SDK cache: IPFS enrichment objects (fetched on init)    │  ← fast path
├──────────────────────────────────────────────────────────┤
│  API: reads IPFS enrichments, serves to frontends        │  ← convenience layer
├──────────────────────────────────────────────────────────┤
│  IPFS: Schema.org enrichment objects with i18n data      │  ← content-addressed truth
│        ipfs://bafyreif7x... (follow enrichment)          │
│        ipfs://bafyreig3m... (like enrichment)            │
├──────────────────────────────────────────────────────────┤
│  On-chain triple: (follow_atom, has source, ipfs://...)  │  ← verifiable link
├──────────────────────────────────────────────────────────┤
│  On-chain triple: (follow_atom, has type, depositional)  │  ← market routing
├──────────────────────────────────────────────────────────┤
│  On-chain atom: DefinedTerm { name, description }        │  ← identity + fallback
└──────────────────────────────────────────────────────────┘
```

Each layer serves a distinct purpose:
- **Atom data:** Identity and English fallback (immutable, always available)
- **On-chain triples:** Market routing and enrichment links (verifiable, updateable)
- **IPFS objects:** Rich metadata and i18n (decentralized, content-addressed, updateable)
- **API/SDK:** Convenience caching (fast, not the source of truth)
- **Frontend:** Rendering (locale-aware, context-aware)

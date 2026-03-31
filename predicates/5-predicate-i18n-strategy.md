# Predicate Internationalization Strategy

## Principle

**Predicate atoms remain canonical, English, and minimal. Multi-language support is layered on later through a progressive localization system with both on-chain attestations and off-chain locale bundles.**

This follows the architecture in [4.predicate-architecture-first-principles.md](./4.predicate-architecture-first-principles.md):

- The predicate atom is the immutable identity.
- The English `name` is the protocol identifier.
- The English `description` is the baseline semantic explanation.
- Market routing remains on-chain.
- Localization is additive interpretation, not identity.

The key consequence is:

**Adding French, Japanese, or Arabic support to `follow` must never create a new predicate atom.**

There is one canonical `follow` atom. Localization is a rendering layer over that atom.

---

## What Problem We Are Solving

At launch, predicates are created in English:

```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "follow",
  "description": "Directional subscription or tracking of the object entity"
}
```

Months later, we may want:

- French UI labels
- Japanese display text
- Arabic right-to-left rendering hints
- subject-sensitive conjugation
- language-specific sentence templates
- improved translations over time

We need a system that allows all of this **without changing the predicate atom ID** and without forcing all translation state to live in one place forever.

That means the i18n system has to satisfy four constraints:

1. **Canonical identity stays English and stable.**
2. **Translations can be added progressively after launch.**
3. **There is an on-chain representation that a translation exists and which source is canonical.**
4. **Frontends and SDKs can resolve localized labels deterministically with sensible fallback behavior.**

---

## The Proposed Model

Use a two-pronged system:

### 1. On-Chain: Localization Attestations

On-chain triples tell the ecosystem:

- which predicate is being localized
- which locale is available
- where the canonical localization payload lives
- which payload version is current

This gives us verifiability, indexability, and progressive upgrades without changing the predicate atom.

### 2. Off-Chain: Locale Bundles

The actual translation payload lives off-chain as structured JSON.

This payload can be stored:

- on IPFS, or
- in another content-addressed artifact system, or
- in a future fully on-chain representation if we ever decide some fields are worth the cost

The important point is that **the protocol-facing shape stays the same**: on-chain triples point to the currently canonical localization bundle for a predicate and locale.

This means we do not hardcode the i18n strategy to "one IPFS document forever." We define a **localization reference layer** that can point to IPFS today and evolve later without redesigning the SDK.

---

## Architecture

### Layer 1: Canonical Predicate Atom

The predicate atom remains the minimal identity document:

```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "follow",
  "description": "Directional subscription or tracking of the object entity"
}
```

This is the only identity-bearing predicate object.

What it does:

- anchors the atom ID
- provides the canonical English identifier
- provides an always-available English fallback
- prevents market fragmentation

What it does not do:

- store all translations
- store locale-specific display forms
- store mutable rendering templates

### Layer 2: On-Chain Localization Registry

Introduce a small set of canonical triples for localization.

Conceptually:

```text
(predicate_atom, has localization, localization_entry_atom)
(localization_entry_atom, has locale, locale_atom)
(localization_entry_atom, has source, source_atom)
(localization_entry_atom, has status, active_atom)
```

For example:

```text
(follow_atom, has localization, follow_fr_entry)
(follow_fr_entry, has locale, fr_atom)
(follow_fr_entry, has source, ipfs://bafy...fr-follow-v1)
(follow_fr_entry, has status, active)

(follow_atom, has localization, follow_ja_entry)
(follow_ja_entry, has locale, ja_atom)
(follow_ja_entry, has source, ipfs://bafy...ja-follow-v1)
(follow_ja_entry, has status, active)
```

This is the core idea:

- the predicate remains the subject
- localization entries are separate updateable interpretation objects
- each locale gets its own source reference
- new locales can be added later with new triples
- updated translations do not require a new predicate atom

### Why a Localization Entry Instead of a Direct Triple

A direct triple like this:

```text
(follow_atom, has french translation, ipfs://...)
```

is tempting, but it does not scale well.

A localization entry object gives us room for:

- locale code
- source reference
- version metadata
- authority / publisher metadata
- activation / deprecation status
- future confidence or review metadata

This is the cleaner long-term model.

### Layer 3: Off-Chain Locale Bundle

Each localization entry points to a locale-specific bundle.

Example:

```json
{
  "predicate": "follow",
  "predicateAtomId": "0xabc...",
  "locale": "fr",
  "version": 1,
  "displayName": "Suivre",
  "forms": {
    "base": "suivre",
    "thirdPerson": "suit",
    "pastParticiple": "suivi"
  },
  "templates": {
    "default": "{subject} {predicate} {object}",
    "firstPerson": "{subject} {predicate} {object}"
  },
  "meta": {
    "direction": "ltr",
    "conjugates": true
  }
}
```

And for Japanese:

```json
{
  "predicate": "follow",
  "predicateAtomId": "0xabc...",
  "locale": "ja",
  "version": 1,
  "displayName": "フォロー",
  "forms": {
    "base": "フォローする",
    "thirdPerson": "フォローする",
    "pastParticiple": "フォローした"
  },
  "templates": {
    "default": "{subject}は{object}を{predicate}"
  },
  "meta": {
    "direction": "ltr",
    "conjugates": false
  }
}
```

The bundle should contain only rendering-oriented localization state:

- localized display name
- localized verb forms
- locale-specific template hints
- text direction
- grammar metadata used by the SDK

It should not redefine the predicate's core identity.

### Layer 4: SDK Resolution Layer

The SDK resolves localization progressively:

1. Read the canonical predicate atom
2. Check whether localization entries exist for the requested locale
3. Resolve the active source for that locale
4. Load and cache the locale bundle
5. Render using locale-specific forms
6. Fall back to English atom data if anything is missing

The rule is simple:

**English atom data is the floor. Localization bundles are upgrades.**

---

## Resolution Flow

### Example: `follow` in French

Input:

```text
predicateAtomId = FOLLOW
locale = "fr"
subjectContext = "first-person"
```

Resolution:

```text
1. Read FOLLOW atom data
   → name = "follow"
   → description = "Directional subscription or tracking..."

2. Query localization entries for FOLLOW
   → find entry for locale = fr

3. Resolve source
   → ipfs://bafy...follow-fr-v1

4. Load locale bundle
   → displayName = "Suivre"
   → forms.base = "suivre"

5. Render
   → "suivre"
```

If French has not been added yet:

```text
1. Read FOLLOW atom data
2. No localization entry for fr
3. Fallback to English atom name
   → "follow"
```

### Example: `follow` added months later

This is the progressive support story we need:

```text
January:
  follow atom exists
  only English atom data exists

May:
  French support is added
  Japanese support is added

October:
  Arabic support is added
  French translation is improved
```

The predicate atom never changes. What changes is the localization overlay:

```text
May:
  create (follow_atom, has localization, follow_fr_entry)
  create (follow_fr_entry, has locale, fr)
  create (follow_fr_entry, has source, ipfs://follow-fr-v1)

October:
  create new source ref for French
  mark old source deprecated or superseded
  keep follow_atom unchanged
```

That is exactly the progressive behavior we want.

---

## Recommended On-Chain Triple Vocabulary

The exact atom names can be decided later, but the conceptual model should include at least these meanings:

| Triple meaning | Purpose |
|---|---|
| `has localization` | Connects predicate atom to a localization entry |
| `has locale` | Declares which BCP 47 locale a localization entry serves |
| `has source` | Points to the canonical off-chain bundle for that locale |
| `has status` | Marks entry as active, deprecated, draft, etc. |
| `supersedes` | Links a new localization source or entry to the previous one |

This vocabulary gives us:

- progressive addition of new locales
- update history without changing the predicate atom
- auditability of translation changes
- deterministic resolution by indexers and SDKs

If we want an even smaller first version, we can launch with just:

```text
(predicate_atom, has localization, entry_atom)
(entry_atom, has locale, locale_atom)
(entry_atom, has source, source_atom)
```

Then add status/versioning later.

---

## Off-Chain Bundle Format

The bundle format should be simple and SDK-oriented, not overfit to Schema.org.

Recommended structure:

```json
{
  "predicateAtomId": "0xabc...",
  "canonicalName": "follow",
  "locale": "fr",
  "version": 1,
  "displayName": "Suivre",
  "forms": {
    "base": "suivre",
    "thirdPerson": "suit",
    "pastParticiple": "suivi"
  },
  "templates": {
    "default": "{subject} {predicate} {object}"
  },
  "meta": {
    "direction": "ltr",
    "conjugates": true
  }
}
```

Why this shape:

- flat enough for SDK consumers
- versionable
- locale-specific
- easy to cache
- easy to validate
- does not pretend localization is identity

If we want a master file too, that can exist as an index for convenience:

```json
{
  "predicateAtomId": "0xabc...",
  "availableLocales": ["en", "fr", "ja", "ar"]
}
```

But the SDK should not depend on a monolithic master document. Locale bundles should be independently addable.

---

## SDK Strategy

The SDK should be interpretation-driven.

The canonical English predicate atom is always the root truth. Localization is resolved on top of it.

### SDK Configuration

```typescript
interface PredicateI18nConfig {
  defaultLocale: string;
  fallbackLocale: 'en';
  sourceResolver: 'api-first' | 'chain-first';
  cacheTtlMs?: number;
}
```

### SDK API

```typescript
type PredicateForm = 'base' | 'thirdPerson' | 'pastParticiple' | 'displayName';

interface RenderPredicateOptions {
  locale: string;
  form?: PredicateForm;
  subjectContext?: 'first-person' | 'singular' | 'plural';
}

async function renderPredicate(
  predicateAtomId: string,
  options: RenderPredicateOptions
): Promise<string> {
  const atom = await getPredicateAtom(predicateAtomId);
  const localeBundle = await getPredicateLocaleBundle(predicateAtomId, options.locale);

  if (!localeBundle) {
    if (options.form === 'displayName') {
      return atom.name[0].toUpperCase() + atom.name.slice(1);
    }
    return atom.name;
  }

  if (options.form === 'displayName') {
    return localeBundle.displayName || atom.name;
  }

  if (options.subjectContext === 'singular' && localeBundle.forms.thirdPerson) {
    return localeBundle.forms.thirdPerson;
  }

  return localeBundle.forms.base || atom.name;
}
```

### Resolution Order

Recommended order:

1. in-memory SDK cache
2. app/API cache
3. indexer/API localization endpoint
4. chain lookup of localization entries
5. source fetch for locale bundle
6. English fallback from atom data

This keeps the common path fast while preserving a verifiable chain-based path underneath.

### SDK Guarantees

The SDK should guarantee:

- no locale ever changes the predicate atom ID
- missing locales always fall back to English
- broken source documents do not break rendering
- locale bundles can be refreshed without app redeploys

---

## Update Model

### Adding a New Locale

Example: adding Korean support to `follow`

```text
1. Create Korean locale bundle
2. Publish it to IPFS or the chosen artifact store
3. Create a localization entry atom if needed
4. Create:
   (follow_atom, has localization, follow_ko_entry)
   (follow_ko_entry, has locale, ko)
   (follow_ko_entry, has source, ipfs://follow-ko-v1)
```

The predicate atom does not change.

### Updating an Existing Locale

Example: improving French

```text
1. Publish new French locale bundle
   → ipfs://follow-fr-v2
2. Create new source triple or supersession triple
3. Indexer resolves v2 as canonical
4. SDK picks up the new bundle on refresh
```

Possible resolution strategies:

- latest active source wins
- explicit `supersedes` relation wins
- authority-wallet-signed latest version wins

My recommendation: use explicit supersession or status, not implicit "latest block wins."

### Removing or Deprecating a Locale

Do not delete history. Mark entries deprecated.

That gives us:

- auditability
- replayability
- safer indexer logic

---

## Language-Specific Concerns

### CJK Languages

- person-based conjugation may not apply
- spacing and sentence templates differ
- UI width needs attention

This is why the bundle format should support `templates` and not assume English word order.

### RTL Languages

- directionality must be explicit
- bidi rendering must be respected
- forms may depend on gender or number

This is why the bundle format should support metadata like:

```json
{
  "meta": {
    "direction": "rtl"
  }
}
```

### Gendered Languages

Some languages may need richer forms later.

Do not force that into the initial launch schema. Start with:

- `base`
- `thirdPerson`
- `pastParticiple`
- `displayName`

Then extend only when real predicates require it.

---

## Launch Plan

### Phase 1: English-First Launch

Ship:

1. canonical English predicate atoms
2. market routing triples
3. SDK rendering with English fallback from atom data
4. localization triple vocabulary
5. optional English locale bundles only if needed for consistency

This is enough to launch without blocking on translations.

### Phase 2: Progressive Locale Rollout

Then add:

1. French bundles
2. Spanish bundles
3. Japanese bundles
4. Arabic bundles
5. locale-aware templates where needed

Each addition is incremental and does not require atom migration.

### Phase 3: Governance and Contribution Workflow

Add:

1. translation review pipeline
2. authority or curator rules for canonical localization entries
3. community contribution process
4. tooling to validate locale bundles before publication

---

## What Not To Do

| Anti-Pattern | Why It's Wrong |
|---|---|
| Create locale-specific predicate atoms | Fragments markets and breaks canonical identity |
| Put all translations directly into predicate atom data | Freezes rendering metadata into identity |
| Depend on one monolithic IPFS document per predicate forever | Makes incremental locale rollout clumsy |
| Let each frontend invent translations independently | Produces inconsistent user-facing language |
| Make SDK rendering depend on a centralized API only | Loses verifiability and resilience |
| Require all locales at launch | Slows shipping for no protocol benefit |

---

## End-to-End Architecture

```text
┌──────────────────────────────────────────────────────────┐
│ Frontend: renderPredicate(atomId, locale, context)      │
├──────────────────────────────────────────────────────────┤
│ SDK cache: resolved locale bundles                      │
├──────────────────────────────────────────────────────────┤
│ API/indexer: resolves active localization entries       │
├──────────────────────────────────────────────────────────┤
│ Off-chain locale bundles: fr, ja, ar, ...              │
│   ipfs://follow-fr-v1                                   │
│   ipfs://follow-ja-v1                                   │
├──────────────────────────────────────────────────────────┤
│ On-chain triples: localization entry + locale + source │
├──────────────────────────────────────────────────────────┤
│ On-chain triples: market pattern + registry membership  │
├──────────────────────────────────────────────────────────┤
│ Predicate atom: DefinedTerm { name, description }       │
└──────────────────────────────────────────────────────────┘
```

Each layer has a clear role:

- **Predicate atom:** canonical identity and English fallback
- **Localization triples:** verifiable statement of available locale overlays
- **Locale bundles:** rendering metadata per language
- **SDK/API:** caching and developer ergonomics
- **Frontend:** locale-aware display

The overall principle is:

**Identity is on-chain and stable. Localization is progressive, layered, and updateable.**

# Predicate Display and Conjugation

## The Problem

Intuition predicates are on-chain identifiers stored as plain UTF-8 strings. The protocol hashes these strings into deterministic atom IDs via `keccak256(ATOM_SALT, keccak256(data))`. This means a predicate's on-chain identity is permanently bound to its exact bytes.

English verb conjugation creates a tension:

| Triple | Reads as | Grammatically correct? |
|---|---|---|
| `(I, follow, Vitalik)` | "I follow Vitalik" | Yes |
| `(Alice, follow, Vitalik)` | "Alice follow Vitalik" | No — should be "follows" |
| `(I, follows, Vitalik)` | "I follows Vitalik" | No — should be "follow" |
| `(Alice, follows, Vitalik)` | "Alice follows Vitalik" | Yes |

The same predicate must appear in both contexts. But `"follow"` and `"follows"` are different byte sequences, which means they produce **different atom IDs, different vaults, and different markets**. You cannot use both without splitting the graph.

This is not a minor formatting issue. It has real consequences:

1. **Market fragmentation.** If some builders use `follow` and others use `follows`, every triple, every vault, and every deposit is permanently split across two parallel predicate atoms. You get two half-sized markets instead of one deep market.

2. **Permanence.** Once atoms exist on-chain, they cannot be merged. The two predicate atoms will coexist forever.

3. **Partner divergence.** Without a clear rule, integration partners will make different choices, and the ecosystem fragments silently.

---

## The Options

### Option 1: Base Form (`follow`, `like`, `trust`)

Use the infinitive/base form — the verb form that works with "I".

| Context | Rendered | Grammar |
|---|---|---|
| `(I, follow, Vitalik)` | "I follow Vitalik" | Correct |
| `(Alice, follow, Vitalik)` | "Alice follow Vitalik" | Incorrect in English prose |
| `(developers, follow, Vitalik)` | "developers follow Vitalik" | Correct (plural subject) |
| `(Ethereum community, follow, Vitalik)` | "Ethereum community follow Vitalik" | Debatable (collective noun) |

**Pros:**
- Works perfectly with `I`, which is the primary subject for depositional triples (the most common pattern)
- Works with plural subjects (`developers`, `auditors`, community-scoped subjects)
- Standard in ontology design — RDF, Wikidata, OWL all use base form for predicates
- Consistent with the protocol's existing design direction

**Cons:**
- Reads awkwardly when a named third-person entity is the subject
- Developers who read raw triple data see "Alice follow Vitalik" and wonder if it's a bug

### Option 2: Third-Person (`follows`, `likes`, `trusts`)

Use the conjugated form that works with named singular subjects.

| Context | Rendered | Grammar |
|---|---|---|
| `(I, follows, Vitalik)` | "I follows Vitalik" | Incorrect |
| `(Alice, follows, Vitalik)` | "Alice follows Vitalik" | Correct |
| `(developers, follows, Vitalik)` | "developers follows Vitalik" | Incorrect |
| `(Ethereum community, follows, Vitalik)` | "Ethereum community follows Vitalik" | Correct (if treated as singular) |

**Pros:**
- Reads naturally for attributive triples with named subjects
- Matches how most people would describe the relationship in conversation
- The existing SDK already uses this form (`FOLLOWS_PREDICATE = 'follows'`)

**Cons:**
- Grammatically wrong with `I` — and `I` is the canonical subject for all depositional triples
- Grammatically wrong with plural subjects
- Goes against ontology conventions

### Option 3: Present Participle / Gerund (`following`, `liking`, `trusting`)

Use the -ing form as a neutral middle ground.

| Context | Rendered | Grammar |
|---|---|---|
| `(I, following, Vitalik)` | "I following Vitalik" | Incorrect (missing "am") |
| `(Alice, following, Vitalik)` | "Alice following Vitalik" | Incorrect (missing "is") |

**Pros:**
- Avoids the base/third-person choice
- Sometimes used in graph visualizations ("Alice → following → Vitalik")

**Cons:**
- Grammatically wrong in both contexts without an auxiliary verb
- Reads like a label, not a sentence — loses the natural language quality that makes triples readable
- Non-standard in ontology design

### Option 4: Relationship Noun (`follower-of`, `trust-in`, `endorsement-of`)

Use a noun form that doesn't conjugate.

| Context | Rendered | Grammar |
|---|---|---|
| `(I, follower-of, Vitalik)` | "I follower-of Vitalik" | Not a sentence |
| `(Alice, follower-of, Vitalik)` | "Alice follower-of Vitalik" | Not a sentence |

**Pros:**
- No conjugation ambiguity
- Works as a machine-readable label

**Cons:**
- Completely loses natural language readability — a core Intuition design goal
- Requires more cognitive work to understand triple meaning
- Unfamiliar to non-technical users

### Option 5: Both Forms, Accept Fragmentation

Create both `follow` and `follows` as atoms. Use `follow` with `I` and `follows` with named subjects.

**Pros:**
- Always grammatically correct
- Natural reading in every context

**Cons:**
- **Fatally fragments the market.** `(I, follow, Vitalik)` and `(Alice, follows, Vitalik)` are now on different predicate atoms. You cannot compare, aggregate, or compose across them.
- Doubles the predicate namespace
- Confuses builders: "which one do I use?"
- Defeats the entire purpose of canonical predicates

---

## The Decision: Base Form

**Intuition uses base form (`follow`, `like`, `trust`, `endorse`, etc.) as the canonical on-chain predicate string.**

The reasoning:

1. **`I` is the primary subject.** The depositional pattern (`I` as subject) is the most common and most important triple type. It produces deep, consolidated markets. Base form is grammatically correct with `I`.

2. **Plural subjects work.** Community-scoped subjects like `developers`, `auditors`, `Ethereum community` are grammatically correct with base form. Third-person form would be wrong here.

3. **Ontology convention.** Every major knowledge graph system (RDF, Wikidata, OWL, SKOS) uses base form for predicates. This isn't an accident — it's because predicates are relationship identifiers, not prose.

4. **One form, one atom, one market.** The overriding principle is: never split the market. One canonical string means one atom ID means one vault per (subject, predicate, object) combination.

5. **Display is a separate concern.** The raw predicate string is not what end users see. The UI layer handles rendering (see below).

### What We're Accepting

This is an imperfect solution. We are explicitly accepting these trade-offs:

- **Raw triple data reads awkwardly for third-person subjects.** A developer inspecting on-chain data will see `(Alice, follow, Bob)` and it reads like broken English. This is a cosmetic cost, not a functional one.
- **The existing SDK uses third-person forms.** `FOLLOWS_PREDICATE = 'follows'`, `CONTAINS_PREDICATE = 'contains'`, etc. Migrating to base form means new atom IDs and a migration period where both forms coexist. (See `predicate-migration-guide.md`.)
- **Non-verb predicates are unaffected.** Predicates like `better than`, `bullish on`, `member of`, `available on` don't conjugate. The tension only exists for single-verb predicates. Of the 100 cataloged predicates, roughly 30 are affected.

### Why This Is the Right Trade-Off

The alternative — using third-person form — is wrong with the protocol's most important subject (`I`) and wrong with plural subjects. Base form is wrong only with singular third-person subjects in attributive triples, which are the less common pattern and the pattern where raw readability matters least (attributive triples are typically created programmatically, not by end users typing).

The cost of choosing wrong here is permanent: on-chain atom IDs cannot be changed. Choosing base form aligns with the protocol's core design (depositional markets with `I`), with industry standards (RDF/OWL), and with the i18n strategy (predicates as locale-independent identifiers).

---

## The Display Layer: Solving It in the UI

The protocol stores `follow`. The user sees "I follow Vitalik" or "Alice follows Vitalik" depending on context. This is the display layer's job.

### Rendering Rules

```
Given a triple (Subject, Predicate, Object):

1. If Subject = "I" (the I-atom)
   → render predicate as base form
   → "I follow Vitalik"

2. If Subject = a singular named entity (Person, Organization, etc.)
   → render predicate as third-person form
   → "Alice follows Vitalik"

3. If Subject = a plural or collective entity
   → render predicate as base form
   → "developers follow Vitalik"

4. If predicate is a multi-word phrase that doesn't conjugate
   → render as-is regardless of subject
   → "Alice better than Bob", "I bullish on Ethereum"
```

### SDK Rendering Helper

The SDK should export a rendering function alongside the predicate constants:

```typescript
// Predicate constants (for on-chain use)
export const FOLLOW = 'follow';
export const LIKE = 'like';
export const TRUST = 'trust';
export const ENDORSE = 'endorse';

// Conjugation map (for display use)
const THIRD_PERSON: Record<string, string> = {
  'follow': 'follows',
  'like': 'likes',
  'trust': 'trusts',
  'endorse': 'endorses',
  'distrust': 'distrusts',
  'vouch for': 'vouches for',
  'agree with': 'agrees with',
  'disagree with': 'disagrees with',
  'support': 'supports',
  'oppose': 'opposes',
  'contain': 'contains',
  'use': 'uses',
  'implement': 'implements',
  'compete with': 'competes with',
  'depend on': 'depends on',
  'supersede': 'supersedes',
  'speak': 'speaks',
  'teach': 'teaches',
  'outperform': 'outperforms',
  'reward': 'rewards',
};

type SubjectType = 'first-person' | 'singular' | 'plural';

export function renderPredicate(
  predicate: string,
  subjectType: SubjectType
): string {
  if (subjectType === 'singular' && THIRD_PERSON[predicate]) {
    return THIRD_PERSON[predicate];
  }
  return predicate; // base form for first-person and plural
}

// Usage:
renderPredicate('follow', 'first-person');  // "follow"    → "I follow Vitalik"
renderPredicate('follow', 'singular');       // "follows"   → "Alice follows Vitalik"
renderPredicate('follow', 'plural');         // "follow"    → "developers follow Vitalik"
renderPredicate('bullish on', 'singular');   // "bullish on" → unchanged (not a verb)
```

### Relationship to i18n

The conjugation problem and the internationalization problem are the same problem at different scales:

| Layer | What changes | Example |
|---|---|---|
| Conjugation | Verb form based on subject | `follow` → `follows` |
| Localization | Entire word based on locale | `follow` → `suivre` |
| Script direction | Layout based on script | LTR → RTL |

All three are display concerns. The on-chain predicate stays the same: `follow`. The rendering pipeline transforms it based on context:

```
On-chain atom: "follow"
     ↓
Conjugation layer: subject is singular → "follows"
     ↓
Localization layer: locale is French → "suit"
     ↓
Rendering layer: display as "Alice suit Vitalik"
```

See `predicate-i18n-strategy.md` for the full localization architecture.

---

## Affected Predicates

These predicates have verb forms that conjugate differently between base and third-person:

| Base Form (canonical) | Third-Person (display) | Non-Conjugating? |
|---|---|---|
| `follow` | `follows` | |
| `like` | `likes` | |
| `endorse` | `endorses` | |
| `trust` | `trusts` | |
| `distrust` | `distrusts` | |
| `vouch for` | `vouches for` | |
| `block` | `blocks` | |
| `report` | `reports` | |
| `agree with` | `agrees with` | |
| `disagree with` | `disagrees with` | |
| `support` | `supports` | |
| `oppose` | `opposes` | |
| `contain` | `contains` | |
| `use` | `uses` | |
| `implement` | `implements` | |
| `compete with` | `competes with` | |
| `depend on` | `depends on` | |
| `outperform` | `outperforms` | |
| `supersede` | `supersedes` | |
| `speak` | `speaks` | |
| `teach` | `teaches` | |
| `reward` | `rewards` | |

These predicates are multi-word phrases or adjective-based and do **not** conjugate:

| Predicate | Why it doesn't conjugate |
|---|---|
| `is` | Already both base and third-person |
| `has type` | `has` is already third-person; but also works as a relation label |
| `has tag` | Same as above |
| `has category` | Same as above |
| `has description` | Same as above |
| `has source` | Same as above |
| `same as` | Adjective phrase |
| `alias of` | Noun phrase |
| `instance of` | Noun phrase |
| `subclass of` | Noun phrase |
| `member of` | Noun phrase |
| `employed by` | Past participle — doesn't change |
| `affiliated with` | Past participle |
| `created by` | Past participle |
| `authored by` | Past participle |
| `audited by` | Past participle |
| `verified by` | Past participle |
| `certified by` | Past participle |
| `governed by` | Past participle |
| `regulated by` | Past participle |
| `backed by` | Past participle |
| `pegged to` | Past participle |
| `listed on` | Past participle |
| `available on` | Adjective phrase |
| `compatible with` | Adjective phrase |
| `compliant with` | Adjective phrase |
| `better than` | Adjective phrase |
| `worse than` | Adjective phrase |
| `equivalent to` | Adjective phrase |
| `alternative to` | Noun phrase |
| `bullish on` | Adjective phrase |
| `bearish on` | Adjective phrase |
| `skeptical of` | Adjective phrase |
| `neutral on` | Adjective phrase |
| `expert in` | Noun phrase |
| `linked account` | Noun phrase |
| `predecessor of` | Noun phrase |
| `successor of` | Noun phrase |
| `student of` | Noun phrase |
| `mentor of` | Noun phrase |
| `partner of` | Noun phrase |

Note: the majority of predicates (especially passive constructions and adjective phrases) do not conjugate at all. The conjugation problem only affects active-voice single verbs, which are roughly 22 out of 100 predicates.

---

## The `has` Anomaly

The predicates `has type`, `has tag`, `has category`, `has description`, and `has source` use third-person `has` rather than base form `have`. This is an exception to the base-form rule.

Why keep `has` instead of migrating to `have type`, `have tag`, etc.:
- These predicates are almost exclusively used in attributive triples with named singular subjects: `(Ethereum, has type, blockchain)`. They are rarely used with `I`.
- `have type`, `have tag` would read oddly even with `I`: "I have type researcher" is not natural.
- These function more as relationship labels than as verbs — `has tag` reads as a compound label, not as conjugated English.
- Changing them would create migration churn with minimal readability benefit.

**Decision:** Keep `has X` predicates as-is. They are treated as fixed compound labels, not as conjugating verbs.

---

## Summary

| Principle | Rule |
|---|---|
| On-chain form | Base form (`follow`, not `follows`) |
| Why | Works with `I` (primary subject), plural subjects, and ontology standards |
| Exception | `has X` predicates stay as-is (compound labels) |
| Display | UI conjugates to third-person when subject is a singular named entity |
| SDK | Exports base form constants + a `renderPredicate()` helper |
| Trade-off accepted | Raw on-chain data reads awkwardly for `(Alice, follow, Bob)` |
| i18n alignment | Conjugation is a subset of the localization problem; same architecture solves both |

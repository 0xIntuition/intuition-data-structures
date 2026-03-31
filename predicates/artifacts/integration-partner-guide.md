# Integration Partner Guide — Enshrined Predicates

This guide is for builders integrating with the Intuition protocol. It covers what you need to know about enshrined predicates, how to create triples correctly, and common mistakes to avoid.

For the full predicate catalog and market design theory, see `predicate-analysis.md` and `predicate-usage-by-entity-type.md`. This guide focuses on practical implementation.

---

## Key Concepts in 60 Seconds

1. **Triples** are `(Subject, Predicate, Object)` — three atoms that form a knowledge claim.
2. **Predicates** are the relationship between subject and object. They are plain strings stored on-chain.
3. **Enshrined predicates** are the canonical predicates supported by the Intuition ecosystem (SDK, indexer, API, frontends).
4. **The `I` atom** is a singleton atom with data `"I"`. It is the universal subject for first-person claims. When Alice deposits on `(I, follow, Vitalik)`, she is saying "I follow Vitalik."
5. **Vaults** are created per triple. Deposits express conviction. TVL is the signal.

---

## The `I` Atom Pattern

This is the most important concept to understand. Getting this wrong fragments the market.

### The Problem

```
BAD:  (Alice, follow, Vitalik)   → vault with 1 depositor
BAD:  (Bob, follow, Vitalik)     → vault with 1 depositor
BAD:  (Carol, follow, Vitalik)   → vault with 1 depositor
Result: 3 tiny vaults. No signal.

GOOD: (I, follow, Vitalik)       → vault with 3 depositors (Alice, Bob, Carol)
Result: 1 deep vault. TVL = aggregate follow signal. Depositor count = follower count.
```

### The Rule

**If the depositor IS the claim-maker (first-person claim), always use `I` as the subject.**

This applies to: `follow`, `like`, `trust`, `distrust`, `endorse`, `recommend`, `vouch for`, `agree with`, `disagree with`, `bullish on`, `bearish on`, and all other depositional predicates.

### When NOT to Use `I`

Use a specific entity as subject when:
- The triple is a factual claim about a specific entity: `(Ethereum, created by, Vitalik)`
- The triple is a comparison between two entities: `(Rust, better than, Solidity)`
- The triple is about a collection: `(DeFi Blue Chips, contain, Aave)`

### Decision Tree

```
Is the depositor making a claim about themselves?
├── YES → Subject = I
│         (I, follow, Vitalik)
│         (I, bullish on, Ethereum)
│         (I, trust, Trail of Bits)
└── NO  → Is it a comparison between two things?
          ├── YES → Subject = the entity being ranked
          │         (Rust, better than, Solidity)
          └── NO  → Subject = the entity the claim is about
                    (Ethereum, created by, Vitalik)
                    (DeFi Stack, contain, Aave)
```

---

## Using Enshrined Predicates

### SDK Constants

```typescript
import {
  I_SUBJECT,
  FOLLOW,
  LIKE,
  TRUST,
  DISTRUST,
  ENDORSE,
  RECOMMEND,
  CONTAIN,
  CURATED_BY,
  IS,
  HAS_TAG,
  HAS_TYPE,
  HAS_DESCRIPTION,
  CREATED_BY,
  SAME_AS,
  BETTER_THAN,
  ALTERNATIVE_TO,
  AGREE_WITH,
  DISAGREE_WITH,
  BULLISH_ON,
  BEARISH_ON,
  // ... see full list in enshrined-predicates-launch-set.md
} from '@intuition/predicates';
```

### Creating a Triple

```typescript
// Depositional: User follows Vitalik
await createTriple({
  subject: I_SUBJECT,       // "I"
  predicate: FOLLOW,         // "follow"
  object: vitalikAtomId,
  deposit: parseEther('0.1'),
});

// Attributive: Factual claim about Ethereum
await createTriple({
  subject: ethereumAtomId,
  predicate: CREATED_BY,     // "created by"
  object: vitalikAtomId,
  deposit: parseEther('0.01'),
});

// Stack curation: Adding an item to a stack
await createTriple({
  subject: defiStackAtomId,
  predicate: CONTAIN,        // "contain"
  object: aaveAtomId,
  deposit: parseEther('0.01'),
});

// Comparative: Head-to-head opinion
await createTriple({
  subject: rustAtomId,
  predicate: BETTER_THAN,    // "better than"
  object: solidityAtomId,
  deposit: parseEther('0.05'),
});
```

### Displaying Predicates

Never show the raw predicate string to users. Use the SDK rendering function:

```typescript
import { renderPredicate } from '@intuition/predicate-i18n';

// "I follow Vitalik"
renderPredicate('follow', { locale: 'en', subjectContext: 'first-person' });
// → "follow"

// "Alice follows Vitalik"
renderPredicate('follow', { locale: 'en', subjectContext: 'singular' });
// → "follows"

// Button label
renderPredicate('follow', { locale: 'en', subjectContext: 'first-person', form: 'displayName' });
// → "Follow"
```

See `predicate-display-and-conjugation.md` for the full rendering spec.

---

## Market Patterns

Each enshrined predicate has a designated market pattern. Your frontend should route users to the correct pattern.

### Depositional (Subject = `I`)

Everyone deposits on the same triple. The vault is a shared market.

```
(I, follow, Vitalik)       ← one vault, many depositors
(I, bullish on, Ethereum)  ← one vault, many depositors
```

**Your app should:** Find the existing `(I, predicate, object)` triple and deposit on it. Never create `(UserName, follow, Vitalik)` — this fragments the market.

**Reading the market:** Depositor count = how many people hold this position. TVL = capital-weighted conviction.

### Attributive (Subject = specific entity)

The triple asserts a fact about a specific entity. Deposits mean "I agree this is true."

```
(Ethereum, created by, Vitalik)     ← factual claim
(DeFi Stack, contain, Aave)         ← curation fact
```

**Your app should:** Create the specific `(entity, predicate, object)` triple if it doesn't exist, then deposit.

**Reading the market:** TVL = market confidence that the claim is accurate.

### Comparative (Subject = entity being ranked)

The triple is a comparison. Deposits mean "I agree with this comparison."

```
(Rust, better than, Solidity)     ← opinion market
(Solana, alternative to, Ethereum) ← substitutability claim
```

**Your app should:** Check for both the triple and its inverse. The TVL ratio between them is the signal.

**Reading the market:** Compare `(A, better than, B)` TVL with `(B, better than, A)` TVL for the full picture.

### Hybrid (Context-dependent)

Some predicates can be either depositional or attributive:

```
(I, member of, Ethereum Foundation)         ← depositional: "I am a member"
(Vitalik, member of, Ethereum Foundation)   ← attributive: "Vitalik is a member"
```

**Your app should:** If the user IS the subject, route to the `I` pattern. If they're making a claim about someone else, use the specific entity.

---

## Querying Predicates

### Get recommended predicates for an entity type

```
GET /predicates?subjectType=Person
```

Returns the predicates that make sense for a Person subject, including market pattern and priority.

### Get market data for a depositional predicate

```
GET /triples?subject=I&predicate=follow&object={atomId}
```

Returns the vault data: depositor count, TVL, share price, deposit/withdrawal history.

### Get all triples for an entity

```
GET /triples?subject={atomId}
GET /triples?object={atomId}
```

Returns all triples where the entity appears as subject or object.

---

## Common Mistakes

### 1. Fragmenting the market

```
BAD:   createTriple({ subject: aliceAtomId, predicate: FOLLOW, object: vitalikAtomId })
GOOD:  createTriple({ subject: I_SUBJECT,   predicate: FOLLOW, object: vitalikAtomId })
```

If the user is following Vitalik, use `I`. Alice's identity is captured by her deposit address.

### 2. Using UI concepts as predicates

```
BAD:   createTriple({ subject: I_SUBJECT, predicate: 'bookmark', object: uniswapAtomId })
BAD:   createTriple({ subject: I_SUBJECT, predicate: 'star',     object: uniswapAtomId })
GOOD:  createTriple({ subject: I_SUBJECT, predicate: LIKE,       object: uniswapAtomId })
```

"Bookmark," "star," and "favorite" are UI concepts. They all mean `like` at the semantic level. Your app can render it as a bookmark icon — that's a frontend choice, not a predicate choice.

### 3. Using old-form predicate strings

```
BAD:   createTriple({ predicate: 'follows' })   // old third-person form
GOOD:  createTriple({ predicate: FOLLOW })        // "follow" — base form
```

`"follows"` and `"follow"` are different atom IDs. Always use the SDK constants. See `predicate-migration-guide.md`.

### 4. Creating custom predicates for covered concepts

Before creating a custom predicate, check if an enshrined predicate already covers your use case:

| You want to express | Use this enshrined predicate |
|---|---|
| User saves/bookmarks something | `like` |
| User subscribes to updates | `follow` |
| User rates something positively | `like` or `endorse` (based on strength) |
| User adds item to a list | `contain` (Stack as subject) |
| User tags something | `has tag` |
| User thinks X is better than Y | `better than` |
| User expresses positive outlook | `bullish on` |

### 5. Wrong triple direction

```
BAD:   (Vitalik, authored by, Ethereum Whitepaper)   ← backwards
GOOD:  (Ethereum Whitepaper, authored by, Vitalik)    ← the work was authored by the person

BAD:   (Alice, curated by, DeFi Stack)                ← backwards
GOOD:  (DeFi Stack, curated by, Alice)                ← the stack was curated by Alice
```

Check the canonical direction in `predicate-usage-by-entity-type.md`.

### 6. Missing paired markets

For opinion predicates, always ensure both sides of the pair exist:

```
INCOMPLETE:  (I, bullish on, Ethereum)     ← exists
             (I, bearish on, Ethereum)     ← doesn't exist

COMPLETE:    (I, bullish on, Ethereum)     ← exists
             (I, bearish on, Ethereum)     ← also exists
```

The ratio between paired markets is the signal. A lone bullish market without a bearish counterpart is less informative.

---

## Proposing New Predicates

If your integration needs a predicate that isn't enshrined, you have two options:

### Option A: Use a Custom Predicate

Create an atom with your predicate string and use it in triples. This works immediately — the protocol doesn't restrict what strings can be predicates. But custom predicates won't be recognized by the broader ecosystem (indexer, API metadata, frontend rendering).

### Option B: Propose Enshrinement

Submit a proposal following the process in `predicate-governance.md`. Your proposal needs:
- The canonical string (base form, lowercase)
- Market pattern (depositional/attributive/comparative)
- At least two independent use cases
- Evidence that existing enshrined predicates don't cover the need

---

## Reference

| Document | What It Covers |
|---|---|
| `enshrined-predicates-launch-set.md` | The 25 predicates shipping at launch, with atom IDs |
| `predicate-analysis.md` | Full 100-predicate catalog, DefinedTerm schemas, representation trade-offs |
| `predicate-usage-by-entity-type.md` | Per-entity-type predicate tables, market design, examples |
| `predicate-display-and-conjugation.md` | Why base form, how the display layer conjugates |
| `predicate-i18n-strategy.md` | Multi-language rendering architecture |
| `predicate-migration-guide.md` | Migrating from old `follows`/`contains` to `follow`/`contain` |
| `predicate-governance.md` | How to propose, approve, and deprecate predicates |

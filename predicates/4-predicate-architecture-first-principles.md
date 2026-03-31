# Predicate Architecture: First Principles Analysis

This document works from first principles to determine the right architecture for enshrined predicates. It is deliberately not anchored to any prior recommendation. It follows the evidence.

---

## The Actual Problem

Intuition stores three-part knowledge claims on-chain:

```
(Subject atom, Predicate atom, Object atom) → Vault
```

Every atom is created from some data. The atom ID is `keccak256(ATOM_SALT, keccak256(data))`. The data is stored on-chain. The atom ID is permanent.

**The question: what data goes into a predicate atom?**

The answer to this question determines:
- Whether predicates are self-describing or opaque
- Whether the system is architecturally consistent
- Whether developers need external resources to understand predicates
- How multi-language support works
- How the ecosystem scales from 25 predicates to 500
- How integration partners build with confidence

---

## An Observation Nobody Has Fully Confronted

Look at how Intuition already stores entities on-chain:

**Person atom data:**
```json
{"@context":"https://schema.org/","@type":"Person","givenName":"Vitalik","familyName":"Buterin"}
```

**Organization atom data:**
```json
{"@context":"https://schema.org/","@type":"Organization","name":"OpenAI","url":"https://openai.com"}
```

**DefinedTerm (tag/concept) atom data:**
```json
{"@context":"https://schema.org/","@type":"DefinedTerm","name":"Knowledge Graph","description":"Structured, semantic network..."}
```

**Predicate atom data:**
```
"follows"
```

One of these is not like the others.

Every entity in Intuition is a typed, self-describing JSON-LD object with Schema.org context. Except predicates. Predicates are bare strings. A predicate IS a DefinedTerm (the `predicate-analysis.md` document literally defines DefinedTerm schemas for each one), but the atom data doesn't reflect this.

This inconsistency has been invisible because predicates were never formally "classified." But now that we're enshrining them for production, we're forced to confront it.

The question isn't just "string vs IPFS." It's: **should predicates be classified atoms like everything else, or are they a special case?**

---

## Four Concrete Approaches

### Approach A: Plain Strings (Current)

Predicate atom data is a raw UTF-8 string.

```
Atom data:   "follow"
Atom ID:     keccak256(ATOM_SALT, keccak256(toHex("follow")))
On-chain:    6 bytes
```

**What a developer sees when they read chain data:**

```
Triple: (atom_0x123, atom_0x456, atom_0x789)

atom_0x123 data: {"@context":"https://schema.org/","@type":"Person","givenName":"Vitalik","familyName":"Buterin"}
atom_0x456 data: "follow"
atom_0x789 data: {"@context":"https://schema.org/","@type":"Organization","name":"Ethereum Foundation"}

Interpretation: OK, so 0x123 is a Person named Vitalik, 0x789 is an Organization...
                but 0x456 is just the string "follow". Is this a predicate? A tag? A name?
                I need external docs to know.
```

**What a new integration partner does:**

```
1. npm install @intuition/sdk
2. Read SDK docs to learn that "follow" is a predicate
3. Import FOLLOW constant
4. Use it
5. Works — but only because the SDK told them what "follow" means
```

**What breaks at scale:**

```
Partner A reads the SDK, uses FOLLOW constant → atom ID 0x456
Partner B doesn't use the SDK, creates an atom with "Follow" → atom ID 0x999
Partner C creates an atom with "follow " (trailing space) → atom ID 0xaaa

Three atoms. Three markets. Fragmentation.
The protocol has no way to tell you which one is the "real" follow predicate
without external documentation.
```

**Honest assessment:**

| Dimension | Rating | Why |
|---|---|---|
| Determinism | Strong | Plain string → deterministic hash. No serialization risk. |
| Gas cost | Minimal | 6-15 bytes per predicate atom |
| Self-describing | **No** | A raw string carries no type information. You can't distinguish a predicate atom from a tag atom or a name atom by reading chain data. |
| Architecture consistency | **No** | Every other atom type uses typed JSON-LD. Predicates are special-cased. |
| Developer cold-start | **Poor** | Without SDK/docs, chain data is opaque |
| SDK developer experience | Good | Simple constants, easy to use |
| Multi-language support | Neutral | Doesn't help or hinder; i18n is always off-chain |
| Scale risk | **Moderate** | No on-chain way to discover or validate enshrined predicates |

### Approach B: Plain Strings + On-Chain Interpretation Triples

Predicate atom data is a plain string. Interpretation is expressed as triples from an authority wallet.

```
Atom data:   "follow"
Atom ID:     keccak256(ATOM_SALT, keccak256(toHex("follow")))
On-chain:    6 bytes + ~7 interpretation triples

Interpretation triples (from authority wallet):
  (follow_atom, is, predicate_atom)
  (follow_atom, has description, follow_desc_atom)
  (follow_atom, same as, schema_follow_atom)
  (follow_atom, has type, depositional_atom)
  (follow_atom, has tag, social_atom)
  (predicate_registry, contain, follow_atom)
  (follow_atom, expected object type, person_type_atom)
```

**What a developer sees when they read chain data:**

```
Triple: (atom_0x123, atom_0x456, atom_0x789)

atom_0x456 data: "follow"
  → Query: what triples involve atom_0x456 as subject?
  → Found: (0x456, is, "predicate")       ← OK, it's a predicate
  → Found: (0x456, has type, "depositional")  ← use I-subject pattern
  → Found: (0x456, has description, "The depositor subscribes to...")

Interpretation: Fully reconstructable from chain data.
But it took 3 extra queries to understand a 6-byte string.
```

**What breaks at scale:**

```
25 predicates × 7 triples = 175 interpretation triples
100 predicates × 7 triples = 700 interpretation triples
Each interpretation triple also needs its OWN atoms
  ("predicate", "depositional", "social", description strings...)

The interpretation layer bootstraps more atoms than the predicates themselves.
This is manageable but it's significant complexity.
```

**The authority wallet problem:**

```
Q: Who creates these interpretation triples?
A: The "authority wallet" — a team-controlled address.

Q: What if the key is compromised?
A: All canonical interpretations are suspect. Need to re-establish authority.

Q: What if two people disagree about an interpretation?
A: The authority wallet wins. This is centralized governance with extra steps.

Q: How does this differ from "the SDK is the authority"?
A: It's verifiable on-chain, which matters for trustless verification.
   But 99% of developers will use the SDK/API regardless.
```

**Honest assessment:**

| Dimension | Rating | Why |
|---|---|---|
| Determinism | Strong | Plain string atom + deterministic triple structure |
| Gas cost | Moderate | ~225 triples for 25 predicates (one-time, cheap on L2) |
| Self-describing | **Partially** | The atom itself is opaque; you need to read related triples |
| Architecture consistency | **No** | Entity atoms are still typed JSON-LD; predicates are still bare strings with metadata bolted on via triples |
| Developer cold-start | Good | Can reconstruct catalog from chain (with effort) |
| SDK developer experience | Good | Same constants; chain verification is a bonus |
| Multi-language support | Neutral | i18n stays off-chain regardless |
| Scale risk | Low | Registry on-chain; discoverability is good |
| Operational complexity | **High** | Authority wallet management, interpretation triple maintenance, bootstrap coordination |

### Approach C: DefinedTerm Atom (Minimal) + On-Chain Market Routing

Predicate atoms use the **same DefinedTerm classification** that tags, concepts, and keywords already use. The atom data is typed JSON-LD, consistent with every other atom in the system. On-chain triples carry only the protocol-critical metadata (market pattern).

```
Atom data:   {"@context":"https://schema.org/","@type":"DefinedTerm","name":"follow","description":"Directional subscription or tracking of the object entity"}
Atom ID:     keccak256(ATOM_SALT, keccak256(toHex(canonical_json)))
On-chain:    ~120 bytes + 2 interpretation triples
```

The interpretation triples carry ONLY what affects protocol correctness:

```
(follow_atom, has type, depositional_atom)     ← market routing (critical)
(predicate_registry, contain, follow_atom)     ← enshrined status (critical)
```

Everything else (i18n, conjugation, Schema.org mappings, usage constraints, examples) is off-chain.

**What a developer sees when they read chain data:**

```
Triple: (atom_0x123, atom_0x456, atom_0x789)

atom_0x123 data: {"@context":"https://schema.org/","@type":"Person","givenName":"Vitalik",...}
atom_0x456 data: {"@context":"https://schema.org/","@type":"DefinedTerm","name":"follow","description":"Directional subscription..."}
atom_0x789 data: {"@context":"https://schema.org/","@type":"Organization","name":"Ethereum Foundation"}

Interpretation:
  0x123 = a Person named Vitalik        (from atom data — @type tells you)
  0x456 = a DefinedTerm named "follow"   (from atom data — @type tells you)
  0x789 = an Organization named EF       (from atom data — vtype tells you)

  The triple reads as: "Vitalik follow Ethereum Foundation"
  The predicate is self-describing. No external docs needed for basic understanding.
```

**Why this might be the right answer:**

```
Entity atoms:     {"@type":"Person", "name":"Vitalik"}        ← typed, self-describing
Tag atoms:        {"@type":"DefinedTerm", "name":"DeFi"}       ← typed, self-describing
Predicate atoms:  {"@type":"DefinedTerm", "name":"follow"}     ← typed, self-describing

Everything is the same shape. The classification pipeline, the indexer,
the API — they all handle DefinedTerm already.

A predicate atom IS a DefinedTerm. The repo has a DefinedTerm classification.
Using it for predicates is not adding complexity — it's removing a special case.
```

**The canonical serialization question:**

```
Q: JSON key ordering changes the hash. Isn't this dangerous?
A: Intuition ALREADY handles this.

   Every Person atom is JSON: {"@context":"https://schema.org/","@type":"Person",...}
   Every Organization atom is JSON.
   Every DefinedTerm tag is JSON.

   The classification pipeline produces canonical JSON.
   The SDK produces canonical JSON.
   This is a solved problem in the codebase.

   If it weren't solved, entity atoms would already be broken.
```

**What breaks at scale:**

```
25 predicates × ~120 bytes = ~3KB on-chain (vs ~150 bytes for plain strings)
100 predicates × ~120 bytes = ~12KB on-chain
On Base L2, 12KB of calldata costs roughly $0.50-$2.00 total. This is negligible.

The real cost is: each predicate needs 2 on-chain triples (market pattern + registry).
25 predicates × 2 = 50 triples. Much less than Approach B's 175-225.
```

**The description-in-atom trade-off:**

```
Pro: A developer reading chain data immediately understands the predicate.
     No external queries, no API calls, no SDK required.

Con: The description is frozen in the atom. If you write a bad description,
     it lives on-chain forever.

Mitigation: Write descriptions carefully (they're one-time).
            Use enrichment/triples for extended documentation.
            The description only needs to be sufficient for basic understanding,
            not comprehensive.

Note: Entity atoms have the same constraint. A Person atom's "givenName"
      can't be corrected. Intuition already accepts this trade-off.
```

**Honest assessment:**

| Dimension | Rating | Why |
|---|---|---|
| Determinism | Strong | Canonical JSON serialization (already solved for all entity atoms) |
| Gas cost | Low | ~120 bytes per atom on L2; trivial |
| Self-describing | **Yes** | `@type: DefinedTerm` + `name` + `description` — readable from atom data alone |
| Architecture consistency | **Yes** | Same classification pattern as Person, Organization, DefinedTerm tags |
| Developer cold-start | **Excellent** | Read any atom, understand it. No external resources needed for basics. |
| SDK developer experience | Good | SDK creates DefinedTerm JSON; same as creating any other atom |
| Multi-language support | Neutral | English description in atom; i18n labels off-chain (same as all approaches) |
| Scale risk | Low | Registry on-chain; self-describing atoms; minimal interpretation triples |
| Operational complexity | Low | Only 2 triples per predicate (market pattern + registry), not 7 |

### Approach D: IPFS CID Pointing to Rich Document

Predicate atom data is an IPFS content identifier.

```
Atom data:   "ipfs://bafyrei..."
Atom ID:     keccak256(ATOM_SALT, keccak256(toHex("ipfs://bafyrei...")))
On-chain:    ~60 bytes
```

The IPFS document contains everything: DefinedTerm schema, description, Schema.org mapping, usage examples, i18n labels.

**What a developer sees when they read chain data:**

```
atom_0x456 data: "ipfs://bafyreibn3..."

Interpretation: I need to fetch this from IPFS to know what it means.
  → IPFS gateway request
  → Wait for response
  → Parse JSON document
  → Now I understand it's "follow"

If the IPFS gateway is down, or the content is unpinned:
  → I see an opaque CID string
  → I have no idea what this predicate means
  → I cannot render this triple
```

**Honest assessment:**

| Dimension | Rating | Why |
|---|---|---|
| Determinism | Strong | CID is content-addressed |
| Gas cost | Low | ~60 bytes |
| Self-describing | **No** — requires IPFS fetch | Opaque without network access |
| Architecture consistency | No | Entity atoms are inline JSON; predicates are pointers |
| Developer cold-start | **Poor** | Requires IPFS infrastructure to understand predicates |
| Availability | **Fragile** | IPFS pinning must be maintained forever |
| Multi-language support | Could embed all translations in IPFS doc | But frozen — can't add languages without new CID = new atom ID |
| Scale risk | **High** | Every predicate resolution requires a network roundtrip |

---

## The Dimensions That Actually Matter

### 1. Developer Experience (Day-to-Day)

What does a developer actually do every day?

```typescript
// They import a constant and use it. This is the same for ALL approaches.
import { FOLLOW } from '@intuition/predicates';
await createTriple({ subject: I_SUBJECT, predicate: FOLLOW, object: vitalikId });
```

The atom data format is invisible to the daily developer. The SDK abstracts it. Whether `FOLLOW` resolves to `"follow"` or `{"@type":"DefinedTerm","name":"follow",...}` doesn't change the import/use pattern.

**Winner: Tie.** All approaches have the same SDK surface.

### 2. Developer Experience (Onboarding / Cold Start)

A new developer who doesn't have the SDK. They're reading chain data, exploring the protocol, trying to understand what they're looking at.

```
Approach A: "follow" — what is this? A name? A tag? A predicate? I need docs.
Approach B: "follow" — same, but I can query related triples (if I know to look)
Approach C: {"@type":"DefinedTerm","name":"follow","description":"..."} — I can read this
Approach D: "ipfs://baf..." — completely opaque without IPFS
```

**Winner: Approach C.** Self-describing from atom data. No external queries, no SDK, no docs needed for basic comprehension.

### 3. Architecture Consistency

```
Entity atoms:    JSON-LD with @type
Tag atoms:       JSON-LD DefinedTerm
Predicate atoms: ???

Approach A: ??? = plain string (inconsistent)
Approach B: ??? = plain string + external triples (bolt-on)
Approach C: ??? = JSON-LD DefinedTerm (consistent)
Approach D: ??? = IPFS pointer (inconsistent)
```

**Winner: Approach C.** It's the only approach where predicates are atoms like everything else.

### 4. On-Chain Reproducibility

Can you reconstruct the full predicate catalog from chain data alone?

```
Approach A: No. You have strings but no way to know which ones are predicates,
            what they mean, or how to use them.
Approach B: Yes, if you know the authority wallet. Query its triples.
Approach C: Partially from atom data (name + description), fully with 2 triples
            per predicate (market pattern + registry membership).
Approach D: Yes, if IPFS is available. No, if it isn't.
```

**Winner: Approach B is most complete. Approach C is close second with far less overhead.**

### 5. Multi-Language Support

All approaches handle i18n the same way: off-chain. You cannot put translations on-chain without creating different atom IDs.

The i18n architecture is:
1. On-chain atom has a canonical English identifier
2. Off-chain registry maps identifier → localized labels per locale
3. SDK `renderPredicate(predicate, locale)` resolves at display time

The only difference: Approach C's atom data includes an English `description` field that aids comprehension for English-speaking developers. This is not a substitute for proper i18n — it's a baseline.

**Winner: Tie.** i18n is always off-chain. No approach changes this.

### 6. Scale (100+ Predicates, 500+ Partners)

```
Approach A:
  On-chain: 100 atoms (100 × ~10 bytes = 1KB)
  Off-chain: SDK, docs, API — all maintained by core team
  Risk: no on-chain discoverability; partners depend entirely on SDK/docs

Approach B:
  On-chain: 100 atoms + ~700 interpretation triples + ~200 auxiliary atoms
  Off-chain: SDK, docs, API (caches of on-chain state)
  Risk: interpretation triple maintenance; authority wallet governance

Approach C:
  On-chain: 100 atoms (~12KB) + ~200 triples (market pattern + registry)
  Off-chain: SDK, docs, API, i18n registry
  Risk: canonical JSON serialization must be consistent across SDK versions

Approach D:
  On-chain: 100 atoms (~6KB of CID strings)
  Off-chain: IPFS documents, pinning infrastructure
  Risk: IPFS availability at scale
```

**Winner: Approach C.** Lowest operational overhead at scale. Self-describing atoms reduce the interpretation burden. Market routing (the only protocol-critical metadata) is on-chain with minimal triple count.

### 7. What Happens When Things Go Wrong

**Scenario: A partner creates a predicate with wrong capitalization**

```
Approach A: "Follow" vs "follow" → different atoms. No way to detect the error
            from chain data. The string "Follow" doesn't tell you it's wrong.
Approach B: Same problem. "Follow" is a valid string. The interpretation
            registry doesn't cover it. But at least the registry provides
            the canonical set for comparison.
Approach C: A partner creating {"@type":"DefinedTerm","name":"Follow"}
            produces a different atom than {"@type":"DefinedTerm","name":"follow"}.
            BUT: the @type and structure make it clear the partner was TRYING
            to create a DefinedTerm predicate. The indexer can flag:
            "This looks like a predicate but doesn't match any enshrined predicate."
Approach D: Same as A. An IPFS CID is opaque.
```

**Scenario: The team needs to update a predicate's description**

```
Approach A: Change the docs. No on-chain impact.
Approach B: Authority wallet creates a new (predicate, has description, new_desc)
            triple. Old triple persists. Need resolution rules (latest wins?).
Approach C: Can't change the atom's description (frozen). Create a new
            enrichment triple or artifact for updated description.
            But the atom's description is a baseline, not the final word.
Approach D: Must create a new IPFS document → new CID → new atom ID.
            This is a new predicate. Market fragmentation.
```

**Scenario: IPFS goes down / pinning lapses**

```
Approach A: No impact. Plain strings don't depend on IPFS.
Approach B: No impact. Interpretation is on-chain triples.
Approach C: No impact. Atom data is inline JSON, not a pointer.
Approach D: FATAL. Predicates become opaque CIDs. Cannot render triples.
```

---

## The Honest Trade-Off Table

| | A: Plain String | B: String + Triples | C: Minimal DefinedTerm + Routing | D: IPFS |
|---|---|---|---|---|
| **Gas (one-time bootstrap)** | ~$0.01 | ~$2-5 | ~$0.50-1 | ~$0.10 |
| **Gas (per new predicate)** | Negligible | ~$0.10 | ~$0.05 | Negligible |
| **On-chain bytes** | Minimal | Moderate | Low-moderate | Low |
| **Self-describing** | No | No (atom is still a string) | **Yes** | No (without fetch) |
| **Consistent with entity atoms** | No | No | **Yes** | No |
| **Cold-start developer UX** | Poor | Good (with effort) | **Excellent** | Poor |
| **Daily developer UX** | Good | Good | Good | Good |
| **On-chain discoverability** | None | High | Medium-high | None (without fetch) |
| **Operational overhead** | Low | **High** (authority wallet, 7 triples/predicate) | **Low** (2 triples/predicate) | Moderate (IPFS pinning) |
| **Availability dependency** | None | None | None | **IPFS** |
| **Description updateability** | Off-chain (easy) | On-chain triples (messy) | Atom frozen + enrichment | New CID = new atom (fatal) |
| **Multi-language** | Off-chain | Off-chain | Off-chain | Could embed (but frozen) |
| **Breaking change from current** | No | No | **Yes** (new atom IDs) | **Yes** |
| **Interpretation maintenance** | SDK/docs only | 7+ triples per predicate | 2 triples per predicate | IPFS docs |

---

## The Uncomfortable Question: Does On-Chain Reproducibility Actually Matter?

Let me be direct about this because it's the crux of the debate.

**The argument for on-chain reproducibility:**

> "A developer with nothing but chain access should be able to reconstruct the full predicate catalog."

This is a decentralization principle. It sounds compelling. But let's ask: **who is this developer, and when does this scenario actually occur?**

- **A developer building an integration:** They will use the SDK. Period. Nobody is going to parse raw chain data and reconstruct predicate meaning from triples when `npm install` exists. The SDK is how software gets built.

- **A researcher analyzing the protocol:** They might read chain data. But they'll also read the docs. Chain-only analysis is an academic exercise, not a practical workflow.

- **A scenario where the Intuition team disappears:** In this catastrophic case, the chain data survives. With Approach A, predicates are opaque strings and someone must reverse-engineer meaning. With Approach B, interpretation triples survive on-chain. With Approach C, the atom data itself tells you what each predicate is.

- **A competing indexer or fork:** They need to understand predicates. With A, they reverse-engineer from observing usage patterns. With B, they read the authority wallet's triples. With C, they read the atom data directly.

**The honest answer:** On-chain reproducibility matters for **resilience and legitimacy**, not for daily operations. It's insurance. The question is whether that insurance is worth the cost.

- **Approach B buys maximum insurance at maximum cost.** 7 triples per predicate, authority wallet operations, interpretation maintenance.
- **Approach C buys most of the insurance at much lower cost.** The atom itself is self-describing. Two triples handle market routing. Everything else is off-chain.

---

## My Recommendation

**Approach C: DefinedTerm atoms with minimal on-chain routing triples.**

If the live question is specifically **inline JSON object vs IPFS-backed JSON object**, the answer is: **inline JSON wins decisively over IPFS for predicates**. If you are going to have a predicate document at all, storing it directly in atom data is superior to storing a pointer to it.

Here's why, working from the principles that actually matter:

### Principle 1: Architectural consistency beats cleverness

Intuition already has a pattern: typed JSON-LD atoms. Person, Organization, DefinedTerm, Event — all follow it. Predicates should follow it too. Special-casing predicates as bare strings is a shortcut that creates technical debt.

The DefinedTerm classification already exists in this repo. A predicate IS a DefinedTerm. The schema is defined. The indexer handles it. Using it for predicates isn't adding complexity — it's removing an exception.

### Principle 2: Self-describing beats documented

A developer who reads `{"@type":"DefinedTerm","name":"follow","description":"Directional subscription or tracking of the object entity"}` understands the predicate. They don't need to find the right doc, import the right SDK version, or query an authority wallet's triples.

This matters most at the boundaries: new partners, forks, researchers, auditors. The people who are NOT deeply embedded in the Intuition ecosystem but need to understand what they're looking at.

### Principle 3: Put protocol-critical metadata on-chain, put everything else off-chain

Not all interpretation is equal:

| Metadata | What happens if it's wrong/missing? | Belongs on... |
|---|---|---|
| Predicate identity (name, type) | **Can't use the predicate at all** | Atom data |
| Market pattern (depositional/attributive) | **Irreversible market fragmentation** | On-chain triple |
| Registry membership (is this enshrined?) | **Partners build on wrong predicates** | On-chain triple |
| Schema.org mapping | Developer inconvenience | Off-chain |
| Usage constraints (expected object types) | Suboptimal but recoverable | Off-chain (phase 2: on-chain) |
| i18n labels | Wrong language in UI | Off-chain |
| Conjugation rules | Grammar issue in display | Off-chain |
| Examples and documentation | Learning friction | Off-chain |

Only three things are load-bearing enough for on-chain storage:
1. **The predicate's identity** (name + description → in the atom data via DefinedTerm)
2. **The market pattern** (depositional/attributive → on-chain triple)
3. **The registry membership** (is this enshrined → on-chain triple)

Everything else can live off-chain because getting it wrong is recoverable. Getting market pattern wrong is not.

### Principle 4: Minimize what you have to maintain on-chain

Approach B requires ~7 triples per predicate: `is`, `has description`, `same as`, `has type`, `has tag`, `contain` (registry), `expected object type`. That's 175+ triples for 25 predicates, plus all the auxiliary atoms those triples reference.

Approach C requires ~2 triples per predicate: `has type` (market pattern) + `contain` (registry). That's 50 triples for 25 predicates. The atom data carries the rest.

Fewer on-chain interpretation triples = less operational overhead, less governance surface area, less to get wrong.

### Principle 5: Accept the trade-offs explicitly

**Trade-off 1: Frozen descriptions.** The `description` field in the atom data can't be updated. Write it carefully. Use enrichment/triples for extended documentation that can evolve.

**Trade-off 2: Breaking change from current SDK.** Current predicates are plain strings (`"follows"`). DefinedTerm JSON predicates will produce different atom IDs. This migration is happening anyway (base form: `"follow"` replacing `"follows"`). Combining both migrations into one is better than two separate breaking changes.

**Trade-off 3: Canonical JSON serialization.** The SDK must produce identical JSON for the same predicate. This is already solved for every other atom type. The same serialization function handles predicates.

**Trade-off 4: DefinedTerm predicates look like DefinedTerm tags.** Both `follow` (predicate) and `DeFi` (tag) are `@type: DefinedTerm`. You distinguish them by: (a) the on-chain registry triple `(predicate_registry, contain, follow_atom)`, or (b) observing which position the atom occupies in triples (predicate position vs object position). This is a non-issue in practice — the indexer already handles positional context.

### First-Principles Verdict: Inline JSON vs IPFS

If those are the two options on the table, here is the clean conclusion:

| Question | Inline JSON object in atom data | JSON object on IPFS with URI in atom data |
|---|---|---|
| Can chain data explain itself? | **Yes** | No |
| Does understanding require an external network fetch? | No | **Yes** |
| Does availability depend on pinning / gateway health? | No | **Yes** |
| Is the atom still permanent and deterministic? | Yes | Yes |
| Do documentation changes force a new atom ID? | **Yes** | **Yes** |
| Does IPFS actually solve mutability? | No | No |
| Does this minimize on-chain bytes? | No | **Yes** |
| Is that byte savings worth the semantic dependency? | Usually no | Only if the document is too large to justify inline storage |

The decisive point is this:

- **Inline JSON and IPFS JSON have the same immutability property.** In both designs, changing the JSON means changing the atom ID.
- **IPFS does not buy you meaningful flexibility.** It mostly buys smaller atom data at the cost of external dependency and worse self-description.
- **For predicate infrastructure, semantic availability matters more than saving a few hundred bytes.**

So if the choice is between inline JSON and IPFS-backed JSON, choose the inline JSON route represented by **Approach C**.

---

## What This Looks Like In Practice

### Bootstrap (One-Time, Pre-Launch)

**Step 1: Create predicate atoms using DefinedTerm classification**

```typescript
// SDK function (same pattern as creating any classified atom)
function createPredicateAtomData(name: string, description: string): string {
  return canonicalJsonStringify({
    "@context": "https://schema.org/",
    "@type": "DefinedTerm",
    "name": name,
    "description": description
  });
}

// Create atoms
const followAtomData = createPredicateAtomData(
  "follow",
  "Directional subscription or tracking of the object entity"
);
const followAtomId = await createAtom(followAtomData);
```

For 25 launch predicates, this produces 25 DefinedTerm atoms on-chain.

**Step 2: Create market routing triples**

```
(follow_atom, has type, depositional_atom)
(like_atom, has type, depositional_atom)
(trust_atom, has type, depositional_atom)
(created_by_atom, has type, attributive_atom)
(better_than_atom, has type, comparative_atom)
...
```

**Step 3: Create registry membership triples**

```
(predicate_registry_atom, contain, follow_atom)
(predicate_registry_atom, contain, like_atom)
(predicate_registry_atom, contain, trust_atom)
...
```

**Total on-chain cost: 25 atoms + ~50 triples.** At Base L2 prices, this is dollars.

### Daily SDK Usage (Unchanged)

```typescript
import { FOLLOW, LIKE, TRUST, I_SUBJECT } from '@intuition/predicates';

// Same as before — developer doesn't care about atom internals
await createTriple({
  subject: I_SUBJECT,
  predicate: FOLLOW,
  object: vitalikAtomId
});
```

The SDK exports pre-computed atom IDs. The fact that `FOLLOW` resolves to a DefinedTerm atom ID instead of a plain string atom ID is invisible.

### Cold-Start Developer (Exploring Chain Data)

```
Developer reads a triple from the indexer:

Subject: {"@type":"Person","givenName":"Vitalik","familyName":"Buterin"}
Predicate: {"@type":"DefinedTerm","name":"follow","description":"Directional subscription..."}
Object: {"@type":"Organization","name":"Ethereum Foundation"}

Reading: "Vitalik follow Ethereum Foundation"
         The predicate is a DefinedTerm called "follow" that means directional subscription.
         No docs needed. Self-describing.
```

### Integration Partner (Building a New Frontend)

```
1. Query: GET /predicates → returns all enshrined predicates
   (API reads registry triples + atom data; serves enriched response)

2. Response for "follow":
   {
     "atomId": "0xdef...",
     "name": "follow",
     "description": "Directional subscription or tracking of the object entity",
     "marketPattern": "depositional",     // from on-chain triple
     "enshrined": true,                    // from registry triple
     "conjugation": {                      // from off-chain i18n registry
       "thirdPerson": "follows",
       "displayName": "Follow"
     },
     "labels": {                           // from off-chain i18n registry
       "en": "Follow",
       "fr": "Suivre",
       "ja": "フォロー"
     }
   }

3. Partner builds their UI using this metadata.
   On-chain: atomId, name, description, marketPattern, enshrined
   Off-chain: conjugation, i18n labels
```

### User Experience (End User Sees)

```
User's locale: French
User action: clicks "Suivre" button on Vitalik's profile

Frontend flow:
  1. Resolve "follow" predicate → FOLLOW atom ID
  2. Market pattern = depositional → use I_SUBJECT
  3. Find or create (I, follow, Vitalik) triple
  4. User deposits
  5. UI shows "Vous suivez Vitalik Buterin" (from i18n labels)

The atom data (JSON), the on-chain triples (routing), and the off-chain registry (i18n)
all work together. No single layer carries the entire burden.
```

---

## Addressing Your Teammate's Specific Points

> "Should the entirety of the state of Intuition be accessible / reproducible from the data stored on-chain?"

**For predicate interpretation: mostly yes.** The atom data contains the name and description (self-describing). The on-chain triples contain the market routing (protocol-critical). Together, a developer can understand any predicate from chain data alone.

What stays off-chain: i18n labels, conjugation rules, usage examples, documentation. These are rendering and education concerns, not protocol state.

> "We have diverged a bit from 'on-chain is the single source of truth' with artifacts"

Artifacts are the right model for entity enrichment (images, descriptions that change). But predicates are not artifacts. They're infrastructure. The atom data should be richer than a bare string (self-describing), and the critical routing metadata should be on-chain.

The principle that emerges: **Identity + infrastructure → on-chain. Context + rendering → off-chain.**

> "You would need SOMETHING to tell you what these special 'predicate Atoms' actually mean"

With DefinedTerm atoms, the atom data ITSELF tells you. `{"@type":"DefinedTerm","name":"follow","description":"..."}` is self-interpreting. The only additional on-chain information needed is market routing (depositional vs attributive), which is 1 triple per predicate.

> "One example pattern: we use strings for predicate atomData, then publish additional metadata as Triples..."

This is Approach B. It works. But it's solving a problem that Approach C partially eliminates. If the atom data is a DefinedTerm (not a bare string), much of the interpretation is already IN the atom. You only need on-chain triples for metadata that can't be in the atom (market pattern, registry membership) — not for basic semantic meaning.

> "Wallet X's attestations are to be read as the canonical interpretation"

The authority wallet concept is still valuable in Approach C. The authority wallet creates the market routing triples and registry membership triples. But it doesn't need to create description triples, Schema.org mapping triples, or tag triples — because the atom data already carries the semantics.

The authority wallet's role shrinks from "full interpretation authority" to "market routing authority." That's a simpler, more defensible responsibility.

---

## Migration Implications

If we go with Approach C, the migration from current state is:

| Current | New | Change |
|---|---|---|
| Atom data: `"follows"` | Atom data: `{"@type":"DefinedTerm","name":"follow",...}` | New atom ID (double migration: base form + DefinedTerm) |
| No on-chain registry | Registry triples | New triples |
| SDK: `FOLLOWS_PREDICATE = "follows"` | SDK: `FOLLOW = calculateAtomId(definedTermJson)` | SDK constant change |
| Interpretation: docs only | Interpretation: atom data + 2 triples + off-chain | New architecture |

This is a larger migration than Approach A or B. But it's a one-time cost at launch. And combining the base-form migration with the DefinedTerm migration is cleaner than doing them separately.

---

## The Counter-Argument: "Just Use Strings, It's Simpler"

This is a legitimate position. Here's the strongest version of the argument:

> Predicate atoms are used in the predicate position of triples. That's all they need to be — identifiers. They don't need to be self-describing because they're always used in context (as the middle element of a triple). The SDK/API tells developers what they mean. Adding JSON-LD to predicate atoms adds complexity and gas cost for a self-description property that nobody will use in practice.

**My response:** This is true for the 99% case (developers using the SDK). But it's false for the boundary cases that determine whether Intuition is a real protocol or a centralized service wearing a decentralization costume. The self-describing property matters for:
- Protocol legitimacy (can anyone verify the system independently?)
- Architectural coherence (is everything consistently structured?)
- Resilience (what survives if the team/SDK/API disappear?)
- Partner confidence (can I trust what I'm building on?)

If Intuition aspires to be protocol-grade infrastructure (not a product with a blockchain backend), the atoms should be self-describing. The cost is ~$1 of gas and a canonical JSON serializer. The benefit is architectural integrity that scales.

---

## Summary

| Decision | Recommendation |
|---|---|
| Atom data format | DefinedTerm JSON-LD (minimal: `@context`, `@type`, `name`, `description`) |
| If choosing between inline JSON vs IPFS JSON | **Inline JSON** |
| On-chain triples | 2 per predicate: market routing (`has type`) + registry (`contain`) |
| Off-chain | i18n labels, conjugation, Schema.org mappings, usage examples, documentation |
| Authority wallet | Creates market routing triples + registry triples (reduced scope vs Approach B) |
| i18n | Off-chain registry; same architecture regardless of atom format |
| Migration | Combined: base-form + DefinedTerm in single migration at launch |
| Consistency | Predicates follow the same classification pattern as all other atom types |

The layering:

```
┌─────────────────────────────────────────────────┐
│  Off-chain: i18n labels, conjugation, examples  │  ← rendering
├─────────────────────────────────────────────────┤
│  On-chain triples: market pattern, registry     │  ← routing
├─────────────────────────────────────────────────┤
│  Atom data: DefinedTerm (name + description)    │  ← identity
└─────────────────────────────────────────────────┘
```

Each layer serves a distinct purpose. Each layer is the right weight for what it carries. Nothing is over-engineered. Nothing is under-specified.

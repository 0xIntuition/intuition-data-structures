# Predicate Governance

This document defines how enshrined predicates are proposed, evaluated, approved, and deprecated within the Intuition protocol.

---

## Why Governance Matters

Predicates are permanent. The on-chain atom ID is derived from the exact bytes of the predicate string: `keccak256(ATOM_SALT, keccak256(data))`. Once created, an atom cannot be renamed, modified, or deleted. A poorly chosen predicate lives on-chain forever.

This permanence means predicate enshrinement decisions carry real weight:
- A bad predicate name fragments the graph (people create alternatives)
- A missing predicate forces builders to improvise (inconsistent atoms)
- Too many enshrined predicates dilute focus and burden the tooling layer
- Too few force the community to reinvent what should be shared infrastructure

---

## Enshrinement Criteria

A predicate should be enshrined when it meets **all** of the following:

### 1. Cross-Application Utility

The predicate is useful across multiple applications, not specific to one builder's use case.

- **Enshrine:** `follow` — every social app, every curation tool, every feed
- **Don't enshrine:** `watchlisted in CoinGecko` — specific to one platform

### 2. Semantic Clarity

The predicate's meaning is unambiguous and well-bounded. Two independent developers reading the name would model the same relationships.

- **Enshrine:** `created by` — clear subject-object relationship, clear direction
- **Don't enshrine:** `related to` — too vague; almost anything is "related to" anything

### 3. Market Consolidation Value

The predicate benefits from having one deep market rather than many fragmented ones. This is especially important for depositional predicates where vault TVL is the signal.

- **Enshrine:** `trust` — one shared trust market per entity is maximally useful
- **Don't enshrine:** `slightly positive about` — overlaps with `like`, fragments the positive-sentiment market

### 4. Stability

The predicate name is unlikely to need revision. It follows the naming conventions (base form, lowercase, established vocabulary).

- **Enshrine:** `follow` — stable, well-understood, base form
- **Don't enshrine:** `hodl` — slang that may not age well or translate

### 5. No Existing Coverage

The predicate captures a relationship not already covered by an existing enshrined predicate.

- **Enshrine:** `distrust` — the inverse of `trust`, not expressible via `trust` alone
- **Don't enshrine:** `bookmark` — already covered by `like` or `contain` depending on context

---

## Naming Conventions

All enshrined predicates must follow these rules:

| Rule | Example | Why |
|---|---|---|
| Base verb form | `follow` not `follows` | Works with `I` subject; see `predicate-display-and-conjugation.md` |
| Lowercase | `has tag` not `Has Tag` | Consistent hashing; no case ambiguity |
| No trailing whitespace | `follow` not `follow ` | Different bytes = different atom ID |
| US English spelling | `favorite` not `favourite` | One canonical form per concept |
| No abbreviations | `has description` not `has desc` | Readability; no ambiguity |
| No UI terminology | `like` not `bookmark`/`star`/`favorite` | Predicates are semantic, not visual |
| Multi-word uses spaces | `bullish on` not `bullish-on` | Natural language readability |
| Exception: `has X` compounds | `has tag` not `have tag` | These are fixed labels; see conjugation doc |

---

## Proposal Process

### Who Can Propose

Anyone — core team, integration partners, community members. Proposals are evaluated on merit, not origin.

### Proposal Format

A predicate proposal must include:

```markdown
## Predicate Proposal: [predicate string]

**Canonical string:** `predicate name`
**Market pattern:** Depositional / Attributive / Comparative / Hybrid
**Category:** (Social, Sentiment, Identity, Attribution, etc.)

### Motivation
Why is this predicate needed? What use cases does it enable?
What existing predicates (if any) partially cover this space and why they're insufficient?

### Specification
- Expected subject types:
- Expected object types:
- Example triples:
  - `(Subject, predicate, Object)` — explanation
  - `(Subject, predicate, Object)` — explanation

### Market Design
- If depositional: What does a deposit mean? What signal does TVL provide?
- If attributive: What factual claim does it assert?
- If comparative: What does the paired/inverse market look like?

### Conjugation
- Third-person form for display: (e.g., "follows" for "follow")
- Does it conjugate? (verbs do, phrases don't)

### Trade-offs
- Risk of overlap with existing predicates:
- Risk of fragmentation:
- Any i18n or localization concerns:
```

### Review Process

**Phase 1: Core Team Review (current)**

During the initial launch period, the Intuition core team reviews and approves predicate proposals. This is a pragmatic bootstrapping choice — the team has the deepest context on protocol implications.

Review criteria:
1. Meets all five enshrinement criteria above
2. Follows naming conventions
3. No collision or near-collision with existing enshrined predicates
4. Market pattern is well-defined
5. At least two independent use cases identified

**Phase 2: Community Governance (future)**

As the protocol matures, predicate enshrinement transitions to community governance:
- Proposals submitted on-chain or via a governance forum
- Community discussion period (minimum 2 weeks)
- Token-weighted or reputation-weighted vote
- Approved predicates are created on-chain by a governance multisig

The exact governance mechanism will be defined in a separate governance framework document. The criteria and naming conventions in this document remain the evaluation standard regardless of who decides.

---

## Deprecation

Predicates cannot be removed from the chain. Deprecation means:

1. **The SDK removes or marks the constant as deprecated** with a pointer to the replacement
2. **The indexer continues to index triples using the old predicate** but marks them as using a deprecated predicate
3. **The API returns a `deprecated: true` flag** and a `replacedBy` field pointing to the new predicate
4. **Frontends stop offering the deprecated predicate** in creation UIs but continue displaying existing triples that use it
5. **A `(old_predicate_atom, deprecated by, new_predicate_atom)` triple** is created on-chain to record the deprecation in the graph itself

### Deprecation Criteria

A predicate should be deprecated when:
- A better-named replacement exists and is enshrined
- The predicate's semantics were ambiguous and caused fragmentation in practice
- The predicate violated naming conventions that are now enforced (e.g., third-person form → base form)

### Migration Support

When deprecating a predicate, the team must provide:
- A migration guide mapping old → new (see `predicate-migration-guide.md`)
- A reasonable transition period (minimum 6 months) where both are supported
- SDK tooling that warns on use of deprecated predicates

---

## Authority and Bootstrapping

### Initial Atom Creation

For the production launch, enshrined predicate atoms are created by a team-controlled deployer address. This is a one-time bootstrapping operation.

The deployment should:
1. Create all Tier 1-5 atoms from `enshrined-predicates-launch-set.md` in a single batch transaction (or minimal transactions)
2. Create the `I` atom
3. Record all atom IDs in a verification file committed to this repository
4. Verify every atom ID matches the SDK's computed hash for the same string

### Registry On-Chain

After atom creation, registry triples are created to make the predicate catalog queryable on-chain:

```
(predicate_registry, contain, follow_atom)
(predicate_registry, contain, like_atom)
(predicate_registry, contain, trust_atom)
...
(follow_atom, has type, depositional_predicate)
(like_atom, has type, depositional_predicate)
(created_by_atom, has type, attributive_predicate)
...
```

This registry is itself composed of enshrined predicates (`contain`, `has type`), making the system self-describing.

### Future Multisig

Once community governance is established, the atom creation authority transfers to a governance multisig. The multisig:
- Creates new enshrined predicate atoms after governance approval
- Creates deprecation triples for deprecated predicates
- Updates the on-chain registry

---

## Anti-Patterns

| Anti-Pattern | Why It's Bad | What to Do Instead |
|---|---|---|
| Enshrining predicates "just in case" | Pollutes the namespace; creates maintenance burden | Wait for demonstrated demand from 2+ use cases |
| Enshrining synonyms | `like` + `enjoy` + `appreciate` = three fragmented markets | Pick one canonical term |
| Enshrining domain-specific predicates | `yield farms on` is too narrow for protocol-level support | Let domain apps use it as a community predicate |
| Skipping the proposal process | Creates governance debt; others can't evaluate the decision | Always document the rationale, even for "obvious" additions |
| Deprecating without a migration path | Breaks existing integrations; erodes partner trust | Always provide transition tooling and timeline |

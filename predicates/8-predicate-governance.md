# Predicate Governance

## How Predicates Work in Intuition

Intuition is a global, permissionless, on-chain protocol. Anyone can create any atom and use it as a predicate in a triple. There is no gatekeeper.

Enshrined predicates are not enforced — they are **coordinated**. They represent predicates that the community has agreed on as shared vocabulary: a common name, a clear definition, a known market pattern. When multiple applications use the same predicate atom, their data composes into one unified graph with deep, meaningful markets.

The purpose of predicate governance is coordination, not control:

- **Declaring** which predicates a team or application uses
- **Sharing** predicate definitions so others can adopt them
- **Aligning** on naming conventions to avoid accidental fragmentation
- **Recommending** market patterns (depositional, attributive, comparative) so frontends route users correctly

Nothing here is enforced on-chain. It is all recommendations and coordination.

---

## Thinking About Creating a New Predicate

Before creating a new predicate, work through these questions:

### Does an existing predicate already cover this?

Check the enshrined predicates catalog. Many concepts map to existing predicates:

| You want to express | Existing predicate |
|---|---|
| User saves/bookmarks/favorites something | `like` |
| User subscribes to updates | `follow` |
| User adds an item to a list | `contain` (list as subject) |
| User tags something | `has tag` |
| User thinks X is better than Y | `better than` |
| User rates something positively | `like` or `endorse` (by strength) |

Creating a new predicate when an existing one works fragments the market. Two predicates that mean the same thing split deposits across two vaults.

### Will multiple applications use this?

A predicate that only one application needs is better kept as that application's custom predicate. Enshrinement is for predicates that benefit from cross-application coordination — where it matters that everyone uses the same atom.

### Is the name clear and stable?

Two developers who have never spoken to each other should read the predicate name and model the same relationship. If the name requires explanation or context to understand, it is not ready for enshrinement.

Avoid slang, abbreviations, or terminology that is specific to one community. Predicates are global vocabulary.

### What is the market pattern?

Every predicate has a market pattern that determines how frontends should route users:

| Pattern | Subject | Example | What deposits mean |
|---|---|---|---|
| **Depositional** | `I` | `(I, follow, Vitalik)` | "I make this claim about myself" — many depositors, one shared vault |
| **Attributive** | Specific entity | `(Ethereum, created by, Vitalik)` | "I agree this fact is true" — deposits confirm the claim |
| **Comparative** | Entity being ranked | `(Rust, better than, Solidity)` | "I agree with this comparison" — check the inverse for the full picture |

If you cannot clearly state the market pattern, the predicate's semantics are not well-defined enough.

---

## Minimum Requirements for a Predicate

Every predicate — enshrined or custom — needs at minimum:

### 1. Name

The canonical string stored as (or within) the atom data. Must follow the naming conventions below.

### 2. Description

One sentence explaining what the predicate means. This should be sufficient for a developer who has never seen the predicate before to understand the relationship it expresses.

Good: *"Directional subscription or tracking of the object entity"*
Bad: *"Follow"* (restates the name, adds nothing)
Bad: *"When a user clicks the follow button on someone's profile, this predicate is used to record that relationship in the social graph"* (describes UI, not semantics)

### 3. Market Pattern

Depositional, attributive, or comparative. This determines how the predicate should be used in triples and how frontends should route deposits.

### 4. Example Triples

At least two concrete examples showing the predicate in use:

```
(I, follow, Vitalik)                    — depositional: "I follow Vitalik"
(Alice, follow, Ethereum Foundation)    — attributive: claiming Alice follows EF
```

### 5. Conjugation Note

Does the predicate conjugate for third-person subjects? If so, what is the display form?

- `follow` → `follows` (conjugates)
- `bullish on` → `bullish on` (does not conjugate)

---

## Naming Conventions

| Rule | Example | Why |
|---|---|---|
| Base verb form | `follow` not `follows` | Works with `I` subject; see `3.predicate-display-and-conjugation.md` |
| Lowercase | `has tag` not `Has Tag` | Consistent hashing; no case ambiguity |
| No trailing whitespace | `follow` not `follow ` | Different bytes = different atom ID |
| US English spelling | `favorite` not `favourite` | One canonical form per concept |
| No abbreviations | `has description` not `has desc` | Readability; no ambiguity |
| No UI terminology | `like` not `bookmark`/`star`/`favorite` | Predicates are semantic, not visual |
| Multi-word uses spaces | `bullish on` not `bullish-on` | Natural language readability |
| Exception: `has X` compounds | `has tag` not `have tag` | Fixed compound labels |

---

## The Enshrined Predicates Repository

Predicate governance happens on GitHub through a public repository. Anyone can contribute.

### Repository Structure

```
predicates/
├── enshrined/
│   ├── follow.md
│   ├── like.md
│   ├── trust.md
│   ├── contain.md
│   └── ...
├── proposed/
│   ├── donated-to.md
│   └── ...
├── deprecated/
│   └── ...
└── registry.json          ← machine-readable index of all enshrined predicates
```

Each predicate file follows a standard template (see below). The `registry.json` file is auto-generated from the individual predicate files and used by SDKs and indexers.

### Predicate File Template

Every predicate — enshrined or proposed — is documented in a single markdown file:

```markdown
# follow

## Definition

| Field | Value |
|---|---|
| **Name** | `follow` |
| **Description** | Directional subscription or tracking of the object entity |
| **Market Pattern** | Depositional |
| **Conjugates** | Yes → `follows` |
| **Category** | Social |
| **Status** | Enshrined |

## Usage

### Example Triples

- `(I, follow, Vitalik)` — "I follow Vitalik." Depositional: deposit = follow signal.
- `(I, follow, DeFi Blue Chips)` — "I follow this stack." Works for any entity type.

### When to Use

Use `follow` when the depositor wants to express ongoing interest in or
subscription to the object entity. The vault's depositor count = follower
count. TVL = conviction-weighted follower strength.

### When NOT to Use

- Do not use `follow` for one-time positive signals — use `like` instead.
- Do not use `follow` for strong endorsements — use `endorse` instead.
- Do not create `(Alice, follow, Bob)` when Alice is the current user — use
  `(I, follow, Bob)` to consolidate the market.

## Schema.org Mapping

`https://schema.org/FollowAction`

## Atom Data

### Inline JSON (Approach C)

\```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "follow",
  "description": "Directional subscription or tracking of the object entity"
}
\```

### IPFS Document (Approach D)

CID: `bafyreif7x...` (once published)
```

### Contributing: How to Propose a New Predicate

1. **Fork the repository.**

2. **Create a new file** in the `proposed/` directory using the predicate file template above. Name the file after the predicate: `proposed/donated-to.md`.

3. **Fill in all required fields.** At minimum: name, description, market pattern, category, example triples, and when to use / when not to use.

4. **Open a pull request.** The PR description should explain:
   - Why this predicate is needed
   - What applications or use cases will use it
   - Whether any existing enshrined predicates partially cover the same ground, and why they are insufficient

5. **Community review.** The PR is open for discussion. Other builders, the core team, and community members can comment, suggest changes, or raise concerns. There is no fixed review period — the PR stays open until there is rough consensus.

6. **Merge.** When the predicate is accepted, the file moves from `proposed/` to `enshrined/`, the `registry.json` is updated, and the predicate atom is created on-chain.

### Contributing: Updating an Existing Predicate

Predicate names and descriptions are frozen once enshrined (the atom data is immutable on-chain). But the documentation around a predicate — usage guidance, examples, when-to-use notes — can always be improved.

Open a PR editing the predicate's file in `enshrined/`. These changes are low-ceremony and can be merged quickly.

### Contributing: Proposing Deprecation

If a predicate should be deprecated in favor of a better alternative, open a PR that:
1. Moves the predicate file from `enshrined/` to `deprecated/`
2. Adds a `Replaced By` field pointing to the replacement predicate
3. Explains why the deprecation is warranted

Deprecation PRs require more scrutiny since they affect existing integrations.

---

## The Registry File

The `registry.json` file is a machine-readable index of all enshrined predicates. SDKs and indexers consume this file to stay in sync with the canonical predicate set.

```json
{
  "version": "1.0.0",
  "predicates": [
    {
      "name": "follow",
      "description": "Directional subscription or tracking of the object entity",
      "marketPattern": "depositional",
      "conjugates": true,
      "thirdPerson": "follows",
      "category": "social",
      "status": "enshrined",
      "schemaOrg": "https://schema.org/FollowAction"
    },
    {
      "name": "like",
      "description": "Lightweight positive signal toward the object entity",
      "marketPattern": "depositional",
      "conjugates": true,
      "thirdPerson": "likes",
      "category": "social",
      "status": "enshrined"
    }
  ]
}
```

This file is the source from which the SDK generates predicate constants, atom data, and atom IDs. When a predicate is enshrined, it is added here. When deprecated, its status changes to `"deprecated"` with a `"replacedBy"` field.

---

## Enshrinement Criteria

A predicate should be enshrined when it meets **all** of the following:

### 1. Cross-Application Utility

The predicate is useful across multiple applications, not specific to one builder's use case.

### 2. Semantic Clarity

The predicate's meaning is unambiguous. Two independent developers reading the name would model the same relationships.

### 3. Market Consolidation Value

The predicate benefits from having one deep market rather than many fragmented ones. Multiple applications using the same predicate atom produce better signal than each application inventing its own.

### 4. Stability

The predicate name is unlikely to need revision. It follows the naming conventions and uses established vocabulary.

### 5. No Existing Coverage

The predicate captures a relationship not already covered by an existing enshrined predicate.

---

## Deprecation

Predicates cannot be removed from the chain. Deprecation means the community recommends against using the predicate for new triples, while existing triples remain valid and indexed.

When a predicate is deprecated:

1. The predicate file moves from `enshrined/` to `deprecated/` in the repository
2. The `registry.json` status changes to `"deprecated"` with a `"replacedBy"` reference
3. The SDK marks the constant as deprecated with a pointer to the replacement
4. A `(old_predicate_atom, deprecated by, new_predicate_atom)` triple is created on-chain to record the deprecation in the graph
5. Frontends stop offering the deprecated predicate in creation UIs but continue displaying existing triples

---

## On-Chain Registry

The GitHub repository is the coordination layer. The on-chain registry is the verifiable record.

When a predicate is enshrined, two things happen on-chain:

```
(predicate_registry, contain, follow_atom)       ← registry membership
(follow_atom, has type, depositional_atom)        ← market routing
```

These triples make the enshrined predicate set queryable on-chain. Any application can read the registry without depending on GitHub, the SDK, or any API.

The on-chain registry is updated by a team-controlled deployer address at launch, with a path toward community governance (multisig, then DAO) as the protocol matures. The GitHub repository remains the coordination and discussion layer regardless of who controls the on-chain deployer.

---

## Anti-Patterns

| Anti-Pattern | Why It's Bad | What to Do Instead |
|---|---|---|
| Enshrining predicates "just in case" | Pollutes the namespace; creates maintenance burden | Wait for demonstrated demand from 2+ use cases |
| Enshrining synonyms | `like` + `enjoy` + `appreciate` = three fragmented markets | Pick one canonical term |
| Enshrining domain-specific predicates | `yield farms on` is too narrow for cross-app use | Keep it as an application-level custom predicate |
| Creating a predicate without checking for overlap | Fragments an existing market | Always check the catalog first |
| Skipping documentation | Others cannot evaluate or adopt the predicate | Every predicate needs at minimum: name, description, market pattern, examples |

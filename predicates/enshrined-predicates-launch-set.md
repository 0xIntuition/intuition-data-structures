# Enshrined Predicates — Launch Set

This document defines the canonical predicates. These are the predicates that Intuition commits to supporting as first-class protocol primitives. All other predicates in the 100-predicate catalog (`predicate-analysis.md`) are valid but not enshrined at launch.

**Enshrined means:**
- The atom exists on-chain with a known, documented atom ID
- The SDK exports the predicate constant
- The indexer recognizes and indexes triples using this predicate
- Frontends receive display metadata (label, conjugation, market pattern) from the API
- The predicate has a defined market pattern (depositional, attributive, or hybrid)

**Not enshrined means:**
- Anyone can still create atoms with any string and use them as predicates
- The protocol doesn't prevent it, but the ecosystem doesn't guarantee tooling support
- Community-created predicates can be proposed for future enshrinement (see `predicate-governance.md`)

---

## Decision Record

**Canonical form:** All predicates use **base form** verbs (`follow`, not `follows`). See `predicate-display-and-conjugation.md` for the rationale and how the display layer handles conjugation.

**Representation:** Plain UTF-8 strings stored as atom data. See `predicate-analysis.md` § Representation Trade-Offs for why plain strings over JSON-LD or IPFS CIDs.

**The `I` Atom:** Depositional predicates use the singleton `I` atom as the subject. The depositor's on-chain address resolves who "I" is. See `predicate-usage-by-entity-type.md` § The `I` Atom.

---

## Launch Set

### Tier 1: Core (Must Have for Stacks Launch)

These predicates are required for the Stacks application to function. Every Stacks deployment must create these atoms.

#### Curation and Collection

| # | Predicate | Market Pattern | Primary Use | Example Triple |
|---|---|---|---|---|
| 1 | `contain` | Attributive | Adding items to a Stack | `(DeFi Blue Chips, contain, Aave)` |
| 2 | `curated by` | Attributive | Stack ownership/authorship | `(DeFi Blue Chips, curated by, Alice)` |

#### Identity and Classification

| # | Predicate | Market Pattern | Primary Use | Example Triple |
|---|---|---|---|---|
| 3 | `is` | Attributive | Type or identity assertion | `(Uniswap, is, DEX)` |
| 4 | `has tag` | Attributive | Free-form tagging | `(Aave, has tag, lending)` |
| 5 | `has type` | Attributive | Formal classification | `(MetaMask, has type, wallet)` |

#### Social

| # | Predicate | Market Pattern | Primary Use | Example Triple |
|---|---|---|---|---|
| 6 | `follow` | Depositional | Following people, stacks, entities | `(I, follow, Vitalik)` |
| 7 | `like` | Depositional | Lightweight positive signal | `(I, like, Uniswap v4)` |

#### Metadata

| # | Predicate | Market Pattern | Primary Use | Example Triple |
|---|---|---|---|---|
| 8 | `has description` | Attributive | Textual description | `(DeFi Blue Chips, has description, "Top DeFi protocols")` |
| 9 | `url` | Attributive | Canonical web address | `(Uniswap, url, uniswap.org)` |
| 10 | `imgUrl` | Attributive | Visual representation | `(Aave, imgUrl, aave-logo.svg)` |

### Tier 2: Social and Reputation (Launch)

These predicates enable the social layer that makes Stacks valuable beyond simple curation.

| # | Predicate | Market Pattern | Primary Use | Example Triple |
|---|---|---|---|---|
| 11 | `trust` | Depositional | Positive trust signal | `(I, trust, Trail of Bits)` |
| 12 | `distrust` | Depositional | Negative trust signal | `(I, distrust, Scam Project)` |
| 13 | `endorse` | Depositional | Strong public support | `(I, endorse, EIP-4844)` |
| 14 | `recommend` | Depositional | Active recommendation | `(I, recommend, Foundry)` |
| 15 | `vouch for` | Depositional | Personal credibility stake | `(I, vouch for, Alice)` |

### Tier 3: Sentiment and Opinion (Launch)

These enable the market-as-signal primitives that differentiate Intuition.

| # | Predicate | Market Pattern | Primary Use | Example Triple |
|---|---|---|---|---|
| 16 | `agree with` | Depositional | Position alignment | `(I, agree with, EIP-4844)` |
| 17 | `disagree with` | Depositional | Position opposition | `(I, disagree with, PoW Revival)` |
| 18 | `bullish on` | Depositional | Positive conviction | `(I, bullish on, Ethereum)` |
| 19 | `bearish on` | Depositional | Negative conviction | `(I, bearish on, Memecoins)` |

### Tier 4: Attribution and Facts (Launch)

These enable the knowledge graph layer — factual claims about entities.

| # | Predicate | Market Pattern | Primary Use | Example Triple |
|---|---|---|---|---|
| 20 | `created by` | Attributive | Origin attribution | `(Ethereum, created by, Vitalik)` |
| 21 | `same as` | Attributive | Cross-representation identity | `(ETH, same as, Ether)` |
| 22 | `linked account` | Attributive | Platform identity linkage | `(Alice, linked account, x.com/alice)` |
| 23 | `has category` | Attributive | Categorical grouping | `(Chainlink, has category, oracle)` |

### Tier 5: Comparison (Launch)

These create the head-to-head opinion markets.

| # | Predicate | Market Pattern | Comparative | Example Triple |
|---|---|---|---|---|
| 24 | `better than` | Comparative | Subjective superiority | `(Rust, better than, Solidity)` |
| 25 | `alternative to` | Comparative | Substitutability | `(Solana, alternative to, Ethereum)` |

---

## Special Atoms

These atoms are not predicates but are required for the predicate system to function.

| Atom | Data | Purpose |
|---|---|---|
| `I` | `"I"` | Universal subject for depositional triples |

---

## Phase 2 Candidates

These predicates are well-defined in the catalog but not required for the initial Stacks launch. They will be enshrined based on usage demand and partner feedback.

| Predicate | Market Pattern | Why Phase 2 |
|---|---|---|
| `support` / `oppose` | Depositional | Governance-adjacent; wait for DAO integration demand |
| `voted for` / `voted against` | Depositional | Requires governance proposal atoms |
| `delegated to` | Depositional | Requires governance framework |
| `skeptical of` / `neutral on` | Depositional | Lower-priority sentiment signals |
| `expert in` | Depositional | Expertise claims need reputation framework |
| `audited by` / `verified by` | Attributive | Security-specific; wait for auditor partnerships |
| `forked from` / `derived from` | Attributive | Lineage; wait for protocol history use cases |
| `depend on` / `use` | Attributive | Technical relationships; developer-tool focused |
| `implement` | Attributive | Standard compliance; developer-focused |
| `compete with` | Comparative | Wait for more comparative market demand |
| `member of` / `employed by` | Hybrid | Privacy considerations; needs ZK layer |
| `authored by` | Attributive | Content attribution; wait for content platform integrations |
| `blocked` / `reported` | Depositional | Moderation primitives; needs policy framework |
| `invested in` / `staked in` | Depositional | Financial signals; regulatory considerations |
| `listed on` / `available on` | Attributive | Exchange/platform metadata |
| `backed by` / `pegged to` | Attributive | DeFi-specific economic relationships |
| `instance of` / `subclass of` | Attributive | Taxonomy; wait for ontology tooling |
| `preceded by` / `followed by` | Attributive | Temporal ordering; event-specific |
| `supersede` / `replaced by` / `deprecated by` | Attributive | Versioning; wait for lifecycle tooling |
| `pinned in` / `featured in` | Attributive | Stack promotion; UX design not finalized |
| `ranked above` | Comparative | Explicit ordering; complex UX |

---

## Atom Creation Checklist

Before production launch, the following must be completed for every Tier 1-5 predicate:

- [ ] Atom created on-chain with canonical base-form string
- [ ] Atom ID recorded and verified against SDK hash computation
- [ ] SDK constant exported (`FOLLOW`, `LIKE`, `TRUST`, etc.)
- [ ] Indexer configured to recognize and index the predicate atom
- [ ] API returns predicate metadata including market pattern and display info
- [ ] Conjugation entry added to SDK rendering helper (see `predicate-display-and-conjugation.md`)
- [ ] Localization entry added for English (see `predicate-i18n-strategy.md`)
- [ ] `I` atom created and verified

---

## What This Document Is NOT

- **Not a restriction.** Anyone can create predicates with any string. This document defines what the ecosystem officially supports.
- **Not permanent.** New predicates can be enshrined via the governance process (see `predicate-governance.md`). But enshrined predicates cannot be un-enshrined — only deprecated.
- **Not the full catalog.** The 100-predicate catalog in `predicate-analysis.md` contains the complete taxonomy. This is the production-ready subset.

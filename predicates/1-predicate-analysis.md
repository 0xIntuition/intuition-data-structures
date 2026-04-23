# Canonical Predicate Catalog Analysis

## Canonical Launch Predicate Catalog

The launch catalog currently contains 97 predicates after removing overloaded/de-duplicated identity predicates (`is`, `alias of`, `instance of`, and `subclass of`). Use `has type`, `same as`, `has tag`, and `has category` for the remaining identity/classification cases.

### Identity and Classification (1-4)

| # | Predicate | Intent | Typical Triple Pattern |
|---|---|---|---|
| 1 | `has type` | Formal taxonomy or defined-term classification | `(Uniswap, has type, Decentralized Exchange)` |
| 2 | `same as` | Cross-representation identity and duplicate collapsing | `(ETH, same as, Ether)` |
| 3 | `has tag` | Free-form tagging | `(Aave, has tag, lending)` |
| 4 | `has category` | Product-level browsable grouping | `(Uniswap, has category, DeFi)` |

### Social and Reputation (5-14)

| # | Predicate | Intent | Typical Triple Pattern |
|---|---|---|---|
| 5 | `follow` | Unidirectional subscription | `(I, follow, Vitalik)` |
| 6 | `like` | Lightweight positive signal | `(I, like, Ethereum)` |
| 7 | `endorse` | Strong public support | `(I, endorse, EIP-4844)` |
| 8 | `trust` | Positive trust assertion | `(I, trust, Auditor X)` |
| 9 | `distrust` | Negative trust assertion | `(I, distrust, Scam Project)` |
| 10 | `reviewed` | Review authorship | `(I, reviewed, Uniswap v4)` |
| 11 | `recommend` | Active recommendation | `(I, recommend, Hardhat)` |
| 12 | `reported` | Flagging for violation | `(I, reported, Phishing Site)` |
| 13 | `blocked` | Exclusion from view | `(I, blocked, Spam Account)` |
| 14 | `vouch for` | Personal credibility stake | `(I, vouch for, Bob)` |

### Curation and Containment (15-22)

| # | Predicate | Intent | Typical Triple Pattern |
|---|---|---|---|
| 15 | `contain` | Collection membership | `(L1 Watchlist, contain, Ethereum)` |
| 16 | `listed in` | Reverse collection membership | `(Ethereum, listed in, L1 Watchlist)` |
| 17 | `curated by` | Collection ownership | `(DeFi Blue Chips, curated by, Alice)` |
| 18 | `pinned in` | Highlighted in collection | `(Ethereum, pinned in, L1 Watchlist)` |
| 19 | `featured in` | Editorially promoted | `(Uniswap, featured in, Top DEXs)` |
| 20 | `ranked above` | Explicit ordering | `(Ethereum, ranked above, Solana)` |
| 21 | `depend on` | Functional dependency | `(Arbitrum, depend on, Ethereum)` |
| 22 | `alternative to` | Substitutability | `(Solana, alternative to, Ethereum)` |

### Authorship and Contribution (23-28)

| # | Predicate | Intent | Typical Triple Pattern |
|---|---|---|---|
| 23 | `created by` | Origin attribution | `(Ethereum, created by, Vitalik Buterin)` |
| 24 | `authored by` | Written content attribution | `(Whitepaper, authored by, Satoshi)` |
| 25 | `contributed to` | Contribution record | `(I, contributed to, OpenZeppelin)` |
| 26 | `forked from` | Divergent copy | `(Sushiswap, forked from, Uniswap)` |
| 27 | `derived from` | Adaptation or build-upon | `(Optimism, derived from, Ethereum)` |
| 28 | `inspired by` | Creative influence | `(Solana, inspired by, PBFT)` |

### Metadata and Linking (29-36)

| # | Predicate | Intent | Typical Triple Pattern |
|---|---|---|---|
| 29 | `linked account` | Platform identity linkage | `(Alice, linked account, x.com/alice)` |
| 30 | `url` | Canonical web address | `(Ethereum, url, ethereum.org)` |
| 31 | `imgUrl` | Image reference (legacy) | `(Ethereum, imgUrl, eth-logo.png)` |
| 32 | `has description` | Textual description | `(Ethereum, has description, "A decentralized...")` |
| 33 | `has source` | Authoritative reference | `(EIP-4844, has source, eips.ethereum.org/...)` |
| 34 | `published at` | Publication venue | `(Whitepaper, published at, bitcoin.org)` |
| 35 | `located in` | Geographic or logical location | `(Devcon, located in, Bangkok)` |
| 36 | `available on` | Platform availability | `(USDC, available on, Ethereum)` |

### Affiliation and Membership (37-42)

| # | Predicate | Intent | Typical Triple Pattern |
|---|---|---|---|
| 37 | `member of` | Organization membership | `(I, member of, Ethereum Foundation)` |
| 38 | `employed by` | Employment relationship | `(I, employed by, Uniswap Labs)` |
| 39 | `founded` | Founder attribution | `(Vitalik, founded, Ethereum)` |
| 40 | `affiliated with` | General association | `(Protocol X, affiliated with, a16z)` |
| 41 | `partner of` | Formal partnership | `(Chainlink, partner of, SWIFT)` |
| 42 | `invested in` | Financial investment | `(a16z, invested in, Uniswap)` |

### Domain-Specific Knowledge (43-47)

| # | Predicate | Intent | Typical Triple Pattern |
|---|---|---|---|
| 43 | `use` | Technology utilization | `(Aave, use, Chainlink)` |
| 44 | `compatible with` | Interoperability | `(MetaMask, compatible with, Ethereum)` |
| 45 | `governed by` | Governance authority | `(Uniswap, governed by, UNI holders)` |
| 46 | `priced in` | Denomination currency | `(NFT Collection, priced in, ETH)` |
| 47 | `implement` | Standard implementation | `(USDC, implement, ERC-20)` |

### Sentiment and Opinion (48-55)

| # | Predicate | Intent | Typical Triple Pattern |
|---|---|---|---|
| 48 | `agree with` | Alignment of position | `(I, agree with, EIP-4844)` |
| 49 | `disagree with` | Opposition of position | `(I, disagree with, PoW Revival)` |
| 50 | `support` | Active backing of a cause or proposal | `(I, support, MiCA regulation)` |
| 51 | `oppose` | Active resistance to a cause or proposal | `(I, oppose, PoS transition)` |
| 52 | `skeptical of` | Cautious doubt without full rejection | `(I, skeptical of, Restaking)` |
| 53 | `bullish on` | Positive conviction about future value | `(I, bullish on, Ethereum)` |
| 54 | `bearish on` | Negative conviction about future value | `(I, bearish on, Memecoins)` |
| 55 | `neutral on` | Explicit non-position | `(I, neutral on, L2 wars)` |

### Comparison and Ranking (56-63)

| # | Predicate | Intent | Typical Triple Pattern |
|---|---|---|---|
| 56 | `better than` | Subjective superiority claim | `(Rust, better than, Solidity)` |
| 57 | `worse than` | Subjective inferiority claim | `(PoW, worse than, PoS)` |
| 58 | `equivalent to` | Functional parity | `(USDC, equivalent to, USDT)` |
| 59 | `compete with` | Direct market competition | `(Uniswap, compete with, Curve)` |
| 60 | `outperform` | Measurable superiority | `(Solana, outperform, Ethereum)` |
| 61 | `supersede` | Replacement of a predecessor | `(Uniswap v4, supersede, Uniswap v3)` |
| 62 | `predecessor of` | Versioning lineage | `(Uniswap v2, predecessor of, Uniswap v3)` |
| 63 | `successor of` | Forward version link | `(Uniswap v3, successor of, Uniswap v2)` |

### Knowledge and Expertise (64-71)

| # | Predicate | Intent | Typical Triple Pattern |
|---|---|---|---|
| 64 | `expert in` | Domain expertise claim | `(I, expert in, ZK proofs)` |
| 65 | `learned from` | Knowledge attribution | `(I, learned from, Bob)` |
| 66 | `teach` | Knowledge dissemination | `(I, teach, Solidity)` |
| 67 | `studied` | Learning engagement | `(I, studied, cryptography)` |
| 68 | `certified by` | Credential attestation | `(I, certified by, Ethereum Foundation)` |
| 69 | `mentor of` | Mentorship relationship | `(I, mentor of, Alice)` |
| 70 | `student of` | Apprenticeship relationship | `(I, student of, Bob)` |
| 71 | `speak` | Language or communication capability | `(I, speak, Rust)` |

### Provenance and Evidence (72-79)

| # | Predicate | Intent | Typical Triple Pattern |
|---|---|---|---|
| 72 | `verified by` | Third-party verification | `(Smart Contract, verified by, CertiK)` |
| 73 | `audited by` | Security or financial audit | `(Aave v3, audited by, Trail of Bits)` |
| 74 | `attested by` | Witness or attestation | `(Credential, attested by, Issuer)` |
| 75 | `cited by` | Academic or reference citation | `(Bitcoin Whitepaper, cited by, Ethereum Whitepaper)` |
| 76 | `reference` | Forward citation or mention | `(Ethereum Whitepaper, reference, Bitcoin Whitepaper)` |
| 77 | `evidenced by` | Supporting proof or data | `(Claim, evidenced by, On-chain Proof)` |
| 78 | `disputed by` | Challenge to a claim | `(Claim, disputed by, Counter-evidence)` |
| 79 | `confirmed by` | Corroboration of a claim | `(Claim, confirmed by, Independent Source)` |

### Temporal and Lifecycle (80-85)

| # | Predicate | Intent | Typical Triple Pattern |
|---|---|---|---|
| 80 | `preceded by` | Temporal ordering | `(Merge, preceded by, Beacon Chain launch)` |
| 81 | `followed by` | Forward temporal link | `(Beacon Chain launch, followed by, Merge)` |
| 82 | `enabled by` | Causal enablement | `(DeFi Summer, enabled by, Compound governance)` |
| 83 | `triggered` | Causal initiation | `(Terra collapse, triggered, Contagion)` |
| 84 | `deprecated by` | Formal deprecation | `(ERC-20 approve, deprecated by, ERC-20 permit)` |
| 85 | `replaced by` | Full substitution | `(Sushiswap Chef, replaced by, MasterChefV2)` |

### Governance and Policy (86-91)

| # | Predicate | Intent | Typical Triple Pattern |
|---|---|---|---|
| 86 | `voted for` | Governance vote in favor | `(I, voted for, Proposal 42)` |
| 87 | `voted against` | Governance vote against | `(I, voted against, Proposal 43)` |
| 88 | `delegated to` | Governance delegation | `(I, delegated to, Bob)` |
| 89 | `proposed` | Proposal authorship | `(Alice, proposed, EIP-7702)` |
| 90 | `regulated by` | Regulatory jurisdiction | `(USDC, regulated by, SEC)` |
| 91 | `compliant with` | Regulatory compliance | `(Exchange X, compliant with, MiCA)` |

### Economic and Market (92-97)

| # | Predicate | Intent | Typical Triple Pattern |
|---|---|---|---|
| 92 | `backed by` | Collateral or backing relationship | `(DAI, backed by, ETH)` |
| 93 | `pegged to` | Price peg relationship | `(USDC, pegged to, USD)` |
| 94 | `listed on` | Exchange or marketplace listing | `(ETH, listed on, Coinbase)` |
| 95 | `sponsored by` | Financial sponsorship | `(Devcon, sponsored by, Ethereum Foundation)` |
| 96 | `reward` | Incentive distribution | `(Aave, reward, Liquidity Providers)` |
| 97 | `staked in` | Staking relationship | `(I, staked in, Ethereum Beacon Chain)` |

---

## Schema.org Action Mapping

This table maps Schema.org Action types to Intuition predicate strings. The Action hierarchy is a useful reference for choosing predicate names and understanding semantic boundaries, but the Action types themselves should not be used as on-chain atom data.

### InteractAction Family

| Schema.org Action | Intuition Predicate | Notes |
|---|---|---|
| `FollowAction` | `follow` | Unidirectional, active interest. Schema.org distinguishes this from SubscribeAction (passive) but that distinction belongs at the app layer. |
| `BefriendAction` | `connected with` | Reciprocal connection. Not in the top 50 — most on-chain graphs are directional. If needed, model as two directional triples. |
| `SubscribeAction` | `follow` | Schema.org treats this as passive reception vs active polling. In a knowledge graph, both are "follow". Product behavior (push vs pull) is app-specific. |
| `JoinAction` | `member of` | The triple records the resulting state, not the join event. |
| `LeaveAction` | *(none)* | Leaving is the absence of the `member of` triple or a counter-triple. Not a standalone predicate. |
| `RegisterAction` | *(none)* | Registration is a one-time event, not a durable relationship. |
| `UnRegisterAction` | *(none)* | Same reasoning. |
| `MarryAction` | *(none)* | Too domain-specific for core predicates. |
| `CommunicateAction` | *(none)* | Communication is ephemeral. Triples record durable knowledge. |

### AssessAction Family

| Schema.org Action | Intuition Predicate | Notes |
|---|---|---|
| `ReactAction` | `like` | Lightweight positive signal. Schema.org's LikeAction is a sub-type of ReactAction. |
| `ReviewAction` | `reviewed` | The triple asserts the review relationship. Review content lives in the object atom or enrichment. |
| `EndorseAction` | `endorse` | Stronger than `like`. Schema.org places this under AssessAction. |
| `ChooseAction` | *(none)* | Selection is a transient decision, not a graph relationship. |
| `IgnoreAction` | `blocked` | Closest durable analog. `blocked` is the on-chain record of exclusion. |
| `DislikeAction` | `distrust` | Negative assessment. `distrust` is more useful in a reputation graph than a generic "dislike". |

### OrganizeAction Family

| Schema.org Action | Intuition Predicate | Notes |
|---|---|---|
| `BookmarkAction` | `contain` | Bookmarking is adding to a personal collection. Model as `(my_collection, contain, item)`. |
| `PlanAction` | *(none)* | Planning is temporal, not a graph relationship. |
| `AllocateAction` | *(none)* | Resource allocation is transactional. |
| `ApplyAction` | *(none)* | Application is an event. |

### CreateAction Family

| Schema.org Action | Intuition Predicate | Notes |
|---|---|---|
| `CreateAction` | `created by` | Inverse direction: the triple is `(thing, created by, actor)`. |
| `WriteAction` | `authored by` | For written content specifically. |
| `DrawAction` / `PaintAction` / `PhotographAction` / `FilmAction` | `created by` | All collapse to `created by` in a general knowledge graph. Domain-specific apps may use more specific predicates. |

### TradeAction Family

| Schema.org Action | Intuition Predicate | Notes |
|---|---|---|
| `BuyAction` / `SellAction` | `invested in` | For recording the relationship, not the transaction. |
| `DonateAction` | `donated to` | Not in the top 50 but a natural extension. |
| `TipAction` | *(none)* | Tipping is transactional, not relational. |
| `RentAction` | *(none)* | Rental is temporal. |

### TransferAction Family

| Schema.org Action | Intuition Predicate | Notes |
|---|---|---|
| `SendAction` | *(none)* | Transfer events belong in transaction history, not the knowledge graph. |
| `GiveAction` | `donated to` | If the gift is durable and notable. |
| `LendAction` / `BorrowAction` | *(none)* | Temporal financial relationships. |

### AchieveAction Family

| Schema.org Action | Intuition Predicate | Notes |
|---|---|---|
| `WinAction` | `outperform` | Durable performance superiority, not a single-event win. |
| `LoseAction` | `worse than` | Relative positioning in the graph. |
| `TieAction` | `equivalent to` | Functional parity assertion. |

### UpdateAction Family

| Schema.org Action | Intuition Predicate | Notes |
|---|---|---|
| `ReplaceAction` | `replaced by` / `supersede` | Forward and backward version links. The triple records the resulting state. |
| `AddAction` | `contain` | Adding to a collection maps to the existing containment predicate. |
| `DeleteAction` | *(none)* | Deletion is the absence of a triple, not a predicate. |

### Schema.org Property Mappings (Non-Action)

Many predicates in 51-100 map more naturally to Schema.org properties than to Action types.

| Schema.org Property | Intuition Predicate | Notes |
|---|---|---|
| `schema:knows` | `expert in` / `learned from` | Schema.org models interpersonal knowledge; Intuition splits into expertise and attribution. |
| `schema:alumniOf` | `studied` / `student of` | Educational lineage. |
| `schema:award` | `certified by` | Credential attestation maps loosely. |
| `schema:hasCredential` | `certified by` | Direct credential link. |
| `schema:isRelatedTo` | `related` (existing auxiliary) | Broad association when nothing more specific fits. |
| `schema:competitor` | `compete with` | Direct market competition. |
| `schema:predecessorOf` | `predecessor of` | Version lineage. |
| `schema:successorOf` | `successor of` | Forward version link. |
| `schema:citation` | `cited by` / `reference` | Forward and backward citation links. |
| `schema:sponsor` | `sponsored by` | Financial sponsorship. |
| `schema:funder` | `backed by` / `invested in` | Financial backing — `backed by` for collateral, `invested in` for equity. |
| `schema:teaches` | `teach` | Knowledge dissemination. |
| `schema:learner` | `student of` | Learning relationship. |
| `schema:legislationAppliedBy` | `regulated by` | Regulatory jurisdiction. |
| `schema:endorsee` | `endorse` / `support` | `endorse` for entity quality, `support` for causes and proposals. |
| `Wikidata:P1552 (has quality)` | `bullish on` / `bearish on` / `skeptical of` | Sentiment predicates have no Schema.org equivalent — they are Intuition-native social graph primitives. |
| `Wikidata:P1269 (facet of)` | `enabled by` / `triggered` | Causal relationships are modeled as directional predicates. |

---

## DefinedTerm Atom Schemas

Each entry below documents the canonical predicate as a minimal Schema.org `DefinedTerm`. The predicates package uses deterministic inline `DefinedTerm` JSON for canonical atom identity and may publish richer IPFS documents as optional enrichment.

### Identity and Classification

#### `has type`

```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "has type",
  "description": "Classifies the subject under a formal taxonomy or defined-term object.",
  "sameAs": ["https://www.wikidata.org/wiki/Property:P31"]
}
```

#### `same as`

```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "same as",
  "description": "Declares that the subject and object refer to the same real-world entity across different representations or naming systems.",
  "sameAs": ["https://schema.org/sameAs", "https://www.w3.org/2002/07/owl#sameAs"]
}
```

#### `has tag`

```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "has tag",
  "description": "Assigns a reusable tag or keyword atom to the subject for filtering, clustering, and discovery.",
  "sameAs": ["https://schema.org/keywords"]
}
```

#### `has category`

```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "has category",
  "description": "Places the subject in a product-level browsable category for user-facing discovery and filtering.",
  "sameAs": ["https://schema.org/category"]
}
```

### Social and Reputation

#### `follow`

```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "follow",
  "description": "The subject chooses to subscribe to or track updates from the object entity. Unidirectional and non-reciprocal.",
  "sameAs": ["https://schema.org/FollowAction"]
}
```

#### `like`

```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "like",
  "description": "Expresses lightweight positive endorsement of the object by the subject.",
  "sameAs": ["https://schema.org/LikeAction"]
}
```

#### `endorse`

```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "endorse",
  "description": "A stronger-than-like signal indicating the subject publicly supports or vouches for the object's quality or legitimacy.",
  "sameAs": ["https://schema.org/EndorseAction"]
}
```

#### `trust`

```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "trust",
  "description": "The subject asserts positive trust in the object. A first-class reputation primitive for web-of-trust graphs."
}
```

#### `distrust`

```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "distrust",
  "description": "The subject asserts negative trust in the object. The inverse of 'trust' — enables negative reputation signals.",
  "sameAs": ["https://schema.org/DislikeAction"]
}
```

#### `reviewed`

```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "reviewed",
  "description": "The subject has authored a review or evaluation of the object.",
  "sameAs": ["https://schema.org/ReviewAction"]
}
```

#### `recommend`

```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "recommend",
  "description": "The subject actively recommends the object to others in the ecosystem. Stronger than 'like', weaker than 'endorse'."
}
```

#### `reported`

```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "reported",
  "description": "The subject has flagged the object for policy violation, spam, or harmful content."
}
```

#### `blocked`

```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "blocked",
  "description": "The subject has chosen to exclude the object from their view or interactions.",
  "sameAs": ["https://schema.org/IgnoreAction"]
}
```

#### `vouch for`

```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "vouch for",
  "description": "The subject stakes personal credibility on the object's identity, quality, or claims. A reputation primitive stronger than endorsement."
}
```

### Curation and Containment

#### `contain`

```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "contain",
  "description": "The subject collection or container includes the object as a member or entry.",
  "sameAs": ["https://schema.org/hasPart"]
}
```

#### `listed in`

```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "listed in",
  "description": "The subject item appears as an entry within the object collection, stack, or curated list.",
  "sameAs": ["https://schema.org/isPartOf"]
}
```

#### `curated by`

```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "curated by",
  "description": "The subject collection or content set is maintained and organized by the object actor.",
  "sameAs": ["https://schema.org/maintainer"]
}
```

#### `pinned in`

```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "pinned in",
  "description": "The subject is pinned or highlighted within the object collection for prominent display."
}
```

#### `featured in`

```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "featured in",
  "description": "The subject is showcased or editorially promoted within the object context.",
  "sameAs": ["https://schema.org/isPartOf"]
}
```

#### `ranked above`

```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "ranked above",
  "description": "The subject is explicitly ranked higher than the object within a shared ordering context."
}
```

#### `depend on`

```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "depend on",
  "description": "The subject requires or relies on the object to function or exist.",
  "sameAs": ["https://schema.org/requirements"]
}
```

#### `alternative to`

```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "alternative to",
  "description": "The subject can serve as a substitute or competing option for the object.",
  "sameAs": ["https://schema.org/isSimilarTo"]
}
```

### Authorship and Contribution

#### `created by`

```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "created by",
  "description": "The subject was originally created or brought into existence by the object actor.",
  "sameAs": ["https://schema.org/creator", "https://schema.org/CreateAction"]
}
```

#### `authored by`

```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "authored by",
  "description": "The subject content was written or composed by the object actor.",
  "sameAs": ["https://schema.org/author", "https://schema.org/WriteAction"]
}
```

#### `contributed to`

```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "contributed to",
  "description": "The subject actor made a meaningful contribution to the object project, work, or entity.",
  "sameAs": ["https://schema.org/contributor"]
}
```

#### `forked from`

```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "forked from",
  "description": "The subject was created as a divergent copy or branch of the object."
}
```

#### `derived from`

```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "derived from",
  "description": "The subject was produced by transforming, adapting, or building upon the object.",
  "sameAs": ["https://schema.org/isBasedOn"]
}
```

#### `inspired by`

```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "inspired by",
  "description": "The subject was creatively or conceptually influenced by the object without direct derivation."
}
```

### Metadata and Linking

#### `linked account`

```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "linked account",
  "description": "Connects the subject identity to one of its platform account atoms.",
  "sameAs": ["https://schema.org/sameAs"]
}
```

#### `url`

```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "url",
  "description": "Links the subject atom to its canonical URL or web-addressable identifier.",
  "sameAs": ["https://schema.org/url"]
}
```

#### `imgUrl`

```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "imgUrl",
  "description": "Links the subject atom to an image URL. Legacy camelCase naming retained for backward compatibility.",
  "sameAs": ["https://schema.org/image"]
}
```

#### `has description`

```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "has description",
  "description": "Attaches a textual description atom to the subject entity.",
  "sameAs": ["https://schema.org/description"]
}
```

#### `has source`

```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "has source",
  "description": "Points to the authoritative origin or reference material for the subject.",
  "sameAs": ["https://schema.org/isBasedOn", "https://schema.org/citation"]
}
```

#### `published at`

```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "published at",
  "description": "Indicates the platform, venue, or location where the subject was published or released.",
  "sameAs": ["https://schema.org/publisher"]
}
```

#### `located in`

```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "located in",
  "description": "Asserts that the subject is geographically or logically situated within the object location.",
  "sameAs": ["https://schema.org/location", "https://schema.org/containedInPlace"]
}
```

#### `available on`

```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "available on",
  "description": "Indicates the subject can be accessed, purchased, or used on the object platform or chain.",
  "sameAs": ["https://schema.org/availableOnDevice"]
}
```

### Affiliation and Membership

#### `member of`

```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "member of",
  "description": "The subject actor or entity holds membership in the object organization or group.",
  "sameAs": ["https://schema.org/memberOf", "https://schema.org/JoinAction"]
}
```

#### `employed by`

```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "employed by",
  "description": "The subject person is employed by the object organization.",
  "sameAs": ["https://schema.org/worksFor"]
}
```

#### `founded`

```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "founded",
  "description": "The subject actor established or co-founded the object organization or project.",
  "sameAs": ["https://schema.org/founder"]
}
```

#### `affiliated with`

```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "affiliated with",
  "description": "A general association between the subject and object entities without implying employment or membership.",
  "sameAs": ["https://schema.org/affiliation"]
}
```

#### `partner of`

```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "partner of",
  "description": "The subject and object have a formal or recognized partnership relationship.",
  "sameAs": ["https://schema.org/sponsor"]
}
```

#### `invested in`

```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "invested in",
  "description": "The subject has made a financial or resource investment in the object entity.",
  "sameAs": ["https://schema.org/funder"]
}
```

### Domain-Specific Knowledge

#### `use`

```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "use",
  "description": "The subject utilizes, integrates, or depends on the object tool, technology, or resource.",
  "sameAs": ["https://schema.org/usesDevice"]
}
```

#### `compatible with`

```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "compatible with",
  "description": "The subject works correctly or interoperates with the object system, standard, or platform.",
  "sameAs": ["https://schema.org/isCompatibleWith"]
}
```

#### `governed by`

```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "governed by",
  "description": "The subject protocol, contract, or entity is under the governance authority of the object."
}
```

#### `priced in`

```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "priced in",
  "description": "The subject asset or service is denominated or quoted in the object currency or token.",
  "sameAs": ["https://schema.org/priceCurrency"]
}
```

#### `implement`

```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "implement",
  "description": "The subject contract, application, or system implements the object standard, specification, or interface."
}
```

### Sentiment and Opinion

#### `agree with`

```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "agree with",
  "description": "The subject's position aligns with the object claim, proposal, or stance.",
  "sameAs": ["https://schema.org/AgreeAction"]
}
```

#### `disagree with`

```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "disagree with",
  "description": "The subject's position opposes the object claim, proposal, or stance.",
  "sameAs": ["https://schema.org/DisagreeAction"]
}
```

#### `support`

```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "support",
  "description": "The subject actively backs the object cause, proposal, or initiative. Broader than 'endorse' — applies to movements and policies, not just entities."
}
```

#### `oppose`

```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "oppose",
  "description": "The subject actively resists or campaigns against the object cause, proposal, or initiative. The inverse of 'support'."
}
```

#### `skeptical of`

```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "skeptical of",
  "description": "The subject expresses cautious doubt about the object without fully rejecting it. Weaker than 'distrust' or 'oppose'."
}
```

#### `bullish on`

```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "bullish on",
  "description": "The subject has positive conviction about the object's future value, growth, or success. A market-native sentiment primitive."
}
```

#### `bearish on`

```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "bearish on",
  "description": "The subject has negative conviction about the object's future value, growth, or success. The inverse of 'bullish on'."
}
```

#### `neutral on`

```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "neutral on",
  "description": "The subject records an explicit non-position on the object. Useful for distinguishing 'no opinion expressed' from 'consciously neutral'."
}
```

### Comparison and Ranking

#### `better than`

```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "better than",
  "description": "The subject is asserted as subjectively superior to the object. Opinion-grade — the triple captures a claim, not an objective fact.",
  "sameAs": ["https://schema.org/WinAction"]
}
```

#### `worse than`

```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "worse than",
  "description": "The subject is asserted as subjectively inferior to the object. The inverse of 'better than'."
}
```

#### `equivalent to`

```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "equivalent to",
  "description": "The subject and object are functionally interchangeable or at parity. Distinct from 'same as' — the entities are different but serve the same role.",
  "sameAs": ["https://schema.org/equal"]
}
```

#### `compete with`

```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "compete with",
  "description": "The subject and object are direct competitors in the same market or category.",
  "sameAs": ["https://schema.org/competitor"]
}
```

#### `outperform`

```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "outperform",
  "description": "The subject demonstrably exceeds the object on measurable criteria. Stronger than 'better than' — implies evidence."
}
```

#### `supersede`

```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "supersede",
  "description": "The subject is the designated replacement for the object. Implies the object is deprecated or obsolete.",
  "sameAs": ["https://schema.org/supersededBy"]
}
```

#### `predecessor of`

```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "predecessor of",
  "description": "The subject is an earlier version or iteration that came before the object.",
  "sameAs": ["https://schema.org/predecessorOf"]
}
```

#### `successor of`

```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "successor of",
  "description": "The subject is the next version or iteration following the object.",
  "sameAs": ["https://schema.org/successorOf"]
}
```

### Knowledge and Expertise

#### `expert in`

```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "expert in",
  "description": "The subject claims or is recognized as having deep expertise in the object domain or skill.",
  "sameAs": ["https://schema.org/knowsAbout"]
}
```

#### `learned from`

```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "learned from",
  "description": "The subject acquired knowledge or skills from the object actor or resource. A directed knowledge-attribution edge."
}
```

#### `teach`

```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "teach",
  "description": "The subject actively disseminates knowledge about the object topic or skill.",
  "sameAs": ["https://schema.org/teaches"]
}
```

#### `studied`

```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "studied",
  "description": "The subject has invested learning effort in the object domain, topic, or institution.",
  "sameAs": ["https://schema.org/alumniOf"]
}
```

#### `certified by`

```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "certified by",
  "description": "The subject holds a credential, certification, or formal recognition issued by the object authority.",
  "sameAs": ["https://schema.org/hasCredential"]
}
```

#### `mentor of`

```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "mentor of",
  "description": "The subject provides ongoing guidance and mentorship to the object person."
}
```

#### `student of`

```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "student of",
  "description": "The subject is learning from or apprenticed under the object person or institution. The inverse of 'mentor of'."
}
```

#### `speak`

```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "speak",
  "description": "The subject has proficiency in the object language, programming language, or communication system.",
  "sameAs": ["https://schema.org/knowsLanguage"]
}
```

### Provenance and Evidence

#### `verified by`

```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "verified by",
  "description": "The subject has been independently verified or validated by the object authority or process."
}
```

#### `audited by`

```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "audited by",
  "description": "The subject has undergone a formal security, financial, or compliance audit by the object firm."
}
```

#### `attested by`

```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "attested by",
  "description": "The subject claim or credential is witnessed and signed by the object attestor."
}
```

#### `cited by`

```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "cited by",
  "description": "The subject work is referenced or cited in the object work. A backward citation link.",
  "sameAs": ["https://schema.org/citation"]
}
```

#### `reference`

```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "reference",
  "description": "The subject work cites or refers to the object work. A forward citation link — the inverse of 'cited by'.",
  "sameAs": ["https://schema.org/citation"]
}
```

#### `evidenced by`

```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "evidenced by",
  "description": "The subject claim is supported by the object proof, data, or artifact."
}
```

#### `disputed by`

```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "disputed by",
  "description": "The subject claim is challenged or contradicted by the object counter-evidence or actor."
}
```

#### `confirmed by`

```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "confirmed by",
  "description": "The subject claim is independently corroborated by the object source or evidence."
}
```

### Temporal and Lifecycle

#### `preceded by`

```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "preceded by",
  "description": "The subject event or state was immediately preceded by the object event or state in a temporal sequence."
}
```

#### `followed by`

```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "followed by",
  "description": "The subject event or state is immediately followed by the object event or state. The inverse of 'preceded by'."
}
```

#### `enabled by`

```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "enabled by",
  "description": "The subject outcome was made possible by the object precondition, innovation, or actor."
}
```

#### `triggered`

```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "triggered",
  "description": "The subject event or action directly caused the object consequence or chain of events."
}
```

#### `deprecated by`

```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "deprecated by",
  "description": "The subject is formally deprecated in favor of the object replacement. The subject still exists but is no longer recommended."
}
```

#### `replaced by`

```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "replaced by",
  "description": "The subject has been fully substituted by the object. Stronger than 'deprecated by' — implies the subject is no longer active.",
  "sameAs": ["https://schema.org/supersededBy"]
}
```

### Governance and Policy

#### `voted for`

```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "voted for",
  "description": "The subject cast a governance vote in favor of the object proposal.",
  "sameAs": ["https://schema.org/VoteAction"]
}
```

#### `voted against`

```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "voted against",
  "description": "The subject cast a governance vote opposing the object proposal.",
  "sameAs": ["https://schema.org/VoteAction"]
}
```

#### `delegated to`

```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "delegated to",
  "description": "The subject has delegated governance power, voting rights, or authority to the object actor."
}
```

#### `proposed`

```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "proposed",
  "description": "The subject actor authored or submitted the object proposal for governance consideration."
}
```

#### `regulated by`

```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "regulated by",
  "description": "The subject entity falls under the regulatory authority or jurisdiction of the object body.",
  "sameAs": ["https://schema.org/legislationAppliedBy"]
}
```

#### `compliant with`

```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "compliant with",
  "description": "The subject entity meets the requirements defined by the object regulation, standard, or framework."
}
```

### Economic and Market

#### `backed by`

```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "backed by",
  "description": "The subject asset or instrument is collateralized, guaranteed, or underwritten by the object asset or entity.",
  "sameAs": ["https://schema.org/funder"]
}
```

#### `pegged to`

```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "pegged to",
  "description": "The subject asset maintains a target price ratio relative to the object reference asset."
}
```

#### `listed on`

```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "listed on",
  "description": "The subject asset or product is available for trading or purchase on the object exchange or marketplace."
}
```

#### `sponsored by`

```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "sponsored by",
  "description": "The subject event, project, or initiative receives financial sponsorship from the object entity.",
  "sameAs": ["https://schema.org/sponsor"]
}
```

#### `reward`

```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "reward",
  "description": "The subject protocol or program distributes incentives to the object participant class or actor."
}
```

#### `staked in`

```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "staked in",
  "description": "The subject has committed assets or stake in the object protocol, validator, or staking pool."
}
```

## Representation Trade-Offs

The protocol hashes atom data into a deterministic ID via `keccak256(ATOM_SALT, keccak256(data))`. This means the on-chain identity of a predicate is permanently bound to the exact bytes stored. Four representation strategies are possible, each with real consequences.

### Option A: Plain String

The predicate atom data is a raw UTF-8 string like `"follow"`.

```
atom data:  "follow"
atom ID:    keccak256(ATOM_SALT, keccak256(toHex("follow")))
```

| | |
|---|---|
| Determinism | Every SDK, chain, and builder produces the same atom ID from the same string. No serialization ambiguity. |
| Gas cost | Minimal — a few bytes on-chain. |
| External dependencies | None. |
| Human readability | Readable in raw transaction data and block explorers. |
| Semantic richness | Zero embedded metadata. Meaning must be documented externally. |
| Schema.org alignment | None inherent, but can be mapped externally via a registry. |

### Option B: JSON-LD / DefinedTerm On-Chain

The predicate atom data is a JSON document stored directly as the atom bytes.

```
atom data:  '{"@context":"https://schema.org/","@type":"DefinedTerm","name":"follow","description":"..."}'
atom ID:    keccak256(ATOM_SALT, keccak256(toHex(json_string)))
```

| | |
|---|---|
| Determinism | **Fragile unless canonicalized.** Key order, whitespace, and Unicode normalization all change the hash. `{"name":"follow","@type":"DefinedTerm"}` and `{"@type":"DefinedTerm","name":"follow"}` produce different atom IDs forever unless the SDK uses strict canonical JSON serialization. |
| Gas cost | 5-10x more bytes than a plain string. |
| External dependencies | None at read time, but requires a serialization spec at write time. |
| Self-describing | Yes — carries its own semantic context in the triple. |
| Schema.org alignment | Native. Machine-readable by any JSON-LD consumer. |
| Risk | Any future change to description, context URL, or schema version means a new atom ID. The old predicate atom and new predicate atom are permanently distinct on-chain. |

### Option C: Schema.org Action Types

Use the full Schema.org Action hierarchy (e.g., `FollowAction`, `EndorseAction`, `BookmarkAction`) as predicate atoms.

```
atom data:  '{"@context":"https://schema.org/","@type":"FollowAction","name":"follow"}'
```

| | |
|---|---|
| Semantic precision | High. Schema.org distinguishes FollowAction (active polling) from SubscribeAction (passive reception) from BefriendAction (reciprocal). |
| Protocol fit | **Poor.** Actions are temporal events with `agent`, `startTime`, `endTime`, `actionStatus`. Predicates are durable relationships. A triple `(Alice, follow, Bob)` is a standing fact — not a timestamped event. |
| Graph fragmentation | If some builders use `FollowAction` and others use canonical `DefinedTerm` predicate atom data, the graph splits. Two predicate atoms, two sets of triples, no interop. |
| Nuance value | The distinctions Schema.org draws (follow vs subscribe vs befriend) are real, but they matter at the **application layer**, not the **triple layer**. Whether "follow" means push notifications or active polling is a product decision, not a predicate identity question. |

### Option D: IPFS-Hosted Document

The predicate atom data is an IPFS CID pointing to a rich metadata document.

```
atom data:  "ipfs://QmXyz..."  (or "bafyrei...")
atom ID:    keccak256(ATOM_SALT, keccak256(toHex("ipfs://QmXyz...")))
```

| | |
|---|---|
| On-chain cost | Low — just the CID string. |
| Metadata richness | Unlimited. The IPFS document can contain Schema.org JSON-LD, multilingual labels, version history, examples. |
| Determinism | The CID is content-addressed, so the same document always produces the same CID. But the atom ID is derived from the CID string, not the document itself — one layer of indirection. |
| Availability | **Requires IPFS infrastructure.** If pinning lapses and no gateway has the content, the predicate becomes opaque bytes. Builders can't resolve the meaning without the document. |
| Lookup cost | Every predicate resolution requires an IPFS fetch. Plain strings are self-evident. |
| Hybrid potential | Works as an optional enrichment layer — the canonical atom can be deterministic inline `DefinedTerm` JSON, and a separate triple like `(follow_atom, has source, ipfs://QmXyz...)` links it to richer metadata. |

### Recommendation

**Use deterministic inline `DefinedTerm` JSON as the canonical predicate atom data.** Publish richer structured metadata (Schema.org mappings, usage examples, i18n, and semantic flags) as optional IPFS enrichment or generated SDK metadata without changing the canonical atom identity.

This gives you:
- Self-describing predicate atoms that match the rest of the typed atom model
- Deterministic atom IDs when the SDK emits canonical JSON bytes
- No graph fragmentation risk from ad hoc plain strings or Schema.org Action variants
- Freedom to enrich predicate metadata without changing canonical atom identity

The generated predicates package remains the source of truth for canonical atom creation; this document explains the semantics and partner-facing modeling guidance.

---

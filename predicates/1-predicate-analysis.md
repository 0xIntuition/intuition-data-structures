# Canonical Predicate Catalog Analysis

## Top 100 Predicates

### Identity and Classification (1-8)

| # | Predicate | Intent | Typical Triple Pattern |
|---|---|---|---|
| 1 | `is` | Identity or type assertion | `(Ethereum, is, blockchain)` |
| 2 | `has type` | Type classification | `(Uniswap, has type, DEX)` |
| 3 | `same as` | Cross-representation identity | `(ETH, same as, Ether)` |
| 4 | `alias of` | Alternative name | `(BTC, alias of, Bitcoin)` |
| 5 | `instance of` | Class membership | `(USDC, instance of, stablecoin)` |
| 6 | `subclass of` | Taxonomy hierarchy | `(DEX, subclass of, exchange)` |
| 7 | `has tag` | Free-form tagging | `(Aave, has tag, lending)` |
| 8 | `has category` | Categorical grouping | `(Chainlink, has category, oracle)` |

### Social and Reputation (9-18)

| # | Predicate | Intent | Typical Triple Pattern |
|---|---|---|---|
| 9 | `follows` | Unidirectional subscription | `(Alice, follows, Bob)` |
| 10 | `likes` | Lightweight positive signal | `(Alice, likes, Ethereum)` |
| 11 | `endorses` | Strong public support | `(Vitalik, endorses, EIP-4844)` |
| 12 | `trusts` | Positive trust assertion | `(Alice, trusts, Auditor X)` |
| 13 | `distrusts` | Negative trust assertion | `(Alice, distrusts, Scam Project)` |
| 14 | `reviewed` | Review authorship | `(Alice, reviewed, Uniswap v4)` |
| 15 | `recommended` | Active recommendation | `(Alice, recommended, Hardhat)` |
| 16 | `reported` | Flagging for violation | `(Alice, reported, Phishing Site)` |
| 17 | `blocked` | Exclusion from view | `(Alice, blocked, Spam Account)` |
| 18 | `vouches for` | Personal credibility stake | `(Alice, vouches for, Bob)` |

### Curation and Containment (19-25)

| # | Predicate | Intent | Typical Triple Pattern |
|---|---|---|---|
| 19 | `contains` | Collection membership | `(L1 Watchlist, contains, Ethereum)` |
| 20 | `curated by` | Collection ownership | `(DeFi Blue Chips, curated by, Alice)` |
| 21 | `pinned in` | Highlighted in collection | `(Ethereum, pinned in, L1 Watchlist)` |
| 22 | `featured in` | Editorially promoted | `(Uniswap, featured in, Top DEXs)` |
| 23 | `ranked above` | Explicit ordering | `(Ethereum, ranked above, Solana)` |
| 24 | `depends on` | Functional dependency | `(Arbitrum, depends on, Ethereum)` |
| 25 | `alternative to` | Substitutability | `(Solana, alternative to, Ethereum)` |

### Authorship and Contribution (26-31)

| # | Predicate | Intent | Typical Triple Pattern |
|---|---|---|---|
| 26 | `created by` | Origin attribution | `(Ethereum, created by, Vitalik Buterin)` |
| 27 | `authored by` | Written content attribution | `(Whitepaper, authored by, Satoshi)` |
| 28 | `contributed to` | Contribution record | `(Alice, contributed to, OpenZeppelin)` |
| 29 | `forked from` | Divergent copy | `(Sushiswap, forked from, Uniswap)` |
| 30 | `derived from` | Adaptation or build-upon | `(Optimism, derived from, Ethereum)` |
| 31 | `inspired by` | Creative influence | `(Solana, inspired by, PBFT)` |

### Metadata and Linking (32-39)

| # | Predicate | Intent | Typical Triple Pattern |
|---|---|---|---|
| 32 | `linked account` | Platform identity linkage | `(Alice, linked account, x.com/alice)` |
| 33 | `url` | Canonical web address | `(Ethereum, url, ethereum.org)` |
| 34 | `imgUrl` | Image reference (legacy) | `(Ethereum, imgUrl, eth-logo.png)` |
| 35 | `has description` | Textual description | `(Ethereum, has description, "A decentralized...")` |
| 36 | `has source` | Authoritative reference | `(EIP-4844, has source, eips.ethereum.org/...)` |
| 37 | `published at` | Publication venue | `(Whitepaper, published at, bitcoin.org)` |
| 38 | `located in` | Geographic or logical location | `(Devcon, located in, Bangkok)` |
| 39 | `available on` | Platform availability | `(USDC, available on, Ethereum)` |

### Affiliation and Membership (40-45)

| # | Predicate | Intent | Typical Triple Pattern |
|---|---|---|---|
| 40 | `member of` | Organization membership | `(Alice, member of, Ethereum Foundation)` |
| 41 | `employed by` | Employment relationship | `(Alice, employed by, Uniswap Labs)` |
| 42 | `founded` | Founder attribution | `(Vitalik, founded, Ethereum)` |
| 43 | `affiliated with` | General association | `(Protocol X, affiliated with, a16z)` |
| 44 | `partner of` | Formal partnership | `(Chainlink, partner of, SWIFT)` |
| 45 | `invested in` | Financial investment | `(a16z, invested in, Uniswap)` |

### Domain-Specific Knowledge (46-50)

| # | Predicate | Intent | Typical Triple Pattern |
|---|---|---|---|
| 46 | `uses` | Technology utilization | `(Aave, uses, Chainlink)` |
| 47 | `compatible with` | Interoperability | `(MetaMask, compatible with, Ethereum)` |
| 48 | `governed by` | Governance authority | `(Uniswap, governed by, UNI holders)` |
| 49 | `priced in` | Denomination currency | `(NFT Collection, priced in, ETH)` |
| 50 | `implements` | Standard implementation | `(USDC, implements, ERC-20)` |

### Sentiment and Opinion (51-58)

| # | Predicate | Intent | Typical Triple Pattern |
|---|---|---|---|
| 51 | `agrees with` | Alignment of position | `(Alice, agrees with, EIP-4844)` |
| 52 | `disagrees with` | Opposition of position | `(Alice, disagrees with, PoW Revival)` |
| 53 | `supports` | Active backing of a cause or proposal | `(Coinbase, supports, MiCA regulation)` |
| 54 | `opposes` | Active resistance to a cause or proposal | `(Mining Pool X, opposes, PoS transition)` |
| 55 | `skeptical of` | Cautious doubt without full rejection | `(Alice, skeptical of, Restaking)` |
| 56 | `bullish on` | Positive conviction about future value | `(Alice, bullish on, Ethereum)` |
| 57 | `bearish on` | Negative conviction about future value | `(Alice, bearish on, Memecoins)` |
| 58 | `neutral on` | Explicit non-position | `(Alice, neutral on, L2 wars)` |

### Comparison and Ranking (59-66)

| # | Predicate | Intent | Typical Triple Pattern |
|---|---|---|---|
| 59 | `better than` | Subjective superiority claim | `(Rust, better than, Solidity)` |
| 60 | `worse than` | Subjective inferiority claim | `(PoW, worse than, PoS)` |
| 61 | `equivalent to` | Functional parity | `(USDC, equivalent to, USDT)` |
| 62 | `competes with` | Direct market competition | `(Uniswap, competes with, Curve)` |
| 63 | `outperforms` | Measurable superiority | `(Solana, outperforms, Ethereum)` |
| 64 | `supersedes` | Replacement of a predecessor | `(Uniswap v4, supersedes, Uniswap v3)` |
| 65 | `predecessor of` | Versioning lineage | `(Uniswap v2, predecessor of, Uniswap v3)` |
| 66 | `successor of` | Forward version link | `(Uniswap v3, successor of, Uniswap v2)` |

### Knowledge and Expertise (67-74)

| # | Predicate | Intent | Typical Triple Pattern |
|---|---|---|---|
| 67 | `expert in` | Domain expertise claim | `(Alice, expert in, ZK proofs)` |
| 68 | `learned from` | Knowledge attribution | `(Alice, learned from, Bob)` |
| 69 | `teaches` | Knowledge dissemination | `(Alice, teaches, Solidity)` |
| 70 | `studied` | Learning engagement | `(Alice, studied, cryptography)` |
| 71 | `certified by` | Credential attestation | `(Alice, certified by, Ethereum Foundation)` |
| 72 | `mentor of` | Mentorship relationship | `(Bob, mentor of, Alice)` |
| 73 | `student of` | Apprenticeship relationship | `(Alice, student of, Bob)` |
| 74 | `speaks` | Language or communication capability | `(Alice, speaks, Rust)` |

### Provenance and Evidence (75-82)

| # | Predicate | Intent | Typical Triple Pattern |
|---|---|---|---|
| 75 | `verified by` | Third-party verification | `(Smart Contract, verified by, CertiK)` |
| 76 | `audited by` | Security or financial audit | `(Aave v3, audited by, Trail of Bits)` |
| 77 | `attested by` | Witness or attestation | `(Credential, attested by, Issuer)` |
| 78 | `cited by` | Academic or reference citation | `(Bitcoin Whitepaper, cited by, Ethereum Whitepaper)` |
| 79 | `references` | Forward citation or mention | `(Ethereum Whitepaper, references, Bitcoin Whitepaper)` |
| 80 | `evidenced by` | Supporting proof or data | `(Claim, evidenced by, On-chain Proof)` |
| 81 | `disputed by` | Challenge to a claim | `(Claim, disputed by, Counter-evidence)` |
| 82 | `confirmed by` | Corroboration of a claim | `(Claim, confirmed by, Independent Source)` |

### Temporal and Lifecycle (83-88)

| # | Predicate | Intent | Typical Triple Pattern |
|---|---|---|---|
| 83 | `preceded by` | Temporal ordering | `(Merge, preceded by, Beacon Chain launch)` |
| 84 | `followed by` | Forward temporal link | `(Beacon Chain launch, followed by, Merge)` |
| 85 | `enabled by` | Causal enablement | `(DeFi Summer, enabled by, Compound governance)` |
| 86 | `triggered` | Causal initiation | `(Terra collapse, triggered, Contagion)` |
| 87 | `deprecated by` | Formal deprecation | `(ERC-20 approve, deprecated by, ERC-20 permit)` |
| 88 | `replaced by` | Full substitution | `(Sushiswap Chef, replaced by, MasterChefV2)` |

### Governance and Policy (89-94)

| # | Predicate | Intent | Typical Triple Pattern |
|---|---|---|---|
| 89 | `voted for` | Governance vote in favor | `(Alice, voted for, Proposal 42)` |
| 90 | `voted against` | Governance vote against | `(Alice, voted against, Proposal 43)` |
| 91 | `delegated to` | Governance delegation | `(Alice, delegated to, Bob)` |
| 92 | `proposed` | Proposal authorship | `(Alice, proposed, EIP-7702)` |
| 93 | `regulated by` | Regulatory jurisdiction | `(USDC, regulated by, SEC)` |
| 94 | `compliant with` | Regulatory compliance | `(Exchange X, compliant with, MiCA)` |

### Economic and Market (95-100)

| # | Predicate | Intent | Typical Triple Pattern |
|---|---|---|---|
| 95 | `backed by` | Collateral or backing relationship | `(DAI, backed by, ETH)` |
| 96 | `pegged to` | Price peg relationship | `(USDC, pegged to, USD)` |
| 97 | `listed on` | Exchange or marketplace listing | `(ETH, listed on, Coinbase)` |
| 98 | `sponsored by` | Financial sponsorship | `(Devcon, sponsored by, Ethereum Foundation)` |
| 99 | `rewards` | Incentive distribution | `(Aave, rewards, Liquidity Providers)` |
| 100 | `staked in` | Staking relationship | `(Alice, staked in, Ethereum Beacon Chain)` |

---

## Schema.org Action Mapping

This table maps Schema.org Action types to Intuition predicate strings. The Action hierarchy is a useful reference for choosing predicate names and understanding semantic boundaries, but the Action types themselves should not be used as on-chain atom data.

### InteractAction Family

| Schema.org Action | Intuition Predicate | Notes |
|---|---|---|
| `FollowAction` | `follows` | Unidirectional, active interest. Schema.org distinguishes this from SubscribeAction (passive) but that distinction belongs at the app layer. |
| `BefriendAction` | `connected with` | Reciprocal connection. Not in the top 50 — most on-chain graphs are directional. If needed, model as two directional triples. |
| `SubscribeAction` | `follows` | Schema.org treats this as passive reception vs active polling. In a knowledge graph, both are "follows". Product behavior (push vs pull) is app-specific. |
| `JoinAction` | `member of` | The triple records the resulting state, not the join event. |
| `LeaveAction` | *(none)* | Leaving is the absence of the `member of` triple or a counter-triple. Not a standalone predicate. |
| `RegisterAction` | *(none)* | Registration is a one-time event, not a durable relationship. |
| `UnRegisterAction` | *(none)* | Same reasoning. |
| `MarryAction` | *(none)* | Too domain-specific for core predicates. |
| `CommunicateAction` | *(none)* | Communication is ephemeral. Triples record durable knowledge. |

### AssessAction Family

| Schema.org Action | Intuition Predicate | Notes |
|---|---|---|
| `ReactAction` | `likes` | Lightweight positive signal. Schema.org's LikeAction is a sub-type of ReactAction. |
| `ReviewAction` | `reviewed` | The triple asserts the review relationship. Review content lives in the object atom or enrichment. |
| `EndorseAction` | `endorses` | Stronger than `likes`. Schema.org places this under AssessAction. |
| `ChooseAction` | *(none)* | Selection is a transient decision, not a graph relationship. |
| `IgnoreAction` | `blocked` | Closest durable analog. `blocked` is the on-chain record of exclusion. |
| `DislikeAction` | `distrusts` | Negative assessment. `distrusts` is more useful in a reputation graph than a generic "dislike". |

### OrganizeAction Family

| Schema.org Action | Intuition Predicate | Notes |
|---|---|---|
| `BookmarkAction` | `contains` | Bookmarking is adding to a personal collection. Model as `(my_collection, contains, item)`. |
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
| `WinAction` | `outperforms` | Durable performance superiority, not a single-event win. |
| `LoseAction` | `worse than` | Relative positioning in the graph. |
| `TieAction` | `equivalent to` | Functional parity assertion. |

### UpdateAction Family

| Schema.org Action | Intuition Predicate | Notes |
|---|---|---|
| `ReplaceAction` | `replaced by` / `supersedes` | Forward and backward version links. The triple records the resulting state. |
| `AddAction` | `contains` | Adding to a collection maps to the existing containment predicate. |
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
| `schema:competitor` | `competes with` | Direct market competition. |
| `schema:predecessorOf` | `predecessor of` | Version lineage. |
| `schema:successorOf` | `successor of` | Forward version link. |
| `schema:citation` | `cited by` / `references` | Forward and backward citation links. |
| `schema:sponsor` | `sponsored by` | Financial sponsorship. |
| `schema:funder` | `backed by` / `invested in` | Financial backing — `backed by` for collateral, `invested in` for equity. |
| `schema:teaches` | `teaches` | Knowledge dissemination. |
| `schema:learner` | `student of` | Learning relationship. |
| `schema:legislationAppliedBy` | `regulated by` | Regulatory jurisdiction. |
| `schema:endorsee` | `endorses` / `supports` | `endorses` for entity quality, `supports` for causes and proposals. |
| `Wikidata:P1552 (has quality)` | `bullish on` / `bearish on` / `skeptical of` | Sentiment predicates have no Schema.org equivalent — they are Intuition-native social graph primitives. |
| `Wikidata:P1269 (facet of)` | `enabled by` / `triggered` | Causal relationships are modeled as directional predicates. |

---

## DefinedTerm Atom Schemas

Each entry below proposes the predicate as an off-chain `DefinedTerm` registry entry following the schema conventions in `intuition/data-structures/classifications/defined-term/index.md`. These are for documentation, partner registries, and optional IPFS publication — **not** for use as on-chain atom data.

### Identity and Classification

#### 1. `is`

```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "is",
  "description": "Asserts identity, type membership, or definitional equivalence between subject and object.",
  "sameAs": ["https://schema.org/additionalType"]
}
```

#### 2. `has type`

```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "has type",
  "description": "Classifies the subject under a type or category atom. More specific than 'is' — use when asserting formal classification rather than loose identity.",
  "sameAs": ["https://www.wikidata.org/wiki/Property:P31"]
}
```

#### 3. `same as`

```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "same as",
  "description": "Declares that the subject and object refer to the same real-world entity across different representations or naming systems.",
  "sameAs": ["https://schema.org/sameAs", "https://www.w3.org/2002/07/owl#sameAs"]
}
```

#### 4. `alias of`

```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "alias of",
  "description": "Marks the subject as an alternative name or identifier for the object entity. Directional: the subject is the alias, the object is the canonical form.",
  "sameAs": ["https://schema.org/alternateName"]
}
```

#### 5. `instance of`

```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "instance of",
  "description": "Asserts that the subject is a concrete instance of the object class or concept.",
  "sameAs": ["https://www.wikidata.org/wiki/Property:P31"]
}
```

#### 6. `subclass of`

```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "subclass of",
  "description": "Asserts that the subject is a more specific type within the object's broader category.",
  "sameAs": ["https://www.wikidata.org/wiki/Property:P279", "https://www.w3.org/2000/01/rdf-schema#subClassOf"]
}
```

#### 7. `has tag`

```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "has tag",
  "description": "Assigns a reusable tag or keyword atom to the subject for filtering, clustering, and discovery.",
  "sameAs": ["https://schema.org/keywords"]
}
```

#### 8. `has category`

```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "has category",
  "description": "Places the subject within a broader categorical grouping. More formal than tags — use for structured taxonomies rather than free-form labeling.",
  "sameAs": ["https://schema.org/category"]
}
```

### Social and Reputation

#### 9. `follows`

```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "follows",
  "description": "The subject chooses to subscribe to or track updates from the object entity. Unidirectional and non-reciprocal.",
  "sameAs": ["https://schema.org/FollowAction"]
}
```

#### 10. `likes`

```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "likes",
  "description": "Expresses lightweight positive endorsement of the object by the subject.",
  "sameAs": ["https://schema.org/LikeAction"]
}
```

#### 11. `endorses`

```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "endorses",
  "description": "A stronger-than-like signal indicating the subject publicly supports or vouches for the object's quality or legitimacy.",
  "sameAs": ["https://schema.org/EndorseAction"]
}
```

#### 12. `trusts`

```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "trusts",
  "description": "The subject asserts positive trust in the object. A first-class reputation primitive for web-of-trust graphs."
}
```

#### 13. `distrusts`

```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "distrusts",
  "description": "The subject asserts negative trust in the object. The inverse of 'trusts' — enables negative reputation signals.",
  "sameAs": ["https://schema.org/DislikeAction"]
}
```

#### 14. `reviewed`

```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "reviewed",
  "description": "The subject has authored a review or evaluation of the object.",
  "sameAs": ["https://schema.org/ReviewAction"]
}
```

#### 15. `recommended`

```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "recommended",
  "description": "The subject actively recommends the object to others in the ecosystem. Stronger than 'likes', weaker than 'endorses'."
}
```

#### 16. `reported`

```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "reported",
  "description": "The subject has flagged the object for policy violation, spam, or harmful content."
}
```

#### 17. `blocked`

```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "blocked",
  "description": "The subject has chosen to exclude the object from their view or interactions.",
  "sameAs": ["https://schema.org/IgnoreAction"]
}
```

#### 18. `vouches for`

```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "vouches for",
  "description": "The subject stakes personal credibility on the object's identity, quality, or claims. A reputation primitive stronger than endorsement."
}
```

### Curation and Containment

#### 19. `contains`

```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "contains",
  "description": "The subject collection or container includes the object as a member or entry.",
  "sameAs": ["https://schema.org/hasPart"]
}
```

#### 20. `curated by`

```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "curated by",
  "description": "The subject collection or content set is maintained and organized by the object actor.",
  "sameAs": ["https://schema.org/maintainer"]
}
```

#### 21. `pinned in`

```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "pinned in",
  "description": "The subject is pinned or highlighted within the object collection for prominent display."
}
```

#### 22. `featured in`

```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "featured in",
  "description": "The subject is showcased or editorially promoted within the object context.",
  "sameAs": ["https://schema.org/isPartOf"]
}
```

#### 23. `ranked above`

```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "ranked above",
  "description": "The subject is explicitly ranked higher than the object within a shared ordering context."
}
```

#### 24. `depends on`

```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "depends on",
  "description": "The subject requires or relies on the object to function or exist.",
  "sameAs": ["https://schema.org/requirements"]
}
```

#### 25. `alternative to`

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

#### 26. `created by`

```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "created by",
  "description": "The subject was originally created or brought into existence by the object actor.",
  "sameAs": ["https://schema.org/creator", "https://schema.org/CreateAction"]
}
```

#### 27. `authored by`

```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "authored by",
  "description": "The subject content was written or composed by the object actor.",
  "sameAs": ["https://schema.org/author", "https://schema.org/WriteAction"]
}
```

#### 28. `contributed to`

```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "contributed to",
  "description": "The subject actor made a meaningful contribution to the object project, work, or entity.",
  "sameAs": ["https://schema.org/contributor"]
}
```

#### 29. `forked from`

```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "forked from",
  "description": "The subject was created as a divergent copy or branch of the object."
}
```

#### 30. `derived from`

```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "derived from",
  "description": "The subject was produced by transforming, adapting, or building upon the object.",
  "sameAs": ["https://schema.org/isBasedOn"]
}
```

#### 31. `inspired by`

```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "inspired by",
  "description": "The subject was creatively or conceptually influenced by the object without direct derivation."
}
```

### Metadata and Linking

#### 32. `linked account`

```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "linked account",
  "description": "Connects the subject identity to one of its platform account atoms.",
  "sameAs": ["https://schema.org/sameAs"]
}
```

#### 33. `url`

```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "url",
  "description": "Links the subject atom to its canonical URL or web-addressable identifier.",
  "sameAs": ["https://schema.org/url"]
}
```

#### 34. `imgUrl`

```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "imgUrl",
  "description": "Links the subject atom to an image URL. Legacy camelCase naming retained for backward compatibility.",
  "sameAs": ["https://schema.org/image"]
}
```

#### 35. `has description`

```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "has description",
  "description": "Attaches a textual description atom to the subject entity.",
  "sameAs": ["https://schema.org/description"]
}
```

#### 36. `has source`

```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "has source",
  "description": "Points to the authoritative origin or reference material for the subject.",
  "sameAs": ["https://schema.org/isBasedOn", "https://schema.org/citation"]
}
```

#### 37. `published at`

```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "published at",
  "description": "Indicates the platform, venue, or location where the subject was published or released.",
  "sameAs": ["https://schema.org/publisher"]
}
```

#### 38. `located in`

```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "located in",
  "description": "Asserts that the subject is geographically or logically situated within the object location.",
  "sameAs": ["https://schema.org/location", "https://schema.org/containedInPlace"]
}
```

#### 39. `available on`

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

#### 40. `member of`

```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "member of",
  "description": "The subject actor or entity holds membership in the object organization or group.",
  "sameAs": ["https://schema.org/memberOf", "https://schema.org/JoinAction"]
}
```

#### 41. `employed by`

```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "employed by",
  "description": "The subject person is employed by the object organization.",
  "sameAs": ["https://schema.org/worksFor"]
}
```

#### 42. `founded`

```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "founded",
  "description": "The subject actor established or co-founded the object organization or project.",
  "sameAs": ["https://schema.org/founder"]
}
```

#### 43. `affiliated with`

```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "affiliated with",
  "description": "A general association between the subject and object entities without implying employment or membership.",
  "sameAs": ["https://schema.org/affiliation"]
}
```

#### 44. `partner of`

```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "partner of",
  "description": "The subject and object have a formal or recognized partnership relationship.",
  "sameAs": ["https://schema.org/sponsor"]
}
```

#### 45. `invested in`

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

#### 46. `uses`

```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "uses",
  "description": "The subject utilizes, integrates, or depends on the object tool, technology, or resource.",
  "sameAs": ["https://schema.org/usesDevice"]
}
```

#### 47. `compatible with`

```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "compatible with",
  "description": "The subject works correctly or interoperates with the object system, standard, or platform.",
  "sameAs": ["https://schema.org/isCompatibleWith"]
}
```

#### 48. `governed by`

```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "governed by",
  "description": "The subject protocol, contract, or entity is under the governance authority of the object."
}
```

#### 49. `priced in`

```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "priced in",
  "description": "The subject asset or service is denominated or quoted in the object currency or token.",
  "sameAs": ["https://schema.org/priceCurrency"]
}
```

#### 50. `implements`

```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "implements",
  "description": "The subject contract, application, or system implements the object standard, specification, or interface."
}
```

### Sentiment and Opinion

#### 51. `agrees with`

```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "agrees with",
  "description": "The subject's position aligns with the object claim, proposal, or stance.",
  "sameAs": ["https://schema.org/AgreeAction"]
}
```

#### 52. `disagrees with`

```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "disagrees with",
  "description": "The subject's position opposes the object claim, proposal, or stance.",
  "sameAs": ["https://schema.org/DisagreeAction"]
}
```

#### 53. `supports`

```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "supports",
  "description": "The subject actively backs the object cause, proposal, or initiative. Broader than 'endorses' — applies to movements and policies, not just entities."
}
```

#### 54. `opposes`

```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "opposes",
  "description": "The subject actively resists or campaigns against the object cause, proposal, or initiative. The inverse of 'supports'."
}
```

#### 55. `skeptical of`

```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "skeptical of",
  "description": "The subject expresses cautious doubt about the object without fully rejecting it. Weaker than 'distrusts' or 'opposes'."
}
```

#### 56. `bullish on`

```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "bullish on",
  "description": "The subject has positive conviction about the object's future value, growth, or success. A market-native sentiment primitive."
}
```

#### 57. `bearish on`

```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "bearish on",
  "description": "The subject has negative conviction about the object's future value, growth, or success. The inverse of 'bullish on'."
}
```

#### 58. `neutral on`

```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "neutral on",
  "description": "The subject records an explicit non-position on the object. Useful for distinguishing 'no opinion expressed' from 'consciously neutral'."
}
```

### Comparison and Ranking

#### 59. `better than`

```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "better than",
  "description": "The subject is asserted as subjectively superior to the object. Opinion-grade — the triple captures a claim, not an objective fact.",
  "sameAs": ["https://schema.org/WinAction"]
}
```

#### 60. `worse than`

```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "worse than",
  "description": "The subject is asserted as subjectively inferior to the object. The inverse of 'better than'."
}
```

#### 61. `equivalent to`

```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "equivalent to",
  "description": "The subject and object are functionally interchangeable or at parity. Distinct from 'same as' — the entities are different but serve the same role.",
  "sameAs": ["https://schema.org/equal"]
}
```

#### 62. `competes with`

```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "competes with",
  "description": "The subject and object are direct competitors in the same market or category.",
  "sameAs": ["https://schema.org/competitor"]
}
```

#### 63. `outperforms`

```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "outperforms",
  "description": "The subject demonstrably exceeds the object on measurable criteria. Stronger than 'better than' — implies evidence."
}
```

#### 64. `supersedes`

```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "supersedes",
  "description": "The subject is the designated replacement for the object. Implies the object is deprecated or obsolete.",
  "sameAs": ["https://schema.org/supersededBy"]
}
```

#### 65. `predecessor of`

```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "predecessor of",
  "description": "The subject is an earlier version or iteration that came before the object.",
  "sameAs": ["https://schema.org/predecessorOf"]
}
```

#### 66. `successor of`

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

#### 67. `expert in`

```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "expert in",
  "description": "The subject claims or is recognized as having deep expertise in the object domain or skill.",
  "sameAs": ["https://schema.org/knowsAbout"]
}
```

#### 68. `learned from`

```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "learned from",
  "description": "The subject acquired knowledge or skills from the object actor or resource. A directed knowledge-attribution edge."
}
```

#### 69. `teaches`

```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "teaches",
  "description": "The subject actively disseminates knowledge about the object topic or skill.",
  "sameAs": ["https://schema.org/teaches"]
}
```

#### 70. `studied`

```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "studied",
  "description": "The subject has invested learning effort in the object domain, topic, or institution.",
  "sameAs": ["https://schema.org/alumniOf"]
}
```

#### 71. `certified by`

```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "certified by",
  "description": "The subject holds a credential, certification, or formal recognition issued by the object authority.",
  "sameAs": ["https://schema.org/hasCredential"]
}
```

#### 72. `mentor of`

```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "mentor of",
  "description": "The subject provides ongoing guidance and mentorship to the object person."
}
```

#### 73. `student of`

```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "student of",
  "description": "The subject is learning from or apprenticed under the object person or institution. The inverse of 'mentor of'."
}
```

#### 74. `speaks`

```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "speaks",
  "description": "The subject has proficiency in the object language, programming language, or communication system.",
  "sameAs": ["https://schema.org/knowsLanguage"]
}
```

### Provenance and Evidence

#### 75. `verified by`

```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "verified by",
  "description": "The subject has been independently verified or validated by the object authority or process."
}
```

#### 76. `audited by`

```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "audited by",
  "description": "The subject has undergone a formal security, financial, or compliance audit by the object firm."
}
```

#### 77. `attested by`

```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "attested by",
  "description": "The subject claim or credential is witnessed and signed by the object attestor."
}
```

#### 78. `cited by`

```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "cited by",
  "description": "The subject work is referenced or cited in the object work. A backward citation link.",
  "sameAs": ["https://schema.org/citation"]
}
```

#### 79. `references`

```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "references",
  "description": "The subject work cites or refers to the object work. A forward citation link — the inverse of 'cited by'.",
  "sameAs": ["https://schema.org/citation"]
}
```

#### 80. `evidenced by`

```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "evidenced by",
  "description": "The subject claim is supported by the object proof, data, or artifact."
}
```

#### 81. `disputed by`

```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "disputed by",
  "description": "The subject claim is challenged or contradicted by the object counter-evidence or actor."
}
```

#### 82. `confirmed by`

```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "confirmed by",
  "description": "The subject claim is independently corroborated by the object source or evidence."
}
```

### Temporal and Lifecycle

#### 83. `preceded by`

```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "preceded by",
  "description": "The subject event or state was immediately preceded by the object event or state in a temporal sequence."
}
```

#### 84. `followed by`

```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "followed by",
  "description": "The subject event or state is immediately followed by the object event or state. The inverse of 'preceded by'."
}
```

#### 85. `enabled by`

```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "enabled by",
  "description": "The subject outcome was made possible by the object precondition, innovation, or actor."
}
```

#### 86. `triggered`

```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "triggered",
  "description": "The subject event or action directly caused the object consequence or chain of events."
}
```

#### 87. `deprecated by`

```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "deprecated by",
  "description": "The subject is formally deprecated in favor of the object replacement. The subject still exists but is no longer recommended."
}
```

#### 88. `replaced by`

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

#### 89. `voted for`

```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "voted for",
  "description": "The subject cast a governance vote in favor of the object proposal.",
  "sameAs": ["https://schema.org/VoteAction"]
}
```

#### 90. `voted against`

```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "voted against",
  "description": "The subject cast a governance vote opposing the object proposal.",
  "sameAs": ["https://schema.org/VoteAction"]
}
```

#### 91. `delegated to`

```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "delegated to",
  "description": "The subject has delegated governance power, voting rights, or authority to the object actor."
}
```

#### 92. `proposed`

```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "proposed",
  "description": "The subject actor authored or submitted the object proposal for governance consideration."
}
```

#### 93. `regulated by`

```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "regulated by",
  "description": "The subject entity falls under the regulatory authority or jurisdiction of the object body.",
  "sameAs": ["https://schema.org/legislationAppliedBy"]
}
```

#### 94. `compliant with`

```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "compliant with",
  "description": "The subject entity meets the requirements defined by the object regulation, standard, or framework."
}
```

### Economic and Market

#### 95. `backed by`

```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "backed by",
  "description": "The subject asset or instrument is collateralized, guaranteed, or underwritten by the object asset or entity.",
  "sameAs": ["https://schema.org/funder"]
}
```

#### 96. `pegged to`

```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "pegged to",
  "description": "The subject asset maintains a target price ratio relative to the object reference asset."
}
```

#### 97. `listed on`

```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "listed on",
  "description": "The subject asset or product is available for trading or purchase on the object exchange or marketplace."
}
```

#### 98. `sponsored by`

```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "sponsored by",
  "description": "The subject event, project, or initiative receives financial sponsorship from the object entity.",
  "sameAs": ["https://schema.org/sponsor"]
}
```

#### 99. `rewards`

```json
{
  "@context": "https://schema.org/",
  "@type": "DefinedTerm",
  "name": "rewards",
  "description": "The subject protocol or program distributes incentives to the object participant class or actor."
}
```

#### 100. `staked in`

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

The predicate atom data is a raw UTF-8 string like `"follows"`.

```
atom data:  "follows"
atom ID:    keccak256(ATOM_SALT, keccak256(toHex("follows")))
```

| | |
|---|---|
| Determinism | Every SDK, chain, and builder produces the same atom ID from the same string. No serialization ambiguity. |
| Gas cost | Minimal — a few bytes on-chain. |
| External dependencies | None. |
| Human readability | Readable in raw transaction data and block explorers. |
| Semantic richness | Zero embedded metadata. Meaning must be documented externally. |
| Schema.org alignment | None inherent, but can be mapped externally via a registry. |

**This is the current Intuition approach.** The SDK (`intuition/sdk/src/predicates.ts`) exports plain strings: `"is"`, `"has tag"`, `"contains"`, `"follows"`, etc.

### Option B: JSON-LD / DefinedTerm On-Chain

The predicate atom data is a JSON document stored directly as the atom bytes.

```
atom data:  '{"@context":"https://schema.org/","@type":"DefinedTerm","name":"follows","description":"..."}'
atom ID:    keccak256(ATOM_SALT, keccak256(toHex(json_string)))
```

| | |
|---|---|
| Determinism | **Fragile.** Key order, whitespace, and Unicode normalization all change the hash. `{"name":"follows","@type":"DefinedTerm"}` and `{"@type":"DefinedTerm","name":"follows"}` produce different atom IDs forever. Requires strict canonical JSON serialization. |
| Gas cost | 5-10x more bytes than a plain string. |
| External dependencies | None at read time, but requires a serialization spec at write time. |
| Self-describing | Yes — carries its own semantic context in the triple. |
| Schema.org alignment | Native. Machine-readable by any JSON-LD consumer. |
| Risk | Any future change to description, context URL, or schema version means a new atom ID. The old predicate atom and new predicate atom are permanently distinct on-chain. |

### Option C: Schema.org Action Types

Use the full Schema.org Action hierarchy (e.g., `FollowAction`, `EndorseAction`, `BookmarkAction`) as predicate atoms.

```
atom data:  '{"@context":"https://schema.org/","@type":"FollowAction","name":"follows"}'
```

| | |
|---|---|
| Semantic precision | High. Schema.org distinguishes FollowAction (active polling) from SubscribeAction (passive reception) from BefriendAction (reciprocal). |
| Protocol fit | **Poor.** Actions are temporal events with `agent`, `startTime`, `endTime`, `actionStatus`. Predicates are durable relationships. A triple `(Alice, follows, Bob)` is a standing fact — not a timestamped event. |
| Graph fragmentation | If some builders use `FollowAction` and others use `"follows"`, the graph splits. Two predicate atoms, two sets of triples, no interop. |
| Nuance value | The distinctions Schema.org draws (follow vs subscribe vs befriend) are real, but they matter at the **application layer**, not the **triple layer**. Whether "follows" means push notifications or active polling is a product decision, not a predicate identity question. |

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
| Hybrid potential | Could work as an optional enrichment layer — the canonical atom is a plain string, and a separate triple like `(follows_atom, has source, ipfs://QmXyz...)` links it to rich metadata. |

### Recommendation

**Use plain strings as the on-chain predicate atom data.** Publish structured metadata (DefinedTerm schemas, Schema.org Action mappings) as an off-chain registry — in this repo, in SDK docs, and optionally on IPFS for decentralized access.

This gives you:
- Deterministic, gas-cheap, dependency-free atom IDs
- The same interoperability guarantees as JSON-LD (because the registry maps strings to structured definitions)
- No graph fragmentation risk from serialization differences
- Freedom to enrich predicate metadata without changing on-chain identity

The DefinedTerm schemas in this document serve as the registry layer. The plain string list is the source of truth for on-chain atom creation.

---

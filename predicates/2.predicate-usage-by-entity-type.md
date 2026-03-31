# Predicate Usage by Entity Type

This document maps which predicates should be used when the **Subject** is a specific entity type, what **Object type** is expected for each predicate, and provides concrete triple examples. The goal is threefold:

1. **Developer guidance** — builders can look up a Subject type and see which predicates make sense
2. **Frontend recommendation engine** — UIs can show recommended predicates when a user is manually creating a triple
3. **Future on-chain registry** — these "interpretations" could be published to Intuition as triples themselves (see [Registry Strategy](#registry-strategy) at the end)

---

## How to Read This Document

Each entity type section contain a table with:

| Column | Meaning |
|---|---|
| **Predicate** | The canonical predicate string (from `predicate-catalog.md`) |
| **Expected Object Type** | The entity type or value type expected in the Object position |
| **Example Triple** | A concrete `(Subject, Predicate, Object)` example |
| **Priority** | `core` = almost every instance of this Subject should have it; `common` = frequently used; `optional` = situational |

Object types use these conventions:
- **Entity types** reference `intuition/data-structures/classifications/`: `Person`, `Organization`, `SoftwareSourceCode`, `Event`, `EthereumERC20`, etc.
- **Value types** describe the expected atom content: `String` (plain text), `URL` (web address), `ImageURL` (image resource), `DefinedTerm` (tag/keyword atom), `Thing` (any atom)
- **`Any`** means the predicate is flexible across object types

---

## Market-Optimized Triple Design

### The Liquidity Problem

Every triple in Intuition creates a vault. Users deposit (stake) into that vault to express conviction in the statement. The deposit amount, the bonding curve price, and the total vault TVL become **market signals** — they are the price mechanism that Hayek described, applied to knowledge claims.

This creates a critical design constraint: **if the same semantic intent gets split across many triples, the signal gets diluted.**

Consider "follow":

| Pattern | Triple | What Happens |
|---|---|---|
| **Nominative** (fragmented) | `(Kames, follow, Vitalik)` | Only Kames would deposit. Vault TVL = one person's conviction. Every person who follow Vitalik creates a separate triple, a separate vault, a separate market. 1,000 followers = 1,000 tiny markets. |
| **First-person** (consolidated) | `(I, follow, Vitalik)` | Everyone who follow Vitalik deposits on the **same** triple. One vault. One price curve. One deep market. 1,000 followers = one market with 1,000 deposits. |

The difference is not cosmetic. It is the difference between **a thousand puddles and a lake**. Deep markets produce better price signals. Shallow markets produce noise.

### The Subject Atom for Depositional Triples

Every triple requires three parts: `(Subject, Predicate, Object)`. For depositional triples — where the depositor IS the claim-maker — we need a universal Subject atom that consolidates everyone into the same market.

#### The `I` Atom

The canonical Subject for depositional triples is the **`I` atom** — a singleton atom whose data is the plain string `"I"`.

```
Atom data:   "I"
Atom ID:     keccak256(ATOM_SALT, keccak256(toHex("I")))
```

When Alice deposits on `(I, follow, Vitalik)`, she is asserting: "I follow Vitalik." When Bob deposits on the same triple, he is asserting the same thing about himself. The blockchain records EACH depositor's address, so the identity of the "I" is resolved per-depositor from the vault's deposit ledger.

**Why `I` and not something else:**

| Candidate | Example | Problem |
|---|---|---|
| `I` | `(I, follow, Vitalik)` | Reads naturally: "I follow Vitalik." Universal. One atom, one ID, one market per (predicate, object) pair. |
| `self` | `(self, follow, Vitalik)` | "Self follow Vitalik" doesn't read naturally. Ambiguous — self of what? |
| `anyone` | `(anyone, follow, Vitalik)` | "Anyone follow Vitalik" changes the meaning — it's a universalizing claim ("everyone does") rather than a personal assertion. |
| `user` | `(user, follow, Vitalik)` | "User follow Vitalik" sounds like a database row, not a human statement. |
| `we` | `(we, follow, Vitalik)` | "We follow Vitalik" implies a collective — but who is "we"? Useful for scoped communities (see below) but not as the universal default. |
| *(empty)* | `(_, follow, Vitalik)` | Triples require three atoms. An empty subject is not valid. |

`I` is the winner because:
1. It reads as natural language: `(I, follow, Vitalik)` = "I follow Vitalik"
2. It is universally applicable — any human can assert "I"
3. It produces one deterministic atom ID, so every SDK and builder arrives at the same Subject
4. The depositor's on-chain address resolves the ambiguity — it tells you WHO "I" is for each deposit

The `I` atom should be exported in the SDK alongside predicate constants:

```typescript
export const I_SUBJECT = 'I';
export const I_SUBJECT_ID = calculateAtomId(I_SUBJECT);
```

#### Verb Conjugation: Base Form, Not Third-Person

Because predicates read as `(I, predicate, object)`, verb predicates must use **base/infinitive form** — the form that works with "I":

| Base form (correct) | Third-person (wrong with `I`) | Why |
|---|---|---|
| `follow` | ~~`follows`~~ | "I follow Vitalik" not "I follows Vitalik" |
| `like` | ~~`likes`~~ | "I like Ethereum" not "I likes Ethereum" |
| `trust` | ~~`trusts`~~ | "I trust CertiK" not "I trusts CertiK" |
| `endorse` | ~~`endorses`~~ | "I endorse EIP-4844" not "I endorses EIP-4844" |
| `agree with` | ~~`agrees with`~~ | "I agree with this" not "I agrees with this" |
| `support` | ~~`supports`~~ | "I support MiCA" not "I supports MiCA" |
| `contain` | ~~`contains`~~ | "I contain" / "(Collection, contain, Item)" |
| `use` | ~~`uses`~~ | "(Aave, use, Chainlink)" |
| `implement` | ~~`implements`~~ | "(USDC, implement, ERC-20)" |

For attributive triples like `(Aave, use, Chainlink)`, the base form reads slightly differently than English prose ("Aave uses Chainlink"), but predicates are **relationship labels**, not sentences. Base form is standard in ontology design (RDF, Wikidata) and is the correct choice when `I` is the primary subject pattern.

**SDK backward compatibility:** The current SDK exports `CONTAINS_PREDICATE = 'contains'`, `IS_PREDICATE = 'is'`, etc. Migrating to base form predicates will create new atom IDs (since `"follow"` hashes differently from `"follows"`). This is a planned migration — the old predicates remain valid but the new base-form predicates become canonical going forward.

#### When NOT to Use `I`

Not all depositional triples use `I`. There are two other valid Subject patterns:

**Entity-anchored comparisons** — the Subject is a real entity, not `I`:

```
(Rust, better than, Solidity)         ← Subject is Rust, not I
(Ethereum, ranked above, Solana)      ← Subject is Ethereum
(Uniswap, compete with, Curve)      ← Subject is Uniswap
```

These are still depositional (anyone can deposit to agree), but the Subject is one of the two entities being compared. It wouldn't make sense to say `(I, better than, Solidity)` — the comparison is between Rust and Solidity, and depositors express agreement with that comparison.

**Community-scoped subjects** — a community or role atom instead of `I`:

```
(developers, recommends, Foundry)          ← scoped to developers
(Ethereum community, support, EIP-4844)   ← scoped to a community
(security researchers, trust, Trail of Bits) ← scoped to a role
```

These create **scoped markets** where the same predicate can have different TVL across different communities. You could have:

```
(I, recommends, Foundry)                     ← universal market: 500 ETH
(developers, recommends, Foundry)             ← developer-scoped: 300 ETH
(auditors, recommends, Foundry)               ← auditor-scoped: 20 ETH
```

This is a valid extension pattern but should be used sparingly. Each community subject fragments the market. The universal `I` market should always exist as the primary signal; scoped markets are supplementary.

#### Subject Decision Tree

```
Is this a first-person claim (the depositor is claiming it about themselves)?
├── YES → Is it a comparison between two entities?
│         ├── YES → Subject = the entity being ranked/compared
│         │         e.g. (Rust, better than, Solidity)
│         └── NO  → Subject = I
│                   e.g. (I, follow, Vitalik)
└── NO  → Subject = the specific entity the claim is about
          e.g. (Ethereum, created by, Vitalik)
```

### Three Classes of Triples

With the Subject question resolved, there are three distinct triple patterns:

#### Depositional Triples (First-Person Markets)

Subject is `I`. Anyone deposits to make the claim about themselves. The vault's depositor list = everyone who holds this position.

```
(I, follow, Vitalik)          ← "I follow Vitalik"
(I, bullish on, Ethereum)      ← "I'm bullish on ETH"
(I, trust, Trail of Bits)     ← "I trust this auditor"
(I, expert in, ZK proofs)      ← "I claim expertise in ZK"
(I, voted for, Proposal 42)    ← "I vote for this"
```

**How to read the data:** Each depositor's address + deposit amount = one person's conviction. Depositor count = headcount. TVL = capital-weighted conviction.

#### Comparative Triples (Opinion Markets)

Subject is a real entity. Object is the entity being compared against. Deposits mean agreement with the comparison.

```
(Rust, better than, Solidity)         ← depositors agree Rust > Solidity
(Solana, alternative to, Ethereum)    ← depositors agree these are substitutes
(Uniswap v4, supersede, Uniswap v3) ← depositors agree v4 replaces v3
```

**How to read the data:** TVL = market conviction that the comparison holds. Compare with the inverse triple `(Solidity, better than, Rust)` for the full picture. The TVL ratio is the market's verdict.

#### Attributive Triples (Factual Claims)

Subject is a specific entity. The triple asserts a fact about the world. Deposits mean "I agree this is true."

```
(Ethereum, created by, Vitalik)          ← factual attribution
(Aave v3, audited by, Trail of Bits)     ← factual audit claim
(USDC, pegged to, USD)                    ← factual economic relationship
(Devcon, located in, Bangkok)              ← factual location
```

**How to read the data:** TVL = market confidence that the factual claim is accurate. Declining TVL = market doubts the claim.

### Full Predicate Classification Table

#### Depositional Predicates — Social

| Predicate | Triple | What a Deposit Means | Example |
|---|---|---|---|
| `follow` | `(I, follow, Vitalik)` | "I follow Vitalik" | 500 depositors = 500 followers, TVL = aggregate follow conviction |
| `like` | `(I, like, Ethereum)` | "I like Ethereum" | Deep market = strong collective positive sentiment |
| `endorse` | `(I, endorse, EIP-4844)` | "I publicly endorse this proposal" | Higher individual deposits = stronger personal endorsement |
| `trust` | `(I, trust, CertiK)` | "I trust this auditor" | TVL becomes a trust score for the Object |
| `distrust` | `(I, distrust, Scam Project)` | "I distrust this entity" | TVL = aggregate distrust signal; a warning system |
| `vouch for` | `(I, vouch for, Alice)` | "I stake my credibility on Alice" | Deposit size = skin in the game behind the vouch |
| `blocked` | `(I, blocked, Spam Account)` | "I've excluded this from my view" | Collective block signal = community moderation |
| `reported` | `(I, reported, Phishing Site)` | "I'm flagging this for harm" | TVL = severity of community concern |

#### Depositional Predicates — Sentiment and Opinion

| Predicate | Triple | What a Deposit Means | Example |
|---|---|---|---|
| `agree with` | `(I, agree with, EIP-4844)` | "I agree with this position" | Deposit for / against = a prediction market on the claim |
| `disagree with` | `(I, disagree with, PoW Revival)` | "I disagree with this position" | Counter-market to `agree with` |
| `support` | `(I, support, MiCA regulation)` | "I actively support this cause" | TVL = movement strength |
| `oppose` | `(I, oppose, PoS transition)` | "I actively oppose this cause" | Counter-market to `support` |
| `bullish on` | `(I, bullish on, Ethereum)` | "I'm bullish on ETH" | TVL = aggregate bullish conviction; share price = market temperature |
| `bearish on` | `(I, bearish on, Memecoins)` | "I'm bearish on this" | Paired with `bullish on` — the ratio is the signal |
| `skeptical of` | `(I, skeptical of, Restaking)` | "I have doubts about this" | Softer than `bearish on` |
| `neutral on` | `(I, neutral on, L2 wars)` | "I'm explicitly taking no side" | Useful for measuring fence-sitters |

#### Depositional Predicates — Expertise and Learning

| Predicate | Triple | What a Deposit Means | Example |
|---|---|---|---|
| `expert in` | `(I, expert in, ZK proofs)` | "I claim expertise in this domain" | Deposit = skin-in-the-game expertise claim; can be challenged |
| `studied` | `(I, studied, cryptography)` | "I've invested learning effort here" | TVL = size of the learning community |
| `speak` | `(I, speak, Rust)` | "I know this language" | Community size signal |

#### Depositional Predicates — Governance

| Predicate | Triple | What a Deposit Means | Example |
|---|---|---|---|
| `voted for` | `(I, voted for, Proposal 42)` | "I vote in favor" | TVL = weighted vote tally |
| `voted against` | `(I, voted against, Proposal 42)` | "I vote against" | Paired with `voted for` — ratio = outcome |
| `delegated to` | `(I, delegated to, Bob)` | "I delegate my governance power to Bob" | TVL = Bob's delegated authority |

#### Depositional Predicates — Economic

| Predicate | Triple | What a Deposit Means | Example |
|---|---|---|---|
| `invested in` | `(I, invested in, Uniswap)` | "I have a financial position in this" | TVL = collective investor conviction |
| `staked in` | `(I, staked in, Ethereum Beacon Chain)` | "I'm staking in this protocol" | Community staking signal |

#### Comparative Predicates — Ranking and Comparison

These use real entity Subjects, not `I`. Deposits mean "I agree with this comparison."

| Predicate | Triple | What a Deposit Means | Example |
|---|---|---|---|
| `better than` | `(Rust, better than, Solidity)` | "I believe Rust is better than Solidity" | Compare with `(Solidity, better than, Rust)` for the full market |
| `worse than` | `(PoW, worse than, PoS)` | "I believe PoW is worse" | Same pattern — comparative markets |
| `outperform` | `(Solana, outperform, Ethereum)` | "I believe Solana outperform" | Evidence-grade comparison claim |
| `compete with` | `(Uniswap, compete with, Curve)` | "I agree these are competitors" | Market validation of competitive relationship |
| `alternative to` | `(Solana, alternative to, Ethereum)` | "I agree these are substitutes" | Substitutability market |
| `ranked above` | `(Ethereum, ranked above, Solana)` | "I rank Ethereum higher" | Pairwise ranking market |

#### Attributive Predicates — Factual Claims

These use specific entity Subjects. The market is about whether the claim is TRUE.

| Predicate | Example Triple | What Deposits Mean |
|---|---|---|
| `is` | `(Ethereum, is, blockchain)` | "I agree with this classification" |
| `has type` | `(Uniswap, has type, DEX)` | "I agree with this type assignment" |
| `instance of` | `(USDC, instance of, stablecoin)` | "I agree USDC is a stablecoin" |
| `subclass of` | `(DEX, subclass of, exchange)` | "I agree with this taxonomy" |
| `same as` | `(ETH, same as, Ether)` | "I agree these are the same entity" |
| `created by` | `(Ethereum, created by, Vitalik)` | "I agree with this attribution" |
| `authored by` | `(Whitepaper, authored by, Satoshi)` | "I agree with this authorship claim" |
| `founded` | `(Vitalik, founded, Ethereum)` | "I agree this is true" |
| `forked from` | `(Sushiswap, forked from, Uniswap)` | "I agree this is the lineage" |
| `located in` | `(Devcon, located in, Bangkok)` | "I agree with this location" |
| `available on` | `(USDC, available on, Ethereum)` | "I agree USDC is on Ethereum" |
| `implement` | `(USDC, implement, ERC-20)` | "I agree with this standard claim" |
| `audited by` | `(Aave v3, audited by, Trail of Bits)` | "I confirm this audit happened" — high TVL = strong market confidence in the audit |
| `verified by` | `(Contract, verified by, CertiK)` | "I confirm this verification" |
| `backed by` | `(DAI, backed by, ETH)` | "I agree with this collateral claim" |
| `pegged to` | `(USDC, pegged to, USD)` | "I agree with this peg" — deposits could DECREASE if confidence in the peg drops |
| `regulated by` | `(USDC, regulated by, SEC)` | "I agree with this regulatory claim" |
| `listed on` | `(ETH, listed on, Coinbase)` | "I agree with this listing claim" |
| `depend on` | `(Arbitrum, depend on, Ethereum)` | "I agree with this dependency" |
| `supersede` | `(Uniswap v4, supersede, v3)` | "I agree v4 replaces v3" |
| `preceded by` | `(Merge, preceded by, Beacon Chain)` | "I agree with this sequence" |
| `governed by` | `(Uniswap, governed by, UNI holders)` | "I agree with this governance structure" |

#### Hybrid Predicates — Context-Dependent

Some predicates can be either depositional (`I` as Subject) or attributive (specific entity as Subject) depending on who is making the claim:

| Predicate | As Depositional | As Attributive |
|---|---|---|
| `member of` | `(I, member of, Ethereum Foundation)` — "I am a member" | `(Vitalik, member of, Ethereum Foundation)` — "I agree Vitalik is a member" |
| `employed by` | `(I, employed by, Uniswap Labs)` — "I work here" | `(Alice, employed by, Uniswap Labs)` — "I agree Alice works there" |
| `affiliated with` | `(I, affiliated with, a16z)` — "I have this affiliation" | `(Protocol X, affiliated with, a16z)` — "I agree with this affiliation" |
| `certified by` | `(I, certified by, Ethereum Foundation)` — "I hold this credential" | `(Alice, certified by, Ethereum Foundation)` — "I agree Alice holds this" |
| `mentor of` | `(I, mentor of, Alice)` — "I mentor Alice" | `(Bob, mentor of, Alice)` — "I agree Bob mentors Alice" |
| `student of` | `(I, student of, Bob)` — "I am Bob's student" | `(Alice, student of, Bob)` — "I agree Alice studies under Bob" |
| `partner of` | `(I, partner of, SWIFT)` — "We are partners" | `(Chainlink, partner of, SWIFT)` — "I agree they are partners" |
| `curated by` | `(I, curated by, DeFi Blue Chips)` — "I curate this" | `(DeFi Blue Chips, curated by, Alice)` — "I agree Alice curates this" |

**Design guidance for hybrid predicates:** Default to the `I` pattern when the relationship is personal. Only use the attributive pattern when making claims about third parties. If your frontend knows the user is the subject, route to the `(I, predicate, object)` market.

### Interpreting Market Data

The triple pattern changes how we interpret vault data:

#### For `I`-Subject Triples (Depositional Markets)

| Signal | Interpretation |
|---|---|
| Number of unique depositors | How many people hold this position (follower count, supporter count, etc.) |
| Total vault TVL | Aggregate conviction weighted by capital |
| Share price (bonding curve) | The "temperature" of the market — early depositors got in cheap, rising price = growing momentum |
| Individual deposit size | How strongly one person holds this position |
| Deposit/withdrawal ratio | Is sentiment growing or declining? |
| Time-weighted deposits | Are people holding long-term or churning? |

**Example:** The triple `(I, bullish on, Ethereum)` has a vault with 2,000 depositors and 500 ETH TVL. The paired triple `(I, bearish on, Ethereum)` has 200 depositors and 30 ETH TVL. The market signal: Ethereum sentiment is 10:1 bullish by headcount and ~17:1 by capital. The ratio IS the sentiment index.

#### For Entity-Subject Triples (Attributive and Comparative Markets)

| Signal | Interpretation |
|---|---|
| Total vault TVL | Market confidence that this factual claim or comparison is true |
| TVL trend over time | Is confidence growing or eroding? |
| Large deposits from known experts | Weighted expert opinion on the claim |
| Low/declining TVL | The market doubts this claim — potentially false or outdated |

**Example:** `(Aave v3, audited by, Trail of Bits)` has 100 ETH TVL from 50 depositors including known security researchers. This is strong market validation of the audit claim. If Trail of Bits disputes it and deposits drop, the market is self-correcting.

### Market Consolidation Guidelines

When designing new predicates or recommending triples to users:

1. **Always use `I` as the Subject for first-person claims.** If the user IS the claim-maker, route them to the `(I, predicate, object)` triple. Never create `(MyName, follow, X)` when `(I, follow, X)` exists.

2. **Use real entities as Subjects only for factual claims and comparisons.** `(Ethereum, created by, Vitalik)` needs Ethereum as the Subject because the claim is about Ethereum. `(Rust, better than, Solidity)` needs Rust because the comparison is between two specific things.

3. **Use paired `I` triples for debates.** `(I, bullish on, ETH)` and `(I, bearish on, ETH)` create a natural for/against market. The TVL ratio is the community sentiment score.

4. **Use attributive triples for objective claims.** `(Ethereum, created by, Vitalik)` is a fact about the world, not a personal assertion. It should have a specific subject.

5. **For comparison predicates, the triple IS the market.** `(Rust, better than, Solidity)` is a shared opinion market. Everyone who agrees deposits on it. Everyone who disagrees either deposits on the inverse `(Solidity, better than, Rust)` or redeems from the original.

6. **Track the depositional/attributive classification in the registry.** The on-chain registry (see [Registry Strategy](#registry-strategy)) should mark each predicate's market pattern so frontends can route users correctly.

---

## Person

Schema.org type: `Person`
Classification: `intuition/data-structures/classifications/person/`

A human identity — the most relationship-rich entity type in a social knowledge graph.

> **Market design note:** Person is the entity type most affected by the depositional/attributive split. When the Person is the **current user**, most social and opinion predicates should use the **depositional pattern** (shared market, deposit = first-person claim). When the Person is a **third party**, use the attributive pattern. The tables below mark each predicate with `D` (depositional) or `A` (attributive) to indicate the recommended market pattern.

### Identity and Metadata (Attributive)

| Predicate | Expected Object Type | Example Triple | Priority |
|---|---|---|---|
| `is` | `DefinedTerm` | `(Vitalik, is, researcher)` | core |
| `has type` | `DefinedTerm` | `(Vitalik, has type, public figure)` | common |
| `has tag` | `DefinedTerm` | `(Alice, has tag, Solidity developer)` | core |
| `has category` | `DefinedTerm` | `(Alice, has category, builder)` | common |
| `same as` | `Person` | `(vitalik.eth, same as, Vitalik Buterin)` | common |
| `alias of` | `Person` | `(V, alias of, Vitalik Buterin)` | optional |
| `linked account` | `SocialMediaAccount` | `(Alice, linked account, x.com/alice_eth)` | core |
| `url` | `URL` | `(Alice, url, alice.xyz)` | common |
| `imgUrl` | `ImageURL` | `(Alice, imgUrl, alice-avatar.png)` | common |
| `has description` | `String` | `(Alice, has description, "Smart contract auditor and DeFi researcher")` | common |
| `located in` | `Location` | `(Alice, located in, Berlin)` | optional |
| `speak` | `DefinedTerm` | `(Alice, speak, Rust)` | optional |

### Social Relationships (Depositional)

When the user IS the person, use the shared depositional market. When claiming about a third party, use attributive.

| Predicate | Expected Object Type | Market Pattern | Depositional Triple | Attributive Triple | Priority |
|---|---|---|---|---|---|
| `follow` | `Person` / `Organization` / `Thing` | `D` | `(I, follow, Bob)` — deposit = "I follow Bob" | `(Alice, follow, Bob)` — only for third-party claims | core |
| `like` | `Any` | `D` | `(I, like, Uniswap v4)` — deposit = "I like this" | `(Alice, like, Uniswap v4)` | core |
| `endorse` | `Person` / `Organization` / `SoftwareSourceCode` | `D` | `(I, endorse, EIP-4844)` — deposit = "I endorse this" | `(Vitalik, endorse, EIP-4844)` — notable claim about public figure | common |
| `trust` | `Person` / `Organization` | `D` | `(I, trust, Trail of Bits)` — deposit = "I trust them" | `(Alice, trust, Trail of Bits)` | common |
| `distrust` | `Person` / `Organization` | `D` | `(I, distrust, Scam Project)` — deposit = "I distrust this" | `(Alice, distrust, Scam Project)` | common |
| `vouch for` | `Person` | `D` | `(I, vouch for, Alice)` — deposit = "I vouch for Alice" | `(Bob, vouch for, Alice)` | common |
| `blocked` | `Person` | `D` | `(I, blocked, Spam Account)` — deposit = "I block this" | — | optional |
| `reported` | `Person` / `Thing` | `D` | `(I, reported, Phishing Site)` — deposit = "I report this" | — | optional |

### Opinions and Sentiment (Depositional)

All opinion predicates should use the depositional pattern. The whole point is to aggregate conviction in shared markets.

| Predicate | Expected Object Type | Depositional Triple | What the Vault Measures | Priority |
|---|---|---|---|---|
| `agree with` | `DefinedTerm` / `Thing` | `(I, agree with, EIP-4844)` | Support for a position — pair with `disagree with` for a debate market | common |
| `disagree with` | `DefinedTerm` / `Thing` | `(I, disagree with, PoW Revival)` | Opposition to a position | common |
| `support` | `DefinedTerm` / `Organization` / `Thing` | `(I, support, MiCA regulation)` | Movement backing strength | common |
| `oppose` | `DefinedTerm` / `Organization` / `Thing` | `(I, oppose, PoS transition)` | Movement resistance strength | common |
| `skeptical of` | `Any` | `(I, skeptical of, Restaking)` | Cautious doubt level | optional |
| `bullish on` | `Any` | `(I, bullish on, Ethereum)` | Aggregate bullish conviction — ratio with `bearish on` = sentiment index | common |
| `bearish on` | `Any` | `(I, bearish on, Memecoins)` | Aggregate bearish conviction | common |
| `neutral on` | `Any` | `(I, neutral on, L2 wars)` | Explicit fence-sitting count | optional |
| `reviewed` | `SoftwareSourceCode` / `Product` / `Thing` | `(I, reviewed, Uniswap v4)` | Number of reviewers + conviction weight | common |
| `recommended` | `Any` | `(I, recommended, Hardhat)` | Recommendation strength | common |

### Professional and Knowledge (Hybrid)

| Predicate | Expected Object Type | Market Pattern | Depositional Triple | Attributive Triple | Priority |
|---|---|---|---|---|---|
| `member of` | `Organization` | `D/A` | `(I, member of, Ethereum Foundation)` — "I'm a member" | `(Alice, member of, Ethereum Foundation)` — third-party claim | common |
| `employed by` | `Organization` | `D/A` | `(I, employed by, Uniswap Labs)` — "I work here" | `(Alice, employed by, Uniswap Labs)` | common |
| `affiliated with` | `Organization` | `D/A` | `(I, affiliated with, a16z)` — "I have this affiliation" | `(Protocol X, affiliated with, a16z)` | optional |
| `founded` | `Organization` / `SoftwareSourceCode` | `A` | — | `(Vitalik, founded, Ethereum)` — factual claim | common |
| `contributed to` | `SoftwareSourceCode` / `Organization` | `D` | `(I, contributed to, OpenZeppelin)` — "I contributed" | `(Alice, contributed to, OpenZeppelin)` | common |
| `expert in` | `DefinedTerm` | `D` | `(I, expert in, ZK proofs)` — "I claim expertise" | `(Alice, expert in, ZK proofs)` | common |
| `teach` | `DefinedTerm` | `D` | `(I, teach, Solidity)` — "I teach this" | `(Alice, teach, Solidity)` | optional |
| `studied` | `DefinedTerm` / `Organization` | `D` | `(I, studied, cryptography)` — "I've studied this" | — | optional |
| `learned from` | `Person` | `D` | `(I, learned from, Bob)` — "I learned from Bob" | — | optional |
| `mentor of` | `Person` | `D/A` | `(I, mentor of, Alice)` — "I mentor Alice" | `(Bob, mentor of, Alice)` | optional |
| `student of` | `Person` / `Organization` | `D` | `(I, student of, Bob)` — "I'm Bob's student" | — | optional |
| `certified by` | `Organization` | `D/A` | `(I, certified by, Ethereum Foundation)` — "I hold this cert" | `(Alice, certified by, Ethereum Foundation)` | optional |

### Governance (Depositional)

Governance predicates are almost always depositional — the depositor is the voter.

| Predicate | Expected Object Type | Depositional Triple | What the Vault Measures | Priority |
|---|---|---|---|---|
| `voted for` | `DefinedTerm` / `Thing` | `(I, voted for, Proposal 42)` | Weighted vote tally in favor — TVL ratio with `voted against` = outcome | common |
| `voted against` | `DefinedTerm` / `Thing` | `(I, voted against, Proposal 42)` | Weighted vote tally against | common |
| `delegated to` | `Person` | `(I, delegated to, Bob)` | Bob's delegated authority weight | common |
| `proposed` | `DefinedTerm` / `Thing` | — (use attributive: `(Alice, proposed, EIP-7702)`) | Factual claim about authorship | optional |

### Economic (Depositional)

| Predicate | Expected Object Type | Depositional Triple | What the Vault Measures | Priority |
|---|---|---|---|---|
| `invested in` | `Organization` / `SoftwareSourceCode` / `EthereumERC20` | `(I, invested in, Uniswap)` | Collective investor conviction | common |
| `staked in` | `SoftwareSourceCode` / `EthereumERC20` | `(I, staked in, Ethereum Beacon Chain)` | Community staking signal | common |

---

## Organization (Company)

Schema.org type: `Organization`
Classification: `intuition/data-structures/classifications/company/`

A company, foundation, DAO, protocol team, or any collective entity.

### Identity and Metadata

| Predicate | Expected Object Type | Example Triple | Priority |
|---|---|---|---|
| `is` | `DefinedTerm` | `(Uniswap Labs, is, DeFi company)` | core |
| `has type` | `DefinedTerm` | `(Ethereum Foundation, has type, non-profit)` | core |
| `has tag` | `DefinedTerm` | `(Aave, has tag, lending)` | core |
| `has category` | `DefinedTerm` | `(Chainlink, has category, oracle)` | common |
| `same as` | `Organization` | `(Uniswap Labs, same as, uniswap.org)` | optional |
| `linked account` | `SocialMediaAccount` | `(Intuition, linked account, x.com/0xIntuition)` | core |
| `url` | `URL` | `(Uniswap Labs, url, uniswap.org)` | core |
| `imgUrl` | `ImageURL` | `(Uniswap Labs, imgUrl, uniswap-logo.svg)` | common |
| `has description` | `String` | `(Aave, has description, "Decentralized lending protocol")` | common |
| `located in` | `Location` | `(Coinbase, located in, San Francisco)` | optional |

### Relationships

| Predicate | Expected Object Type | Example Triple | Priority |
|---|---|---|---|
| `partner of` | `Organization` | `(Chainlink, partner of, SWIFT)` | common |
| `affiliated with` | `Organization` | `(Protocol X, affiliated with, a16z)` | common |
| `invested in` | `Organization` / `SoftwareSourceCode` / `EthereumERC20` | `(a16z, invested in, Uniswap)` | common |
| `compete with` | `Organization` | `(Uniswap, compete with, Curve)` | common |
| `founded` | `SoftwareSourceCode` / `Organization` | `(Ethereum Foundation, founded, Devcon)` | optional |
| `created by` | `Person` | `(Uniswap Labs, created by, Hayden Adams)` | common |
| `sponsored by` | `Organization` | `(Devcon, sponsored by, Ethereum Foundation)` | optional |

### Technical

| Predicate | Expected Object Type | Example Triple | Priority |
|---|---|---|---|
| `use` | `SoftwareSourceCode` / `DefinedTerm` | `(Aave, use, Chainlink)` | common |
| `implement` | `DefinedTerm` | `(Circle, implement, ERC-20)` | optional |
| `governed by` | `DefinedTerm` / `EthereumERC20` | `(Uniswap, governed by, UNI holders)` | common |
| `compliant with` | `DefinedTerm` | `(Coinbase, compliant with, MiCA)` | optional |
| `regulated by` | `Organization` | `(Coinbase, regulated by, SEC)` | optional |

### Opinions and Sentiment

| Predicate | Expected Object Type | Example Triple | Priority |
|---|---|---|---|
| `support` | `DefinedTerm` / `Thing` | `(Coinbase, support, MiCA regulation)` | common |
| `oppose` | `DefinedTerm` / `Thing` | `(Mining Pool X, oppose, PoS transition)` | common |
| `endorse` | `Person` / `Organization` / `Thing` | `(a16z, endorse, EIP-4844)` | optional |

---

## Software / Protocol

Schema.org type: `SoftwareSourceCode`
Classification: `intuition/data-structures/classifications/software/`

A protocol, dApp, library, framework, or any code-based project.

### Identity and Metadata

| Predicate | Expected Object Type | Example Triple | Priority |
|---|---|---|---|
| `is` | `DefinedTerm` | `(Uniswap, is, DEX)` | core |
| `has type` | `DefinedTerm` | `(MetaMask, has type, wallet)` | core |
| `has tag` | `DefinedTerm` | `(Aave, has tag, lending)` | core |
| `has category` | `DefinedTerm` | `(Hardhat, has category, developer tools)` | common |
| `url` | `URL` | `(Uniswap, url, uniswap.org)` | core |
| `imgUrl` | `ImageURL` | `(Aave, imgUrl, aave-ghost-logo.svg)` | common |
| `has description` | `String` | `(Hardhat, has description, "Ethereum development environment")` | common |
| `has source` | `URL` | `(OpenZeppelin, has source, github.com/OpenZeppelin)` | common |

### Authorship and Lineage

| Predicate | Expected Object Type | Example Triple | Priority |
|---|---|---|---|
| `created by` | `Person` / `Organization` | `(Ethereum, created by, Vitalik Buterin)` | core |
| `authored by` | `Person` | `(Whitepaper, authored by, Satoshi)` | common |
| `forked from` | `SoftwareSourceCode` | `(Sushiswap, forked from, Uniswap)` | common |
| `derived from` | `SoftwareSourceCode` / `DefinedTerm` | `(Optimism, derived from, Ethereum)` | common |
| `inspired by` | `SoftwareSourceCode` / `DefinedTerm` | `(Solana, inspired by, PBFT)` | optional |
| `predecessor of` | `SoftwareSourceCode` | `(Uniswap v2, predecessor of, Uniswap v3)` | common |
| `successor of` | `SoftwareSourceCode` | `(Uniswap v3, successor of, Uniswap v2)` | common |
| `supersede` | `SoftwareSourceCode` | `(Uniswap v4, supersede, Uniswap v3)` | common |

### Technical Relationships

| Predicate | Expected Object Type | Example Triple | Priority |
|---|---|---|---|
| `use` | `SoftwareSourceCode` / `DefinedTerm` | `(Aave, use, Chainlink)` | core |
| `depend on` | `SoftwareSourceCode` | `(Arbitrum, depend on, Ethereum)` | core |
| `compatible with` | `SoftwareSourceCode` / `DefinedTerm` | `(MetaMask, compatible with, Ethereum)` | common |
| `implement` | `DefinedTerm` | `(USDC, implement, ERC-20)` | common |
| `available on` | `SoftwareSourceCode` / `DefinedTerm` | `(USDC, available on, Ethereum)` | common |
| `alternative to` | `SoftwareSourceCode` | `(Solana, alternative to, Ethereum)` | common |
| `compete with` | `SoftwareSourceCode` | `(Uniswap, compete with, Curve)` | common |

### Trust and Verification

| Predicate | Expected Object Type | Example Triple | Priority |
|---|---|---|---|
| `audited by` | `Organization` | `(Aave v3, audited by, Trail of Bits)` | common |
| `verified by` | `Organization` / `Person` | `(Smart Contract, verified by, CertiK)` | common |
| `governed by` | `DefinedTerm` / `EthereumERC20` | `(Uniswap, governed by, UNI holders)` | common |

### Ranking and Comparison

| Predicate | Expected Object Type | Example Triple | Priority |
|---|---|---|---|
| `better than` | `SoftwareSourceCode` | `(Rust, better than, Solidity)` | optional |
| `worse than` | `SoftwareSourceCode` | `(PoW, worse than, PoS)` | optional |
| `equivalent to` | `SoftwareSourceCode` | `(USDC, equivalent to, USDT)` | optional |
| `outperform` | `SoftwareSourceCode` | `(Solana, outperform, Ethereum)` | optional |

### Lifecycle

| Predicate | Expected Object Type | Example Triple | Priority |
|---|---|---|---|
| `deprecated by` | `SoftwareSourceCode` / `DefinedTerm` | `(ERC-20 approve, deprecated by, ERC-20 permit)` | optional |
| `replaced by` | `SoftwareSourceCode` | `(Sushiswap Chef, replaced by, MasterChefV2)` | optional |

---

## ERC-20 Token

Classification: `intuition/data-structures/classifications/ethereum-erc20/`

A fungible token on an EVM chain.

### Identity and Metadata

| Predicate | Expected Object Type | Example Triple | Priority |
|---|---|---|---|
| `is` | `DefinedTerm` | `(USDC, is, stablecoin)` | core |
| `has type` | `DefinedTerm` | `(UNI, has type, governance token)` | core |
| `has tag` | `DefinedTerm` | `(AAVE, has tag, DeFi)` | core |
| `has category` | `DefinedTerm` | `(WBTC, has category, wrapped asset)` | common |
| `instance of` | `DefinedTerm` | `(USDC, instance of, stablecoin)` | common |
| `url` | `URL` | `(USDC, url, circle.com/usdc)` | common |
| `imgUrl` | `ImageURL` | `(ETH, imgUrl, eth-diamond.svg)` | common |
| `has description` | `String` | `(UNI, has description, "Governance token of the Uniswap protocol")` | common |

### Economic Relationships

| Predicate | Expected Object Type | Example Triple | Priority |
|---|---|---|---|
| `priced in` | `EthereumERC20` / `DefinedTerm` | `(NFT Collection, priced in, ETH)` | common |
| `backed by` | `EthereumERC20` / `DefinedTerm` | `(DAI, backed by, ETH)` | common |
| `pegged to` | `DefinedTerm` / `EthereumERC20` | `(USDC, pegged to, USD)` | common |
| `listed on` | `Organization` / `SoftwareSourceCode` | `(ETH, listed on, Coinbase)` | common |
| `available on` | `SoftwareSourceCode` / `DefinedTerm` | `(USDC, available on, Arbitrum)` | common |

### Technical and Governance

| Predicate | Expected Object Type | Example Triple | Priority |
|---|---|---|---|
| `implement` | `DefinedTerm` | `(USDC, implement, ERC-20)` | core |
| `created by` | `Person` / `Organization` | `(UNI, created by, Uniswap Labs)` | common |
| `governed by` | `DefinedTerm` / `Organization` | `(UNI, governed by, UNI holders)` | optional |
| `regulated by` | `Organization` | `(USDC, regulated by, SEC)` | optional |
| `compliant with` | `DefinedTerm` | `(USDC, compliant with, MiCA)` | optional |
| `equivalent to` | `EthereumERC20` | `(USDC, equivalent to, USDT)` | optional |
| `compete with` | `EthereumERC20` | `(USDC, compete with, DAI)` | optional |

---

## Event

Schema.org type: `Event`
Classification: `intuition/data-structures/classifications/event/`

A conference, hackathon, governance event, or notable occurrence.

### Identity and Metadata

| Predicate | Expected Object Type | Example Triple | Priority |
|---|---|---|---|
| `is` | `DefinedTerm` | `(Devcon, is, conference)` | core |
| `has type` | `DefinedTerm` | `(ETHDenver, has type, hackathon)` | core |
| `has tag` | `DefinedTerm` | `(Devcon, has tag, Ethereum)` | core |
| `located in` | `Location` | `(Devcon, located in, Bangkok)` | core |
| `url` | `URL` | `(Devcon, url, devcon.org)` | common |
| `imgUrl` | `ImageURL` | `(ETHDenver, imgUrl, ethdenver-banner.png)` | optional |
| `has description` | `String` | `(Devcon, has description, "Annual Ethereum developer conference")` | common |

### Relationships

| Predicate | Expected Object Type | Example Triple | Priority |
|---|---|---|---|
| `created by` | `Organization` | `(Devcon, created by, Ethereum Foundation)` | core |
| `sponsored by` | `Organization` | `(Devcon, sponsored by, Ethereum Foundation)` | common |
| `featured in` | `DefinedTerm` / `Thing` | `(Uniswap v4, featured in, Devcon)` | optional |

### Temporal

| Predicate | Expected Object Type | Example Triple | Priority |
|---|---|---|---|
| `preceded by` | `Event` | `(Devcon 7, preceded by, Devcon 6)` | common |
| `followed by` | `Event` | `(Devcon 6, followed by, Devcon 7)` | common |
| `predecessor of` | `Event` | `(Devcon 6, predecessor of, Devcon 7)` | common |
| `successor of` | `Event` | `(Devcon 7, successor of, Devcon 6)` | common |
| `enabled by` | `Thing` | `(DeFi Summer, enabled by, Compound governance)` | optional |
| `triggered` | `Thing` | `(Terra collapse, triggered, Contagion)` | optional |

---

## Article / Written Content

Schema.org type: `Article`
Classification: `intuition/data-structures/classifications/article/`

An article, whitepaper, blog post, research paper, or written work.

### Identity and Metadata

| Predicate | Expected Object Type | Example Triple | Priority |
|---|---|---|---|
| `is` | `DefinedTerm` | `(Bitcoin Whitepaper, is, whitepaper)` | core |
| `has type` | `DefinedTerm` | `(EIP-4844, has type, improvement proposal)` | core |
| `has tag` | `DefinedTerm` | `(Bitcoin Whitepaper, has tag, cryptography)` | core |
| `url` | `URL` | `(Bitcoin Whitepaper, url, bitcoin.org/bitcoin.pdf)` | core |
| `has description` | `String` | `(Bitcoin Whitepaper, has description, "A Peer-to-Peer Electronic Cash System")` | common |
| `published at` | `Organization` / `URL` | `(Whitepaper, published at, bitcoin.org)` | common |
| `has source` | `URL` | `(EIP-4844, has source, eips.ethereum.org/EIPS/eip-4844)` | common |

### Authorship

| Predicate | Expected Object Type | Example Triple | Priority |
|---|---|---|---|
| `authored by` | `Person` | `(Bitcoin Whitepaper, authored by, Satoshi)` | core |
| `created by` | `Person` / `Organization` | `(EIP-4844, created by, Dankrad Feist)` | core |

### Citation Graph

| Predicate | Expected Object Type | Example Triple | Priority |
|---|---|---|---|
| `reference` | `Article` / `SoftwareSourceCode` / `Thing` | `(Ethereum Whitepaper, reference, Bitcoin Whitepaper)` | common |
| `cited by` | `Article` | `(Bitcoin Whitepaper, cited by, Ethereum Whitepaper)` | common |
| `derived from` | `Article` / `Thing` | `(EIP-4844, derived from, Danksharding research)` | optional |
| `inspired by` | `Article` / `Thing` | `(Solana Whitepaper, inspired by, PBFT)` | optional |

### Evidence

| Predicate | Expected Object Type | Example Triple | Priority |
|---|---|---|---|
| `evidenced by` | `Thing` / `URL` | `(Claim, evidenced by, On-chain Proof)` | optional |
| `disputed by` | `Article` / `Person` | `(Claim, disputed by, Counter-paper)` | optional |
| `confirmed by` | `Article` / `Person` / `Organization` | `(Claim, confirmed by, Independent Study)` | optional |

---

## Product

Schema.org type: `Product`
Classification: `intuition/data-structures/classifications/product/`

A physical or digital product, hardware device, or consumer good.

### Identity and Metadata

| Predicate | Expected Object Type | Example Triple | Priority |
|---|---|---|---|
| `is` | `DefinedTerm` | `(Ledger Nano X, is, hardware wallet)` | core |
| `has type` | `DefinedTerm` | `(Ledger Nano X, has type, cold storage)` | core |
| `has tag` | `DefinedTerm` | `(Ledger, has tag, security)` | core |
| `url` | `URL` | `(Ledger Nano X, url, ledger.com)` | common |
| `imgUrl` | `ImageURL` | `(Ledger Nano X, imgUrl, nano-x.png)` | common |
| `has description` | `String` | `(Ledger Nano X, has description, "Bluetooth-enabled hardware wallet")` | common |
| `priced in` | `EthereumERC20` / `DefinedTerm` | `(Ledger Nano X, priced in, USD)` | optional |

### Relationships

| Predicate | Expected Object Type | Example Triple | Priority |
|---|---|---|---|
| `created by` | `Organization` | `(Ledger Nano X, created by, Ledger)` | core |
| `compatible with` | `SoftwareSourceCode` / `DefinedTerm` | `(Ledger Nano X, compatible with, Ethereum)` | common |
| `alternative to` | `Product` | `(Ledger Nano X, alternative to, Trezor Model T)` | common |
| `compete with` | `Product` | `(Ledger, compete with, Trezor)` | common |
| `better than` | `Product` | `(Ledger Nano X, better than, Ledger Nano S)` | optional |
| `supersede` | `Product` | `(Ledger Nano X, supersede, Ledger Nano S)` | optional |
| `reviewed` | — (Person as Subject) | *(Products are typically the Object of `reviewed`, not the Subject)* | — |

---

## Collection

Collections are curated groups of atoms. They may represent watchlists, directories, ranked lists, thematic bundles, or any other list-like grouping.

### Identity and Metadata

| Predicate | Expected Object Type | Example Triple | Priority |
|---|---|---|---|
| `is` | `DefinedTerm` | `(DeFi Blue Chips, is, watchlist)` | core |
| `has type` | `DefinedTerm` | `(DeFi Blue Chips, has type, curated list)` | common |
| `has tag` | `DefinedTerm` | `(Layer 1 Watchlist, has tag, crypto)` | core |
| `has description` | `String` | `(DeFi Blue Chips, has description, "Top DeFi protocols by TVL")` | common |
| `imgUrl` | `ImageURL` | `(DeFi Blue Chips, imgUrl, defi-cover.png)` | optional |

### Curation Relationships

| Predicate | Expected Object Type | Example Triple | Priority |
|---|---|---|---|
| `contain` | `Any` | `(Layer 1 Watchlist, contain, Ethereum)` | core |
| `curated by` | `Person` / `Organization` | `(DeFi Blue Chips, curated by, Alice)` | core |
| `pinned in` | — (Subject is the item) | *(Items are pinned in a collection; the collection is the Object)* | — |
| `featured in` | — (Subject is the item) | *(Items are featured in a collection; the collection is the Object)* | — |

### Comparison

| Predicate | Expected Object Type | Example Triple | Priority |
|---|---|---|---|
| `alternative to` | `Collection` | `(My DeFi List, alternative to, DeFi Blue Chips)` | optional |
| `derived from` | `Collection` | `(My DeFi List, derived from, DeFi Blue Chips)` | optional |
| `forked from` | `Collection` | `(My L1 List, forked from, Layer 1 Watchlist)` | optional |

---

## Location

Schema.org type: `Place`
Classification: `intuition/data-structures/classifications/location/`

A geographic place, city, country, or virtual region.

| Predicate | Expected Object Type | Example Triple | Priority |
|---|---|---|---|
| `is` | `DefinedTerm` | `(Berlin, is, city)` | core |
| `has type` | `DefinedTerm` | `(Crypto Valley, has type, tech hub)` | common |
| `has tag` | `DefinedTerm` | `(Zug, has tag, crypto-friendly)` | common |
| `url` | `URL` | `(Crypto Valley, url, cryptovalley.swiss)` | optional |
| `located in` | `Location` | `(Zug, located in, Switzerland)` | core |
| `contain` | `Organization` / `Event` | `(Crypto Valley, contain, Ethereum Foundation)` | optional |

---

## DefinedTerm (Tag / Concept / Keyword)

Schema.org type: `DefinedTerm`
Classification: `intuition/data-structures/classifications/defined-term/`

A tag, keyword, concept, standard, or categorical label used as an Object in many triples — but sometimes also as a Subject.

| Predicate | Expected Object Type | Example Triple | Priority |
|---|---|---|---|
| `is` | `DefinedTerm` | `(DeFi, is, concept)` | common |
| `subclass of` | `DefinedTerm` | `(DEX, subclass of, exchange)` | common |
| `instance of` | `DefinedTerm` | `(Uniswap, instance of, DEX)` | common |
| `same as` | `DefinedTerm` | `(DeFi, same as, decentralized finance)` | common |
| `alias of` | `DefinedTerm` | `(defi, alias of, DeFi)` | optional |
| `has description` | `String` | `(ZK proofs, has description, "Zero-knowledge proof systems")` | common |
| `url` | `URL` | `(ERC-20, url, eips.ethereum.org/EIPS/eip-20)` | optional |
| `alternative to` | `DefinedTerm` | `(PoS, alternative to, PoW)` | optional |
| `supersede` | `DefinedTerm` | `(ERC-20 permit, supersede, ERC-20 approve)` | optional |
| `deprecated by` | `DefinedTerm` | `(ERC-20 approve, deprecated by, ERC-20 permit)` | optional |

---

## Social Media Account

Schema.org type: `SocialMediaPosting` (account variant)
Classification: `intuition/data-structures/classifications/social-media-account/`

A platform account atom linked to an identity.

| Predicate | Expected Object Type | Example Triple | Priority |
|---|---|---|---|
| `is` | `DefinedTerm` | `(x.com/alice_eth, is, social account)` | common |
| `has type` | `DefinedTerm` | `(x.com/alice_eth, has type, X account)` | common |
| `url` | `URL` | `(x.com/alice_eth, url, x.com/alice_eth)` | core |
| `imgUrl` | `ImageURL` | `(x.com/alice_eth, imgUrl, alice-profile.jpg)` | optional |
| `verified by` | `Organization` | `(x.com/alice_eth, verified by, X)` | optional |

*Note: Social media accounts are most commonly the **Object** of `(Person, linked account, SocialMediaAccount)` triples rather than the Subject.*

---

## Ethereum Smart Contract

Classification: `intuition/data-structures/classifications/ethereum-smart-contract/`

A deployed contract on an EVM chain.

| Predicate | Expected Object Type | Example Triple | Priority |
|---|---|---|---|
| `is` | `DefinedTerm` | `(0xUniRouter, is, router contract)` | core |
| `has type` | `DefinedTerm` | `(0xUniRouter, has type, DEX router)` | core |
| `has tag` | `DefinedTerm` | `(0xUniRouter, has tag, DeFi)` | common |
| `created by` | `Person` / `Organization` | `(0xUniRouter, created by, Uniswap Labs)` | core |
| `implement` | `DefinedTerm` | `(0xUniRouter, implement, ERC-20)` | common |
| `available on` | `DefinedTerm` / `SoftwareSourceCode` | `(0xUniRouter, available on, Ethereum)` | core |
| `audited by` | `Organization` | `(0xUniRouter, audited by, Trail of Bits)` | common |
| `verified by` | `Organization` | `(0xUniRouter, verified by, Etherscan)` | common |
| `depend on` | `SoftwareSourceCode` / `EthereumSmartContract` | `(0xUniRouter, depend on, WETH)` | common |
| `use` | `SoftwareSourceCode` | `(0xUniRouter, use, OpenZeppelin)` | common |
| `supersede` | `EthereumSmartContract` | `(UniRouterV2, supersede, UniRouterV1)` | optional |
| `replaced by` | `EthereumSmartContract` | `(UniRouterV1, replaced by, UniRouterV2)` | optional |

---

## Quick Reference: Universal Predicates

These predicates work with **any** Subject entity type:

| Predicate | Expected Object Type | Notes |
|---|---|---|
| `is` | `DefinedTerm` | Type or identity assertion |
| `has type` | `DefinedTerm` | Formal classification |
| `has tag` | `DefinedTerm` | Free-form tagging |
| `has category` | `DefinedTerm` | Structured taxonomy |
| `url` | `URL` | Canonical web address |
| `imgUrl` | `ImageURL` | Visual representation |
| `has description` | `String` | Textual description |
| `same as` | Same entity type | Cross-representation identity |
| `alias of` | Same entity type | Alternative name |
| `contain` | `Any` | Collection membership (when Subject is a container) |
| `created by` | `Person` / `Organization` | Origin attribution |
| `like` | `Any` | Lightweight positive signal (when Subject is a Person) |

---

## Registry Strategy

The feedback raises three options for where these interpretations live. They are not mutually exclusive.

The market-optimized triple design (depositional vs. attributive) adds a critical dimension to the registry: it must encode not just which predicates apply to which entity types, but **which market pattern to use**. This determines whether frontends route users to a shared depositional market or create a subject-specific triple.

### Option 1: Documentation Only (Current)

Keep the Subject-type-to-Predicate mappings in this file and reference them in SDK docs.

**Pros:** Zero overhead, easy to iterate, no on-chain cost.
**Cons:** Enforcement is social. Developers must find and read the docs. Frontends must hardcode the mappings.

### Option 2: Backend Enforcement

Encode the mappings in the backend (API or indexer) so that triple creation validates Subject-type/Predicate/Object-type constraints.

**Pros:** Prevents invalid triples at ingestion time. Frontends get typed recommendations from an API.
**Cons:** Couples the backend to a specific schema version. Makes the system more opinionated than the protocol intends. Requires migration strategy for new predicates.

### Option 3: On-Chain Registry (Recommended)

Publish the interpretations to Intuition itself as triples:

```
(Person_atom, recommended predicates, follow_atom)
(follow_atom, expected object type, Person_atom)
(follow_atom, expected object type, Organization_atom)
```

Or more compactly, create a registry atom and use `contain`:

```
(Person_predicates_registry, contain, follow_atom)
(Person_predicates_registry, contain, like_atom)
(Person_predicates_registry, contain, trust_atom)
...
```

With object type expectations encoded as:

```
(follow_atom, expected object type, Person_type_atom)
(follow_atom, expected object type, Organization_type_atom)
(located_in_atom, expected object type, Location_type_atom)
```

And market patterns encoded as:

```
(follow_atom, market pattern, depositional_atom)
(created_by_atom, market pattern, attributive_atom)
(member_of_atom, market pattern, hybrid_atom)
```

This lets frontends dynamically determine: "When a user clicks 'follow' on Vitalik's profile, should we route them to the shared `(I, follow, Vitalik)` market or create a new `(Alice, follow, Vitalik)` triple?" The answer comes from the registry: `follow` is `depositional`, so route to the `I`-subject market.

**Pros:**
- The interpretations are themselves Intuition knowledge — fully dog-fooded
- Anyone can query the registry on-chain or via the API
- Third-party builders can extend it by creating their own registry triples
- The registry is versionable — a new registry atom can `supersede` the old one
- Frontends can dynamically fetch recommended predicates per entity type without hardcoding

**Cons:**
- Requires bootstrapping the registry atoms (one-time cost)
- Registry triples have gas costs
- Need to define who curates the canonical registry (Intuition team initially, community governance later)

### Hybrid Approach

Start with Option 1 (this document) as the source of truth. Use it to generate the on-chain registry (Option 3) as a batch operation. Keep the backend (Option 2) as a read-through cache of the on-chain registry for fast frontend lookups.

The flow:
1. This document defines the mappings and market patterns
2. A script creates the registry atoms and triples on-chain (including market pattern markers)
3. The backend indexes the registry triples
4. Frontends query the backend for `GET /predicates?subjectType=Person`
5. The response includes market pattern (`depositional` / `attributive` / `hybrid`) for each predicate
6. The UI routes the user to the correct market:
   - **Depositional:** Find or create the shared `(predicate, object)` triple, then deposit
   - **Attributive:** Create the specific `(subject, predicate, object)` triple
   - **Hybrid:** If the user IS the subject, use depositional; otherwise, use attributive

This way the documentation, the on-chain state, and the API are always derivable from the same source. And the market consolidation logic — the decision about whether to create a new triple or deposit on an existing one — is driven by the registry, not hardcoded in each frontend.

---

## Examples

This section walks through real-world scenarios showing how to model knowledge claims as triples, when to use depositional vs. attributive patterns, and — just as importantly — what NOT to do.

---

### Example 1: Following a Person

**Scenario:** Alice wants to follow Vitalik on Intuition.

**Wrong way (fragments the market):**
```
Triple:   (Alice, follow, Vitalik)
Result:   A new vault is created just for Alice's follow.
          TVL = Alice's deposit only.
          No one else can meaningfully stake on "Alice follows Vitalik."
          If 10,000 people follow Vitalik this way, there are 10,000 tiny vaults.
```

**Right way (consolidates the market):**
```
Triple:   (I, follow, Vitalik)
Action:   Alice deposits on this triple.
Result:   One shared vault for "I follow Vitalik."
          Alice's deposit = her follow signal.
          Bob deposits too = Bob also follows.
          TVL = aggregate follow conviction for Vitalik.
          Share price rises as more people follow = momentum signal.
```

**What we learn from the market:**
- Number of depositors = follower count
- Total TVL = conviction-weighted follower strength
- Deposit sizes = who is a casual follower (small deposit) vs. superfan (large deposit)
- Withdrawals = unfollow, and the market price adjusts downward

---

### Example 2: Bullish vs. Bearish Sentiment

**Scenario:** The community is split on whether Ethereum or Solana will dominate L1s.

**The market pair:**
```
Triple A:   (I, bullish on, Ethereum)      — TVL: 500 ETH from 2,000 depositors
Triple B:   (I, bearish on, Ethereum)      — TVL: 30 ETH from 200 depositors
Triple C:   (I, bullish on, Solana)        — TVL: 120 ETH from 800 depositors
Triple D:   (I, bearish on, Solana)        — TVL: 15 ETH from 150 depositors
```

**What we learn from the market:**
- ETH bull/bear ratio by capital: 500:30 = ~17:1 bullish
- ETH bull/bear ratio by headcount: 2,000:200 = 10:1 bullish
- SOL bull/bear ratio by capital: 120:15 = 8:1 bullish
- The gap between capital ratio and headcount ratio for ETH (17:1 vs 10:1) tells you whales are disproportionately bullish
- Cross-compare: ETH has 4x the bullish TVL of SOL, suggesting stronger overall conviction

**Adding comparison markets:**
```
Triple E:   (Ethereum, better than, Solana)   — TVL: 200 ETH from 1,200 depositors
Triple F:   (Solana, better than, Ethereum)    — TVL: 80 ETH from 600 depositors
```

Now we have a direct head-to-head market. Ratio = 2.5:1 in favor of Ethereum.

---

### Example 3: Building a Trust Graph for Auditors

**Scenario:** The community wants to build a reputation layer for smart contract auditors.

**Depositional trust signals:**
```
(I, trust, Trail of Bits)       — TVL: 300 ETH, 1,500 depositors
(I, trust, CertiK)              — TVL: 80 ETH, 2,000 depositors
(I, trust, OpenZeppelin)         — TVL: 250 ETH, 1,200 depositors
(I, distrust, CertiK)           — TVL: 40 ETH, 300 depositors
```

**What we learn:**
- Trail of Bits: high TVL, high depositor count = widely and deeply trusted
- CertiK: highest depositor count but lowest TVL per depositor = many people trust them casually, but large stakeholders don't. Plus a significant distrust market (40 ETH). Trust/distrust ratio is only 2:1 by capital.
- OpenZeppelin: strong trust, no meaningful distrust market = clean reputation

**Layering attributive facts on top:**
```
(Aave v3, audited by, Trail of Bits)       — TVL: 100 ETH
(Aave v3, audited by, OpenZeppelin)         — TVL: 90 ETH
(Compound v3, audited by, Trail of Bits)    — TVL: 60 ETH
(Uniswap v4, audited by, CertiK)           — TVL: 20 ETH
```

Now you can compose: "Trail of Bits has high personal trust (300 ETH in `trust`) AND high factual validation on specific audits (100 ETH on Aave alone)."

---

### Example 4: Governance Voting

**Scenario:** A DAO proposal is up for vote. Token holders want to express their position.

**The vote market:**
```
(I, voted for, Proposal 42)        — TVL: 150 ETH from 800 depositors
(I, voted against, Proposal 42)    — TVL: 90 ETH from 400 depositors
```

**What we learn:**
- For/against by capital: 150:90 = 5:3 in favor
- For/against by headcount: 800:400 = 2:1 in favor
- The headcount ratio exceeds the capital ratio, meaning smaller holders disproportionately support the proposal

**Delegation:**
```
(I, delegated to, Alice)     — TVL: 500 ETH from 50 depositors
(I, delegated to, Bob)       — TVL: 200 ETH from 200 depositors
```

Alice has fewer delegators but much more capital behind her = whale-backed delegate. Bob has grassroots support = many small delegators.

---

### Example 5: Curating a Collection

**Scenario:** Alice creates a "DeFi Blue Chips" collection and curates it.

**Collection structure (all attributive — these are facts about the collection):**
```
(DeFi Blue Chips, is, curated list)
(DeFi Blue Chips, has tag, DeFi)
(DeFi Blue Chips, has description, "Top DeFi protocols by TVL and track record")
(DeFi Blue Chips, curated by, Alice)
(DeFi Blue Chips, contain, Aave)
(DeFi Blue Chips, contain, Uniswap)
(DeFi Blue Chips, contain, Compound)
(DeFi Blue Chips, contain, MakerDAO)
(DeFi Blue Chips, contain, Curve)
```

**Community engagement (depositional — people stake on the collection):**
```
(I, like, DeFi Blue Chips)          — TVL: 50 ETH from 300 depositors
(I, follow, DeFi Blue Chips)        — TVL: 20 ETH from 150 depositors
(I, recommended, DeFi Blue Chips)    — TVL: 10 ETH from 40 depositors
```

**Ranking items within the collection (depositional — opinion markets):**
```
(Aave, better than, Compound)           — TVL: 15 ETH
(Uniswap, better than, Curve)           — TVL: 8 ETH
(Compound, better than, Curve)           — TVL: 3 ETH
```

Each pairwise comparison is its own market. From the full set of pairwise deposits you can derive a ranked ordering — similar to Elo ratings.

---

### Example 6: Expertise and Credentials

**Scenario:** Alice wants to establish her reputation as a ZK researcher.

**Self-declared (depositional):**
```
(I, expert in, ZK proofs)          — Alice deposits 2 ETH
(I, studied, cryptography)          — Alice deposits 0.5 ETH
(I, speak, Rust)                   — Alice deposits 0.1 ETH
(I, contributed to, Circom)         — Alice deposits 1 ETH
```

Alice's deposit on `(I, expert in, ZK proofs)` is a skin-in-the-game expertise claim. She's not just saying she's an expert — she's staking capital on it. If she's not actually an expert, the market can push back (low TVL, others don't co-deposit, or the `distrust` counter-market grows).

**Community-validated (also depositional — others co-deposit):**
```
(I, expert in, ZK proofs)     — TVL: 30 ETH from 80 depositors (including Alice's 2 ETH)
(I, contributed to, Circom)   — TVL: 10 ETH from 20 depositors
```

80 people co-deposited on `(I, expert in, ZK proofs)` = the community validates Alice's expertise claim (plus many others in the same market). The deposit is "I too am an expert" OR "I affirm this is a real expertise area" depending on context.

**Third-party attestation (attributive):**
```
(Alice, certified by, Ethereum Foundation)    — TVL: 5 ETH from 15 depositors
(Alice, mentor of, Bob)                       — TVL: 1 ETH from 3 depositors
```

These are specific factual claims about Alice. Others deposit to confirm the claim.

---

### Example 7: Protocol Lineage and Versioning

**Scenario:** Documenting the Uniswap version history.

**All attributive — these are factual claims about specific entities:**
```
(Uniswap v1, created by, Hayden Adams)
(Uniswap v2, successor of, Uniswap v1)
(Uniswap v3, successor of, Uniswap v2)
(Uniswap v4, successor of, Uniswap v3)
(Uniswap v4, supersede, Uniswap v3)
(Sushiswap, forked from, Uniswap v2)
(Uniswap v3, audited by, Trail of Bits)
(Uniswap v4, audited by, OpenZeppelin)
(Uniswap, governed by, UNI holders)
(Uniswap, use, Chainlink)
(Uniswap, implement, ERC-20)
(Uniswap, available on, Ethereum)
(Uniswap, available on, Arbitrum)
(Uniswap, available on, Polygon)
```

**Why these are all attributive:** Every triple has a specific subject. "Uniswap v2 is the successor of v1" is a factual claim, not a personal assertion. Deposits mean "I agree this lineage is correct." High TVL on the audit triples means strong community confidence in the audit claims.

---

### Example 8: Debate — "Is Bitcoin Digital Gold?"

**Scenario:** A contested claim that the community wants to market-test.

**The core debate triples (all depositional):**
```
(I, agree with, Bitcoin is digital gold)       — TVL: 400 ETH from 3,000 depositors
(I, disagree with, Bitcoin is digital gold)    — TVL: 150 ETH from 1,200 depositors
```

**Supporting argument triples (depositional — people stake on arguments):**
```
(I, agree with, Bitcoin is a store of value)          — TVL: 350 ETH
(I, agree with, Bitcoin is too volatile for gold)     — TVL: 100 ETH
(I, agree with, Bitcoin replaces gold for millennials) — TVL: 80 ETH
```

**Factual triples that inform the debate (attributive):**
```
(Bitcoin, is, cryptocurrency)
(Bitcoin, created by, Satoshi Nakamoto)
(Bitcoin, has tag, store of value)
(Bitcoin, has tag, digital gold)
(Bitcoin, implement, PoW consensus)
```

**What we learn:** The debate has a 2.7:1 ratio by capital in favor of "digital gold." The supporting argument market shows that "store of value" (350 ETH) is a much stronger conviction than the specific "digital gold" framing (400 ETH). The "too volatile" counter-argument at 100 ETH shows the main bears' reasoning.

---

### Example 9: Event Knowledge Graph

**Scenario:** Building the knowledge graph around Devcon 7.

**Attributive facts about the event:**
```
(Devcon 7, is, conference)
(Devcon 7, has type, developer conference)
(Devcon 7, located in, Bangkok)
(Devcon 7, created by, Ethereum Foundation)
(Devcon 7, sponsored by, Ethereum Foundation)
(Devcon 7, has tag, Ethereum)
(Devcon 7, predecessor of, Devcon 8)
(Devcon 7, successor of, Devcon 6)
(Devcon 7, url, devcon.org)
```

**Depositional engagement signals:**
```
(I, like, Devcon 7)             — TVL: 25 ETH from 500 depositors
(I, recommended, Devcon 7)       — TVL: 8 ETH from 100 depositors
```

**Items featured at the event (attributive):**
```
(Uniswap v4, featured in, Devcon 7)
(EIP-4844, featured in, Devcon 7)
(Verkle Trees, featured in, Devcon 7)
```

---

### Example 10: Token Economic Relationships

**Scenario:** Documenting the USDC ecosystem.

**Attributive facts (all specific to USDC):**
```
(USDC, is, stablecoin)
(USDC, instance of, stablecoin)
(USDC, has type, fiat-backed stablecoin)
(USDC, created by, Circle)
(USDC, implement, ERC-20)
(USDC, pegged to, USD)
(USDC, backed by, US Treasury Bills)
(USDC, regulated by, SEC)
(USDC, compliant with, MiCA)
(USDC, available on, Ethereum)
(USDC, available on, Solana)
(USDC, available on, Arbitrum)
(USDC, listed on, Coinbase)
(USDC, listed on, Binance)
```

**Depositional trust and opinion:**
```
(I, trust, Circle)                  — TVL: 200 ETH from 1,000 depositors
(I, trust, USDC)                    — TVL: 300 ETH from 2,500 depositors
(USDC, better than, USDT)         — TVL: 80 ETH from 500 depositors
(USDT, better than, USDC)         — TVL: 40 ETH from 300 depositors
(USDC, equivalent to, USDT)       — TVL: 60 ETH from 400 depositors
```

**What we learn:** USDC trust (300 ETH) exceeds Circle trust (200 ETH) — people trust the product more than the company. In the head-to-head, USDC wins 2:1 by capital over USDT. But the `equivalent to` market at 60 ETH shows a meaningful segment that considers them interchangeable.

---

### Example 11: Cross-Entity Relationship Web

**Scenario:** Mapping the full relationship network around a single person — Vitalik Buterin.

**Attributive facts about Vitalik:**
```
(Vitalik Buterin, is, researcher)
(Vitalik Buterin, has type, public figure)
(Vitalik Buterin, founded, Ethereum)
(Vitalik Buterin, founded, Bitcoin Magazine)
(Vitalik Buterin, authored by, Ethereum Whitepaper)        ← wrong direction!
(Ethereum Whitepaper, authored by, Vitalik Buterin)         ← correct
(Vitalik Buterin, linked account, x.com/VitalikButerin)
(Vitalik Buterin, located in, Singapore)
(Vitalik Buterin, member of, Ethereum Foundation)
```

**Depositional engagement with Vitalik:**
```
(I, follow, Vitalik Buterin)        — TVL: 1,000 ETH from 15,000 depositors
(I, trust, Vitalik Buterin)          — TVL: 800 ETH from 10,000 depositors
(I, endorse, Vitalik Buterin)        — TVL: 200 ETH from 2,000 depositors
(I, like, Vitalik Buterin)           — TVL: 500 ETH from 20,000 depositors
```

**Vitalik's opinions (attributive claims about what HE thinks):**
```
(Vitalik Buterin, endorse, EIP-4844)          — TVL: 50 ETH (people confirming he endorse it)
(Vitalik Buterin, support, account abstraction) — TVL: 30 ETH
(Vitalik Buterin, agree with, proof of stake)   — TVL: 100 ETH
```

Note the distinction: `(I, endorse, EIP-4844)` is a depositional market where anyone stakes their own endorsement. `(Vitalik Buterin, endorse, EIP-4844)` is an attributive claim that Vitalik specifically endorse it — deposits confirm the factual claim.

---

### Example 12: Anti-Patterns — What NOT to Do

These examples show common mistakes and how to fix them.

#### Anti-pattern: Fragmenting social signals

```
BAD:   (Alice, follow, Vitalik)
BAD:   (Bob, follow, Vitalik)
BAD:   (Carol, follow, Vitalik)

GOOD:  (I, follow, Vitalik)   ← Alice, Bob, and Carol all deposit here
```

Three separate vaults with one depositor each vs. one vault with three depositors. The difference compounds — at scale, the bad pattern creates thousands of dust vaults with zero signal.

#### Anti-pattern: Encoding UI state in predicates

```
BAD:   (Alice, favorited, Uniswap)
BAD:   (Alice, bookmarked, Uniswap)
BAD:   (Alice, starred, Uniswap)

GOOD:  (I, like, Uniswap)    ← Alice deposits. Her app can render it as a favorite, bookmark, or star.
```

"Favorited," "bookmarked," and "starred" are UI concepts, not semantic relationships. They all mean `like`. Creating separate predicates fragments the signal across three vaults when it should be one.

#### Anti-pattern: Duplicating direction

```
BAD:   (Vitalik, is followed by, Alice)    ← "is followed by" is redundant
BAD:   (Uniswap, is liked by, Alice)        ← inverts direction unnecessarily

GOOD:  (I, follow, Vitalik)    ← Alice deposits
GOOD:  (I, like, Uniswap)      ← Alice deposits
```

The triple already has direction via subject/object ordering. Adding "is followed by" or "is liked by" as separate predicates doubles the vocabulary with no new information and splits the market.

#### Anti-pattern: Over-specifying when general predicates exist

```
BAD:   (Aave, provides lending on, Ethereum)      ← too specific, one-off predicate
BAD:   (Aave, is a DeFi lending protocol on, Ethereum)  ← sentence in a predicate

GOOD:  (Aave, is, lending protocol)
       (Aave, has tag, DeFi)
       (Aave, available on, Ethereum)
```

Compose multiple simple triples instead of cramming meaning into a single over-specific predicate. Each simple triple creates its own stakeable market.

#### Anti-pattern: Using attributive when depositional works

```
BAD:   (Alice, bullish on, Ethereum)     ← only Alice stakes, tiny market
BAD:   (Bob, bullish on, Ethereum)       ← another tiny market

GOOD:  (I, bullish on, Ethereum)            ← Alice and Bob both deposit, one deep market
```

Sentiment is inherently first-person. There's no value in knowing that "Alice specifically is bullish" as a separate market — Alice's deposit in the shared market already records that.

#### Anti-pattern: Redundant triples that can be computed

```
BAD:   (Ethereum, has 15000 followers, true)    ← encoding a count as a triple
BAD:   (Vitalik, follower count, 15000)          ← same mistake

GOOD:  (I, follow, Vitalik)   ← the follower count IS the depositor count on this vault
```

Don't create triples for data that is derivable from vault state. The number of depositors on `(I, follow, Vitalik)` IS the follower count. Creating a separate triple for the count just creates stale data that immediately diverges from reality.

#### Anti-pattern: Missing the paired market

```
INCOMPLETE:  (I, bullish on, Ethereum)    ← exists, but no counter-market

COMPLETE:    (I, bullish on, Ethereum)     — 500 ETH TVL
             (I, bearish on, Ethereum)     — 30 ETH TVL
```

For opinion and sentiment predicates, always create the pair. The ratio between paired markets is the signal. A lone `(I, bullish on, Ethereum)` with 500 ETH TVL is less informative than knowing the bear case has only 30 ETH. The ratio (17:1) tells you more than either number alone.

#### Anti-pattern: Wrong triple direction

```
BAD:   (Vitalik Buterin, authored by, Ethereum Whitepaper)   ← backwards
GOOD:  (Ethereum Whitepaper, authored by, Vitalik Buterin)   ← the work was authored by the person

BAD:   (Alice, curated by, DeFi Blue Chips)    ← backwards
GOOD:  (DeFi Blue Chips, curated by, Alice)     ← the collection was curated by the person

BAD:   (Ethereum, created by, Vitalik)     ← actually this is correct
BAD:   (Vitalik, created, Ethereum)        ← "created" is not the canonical predicate
GOOD:  (Ethereum, created by, Vitalik)     ← matches the canonical "created by" predicate
```

Always check the canonical direction documented in the predicate catalog. The subject and object positions are not interchangeable — `authored by` means the Subject was authored by the Object, not the other way around.

---

### Example 13: Real-World Scenario — Security Incident Response

**Scenario:** A bridge exploit happens. The community needs to rapidly assess the situation.

**Phase 1 — Immediate reports (depositional):**
```
(I, reported, Wormhole Bridge)       — TVL spikes to 50 ETH in 1 hour from 200 depositors
(I, distrust, Wormhole Bridge)      — TVL jumps to 30 ETH from 150 depositors
```

**Phase 2 — Factual claims emerge (attributive):**
```
(Wormhole Exploit, is, bridge hack)
(Wormhole Exploit, has tag, security incident)
(Wormhole Bridge, audited by, Neodyme)                    — TVL: 5 ETH (people confirm the audit happened)
(Wormhole Exploit, evidenced by, etherscan.io/tx/0x...)   — TVL: 20 ETH (people confirm the evidence)
```

**Phase 3 — Community assessment (depositional):**
```
(I, trust, Wormhole Bridge)         — TVL drops from 100 ETH to 20 ETH as people withdraw
(I, agree with, Wormhole is safe to use again)    — TVL: 5 ETH from 30 depositors
(I, disagree with, Wormhole is safe to use again) — TVL: 40 ETH from 300 depositors
```

**What the market tells us in real time:**
- Rapid spike in `(I, reported, Wormhole Bridge)` = early warning system
- `(I, trust, Wormhole Bridge)` TVL declining = market confidence eroding
- The 8:1 ratio against "safe to use again" = strong community consensus it's still risky
- The audit claim `(Wormhole Bridge, audited by, Neodyme)` still has TVL, confirming the audit happened — the exploit doesn't erase the audit fact, but the trust market reflects the reality that audits don't guarantee safety

---

### Example 14: Comparing Approaches — A New Developer Choosing a Framework

**Scenario:** A developer wants to know whether to learn Hardhat or Foundry.

**Available market data:**
```
(I, like, Hardhat)                 — TVL: 40 ETH from 800 depositors
(I, like, Foundry)                 — TVL: 80 ETH from 1,200 depositors
(Hardhat, better than, Foundry)  — TVL: 15 ETH from 200 depositors
(Foundry, better than, Hardhat)  — TVL: 50 ETH from 600 depositors
(I, recommended, Foundry)           — TVL: 30 ETH from 300 depositors
(I, recommended, Hardhat)           — TVL: 10 ETH from 100 depositors
```

**Attributive context:**
```
(Hardhat, is, development framework)
(Foundry, is, development framework)
(Hardhat, created by, NomicLabs)
(Foundry, created by, Paradigm)
(Hardhat, use, JavaScript)
(Foundry, use, Solidity)
(Foundry, successor of, DappTools)
```

**What the developer learns:** Foundry wins on every depositional signal — more likes (2:1), stronger head-to-head (3.3:1), and more recommendations (3:1). The attributive triples tell you Foundry uses Solidity for tests (relevant if you prefer staying in one language) and it's the successor to DappTools (shows lineage and maturity).

---

### Example 15: Modeling Relationships That Don't Fit the Pattern

Not everything maps cleanly to depositional or attributive. These edge cases require judgment.

#### Edge Case: Private or Sensitive Relationships

```
TRICKY:  (I, employed by, CompanyX)    ← depositional, but do you want your employer on-chain?
```

Employment, salary, and private affiliations are valid knowledge but may not belong in a public on-chain graph. The protocol doesn't restrict this, but builders should consider whether to recommend these predicates in public-facing UIs. A future privacy layer (ZK attestations) could enable private depositional claims.

#### Edge Case: Temporal Facts That Change

```
TRICKY:  (USDC, pegged to, USD)    ← true today, but what about a depeg event?
```

If USDC depegs, do people withdraw from this triple? The market self-corrects — withdrawals signal loss of confidence in the peg. But the triple itself doesn't have a timestamp or expiry. The vault TVL becomes the confidence score: high TVL = market believes the peg holds, declining TVL = market doubts it.

#### Edge Case: Triples About Triples

```
META:  (I, like, (Ethereum, created by, Vitalik))   ← liking a triple, not an atom
```

Can you deposit on a triple that reference another triple? In Intuition, triples have their own term IDs, so they can appear as objects. This enables meta-claims: "I like the fact that Ethereum was created by Vitalik." The vault for this meta-triple would measure sentiment about the claim itself.

#### Edge Case: Negation

```
TRICKY:  How do you express "Ethereum is NOT a security"?
```

There is no `is not` predicate. Two approaches:
1. Use the positive claim and let low/no TVL speak: `(Ethereum, is, security)` — if no one deposits, the market has spoken.
2. Use the counter-market: `(I, agree with, Ethereum is not a security)` and `(I, disagree with, Ethereum is not a security)`.

Option 2 is better because it creates an explicit market. Absence of deposits is ambiguous (maybe no one noticed the triple), but an active market with a strong ratio is unambiguous signal.

#### Edge Case: Quantitative Claims

```
TRICKY:  How do you express "Aave has $10B TVL"?
```

Exact numbers don't belong in triples — they change constantly. Instead:
```
(Aave, has tag, high TVL)
(Aave, ranked above, Compound)    ← relative ranking is more durable than absolute numbers
```

Or for specific milestone claims:
```
(I, agree with, Aave has over 10B TVL)    ← depositional market on the claim
```

The market validates the claim. If Aave's TVL drops below $10B, depositors withdraw and the market self-corrects.

#### Edge Case: Predictions and Future States

```
TRICKY:  "ETH will hit $10,000 by 2027"
```

This is a prediction, not a current-state claim. Model it as:
```
(I, agree with, ETH will hit 10000 by 2027)     — TVL: 100 ETH
(I, disagree with, ETH will hit 10000 by 2027)  — TVL: 60 ETH
```

This is essentially a prediction market. The ratio (5:3 in favor) is the market's probability estimate. As the deadline approaches, deposits will shift to reflect updated beliefs.

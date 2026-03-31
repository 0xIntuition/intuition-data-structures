# Predicate Testing Matrix

This document defines the test plan for verifying enshrined predicates before production launch. Every test must pass before the predicate system goes live.

---

## 1. Atom ID Determinism

**Goal:** Verify that the same predicate string produces the same atom ID across all environments.

### Tests

| Test | Input | Expected | Pass Criteria |
|---|---|---|---|
| SDK hash matches contract hash | `"follow"` | Same atom ID from SDK `calculateAtomId()` and on-chain `keccak256(ATOM_SALT, keccak256(toHex("follow")))` | Exact match |
| Two SDK instances agree | `"follow"` computed in SDK instance A and B | Same atom ID | Exact match |
| Case sensitivity | `"follow"` vs `"Follow"` vs `"FOLLOW"` | Three different atom IDs | All three are distinct |
| Whitespace sensitivity | `"follow"` vs `"follow "` vs `" follow"` | Three different atom IDs | All three are distinct |
| Unicode normalization | `"café"` (NFC) vs `"café"` (NFD) | Depends on normalization policy | Document which form is canonical; SDK enforces it |

### Run for Every Enshrined Predicate

For each predicate in `enshrined-predicates-launch-set.md`:

```
[ ] SDK: calculateAtomId("follow")     = 0x____
[ ] Contract: computeAtomId("follow")  = 0x____
[ ] On-chain atom (after creation)     = 0x____
All three match: [ ] YES
```

---

## 2. `I` Atom Verification

**Goal:** Verify the `I` atom exists on-chain, matches the SDK, and functions as expected for depositional triples.

### Tests

| Test | Expected | Pass Criteria |
|---|---|---|
| `I` atom exists on-chain | Atom with data `"I"` is created | Atom ID matches SDK computation |
| SDK `I_SUBJECT` constant matches | `I_SUBJECT` value is `"I"` | String match |
| SDK `I_SUBJECT_ID` matches on-chain | `calculateAtomId("I")` equals on-chain atom ID | Exact match |
| Two users deposit on `(I, follow, X)` | Both deposits land in the same vault | Same vault ID, depositor count = 2 |
| Depositor identity resolution | After deposit, depositor address appears in vault ledger | Address matches transaction sender |

---

## 3. Triple Creation with Enshrined Predicates

**Goal:** Verify that triples using enshrined predicates are created correctly and indexed.

### Depositional Triple Tests

| Test | Triple | Expected |
|---|---|---|
| Create depositional triple | `(I, follow, Vitalik_atom)` | Triple created; vault created; deposit recorded |
| Second deposit on same triple | User B deposits on `(I, follow, Vitalik_atom)` | Same vault; depositor count increments; TVL increases |
| Withdrawal from depositional triple | User A withdraws from `(I, follow, Vitalik_atom)` | TVL decreases; depositor count decrements if full withdrawal |
| All 13 depositional launch predicates | Create a triple with each | All succeed; all produce distinct triple IDs |

### Attributive Triple Tests

| Test | Triple | Expected |
|---|---|---|
| Create attributive triple | `(Ethereum_atom, created by, Vitalik_atom)` | Triple created; vault created |
| Deposit on attributive triple | User deposits on above | Deposit recorded; TVL reflects deposit |
| All 10 attributive launch predicates | Create a triple with each | All succeed; all produce distinct triple IDs |

### Comparative Triple Tests

| Test | Triple | Expected |
|---|---|---|
| Create comparative triple | `(Rust_atom, better than, Solidity_atom)` | Triple created |
| Create inverse | `(Solidity_atom, better than, Rust_atom)` | Separate triple and vault |
| Deposits on both | Deposit on each | Two separate vaults with independent TVL |

---

## 4. Predicate Uniqueness

**Goal:** Verify that no two enshrined predicates produce the same atom ID, and that old-form and new-form predicates produce distinct IDs.

### Tests

| Test | Expected |
|---|---|
| All 25 launch predicates have unique atom IDs | No duplicates in the set |
| `"follow"` ≠ `"follows"` | Different atom IDs |
| `"contain"` ≠ `"contains"` | Different atom IDs |
| `"like"` ≠ `"likes"` | Different atom IDs |
| `"trust"` ≠ `"trusts"` | Different atom IDs |
| `"use"` ≠ `"uses"` | Different atom IDs |
| `"endorse"` ≠ `"endorses"` | Different atom IDs |
| All 22 migrated predicates: old ≠ new | All pairs produce different atom IDs |

---

## 5. Indexer Recognition

**Goal:** Verify the indexer correctly identifies and categorizes triples using enshrined predicates.

### Tests

| Test | Expected |
|---|---|
| Triple with enshrined predicate is indexed | Appears in API query results |
| Triple with non-enshrined predicate is indexed | Appears in API but without enriched metadata |
| Predicate metadata in API response | Includes: label, market pattern, deprecated flag |
| Deprecated predicate triple is indexed | Indexed with `deprecated: true` and `replacedBy` field |
| Query by predicate | `GET /triples?predicate=follow` returns only triples with the `follow` predicate atom |
| Query by subject type + predicate | `GET /predicates?subjectType=Person` returns correct predicate list |

---

## 6. Display and Conjugation

**Goal:** Verify the SDK rendering function produces correct output for all predicates across contexts.

### Tests

For each conjugating predicate:

| Predicate | `first-person` | `singular` | `plural` |
|---|---|---|---|
| `follow` | `"follow"` | `"follows"` | `"follow"` |
| `like` | `"like"` | `"likes"` | `"like"` |
| `trust` | `"trust"` | `"trusts"` | `"trust"` |
| `distrust` | `"distrust"` | `"distrusts"` | `"distrust"` |
| `endorse` | `"endorse"` | `"endorses"` | `"endorse"` |
| `recommend` | `"recommend"` | `"recommends"` | `"recommend"` |
| `vouch for` | `"vouch for"` | `"vouches for"` | `"vouch for"` |
| `agree with` | `"agree with"` | `"agrees with"` | `"agree with"` |
| `disagree with` | `"disagree with"` | `"disagrees with"` | `"disagree with"` |
| `contain` | `"contain"` | `"contains"` | `"contain"` |

For each non-conjugating predicate:

| Predicate | `first-person` | `singular` | `plural` |
|---|---|---|---|
| `is` | `"is"` | `"is"` | `"is"` |
| `has tag` | `"has tag"` | `"has tag"` | `"has tag"` |
| `has type` | `"has type"` | `"has type"` | `"has type"` |
| `has description` | `"has description"` | `"has description"` | `"has description"` |
| `created by` | `"created by"` | `"created by"` | `"created by"` |
| `same as` | `"same as"` | `"same as"` | `"same as"` |
| `better than` | `"better than"` | `"better than"` | `"better than"` |
| `alternative to` | `"alternative to"` | `"alternative to"` | `"alternative to"` |
| `bullish on` | `"bullish on"` | `"bullish on"` | `"bullish on"` |
| `bearish on` | `"bearish on"` | `"bearish on"` | `"bearish on"` |

### Edge Cases

| Test | Input | Expected |
|---|---|---|
| Unknown predicate (not in registry) | `renderPredicate("custom thing", ...)` | Returns `"custom thing"` unchanged |
| Empty string | `renderPredicate("", ...)` | Returns `""` |
| `displayName` form | `renderPredicate("follow", { form: "displayName" })` | Returns `"Follow"` |

---

## 7. Migration Verification

**Goal:** Verify that old-form and new-form predicates coexist correctly during the migration period.

### Tests

| Test | Expected |
|---|---|
| Old predicate atom and new predicate atom both exist on-chain | Two distinct atoms |
| Deprecation triple exists | `(follows_atom, deprecated by, follow_atom)` is on-chain |
| Triples on old predicate still indexed | `(I, follows, Vitalik)` appears in query results |
| Triples on old predicate marked deprecated | API response includes `deprecated: true` |
| New triples use new predicate only | SDK constants resolve to base form |
| SDK deprecated constants emit warning | Using `FOLLOWS_PREDICATE` logs a deprecation warning |

---

## 8. Registry On-Chain

**Goal:** Verify the predicate registry triples are correctly created and queryable.

### Tests

| Test | Expected |
|---|---|
| Registry atom exists | `predicate_registry` atom created |
| All launch predicates in registry | `(predicate_registry, contain, X)` exists for each |
| Market pattern triples exist | `(follow_atom, has type, depositional_predicate)` |
| Registry queryable via API | `GET /registry/predicates` returns all enshrined predicates |

---

## 9. Gas Cost Baseline

**Goal:** Establish gas cost benchmarks for predicate-related operations.

### Measurements

| Operation | Gas Used | Notes |
|---|---|---|
| Create a predicate atom (plain string) | _____ | Baseline for string-based predicates |
| Create the `I` atom | _____ | One-time cost |
| Create a triple with enshrined predicate | _____ | Standard operation |
| Deposit on existing triple | _____ | Most common user operation |
| Withdraw from triple | _____ | |
| Create deprecation triple | _____ | Migration cost |
| Batch create N registry triples | _____ | Bootstrap cost |

---

## 10. End-to-End Scenarios

**Goal:** Verify complete user flows work correctly.

### Scenario A: New User Follows Someone

1. User connects wallet
2. Frontend presents "Follow" button on Vitalik's profile
3. Frontend finds or creates `(I, follow, Vitalik)` triple
4. User deposits 0.01 ETH
5. Verify: deposit lands in correct vault
6. Verify: depositor count increments
7. Verify: frontend displays "Following" state
8. Verify: Vitalik's profile shows updated follower count

### Scenario B: User Creates a Stack

1. User creates a new Stack atom
2. Frontend creates `(Stack, is, curated list)` and `(Stack, curated by, User)` triples
3. User adds Aave to Stack
4. Frontend creates `(Stack, contain, Aave)` triple
5. Verify: all triples exist and are indexed
6. Verify: Stack page displays items correctly

### Scenario C: User Expresses Sentiment

1. User clicks "Bullish" on Ethereum
2. Frontend finds or creates `(I, bullish on, Ethereum)` triple
3. User deposits 0.1 ETH
4. Verify: deposit in correct vault
5. User navigates to bearish counter-market
6. Frontend shows ratio: bullish TVL / bearish TVL

### Scenario D: Head-to-Head Comparison

1. Frontend shows Rust vs Solidity comparison
2. User clicks "Rust is better"
3. Frontend finds or creates `(Rust, better than, Solidity)` triple
4. User deposits
5. Verify: deposit in correct vault (not the inverse)
6. Frontend shows ratio of `(Rust, better than, Solidity)` to `(Solidity, better than, Rust)`

---

## Sign-Off

All tests must pass before production launch. Each section should be signed off by:

| Section | Owner | Status | Date |
|---|---|---|---|
| 1. Atom ID Determinism | | Pending | |
| 2. I Atom Verification | | Pending | |
| 3. Triple Creation | | Pending | |
| 4. Predicate Uniqueness | | Pending | |
| 5. Indexer Recognition | | Pending | |
| 6. Display and Conjugation | | Pending | |
| 7. Migration Verification | | Pending | |
| 8. Registry On-Chain | | Pending | |
| 9. Gas Cost Baseline | | Pending | |
| 10. End-to-End Scenarios | | Pending | |

# Changelog: `fullstack-dapp/tutorial.md` → `midnight-attestation-dapp/tutorial.md`

This document records every significant change made when the `fullstack-dapp` tutorial was extracted from the `midnight-apps` monorepo into the standalone `midnight-attestation-dapp` repository and rewritten for publication.

---

## Meta

| | Before | After |
|---|---|---|
| **File location** | `midnight-apps/fullstack-dapp/tutorial.md` | `midnight-attestation-dapp/tutorial.md` |
| **Repository** | Subfolder of `0xfdbu/midnight-apps` | Standalone `0xfdbu/midnight-attestation-dapp` |
| **Word count** | ~2,800 words | ~4,400 words |
| **Primary audience** | Developers | Developers |

---

## 1. Repository and structural changes

### Extracted into a standalone repository
- All internal links changed from `github.com/0xfdbu/midnight-apps/tree/main/fullstack-dapp/...` to `github.com/0xfdbu/midnight-attestation-dapp/...`.
- Clone instructions now point to the standalone repo:
  ```bash
  git clone https://github.com/0xfdbu/midnight-attestation-dapp.git
  cd midnight-attestation-dapp
  npm install
  ```
- Source-code links for `WalletSelectModal.tsx`, `useWallet.ts`, `witnesses.ts`, `Deploy.tsx`, `Home.tsx`, `Prove.tsx`, and `package.json` updated to the new repo path.

### Added page title
- Added an H1 title that describes the concrete outcome:
  ```markdown
  # Build a full-stack private age-verification DApp on Midnight with ZK attestations
  ```

---

## 2. Onboarding and setup framing

### Added `## What you'll build`
New section that tells the reader the end result before any code appears:
- A Compact smart contract with Merkle-tree commitments and nullifiers.
- A React frontend with deploy / commitment / attest / prove flows.
- An optional Express + PostgreSQL analytics server.

### Added `## Project setup`
New section that makes the repo link actionable:
- Clone command.
- Final project structure tree.
- Run commands (`npm run dev` for frontend, `cd node-analytics && npm install && npm run dev` for the analytics server).

This addresses the feedback that the original repo link at the top had no instruction to use it.

---

## 3. Prerequisites and dependencies

### Rewrote `## Prerequisites`
**Before:** a long list ending with 25+ package names.

**After:** a short, actionable list:
- Node.js installed (v20+)
- The Compact compiler installed (`compact --version`)
- A Midnight wallet extension (e.g., 1AM or Lace)
- Some Preprod faucet NIGHT tokens
- PostgreSQL if you want to run the analytics server

### Added `## Dependencies`
New section that moved the package dump out of prerequisites:
- Curated list of 5 key Midnight.js packages with one-line purposes.
- Links to `package.json` for the full list.
- Pins `@midnight-ntwrk/dapp-connector-api` at `^4.0.1`.

---

## 4. Context before the contract

### Added `## How the app works`
New section before the first circuit that gives the mental model:
- **Three roles:** Authority, User, Verifier.
- **`### What is public vs private` table** showing what hits the chain and what stays in the wallet.
- **Important note** explaining that commitment values are visible as public arguments during attestation, but proof transactions hide which commitment was proven.
- **`### End-to-end flow`** numbered list (1–5) from secret-key derivation to nullifier recording.
- End-to-end flow diagram retained as a visual reference.

This pulls scattered context up front so the contract code lands with purpose.

---

## 5. Smart contract corrections

### Added `pragma language_version` and compiler pin
**Before:** the printed contract snippet omitted the pragma entirely.

**After:**
```typescript
pragma language_version 0.22;
import CompactStandardLibrary;
```
Added prose: "The contract targets Compact language version `0.22` and was compiled with the Compact compiler `0.30.0`."

### Added the `publicKey()` helper circuit
**Before:** `publicKey(sk)` was called in the constructor and `attestAge()` but never defined. A reader copying the article would hit an undefined-identifier error.

**After:** added the definition before the constructor:
```typescript
circuit publicKey(sk: Bytes<32>): Bytes<32> {
    return persistentHash<Vector<2, Bytes<32>>>(
        [pad(32, "mydapp:pk:v1"), sk]
    );
}
```

### Softened the multi-domain claim
**Before:**
> The example uses `domain` because the smart contract is set to handle multiple types of attestations (age, residency, certifications). Refer to the GitHub repo for more information.

**After:**
> The `domain` parameter lets the same smart contract pattern extend to other attestation types — residency, certification, and so on. The full repo includes those circuits; this tutorial focuses on `age` to keep the walkthrough concise.

This addresses the feedback that only `proveAge()` was shown in detail.

---

## 6. Wallet and provider section improvements

### Pinned the DApp connector API version
**Before:** "For this you need DApp connector API v4 installed."

**After:** "The project uses `@midnight-ntwrk/dapp-connector-api` at `^4.0.1`, which exposes the v4 DApp connector API."

### Added `COMPATIBLE_CONNECTOR_API_VERSION` note
New note after the wallet-filter snippet:
> `COMPATIBLE_CONNECTOR_API_VERSION` is `'4.x'`, matching the installed `@midnight-ntwrk/dapp-connector-api` at `^4.0.1`.

### Active-voice fix
**Before:** "`localSecretKey()` will be used to fetch the user's secret key..."

**After:** "`localSecretKey()` fetches the user's secret key..."

---

## 7. Type-safety note

### Added `## 3. Deploy` cast note
New note explaining `as any` / `as never` casts in deploy/attest/prove code:
> These work around SDK type-resolution friction at the `compact-js` / `midnight-js` boundary, but the generated contract types and the provider types do not always align at compile time. They are safe at runtime, but they are not best practice for production code. If you are building your own DApp, prefer proper type narrowing or a thin wrapper over casting.

---

## 8. Backend section accuracy

### Fixed `GET /contract` description
**Before:** "The endpoint joins the `contracts` and `contract_states` tables..."

**After:** "The endpoint first queries the `contracts` table, then fetches the most recent row from `contract_states` ordered by `recorded_at`."

This matches the actual server code, which performs two sequential SELECTs, not a SQL JOIN.

### Marked `TRACKED_CONTRACT` as a placeholder
**Before:** "`TRACKED_CONTRACT` is a hardcoded `331460e632...` smart contract value."

**After:** "`TRACKED_CONTRACT` is a hardcoded placeholder (`331460e632...`) — replace it with your own deployed contract address before running the server."

---

## 9. Style and grammar improvements

### Heading grammar
- `### What stays public vs private` → `### What is public vs private`

### `e.g.` formatting
- `eg: `age`, `residency`` → `e.g., `age` or `residency``
- `public counters eg: `totalAgeProofs`` → `public counters, e.g., `totalAgeProofs``

### "contract" → "smart contract" in prose
- "the contract code below" → "the smart contract code below"
- "the same contract pattern" → "the same smart contract pattern"

### Sentence punctuation
- Fixed run-on sentence after the cast note.
- Added comma after "However" in the domain-separator warning.

### Link text
- "Check the full repository [source code]" → "[View the full source code in the midnight-attestation-dapp repository]"

---

## 10. Remaining known differences

These are intentional and not errors:

| Difference | Reason |
|---|---|
| `TRACKED_CONTRACT` value in tutorial is a concrete hash; actual source uses `''` | The tutorial uses a concrete example to make the placeholder visible; the source uses an empty string to force replacement. |
| Analytics server source has extra routes (`GET /status`, `POST /track/:address`, `DELETE /contract/:address`) not shown in tutorial | Tutorial focuses on the core polling and `/contract` flow for brevity. |
| `initDb()` in the server drops and recreates tables | Tutorial omits this destructive behavior for brevity. |

---

## Summary of feedback addressed

All items from the editorial review have been addressed:

1. ✅ Title rewritten to be concrete and outcome-focused.
2. ✅ Setup/onboarding section added before the contract walkthrough.
3. ✅ Project extracted into a standalone repository.
4. ✅ Prerequisites reformatted; dependencies moved to their own section.
5. ✅ Context (roles, public/private, end-to-end flow) added before the contract.
6. ✅ `publicKey()` helper circuit added to the article.
7. ✅ Pragma and compiler version added.
8. ✅ Multi-domain claim softened.
9. ✅ `as any` / `as never` casts acknowledged.
10. ✅ Connector API version pinned and vocabulary aligned.
11. ✅ `TRACKED_CONTRACT` flagged as a placeholder.
12. ✅ Backend `/contract` description corrected.
13. ✅ Public/private table refined for accuracy.

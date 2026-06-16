# Changelog: `fullstack-dapp/tutorial.md` → `midnight-attestation-dapp/tutorial.md`

Changes made when extracting the `fullstack-dapp` tutorial from the `midnight-apps` monorepo into the standalone `midnight-attestation-dapp` repository and rewriting it for publication.

---

## Meta

| | Before | After |
|---|---|---|
| Repository | Subfolder of `0xfdbu/midnight-apps` | Standalone `0xfdbu/midnight-attestation-dapp` |
| File | `fullstack-dapp/tutorial.md` | `midnight-attestation-dapp/tutorial.md` |
| Word count | ~3,723 words | ~4,438 words |

---

## 1. Repository extraction

- Changed all GitHub links from `github.com/0xfdbu/midnight-apps/tree/main/fullstack-dapp/...` to `github.com/0xfdbu/midnight-attestation-dapp/...`.
- Updated clone instructions:
  ```bash
  git clone https://github.com/0xfdbu/midnight-attestation-dapp.git
  cd midnight-attestation-dapp
  npm install
  ```

---

## 2. Title and onboarding

- Added H1 title:
  ```markdown
  # Build a full-stack private age-verification DApp on Midnight with ZK attestations
  ```
- Added `## What you'll build` section describing the end result.
- Added `## Project setup` section with clone command, project structure tree, and run instructions (`npm run dev`, analytics server).

---

## 3. Prerequisites and dependencies

- Rewrote `## Prerequisites` as a short bulleted list:
  - Node.js v20+
  - Compact compiler
  - Midnight wallet
  - Preprod NIGHT tokens
  - Optional PostgreSQL
- Added `## Dependencies` section with curated package list and link to `package.json`.
- Pinned `@midnight-ntwrk/dapp-connector-api` at `^4.0.1`.

---

## 4. Context before the contract

- Added `## How the app works` section with:
  - Three roles: Authority, User, Verifier
  - `### What is public vs private` table
  - Note explaining commitment visibility during attestation vs. proof
  - `### End-to-end flow` numbered list
  - End-to-end flow diagram

---

## 5. Smart contract corrections

- Added `pragma language_version 0.22;` and `import CompactStandardLibrary;` to the printed contract.
- Pinned Compact compiler version: `0.30.0`.
- Added the `publicKey()` helper circuit definition before the constructor.
- Softened multi-domain claim: tutorial focuses on `age`; full repo contains `residency` and `certification` circuits.

---

## 6. Wallet, providers, and deploy

- Replaced vague "DApp connector API v4" with exact package name and version.
- Added note explaining `COMPATIBLE_CONNECTOR_API_VERSION = '4.x'`.
- Fixed passive voice: `will be used` → `fetches`.
- Added `## 3. Deploy the smart contract` section.
- Added note acknowledging `as any` / `as never` casts as SDK type-resolution workarounds, not best practice.

---

## 7. Backend and analytics

- Corrected `GET /contract` description: two separate queries, not a SQL JOIN.
- Marked `TRACKED_CONTRACT` as a hardcoded placeholder readers must replace.

---

## 8. Style and grammar

- Fixed sentence-case headings throughout.
- Fixed `DApp` capitalization in prose.
- Changed `eg:` to `e.g.,` in two places.
- Fixed `contract` → `smart contract` in prose where missing.
- Tightened informal aside: removed "Think of it as a secure cryptographic folder."
- Fixed punctuation after `However`.
- Improved link text for source-code link.

---

## 9. Privacy structure

- Refined public vs private table to include:
  - Authority public key as public
  - User password as private
  - Nuanced note about commitment visibility during attestation

---

## 10. Technical verification performed

- Compiled `Contract.compact` successfully with `compactc-fixed 0.30.0`.
- Cross-referenced claims against official Midnight docs (`midnight-docs` repo clone).
- Verified DApp Connector API methods (`connect`, `getConnectionStatus`, `apiVersion`, `balanceUnsealedTransaction`) against official spec.
- Verified Merkle tree privacy claims against `keeping-data-private.mdx`.

---

## Known remaining differences

| Item | Tutorial | Source | Reason |
|---|---|---|---|
| `TRACKED_CONTRACT` placeholder | Concrete hash shown | `''` empty string in code | Tutorial uses concrete example for visibility |
| Analytics server routes | Only `/contract` shown | Also has `/status`, `/track/:address`, `DELETE /contract/:address` | Tutorial focuses on core flow |
| `initDb()` behaviour | Not detailed | Drops and recreates tables | Omitted for brevity |

# nft-cheddar-creator.clar (creator NFT & Utility)

++Purpose: Manages the core NFT collection (SIP-009) which grants Creator Status and basic DAO membership.

---

## A. Data Defintions (State & Constants)
| Clarity Syntax | Data Name | Type | Description |
|----------------|-----------|------|------------|
| `(define-non-fungible-token)` | `cheddar-creator-nft` | `uint` | Core NFT ID to manage ownership. |
| `(define-data-var)` | `next-token-id` | `unit` | Counter for minting (starts at u1). |
| `(define-constant)` | `MAX_SPPLY`  | `u5000` | Hard cap on the number of Creator NFTs. |
| `(define-constant)` | `MINT-PRICE` | `u1000` | Price in STX for minting a single NFT. |
| `(define-constant)` | `FEE-RECEIVER-ADDRESS` | `principal` | Address of the DAO Treasury to receive fees. |

---

## B. Public Function: `mint()` (Create NFT)

++Goal: Allow users to purchase and mint a new Creator NFT.

| Step | Pseudocode Logic | Description |
|------|-------------------|------------|
| 1 | `asserts! (next-token-id <= MAX_SUPPLY) ERR-SOLD-OUT` | Prevents over-minting. |
| 2 | `try! (stx-transfer-from tx-sender FEE-RECEIVER-ADDRESS MINT-PRICE)` | Ensure sender pays the price to the Treasury. |
| 3 | `(nft-mint cheddar-creator-nft next-token-id tx-sender)` | Mints the new NFT to the sender. |
| 4 | `(set-next-token-id (+ next-token-id u1))` | Increment otken counter. |

## C. Read-Only Function: `is-creator(address)` (Membership CHeck)

++Goal: Check if an address holds any Cheddar Creator NFT. (Used by UI and DAO Contract)

| Step | Pseudocode Logic | Description |
|------|------------------|------------|
| 1 | `(define-read-only (get-balance cheddar-creator-nft address))` | Query the number of NFTs held by the input address. |
| 2 | `(return (> balance u0))` | Returnr True if the address holds at least one NFT. |

---

# dao-cheddar-governance.clar (Governance & Volting)

++Purpose: Manages the creation, voting, and executing of DAO proposals, using `nft-cheddar-creator` for voting power.

---

## A. Data Definitions (State & Constants)

| Clarity Syntax | Data Name | Type | Description |
|----------------|----------|-------|------------|
| `(define-map)` | `proposals` | `uint -> {status, yes-votes, no-votes, ...}` | Stores the current state of all proposals. |
| `(define-map)` | `voter-records` | `{ proposal-id: uint, voter: principal } -> bool` | Tracks who has voted to prevent double voting. | 
| `(define-constant)` | `VOTING_PERIOD-BLOCKS` | `u10000` | Duration of the voting period (in Stacks blocks). |
| `(define-constant)` | `QUORUM-THRESHOLD` | `u500` | Mininum number of total votes required for a proposal to be valid. |

---

## B. Public Function:  `submit-proposal()` (Create Proposal)

| Step | Pseudocode Logic | Description |
|-----|-------------------|------------|
| 1 | `asserts! (try! (contract-call? 'nft-cheddar-creator.clar' is-creator tx-sender)) ERR-NOT-DAO-MEMBER` | Only Creator NFT holders can submit proposals. |
| 2 | `(map-set proposals new-id { status: "Active", start-block: block-height, ...})` | Initializes the new proposal. |

## C. Public Function: `vote(proposal-id, vote-option)` (Cast Vote)

| Step | Pseudocode Logic | Description |
|------|-----------------|----------|
| 1 | `asserts! (is-active proposal-id)` <br> `asserts! (try! (contract-call? 'nft-cheddar-creator.clar' is-creator tx-sender)) ERR-NO-VOTE-POWER` | Ensure proposal is active and sender is a Creator. |
| 2 | `(let (nft-count (get-balance 'nft-cheddar-creator.clar' tx-sender)))` | Voting Power: Gets number of NFTs held (1 NFT = 1 Vote). |
| 3 | `asserts! (not (map-get voter-records { ... })) ERR-ALREADY-VOTED` | Prevent casting the same vote twice |
| 4 | `(map-set proposals proposal-id { ... add-votes nft-count ... })` | Adds voting power to the chosen option. | 

---

## D. Public Function: `execute-proposal(proposal-id)` (Finalize)

| Step | Pseudocode Logic | Description |
|------|------------------|----------|
| 1 | `asserts! (block-height > end-block proposal-id) ERR-VOTING-STILL-OPEN` | Ensures voting period is over. |
| 2 | `if (is-passed proposal-id QUORUM-THRESHOLD)` | Checks if total votes meet the quorum AND Yes votes > No votes. |
| 3 | `try! (call-intended-action proposal-data)` | Execute the action (e.g., changing fee structure, transferring Treasury funds). |
| 4 | `(map-set proposals proposal-id { status: "Executed" })` | Sets final status. |
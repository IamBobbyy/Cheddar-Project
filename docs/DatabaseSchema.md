# I. Gun DB Scheme: Real-Time & Mutable Data

++Mechanism:++
Gun DB is a graph database that operates in real-time and is decentralized. It is suitable for storing non-critical data that dose not requires strict validation but needs fast peer-to-peer access and updates.

---

## 1. NFT Metadata (Rich Content)

Although NFT contracts store URIs on Arweave, the UI requires parsed metadata to render quickly.

| Node Key Format (Graph Node ID) | Fields Stored | Mechanism & Use Case |
|---------------------------------|---------------|---------------------|
| `nft_metadata/{token-id}` | title (string), description (string), image_url (Arweave URL), attributes (Map/Object of key/value traits), creator_address (principal) | Real-time Display: UI loads this data immediately to render the NFT detail page |

---

## 2. DAO Proposal Content (Full Text)

The DAO contract only stores a hash or ID of the proposal. Gun DB stores the full content so members can read it.

| Node Key Format (Graph Node ID) | Fields Stored | Mechanism & Use Case |
|---------------------------------|---------------|--------------------|
| `dao_proposal/{proposal-id}` | markdown_content (string), submitted_by (principal), submitted_tx_id (string) | Audit Linkage: UI loads the proposal content, and `submitted_tx_id` links to the on-chain hash for verification |

## II.KYVE Indexing: Historical & Verifiable Data

++Mechanism++
KYVE is a data indexing network that stores data from the Stacks Blockchain permanently and verifiably. It indexes on-chain events from smart contracts to allow the frontend to query historical data efficiently.

---

## 1. NFT & Trancation History

KYVE listens to state changes in `nft-cheddar-creator.clar`

| Data Indexed (Events & State) | Description | Use Case for Frontend |
|------------------------------|------------|--------------------|
| mint events | Records ID, minter, mint price, and block height |  Displays mint history and statistics |
| transfer events | Records NFT transfers (seller, Buyer, Token ID) | Shows ownership history and total sales volumn |
| Current NFT Supply | Number or NFTs minted | Updates the progress bar on the minting page |

---

## 2. DAO Governance Audit Trail

KYVE listens to key function calls in `dao-cheddar-governance.clar`

| Data Indexed (Events & State) | Description | Use Case for frontend |
|-------------------------------|-------------|---------------------|
| submit-proposal calls | Records proposal ID and block time when created | Displays active and upcoming proposals |
| vote calls | Records voter address, proposal ID, and chosen option | Auditing: Fronted shows who voted for what |
| execute_proposal results | Records final outcomes of the vote (Pass/Fail) | Displays historical DAO decisions and platform changes |

---

## Summary: Data Connection 
| Feature | Storage Method | Fronted Use Case |
|---------|----------------|-----------------|
| Creator Status Check | On-Chain: Use Micro-Stacks to call `nft-cheddar-creator::is-creator()` (Read-Only) | Display NFT ownership history |
| Transfer History | KYVE: Query indexed transfer events | Display NFT Transfer history |
| Proposal Content | Gun DB: Load `markdown_content` using proposal ID | Render full proposal text |
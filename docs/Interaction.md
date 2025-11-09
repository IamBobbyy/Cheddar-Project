# Blockchain Data Interaction Plan (Indexing & Querrying)

Data access is divided into ++three levels++, based on importance and type of data.

---

## I. Real-Time On-Chain Checks

For critical data that requires verification of the current state (e.g., ownership rights), queries are made directly from smart contracts via ++Micro-Stacks Library++.

| Data Queried | Smart Contract Function | Micro-Stacks query method | UI Usage |
|--------------|-------------------------|---------------------------|----------|
| Creator Status | `nft-cheddar-creator.clear::is-creator(principal)` | `callReadOnlyFunction` to check if `tx-sender` holds the NFT | Controls display of "Submit Proposal" or "List NFT" buttons |
| Voting Power |  `nft-cheddar-creator.clear::get-balance(principal)` | fetch the number of NFTs held by the user to determine voting weight | Shows the number of votes the user can cast |
| Proposal Status | `dao-cheddar-governance.clar::get-propocal-status(uint)` | check the current status of a proposal (Active, Passed, Failed) | Update UI to show if the proposal can be voted on or executed |

---

## II. Historical Indexing & Auditable Date (KYVE Indexing)

For historical and verifiable data, ++KYVE++ indexes all events and transaction calls on the Stacks Blockchain.

### A. Data Pipleline (KYVE)

1. ++KYVE Listener++:
    Set up a KYVE Pool to listen to events emitted from the two smart contracts:
    - `nft-cheddar-creator.clar`
    - `dao-cheddar-governance.clar`

2. ++Indexing++:
    When a transaction occurs (e.g., mint or vote), KYVE fetches all event outputs and stores them in the data indexer.

3. ++Data Indexer++:
    KYVE stores structured data such as `(Block Height, Token ID, From Address, To Address, Event Type)`.

### B. Querying for Fronted

The fronted queries the KYVE indexer for historical data instead of querying nodes directly, which is much slower.

| Data Queried | Source / KYVE Indexed Data | Example Query |
|--------------|----------------------------|--------------|
| NFT Sales History | Transfer events (from `nft-cheddar-creator.clar`) | "Fetch all transfer events for Token ID X in the last 30 days" |
| DAO Vote Tally | vote calls (from `dao-cheddar-governance.clar`) | "Fetch total Yes or No votes for Proposal ID Y" |
| Creator NFT Volume | mint and transfer events | "Calculate total trading volume of all Cheddar NFTs" |

---

## III. Real-time Supplement Data Storage (Gun DB)

For non-critical data or data requiring fast peer-to-peer updates, ++Gun DB++ is used.

| Data queried | Gun DB Key | Query Method | UI Usage |
|--------------|------------|--------------|---------|
| Rich Proposal Text | `dao_proposal/{proposal-id}` | Use Gun Node ID to fetch full Markdown content | Display proposal details immediately when user clicks |
| NFT Visual Metadata | `nft-metadata/{token-id}` | Load URI, title, and description (parsed) | Render NFT detail page quickly  (cache Arweave content) |

---

## Summary: Dataflow Interaction

| Step | Component Used | Purpose / Reason |
|------|----------------|------------------|
| Initical Check | Micro-Stacks (Read-Only) | Verify permissions before user performs transactions (safety efficiency) |
| Execution | Stacks API / Hiro Wallet (Public Function) | send transactions (`mint()` or `vote()`) to update on-chain state |
| Historical VIew | KYVE Indexer | Load historical NFT sales, votes, and aggregates for dashboards |
| UI Enhancements | Gun DB | Fetch frequently updated or non-critical supplemental data (e.g., pre-parsed metadata) |
## Layered Storage Architecture

Each service is chosen based on whether the data must remain ++permanent++ or be ++mutable and fast++.

---

## 1. Primary Service : Arweave (Permaweb Storage)

Arweave is the ideal choice for data that must be ++permanent++, ++immutable++, and ++stored with a single, upfront payment++.

| Data Type Stored | Reason Using Arweave | How It Works |
|----------------------|--------------------------|-----------------|
| NFT Metadata (JSON files) | Immutability Guarantee: Ensures that the title, description, and properties of the Creator NFT (and any user-minted NFTs) can never be changed after minting. Prevents "rug pulls" Where the asset link is modified later. | The NFT Smart Contract (`nft-cheddar-creator.clar`) stores the Arweave Transaction Hash as the Token URI. |
| Frontend Assets | Censorship Resistance: The web application's ++HTML, CSS, and JavaScript files should be stored permamently to ensure that the UI is always accessible - independent of centralized servers. | The ++Stacks ecosystem++ can link decentralized domains (link ENS/BNS names) directly to the Arweave-hosted frontend. |

---

## 2. Dynamic Data: Gun DB (Off-Chain Mutability)

Gun DB handles real-time, mutable data that needs to be updated quickly and frequently.
This includes data types that are not practical to store permanently on an immutable ledgar.

| Data Type Stored | Reason for Using Gun DB | Use Case Examples |
|------------------|-------------------------|---------------|
| Notificaiton & Activity logs | frequent updates unsuitable for permanent storage. | User alerts, new proposals, or NFT sale updates. |
| Proposal Drafts / Temporary Edits | Editable content before being finalized on-chain or in Arweave. | DAO proposal drafts or unsubmitted forms. |

## Summary: Why This Matters

- Arweave : Provides permanent, verifiable storage for NFT metadata and frontend assets - ensuring the integrity of your ecosystem.
- Gun DB : Complements it by handling dynamic, real-time, and mutable data off-chain.
- Together, they create a resilient, efficient, and Web3-native data storage stack.

---

## Benefits
- Immutability: Arweave guarantees that critical NFT data cannot be altered.
- Speed: Gun DB ensures fast updates without blockchain latency.
- Web3 Alignment: Combines permanent storage with real-time decentralized updates - the best of both worlds.
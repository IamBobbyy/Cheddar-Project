# Data Requirements from Redsone Oracle

while the core NFT and DAO logic is self-contained on ++Stacks++, an ++NFT EXchange++ requires reliable real-world pricing data for key operational and financial calculations.

---

## Required Data Types

| ++Data Type Required++ | ++Why It's Needed++ | ++Redstone Data Feed++ |
|------------------------|---------------------|---------------------|
| ++Asset Price Feeds++ | To convert fiat or alternative token values into the base Stacks/Bitcoin currency, or vice versa, for pricing and fee calculation. | `STX/USD` (Stacks Price), `BTC/USD` (Bitcoin Price), `ETH/USD` (for cross-chain compatibility if needed) |
| ++sBTC Peg Verifcation++ | If you exchange allows transactions in sBTC (Stacked Bitcoin), you must verify its peg against regular BTC - important for complex transactions or collateral checks. | `BTC/sBTC` (Parity Check) |
| ++Gas Fee Prediction++ | While Stacks fees are generally low and predictable, estimating the current Bitcoin fee rate can help if transactions rely on Bitcoin finally (PoX layer) or for advanced fee estimation. |  `BTC Gas Price` (or similar network fee data) |

---

## How Redstone Integrates with Stacks

Redstone operates on a ++modular data delivery model++, which fits naturally with ++Clarity++ language and the ++Stacks++ ecosystem.

### Data Flow Overview

1. ++Data Encoding++
    Redstone aggregates data feeds and ++cryptographically signs++ them off-chain.

2. ++Data Delivery++
    The ++Signed data packages++ are delivered to your DApp via either:
    - The ++Frontend++ (off-chain caching/storage), or
    - A ++dedicated Relayer++ that injects the data into transactions.

3. ++On-Chain Verification++
    When your ++Smart Contract++ on Stacks requires a price:
    - The ++signed Redstone data package++ is included in the transaction payload.
    - The ++Clarity contract++ verifies:
        - The ++signature authenticity++, and
        - The ++timestamp freshness++ (to prevent replay or outdated data).
    - Once verified, the price value can safely be used in transaction logic.

---

## Example Use Case : NFT Pricing with Redstone

> ++Scenario: A creator lists an NFT for ++$1,000 USD++, but the transaction is executed in ++STX++.

### Step-by-Step Process

1. ++Frontend Price Fetch++
    The frontend fetches the current ++STX/USD++ price from Redstone
    -> Example: `$1.50 USD / STX`

2. ++Price Conversion++
    Convert target price into STX: => $1,000 / $1.50 = 666.67 STX

3. ++Transaction Preparation++
The frontend prepares the NFT listing transaction with:
- The ++target price (666.67 STX)++
- The ++signed Redstone price data++ as proof of validity

4. ++Smart Contract Verification++
The ++Clarity contract++ verifies:
- The ++Redstone signature++, ensuring authenticity
- The ++timestamp++, ensuring freshness

5. ++Finalization++
Once verified, the NFT listing is confirmed using the ++verified, real-world price++.

---

## Benefits
- Tamper-Proof = All pricing data is cryptographically signed.
- Time-Sensitive = Ensures prices are up-to-date and not replayed.
- Seamless Integration = Works directly with Clarity contracts through payload inclusion.
- Cross-Chain Ready = Supports STX, BTC, ETH, and more for future-proof interoperability
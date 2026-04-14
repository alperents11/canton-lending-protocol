# Canton Lending Protocol

A decentralized lending protocol built on Canton Network using Daml smart contracts. Users can deposit collateral, request loans, and manage repayments — all enforced on-chain with Canton's privacy-preserving architecture.

---

## What It Does

Canton Lending Protocol enables trustless peer-to-peer lending on the Canton Network:

- A **borrower** locks collateral into a `CollateralVault`
- The borrower submits a `LoanRequest` to a specific lender
- The **lender** reviews and either accepts or declines the request
- Upon acceptance, a `LoanAgreement` is created on-chain
- The borrower repays the loan, or the lender liquidates the collateral if the borrower defaults

All logic is enforced by Daml smart contracts — no intermediary needed.

---

## Why Canton Network?

Canton's **Need-to-Know Privacy** model makes it ideal for lending:

- Loan details are visible **only** to the borrower and lender — not the whole network
- `CollateralVault` data is physically isolated on the owner's participant node
- Multi-party signatories ensure mutual consent before any agreement is created
- Sub-transaction privacy: even the Canton Sequencer only sees encrypted payloads

---

## Smart Contract Architecture

### `CollateralVault`
Holds the borrower's collateral. Only the owner has visibility.

| Field | Type | Description |
|-------|------|-------------|
| `owner` | Party | Borrower who owns the vault |
| `amount` | Decimal | Collateral amount |
| `vaultId` | Text | Unique vault identifier |

**Choices:** `Lock`, `Unlock`

---

### `LoanRequest`
A loan request submitted by the borrower to a specific lender.

| Field | Type | Description |
|-------|------|-------------|
| `borrower` | Party | Party requesting the loan |
| `lender` | Party | Target lender |
| `amount` | Decimal | Requested loan amount |
| `collateralCid` | ContractId CollateralVault | Reference to locked collateral |

**Choices:** `Accept` → creates `LoanAgreement`, `Decline`

---

### `LoanAgreement`
The active loan agreement between borrower and lender.

| Field | Type | Description |
|-------|------|-------------|
| `borrower` | Party | Borrower |
| `lender` | Party | Lender |
| `amount` | Decimal | Loan amount |
| `collateralCid` | ContractId CollateralVault | Locked collateral reference |

**Choices:** `Repay`, `Liquidate`

---

## Full Lending Flow

```
Borrower                          Lender
   |                                 |
   |-- Create CollateralVault -----> |
   |-- Submit LoanRequest ---------> |
   |                                 |-- Accept --> LoanAgreement created
   |-- Repay ------------------------|
   |                                 |-- Collateral released
```

---

## Test Simulation

The project includes a full end-to-end test script (`testLendingFlow`) that simulates:

1. Alice (borrower) creates a `CollateralVault` with 1000.0
2. Alice submits a `LoanRequest` to Bob (lender) for 500.0
3. Bob accepts → `LoanAgreement` created on-chain
4. Alice repays the loan

---

## Tech Stack

- **Smart Contracts:** Daml 3.x (SDK 3.4.10, LF 2.1)
- **Network:** Canton Network (Global Synchronizer)
- **Development:** DAML Studio
- **Frontend Integration:** `@c7/ledger` library

---

## Getting Started

```bash
# Install Daml SDK
curl -sSL https://get.daml.com/ | sh

# Build the project
dpm build

# Run simulation
daml script --dar .daml/dist/lending-protocol.dar \
  --script-name LendingFlow:testLendingFlow \
  --ledger-host localhost --ledger-port 6865
```

---

## Track

**Financial Applications (DeFi)** — This project delivers a working MVP of a peer-to-peer lending protocol on Canton Network, demonstrating real economic activity with collateral management and loan lifecycle enforcement.

---

## Author

Built solo for HackCanton Season #1.

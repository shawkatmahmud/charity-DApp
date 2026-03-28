# CN6035 Coursework Report
## Development, Configuration, and Enhancement of a Web3 Charity DApp

## Introduction

This report presents the design, implementation, debugging, and enhancement of a decentralized charity donation application (DApp) completed as part of CN6035 coursework. The project is a Web3-enabled fundraising platform where users can create charity campaigns, donate in cryptocurrency, and track transparent donation activity through blockchain state.

### Aims and Objectives

The primary aim of this coursework was to develop and operationalize a full-stack DApp integrating smart contracts, Web frontend technologies, and wallet-based authentication/transactions. The specific objectives were:

- To understand and apply decentralized application architecture across frontend, backend API routes, and blockchain layers.
- To deploy and interact with a Solidity contract for charity campaign lifecycle operations.
- To configure wallet connectivity and authenticated user sessions suitable for real-world Web3 UX.
- To troubleshoot and resolve practical runtime issues, including network mismatch, insufficient funds, local node integration, and component rendering failures.
- To refactor and improve project stability for local development and demonstration use.

### Expected Outcomes

The expected outcomes were:

- A functioning charity DApp capable of campaign creation, campaign viewing, and ETH donations.
- Successful local execution against Hardhat with deterministic smart-contract interaction.
- Stable wallet connectivity and transaction flow across test environments.
- Evidence of iterative software improvement through structured debugging and targeted code changes.
- A maintainable codebase with clear local run instructions and documented modifications.

---

## Discussing the DApp in Detail

### 1. System Overview

The DApp is structured as a modern Web3 full-stack application with four major layers:

1. **Smart Contract Layer (Solidity + Hardhat)**  
   A contract manages charity entities and supporter records, including creation, updates, donation, and administrative controls (ban/delete/tax modifications).

2. **Blockchain Interaction Layer (Ethers + service wrappers)**  
   Client-side service modules encapsulate contract calls and map blockchain data into UI-friendly structures.

3. **Frontend Layer (Next.js + React + Redux + Tailwind)**  
   The UI includes homepage discovery, project details pages, create/edit forms, donation modals, and support list views.

4. **Wallet/Auth Layer (Wagmi + RainbowKit + NextAuth + SIWE)**  
   Wallet connection is managed through RainbowKit/Wagmi while authentication uses Sign-In with Ethereum semantics.

### 2. Core Functional Flows

#### a) Charity Creation Flow
A connected user submits campaign metadata (name, owner name, profile link, target amount, description, image). The frontend validates form fields and then triggers an on-chain transaction to `createCharity(...)`. On confirmation, the campaign becomes queryable through smart-contract getters.

#### b) Donation Flow
A donor opens a project page and submits a donation amount and optional message. The donation function sends ETH value to the contract and records supporter metadata. UI components refresh supporter/campaign totals from chain state and re-render updated values.

#### c) Admin/Owner Management
Authorized controls exist for updating/deleting campaigns and toggling ban status. These operations are contract-gated and surfaced in the frontend only where role/ownership conditions are met.

### 3. Data and State Management

- **On-chain state** is the source of truth for campaigns and donations.
- **Redux global slices** are used to synchronize campaign/support data across components after transactions.
- **Server-side page data loading** is used on detail pages to fetch initial campaign/support datasets.

### 4. Network and Wallet Considerations

The project supports Sepolia-style configuration and local Hardhat workflows. In practice, local development introduces common challenges:

- RPC endpoint mismatch (`http` vs `https` on localhost).
- Wallet connected to a different chain than the contract deployment chain.
- Account switching in WalletConnect/mobile sessions causing unexpected signer changes.
- Local signer balance inconsistencies after node restart or account drift.

---

## Changes Made to the DApp

### 1) Branding + Homepage Customization

- Updated the public name to **Shawkat's Charity DApp** in homepage and core UI labels.
- Changed homepage banner image to local asset `/background.webp`.
- Updated CTA text and homepage title to align with coursework branding.

### 2) Wallet/Chain Configuration Fixes

- Standardized supported chains to `sepolia`, `hardhat`, and `localhost`.
- Added safer provider fallback behavior when optional key-based providers are unavailable.
- Set a deterministic initial chain for improved connect behavior.

### 3) Local Hardhat Reliability Improvements

- Added local fixed-signer mode via environment flag to avoid unstable signer switching in mobile WalletConnect flows.
- Added robust local RPC fallback handling.
- Ensured transaction functions can run through fixed local signer mode during localhost testing.

### 4) Runtime UI Crash Stabilization

To address `Element type is invalid` issues during submit actions:

- Reworked menu/dropdown rendering path to a plain React implementation.
- Replaced unstable toast-promise submit paths in create and donor flows with explicit `try/catch` + user feedback.

### 5) Local Deployment Alignment

- Re-deployed and seeded contract on localhost.
- Updated local contract address mapping (`contracts/contractAddress.json`) to active localhost deployment.
- Aligned environment settings to local chain operation (`NEXT_PUBLIC_RPC_URL=http://127.0.0.1:8545`).

### 6) Documentation Improvements

- Added `RUN_DAPP.md` with end-to-end setup, MetaMask (desktop/mobile) config, and troubleshooting commands.
- Updated `README.md` with a coursework-focused change log and practical local run sequence.

---

## Testing and Evaluation

This section summarizes concise but targeted testing performed to validate functionality, integration, and runtime stability.

### 1) Test Environment

- OS: Windows
- Local chain: Hardhat (`chainId 31337`)
- RPC endpoint: `http://127.0.0.1:8545`
- Frontend: Next.js development server on `http://localhost:3000`
- Wallet contexts tested: desktop MetaMask and mobile WalletConnect scenarios

### 2) Functional Validation Cases

- **Contract read path**: verified `getCharities()` and `getCharity()` return valid decoded data when RPC and contract address are aligned.
- **Create charity flow**: validated successful campaign creation after signer and balance corrections.
- **Donate flow**: validated modal submit and donation transaction execution after runtime fixes and signer stabilization.
- **Deploy + seed pipeline**: verified local deployment and seed scripts execute successfully against localhost network.

### 3) Integration/Configuration Validation

- **Chain verification**: confirmed local RPC returned `eth_chainId = 0x7a69` (31337).
- **Balance verification**: used `eth_getBalance` to verify active sender account funding before transactions.
- **Transaction forensics**: decoded failing raw transactions to identify actual signer addresses and diagnose account mismatch.
- **Wallet-network consistency**: validated mismatch scenarios and remediation through fixed signer or explicit funded-account selection.

### 4) Key Defects Encountered and Resolutions

- **Wrong network detected** → fixed chain configuration and supported chain set.
- **Bad decode result data (`0x`)** → corrected contract/network/RPC alignment and re-deployment.
- **Insufficient funds with apparent funded wallet** → traced true signer from raw tx and funded exact sender.
- **Runtime invalid element type on submit** → replaced unstable rendering/submit patterns in impacted components.

### 5) Evaluation Summary

The adjusted system demonstrates reliable local execution with improved determinism in signer selection, fewer runtime rendering failures, and repeatable deploy/run workflows. Remaining complexity is primarily around external wallet session behavior (especially mobile WalletConnect), which was mitigated via fixed local signer mode for coursework demonstration.

---

## Conclusion

This coursework demonstrates practical end-to-end DApp engineering beyond initial implementation: architecture design, wallet integration, contract interaction, environment setup, and iterative debugging in real operational conditions.

The final system supports the intended charity campaign lifecycle and donation functionality, with meaningful improvements in stability and developer usability. A major learning outcome was moving from symptom-level fixes to root-cause analysis (for example, extracting true sender addresses from signed raw transactions).

### Key Achievements

- Delivered a working blockchain-backed charity DApp flow.
- Resolved major blockers in wallet/network/local deployment behavior.
- Improved runtime robustness for create/donate operations.
- Added deterministic local signer support for repeatable demonstrations.
- Produced structured technical documentation suitable for coursework assessment.

---

## References (Harvard Style)

Buterin, V. (2014) *A next-generation smart contract and decentralized application platform*. Available at: https://ethereum.org/en/whitepaper/ (Accessed: 28 March 2026).

Dannen, C. (2017) *Introducing Ethereum and Solidity: Foundations of Cryptocurrency and Blockchain Programming for Beginners*. Berkeley, CA: Apress.

Hardhat (2026) *Hardhat documentation*. Available at: https://hardhat.org/docs (Accessed: 28 March 2026).

MetaMask (2026) *MetaMask developer documentation*. Available at: https://docs.metamask.io/ (Accessed: 28 March 2026).

Next.js (2026) *Next.js documentation*. Available at: https://nextjs.org/docs (Accessed: 28 March 2026).

OpenZeppelin (2026) *OpenZeppelin Contracts documentation*. Available at: https://docs.openzeppelin.com/contracts (Accessed: 28 March 2026).

RainbowKit (2026) *RainbowKit documentation*. Available at: https://www.rainbowkit.com/docs/introduction (Accessed: 28 March 2026).

Redux Toolkit (2026) *Redux Toolkit documentation*. Available at: https://redux-toolkit.js.org/ (Accessed: 28 March 2026).

Wagmi (2026) *Wagmi documentation*. Available at: https://wagmi.sh/ (Accessed: 28 March 2026).

Wood, G. (2014) *Ethereum: A secure decentralised generalised transaction ledger (Yellow Paper)*. Available at: https://ethereum.github.io/yellowpaper/paper.pdf (Accessed: 28 March 2026).

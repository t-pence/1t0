Citizen Wallet & Open-Market Banking App
(v 0.9 – for community review)

1 · Vision
Create a fully open-source “citizen wallet” that lets any UK resident (or global user, under local compliance) hold digital pounds, tokenised sovereign bonds, and selected crypto-assets, then trade or lend them peer-to-peer at zero commission.
The stack must be:

Principle	Implementation Snapshot
Open-source	GPL-3 / MIT dual licensing for core modules
Modular & auditable	Micro-services in Docker, CI/CD via GitHub Actions
Reg-ready	Hooks for KYC/AML, UK Open Banking & HMRC APIs
Self-custodial	Keys in browser/mobile secure enclave; no custodial risk
Low-fee	Gas-optimised contracts, Layer-2 default rails
Community-governed	DAO-style module upgrade voting (optional plug-in)

2 · Product Modules
#	Module	Key Features	Tech Choices
1	Wallet Core (frontend)	Multi-asset balances; send/receive; QR; hardware-wallet connect	React + TypeScript, Web3Modal, Ethers.js
2	DEX & Money-Market Hub	0 %-commission swap aggregator; pooled lending/borrowing; variable APY display	Smart-contract adapter to Aave v3, Uniswap v4 hooks
3	Tokenised Bond Manager	Display Commons Sovereign Bond holdings; auto-claim dividends; governance vote interface	Custom Solidity/Besu contracts; subgraph indexing
4	Fiat On/Off-Ramp	Open Banking payments-initiation (UK) & SEPA; card plug-in	TrueLayer / Volt APIs
5	Identity & Compliance	KYC widget; proof-of-address; Politically-Exposed-Person screening	Open-source Passbase fork; UKGov Verify adapter
6	Analytics & Tax	HMRC tax-lot export (CSV); capital-gains calculator	Node.js service + PostgreSQL
7	Developer SDK	JS/TS SDK; Python client; example dApp templates	npm/pyPI packages; OpenAPI spec
8	Governance UI (opt-in)	DAO proposals; quadratic voting; contract upgrade approvals	Snapshot/X-Space integration

3 · Supported Blockchains & “Plug-In” Policy
Tier	Network	Rationale
Core L2s	Polygon PoS / zkEVM, Arbitrum, Optimism	Low gas, EVM-compatible, proven bridges
Sovereign rails	Besu permissioned chain (DIGIT testnet)	For Commons Bond & HMRC integration
Optionals	Celo (mobile first), Avalanche Subnet, Cosmos SDK (IBC)	Community demand; modular bridge adaptors

Crypto-asset list: ETH / wETH, MATIC, DAI, GBP-stable (EURS-style), tokenised gilts (CSB), BTC (via wBTC)
Policy: No privacy-coins or securities-like tokens without explicit legal clearance.

4 · High-Level Architecture
pgsql
Copy
Edit
                       +-------------------+
                       |  React Frontend   |
                       +--^-----------^----+
                          | WebSocket | REST / GraphQL
+--------------+      +---+-----------+---+        +--------------------+
|  Wallet-Core |<---> | API Gateway (NestJS) | <--> |  PostgreSQL / Redis|
+--------------+      +---+-----------+---+        +--------------------+
                          | RPC/SDK   | gRPC
                +-----------------+   +------------------+
                |  Chain Adaptor  |   | Compliance Micro |
                |  (EVM & Besu)   |   |  (KYC/AML)       |
                +---------+-------+   +---------+--------+
                          | On-chain calls
                       +--v--------------------+
                       |  Smart-Contract Set   |
                       |  • BondFactory        |
                       |  • DividendRouter     |
                       |  • P2P Lend Pool      |
                       +-----------------------+
All containers orchestrated via docker-compose.yml; future Helm charts for Kubernetes.

5 · Docker Support Checklist
Dockerfile per micro-service (multi-stage: build → slim runtime).

docker-compose.yml with profiles:

dev-local (Ganache, Postgres, Mailhog)

testnet (Infura/Alchemy, Besu, external Redis)

Volumes for keys/secrets (never baked into images).

Health-check endpoints (/healthz).

CI pipeline: lint → unit tests → Docker build → image scan → push to GHCR.

6 · Security & Audits
Area	Plan
Smart contracts	Static analysis (Slither), formal spec (Scribble), external audit (OpenZeppelin).
Frontend	Snyk OSS scanning, CSP headers, encrypted keystore in IndexedDB.
API Gateway	JWT + rate-limit; OWASP ZAP automated in CI.
Key management	ECDSA keys client-side; server signs only non-custodial meta-tx if user opts.

7 · MVP Acceptance Criteria
Create wallet, back up seed; send/receive ETH & DAI on Polygon testnet.

Import tokenised bond; claim mock dividend.

P2P lend 10 DAI, borrow 5 DAI via Aave test pool.

Export HMRC tax CSV.

All services run with one-command docker-compose up and 90% unit-test coverage.

8 · Roadmap (Quarter-level)**
Q	Deliverable
Q1	Wallet core + Polygon L2; Docker baseline; dividend demo.
Q2	Besu integration; fiat on-ramp; compliance micro-service.
Q3	P2P marketplace live; DAO gov module; mobile PWA.
Q4	Multi-chain bridge plug-ins; open external API & SDK; third-party dApp listing.

9 · Governance & Contributions
Project lead: Commons Infrastructure Group

Core maintainers: 5 FTE devs (2 smart-contract, 3 full-stack)

Community pull-requests: reviewed weekly; merge requires 2-of-3 maintainer approvals.

Roadmap votes: token-gated Snapshot for critical feature priorities.

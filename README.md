# MonDAO — On-Chain Governance on Monad

> A transparent, permissionless voting system where every decision is recorded permanently on a public blockchain.

**Live app →** https://monskills-tracker.replit.app/
**Smart contract on MonadScan →** https://testnet.monadscan.com/address/0xF4699628B3ae35eA05Cb94C891b589E9F180d217

---

## What Is This?

MonDAO is a **decentralized autonomous organization (DAO) voting application** built on the [Monad](https://monad.xyz) blockchain. It lets any community make collective decisions without relying on a central authority — no company, no administrator, no single point of control.

When a proposal is created and voted on, the result is not stored in a database owned by anyone. It is written to a global, append-only ledger (the blockchain) that anyone in the world can read and verify independently.

---

## Why This Matters — For Non-Technical Readers

Traditional voting systems — whether in corporations, co-ops, non-profits, or online communities — share a common problem: **you have to trust whoever counts the votes**. The tallying authority can make mistakes, can be pressured, or can act in bad faith. Auditing is difficult and often restricted.

Blockchain-based governance replaces that trust requirement with mathematics and open code:

| Traditional voting | MonDAO |
|--------------------|--------|
| Results stored in a private database | Results stored on a public blockchain |
| Auditing requires access to internal systems | Anyone can verify results independently |
| Administrator can alter records | Records are cryptographically immutable |
| Identity verified by a gatekeeper | Identity verified by your own cryptographic key |
| Closed-source tallying logic | Open-source smart contract, auditable by anyone |

This is especially relevant for:
- **Research communities** deciding on shared resources or norms
- **Open-source projects** choosing roadmap priorities
- **Cooperatives and collectives** where members want equal, verifiable participation
- **Interdisciplinary teams** spanning institutions who need a neutral coordination layer

---

## How It Works — The Governance Flow

```
Anyone with a wallet
        │
        ▼
  Create a proposal          ← sets title, description, 3-day voting window
        │
        ▼
  Community votes            ← For or Against, one vote per address
        │
        ▼
  Results finalized          ← permanently visible on the blockchain
```

Every step above produces a **transaction** — a cryptographically signed record that is broadcast to the Monad network, included in a block, and becomes part of the permanent ledger. There is no "undo."

### Voting rules (set in the smart contract)
- Each wallet address may vote **once per proposal**
- Voting is open for **3 days** from the moment the proposal is created
- For votes and Against votes are counted separately and displayed in real time
- There is no quorum requirement or threshold — results are informational

---

## Why Monad?

[Monad](https://monad.xyz) is an EVM-compatible Layer 1 blockchain designed for high throughput and low cost. Key properties relevant to governance:

- **Fast finality** — transactions confirm in seconds, not minutes
- **Low fees** — voting does not require expensive gas, making participation accessible
- **EVM-compatible** — the same smart contract language (Solidity) and tooling used across the Ethereum ecosystem
- **Fully public** — all transactions visible on [MonadScan](https://testnet.monadscan.com)

This app currently runs on **Monad Testnet** (a free-to-use test network). Testnet tokens have no monetary value; you can get them from a faucet to experiment.

---

## How To Use the App

### Step 1 — Get a wallet
Install [MetaMask](https://metamask.io) (browser extension) or any EVM-compatible wallet. This is your identity on the blockchain — a cryptographic key pair that signs your actions.

### Step 2 — Add Monad Testnet to your wallet
| Setting | Value |
|---------|-------|
| Network name | Monad Testnet |
| RPC URL | `https://testnet-rpc.monad.xyz` |
| Chain ID | `10143` |
| Currency symbol | `MON` |
| Block explorer | `https://testnet.monadscan.com` |

### Step 3 — Get testnet tokens
Visit the [Monad Testnet Faucet](https://faucet.monad.xyz) and request free MON tokens. You need a small amount to pay gas fees.

### Step 4 — Connect and participate
1. Open https://monskills-tracker.replit.app/
2. Click **Connect Wallet** → approve the connection in MetaMask
3. Browse active proposals and click **Vote For** or **Vote Against**
4. To create a proposal, click **New Proposal**, fill in the title and description, and submit
5. Click **View proposal on MonadScan** on any card to inspect the raw on-chain data

---

## Smart Contract

The governance logic lives entirely in a single Solidity contract, deployed and verified on Monad Testnet.

| Property | Value |
|----------|-------|
| Contract address | `0xF4699628B3ae35eA05Cb94C891b589E9F180d217` |
| Network | Monad Testnet (chain ID 10143) |
| Language | Solidity 0.8.24 |
| Verified on | MonadScan · MonadVision |
| Source | [`contracts/src/DAOVoting.sol`](contracts/src/DAOVoting.sol) |

### Public functions

| Function | What it does |
|----------|-------------|
| `createProposal(title, description)` | Opens a new vote with a 3-day deadline |
| `vote(proposalId, support)` | Casts a For (`true`) or Against (`false`) vote |
| `getProposals(offset, limit)` | Returns paginated list of proposals |
| `hasVoted(proposalId, address)` | Returns whether an address has voted |

### Multisig treasury (Safe)
Privileged administrative actions are protected by a **2-of-3 multisig wallet** at `0xbC122FD60b1c3ECA9612DF72bC24FB48D3471357`. No single key can act unilaterally — two of three keyholders must co-sign. This is a standard [Safe](https://safe.global) deployment.

---

## Technical Stack — For Developers

| Layer | Technology |
|-------|-----------|
| Blockchain | Monad Testnet (EVM, Solidity 0.8.24) |
| Contract tooling | Foundry (forge, cast) |
| Frontend framework | React 19 + Vite 7 |
| Blockchain connectivity | wagmi v3 + viem v2 |
| UI components | shadcn/ui + Tailwind CSS v4 |
| Animations | Framer Motion |
| Routing | Wouter |
| Data fetching | TanStack Query v5 |
| Monorepo | pnpm workspaces |
| Deployment | Vercel (static) |

### Running locally

```bash
# Prerequisites: Node 20+, pnpm

git clone https://github.com/sunshineluyao/Monskills-Tracker
cd Monskills-Tracker
pnpm install

# Start the frontend dev server
pnpm --filter @workspace/dao-voting run dev
```

Open http://localhost:3000 (or the port shown in the terminal).

### Building for production

```bash
BASE_PATH=/ pnpm --filter @workspace/dao-voting run build
# Output: artifacts/dao-voting/dist/public/
```

### Deploying the smart contract (Foundry)

```bash
# Foundry binaries are at .config/.foundry/bin/ in this repo
export PATH="$PWD/.config/.foundry/bin:$PATH"

forge script contracts/script/Deploy.s.sol \
  --rpc-url https://testnet-rpc.monad.xyz \
  --broadcast \
  --private-key $DEPLOYER_KEY
```

---

## Repository Structure

```
.
├── artifacts/
│   └── dao-voting/          # React + Vite frontend
│       ├── src/
│       │   ├── components/  # UI components (ProposalCard, WalletButton, …)
│       │   ├── pages/       # home.tsx, create-proposal.tsx
│       │   └── lib/         # wagmi config, ABI, constants, utils
│       └── vite.config.ts
├── contracts/
│   └── src/
│       └── DAOVoting.sol    # Governance smart contract
└── vercel.json              # Vercel deployment config
```

---

## Design Decisions

**Why one contract, no upgradability?**  
Upgradeable contracts introduce governance risk — whoever controls the upgrade key can change the rules. An immutable contract means the rules are fixed at deployment and everyone can trust them.

**Why no token-weighted voting?**  
Token weighting concentrates power with large holders. One-address-one-vote is more egalitarian, though it is susceptible to Sybil attacks (one person creating many wallets). Future versions could integrate proof-of-personhood systems.

**Why a 3-day window?**  
Long enough for participants across time zones to see and respond; short enough to keep governance moving. This is a parameter that communities could adjust by deploying their own instance.

**Why Monad instead of Ethereum mainnet?**  
Ethereum mainnet gas fees make small governance actions (a vote) economically inaccessible. Monad's throughput and fee structure make participation cheap enough that the cost of voting is not a barrier to participation.

---

## Concepts Glossary

| Term | Plain-language definition |
|------|--------------------------|
| **Blockchain** | A public database replicated across thousands of computers, where entries can be added but never deleted or altered |
| **Smart contract** | A program that runs on a blockchain; its rules are enforced automatically without a middleman |
| **Wallet** | Software that holds your cryptographic keys; signs transactions to prove your identity |
| **Gas** | A small fee paid to the network to process a transaction; denominated in the network's native token (MON) |
| **DAO** | Decentralized Autonomous Organization — a group coordinated by smart contracts rather than legal entities |
| **Testnet** | A practice version of a blockchain where tokens have no real-world value; safe for experimentation |
| **Multisig** | A wallet requiring multiple keyholders to sign before any action is taken |
| **Transaction** | A signed instruction sent to the blockchain (e.g., "cast my vote FOR proposal #2") |
| **Block explorer** | A public website that displays all transactions and contracts on a blockchain |

---

## Contributing

Pull requests are welcome. For major changes please open an issue first.

If you are researching on-chain governance, DAO design, or blockchain-based coordination and would like to collaborate or discuss the architecture, open an issue or reach out via the repository.

---

## License

MIT

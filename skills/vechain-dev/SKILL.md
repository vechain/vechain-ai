---
name: vechain-dev
description: VeChain development playbook. VeChain Kit for React dApps, SDK for backends, Solidity+Hardhat for contracts, Thor Solo for testing. Covers fee delegation, multi-clause, social login, VeBetterDAO, StarGate, governance.
allowed-tools: []
license: MIT
metadata:
  author: VeChain
  version: "0.2.0"
---

# VeChain Development Skill

## CRITICAL RULES

1. **Read reference files FIRST.** When the user's request involves any topic in the reference table below, read those files before doing anything else — before writing code, before making decisions. Briefly mention which files you are reading so the user can confirm the skill is active (e.g., "Reading VeChain Kit reference...").
2. **Information priority for VeChain topics:** (a) Reference files in this skill — always the primary source. (b) VeChain MCP tools — use `@vechain/mcp-server` for on-chain data, transaction building, and live network queries; use Kapa AI MCP for VeChain documentation lookups. (c) Web search — only as a last resort, and only for topics NOT covered in the reference files.
3. **Prefer working directly in the main conversation** for VeChain tasks. Plan mode and subagents do not inherit skill context and may fall back to web search instead of using reference files.
4. **After compaction or context loss**, re-read this SKILL.md to restore awareness of the reference table and operating procedure before continuing work.

## Scope

Use this Skill for any VeChain development task:

- Frontend dApps (React/Next.js), wallet connection, social login
- Transaction building, sending, confirmation UX
- Solidity smart contracts on VeChainThor
- Multi-clause transactions, fee delegation (VIP-191)
- Testing with Hardhat + Thor Solo
- Security reviews
- VeBetterDAO / X2Earn apps, StarGate staking, governance

## Default stack (opinionated)

| Layer | Default | Alternative |
|-------|---------|-------------|
| Frontend | `@vechain/vechain-kit` | `@vechain/dapp-kit-react` (lightweight/non-React) |
| SDK | `@vechain/sdk-core` + `@vechain/sdk-network` | `@vechain/sdk-ethers-adapter` |
| Contracts | Solidity + Hardhat + `@vechain/sdk-hardhat-plugin` | -- |
| EVM target | `paris` (mandatory) | -- |
| Testing | Hardhat + Thor Solo (`--on-demand`) | -- |
| Types | TypeChain (`@typechain/ethers-v6`) | `@vechain/vechain-contract-types` (pre-built) |
| Node | Node 20 LTS (managed via `nvm`) | -- |

## Operating procedure

### 1. Check Node version

Before installing dependencies or running any command:

- Check if `.nvmrc` exists in the project root. If yes, run `nvm use` to switch to the required version.
- If `.nvmrc` does not exist, create one with `20` (Node 20 LTS) and run `nvm use`.
- When adding dependencies that require a specific Node version, update `.nvmrc` accordingly.

### 2. Detect project structure

- `turbo.json` present → follow Turborepo conventions (`apps/frontend`, `packages/contracts`, `packages/*`)
- Use `useThor` for Thor client access (both VeChain Kit and dapp-kit v2). `useConnex` is deprecated everywhere.
- Apply conditional patterns (Chakra UI, i18n, Zustand) only when the project uses them

### 3. Clarify before implementing

When the user's request is ambiguous or could be solved multiple ways, **ask before building**. Do not silently research alternatives and pick one. Separate research from implementation:

- If the scope is unclear, ask the user to narrow it
- If multiple architectures are viable, present trade-offs and let the user choose
- Only proceed to implementation once the approach is agreed upon

### 4. Classify the task layer

UI/hooks → SDK/scripts → Smart contracts → Testing/CI → Infra

### 5. Pick building blocks

- UI (full-featured): VeChain Kit hooks + components
- UI (lightweight/non-React): dapp-kit
- Backend/scripts: `@vechain/sdk-core` + `sdk-network` directly
- Legacy Connex present: migrate or isolate behind adapter boundary

**When to ask the user:** If the project doesn't already use VeChain Kit or dapp-kit and the user hasn't specified which to use, ask before choosing. Key questions:

- Do you need social login (email, Google, passkey)? → VeChain Kit
- Do you want pre-built UI modals and hooks (WalletButton, TransactionModal, token hooks)? → VeChain Kit
- Do you want a lightweight wallet-only integration with minimal dependencies? → dapp-kit
- Non-React framework? → dapp-kit

### 6. Implement with VeChain-specific correctness

- Network: always explicit (`mainnet`/`testnet`/`solo`)
- Gas: estimate first, use fee delegation where appropriate
- Transactions: use multi-clause when batching benefits atomicity or UX
- Tokens: VET for value, VTHO for gas (dual-token model)
- Social login: Generic Delegator auto-enabled (users pay gas in VET/VTHO/B3TR); app-sponsored delegation optional for better UX; smart accounts; pre-fetch data before `sendTransaction`

### 7. Test

- Unit: Hardhat + Thor Solo
- Integration: Thor Solo with realistic state
- Wallet UX: mocked hook/provider tests

### 8. Verify and deliver

A task is **not complete** until all applicable gates pass:

1. **Code compiles** — no build errors (`npm run build` or equivalent succeeds)
2. **Tests pass** — existing tests still pass; new logic has test coverage
3. **Risk notes documented** — any signing, fee, or token-transfer implications are called out to the user

Then provide:

- Files changed + diffs
- Install/build/test commands
- Risk notes for signing, fees, token transfers

## Reference files

Read the matching files BEFORE doing anything else. See Critical Rules above.

| Topic | File | Read when user mentions... |
|-------|------|---------------------------|
| Frontend patterns (shared) | [references/frontend.md](references/frontend.md) | frontend, React Query, caching, query keys, loading, skeleton, Turborepo, Chakra, i18n, state management |
| VeChain Kit | [references/frontend-vechain-kit.md](references/frontend-vechain-kit.md) | VeChain Kit, useWallet, useSendTransaction, useCallClause, WalletButton, TransactionModal, social login, Privy, smart accounts, account abstraction, theming |
| dapp-kit | [references/frontend-dappkit.md](references/frontend-dappkit.md) | dapp-kit, DAppKitProvider, lightweight wallet |
| Legacy migration | [references/sdk-migration.md](references/sdk-migration.md) | Connex, thor-devkit, migration, deprecated |
| Smart contracts | [references/smart-contracts.md](references/smart-contracts.md) | Solidity, Hardhat, ERC-20, ERC-721, deploy, contract interaction, libraries, contract size, upgradeable |
| Gas optimization | [references/smart-contracts-optimization.md](references/smart-contracts-optimization.md) | gas, optimize, storage packing, assembly, unchecked |
| Testing | [references/testing.md](references/testing.md) | test, Thor Solo, Docker, CI, fixtures |
| ABI / codegen | [references/abi-codegen.md](references/abi-codegen.md) | TypeChain, ABI, typechain-types, code generation |
| Fee delegation | [references/fee-delegation.md](references/fee-delegation.md) | gasless, sponsored, VIP-191, delegator, vechain.energy |
| Multi-clause | [references/multi-clause-transactions.md](references/multi-clause-transactions.md) | batch, multi-clause, atomic, multiple operations |
| Security | [references/security.md](references/security.md) | security, audit, vulnerability, reentrancy, access control |
| VeBetterDAO | [references/vebetterdao.md](references/vebetterdao.md) | X2Earn, B3TR, sustainability, rewards, VeBetterDAO |
| StarGate staking | [references/stargate-staking.md](references/stargate-staking.md) | staking, StarGate, validator, delegation, VTHO rewards, node tier |
| Governance | [references/governance.md](references/governance.md) | VeVote, governance, voting, VOT3, proposal, steering committee |
| Reference links | [references/resources.md](references/resources.md) | docs URL, npm link, GitHub repo |

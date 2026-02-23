# Ziver Chain — Implementation Phases

> **Goal:** Transform Ziver Chain from an architectural blueprint into a fully functional,
> runnable blockchain built on the Zexus language and its built-in blockchain runtime.
>
> **Key Insight from ZEXUS_BLOCKCHAIN.md:** The Zexus interpreter (v1.8.0+) already ships
> with a complete blockchain runtime at `src/zexus/blockchain/` (21 Python modules + Rust core).
> This includes `chain.py`, `node.py`, `consensus.py`, `contract_vm.py`, `crypto.py`,
> `wallet.py`, `tokens.py`, `network.py`, `multichain.py`, `events.py`, `mpt.py`,
> `transaction.py`, `rpc.py`, `ledger.py`, `upgradeable.py`, `verification.py`,
> `accelerator.py`, `monitoring.py`, `storage.py`, `loadtest.py`, and `rust_bridge.py`.
>
> **Strategy:** Rather than reimplementing everything from scratch, we wire the `.zx` source
> files into the Zexus interpreter's built-in `"blockchain"` and `"crypto"` stdlib modules,
> which already provide production-grade implementations behind Python.

---

## Current State Summary

| Aspect | Status |
|---|---|
| Total `.zx` code | ~20,000 lines across 17 files |
| Architecture | Well-structured: core, network, rpc, runtime, contracts |
| Runs? | **No** — fails on first import (`Module not found: ./rpc/server.zx`) |
| Phantom imports | `../database/postgres`, `../middleware/security_middleware`, `../ai/zaie_engine` — none exist |
| Real crypto | Calls to `crypto` stdlib — partially available in Zexus runtime |
| Real networking | None — only data structures, no socket/TLS code |
| Real storage | None — `persistent storage` declarations but no backend |
| Real consensus loop | None — no block production timer or fork choice |
| TX context | Hardcoded `"context.caller"` strings instead of `TX.caller` |
| Events | `print()` statements instead of `emit` / event log |

---

## Phase 0: Foundation — Make It Run (Critical Path)

**Goal:** Get `zx run src/main.zx` to execute without errors.

### 0.1 — Fix Module Import System
- [ ] Audit which `use` import syntax actually works in Zexus 1.8.0 (`use "file.zx"` vs `use { X } from "file.zx"`)
- [ ] Run `zexus check` on each `.zx` file to identify syntax errors
- [ ] Normalize all inter-file imports to a working pattern
- [ ] Create an import test file that validates each module loads

### 0.2 — Remove or Stub Phantom Dependencies
- [ ] Remove all imports of `../database/postgres` (does not exist)
- [ ] Remove all imports of `../middleware/security_middleware` (does not exist)
- [ ] Remove all imports of `../ai/zaie_engine` (does not exist)
- [ ] Replace removed functionality with:
  - Inline stubs for audit/security (simple print-based logging)
  - Built-in Zexus `"crypto"` module for crypto operations
  - Built-in Zexus `"blockchain"` module for chain operations
- [ ] Remove or stub references to `track_memory()`, `cache()`, `throttle()`, `audit()`, `verify()`, `watch` blocks (Zexus-specific features that may not exist in 1.8.0)

### 0.3 — Simplify main.zx Entry Point
- [ ] Create a minimal `main.zx` that imports one module at a time
- [ ] Verify each module loads independently
- [ ] Build up to full node initialization incrementally
- [ ] Get `zx run src/main.zx` to print "Node initialized" successfully

### 0.4 — Establish Test Harness
- [ ] Create `tests/smoke_test.zx` — imports all modules, creates basic objects
- [ ] Create `tests/run_tests.sh` — runs all test files and reports pass/fail
- [ ] Validate with `zx run tests/smoke_test.zx`

**Exit Criteria:** `zx run src/main.zx` executes and prints initialization messages without errors.

---

## Phase 1: Core Blockchain — Blocks, Transactions, State

**Goal:** Create, validate, and chain blocks with real transactions.

### 1.1 — Wire Block Creation to Zexus Built-ins
- [ ] Use `use "crypto" as crypto` for hashing (keccak256, sha256, merkle roots)
- [ ] Use `use "blockchain" as bc` for `bc.create_genesis_block()`, `bc.create_block()`, `bc.hash_block()`
- [ ] Rewrite `block.zx` to use these built-ins instead of phantom imports
- [ ] Validate: create genesis → create block 1 → verify hash chain

### 1.2 — Transaction Lifecycle
- [ ] Implement `Transaction` creation with `bc.create_transaction(from, to, amount)`
- [ ] Wire `TX.caller` into contract runtime (replace hardcoded `"context.caller"`)
- [ ] Implement transaction signing using `crypto.secp256k1_sign()` / `signature()`
- [ ] Implement transaction verification using `crypto.verify_signature()` / `verify_sig()`
- [ ] Implement nonce tracking per account

### 1.3 — State Management (In-Memory)
- [ ] Implement account state: `{ address → { balance, nonce, code_hash } }`
- [ ] Implement balance transfers with double-entry validation
- [ ] Implement state root computation using `bc.calculate_merkle_root()`
- [ ] Implement state snapshot and rollback for block-level atomicity

### 1.4 — Block Validation Pipeline
- [ ] Validate block hash matches content
- [ ] Validate previous_hash links to parent
- [ ] Validate all transactions in block (signatures, nonces, balances)
- [ ] Validate merkle_root matches transaction hashes
- [ ] Validate state_root after applying all transactions

**Exit Criteria:** Can create a chain of 10+ blocks with real signed transactions, verify the chain, and query account balances.

---

## Phase 2: Consensus — Block Production Loop

**Goal:** Automated block production with Proof of Stake validator selection.

### 2.1 — Mempool
- [ ] Implement priority queue for pending transactions (sorted by gas price)
- [ ] Implement duplicate detection (by tx hash)
- [ ] Implement nonce-gap handling
- [ ] Implement configurable max size
- [ ] Optional: Replace-by-Fee (RBF)

### 2.2 — Proof of Stake Consensus
- [ ] Implement validator registration with minimum stake
- [ ] Implement stake-weighted random validator selection (use `crypto` for VRF)
- [ ] Implement block proposal by selected validator
- [ ] Implement block reward distribution
- [ ] Implement slashing for misbehavior (double-signing detection)

### 2.3 — Block Production Loop
- [ ] Implement timed block production (configurable block time, e.g., 2s)
- [ ] Selected validator collects txs from mempool → creates block → signs it
- [ ] Apply block to state
- [ ] Clear confirmed txs from mempool
- [ ] Emit block event

### 2.4 — Fork Choice Rule
- [ ] Implement longest-chain (or heaviest-chain) fork selection
- [ ] Handle receiving a block that extends a non-tip chain
- [ ] Implement chain reorganization (reorg) up to configurable depth

**Exit Criteria:** Node automatically produces blocks at regular intervals, selects validators by stake weight, and handles basic fork scenarios.

---

## Phase 3: Smart Contracts — VM & Execution

**Goal:** Deploy and execute smart contracts with gas metering.

### 3.1 — Contract Deployment
- [ ] Accept contract code (`.zx` source) in a deployment transaction
- [ ] Assign contract address (derived from deployer address + nonce)
- [ ] Store contract code in state
- [ ] Execute constructor (`init` action) on deployment
- [ ] Return deployment receipt

### 3.2 — Contract Execution Environment
- [ ] Route `contract_call` transactions to the correct contract
- [ ] Inject execution context: `TX.caller`, `TX.timestamp`, `TX.block_hash`, `TX.gas_limit`
- [ ] Implement `this` reference for contract self-reference
- [ ] Implement `persistent storage` reads/writes against contract state
- [ ] Implement cross-contract calls (`contract_call`, `static_call`, `delegate_call`)

### 3.3 — Gas Metering
- [ ] Implement gas cost model (matching ZEXUS_BLOCKCHAIN.md Section 12):
  - read: 800, write: 20000, transfer: 2100, call: 700, create: 32000, hash: 30, event: 375
- [ ] Track gas consumption per operation
- [ ] Revert execution when gas limit exceeded
- [ ] Calculate gas refund for unused gas
- [ ] Implement `gas.used`, `gas.remaining`, `gas.limit` properties

### 3.4 — Event System
- [ ] Implement `emit EventName(args...)` that writes to an event log (not just print)
- [ ] Store events per block with: address, topics, data, block_number, tx_hash, log_index
- [ ] Implement Bloom filter for efficient event topic lookups
- [ ] Implement event querying by block range, address, and topic

### 3.5 — Security Features
- [ ] Implement reentrancy guard (block recursive calls to same contract)
- [ ] Implement call depth limit (max 10)
- [ ] Implement `require(condition, message)` that reverts on failure
- [ ] Implement `revert(message)` for explicit transaction revert
- [ ] Implement state rollback on any revert

**Exit Criteria:** Can deploy a ZRC-20 token contract, transfer tokens, check balances, emit Transfer events, and query events — all with gas metering and reentrancy protection.

---

## Phase 4: Persistence — Storage Backend

**Goal:** Blockchain state survives node restarts.

### 4.1 — File-Based Block Storage
- [ ] Serialize blocks to JSON/binary files in `chain_data/`
- [ ] Implement block index: `height → file_offset` or `height → filename`
- [ ] Load chain from disk on node startup
- [ ] Append new blocks to disk on creation

### 4.2 — State Persistence
- [ ] Serialize account state to disk (JSON or binary)
- [ ] Implement write-ahead log (WAL) for crash recovery
- [ ] Implement periodic state snapshots
- [ ] Load latest state on startup, replay blocks since last snapshot if needed

### 4.3 — SQLite Backend (Optional Upgrade)
- [ ] If Zexus supports file I/O or Python interop, use SQLite for:
  - Block storage (indexed by height and hash)
  - Transaction index (by hash)
  - Account state
  - Event log
- [ ] Implement migration scripts for schema changes

### 4.4 — Merkle Patricia Trie (Optional Upgrade)
- [ ] Implement basic MPT for state storage (or use Zexus runtime's `StateTrie` if accessible)
- [ ] Generate inclusion proofs for account state
- [ ] Verify proofs for light client support

**Exit Criteria:** Stop the node, restart it, and the chain continues from where it left off with all balances and contracts intact.

---

## Phase 5: Networking — P2P Communication

**Goal:** Multiple nodes discover each other, sync blocks, and propagate transactions.

### 5.1 — TCP Peer Communication
- [ ] Implement basic TCP server/client in Zexus (or via Zexus runtime's network module)
- [ ] Define wire protocol: message framing, type headers, JSON payload
- [ ] Implement handshake: exchange node_id, chain_id, best block height
- [ ] Implement ping/pong keepalive

### 5.2 — Peer Discovery
- [ ] Implement bootstrap node list (hardcoded seed peers)
- [ ] Implement peer exchange: ask connected peers for their peer lists
- [ ] Implement max peer limit
- [ ] Implement peer scoring and ban list

### 5.3 — Block Propagation
- [ ] Broadcast new blocks to all connected peers
- [ ] Validate received blocks before adding to chain
- [ ] Request missing blocks (block sync protocol)
- [ ] Handle chain sync on initial connection (download missing blocks)

### 5.4 — Transaction Propagation
- [ ] Broadcast new transactions to peers
- [ ] Deduplicate incoming transactions
- [ ] Validate incoming transactions before adding to mempool

### 5.5 — TLS Encryption (Optional Upgrade)
- [ ] Implement TLS handshake for peer connections
- [ ] Support certificate pinning
- [ ] Support mutual TLS (mTLS)

**Exit Criteria:** Two nodes on different ports discover each other, sync their chains, and propagate new transactions and blocks in real-time.

---

## Phase 6: RPC API — External Interface

**Goal:** External clients can interact with the blockchain via JSON-RPC.

### 6.1 — HTTP JSON-RPC Server
- [ ] Implement HTTP server listening on configurable port (default 8545)
- [ ] Implement JSON-RPC 2.0 request/response parsing
- [ ] Implement core methods:
  - `zx_chainId`, `zx_blockNumber`, `zx_getBlockByNumber`, `zx_getBlockByHash`
  - `zx_getBalance`, `zx_getTransactionCount` (nonce)
  - `zx_sendTransaction`, `zx_getTransactionByHash`, `zx_getTransactionReceipt`
  - `zx_gasPrice`, `zx_estimateGas`
  - `zx_getCode`, `zx_getLogs`
- [ ] Implement error codes per ZEXUS_BLOCKCHAIN.md Section 13

### 6.2 — Contract Interaction Methods
- [ ] `contract_deploy` — deploy a contract via RPC
- [ ] `contract_call` — execute a contract action
- [ ] `contract_staticCall` — read-only contract call

### 6.3 — Mining/Admin Methods
- [ ] `miner_start`, `miner_stop`, `miner_status`
- [ ] `admin_nodeInfo`, `admin_fundAccount` (testnet faucet)
- [ ] `txpool_status`, `txpool_content`

### 6.4 — WebSocket Subscriptions (Optional Upgrade)
- [ ] Implement WebSocket server on separate port (default 8546)
- [ ] Implement `subscribe` for new blocks, new transactions, logs
- [ ] Implement `unsubscribe`

**Exit Criteria:** Can use `curl` to send JSON-RPC requests to the node, submit transactions, query balances, deploy contracts, and get block info.

---

## Phase 7: Token Standards & DeFi Contracts

**Goal:** Production-ready token contracts and DeFi primitives.

### 7.1 — ZRC-20 Fungible Token
- [ ] Rewrite `token_standard.zx` using `TX.caller` (not hardcoded strings)
- [ ] Implement: `transfer`, `transferFrom`, `approve`, `allowance`, `balanceOf`
- [ ] Implement: `mint` (owner-only), `burn`
- [ ] Implement: `totalSupply`, `name`, `symbol`, `decimals`
- [ ] Emit proper `Transfer` and `Approval` events
- [ ] Add pausable pattern
- [ ] Write comprehensive tests

### 7.2 — ZRC-721 Non-Fungible Token
- [ ] Implement per ZEXUS_BLOCKCHAIN.md Section 4
- [ ] `mint`, `burn`, `transferFrom`, `approve`, `setApprovalForAll`
- [ ] `ownerOf`, `tokenURI`, `tokensOfOwner`
- [ ] Emit `Transfer`, `Approval`, `ApprovalForAll` events

### 7.3 — ZRC-1155 Multi-Token
- [ ] Implement per ZEXUS_BLOCKCHAIN.md Section 4
- [ ] Batch mint, batch transfer, batch balance queries

### 7.4 — DeFi Contracts
- [ ] Staking Pool (per Example 8 in ZEXUS_BLOCKCHAIN.md)
- [ ] Simple AMM DEX (per Example 2 in ZEXUS_BLOCKCHAIN.md)
- [ ] DAO Governance (per Example 4 / Section 14)

### 7.5 — Wallet Contract
- [ ] Rewrite `wallet.zx` with real crypto (not stubs)
- [ ] Multi-chain balance tracking
- [ ] Guardian-based social recovery
- [ ] Token portfolio view

**Exit Criteria:** Deploy ZRC-20 token, transfer between accounts, check balances via RPC, stake tokens, swap on DEX, vote on governance proposals — all on-chain with proper events.

---

## Phase 8: Cross-Chain Bridge

**Goal:** Bridge assets between Ziver Chain and simulated external chains.

### 8.1 — Lock-and-Mint Pattern
- [ ] Implement escrow contract on source chain
- [ ] Implement mint contract on destination chain
- [ ] Implement relay that watches source chain events and triggers minting
- [ ] Implement Merkle proof verification for cross-chain messages

### 8.2 — Burn-and-Release Pattern
- [ ] Implement burn on destination chain
- [ ] Implement release from escrow on source chain
- [ ] Implement relay for reverse direction

### 8.3 — Multi-Chain Router
- [ ] Implement ChainRouter that routes messages between registered chains
- [ ] Support Ziver ↔ simulated Ethereum, BSC, TON chains
- [ ] Fee calculation per chain

**Exit Criteria:** Bridge 100 tokens from Chain A to Chain B, verify balances on both chains, bridge back, verify again.

---

## Phase 9: Security & Verification

**Goal:** Formal verification, reentrancy protection, and audit tooling.

### 9.1 — Contract Verification
- [ ] Structural verification: detect missing access control, reentrancy risks
- [ ] Invariant checking: user-defined invariants preserved across all actions
- [ ] Integrate with `zexus check` or custom verification pass

### 9.2 — Taint Analysis
- [ ] Track user-controlled data flow through contracts
- [ ] Flag: user input → owner/admin variable (privilege escalation)
- [ ] Flag: tainted data → transfer/send without validation

### 9.3 — Security Hardening
- [ ] Rate limiting on RPC endpoints
- [ ] Peer reputation scoring with automatic bans
- [ ] Transaction replay protection (chain_id in tx hash)
- [ ] Double-spend detection

**Exit Criteria:** Verification tool catches known vulnerability patterns in test contracts, and the node rejects malformed/replayed transactions.

---

## Phase 10: Production Hardening

**Goal:** Production-ready deployment with monitoring and performance.

### 10.1 — Performance Optimization
- [ ] Profile block production and optimize hot paths
- [ ] Enable Rust core if available (`maturin develop --release`)
- [ ] Implement transaction batching for parallel execution
- [ ] Target: 1,800+ TPS sustained

### 10.2 — Monitoring
- [ ] Implement metrics collection: block height, TPS, peer count, mempool size
- [ ] Expose `/metrics` endpoint (Prometheus format)
- [ ] Expose `/health` endpoint
- [ ] Log structured events for observability

### 10.3 — Configuration
- [ ] Config file support (JSON/TOML) for:
  - Network settings (port, max peers, bootstrap nodes)
  - Consensus parameters (block time, min stake, rewards)
  - RPC settings (port, rate limits, CORS)
  - Storage backend selection
- [ ] Command-line argument parsing
- [ ] Environment variable overrides

### 10.4 — Documentation
- [ ] Update README.md with accurate setup instructions
- [ ] Write operator guide (how to run a node)
- [ ] Write developer guide (how to write and deploy contracts)
- [ ] API reference for all RPC methods
- [ ] Network architecture diagram

**Exit Criteria:** A fully documented, configurable node that starts from config file, produces blocks, serves RPC, reports metrics, and handles graceful shutdown.

---

## Phase Dependency Graph

```
Phase 0 (Foundation)
   │
   ▼
Phase 1 (Core Blockchain) ──────────────────┐
   │                                         │
   ▼                                         ▼
Phase 2 (Consensus)                   Phase 3 (Smart Contracts)
   │                                         │
   ├─────────────┬───────────────────────────┘
   ▼             ▼
Phase 4       Phase 5          Phase 6
(Storage)    (Networking)      (RPC API)
   │             │                │
   └─────────────┴────────────────┘
                  │
                  ▼
           Phase 7 (Tokens & DeFi)
                  │
                  ▼
           Phase 8 (Cross-Chain Bridge)
                  │
                  ▼
           Phase 9 (Security & Verification)
                  │
                  ▼
           Phase 10 (Production Hardening)
```

**Phases 1–3** can be worked on in parallel after Phase 0.
**Phases 4–6** can be worked on in parallel after Phases 1–3.
**Phases 7–10** are sequential, building on all prior phases.

---

## Estimated Effort

| Phase | Effort | Priority |
|---|---|---|
| Phase 0: Foundation | 1–2 days | **P0 — CRITICAL** |
| Phase 1: Core Blockchain | 3–5 days | **P0 — CRITICAL** |
| Phase 2: Consensus | 3–5 days | **P0 — CRITICAL** |
| Phase 3: Smart Contracts | 5–7 days | **P0 — CRITICAL** |
| Phase 4: Persistence | 2–3 days | **P1 — HIGH** |
| Phase 5: Networking | 5–7 days | **P1 — HIGH** |
| Phase 6: RPC API | 3–5 days | **P1 — HIGH** |
| Phase 7: Tokens & DeFi | 5–7 days | **P2 — MEDIUM** |
| Phase 8: Cross-Chain | 3–5 days | **P2 — MEDIUM** |
| Phase 9: Security | 3–5 days | **P2 — MEDIUM** |
| Phase 10: Production | 3–5 days | **P3 — LOW** |
| **Total** | **~36–56 days** | |

---

## Key References

- [ZEXUS_BLOCKCHAIN.md](docs/reference/ZEXUS_BLOCKCHAIN.md) — Complete API reference for all 21 blockchain modules
- [Zexus Interpreter](https://github.com/Zaidux/zexus-interpreter) — The Zexus language runtime
- Built-in modules: `use "crypto" as crypto`, `use "blockchain" as bc`
- Built-in globals: `hash()`, `keccak256()`, `generateKeypair()`, `signature()`, `verify_sig()`, `deriveAddress()`, `randomBytes()`
- Runtime Python modules: `chain.py`, `node.py`, `consensus.py`, `contract_vm.py`, `crypto.py`, `wallet.py`, `tokens.py`, `network.py`, `multichain.py`, `events.py`, `mpt.py`, `transaction.py`, `rpc.py`, `ledger.py`, `upgradeable.py`, `verification.py`, `accelerator.py`, `monitoring.py`, `storage.py`

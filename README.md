# Besu Hyperledger Network

<div align="left">
    <img src="https://img.shields.io/badge/Besu-24.4.1-blue?style=for-the-badge&logo=hyperledger" alt="Besu" />
    <img src="https://img.shields.io/badge/Consensus-IBFT_2.0-green?style=for-the-badge" alt="Consensus" />
    <img src="https://img.shields.io/badge/Ethereum-Fork_Latest-3C3C3D?style=for-the-badge&logo=ethereum" alt="Ethereum" />
    <img src="https://img.shields.io/badge/Network-Permissioned-orange?style=for-the-badge" alt="Network" />
    <img src="https://img.shields.io/badge/Gas-Free_Network-lightgrey?style=for-the-badge" alt="Gas" />
</div>

<p align="left">
    <i>A local permissioned Ethereum blockchain implementation using the IBFT 2.0 consensus protocol and Besu's native permissioning system.</i>
</p>

## Network Topology

The network consists of multiple nodes running locally, each configured with specific **P2P** and **RPC** ports to simulate a distributed environment on a single host.

| Node       | Name          | P2P Port | RPC Port | Initial Role         |
| ---------- | ------------- | -------- | -------- | -------------------- |
| **Node 1** | alumno-nodo-1 | 7001     | 8001     | Bootnode / Validator |
| **Node 2** | alumno-nodo-2 | 7002     | 8002     | Validator            |
| **Node 3** | alumno-nodo-3 | 7003     | 8003     | Validator            |
| **Node 4** | alumno-nodo-4 | 7004     | 8004     | Dynamic Validator    |

## Consensus Model

This network utilizes **IBFT 2.0** (Istanbul Byzantine Fault Tolerance), a Proof of Authority (PoA) consensus mechanism designed for private and permissioned networks.

| Parameter           | Value      | Description                                         |
| ------------------- | ---------- | --------------------------------------------------- |
| **Block Period**    | 60s        | The minimum time interval between block production. |
| **Epoch Length**    | 100 blocks | Number of blocks between validator set updates.     |
| **Request Timeout** | 120s       | Time limit for consensus round completion.          |
| **Chain ID**        | 1234       | Unique identifier for the local blockchain.         |

## System Architecture

| Component                 | Role                                                                    |
| ------------------------- | ----------------------------------------------------------------------- |
| **Bootnode**              | Node 1 serves as the discovery point for all other peers.               |
| **Validator Nodes**       | Nodes 1, 2, and 3 participate in the initial block validation.          |
| **RPC Interface**         | HTTP JSON-RPC endpoint for management and chain interaction.            |
| **Permissioning Manager** | Handles node-level access control via local configuration and PERM API. |

## Technology Stack

- **Blockchain Platform**: Hyperledger Besu 24.4.1 (or latest)
- **Consensus Protocol**: IBFT 2.0
- **Development Tooling**: Python (for key derivation), Curl (for RPC interaction)
- **Environment**: Local Windows / PowerShell

## Key Features

1. **Permissioned Access** — Restricts network participation to authorized nodes only via `permissions_config.toml`.
2. **Zero Gas Economy** — Configured as a free gas network (`min-gas-price=0`) for internal enterprise use cases.
3. **Dynamic Governance** — Allows adding or removing validators and white-listing nodes in real-time using `IBFT` and `PERM` APIs.
4. **Pre-funded Genesis** — Includes pre-configured accounts with 100 ETH each for immediate deployment testing.

## Testing Strategy

The network is verified through direct **JSON-RPC** queries to monitor node health and consensus state. Manual testing involves triggering the `admin_peers` and `ibft_getSignerMetrics` methods to ensure all nodes are synchronized and actively signing blocks.

## Project Setup

1. **Start Initial Network Nodes**:

   Open three terminals and run the following commands sequentially:

   ```bash
   # Terminal 1
   besu --config-file=nodo-1/config.toml

   # Terminal 2
   besu --config-file=nodo-2/config.toml

   # Terminal 3
   besu --config-file=nodo-3/config.toml
   ```

2. **Verify Consensus and Peers**:

   Execute RPC calls to confirm network status:

   ```bash
   # Check Peers
   curl -X POST --data '{"jsonrpc":"2.0","method":"admin_peers","params":[],"id":1}' http://localhost:8001

   # Check Validator Metrics
   curl -X POST --data '{"jsonrpc":"2.0","method":"ibft_getSignerMetrics","params":[],"id":1}' http://localhost:8001
   ```

3. **Expand Network (Node 4)**:

   Start the fourth node and propose it as a validator:

   ```bash
   besu --config-file=nodo-4/config.toml

   # Vote from existing validators
   curl -X POST --data '{"jsonrpc":"2.0","method":"ibft_proposeValidatorVote","params":["0x814C7a105918F34B453BAAfa3b93C45Ff489Ad09", true],"id":1}' http://localhost:8001
   ```

4. **Dynamic Permissioning**:

   Add Node 3 to the allowlist while the network is running:

   ```bash
   curl -X POST --data '{"jsonrpc":"2.0","method":"perm_addNodesToAllowlist","params":[["enode://..."]],"id":1}' http://localhost:8001
   ```

## Deliverables

This project requires the following evidence for submission:

### Network Operation Screenshots

- [ ] **Node 1 Startup** — Capture terminal showing successful boot.
- [ ] **Network Sync** — Capture terminal showing block production.
- [ ] **Validator Metrics** — Result of `ibft_getSignerMetrics`.

### RPC Verification

| Query                     | Expected Result                                        |
| ------------------------- | ------------------------------------------------------ |
| **admin_peers**           | List containing at least 2 peers for Node 1.           |
| **ibft_getSignerMetrics** | Proof that all 3 (or 4) validators are signing blocks. |

---

Built for Hyperledger Besu.

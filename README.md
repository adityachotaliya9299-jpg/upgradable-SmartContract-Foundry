# 📦 Upgradeable Smart Contract — Foundry (UUPS)

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Solidity](https://img.shields.io/badge/Solidity-0.8.19-363636?logo=solidity)](https://soliditylang.org/)
[![Foundry](https://img.shields.io/badge/Built%20with-Foundry-FFDB1C?logo=ethereum)](https://book.getfoundry.sh/)
[![OpenZeppelin](https://img.shields.io/badge/OpenZeppelin-Upgradeable-4E5EE4?logo=openzeppelin)](https://docs.openzeppelin.com/contracts/upgradeable)
[![UUPS](https://img.shields.io/badge/Pattern-UUPS%20Proxy-blue)](https://eips.ethereum.org/EIPS/eip-1822)

> A minimal, production-style implementation of an **upgradeable smart contract** using the **UUPS proxy pattern** with OpenZeppelin and Foundry.

---

## 📸 Architecture Overview

```
             ┌─────────────────────────────────┐
             │        User / Frontend          │
             └────────────────┬────────────────┘
                              │ calls
                              ▼
             ┌─────────────────────────────────┐
             │     ERC1967Proxy  (Stable)      │
             │   📌 Address never changes      │
             └────────┬──────────────┬─────────┘
                      │              │
            delegate  │              │ upgrade (owner only)
                      ▼              ▼
        ┌─────────────────┐   ┌─────────────────┐
        │    BoxV1.sol    │   │    BoxV2.sol    │
        │  version() = 1  │   │  version() = 2  │
        │  getValue()     │   │  getValue()     │
        │                 │   │  setValue() ✅  │
        └─────────────────┘   └─────────────────┘
```

---

## ✨ What It Does

This project shows how to deploy a proxy pointing to **BoxV1**, then seamlessly **upgrade it to BoxV2** — without changing the proxy address and without losing on-chain state.

| Version | Features |
|---------|----------|
| **BoxV1** | `getValue()`, `version()` returns `1` — read only |
| **BoxV2** | Adds `setValue()`, `version()` returns `2` — read + write |

> 🔐 Only the **contract owner** can authorize upgrades via `_authorizeUpgrade()`.

---

## 🗂️ Project Structure

```
upgradeable-smart-contract/
│
├── src/
│   ├── BoxV1.sol                    # V1 — read-only implementation
│   └── BoxV2.sol                    # V2 — adds setValue()
│
├── script/
│   ├── DeployBox.s.sol              # Deploys BoxV1 behind ERC1967Proxy
│   └── UpgradeBox.s.sol             # Upgrades proxy to BoxV2
│
├── test/
│   └── DeployAndUpgradeTest.t.sol   # Full deploy + upgrade test suite
│
├── lib/                             # Foundry dependencies
└── foundry.toml                     # Foundry config
```

---

## 🔧 Tech Stack

| Tool | Purpose |
|------|---------|
| [Foundry](https://book.getfoundry.sh/) | Build, test, deploy |
| [OpenZeppelin Upgradeable](https://docs.openzeppelin.com/contracts/upgradeable) | UUPS proxy, Ownable, Initializable |
| [foundry-devops](https://github.com/Cyfrin/foundry-devops) | Fetch most recent deployment for upgrade |
| Solidity `^0.8.19` | Smart contract language |

---

## 🚀 Getting Started

### 1. Clone the repo

```bash
git clone https://github.com/YOUR_USERNAME/upgradeable-smart-contract.git
cd upgradeable-smart-contract
```

### 2. Install Foundry

```bash
curl -L https://foundry.paradigm.xyz | bash
foundryup
```

### 3. Install dependencies

```bash
forge install OpenZeppelin/openzeppelin-contracts-upgradeable --no-commit
forge install OpenZeppelin/openzeppelin-contracts --no-commit
forge install Cyfrin/foundry-devops --no-commit
```

### 4. Build

```bash
forge build
```

---

## 🧪 Run Tests

```bash
forge test -v
```

| Test | Description |
|------|-------------|
| `testBoxWorks` | Proxy correctly points to V1 (`version() == 1`) |
| `testDeploymentIsV1` | `setValue()` reverts on V1 (function doesn't exist yet) |
| `testUpgradeWorks` | Upgrades to V2, confirms `version() == 2` and `setValue()` works |

---

## 📜 Deployment

### Local (Anvil)

```bash
# Terminal 1 — start local node
anvil

# Terminal 2 — deploy BoxV1 behind proxy
forge script script/DeployBox.s.sol --rpc-url http://localhost:8545 --broadcast

# Upgrade proxy to BoxV2
forge script script/UpgradeBox.s.sol --rpc-url http://localhost:8545 --broadcast
```

### Testnet (e.g. Sepolia)

```bash
forge script script/DeployBox.s.sol \
  --rpc-url $SEPOLIA_RPC_URL \
  --private-key $PRIVATE_KEY \
  --broadcast \
  --verify \
  --etherscan-api-key $ETHERSCAN_API_KEY
```

---

## 🔐 Security — Upgrade Authorization

```solidity
function _authorizeUpgrade(address newImplementation)
    internal
    override
    onlyOwner   // 👈 Only the owner can upgrade
{}
```

- Constructors are **disabled** via `_disableInitializers()` to prevent re-initialization attacks.
- State is initialized via `initialize()` using OpenZeppelin's `Initializable` pattern.
- Proxy storage follows **ERC1967** storage slots to avoid collisions.

---

## 📚 Learn More

- 📖 [UUPS Proxy Pattern — EIP-1822](https://eips.ethereum.org/EIPS/eip-1822)
- 📖 [OpenZeppelin Upgrades Docs](https://docs.openzeppelin.com/contracts/upgradeable)
- 📖 [Foundry Book](https://book.getfoundry.sh/)
- 🎓 [Cyfrin Updraft — Smart Contract Course](https://updraft.cyfrin.io/)

---

## 📄 License

This project is licensed under the **MIT License** — see below for details.

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

```
MIT License

Copyright (c) 2024 ADITYA_CHOTALIYA

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT.
```

---

Made with ❤️ using Foundry & OpenZeppelin

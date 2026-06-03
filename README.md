# Foundry Account Abstraction

A minimal ERC-4337 (Account Abstraction) smart account implementation built with Foundry.

## Overview

This project implements a minimal smart contract account that conforms to the [ERC-4337](https://eips.ethereum.org/EIPS/eip-4337) standard. It allows an owner-controlled smart contract wallet to validate and execute user operations through the EntryPoint contract.

### Goals

1. Create a basic AA account on Ethereum
2. Create a basic AA account on zkSync
3. Deploy and send a userOp/transaction through them

## Contracts

### `MinimalAccount.sol` (Ethereum)

A minimal ERC-4337 compliant smart account that supports:

- **ECDSA Signature Validation** -- Validates user operations using EIP-191 signed messages, ensuring only the account owner can authorize transactions
- **EntryPoint Integration** -- Restricts `validateUserOp` to be callable only by the designated EntryPoint contract
- **Arbitrary Execution** -- Executes arbitrary calls via `execute()`, restricted to the EntryPoint or the account owner
- **Prefund Payment** -- Automatically pays the required prefund to the EntryPoint during validation

### `ZkMinimalAccount.sol` (zkSync)

A minimal smart account for zkSync Era using native account abstraction (Type 113 / 0x71 transactions):

- **Native AA** -- Implements zkSync's `IAccount` interface directly, no EntryPoint needed
- **Bootloader Integration** -- Transaction validation and execution are gated to the zkSync bootloader system contract
- **Nonce Management** -- Increments nonce via the `NonceHolder` system contract
- **System Contract Calls** -- Supports deployer system contract interactions for contract deployment
- **External Execution** -- Allows anyone to submit pre-signed transactions via `executeTransactionFromOutside`

## Project Structure

```
.
├── src/
│   ├── ethereum/
│   │   └── MinimalAccount.sol           # ERC-4337 account (Ethereum)
│   └── zksync/
│       └── ZkMinimalAccount.sol         # Native AA account (zkSync)
├── script/
│   ├── DeployMinimal.s.sol              # Deployment script
│   ├── HelperConfig.s.sol               # Network configuration (Mainnet, Sepolia, Arbitrum, zkSync, Anvil)
│   └── SendPackedUserOp.s.sol           # Script to send packed user operations
├── test/
│   ├── ethereum/
│   │   └── MinimalAccountTest.t.sol     # Ethereum account tests
│   └── zksync/
│       └── ZkMinimalAccountTest.t.sol   # zkSync account tests
├── Makefile                             # Build, test, and deploy commands
└── foundry.toml                         # Foundry configuration
```

## Dependencies

- [forge-std](https://github.com/foundry-rs/forge-std) -- Foundry standard library
- [account-abstraction](https://github.com/eth-infinitism/account-abstraction) -- ERC-4337 EntryPoint and interfaces
- [openzeppelin-contracts](https://github.com/OpenZeppelin/openzeppelin-contracts) -- Ownable, ECDSA, MessageHashUtils
- [foundry-devops](https://github.com/Cyfrin/foundry-devops) -- Deployment utilities
- [foundry-era-contracts](https://github.com/Cyfrin/foundry-era-contracts) -- zkSync Era system contracts

## Getting Started

### Prerequisites

- [Foundry](https://book.getfoundry.sh/getting-started/installation)

### Install

```bash
git clone https://github.com/manu-cadena/foundry-account-abstraction.git
cd foundry-account-abstraction
forge install
```

### Build

```bash
forge build
```

### Test

```bash
forge test
```

### Deploy (Local Anvil)

```bash
anvil
forge script script/DeployMinimal.s.sol --rpc-url http://localhost:8545 --broadcast
```

## Supported Networks

| Network         | EntryPoint                                   |
| --------------- | -------------------------------------------- |
| Ethereum Mainnet | `0x0000000071727De22E5E9d8BAf0edAc6f37da032` |
| Ethereum Sepolia | `0x5FF137D4b0FDCD49DcA30c7CF57E578a026d2789` |
| Arbitrum Mainnet | `0x0000000071727De22E5E9d8BAf0edAc6f37da032` |
| zkSync           | Native AA (no EntryPoint needed)              |
| Local (Anvil)    | Deployed automatically via HelperConfig       |

## Makefile Commands

```bash
make build        # Compile contracts
make test         # Run tests
make testFork     # Run tests with mainnet fork
make format       # Format code
make anvil        # Start local Anvil node
make deployEth    # Deploy to Arbitrum
make sendUserOp   # Send a packed user operation
make zkbuild      # Build for zkSync
make zktest       # Run zkSync tests
```

## License

MIT

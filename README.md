# EnterSoft
Working on learning smart contract auditing skills using Algorand PyTeal

For more information on using smart contracts with the SDKs see the Interacting with smart contracts documentation.
https://developer.algorand.org/docs/get-details/dapps/pyteal/

Algorand Smart Contracts (ASC1) are small programs that serve various functions on the blockchain and operate on layer-1. Smart contracts are separated into two main categories, smart contracts, and smart signatures. These types are also referred to as stateful and stateless contracts respectively. The type of contract that is written will determine when and how the logic of the program is evaluated. See the following sections to understand how each type of contract is used on the Algorand blockchain. Both types of contracts are written in the Transaction Execution Approval Language (TEAL), which is an assembly-like language that is interpreted by the Algorand Virtual Machine (AVM) running within an Algorand node. TEAL programs can be written by hand or by using the Python language with the PyTEAL compiler.

#Smart Contracts
Smart contracts are contracts that, once deployed, are remotely callable from any node in the Algorand blockchain. Once deployed, the on-chain instantiation of the contract is referred to as an Application and assigned an Application Id. These applications are triggered by a specific type of transaction called an Application Call transaction. These on-chain applications handle the primary decentralized logic of a dApp.

Applications can modify state associated with the application (global state) or a per application+account (local state) basis.

Applications can access on-chain values, such as account balances, asset configuration parameters, or the latest block time.

Applications can execute transactions as part of the execution of the logic. One type of transaction they can perform, as of AVM 1.1, is an Application Call transaction which allows one application to call another. This ability to call other applications enables composability between applications.

Applications have an associated Application Account that can hold Algos or ASAs balances and can be used as on-chain escrow accounts.

To provide a standard method for exposing an API and encoding/decoding data types from application call transactions, the ABI should be used.

Check out their website for more information: 
(https://entersoftsecurity.com/) Entersoft Security

## Usage

Use the **sandbox** command to interact with the Algorand Sandbox.

```plain
sandbox commands:
  up    [config]  -> start the sandbox environment.
  down            -> tear down the sandbox environment.
  reset           -> reset the containers to their initial state.
  clean           -> stops and deletes containers and data directory.
  test            -> runs some tests to demonstrate usage.
  enter [algod||indexer||indexer-db]
                  -> enter the sandbox container.
  version         -> print binary versions.
  copyTo <file>   -> copy <file> into the algod container. Useful for offline transactions & LogicSigs plus TEAL work.
  copyFrom <file> -> copy <file> from the algod container. Useful for offline transactions & LogicSigs plus TEAL work.

algorand commands:
  logs            -> stream algorand logs with the carpenter utility.
  status          -> get node status.
  goal (args)     -> run goal command like 'goal node status'.
  tealdbg (args)  -> run tealdbg command to debug program execution.

special flags for 'up' command:
  -v|--verbose           -> display verbose output when starting standbox.
  -s|--skip-fast-catchup -> skip catchup when connecting to real network.
  -i|--interactive       -> start docker-compose in interactive mode.
```

Sandbox creates the following API endpoints:

- `algod`:
  - address: `http://localhost:4001`
  - token: `aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa`
- `kmd`:
  - address: `http://localhost:4002`
  - token: `aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa`
- `indexer`:
  - address: `http://localhost:8980`

## Getting Started

### Ubuntu and macOS

Make sure the docker daemon is running and docker-compose is installed.

Open a terminal and run:

```bash
git clone https://github.com/algorand/sandbox.git
```

In whatever local directory the sandbox should reside. Then:

```bash
cd sandbox
./sandbox up
```

This will run the `sandbox` shell script with the default configuration. See the [Basic Configuration](#basic-configuration) for other options.

<!-- markdownlint-disable-file MD034 -->

Note for Ubuntu: You may need to alias `docker` to `sudo docker` or follow the steps in https://docs.docker.com/install/linux/linux-postinstall so that a non-root user can use the command `docker`.

Run the test command for examples of how to interact with the environment:

```bash
./sandbox test
```

### Windows

Note: Be sure to use the latest version of Windows 10. Older versions may not work properly.

Note: While installing the following programs, several restarts may be required for windows to recognize the new software correctly.

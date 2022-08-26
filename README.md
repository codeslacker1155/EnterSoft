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

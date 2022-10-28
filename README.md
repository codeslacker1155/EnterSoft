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


### Install Dependencies
1. Install [Git](https://github.com/git-guides/install-git)
2. Install [Docker Desktop](https://www.docker.com/products/docker-desktop)
3. Install [Algorand sandbox](https://github.com/algorand/sandbox)
4. Copy this project without cloning:
   ```bash
   git clone -b main --depth 1 --single-branch git@github.com:manustays/algorand-pyteal-smart-contract-starter-template.git && rm -rf algorand-pyteal-smart-contract-starter-template/.git/
   ```


### Setup the Sandbox for Local Testing
1. Start Docker Desktop and goto the Sandbox folder
2. Add your project folder as bind volume in the Sandbox Docker image:
   - In the Sandbox root folder, edit the file `docker-compose.yml` and add the following lines under the key `services.algod`:
      ```yml
      volumes:
        - type: bind
          source: <path to this project folder>
          target: /data
      ```
3. Start the Sandbox docker container with the command: `./sandbox up -v`
4. **Other useful Sandbox commands** (after starting it):
    1. Shut down the Sandbox: `./sandbox down`
    2. Get a list of test wallets/accounts: `./sandbox goal account list`
    3. Check the balance of an account (app account or wallet): `./sandbox goal balance --address <account-address>`
    4. Reset Sandbox (including the test accounts): `./sandbox reset`


### Setup your Project
1. Goto project folder & setup Python virtual env for the project _(one time only)_: `python3 -m venv venv`
2. Activate the Python virtual environment _(everytime you start this project)_:
    ```bash
    $ ./venv/Scripts/activate # Windows
    $ ./venv/bin/activate # Linux/Mac
    ```
3. Check if virtual env is setup properly: `pip -V`
   - It should show the path to Python executable under your project's venv folder.
4. Install the python dependencies _(one time only)_: **`pip3 install -r requirements.txt`**


## Compile & Run
1. **Within the project folder:**
   1. Activate the Python virtual environment _(if not already done)_: `. venv/bin/activate`
   2. Build source files: `./build.sh contracts.<subfolder>.<filename-without-py-ext>`
2. **Within the Sandbox folder:**
   1. Enter the docker container terminal: `./sandbox enter algod`
   2. Check if project’s bound volume is working: `ls /data`
   3. Get test wallet accounts: `goal account list`
      ![Example](/img/1.png)
      - Copy account address in a variable for easy access: `WALLET1=F74DX......`
   4. Deploy contract within sandbox:
      ```bash
      goal app create --creator $WALLET1 --approval-prog /data/build/approval.teal --clear-prog /data/build/clear.teal --global-byteslices 1 --global-ints 3 --local-byteslices 0 --local-ints 0
      ```
      1. It returns a numeric app ID if successfully deployed (eg: 1)
      2. Store in a variable for easy access: `APPID=1`
   5. Get deployed app info: `goal app info --app-id $APPID`
       1. It returns application account, creator, approval hash, clear hash, etc.
       ![Example](/img/2.png)

   6. Read smart contract storage: `goal app read --global --app-id $APPID --guess-format`
       1. Returns a formatted JSON with all current global values
       ![Example](/img/3.png)

   7. Maintain minimum balance (0.1 algo or 100,000 microalgo) in the smart contract account:
       ```bash
       goal clerk send -f $WALLET1 -t $APP_ACCOUNT -a 100000
       ```
       1. [Algorand PyTeal Course | Transaction Fee Pooling and Minimum Balances - YouTube](https://www.youtube.com/watch?v=k3K9_UNlsFY&list=PLpAdAjL5F75CNnmGbz9Dm_k-z5I6Sv9_x&index=12)
   8. Call a Smart Contract Operation:
      ```bash
      goal app call --app-id $APPID --from $WALLET1 --app-arg "str:inc"
      ```
      1. Here, “inc” is the operation name as defined in the smart contract
      2. Check updated values *(counter incremented to one)*:
         ![Example](/img/4.png)

## Debug Smart Contract

Sandbox comes with the `tealdbg` tool for debugging. It requires a transaction dump file as input that we can generate while executing the smart-contract.

1. Generate the transaction dump file:
   ```bash
   goal app call --app-id $APPID --from $WALLET1 --app-arg "str:dec" **--dryrun-dump -o tx.dr**
   ```
2. Start debugger session:
   ```bash
   tealdbg debug -d tx.dr --listen 0.0.0.0
   ```
   ![Example](/img/6.png)
   1. The debug port (9392) can also be found from the `docker-compose.yml` file under the key: `CDT_PORT`.
3. Open the Chrome browser and goto `chrome://inspect/#devices`
   1. Click on “Configure” beside the “Discover network targets” option.
   2. Add `localhost:9392`
      ![Example](/img/7.png)
   3. Under “Remote Target”, look for the Algorand TEAL program and click on “inspect”.


## Links

- [Official Algorand Smart Contract Guidelines](https://developer.algorand.org/docs/get-details/dapps/avm/teal/guidelines/)
- [PyTeal Documentation](https://pyteal.readthedocs.io/en/latest/index.html)
- [Algorand DevRel Example Contracts](https://github.com/algorand/smart-contracts)
- [Awesome-Algorand](https://github.com/aorumbayev/awesome-algorand)

## Contents

- [Official](#official)
- [Wallets](#wallets)
- [Blockchain Explorers](#blockchain-explorers)
- [Portfolio Trackers](#portfolio-trackers)
- [Learning](#learning)
- [Development](#development)
- [IDEs](#ides)
- [Tools](#tools)
- [NFT Marketplaces](#nft-marketplaces)
- [DeFi Platforms](#defi-platforms)
- [Security Audits](#security-audits)
- [Bridges](#bridges)
- [Oracles](#oracles)
- [Name Services](#name-services)
- [Community](#community)
- [ARCs](#arcs)
- [Metrics](#metrics)

## Official

- [Algorand](https://www.algorand.com/) - Official website.
- [Algorand Foundation](https://algorand.foundation/) - Official website of the Foundation.
- [Algorand FAQ](https://algorand.foundation/faq) - FAQ maintained by the Algorand Foundation.
- [Algorand Governance](https://governance.algorand.foundation/) - Official website of Algorand Governance program.
- [Algorand Developer Portal](https://developer.algorand.com/) - Official Algorand developer portal.
- [Algorand Discord](https://discord.com/invite/YgPTCVk) - Official Algorand Discord server.

## Wallets

- [Pera Wallet](https://github.com/perawallet) - Secure, open source and community driven wallet for mobile devices. Maintained by the team behind official Algorand Wallet.
- [MyAlgo Connect](https://github.com/randlabs/myalgo-connect) - Web Wallet and JS library for signing transactions in the browser.
- [AlgoSigner](https://github.com/PureStake/algosigner) - Browser plugin Web Wallet and JS library for signing transactions in the browser.
- [Method Wallet](https://methodwallet.app/) - Algorand Wallet you'll love.

## Blockchain Explorers

- [AlgoExplorer](https://www.algoexplorer.io/) - Algorand blockchain explorer built by [RandLabs](https://randlabs.io/).
- [Goalseeker](https://goalseeker.purestake.io/algorand/mainnet) - Algorand blockchain explorer built by [PureStake](https://www.purestake.com/).
- [NFTExplorer](https://www.nftexplorer.app/) - Algorand Standard Asset (ASA) explorer built by [@TomassoAE](https://twitter.com/TommasoAE) and [JoshLmao](https://twitter.com/JoshLmao).
- [Algorand Ballet](https://akaalias.github.io/algorand-ballet/) - Algorand accounts' 2D graphs.
- [Algorand Multiverse](https://algo3d.live/) - Algorand accounts' 3D graphs.
- [Algoscan](https://algoscan.app/) - Algoscan is a Blockchain Explorer and Analytics Platform. Built on top of the Algorand Network.
- [Asalytic](https://www.asalytic.app/) - Analyze the Algorand NFT space.
- [Dappflow](https://explorer.dappflow.org/) - Algorand Private Network Explorer (supports Sandbox in `localhost`).

## Portfolio Trackers

- [Algogator.Finance](https://algogator.finance/) - Track or search assets, rewards, yield farming, transactions, and NFTs on the Algorand blockchain anywhere and anytime. Built by [@TeamAlgogator](https://twitter.com/TeamAlgogator).
- [ASA Stats](https://www.asastats.com/) - One-stop portfolio tracker used to summarize Algorand asset valuations from up to five wallet addresses.

## Learning

### Crash Courses

- [Algorand School](https://github.com/cusma/algorand-school) - Algorand School crash course slide deck.
- [Zero to Hero PyTeal](https://www.youtube.com/playlist?list=PLpAdAjL5F75CNnmGbz9Dm_k-z5I6Sv9_x) - PyTeal crash course video lectures.
- [Algorand, efficient self-sustaining blockchain](https://prismic-io.s3.amazonaws.com/algorandfoundationv2/d5407f96-8e7d-4465-9656-2abb558850a9_Proof+of+Stake+Blockchain+Efficiency+Framework.pdf) - Proof of Stake Blockchain Efficiency Framework.
- [Algorand Efficiency](https://www.youtube.com/watch?v=e8s8Ui8vDaY) - Understanding Algorand's working principles and its efficiency.
- [Introduction to AVM and Applications](https://www.youtube.com/watch?v=fTAPLiPcj28) - Introduction to the Algorand Virtual Machine architecture and Algorand Smart Contracts (aka Applications).
- [Introduction to PyTeal](https://www.youtube.com/watch?v=zXDqJHK_Bqs) - Introduction to PyTeal, a Python framework to develop Smart Contract on Algorand (with [@matteojug](https://twitter.com/matteojug)).
- [PyTeal ABI Smart Contracts](https://www.youtube.com/watch?v=USLcyfVD_ws) - Using PyTeal to develop _ABI-compliant_ Smart Contracts on Algorand. Final live coding section (with [@_deanste](https://twitter.com/_deanste)).
- [Beaker](https://www.youtube.com/watch?v=031VvOxvuxY) - Framework for Algorand Smart Contract development, client and testing based on PyTeal. Live coding session (with [@HGKimChris](https://twitter.com/HGKimChris)).

### General courses

> Please note these are intended for absolute beginners interested in foundational knowledge relatable to all blockchain systems. Building a theoretical understanding of the domain of Blockchain protocols is an important prerequisite that can significantly amplify your learning about Algorand technology.

- [Foundations of Blockchains](https://www.youtube.com/watch?v=KNJGPI0fuFA&list=PLEGCF-WLh2RLOHv_xUGLqRts_9JxrckiA) - A video course by Tim Roughgarden a Professor of Computer Science at Columbia University highlighting the fundamental principles, concepts and properties of Blockchain protocols.

### Tutorials

- [Lending pool using Reach](https://developer.algorand.org/tutorials/building-a-lending-pool-using-reach/) - Tutorial on how to build a lending pool using the Reach language.
- [Creating a License Manager Contract](https://developer.algorand.org/tutorials/creating-a-license-manager-contract-utilizing-pyteal-and-inner-transactions/) - Tutorial on utilizing PyTEAL and Inner Transactions.
- [Connect to Algorand Wallet via WalletConnect](https://developer.algorand.org/tutorials/redux-example-connect-wallet-walletconnect/) - Redux example using WalletConnect.
- [AlgoMinter](https://developer.algorand.org/tutorials/algominter-a-web-app-for-minting-assets-using-python-algosigner-and-anvil-platform/) - Build your web app for minting assets using Python, AlgoSigner, and Anvil Platform.
- [Getting Started with Django, Python, and Algorand](https://developer.algorand.org/solutions/getting-started-with-python-algorand-sdk-and-django/) - Tutorial from algorand developer portal.
- [Swift iOS app using Augmented Reality for Algorand purchases](https://developer.algorand.org/tutorials/ios-app-using-augmented-reality-for-purchases/) - An iOS App Using Augmented Reality for Purchases.
- [MultiSig with Algorand for Co-operative Groups](https://developer.algorand.org/tutorials/decentralised-co-operative-unions-algorand-multisignature-account/) - Decentralised co-operative unions with Algorand Multisignature Account.
- [Algorand on RaspberryPi](https://developer.algorand.org/tutorials/development-on-algorand-using-raspberry-pi-part-1/) - Development on Algorand using Raspberry Pi.
- [Adding Notes to Transactions](https://developer.algorand.org/tutorials/v2-read-and-write-transaction-note-field-python/) - Read and Write to the Transaction Note Field with Python.
- [Create Assets with a Stateful Smart Contract](https://developer.algorand.org/solutions/using-stateful-smart-contract-to-create-algorand-standard-asset/) - Using Stateful Smart Contract To Create Algorand Standard Asset.

## Development

> Awesome client libraries, tools, and community utilities sorted by the language of implementation.

### C/C++

- [vertices-algorand-sdk](https://github.com/vertices-network/c-vertices-sdk) - The Vertices SDK provides developers with easy device access to interact with Blockchains.

### Python

- [py-algorand-sdk](https://github.com/algorand/py-algorand-sdk) - The Algorand Python SDK.
- [pyteal](https://github.com/algorand/pyteal) - Algorand Smart Contracts in Python.
- [pyteal-utils](https://github.com/algorand/pyteal-utils) - PyTEAL utilites library.
- [tinyman-py-sdk](https://github.com/tinymanorg/tinyman-py-sdk) - Tinyman Python SDK.
- [algofi-py-sdk](https://github.com/Algofiorg/algofi-py-sdk) - Algofi Python SDK.
- [beaker](https://github.com/algorand-devrel/beaker) - A tool for smart contract development on the Algorand blockchain. Inspired by `flask`.
- [smart-asa](https://github.com/algorandlabs/smart-asa) - Smart ASA PyTeal reference implementation based on ARC-20.

### VSCode

- [Obsidian Labs/vscode-algorand](https://github.com/ObsidianLabs/vscode-algorand) - Algorand VS Code Extension.
- [optio-labs/teal-debugger-extension](https://github.com/optio-labs/teal-debugger-extension) - Debug teal with minimal AVM configuration inside VSCode.

## Tools

### Smart Contracts

- [reach](https://docs.reach.sh) - A domain-specific language for building cross chain decentralized applications (DApps).
- [aqua-compiler](https://github.com/optio-labs/aqua-compiler) - An expressive high level language for the Algorand block chain that compiles to TEAL code.
- [algoml](https://github.com/petitnau/algoml) - A domain-specific language for specifying Algorand smart contracts, which compiles into TEAL scripts.
- [tealang](https://github.com/pzbitskiy/tealang) - A high level language for Algorand ASC1 and TEAL.
- [ASC Builder](https://ascbuilderapp.com) - A GUI (drag and drop) tool for developing & deploying Algorand smart contracts developed by [Algoknox](https://twitter.com/Algoknox).
- [tealish](https://github.com/Hipo/tealish) - Tealish is a readable language for the Algorand Virtual Machine. It enables developers to write TEAL in a procedural style optimized for readability.
- [avm-semantics](https://github.com/runtimeverification/avm-semantics) - Algorand Virtual Machine and TEAL Semantics in K framework. Aids with testing and formal verification of smart contracts.

### Docker

- [Algorand Sandbox](https://github.com/algorand/sandbox) - Fast way to create and configure an Algorand development environment.
- [Algorand Sandbox Dev](https://github.com/MakerXStudio/algorand-sandbox-dev) - Docker Hub image for faster local development and CI/CD usage.

### Testing

- [graviton](https://github.com/algorand/graviton) - Algorand's TEAL blackbox testing toolkit.
- [tealer](https://github.com/crytic/tealer) - Static TEAL analyser with a set of vulnerability detectors for quick contracts reviews.
- [irulan](https://irulan.dev/) - Web app for deploying + testing smart contracts ([open source! + PRs welcome](https://github.com/ncc/irulan)).

### Nodes and Indexers

- [Algorand - The Undocumented Docs](https://github.com/AlgoChads/algorand-undoc-docs) - Dev Notes for Archival Node, Indexer Setup (and more).
- [AlgoNode](https://algonode.cloud) - Node running FAQ, Node/Indexer daily snapshots, Free Node/Indexer APIs.
- [Algorand Node UI](https://github.com/algorand/node-ui) - Terminal UI for remote Algorand node management.
- [SubQuery](https://subquery.network) - Open, fast, flexible, and decentralised cross-chain data indexer for Algorand ([getting started guide](https://academy.subquery.network/quickstart/quickstart_chains/algorand.html)).

### Wallets

- [use-wallet](https://github.com/txnlab/use-wallet) - React hooks for using Algorand compatible wallets with web applications. Developed by [txnlab](https://www.txnlab.dev/).

## NFT Marketplaces

- [AB2](https://ab2.gallery/) - decentralized marketplace for crypto art assets secured on the Algorand blockchain created by [@ab2_gallery](https://twitter.com/AB2_Gallery).
- [Rand Gallery](https://www.randgallery.com/) - Algorand Standard Asset (ASA) explorer and markeplace developed by [Chris Antaki](https://github.com/ChrisAntaki).
- [AlgoGems](https://algogems.io/) - Algorand Standard Asset (ASA) markeplace and trading platform for NFT collectors.
- [AlgoWorldExplorer](https://algoworldexplorer.io/) - NFT marketplace, explorer and gallery for AlgoWorld NFTs developed by [@millionalgosfather](https://twitter.com/millionalgos).
- [AlgoMart](https://github.com/deptagency/algomart) - Opensource NFT marketplace whitelabel solution.
- [The GIF Economy](https://gifeconomy.com/) - Decentralized NFT explorer, minting and trading.
- [ALGOxNFT](https://algoxnft.com/listings/live) - NFT gallery and marketplace.
- [Dartroom](https://dartroom.xyz/overview) - Decentralized NFT explorer, minting and trading.
- [Alchemon](https://alchemon.net/) - Monster-collecting NFT card game.
- [Otherverse](https://otherverse.io/) - NFT collections marketplace.
- [Musa](https://www.musanft.io/) - Music, Art & Fashion NFT gallery and marketplace.
- [Flatter](https://www.flatternft.com/) - NFT art and collectible marketplace.
- [Dahai](https://www.dahai.uk/) - NFT art gallery and marketplace.
- [Aorist](https://aorist.art/) - NFT art gallery and marketplace.
- [ZestBloom](https://zestbloom.com/) - Digital Media NFT marketplace that offers ways to promote artists for their contributions.

## DeFi Platforms

- [Yieldly](https://yieldly.finance/) - DeFI suite for cross-chain swapping, ASA staking and lottery games.
- [Tinyman](https://tinyman.org/) - A decentralized trading protocol, AMM and platform.
- [Algofi](https://www.algofi.org/) - Decentralized lending market and stablecoin on the Algorand blockchain.
- [Pact](https://www.pact.fi/) - Decentralised Automated Market Maker (AMM) built on the Algorand protocol.
- [Lofty.ai](https://www.lofty.ai/) - Tokenized real estate investing platform.
- [Folks.finance](https://folks.finance/) - Decentralized capital markets protocol.
- [C3](https://c3.io/) - Cross-margin cross-collateralization clearing engine protocol.

## Security Audits

> This section is not aimed to promote any of the companies below, please do your due diligence when researching on options available for audits. Instead, the following is simply aimed to highlight an expanding variety of companies offering smart contract audits for Algorand ecosystem.

- [Certik](https://www.certik.com/ecosystems/algorand) - CertiK provides the gold standard in web3 security tools, from its industry-leading smart contract audits, to its blockchain analytics tools such as Skynet and SkyTrace, making it the ideal partner in ensuring the end-to-end security of Algorand projects.
- [AlgoBuilder Audits](https://algobuilder.dev/auditing.html) - The team behind AlgoBuilder offering to consult your project, have smart contracts done by professionals, write automated tests or audit your existing project.
- [UlamLabs](https://www.ulam.io/software-services/smart-contract-audits) - A blockchain lab based in Poland, offering auditing services for Algorand smart contracts.
- [Runtime Verification](https://runtimeverification.com/smartcontract) - Smart contract analysis and verification by the team who audited platforms like Algofi, FolksFinance, Yieldly and other prominent DeFi platforms in the ecosystem.
- [Immunebytes](https://www.immunebytes.com) - Secure your Algorand Smart Contract with credible security auditing solutions.
- [KudelskiSecurity](https://kudelskisecurity.com) - Move your blockchain project securely and successfully into production or onto mainnet. Company can help you assess, design, customize, deploy and manage blockchain and digital ledger technology systems so you can confidently leverage security as a powerful differentiator in this dynamic market.

## Bridges

- [Algomint](https://algomint.io/) - Centralized BTC and ETH bridge to Algorand.
- [QUIGON](https://bridge.quigon.com/) - Centralized cross-chain NFT bridge.
- [GlitterFinance](https://glitter.finance/) - Centralized (soon to be decentralized upon TEAL 6) bridge for Solana, Polygon, Terra and Cudos to Algorand.
- [ptokens](https://dapp.ptokens.io/swap?asset=btc&from=btc&to=algorand) - pNetwork Officially Launched Cross-Chain Bridges for Algorand.

## Oracles

- [Algoracle](https://www.algoracle.ai/) - Decentralized oracle networks that connect the Algorand blockchain with the real world.

## Name Services

- [NFDomains](https://nf.domains/) - Algorand name service and marketplace for Non-Fungible Domains (NFDs) — unique, readable aliases for wallet addresses.
- [Algorand Name Service](https://algonameservice.com/home) - ANS is a decentralized naming service. An Algorand smart contract stores names and governs the naming policy.

## Community

> The following contains sections related to open source projects, utilities, and news resources.

### Projects

- [arc3.xyz](https://github.com/barnjamin/arc3.xyz) - Dapp that can be used to mint ARC3 compliant NFTs.
- [Auction Demo](https://github.com/algorand/auction-demo) - On-chain NFT auction using smart contracts.
- [Algorand Session Wallet](https://github.com/barnjamin/algorand-session-wallet) - Session wallet to allow persisted wallet connections across multiple wallets.
- [AlgoWorld-Contracts](https://github.com/algoworldNFT/algoworld-contracts) - Collection of all smart contracts used by AlgoWorld, written in PyTeal.
- [AlgoWorld-Swapper](https://github.com/algoworldNFT/algoworld-swapper) - Free and trustless ASA swapper, powered by Algorand Smart Signatures.
- [WalletConnect Example DApp](https://github.com/algorand/walletconnect-example-dapp) - Algorand WalletConnect demo.
- [paytx](https://github.com/algorand-devrel/paytx) - Examples of common pay transaction scenarios using the Algorand Python SDK.
- [TinyBar App](https://tinybar.app) - A tiny macOS menu bar app for tracking ASA prices from TinyMan.
- [Algodesk](https://www.algodesk.io/) - Create, configure and manage your Assets on Algorand Blockchain.
- [algonim](https://github.com/cusma/algonim) - First Algorand mini-puzzle-game. Written in Python+PyTEAL by [@cusma](https://twitter.com/cusma_b).
- [algorealm](https://github.com/cusma/algorealm) - Claim the Crown and the Sceptre of Algorand Realm! Written in Python+PyTEAL by [@cusma](https://github.com/cusma).
- [minter](https://github.com/algofishexe/minter) - Bulk mint Algorand NFTs following the ARC-69 community standard. Written in Node.js by [@fish.exe](https://twitter.com/AlgofishExe).
- [algovanity](https://algovanity.com/) - Algorand Vanity Address Generator from [Ripe](https://github.com/Ripe/algovanity).
- [galvanity](https://github.com/shmutalov/galvanity) - Galvanity is Algorand vanity address generator written in Go.
- [Algocurator](https://www.algocurator.com/home) - The Biggest Unofficial One-Stop Hub for Algorand.
- [genpyteal](https://github.com/runvnc/genpyteal) - Generate PyTeal from (mostly) normal Python.
- [AgorHash](https://github.com/bafio89/agorhash) - Public, permissionless, decentralized and uncensorable free speech protocol.
- [QRCode Generator](https://github.com/emg110/algorand-qrcode)
- [algofractals](https://github.com/aorumbayev/algofractals) - Mint randomly generated mandelbrot fractals with embedded ARC69 tags.
- [algorewards](https://algorewards.github.io/) - Free and unofficial Algorand governance reward calculator. Hosted on GitHub Pages.
- [algorand.dev](https://algorand.dev/) - Algorand Developer: The unofficial developer resource.
- [daotools](https://daotools.org/) - Manage all your DAO voting sessions in one Dapp.
- [FORUM](https://forum.ax/) - A proof-of-stake social network on the Algorand blockchain.
- [Pipeline-UI](https://www.pipeline-ui.com/) - A React.js based component library for rapid deployment of Algorand Dapps.
- [AlgoCloud](https://algocloud.org/) - Algorand ecosystem dashboard. Connect DeFi apps, display NFTs, account analytics, and more.
- [STOI](https://stoi.org/) - Song ownership gone decentralized via microDAOs.
- [AlgoTables](https://github.com/algotables/algotables.github.io) - A suite of tools designed to aid everyday hodlers of ALGO who participate in the Algorand ecosystem.
- [AlgoPing](https://github.com/aorumbayev/algoping) - A tiny cron job that issues a [tweet](https://twitter.com/algoping) if public Algorand Nodes (AlgoExplorer, AlgoNode and etc) are not healthy.
## ARCs

> Standards and specs defined in _finalized_ ARCs.
> The list of all the ARCs can be found [here](https://arc.algorand.foundation).

- [ARC3](https://github.com/algorandfoundation/ARCs/blob/main/ARCs/arc-0003.md) - Official Algorand Standard Asset Parameters Conventions for Fungible and Non-Fungible Tokens.
- [ARC69](https://github.com/algokittens/arc69) - Unofficial Algorand Standart Asset Parameters Convention popular in Algorand NFT community, inspired by [Open Sea's metadata standards](https://docs.opensea.io/docs/metadata-standards) and [EIP-1155](https://github.com/ethereum/EIPs/blob/master/EIPS/eip-1155.md#erc-1155-metadata-uri-json-schema), created by [@algokittens](https://twitter.com/AlgoKittens).

## Metrics

- [Algorand MainNet metrics](https://metrics.algorand.org/) - Dashboard that measures the current scale, security, decentralization, and adoption of the open-source Algorand protocol.
- [How Big is Algorand?](https://howbigisalgorand.com/) - Algorand's networks size monitor.
- [Algorand Mempool](https://www.iamnotabot.com/pool) - Algorand mempool explorer.
- [Metrika](https://app.metrika.co/dashboard/algorand/) - Algorand network performance and account monitor.
- [Algorand Stats](https://www.algorandstats.com/) - All stats about Algorand Governance.

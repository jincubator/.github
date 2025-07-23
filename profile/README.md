# Solving and Arbitrage Research

## Overview

Here you'll find my work in 2025 on Solving, Arbitrage and Indexing using [Tycho](https://docs.propellerheads.xyz/tycho/overview), Intents using [ERC-7683](https://www.erc7683.org/spec), [EIP-712](https://eips.ethereum.org/EIPS/eip-712), [Compactx](https://github.com/uniswap/compactx) and [Uniswap V4 Hooks](https://docs.uniswap.org/contracts/v4/overview). As of July 2025 I use [jincubator](https://github.com/jincubator) for research and development of prototypes.

## Abstract

Liquidity Fragmentation and Capital Efficiency are areas that can be optimized in Blockchain protocols, with the emergence of Multiple L2 Chains and a shift towards intent-based architectures. There is a greater need than ever for a settlement layer to balance provided liquidity.

Project Description: Jincubator (Working Name) provides infrastructure and services for multi-chain, intent-based protocols, leveraging hooks built on Uniswap V4.

It aims to provide the following

**Features of the Solution**

- Pool Functionality
  - JIT Liquidity Provisioning,
  - Dynamic Fees to reduce arbitrage,
  - Gas Manager allows users to pay for gas using their swap token instead of the native token,
  - Incentivization Tools for Pools to incentivize Liquidity Providers,
- Order Flow
  - Intent Solving infrastructure providing traders with the best value for their swap,
  - SlowTrack and Fastrak - Settlement Options,
- Capital Efficiency
  - Settlement Layer with Dynamic Rebalancing of Capital Across Chains,
  - JIT provisioning of liquidity to pools as needed which integrates with Yield Earning Protocols.,
  - Arbitraging Liquidity across Pools, Protocols, and Chains


**Solution we are implementing**

- Intent Based - Cross Chain Solving
- Dynamic Cross-Chain Capital Allocation for Liquidity Providers
- JIT Liquidity Provisioning for Pools
- Yield Earning Capabilities for unitilized liquidity
- Centralized Liquidity Pool which is dynamically allocated
- Settlement Layer used by Solvers and Liquidity Providers used to rebalance funds across chains.
- Advanced Indexing and Simulation capabilities to enable a global view of liquidity, swap prices and arbitrage opportunities

## Opinionated Sample Architecture from [jincubator](https://github.com/jincubator)

This is an opinionated architecture for an intent based solving protocol which facilitates single and mult-chain solving of intents. Intents can be solved on a single chain without provisioning up front capital as we arbiters can confirm mandates have been met by solvers at execution time, thus solvers may used the swappers locked funds for execution.

- Solving built in RUST leveraging [Tycho](https://docs.propellerheads.xyz/tycho/overview) from [Propellor Heads](https://www.propellerheads.xyz/) including
  - [tycho-sdk](https://github.com/propeller-heads/tycho-protocol-sdk): For integrate DEXs and other onchain liquidity protocols
  - [tycho-indexer](https://github.com/propeller-heads/tycho-indexer): a low-latency, reorg-aware stream of all attributes you need to simulate swaps over DEX and other on-chain liquidity built on [substreams](https://github.com/streamingfast/substreams)
  - [tycho-simulation](https://github.com/propeller-heads/tycho-simulation): a Rust crate which allows simulating a set of supported protocols off-chain
  - [tycho-execution](https://github.com/propeller-heads/tycho-execution): a simple, ready-to-use tool that generates the necessary data to execute trades on multiple chains and DEX's
- Intent Management platform allowing optimized trading routes optimized by solvers who do not need to provide liquidity up front
  - [the-compact](https://github.com/Uniswap/the-compact): an ownerless ERC6909 contract that facilitates the voluntary formation (and, if necessary, eventual dissolution) of reusable resource locks.
  - [arbiters](https://github.com/Uniswap/arbiters): selects a claim method based on the type of Compact message signed by the sponsor and allocator and on the desired settlement behavior. To finalize a claim, some actor must call into the arbiter, which will act on the input and translate it into their preferred claim method. The arbiter then must call the derived claim method on The Compact to finalize the claim process.
  - [Tribunal](https://github.com/uniswap/tribunal): a framework for processing cross-chain swap settlements against PGA (priority gas auction) blockchains. It ensures that tokens are transferred according to the mandate specified by the originating sponsor and enforces that a single party is able to perform the settlement in the event of a dispute. _Note: currently working on enhancing the [EIP712 Signing](https://eips.ethereum.org/EIPS/eip-712) of the [mandates](https://github.com/uniswap/tribunal?tab=readme-ov-file#mandate-structure) so that the protocol can be used for solving on a single chain and multichain settlement._
  - Services that enable Solving and Arbitrage are drawn primarily from uniswap prototypes for [compactX](https://github.com/uniswap/compactx). _Note: it would be good to develop the majority of these in Rust and leverage Tycho's indexing and execution services_
    - [callibrator](https://github.com/Uniswap/Calibrator): An intent parameterization service, demo is [here](https://calibrat0r.com/). _Note: This will need to incorprate/integrate [mandates](https://github.com/uniswap/tribunal?tab=readme-ov-file#mandate-structure) as we build a solution for solving._
    - [v4-router](https://github.com/jincubator/v4-router): a simple and optimized router for swapping on Uniswap V4. _Note: Currently working on integrating intents into this management into this codebase and integrating this with an optimized smart order router._
    - [autocator](https://github.com/uniswap/autocator): A server-based allocator for The Compact that leverages protocol signatures and transactions for authentication, API reference is [here](https://autocator.org/).
    - [smallocator](https://github.com/Uniswap/Smallocator): Similar to autocator with smart contract support via EIP-4361 session authentication and signing EIP-712 Compact messages.
    - [Fillanthropist](https://github.com/Uniswap/Fillanthropist): receiving and filling broadcasted cross-chain swap intents, demo is [here](https://fillanthropist.org/). _Note: This infrastructure can be replaced by solver technology built on tycho as well as an update dissemination approach (see repo below) which can leverage tycho indexing._
    - [disseminator](https://github.com/Uniswap/disseminator): A TypeScript WebSocket server implementation that broadcasts messages to both HTTP endpoints and WebSocket clients. The server validates incoming messages using Zod schemas and ensures proper message delivery to all connected clients. _Note: Design work still needs to be done as to the most efficient way to store and transmit detailed intent and mandate information_
  - Frontend would include swapping and also liquidity provisioning and optimized Yield strategies for Liquidity Providers two inspirational repositories are
    - [compactX](https://github.com/uniswap/compactx): a proof-of-concept, React-based web interface for performing cross-chain swaps.
    - [YOLO Protocol](https://yolo-demo-ui-hackathon-chainlink-ch.vercel.app/): A Demo app developed for a hackathon by [YOLO Protocol](https://linktr.ee/yolo.protocol) which includes a dashboard for Liquidity Providers to manage their positions.
   
## Uniswap V4 Hook Integration - WIP

This section documents design work to be done for enhancing Uniswap V4 through the use of hooks for better price discovery, swapping, and solver execution.

It leverages [Tycho](https://docs.propellerheads.xyz/tycho/overview), Intents using [ERC-7683](https://www.erc7683.org/spec), [EIP-712](https://eips.ethereum.org/EIPS/eip-712), [Compactx](https://github.com/uniswap/compactx) and [Uniswap V4 Hooks](https://docs.uniswap.org/contracts/v4/overview). As of July 2025 I use [jincubator](https://github.com/jincubator) for research and development of prototypes.

### Hook Mods

1. Booster Pools
2. BackRunning
3. ReHypothecation
4. Paymaster
5. Settlement Pools - CrossChain
6. Router change to Slippage Failure to create an Intent.
7. HOOK

### Deliverables

1.  No Liquidity Pool
    1. Swap via preferred LP at fixed price from Oracle
2.  IntentSwap
    1. CreateIntentSwap (includes output amount in each call)
    2. ExecuteIntentSwap (uses funds from compact)
    3. SweepIntentSwap (passed a compact)
3.  BoosterPool
    1. Adds IntentManagement to Any Pool
    2. Adds Dynamic fees to Any Pool
    3. Adds BackRunning via Solver
    4. Adds ReHypothecation to any pool USDCY
    5. Adds Oracle Pricing to any pool EULER-ORACLES
    6. Adds Gas Sponsorship

### Team

Development is being lead by John Whitton, below are some handy links about him.

- [github](https://github.com/johnwhitton): Johns github profile
- [johnwhitton.com](https://johnwhitton.com): All about John, his work, writing, research etc.
- [My Resume](https://resume.johnwhitton.com): One-page resume in pdf format.
- [Overview](https://overview.johnwhitton.com): A little infographic of John's history
- [Writing](https://johnwhitton.com/posts.html) and [Research](https://johnwhitton.com/research.html): Some writing and research John has done (a little outdated)
- [Uniswap v4](https://github.com/johnwhitton/uhi5-exercises): Completed exercises and references for the Uniswap Hook Incubator

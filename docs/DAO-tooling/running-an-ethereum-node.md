---
title: Running an Ethereum Node - stateless

description: Proper ways to run a Parity or Geth Ethereum node.
---

# Running an Ethereum Node

## Summary

Anyone is able to run an Ethereum node on their computer. This means that you can participate in validating transactions and blocks on the Ethereum blockchain. The two most common clients for running nodes are [Geth](https://geth.ethereum.org/downloads/) and [OpenEthereum](https://github.com/openethereum/openethereum/releases/). Depending on the type of node run and the hardware specifications of the system, the initial syncronization time and storage requirements vary. For information related to the current Full Node chaindata and state storage size check out:
[https://etherscan.io/chartsync/chaindefault](https://etherscan.io/chartsync/chaindefault)

For an easy guide on running your own node, please see this [blog post](https://medium.com/@JustinMLeroux/running-ethereum-full-nodes-a-guide-for-the-barely-motivated-a8a13e7a0d31).

Below are the different types of nodes a user can run, their settings, and what they mean.

## Full nodes

A full node:

* Stores the full blockchain data available on disk and can serve the network with any data on request.
* Receives new transactions and blocks while participating in block validation.
* Verifies all blocks and states.
* Stores recent state only for more efficient initial sync.
* All state can be derived from a full node.
* Once fully synced, stores all state moving forward similar to archive nodes (more below).

### Client settings

**geth**

The default sync mode. Synchronizes a full node doing a [fast synchronization](https://ethereum.stackexchange.com/questions/1161/what-is-geths-fast-sync-and-why-is-it-faster) by downloading the entire state database, requesting the headers first, and filling in block bodies and receipts afterward. Once the fast sync reaches the best block of the Ethereum network, it switches to full sync mode.

**geth --syncmode full**

Synchronizes a full node starting at genesis, verifying all blocks and executing all transactions. This mode is a bit slower than the fast sync mode but comes with increased security.

**./Nethermind.Runner --config mainnet**

The default [sync mode](https://docs.nethermind.io/nethermind/ethereum-client/sync-modes). Synchronizes a full node doing a [fast synchronization](https://ethereum.stackexchange.com/questions/1161/what-is-geths-fast-sync-and-why-is-it-faster) by downloading the entire state database, requesting the headers first, and optionally filling in block bodies and receipts. Once the fast sync reaches the best block of the Ethereum network, it switches to full sync mode.


**openethereum**

The default sync mode. Synchronizes a full Ethereum node using [warp synchronization](https://ethereum.stackexchange.com/questions/9991/what-is-paritys-warp-sync-and-why-is-it-faster-than-geth-fast) mode by downloading a snapshot of the 30,000 best blocks and the latest state database.

Once the snapshot is restored, the client switches to full sync and ancient blocks are synchronized from the network in background.

A openethereum default node serves the network as a full node after it has finished synchronizing.

**besu**

Besu's default sync mode is with [fastsync](https://besu.hyperledger.org/en/stable/Reference/CLI/CLI-Syntax/#sync-mode) enabled. This setting reduces sync time considerably compared to full sync.

**besu --pruning-enabled=true**

This setting enables [pruning](https://besu.hyperledger.org/en/stable/Reference/CLI/CLI-Syntax/#pruning-enabled) in order to reduce storage required for the world state. Pruning removes state trie nodes that aren’t required.


## Light nodes

A light node:

* Stores the header chain and requests everything else on demand.
* Can verify the validity of the data against the state roots in the block headers.

Light nodes are useful for low capacity devices, such as embedded devices or mobile phones, which can't afford to store multiple dozen Gigabytes of blockchain data.

### Client settings

**geth --syncmode light**

Waits for around 200 seconds before beginning to sync from 2,300 blocks in the past, then periodically receives small bundles of 1 to 10 blocks. The initial sync takes very little time.

## Archive Nodes

An archive node:

* Stores everything kept in the full node.
* Also builds an archive of historical states.

Archive nodes are only necessary if you want to check the state of an account at any given block height. For example, if you wanted to know the Ether balance an account had at block #4,000,000, you would need to run and query an archive node.

They are commonly only used for services such as block explorers and infrastructure providers like Infura. They are use case dependent and have no impact on the security or trust model of the blockchain.

### Client Settings

**geth --syncmode full --gcmode archive --txlookuplimit=0 --cache.preimages**

Synchronizes an archive node starting at genesis, thoroughly verifying all blocks, executing all transactions, and writing all intermediate states to disk ("archive").

In Geth, this is called gcmode which refers to the concept of [garbage collection](https://en.wikipedia.org/wiki/Garbage_collection_(computer_science)). Setting it to archive basically turns it off.

**./Nethermind.Runner --config mainnet_archive**

Synchronizes a full node starting at genesis, verifying all blocks and executing all transactions. This mode is much slower than the fast sync mode but comes with increased security.

**openethereum --pruning="archive" --no-warp**

[Pruning](https://besu.hyperledger.org/en/stable/Concepts/Pruning/) is enabled by default, setting it to archive while turning off warp will build all historical data from genesis. 


**besu --sync-mode=FULL**

To run an archive node, enable full synchronization using --sync-mode=FULL, which by default also disables pruning (--pruning-enabled=false).


## Hardware

A consumer-grade laptop will be enough to run a full node, but not an archive node. An archive node does need 2+ TB of disk space, and that disk space cannot be HDD - it must be SSD for both full and archive nodes. Light nodes run fine on SD cards and HDDs.

If a full node goes offline for a while, its data can get corrupted and it can take a while to restore it. For best results, if running your own node, run it on an always-on always-connected device for minimal downtime and maximum reliability. This can be impossible on a laptop and expensive on a desktop PC (500W+) so it's better to use a device that's cheap to build and replace and almost free to run. A Raspberry Pi is enough for light nodes while a full node will run fine on an ARM micro computer. Check out pre-synced pre-built devices by [Ava.do](https://ava.do), and [DAppNode](https://shop.dappnode.io/).

Warning: never plug into your LAN anything you cannot thoroughly inspect and verify. You might be subjecting yourself to DNS hijacking or cryptojacking without knowing it. If the hardware and software aren't open source (at least the assembly part), they aren't safe to use.

## Resources

* [Spin up your own Ethereum node](https://ethereum.org/sk/developers/docs/nodes-and-clients/run-a-node/)
* [Running Ethereum Full Nodes: A Complete Guide](https://medium.com/@JustinMLeroux/running-ethereum-full-nodes-a-guide-for-the-barely-motivated-a8a13e7a0d31)
* Huge shout out and thanks to Afri Schoedon's blogs [here](https://dev.to/5chdn/ethereum-node-configuration-modes-cheat-sheet-25l8) and [here](https://dev.to/5chdn/the-ethereum-blockchain-size-will-not-exceed-1tb-anytime-soon-58a) which is where a lot of the information on this page came from.
* [Run an Ethereum Node on Debian](https://medium.com/better-programming/run-an-ethereum-node-on-linux-late-2019-b37a1d35800e)
* [Dispelling Myths About Ethereum's Disk Space](https://www.tokendaily.co/blog/dispelling-myths-about-ethereum-s-disk-space)
* [What Comprises an Ethereum Fullnode Implementation?](https://medium.com/amentum/what-comprises-an-ethereum-fullnode-implementation-a9113ce3fe3a)
* [Running an Ethereum Full Node on a RaspberryPi 4](https://kauri.io/article/9695fcca217f46feb355245275835fc0)
* [Getting started with Nethermind](https://docs.nethermind.io/nethermind/)

# Zapa

Zapa is **Z**ero-Knowledge **A**nonymous **P**ayments on **A**valanche -- a privacy coin built and deployed as an Avalanche Subnet. Zapa integrates the shielded payments technology of [Zcash](https://z.cash/) with the [Avalanche](https://docs.avax.network/) consensus mechanism in order to offer a lightning fast privacy coin fully operated on Proof of Stake.

## Status

- [x] Locally working end to end with [ava-sim](https://github.com/rkass/ava-sim/tree/master)
- [x] Testnet deployed
- [ ] Mainnet deployed 

## Fuji Deployment Information

Subnet ID: `2ma1j6rSYPykvTDk6JeHKGgXQzqtWbwrsEzUethnTf8k7DMSEb`
VM ID: `tGas3T58KzdjLHhBDMnH2TvrddhqTji5iZAMZ3RXs2NLpSnhH`
Active Blockchain ID: `6Jdeqk1VMB3qXhse3C3YnuoZPnaNAqGJJucyRSkXmpiekrEto`
Disabled Blockchain IDs: `ydwMTPrYBWHrGVmWfG8k4Po2eTPEqe7y7Z4jZaUr2Me6rin7, 2LedqoeDb3zZQSqPBczemzrofepr6SzSHXxHXANrfzeFKGGNVd`

## Setup

After cloning, fetch submodules:

`git submodule update --init --recursive`

Throughout this project there are optional directives for debugging in VS Code. Contained in this repo is a pre-configured VS Code workspace. To open this project in VS Code, open VS Code and select "Open Workspace from file", then select `zapa.code-workspace` from this repo's main directory.

## Project Overview

![Zapa Diagram](https://github.com/rkass/zapa/blob/master/ZapaDiagram.png)

This image depicts the three main components of Zapa and how they work together to facilitate an anonymous transaction on the blockchain. Each of those three components are also submodules of this repository:

- [avalanchego](https://github.com/ava-labs/avalanchego) -- Node implementation of the Avalanche network.
- [zapavm](https://github.com/zapalabs/zapavm) -- Go plugin which acts as a thin bridge between avalanchego and zcashd.
- [zcash](https://github.com/zapalabs/zcash) -- A daemon for generating and validating anonymous transactions.

The fourth submodule (not depicted above) is [ava-sim](https://github.com/zapalabs/ava-sim) which is a utility for spinning up a local network for testing.

## Local Setup

The best way to develop, test, and debug is to setup a local, five-node network using ava-sim. The steps required are:

1. Run five versions of zcashd, each exposing a different port to talk to a unique zapavm node in the local network.
2. Run ava-sim with the zapavm plugin as an argument.
3. Interact with the local nodes to send transactions, query balances, and mine blocks.

### Step 1: Running zcashd

Follow the steps outlined in [Running zcashd: As Part of a 5 Node Local Setup](https://github.com/zapalabs/zcash/blob/master/doc/running.md#running-zcashd-as-part-of-a-5-node-local-setup).

### Step 2: Running ava-sim with zapavm plugin

Follow the steps linked here to [run ava-sim with the zapavm plugin](https://github.com/zapalabs/ava-sim/blob/master/README.md#running).

### Step 3: Interact with Local Nodes

See [Interacting with the Chain](https://github.com/zapalabs/ava-sim/blob/master/README.md#interacting-with-the-chain) for instructions on how to interact with the local nodes.

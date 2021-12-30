# zsno

zsno is a project for integrating a zcash virtual machine into a custom Avalanche subnet. This repo contains all dependencies needed to develop, debug, deploy, and/or become a validator on zsno.

## Status

- [x] Locally working end to end with [ava-sim](https://github.com/rkass/ava-sim/tree/master)
- [ ] Testnet deployed
- [ ] Mainnet deployed 

## Setup

After cloning, fetch submodules:

`git submodule update --init --recursive`

Throughout this project there are optional directives for debugging in VS Code. Contained in this repo is a pre-configured VS Code workspace. To open this project in VS Code, open VS Code and select "Open Workspace from file", then select `zsno.code-workspace` from this repos' main directory.

## Interacting with the zcashvm API

### Overview

Three API methods are provided by the zcashvm: 

1. `zcashrpc` is a generalized method for sending an rpc to zcashd and returning its response. For all methods available see https://zcash-rpc.github.io/ for all methods exposed by vanilla zcash as well as https://github.com/rkass/zcash/blob/zsno-release/doc/zsno.md for additional methods exposed by the custom zcashd that zsno runs with.
2. `submittx` is a specialized method for submitting a transaction. This could technically be accomplished with `zcashrpc`, but a separate method is exposed for clarity of inputs.
3. `localnodestartup` only used locally. See https://github.com/rkass/zsno/blob/master/README.md#5-post-launch

### Examples

All hosts below need to be replaced with the host you're using.

#### listunspent

Lists unspent utxos for the wallet of the given node.

##### Request

```
curl --location --request POST 'http://127.0.0.1:9650/ext/bc/2LG588Xat1PZYtf3LhMx4upBSM7yucVb2EmkiNqeiUkNVLQ7Qb' \
--header 'Content-Type: application/json' \
--data-raw '{
    "jsonrpc": "2.0",
    "method": "zcashvm.zcashrpc",
    "params":{
        "params":[],
        "Method":"listunspent",
        "ID": "fromavax"
    },
    "id": 1
}
'
```

##### Response

```
{
	"jsonrpc": "2.0",
	"result": {
		"Result": [{
				"txid": "a774565e0eab2ab1f7e148625051198d9fdce586c20a71b2cc9a5fa96adec304",
				"vout": 0,
				"generated": true,
				"address": "tmRe7AUNTHdCjAavKzkKux1ZNUWWQUgxrjE",
				"scriptPubKey": "76a914aeb485c202ea7b504dcfe648dce458d949a95b7888ac",
				"amount": 10.00000000,
				"amountZat": 1000000000,
				"confirmations": 119,
				"spendable": true
			},
			{
				"txid": "5cead91f3cf597d16bb0ad24f0355eecf49d65aab841c49bff40b712d9c3c408",
				"vout": 0,
				"generated": true,
				"address": "tmRe7AUNTHdCjAavKzkKux1ZNUWWQUgxrjE",
				"scriptPubKey": "76a914aeb485c202ea7b504dcfe648dce458d949a95b7888ac",
				"amount": 10.00000000,
				"amountZat": 1000000000,
				"confirmations": 179,
				"spendable": true
			}
		],
		"error": "",
		"id": "fromgo"
	},
	"id": 1
}
```

#### z_getnewaddress

Now we'd like to shield some coins so that they are held in a private address. First thing we must do is obtain a private address.

##### Request

```
curl --location --request POST 'http://127.0.0.1:9650/ext/bc/2LG588Xat1PZYtf3LhMx4upBSM7yucVb2EmkiNqeiUkNVLQ7Qb' \
--header 'Content-Type: application/json' \
--data-raw '{
    "jsonrpc": "2.0",
    "method": "zcashvm.zcashrpc",
    "params":{
        "params":[],
        "Method":"z_getnewaddress",
        "ID": "fromavax"
    },
    "id": 1
}
'
```

##### Response

```
{
    "jsonrpc": "2.0",
    "result": {
        "Result": "zregtestsapling1r62wjyz84w2ndevd48l0ca5uwwrtx993fv2n9s2evmul58lkghl6ylzjsguhu6zzkdm22ke2wxt",
        "error": "",
        "id": "fromgo"
    },
    "id": 1
}
```

#### submittx

Now let's shield some funds by sending a transaction from a t-address from one of our utxos above to the z address output directly above.

##### Request

```
curl --location --request POST 'http://127.0.0.1:9650/ext/bc/2LG588Xat1PZYtf3LhMx4upBSM7yucVb2EmkiNqeiUkNVLQ7Qb' \
--header 'Content-Type: application/json' \
--data-raw '{
    "jsonrpc": "2.0",
    "method": "zcashvm.submitTx",
    "params":{
        "from":"tmRe7AUNTHdCjAavKzkKux1ZNUWWQUgxrjE",
        "to":"zregtestsapling1r62wjyz84w2ndevd48l0ca5uwwrtx993fv2n9s2evmul58lkghl6ylzjsguhu6zzkdm22ke2wxt",
        "amount": 0.1
    },
    "id": 1
}
'
```

##### Response 

```
{
    "jsonrpc": "2.0",
    "result": {
        "SubmittedTx": "WzQsMCw...=="
    },
    "id": 1
}
```

#### z_getbalance

Let's check the balance of the address we just sent coins to to make sure they got there.

##### Request

```
curl --location --request POST 'http://127.0.0.1:9650/ext/bc/2LG588Xat1PZYtf3LhMx4upBSM7yucVb2EmkiNqeiUkNVLQ7Qb' \
--header 'Content-Type: application/json' \
--data-raw '{
    "jsonrpc": "2.0",
    "method": "zcashvm.zcashrpc",
    "params":{
        "params":["zregtestsapling1r62wjyz84w2ndevd48l0ca5uwwrtx993fv2n9s2evmul58lkghl6ylzjsguhu6zzkdm22ke2wxt"],
        "Method":"z_getbalance",
        "ID": "fromavax"
    },
    "id": 1
}
'
```

##### Response

```
{
    "jsonrpc": "2.0",
    "result": {
        "Result": 0.100000,
        "error": "",
        "id": "fromgo"
    },
    "id": 1
}
```

## Run it locally

The below instructions will allow you to run 5 local zsno nodes all validating the same chain, which is initialized on a local subnet of the local Avalanche network. See [ava-sim](https://github.com/rkass/ava-sim) for more details on the local environment.

### 1. Build and Run zcash
Complete the the tutorial for running zcash: [zcash/docs/debugging.md](zcash/docs/debugging.md). Ability to debug zcash is optional.
### 2. Run Many zcashes
Shut down any running zcash instances and configure 5 different configuration directories. These directories will hold your zcash.conf, blockchain details (serialized blocks), as well as wallet details. Each zcash instance will have a different wallet associated with it, slightly different configuration, and the same blockchain details.

#### Create configuration directories

In a directory of your choosing (I use `~/Library/Application\ Support/`, initialize the following subdirectories:
- Zcash0
- Zcash1
- Zcash2
- Zcash3
- Zcash4

Inside each of the above directories, initialize a file called `zcash.conf`. The contents of each file should be identical to what's specified [here](https://github.com/rkass/zcash/blob/zsno-release/doc/debugging.md#2-create-a-configuration), with the exception that `rpccport` should be set to `8232 + nodenum`. So Zcash0 should have `rpcport=8232`, Zcash1 should have `rpcport=8233`. The actual values aren't important, however it's important that they're unique. 

#### Run 5 different zcash instances

- Before starting the first instance, remove any existing blockchain files so we're starting fresh. Bringing up a node from existing state is possible however if that state is too stale (more than a day), zcash complains that the chain is stale as it expects blocks to be produced at a somewhat continuous rate. To do this run the following (note that if this is your first run from Zcash0 as your config directory this isn't necessary):

```
rm -rf Zcash0/regtest/
```

- Start up your first instance (instance 0), mine 200 empty blocks, then shut down the instance. We need to do this to seed the network with some coins (rewarded from the coinbase of the mined blocks). Future iterations will more elegantly seed the network with coins. We then need to shut down the instance so that block files are written to disk so that the other instances can start up from the same block. Run the following command to start the instance:

```
DISABLE_POW=true HOME=/path/to/urhome zcash/src/zcashd -datadir="path/to/Zcash0/" -port="18250" -reindex
```

- Once your instance has started up, execute this rpc to generate 200 new blocks. Note these blocks will only contain coinbase transactions:

```
curl --location --request POST 'http://test:pw@127.0.0.1:8232' \
--header 'Content-Type: text/plain' \
--data-raw '{"jsonrpc": "1.0", "id":"curltest", "method": "generate", "params": [200] }'
```

- Once the above successfully runs, exit out of the Zcash process by pressing `ctrl+c` or otherwise killing the process.

- Now you can start each of the 5 zcash instances in separate terminals. Below are the commands. Note that the first part of each command (except for instance 0) copies the blockchain files into the conifguration directory.

*Instance 0* (same as above):

```
DISABLE_POW=true HOME=/path/to/urhome zcash/src/zcashd -datadir="/path/to/Zcash0/" -port="18250" -reindex
```

*Instance 1*:

```
cp -r /path/to/Zcash0/regtest/blocks/ /path/to/Zcash1/regtest/blocks && DISABLE_POW=true HOME=/path/to/urhome zcash/src/zcashd -datadir="/path/to/Zcash1/" -port="18251" -reindex
```

*Instance 2*:

```
cp -r /path/to/Zcash0/regtest/blocks/ /path/to/Zcash2/regtest/blocks && DISABLE_POW=true HOME=/path/to/urhome zcash/src/zcashd -datadir="/path/to/Zcash2/" -port="18252" -reindex
```

*Instance 3*:

```
cp -r /path/to/Zcash0/regtest/blocks/ /path/to/Zcash3/regtest/blocks && DISABLE_POW=true HOME=/path/to/urhome zcash/src/zcashd -datadir="/path/to/Zcash3/" -port="18253" -reindex
```

*Instance 4*:

```
cp -r /path/to/Zcash0/regtest/blocks/ /path/to/Zcash4/regtest/blocks && DISABLE_POW=true HOME=/path/to/urhome zcash/src/zcashd -datadir="/path/to/Zcash3/" -port="18254" -reindex
```

Before starting each instance, the blockchain files of each node must be syncd. Once zsno is running, all nodes are kept in sync by the coordinating zcashvm, however each node must start from a common place. This will be changed in the future so that a node can be brought up from a fresh state into a mature chain and download the entire block history. 

### 3. Build zcashvm

- Create a logs directory:

```
mkdir zcashvm/logs
```

- Build it. This creates an artifact at `zcashvm/builds/zcashvm`

```
./scripts/build.sh builds zcashvm
```

### 4. Run ava-sim

- Forcefully kill off any previously running ava-sim processes by running `pkill -f 'ava-sim'`
- Run `ava-sim/scripts/prepare-system-plugins.sh`
- Run the script specified by the `Custom VM custom genesis` launch configuration specified [here](https://github.com/rkass/ava-sim/blob/master/.vscode/launch.json) either through VS Code debug or command line.

### 5. Post-Launch

- You'll know the subnet and each node is running when ava-sim outputs a message like the one below:

```
Custom VM endpoints now accessible at:
NodeID-7Xhw2mDxuDS44j42TCB6U5579esbSt3Lg: http://127.0.0.1:9650/ext/bc/24nwKwXReCpoGxKSPFJoVtE6YeF428h82SeKGCxkBFkVvEiHJb
NodeID-MFrZFVCXPv5iCn6M9K6XduxGTYp891xXZ: http://127.0.0.1:9652/ext/bc/24nwKwXReCpoGxKSPFJoVtE6YeF428h82SeKGCxkBFkVvEiHJb
NodeID-NFBbbJ4qCmNaCzeW7sxErhvWqvEQMnYcN: http://127.0.0.1:9654/ext/bc/24nwKwXReCpoGxKSPFJoVtE6YeF428h82SeKGCxkBFkVvEiHJb
NodeID-GWPcbFJZFfZreETSoWjPimr846mXEKCtu: http://127.0.0.1:9656/ext/bc/24nwKwXReCpoGxKSPFJoVtE6YeF428h82SeKGCxkBFkVvEiHJb
NodeID-P7oB2McjBGgW2NXXWVYjV8JEDFoW9xDE5: http://127.0.0.1:9658/ext/bc/24nwKwXReCpoGxKSPFJoVtE6YeF428h82SeKGCxkBFkVvEiHJb
```

- After each node has come up, one more step is required to associate each zcashvm with a corresponding zcashd. This is done by broadcasting all node ids to each other. Once each node knows each node id, each node can sort the node ids to get its corresponding zcashd instance. Fire the following rpc against *each instance* (total of 5 rpcs) specified above:

```
curl --location --request POST 'http://127.0.0.1:9650/ext/bc/24nwKwXReCpoGxKSPFJoVtE6YeF428h82SeKGCxkBFkVvEiHJb' \
--header 'Content-Type: application/json' \
--data-raw '{
    "jsonrpc": "2.0",
    "method": "zcashvm.localnodestart",
    "params":{},
    "id": 1
}
'
```

### 6. View logs

- All nodes dump logs to the same file. Tail them by running the following:

```
tail -f zcashvm/logs/log
```

It can be particularly necessary to look at the logs to understand which zcashvm is associated with which zcashd, especially when only some zcashds have funds. That is, in order to start sending transactions the first time you start up the network, you'll need to do so from zcashd instance 0, since that is the only wallet which has funds. After you interact with the chain and send transactions, other nodes will receive coinbase rewards, but instance 0 is the only one with funds to start. This will of course be addressed in future iterations.

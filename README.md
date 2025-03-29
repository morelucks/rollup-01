# rollup-01
This tutorial helps developers set up an OP Stack testnet
### Prerequisites
Before proceeding, make sure you have the following software dependencies installed:


| Dependency | Required Version | Check Command | Download Link |
|------------|-----------------|--------------|---------------|
| Just       | Latest          | `just --version` | [Download Just](https://github.com/casey/just) | 
| Git        | ^2              | `git --version` | [Download Git](https://git-scm.com/downloads) |
| Go         | ^1.21           | `go version` | [Download Go](https://go.dev/dl/) |
| Node.js    | ^20             | `node --version` | [Download Node.js](https://nodejs.org/) |
| pnpm       | ^8              | `pnpm --version` | [Download pnpm](https://pnpm.io/installation) |
| Foundry    | ^0.2.0          | `forge --version` | [Download Foundry](https://book.getfoundry.sh/getting-started/installation) |
| Make       | ^3              | `make --version` | [Download Make](https://www.gnu.org/software/make/) |
| jq         | ^1.6            | `jq --version` | [Download jq](https://stedolan.github.io/jq/download/) |
| direnv     | ^2              | `direnv --version` | [Download direnv](https://direnv.net/) |  

### Why these Specific Dependencies  

- **Just**: A command runner for automating tasks like building the **op-node**. You can define commands such as `go build` or other setup tasks in a `justfile`.  

- **Git**: Essential for cloning repositories and managing version control. Ensure you have Git installed and configured.  

- **Go**: Required for compiling OP Stack components. Make sure you have the correct version to avoid build issues.  

- **Node.js**: Use the latest LTS version  to avoid compatibility issues. I recommend using `nvm` to manage multiple Node.js versions efficiently.  

- **pnpm**: A fast and efficient package manager for Node.js projects. It's used to install dependencies within the OP Stack monorepo.  

- **Foundry**: A toolkit for smart contract development. Use the scripts provided in the monorepo's `package.json` to manage Foundry versions and ensure consistency. Clone the monorepo before proceeding.  

- **Make**: Automates build and setup processes. Many scripts rely on `make`, so ensure it's installed before running setup commands.  

- **jq**: A lightweight command line tool for parsing JSON data. Required for handling configuration files and outputs in scripts.  

- **direnv**: Used to automatically load environment variables from `.envrc` files, reducing the need for manual exports. Ensure `direnv` is properly integrated into your shell by following the setup guide and modifying your shell config (`~/.bashrc` or `~/.zshrc`).  



### Sepolia Node Access  

You'll deploy an OP Stack Rollup using **Sepolia** as the Layer 1 (L1) blockchain. While other EVM compatible networks can be used, Sepolia is recommended to minimize errors. Access can be obtained through a provider like **Alchemy** (easier) or by running your own Sepolia node (more complex).  

### Building from the Source code  

Instead of using Docker, you'll build the OP Stack chain from **source code**. While this adds extra setup steps, it allows for easier customization of the stack's behavior. 

### Step 1. Building the Optimism monorepo

 **i. Clone the Optimism Monorepo**  
   ```bash
   cd ~/Desktop
   git clone https://github.com/ethereum-optimism/optimism.git   
   ```
   
   
  **ii. Enter the Optimism Monorepo**
   ```bash
   cd optimism  
   ```
**iii. Check out the correct branch**
   ```bash
git checkout tutorials/chain
```
 **iv. run the command to Check your dependencies**
 Execute the following script to confirm that you have all the required versions installed. If the versions are incorrect, you might encounter errors
   ```bash
./packages/contracts-bedrock/scripts/getting-started/versions.sh
```

 **v. Install the dependencies by running**
   ```bash
pnpm install
```

 **vi. Build the various packages inside of the Optimism Monorepo**
   ```bash
make op-node op-batcher op-proposer
pnpm build
```

![image](https://hackmd.io/_uploads/SyPPEibayl.png)

After a successful build, you should see a similar output. If you encounter an error during the build, make sure your dependencies are up to date.

### Step 2. Building the op-geth

 **i. Clone op-geth by running the command bellow**  
   ```bash
   cd ~/Desktop
git clone https://github.com/ethereum-optimism/op-geth.git
   ```
   
 **ii. Enter the op-geth dir**  
   ```bash
cd op-geth
```

 **iii. Enter the op-geth dir**  
   ```bash
make geth
```
### Step 3. Configuring Your Environment Variables for Deployment
  
   **i. Enter the Optimism Monorepo**
   ```bash
cd ~/Desktop/optimism
```
 **ii. Copy the sample environment variable file**
   ```bash
cp .envrc.example .envrc
```
 **iii. Open up the envrc file and fill out the following variables**

##### Environment Variables

| Name of variable | It's  Description                                                                 |
|------------------|-----------------------------------------------------------------------------|
| L1_RPC_URL   | The URL of your Layer1 node, a Sepolia node            |
| L1_RPC_KIND  | The type of L1 RPC you're connecting to. eg.  infura,alchemy parity and e.t.c. |

### Step 3. Generating addresses


#### Required Addresses and Their Roles

You'll need four addresses and their private keys to set up the chain:

- The **Admin** address can upgrade contracts.
- The **Batcher** address sends Sequencer transaction data to L1.
- The **Proposer** address sends L2 transaction results (state roots) to L1.
- The **Sequencer** address signs blocks on the p2p network.

 **i. Enter the Optimism Monorepo**  
   ```bash
cd ~/Desktop/optimism
```

 **ii. Generate new addresses**  
   ```bash
./packages/contracts-bedrock/scripts/getting-started/wallets.sh
```
The wallets.sh tool should not be used for production deployments. For deploying an OP Stack-based chain in production    

Check the output
**iii. Check the output**  
   ```bash
./packages/contracts-bedrock/scripts/getting-started/wallets.sh
```
Make sure that you see output that looks something like the following:

![image](https://hackmd.io/_uploads/SkVPH2W6Jg.png)


 **iv. Save the addresses**  
 Store the addresses by copying the output from the previous step and pasting it into your .envrc file as instructed

**v Fund the addresses**
You will need to send ETH to the Admin, Proposer, and Batcher addresses. The required amount of ETH depends on the L1 network you're using. The Sequencer address does not require any ETH, as it does not send transactions.

When using Sepolia, it's recommended to fund the addresses with the following amounts:

- Admin => 0.5 Sepolia ETH
- Proposer => 0.2 Sepolia ETH
- Batcher => 0.1 Sepolia ETH

### Step 4. Load the environment variables

 **i. run the commant in your terminal**  
  You'll need to allow direnv to read this file and load the variables into your terminal using the command
   ```bash
direnv allow
```
When you modify the .envrc file, direnv automatically unloads its environment settings. To reapply the changes, you execute the  command after each modification.
 **ii. Confirming that the variables were loaded**  
![image](https://hackmd.io/_uploads/SyXrmefpkg.png)

After executing direnv allow, you should observe output similar to the screenshot above. If you don't observe this output, it likely indicates that direnv isn't configured correctly.Ensure Proper Configuration and run the command again

### Step 5. Network configuration
After building both repositories, return to the Optimism Monorepo to configure your chain. The chain's configuration is located within the contracts-bedrock package as a JSON file

 **i. Enter the Optimism Monorepo**  
   ```bash
cd ~/Desktop/optimism
```
**ii. To navigate to the contracts-bedrock package within the Optimism Monorepo, run the command bellow**
 ```bash
cd packages/contracts-bedrock
```

**iii. Install the Foundry dependencies**
   ```bash
forge install
```
**iv. Generate the configuration file**
To create the getting-started.json configuration file within the deploy-config directory, execute the following script

  ```bash
./scripts/getting-started/config.sh
```
This script will generate the necessary configuration file for your chain setup.

### **6.Deploying the L1 contracts**
I'm going to be using op-deployer. The op-deployer tool streamlines the process of creating two essential configuration files for your network

- genesis.json: This file initializes the execution client, known as op-geth.

- rollup.json: This file sets up the consensus client, referred to as op-node.

These configuration files are vital for properly setting up and running the network components


**i. Install the op-deployer**
To build and initialize the op-deployer tool for deploying OP Stack chains, follow these steps.
Begin by cloning the Optimism repository from GitHub into a directory named op-deployer-source in your Destop
   ```bash
cd ~/Desktop
git clone https://github.com/ethereum-optimism/optimism.git op-deployer-source
```

**ii. Navigate to the op-deployer Directory**
  ```bash
cd op-deployer-source/op-deployer
```
**iii. Build the op-deployer Binary**
  ```bash
go build -o ~/Desktop/op-deployer/bin/op-deployer ./cmd/op-deployer
```
**iv. Make the Binary Executable**
 ```bash
chmod +x ~/Desktop/op-deployer/bin/op-deployer
```
**v. Initialize the Deployment Configuration**
 ```bash
cd ~/Desktop/op-deployer
./bin/op-deployer init --l1-chain-id 11155111 --l2-chain-ids 42069 --workdir .deployer
```
![image](https://hackmd.io/_uploads/HJzsj-Gayx.png)
The screenshot above shows a Successfully initialization.

- --l1-chain-id 11155111: Specifies the chain ID of the Layer 1 network (e.g., Sepolia).
- --l2-chain-ids 42069: Defines the chain ID for your Layer 2 network.(it can also be 11155112)
- --workdir .deployer: Sets the working directory for the deployment configuration files.
After executing these steps, the .deployer directory will contain the necessary configuration files (intent.toml and state.json) for deploying your OP Stack chain

## 7. Generate the L2 genesis file and rollup file
Once the Layer 1 (L1) contracts are deployed, you can create the Layer 2 (L2) genesis and rollup configuration files by examining the state.json file generated by the deploye
 ```bash
./bin/op-deployer inspect genesis --workdir .deployer <L2_CHAIN_ID> > .deployer/genesis.json
./bin/op-deployer inspect rollup --workdir .deployer <L2_CHAIN_ID> > .deployer/rollup.json
```
The genesis.json file is essential for initializing your execution client, such as op-geth, by providing the initial state and parameters of your blockchain network. Similarly, the rollup.json file is used to configure your consensus client, like op-node, detailing the rollup-specific settings necessary for network consensus operations.

Once you have genesis.json and rollup.json:
- Initialize op-geth using genesis.json.
- Configure op-node with rollup.json.
## 8. Initialize op-geth
You're almost ready to run your OP Stack chain! To proceed, you'll need to initialize op-geth and import the Sequencer's private key, which will be used to sign new blocks.
**i. Navigate to the op-geth directory**
 ```bash
cd ~/Desktop/op-geth
```

**ii. Create a Data Directory**
 ```bash
mkdir datadir
```
**iii. Build the op-geth binary**
 ```bash
make geth
```

**iii. Initialize op-geth with the Genesis File**
 ```bash
build/bin/geth init --state.scheme=hash --datadir=datadir genesis.json
```
To avoid encountering a "no such file or directory" error during the op-geth initialization, ensure that the genesis.json file is located in the specified directory. If the file is missing or incorrectly placed, op-geth won't be able to initialize properly.
## 9. Start op-geth
Start op-geth, your Execution Client. Transactions will appear once you start the Consensus Client in the next step

**i. Open up a new terminal and Navigate to the op-geth directory**
 ```bash
cd ~/Desktop/op-geth
```

**ii. Run op-geth**
When operating op-geth as a Sequencer node, it's advisable to use the --gcmode=archive flag. This configuration enables the node to retain the entire historical state of the blockchain, which is essential for the op-proposer component to access comprehensive state data.
run the command bellow
 ```bash
 ./build/bin/geth \
  --datadir ./datadir \
  --http \
  --http.corsdomain="*" \
  --http.vhosts="*" \
  --http.addr=0.0.0.0 \
  --http.api=web3,debug,eth,txpool,net,engine \
  --ws \
  --ws.addr=0.0.0.0 \
  --ws.port=8546 \
  --ws.origins="*" \
  --ws.api=debug,eth,txpool,net,engine \
  --syncmode=full \
  --gcmode=archive \
  --nodiscover \
  --maxpeers=0 \
  --networkid=42069 \
  --authrpc.vhosts="*" \
  --authrpc.addr=0.0.0.0 \
  --authrpc.port=8551 \
  --authrpc.jwtsecret=./jwt.txt \
  --rollup.disabletxpoolgossip=true
```
![image](https://hackmd.io/_uploads/SJD2nfM6Je.png)
Your output shoud be similar to this.
- Before starting op-geth, ensure it has been properly initialized as outlined in the previous section. Skipping this step can lead to startup issues between op-geth and op-node. Proper initialization is crucial for seamless interaction between these components
## 10 Start op-node
After launching op-geth, the Execution Client, the next step is to start op-node, which serves as the Consensus Client in the OP Stack. In this architecture, the Consensus Client (op-node) coordinates with the Execution Client (op-geth) through the Engine API, similar to the interaction between consensus and execution layers in Ethereum

**i. Open up a new terminal and Navigate to the op-node directory**

 ```bash
cd ~/Desktop/optimism/op-node
```

**ii. Run op-node**

 ```bash
 ./bin/op-node   --rollup.config=$(pwd)/rollup.json   --l1=$L1_RPC_URL   --l1.beacon=https://ethereum-sepolia-beacon-api.publicnode.com   --l2=ws://localhost:8546   --l2.jwt-secret=$(pwd)/jwt-secret.txt   --syncmode=execution-layer   --p2p.priv.path=$(pwd)/opnode_p2p_priv.txt   --p2p.peerstore.path=$(pwd)/opnode_peerstore_db
```
![image](https://hackmd.io/_uploads/ryl4e7Mpkg.png)

This shows that op-node is running

- common errors
If you encounter an error like
![image](https://hackmd.io/_uploads/ByGjGQzTyx.png)

This error is likely caused by recent changes in the repository. If you encounter it, switch to the develop branch and follow the updated steps for running op-node. 

## 11. Start op-batcher
The op-batcher takes transactions from the Sequencer and sends them to Layer 1. Once these transactions are included in a finalized L1 block, they're officially part of the blockchain. It's crucial to fund the Batcher address with at least 1 Sepolia ETH so it always has enough gas for its operations, and you should monitor its balance since high transaction volume can quickly use up its funds

**i. Open up a new terminal and Navigate to the op-batcher directory**
```bash
cd ~/Desktop/optimism/op-batcher
```
**ii. Run op-batcher**
```bash
./bin/op-batcher   --l2-eth-rpc=http://localhost:8545   --rollup-rpc=http://localhost:9545   --poll-interval=1s   --sub-safety-margin=6   --num-confirmations=1   --safe-abort-nonce-too-low-count=3   --resubmission-timeout=30s   --rpc.addr=0.0.0.0   --rpc.port=8548   --rpc.enable-admin   --max-channel-duration=25   --l1-eth-rpc=$L1_RPC_URL   --private-key=$GS_BATCHER_PRIVATE_KEY
```

![image](https://hackmd.io/_uploads/ryIkvQzake.png)
When the command completes successfully, you'll see output that looks similar to the one above

## 12. Start the op-proposer
Next, launch the op-proposer, which is responsible for calculating and proposing updated state roots based on new transactions. This component is critical as it ensures that all state changes on your chain are accurately recorded and finalized

**i. Open up a new terminal and Navigate to the op-proposer directory**
```bash
cd ~/Desktop/optimism/op-proposer
```

**ii. Run the op-proposer**
```bash
./bin/op-proposer \
  --poll-interval=12s \
  --rpc.port=8560 \
  --rollup-rpc=http://localhost:9545 \
  --l2oo-address=$(cat ../packages/contracts-bedrock/deployments/getting-started/.deploy | jq -r .L2OutputOracleProxy) \
  --private-key=$GS_PROPOSER_PRIVATE_KEY \
  --l1-eth-rpc=$L1_RPC_URL
```

![image](https://hackmd.io/_uploads/HywA5mGa1e.png)
- this shows that op-proposer is working successfully

Congratulations! Your OP Stack Rollup is now fully operational, and your Sequencer node is running at http://localhost:8545. This means your network is up and ready to process transactions

**connect your wallet**
Connecting your wallet to this chain is just like adding any other EVM compatible network. You will need to enter specific network details such as the RPC URL, chain ID, currency symbol, and any other required settings into your wallet’s network configuration. Once you've updated these settings, your wallet will interact with the network just as it does with Ethereum or other EVM chains. See the screenshot below for an example of how to configure your wallet.
![image](https://hackmd.io/_uploads/rJff2mzpye.png)

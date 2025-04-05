---
weight: 40
title: "Solving your first exercise"
description: "Validate your first exercise without the headache"
icon: "emoji_objects"
date: "2025-04-02T08:00:00+02:00"
lastmod: "2025-04-02T08:00:00+02:00"
draft: false
toc: true
---

This guide will walk you through solving the "Variable Modification Challenge", the first exercise in Hack The Chain. This is a simple exercise designed to get you familiar with the basics of interacting with smart contracts on the blockchain.

Note : This tutorial assumes the user has already set up Metamask and has some Sepolia ETH as explained in the [setup guide](/docs/setting_up).

## Understanding the Challenge

### Exercise Overview:

  * **Title**: Variable Modification Challenge
  * **Difficulty**: Easy
  * **Objective**: Change the value of the `storedData` variable from its initial value (0) to any other value.

### The Contract Code:

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

contract SimpleStorage {
    uint256 private storedData;
    address public owner;

    constructor() {
        storedData = 0;
        owner = msg.sender;
    }

    function setVariable(uint256 x) public {
        storedData = x;
    }

    function getVariable() public view returns (uint256) {
        return storedData;
    }
}
```

### How the Verification Works:

The platform will check if you've successfully modified the `storedData` variable by calling the `getVariable()` function and verifying that its value is greater than 0.

## Solving with Remix IDE

Remix is a popular web-based IDE for Solidity development. Here's how to solve the exercise using Remix:

### Step 1: Set Up Remix

1. Open [Remix IDE](https://remix.ethereum.org/)
2. Create a new file called `SimpleStorage.sol` in the `contracts` folder.
3. Copy and paste the contract code provided above into your new file

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

contract StorageCaller {
    // ADDRESS OBTAINED AFTER CONTRACT DEPLOYMENT
    address public simpleStorageAddress = 0x93980988c77c1f1c0d86D93a0485c4D7dd26e10D;

    function callSetVariable(uint256 newValue) public {
        // Direct call with function signature
        (bool success, ) = simpleStorageAddress.call(
            abi.encodeWithSignature("setVariable(uint256)", newValue)
        );
        require(success, "Call failed");
    }

    function checkStoredValue() public view returns (uint256) {
        // Read-only call (staticcall)
        (bool success, bytes memory data) = simpleStorageAddress.staticcall(
            abi.encodeWithSignature("getVariable()")
        );
        require(success, "Call failed");
        return abi.decode(data, (uint256));
    }
}
```

![Remix Contract](https://hackthechain.s3.nl-ams.scw.cloud/images/solving_your_first_exercise/remix_contract.jpg)

### Step 2: Compile the Contract

1. Click on the "Solidity Compiler" tab in the left sidebar
2. Ensure that the compiler version is set to at least 0.8.0
3. Click "Compile SimpleStorage.sol"
4. Verify that there are no errors in the compilation output

![Compile Remix Contract](https://hackthechain.s3.nl-ams.scw.cloud/images/solving_your_first_exercise/compile_contract.jpg)

### Step 3: Deploy to Sepolia Testnet

1. Click on the "Deploy & Run Transactions" tab in the left sidebar
2. Change the environment dropdown to "Injected Provider - MetaMask"
3. Ensure your MetaMask is connected to Sepolia Testnet
4. Click "Deploy"
5. Confirm the transaction in the MetaMask popup
6. Wait for the transaction to be confirmed on the blockchain

![Deploy Remix Contract](https://hackthechain.s3.nl-ams.scw.cloud/images/solving_your_first_exercise/deploy_contract.jpg)

### Step 4: Interact with the Contract

1. Once deployed, the contract will appear under the "Deployed Contracts" section
2. Expand the contract to see its functions
3. Find the `callSetVariable` function
4. Enter a value greater than 0 (e.g., 42) in the input field
5. Click the "transact" button
6. Confirm the transaction in MetaMask
7. Wait for the transaction to be confirmed

![Remix - Call Set Variable](https://hackthechain.s3.nl-ams.scw.cloud/images/solving_your_first_exercise/remix_set_variable.jpg)

### Step 5: Verify the Change

1. Find the `checkStoredValue` function under the deployed contract
2. Click the "call" button (blue button)
3. Verify that the returned value is the one you set (e.g., 42)

### Step 6: Complete the Exercise

1. Return to the Smart Contract Auditing platform
2. Enter the deployed contract address
3. Click "Check Exploit"
4. The platform should indicate that you've successfully solved the exercise

## Solving with Foundry (Local Development)

Foundry is a powerful Ethereum development toolkit for more advanced development. Here's how to solve the exercise using Foundry:

### Step 1: Install Foundry

If you haven't installed Foundry yet, run:

```bash
curl -L https://foundry.paradigm.xyz | bash
foundryup
```

### Step 2: Create a New Project

```bash
mkdir variable-modification
cd variable-modification
forge init
```

### Step 3: Create the Contract File

Create a new file at `src/SimpleStorage.sol` and add the contract code:

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

contract SimpleStorage {
    uint256 private storedData;
    address public owner;

    constructor() {
        storedData = 0;
        owner = msg.sender;
    }

    function setVariable(uint256 x) public {
        storedData = x;
    }

    function getVariable() public view returns (uint256) {
        return storedData;
    }
}
```

### Step 4: Create a Deployment Script

Create a new file at `script/Deploy.s.sol`:

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

import "forge-std/Script.sol";
import "../src/SimpleStorage.sol";

contract DeployScript is Script {
    function run() external {
        uint256 deployerPrivateKey = vm.envUint("PRIVATE_KEY");

        vm.startBroadcast(deployerPrivateKey);

        new SimpleStorage();

        vm.stopBroadcast();
    }
}
```

### Step 5: Create an Interaction Script

Create a new file at `script/Interact.s.sol`:

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

import "forge-std/Script.sol";
import "../src/SimpleStorage.sol";

contract InteractScript is Script {
    function run() external {
        uint256 deployerPrivateKey = vm.envUint("PRIVATE_KEY");
        address contractAddress = vm.envAddress("CONTRACT_ADDRESS");

        vm.startBroadcast(deployerPrivateKey);

        SimpleStorage simpleStorage = SimpleStorage(contractAddress);

        // Set the variable to 42
        simpleStorage.setVariable(42);

        // Verify the change
        uint256 value = simpleStorage.getVariable();
        console.log("New value:", value);

        vm.stopBroadcast();
    }
}
```

### Step 6: Set Up Environment Variables

Create a `.env` file:

```
// Prefix your metamask private key with `0x`
PRIVATE_KEY="your_private_key_here"

// You can use "https://sepolia.drpc.org"
//   > https://rpc.info/ethereum-sepolia
SEPOLIA_RPC_URL="your_sepolia_rpc_url_here"

// Needed only if you use `--verify` on step 7
ETHERSCAN_API_KEY="etherscan_api_key"
```

**Important security note:** Never commit your private key to version control!

### Step 7: Deploy the Contract

```bash
source .env
forge script script/Deploy.s.sol:DeployScript --rpc-url $SEPOLIA_RPC_URL --broadcast --verify
```

![Forge Deploy](https://hackthechain.s3.nl-ams.scw.cloud/images/solving_your_first_exercise/forge_deploy.jpg)

### Step 8: Interact with the Contract

Set the contract address in your environment variables:

```bash
// The value of the target contract deployed from the site
export CONTRACT_ADDRESS=the_target_contract_address
```

Then run the interaction script:

```bash
forge script script/Interact.s.sol:InteractScript --rpc-url $SEPOLIA_RPC_URL --broadcast
```

![Forge Interact](https://hackthechain.s3.nl-ams.scw.cloud/images/solving_your_first_exercise/forge_interact.jpg)

### Step 9: Complete the Exercise

1. Return to the Smart Contract Auditing platform
2. Enter the deployed contract address
3. Click "Check Exploit"
4. The platform should indicate that you've successfully solved the exercise

## Understanding What You Did

In this exercise, you:

1. Deployed a simple smart contract with a state variable `storedData` initialized to 0
2. Called the `setVariable` function to change that value to something non-zero
3. Verified the change was successful by checking the return value of `getVariable`

While this is a very simple exercise, it teaches the fundamental concept of modifying state in a blockchain. In more complex exercises and real-world scenarios, understanding how to modify state is crucial for both legitimate interactions and potential exploits.

## Next Steps

Now that you've completed your first exercise, consider trying more challenging exercises to learn !

Each exercise builds on your knowledge and prepares you for real-world smart contract auditing.

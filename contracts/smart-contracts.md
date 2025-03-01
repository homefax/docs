# HomeFax Smart Contracts Documentation

This document provides an overview of the HomeFax smart contracts, their functionality, and how to interact with them.

## Overview

The HomeFax smart contract system is built on the Base blockchain (Coinbase's Layer 2 solution) and provides a secure, transparent way to store and verify property records and reports.

## Contract Architecture

The main contract is `HomeFax.sol`, which implements the following functionality:

- Property record creation and management
- Property report creation and management
- Report purchasing and access control
- Verification of properties and reports

## HomeFax.sol

### Key Features

1. **Property Management**

   - Create property records with address, city, state, and zip code
   - Update property information
   - Verify properties (admin only)
   - Retrieve property details

2. **Report Management**

   - Create reports linked to properties
   - Store report content securely (via IPFS hash)
   - Set pricing for reports
   - Verify reports (admin only)

3. **Access Control**

   - Purchase reports using cryptocurrency
   - Track report purchases
   - Control access to report content based on ownership or purchase status

4. **Ownership and Security**
   - Inherits from OpenZeppelin's Ownable for admin functions
   - Uses ReentrancyGuard to prevent reentrancy attacks
   - Implements proper access control modifiers

### Data Structures

#### Property

```solidity
struct Property {
    uint256 id;
    string propertyAddress;
    string city;
    string state;
    string zipCode;
    address owner;
    uint256 createdAt;
    uint256 updatedAt;
    bool isVerified;
}
```

#### Report

```solidity
struct Report {
    uint256 id;
    uint256 propertyId;
    string reportType; // "inspection", "title", "renovation", etc.
    string reportHash; // IPFS hash of the report content
    address creator;
    uint256 price;
    uint256 createdAt;
    bool isVerified;
}
```

### Key Functions

#### Property Functions

```solidity
function createProperty(
    string memory propertyAddress,
    string memory city,
    string memory state,
    string memory zipCode
) external returns (uint256)
```

Creates a new property record and returns its ID.

```solidity
function updateProperty(
    uint256 propertyId,
    string memory propertyAddress,
    string memory city,
    string memory state,
    string memory zipCode
) external propertyExists(propertyId) onlyPropertyOwner(propertyId)
```

Updates an existing property record.

```solidity
function verifyProperty(uint256 propertyId) external propertyExists(propertyId) onlyOwner
```

Verifies a property record (admin only).

```solidity
function getProperty(uint256 propertyId) external view propertyExists(propertyId) returns (Property memory)
```

Retrieves property details.

#### Report Functions

```solidity
function createReport(
    uint256 propertyId,
    string memory reportType,
    string memory reportHash,
    uint256 price
) external propertyExists(propertyId) returns (uint256)
```

Creates a new report for a property and returns its ID.

```solidity
function verifyReport(uint256 reportId) external reportExists(reportId) onlyOwner
```

Verifies a report (admin only).

```solidity
function purchaseReport(uint256 reportId) external payable reportExists(reportId) nonReentrant
```

Purchases access to a report.

```solidity
function getReport(uint256 reportId) external view reportExists(reportId) returns (Report memory)
```

Retrieves report details.

```solidity
function getReportContent(uint256 reportId) external view reportExists(reportId) returns (string memory)
```

Retrieves the report content hash if the user has purchased access.

#### Query Functions

```solidity
function getUserProperties(address user) external view returns (uint256[] memory)
```

Gets all properties owned by a user.

```solidity
function getPropertyReports(uint256 propertyId) external view propertyExists(propertyId) returns (uint256[] memory)
```

Gets all reports for a property.

```solidity
function hasPurchasedReport(address user, uint256 reportId) external view returns (bool)
```

Checks if a user has purchased a report.

### Events

```solidity
event PropertyCreated(uint256 indexed id, string propertyAddress, address indexed owner);
event PropertyUpdated(uint256 indexed id, string propertyAddress, address indexed owner);
event PropertyVerified(uint256 indexed id, string propertyAddress);
event ReportCreated(uint256 indexed id, uint256 indexed propertyId, string reportType, address indexed creator);
event ReportVerified(uint256 indexed id, uint256 indexed propertyId);
event ReportPurchased(uint256 indexed reportId, address indexed buyer, uint256 price);
```

## Deployment

The HomeFax contract is deployed on the Base blockchain. The current deployment addresses are:

- **Base Goerli (Testnet)**: `0x0000000000000000000000000000000000000000` (placeholder)
- **Base Mainnet**: `0x0000000000000000000000000000000000000000` (placeholder)

## Interacting with the Contracts

### Using Web3.js or Ethers.js

Here's an example of how to interact with the HomeFax contract using Ethers.js:

```javascript
const { ethers } = require("ethers");
const HomeFaxABI = require("./HomeFaxABI.json");

// Connect to the provider
const provider = new ethers.providers.JsonRpcProvider(
  "https://goerli.base.org"
);

// Connect to the contract
const contractAddress = "0x0000000000000000000000000000000000000000"; // Replace with actual address
const homeFaxContract = new ethers.Contract(
  contractAddress,
  HomeFaxABI,
  provider
);

// Connect with a signer for transactions
const privateKey = "your_private_key"; // Replace with your private key
const wallet = new ethers.Wallet(privateKey, provider);
const homeFaxWithSigner = homeFaxContract.connect(wallet);

// Create a property
async function createProperty() {
  const tx = await homeFaxWithSigner.createProperty(
    "123 Blockchain Street",
    "Crypto City",
    "CA",
    "94105"
  );

  const receipt = await tx.wait();
  console.log(
    "Property created with ID:",
    receipt.events[0].args.id.toString()
  );
}

// Get property details
async function getProperty(propertyId) {
  const property = await homeFaxContract.getProperty(propertyId);
  console.log("Property details:", property);
}
```

### Using the HomeFax Frontend

The HomeFax frontend application provides a user-friendly interface for interacting with the smart contracts. Users can:

1. Connect their wallet (MetaMask, WalletConnect, etc.)
2. Create and manage properties
3. Create and purchase reports
4. View property and report details

## Security Considerations

1. **Access Control**: The contract uses modifiers to ensure only authorized users can perform certain actions.
2. **Reentrancy Protection**: The contract uses OpenZeppelin's ReentrancyGuard to prevent reentrancy attacks.
3. **Ownership**: Admin functions are protected by OpenZeppelin's Ownable contract.
4. **Input Validation**: All functions validate inputs to ensure data integrity.

## Upgradeability

The current version of the HomeFax contract is not upgradeable. Any changes to the contract would require deploying a new version and migrating data.

## Gas Optimization

The contract implements several gas optimization techniques:

- Using structs to organize data
- Using mappings for efficient lookups
- Minimizing storage operations
- Using events for off-chain indexing

## Testing

The contract includes comprehensive tests to ensure functionality and security. Run the tests using:

```bash
npx hardhat test
```

## Auditing

The HomeFax contract has not yet undergone a formal security audit. This is planned for the future before mainnet deployment.

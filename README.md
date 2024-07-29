# Degen-Token
This Solidity contract, DegenToken, is an ERC20 token with added features like minting, burning, and transferring tokens securely. It uses OpenZeppelin libraries for enhanced functionality and security. Users can redeem tokens for predefined store items, and the owner manages critical operations. The token has zero decimals and can be paused for additional control.

## Description
The `DegenToken` contract includes the following features:

- **Minting Tokens**: The contract owner can mint new Degen (DGN) tokens to any address using the `mint` function.
- **Burning Tokens**: Users can burn their tokens using the `burnTokens` function.
- **Transferring Tokens**: Players can transfer their tokens to others using the `transferTokens` function.
- **Redeeming Tokens**: Players can redeem their tokens for items in the in-game store using the `redeemTokens` function.
- **Checking Token Balance**: Players can check their token balance using the `getBalance` and `getBalanceOf` functions.
- **Pausing the Contract**: The contract owner can pause and unpause the contract to halt and resume all token transfers and minting.

## Getting Started

### Installing
To get started with this project, you'll need a Solidity development environment. We recommend using Remix, an online Solidity IDE. Follow these steps to set up your environment:

Go to Remix: Visit the Remix IDE.
Create a New File: Click the "+" icon in the left-hand sidebar to create a new file. Save the file with a .sol extension (e.g., DegenToken.sol).
Copy and Paste the Template Code: Copy the code provided below and paste it into your new file.
Executing Program
To run this program, follow these steps:

Copy the following code into your file:

    // SPDX-License-Identifier: GPL-3.0
    
    pragma solidity >=0.8.2 <0.9.0;
    
    import "@openzeppelin/contracts/token/ERC20/ERC20.sol";
    import "@openzeppelin/contracts/access/Ownable.sol";
    import "@openzeppelin/contracts/token/ERC20/extensions/ERC20Burnable.sol";
    import "@openzeppelin/contracts/security/Pausable.sol";
    import "@openzeppelin/contracts/security/ReentrancyGuard.sol";
    import "hardhat/console.sol";
    
    // Contract definition
    contract DegenToken is ERC20, Ownable, ERC20Burnable, Pausable, ReentrancyGuard {
        // Event for item redemption
        event ItemRedeemed(address indexed user, uint256 itemId, uint256 cost);
    
      // Structure for store items
      struct StoreItem {
          uint256 itemId;
          string itemName;
          uint256 cost;
      }
  
      // Array to hold store items
      StoreItem[] public storeItems;
  
      // Constructor to initialize the token and store items
      constructor() ERC20("Degen", "DGN") Ownable(msg.sender) {
          _transferOwnership(msg.sender);
          _initializeStoreItems();
      }
  
      // Function to mint tokens, only owner can call this function
      function mint(address to, uint256 amount) public onlyOwner whenNotPaused {
          _mint(to, amount);
      }
  
      // Override decimals function to return 0
      function decimals() public pure override returns (uint8) {
          return 0;
      }
  
      // Function to transfer tokens with non-reentrancy protection
      function transferTokens(address receiver, uint256 value) external whenNotPaused nonReentrant {
          require(balanceOf(msg.sender) >= value, "Insufficient Degen Tokens to complete the transfer.");
          approve(msg.sender, value);
          transferFrom(msg.sender, receiver, value);
      }
  
      // Function to burn tokens with non-reentrancy protection
      function burnTokens(uint256 value) external whenNotPaused nonReentrant {
          require(balanceOf(msg.sender) >= value, "Not enough Degen Tokens to burn.");
          _burn(msg.sender, value);
      }
  
      // Function to redeem tokens for store items
      function redeemTokens(uint256 itemId) external whenNotPaused nonReentrant {
          uint256 cost;
          string memory itemName;
          
          // Get item details by itemId
          (cost, itemName) = _getItemDetails(itemId);
  
          require(balanceOf(msg.sender) >= cost, "Insufficient tokens to redeem the item.");
          _burn(msg.sender, cost);
  
          // Emit event after successful redemption
          emit ItemRedeemed(msg.sender, itemId, cost);
      }
  
      // Function to get balance of the caller
      function getBalance() external view returns (uint256) {
          return balanceOf(msg.sender);
      }
  
      // Function to get balance of a specific account
      function getBalanceOf(address account) external view returns (uint256) {
          return balanceOf(account);
      }
  
      // Function to display store items
      function showStoreItems() external pure returns (string memory) {
          return "Available Store Items:\n\
                  1. Digital Artwork: 150 Tokens\n\
                  2. Custom T-shirt: 100 Tokens\n\
                  3. Exclusive Access Pass: 75 Tokens\n\
                  4. Collectible Sticker: 50 Tokens\n\
                  5. VIP Membership: 200 Tokens";
      }
  
      // Internal function to initialize store items
      function _initializeStoreItems() internal {
          storeItems.push(StoreItem(1, "Digital Artwork", 150));
          storeItems.push(StoreItem(2, "Custom T-shirt", 100));
          storeItems.push(StoreItem(3, "Exclusive Access Pass", 75));
          storeItems.push(StoreItem(4, "Collectible Sticker", 50));
          storeItems.push(StoreItem(5, "VIP Membership", 200));
      }
  
      // Internal function to get item details by itemId
      function _getItemDetails(uint256 itemId) internal view returns (uint256, string memory) {
          for (uint i = 0; i < storeItems.length; i++) {
              if (storeItems[i].itemId == itemId) {
                  return (storeItems[i].cost, storeItems[i].itemName);
              }
          }
          revert("Invalid item ID.");
      }
  
      // Function to pause the contract, only owner can call this
      function pause() public onlyOwner {
          _pause();
      }
  
      // Function to unpause the contract, only owner can call this
      function unpause() public onlyOwner {
          _unpause();
      }
    }


### Compile the Code
Open Solidity Compiler: Click the "Solidity Compiler" tab in the left sidebar.
Set Compiler Version: Ensure the compiler is set to a compatible version (e.g., 0.8.20).
Compile: Click "Compile DegenToken.sol".
Deploy the Contract
Open Deploy & Run Transactions: Click the "Deploy & Run Transactions" tab in the left sidebar.
Select Contract: Choose "DegenToken" from the dropdown menu.
Deploy: Click the "Deploy" button.

### Interact with the Contract

Once deployed, you can interact with the contract using Remix’s interface. Key functions include:

mint: Mint new DGN tokens to a specified address (owner only).
burnTokens: Burn DGN tokens from your balance.
transferTokens: Transfer DGN tokens to another address.
redeemTokens: Redeem DGN tokens for store items by item ID.
getBalance: Get your DGN token balance.
getBalanceOf: Get the DGN token balance of any address.
showStoreItems: Display available store items and their costs.
pause: Pause all token transfers and minting (owner only).
unpause: Resume token transfers and minting (owner only).

## Authors
Metacrafter Student Ivanne Cres Yabut https://x.com/ivanne_cres

## License
This project is licensed under the author's License - see the LICENSE.md file for details


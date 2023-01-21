### 1. User's Orders can be  canceled  by anyone and their ETH can be stolen

When a user makes a WETH order from the router. After calculations, an id is returned. And the router does not save the offer's originator.
Using cancelForETH function, any attacker can call the cancel function by passing the order ID and get all the unfilled funds from the order.

**Recommendation**: Add a require  check that only the user who created the order can cancel it will solve the issue.

---
### 2. Double transfer in the `transferAndCall` function

The transferAndCall function of ERC677 was incorrect. Instead of a single transfer, the function was performing transferring twice.
We can see that both super.transfer(_to, _value); and _transfer(msg.sender, _to, _value); are doing the same thing - transfering _value of tokens from msg.sender to _to.

**Recommendation**: Removing any of both methods of transfer from transferAndCall function will mitigate the vulnerability. 

---
### 3. Unchecked Return Value from "ecrecover"

The function uses ecrecover for signature verification. In the Solidity documentation, it says it will “return zero on error”. So, if it had an issue, it would return 0.
 someone could send an invalid signature, which would result in a zero address returned from "ecrecovery", and the MRC20 contract would send them tokens without a lack of checking for "from" balance.

**Recommendation**: As a fix to Vulnerability, Polygon removed the transferWithSig function.

---
### 4. EIP-712 signatures can be re-used

"buyFromPrivateSaleFor" method has no checks to determine if the EIP-712 signature has been used before.

**Recommendation**: Use a nonce while creating a  signature. When a nonce is injected into a signature, it makes it impossible for re-use, assuming of 
course the nonce feature is done correctly.

---
### 5. Use safeCast for changing types

the _mint function takes an argument "_fCashAmount" of type uint256. Now, in the function,  the value of _fCashAmount is downcasted to uint88.
If value that is greater than uint88 is passed for "_fCashAmount" into the _mint function, downcasting it to uint88 will silently overflow.

**Recommendation**: Consider using OpenZeppelin's SafeCast library to prevent unexpected overflows when casting from uint256.

---
### 6. BLOCK_PERIOD IS INCORRECT

In config.sol of zkSync contracts, The BLOCK_PERIOD is set to 13 seconds in Config.sol.
Ethereum  Since moving to Proof-of-Stake (PoS) after the Merge, block times on Ethereum are fixed at 12 seconds per block (slots).
 By using a  block time of 13 sec, a txn in the Priority Queue expires 5.5 hours earlier than expected.

**Recommendation**: Change the block period to 12 seconds

---
### 7. Insufficient validation of Chainlink Oracle data feed

When fetching prices from latestRoundData, there is not enough validation that ensures the price is not stale.
The getPrice function fetches the price from Chainlink using latestRoundData, there are checks to make sure the price is not negative.

**Recommendation**: Checking the returned answer is not 0. Verify result is within an allowed margin of freshness by checking updatedAt.
 Verify answer is indeed for the last known round.

---
### 8. 88mph Function Initialization Bug

The init() function, which is used to initialize the NFT contract on 88mph’s platform, was missing an access control modifier 'onlyOwner'.  
The result of this unprotected function would have allowed a malicious attacker to have access to any user’s NFTs and deposits.

**Recommendation**: 1. Add an onlyOwner modifier
2. Add an initializer modifier

---
### 10. Sandwich attack due to hardcoded slippage

The _swapUstToUnderlying function is used to swap Ust to underlying tokens. The argument exchange_underlying specifies the minimum number of underlying to be returned from the swap. Currently, this value is set to 0, so the function is subject to a sandwich attack.

**Recommendation**: Add a slippage argument to the functions instead of hard-coded  0. 

---
### 11. Initialize function can be invoked multiple times.

The implementation contract can initialize the _controller address multiple times on Managed contract even though it should only be allowed once.
This means a compromised implementation can reinitialize the contract above and become the owner to complete the privilege escalation then drain the user's fund.  

**Recommendation**: Use the  initializer modifier to protect the function from being reinitiated

---
### 12. A Typo leading to locking of Funds

In Solidity, "==" checks if the value of two operands are equal or not. And "=" is used to set a certain value to variables.  
 If a user calls unlock() multiple times, insurances[_id].amount will be subtracted multiple times. It will result in 'lockedAmount' being way smaller.
 
**Recommendation**: Change "insurances[_id].status == false;" to "insurances[_id].status = false;"

---
### 13. Centralisation RIsk: Owner Of RoyaltyVault Can Take All Funds

The owner of RoyaltyVault can set _platformFee to any arbitrary value (e.g. 100% = 10000) as there exists no upper limit while setting fees for the platform.
As a result the owner can steal the entire contract balance and any future balances avoiding the splitter.
 
**Recommendation**: This issue may be mitigated by adding a maximum value for the _platformFee. For example 5% or 10%.

---
### 14. Call Return is executed before 'require' check

![image](https://user-images.githubusercontent.com/104667778/212737464-2654665c-3be1-4a2d-a766-99d8ea42c1e1.png)

In _withdrawFromYieldPool function, The code checks transaction success after returning the transfer value and finishing execution.
Therefore, the return value from the call is never checked to see if the call succeeded. So, Regardless of the success or failure of the call, the function will exit as if everything succeeded.
 
**Recommendation**: Add the 'require' check for the call before returning the value.

---
### 15. Reentrancy Vulnerability due to violation of the Check-Effect-Interaction Pattern.

![image](https://user-images.githubusercontent.com/104667778/212737644-01335e42-9666-4251-9ded-2d5363798275.png)

The key to the vulnerability is that the `doTransferOut` method in the CEtherDelegator contract uses the call method to transfer tokens.
As there is no gas limit in the call method,  it can be exploited to implement reentrancy attacks.
 
**Recommendation**: Follow check-effect-interaction pattern in borrowFresh function.

---
### 17. Lack of access control in the parameterize function of proposal contracts

In BurnFlashStakeDeposit.sol the parameterize() function can be called by anyone setting all the Parameters state in the contract.
This function deals with important governance decisions being executed and should only be called by the DAO.

 
**Recommendation**: Add access modifiers to proposal details change, say anyone can create one, but only the author can subsequently modify it.

---
### 18. Reentrancy Guard Lacking in mint function.

In CollateralizedDebt.sol, the mint() function calls _safeMint() which has a callback to the "to" address argument.
Functions with callbacks should have reentrancy guards in place for protection against reentrancy attacks.
 
**Recommendation**: Add a reentrancy guard modifier on the mint() function in CollateralizedDebt.sol 

---
### 19.  Lender can change NFT valuation oracle without borrower permission

 When the lender updates loan parameters in the updateLoanParameters function, the function doesn't check if oracle is updated.
 So, A malicious lender could pass in his own oracle after the loan becomes outstanding, and the change would be updated without any checks.
 
**Recommendation**: Once a loan is agreed to, the oracle used should not change. Adding a check in the require statement that "params. oracle == cur. oracle" should solve it.

---
### 20.  Incorrect airdrop calculation

 The underlying cause was that the getClaimableTokenAmountAndGammaToClaim() function determines the amount of ApeCoin to claim based on the number of NFT the caller owns. It doesn’t consider how long the caller owns those NFTs. The attacker took a flash loan and borrowed BAYC tokens that can be redeemed for NFTs, and then use these NFTs to claim the AirDrop. 
 
**Recommendation**: The attack could have been avoided if the airdrop calculation had taken into consideration how long a person had to own the NFT before claiming the reward.

---
### 21. Tokens with more than 18 decimal points will cause issues

It is assumed that the maximum number of decimals for each token is 18. However,  It is possible to have tokens with more than 18 decimals like YAMv2 (24 decimals).
The contract uses solidity version 0.7.6  which is prone to overflow and underflow.
While calculating the rate in the getSellRate function, The decimal of Token is subtracted from 18. But if the token has more decimals than 18, It can result in broken code flow and underflow.
  
**Recommendation**: Make sure the code won’t fail in case the token’s decimals are  more than 18.

---

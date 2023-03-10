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
### 22. Cannot unpause exchange

The OneInchExchange.sol contract exposes a mechanism for the owner to pause the contract. This disables the swap functionality.
But the problem is that there is no corresponding mechanism to unpause the contract. This can be problematic and can shutdown the swap functionality permanently.
  
**Recommendation**: Introducing a mechanism for the owner to unpause the contract.

---
### 24. Usage of deprecated ChainLink API

According to Chainlink's documentation, the latestAnswer function is deprecated. The contracts of Gro protocol were using Chainlink’s deprecated API latestAnswer().

![image](https://user-images.githubusercontent.com/104667778/215552408-9b0d913b-b842-49cc-b44c-2162de2c505f.png)

**Recommendation**: Use the latestRoundData function to get the price instead.

---
### 25.  Lack of Access control over burn function 

Hospowise was hacked due to an access control flaw, i.e. the burn function was public and was accessible to any user. 
An attacker purchased the tokens and then used the public burn function to burn all the hospo tokens on UniSwap, inflating the token's value, and then finally swapped it for ETH until the pool was depleted.

**Recommendation**: This might have been avoided if the function had implemented access control, such as onlyOwner, or if the function was internal with proper access control logic.

---
### 26. Bad Source of Randomness

 In LuckyTigerNFT's Contract, _getRandom function is used to generate random numbers, which is called in the mint function while minting NFT.
 The _getRandom function is using block.difficulty and block.timestamp for generating random numbers.
 Now, Due to bad randomness we can predict it and mint the NFT Infinite times.
 
**Recommendation**: Use Chainlink VRF to generate random numbers.

---
### 27. Arbitrary token burn

 In the redeem function, while redeeming the function burns shares from a user-supplied address account instead of the user's account(msg.sender)
 Also the redeem() function checks the balance of msg.sender, but burns from the balance of user supplied 'o' address.
 Due to which a malicious user can burn all other's user shares by having the necessary shares on her balance.
 
**Recommendation**:Consider burning the shares from msg.sender instead

---
### 28. Users can get unlimited Votes

 In the Voting Escrow contract, When minting a token, voting rights are added to the owner's delegate using _moveTokenDelegates. Also while transferring the tokens _moveTokenDelegates is called to transfer voting rights.
 But when burning a token, the token is not removed from the delegate's list. This can can allow any attacker to keep their votes even after burning the tokens.
 
**Recommendation**: Call _moveTokenDelegates(owner,address(0)) in _burn() function

---
### 29.  Incorrect number of seconds in ONE_YEAR variable

 In HolyPaladinToken.sol the ONE_YEAR variable claims that there are 31557600 seconds in a year when this is incorrect. The correct number of seconds in a year is 31_536_000.
 
**Recommendation**: The 'ONE_YEAR' variable should be changed to 31_536_000

---
### 30.  Unnecessary precision loss in _recipientBalance()

In Steam.sol contract, Using ratePerSecond() to calculate the _recipientBalance() incurs an unnecessary precision loss.
The current formula in _recipientBalance() to calculate the vested amount (balance) incurs an unnecessary precision loss, as it includes div before multiply. 

**Recommendation**: Change the code to: "balance = elapsedTime_ * tokenAmount_ / duration"

---
### 31. Reward Manager of the Convex Base Reward Pool Can DoS processYield()

Rewards are paid out in native CRV and CVX tokens but the reward manager of the base pool may opt to add extra rewards via addExtraRewards() function.
In processYield() function, getting the value from `extraRewardsLength` function. If the value of extraRewards is big, the gas cost to execute this function is so high that the function will revert causing Denial of Service. 

**Recommendation**: Consider restricting the number of extra rewards by only iterating through the first X number of tokens in processYield().

---
### 32. Low-level transfer via call() can fail silently

The low-level functions call, delegatecall and staticcall return true as their first return value if the account called is non-existent, as part of the design of the EVM. Account existence must be checked prior to calling if needed.
In the _call() function in TimelockController.sol, a call is executed without checking for the contract existence. Therefore, transfers may fail silently.
 
**Recommendation**: Check for the account's existence prior to transferring.

---
### 33. ERC20 bridging functions do not revert on non-zero msg.value

A user wanting to bridge ERC20 tokens would lose all ETH accidentally send to the contract. The bridging facets follow the pattern of checking if the token is native or ERC20.  In case the token is ERC20, there is no check that msg.value == 0.
This leads to the ETH sent accidentally by a user being held unaccounted for in the contract and being lost for the user.
 
**Recommendation**: Consider reverting when bridging functions with non-native target are called with non-zero native amount added.

---
### 34. User can escape from paying fees

The addExcluded() function in Vader contract is used to exempt any user address to pay any fees. This function can be called by anyone, due to which anyone can map their own address.
 
**Recommendation**: Add access control modifier

---
### 35. The noContract modifier does not work as expected.

According to the docs, the noContract() Modifier ensures that non-whitelisted contracts can't interact with the LP farm.
It can be bypassed when deploying a smart contract through the smart contract's constructor call.
 
**Recommendation**: Modify the code to 'require(msg.sender == tx.origin);'

---
### 36. Sandwich attacks are possible as there is no slippage control

Swapping function in Marketplace and Lender's yield() can be sandwiched as there is no slippage control option. Trades can happen at a manipulated price and end up receiving fewer tokens than current market price dictates.
 
**Recommendation**: Consider adding minimum accepted return argument and slippage arguments to sustain the sandwich attacks to an extent. 

---
### 37. No checked success for Oracle

In removeCollateral() function, the success return of loanParams. oracle. get() is ignored. Making it possible for the loan to be liquidated unfairly with an invalid or stale price.
 
**Recommendation**: Consider adding a check for "success".

---
### 38. HolyPaladinToken.sol uses ERC20 token with a highly unsafe pattern

The transferFrom() function in ERC20.sol used by HolyPaladinToken.sol calls _transfer() first and then updates the sender allowance which is unsafe. The openZeppelin ER20.sol contract first updates the sender allowance before calling _transfer.
The transferFrom() function itself does not make any external calls so there is no exposure to reentrancy issues. But in the future if the contract is been extended in a way that enable an external call this would prove problematic.
 
**Recommendation**: Consider importing the Open Zeppelin ERC20.sol contract code directly as it is battle tested and safe code.

---
### 39. HolyPaladinToken.sol uses ERC20 token with a highly unsafe pattern

The transferFrom() function in ERC20.sol used by HolyPaladinToken.sol calls _transfer() first and then updates the sender allowance which is unsafe. The openZeppelin ER20.sol contract first updates the sender allowance before calling _transfer.
The transferFrom() function itself does not make any external calls so there is no exposure to reentrancy issues. But in the future if the contract is been extended in a way that enable an external call this would prove problematic.
 
**Recommendation**: Consider importing the Open Zeppelin ERC20.sol contract code directly as it is battle tested and safe code.

---
### 40. No upper limit for selling fees (Exit Scam)

The transferFrom() function in ERC20.sol used by HolyPaladinToken.sol calls _transfer() first and then updates the sender allowance which is unsafe. The openZeppelin ER20.sol contract first updates the sender allowance before calling _transfer.
The transferFrom() function itself does not make any external calls so there is no exposure to reentrancy issues. But in the future if the contract is been extended in a way that enable an external call this would prove problematic.
 
**Recommendation**: rug pull-detection

---
### 41. Division before multiplication

If interestPerSec is greater than 0, there will still be rounding errors due to division before multiplication. As a result, calculatedInterest() will be underestimated.

**Recommendation**: This issue may be resolved by performing the multiplication by elapsedTime before the division by the denominator or 365 days.

---
### 43. Protocol pays swap fees instead of users.

PaprController implements the buyAndReduceDebt() function, which allows users to buy Papr tokens for underlying tokens and burn them to reduce their debt.
The function allows the caller to specify a swap fee, a fee that is supposed to be collected from the caller. But the fee is collected from PaprController itself as it uses transfer instead of transferFrom on the underlying token.

![image](https://user-images.githubusercontent.com/104667778/220969035-384971b5-e2a1-42dc-811a-19e75d5d51a9.png)


**Recommendation**: Use the safeTransferFrom() function instead of transfer(). This way the fees will be transferred from msg.sender to swapFeesTo and would revert on failure.

---
### 44. call() should be used instead of transfer() on an address payable

The transfer() and send() functions forward a fixed amount of 2300 gas. Historically, it has often been recommended to use these functions for value transfers to guard against reentrancy attacks.
The use of the deprecated transfer() function for an address will inevitably make the transaction fail when the claimer smart contract does implement a payable fallback which uses more than 2300 gas unit.

**Recommendation**: Use call() instead of transfer(), but be sure to use the CEI pattern and/or add re-entrancy guards, as several hacks already happened in the past due to this recommendation not being fully understood.

---
### 45. Dust amounts can cause payments to fail, leading to default

In Cooler.sol, When repay() is called and repaid is greater than loan.amount then the function will go into the else statement. Here loan.amount is deducted the value of repaid. If repaid is greater than loan.value then the function will revert. the subtraction marked below will underflow, reverting the payment.

**Recommendation**:Only collect and subtract the minimum of the current loan balance, and the amount specified in the repaid variable

---
### 46. Votes can be amplified due to insufficient checks

The voters can increase their community voting power when they vote on an active proposal using castVote() function. But, There is no check if a user has 0 votes in the function. For each time user make a vote, the value of totalCommunityScoreData.votes and userCommunityScoreData[_voter] will increase by 1.
This could lead to amplification of votes and an attacker could use a large number of addresses to vote with zero votes to drain the vault.

**Recommendation**: castVote should revert if msg.sender doesn't have any votes. Also there should exist check for if votes is more than 0.

---
### 47. Anyone can spend on behalf of roller periphery

The RollerPeriphery contract's approve method allows anyone to steal its ERC20 tokens. It does not have any access control, So, anyone call approve() which would make an ERC20 approve call to the token inputed, and allowing the 'to' address to spend.

**Recommendation**: Add access control to the approve method so only an admin or allowed addresses can use it.

---
### 48. Lack of Access control on Minting tokens.

The mintFromStaking() function is used to mint DMC tokens, but the problem is this function lacked any access control. The visibility modifier was set to external allowing anyone to mint tokens to their address.

**Recommendation**: Adding access control modifiers like onlyOwner would have prevented the hack.

---
### 50. Incorrect Validation leading to a DOS attack

The code in the transferLPs function has an incorrect validation check, where it requires allowances_ to be strictly equal to lenderLpBalance, instead of just allowances_ being greater than transferAmount.

**Recommendation**: The validation check in the transferLPs function should be updated to allow for allowances_ to be greater than transferAmount, rather than requiring them to be strictly equal.

---
### 51.  Pool Manager can front-run fees to 100%

Anyone can create a pool, including the pool manager. However, the pool manager is not considered a trusted actor. Pool Manager can front-run entry fee to 100% and users could lose all their deposits. There is no upper limit for setting the fee amount, meaning that it can be set as high as 100%. There should be a limit set for changing fees like 5% or 10%.

**Recommendation**: Add a timelock to change fees. In that way, frontrunning wouldn’t be possible and users would know the fees they are agreeing with.

---
### 52.  Precision loss due to division before multiplication

The _calculateNewRewards function calculates the new rewards for a staker by first dividing interestEarned_ by totalInterestEarnedInPeriod and then multiplying by totalBurnedInPeriod.If interestEarned_ is small enough and totalInterestEarnedInPeriod is large enough, the division may result in a value of 0, resulting in the staker receiving 0 rewards due to precision loss.

**Recommendation**: Consider calculating the new rewards by first multiplying interestEarned_ by totalBurnedInPeriod and then dividing by totalInterestEarnedInPeriod to avoid precision loss.

---
### 53.  NFT to be frozen in a contract that does not support ERC721

There are certain smart contracts that do not support ERC721, using transferFrom() may result in the NFT being sent to such contracts.
if the _to address is a contract address that does not support ERC721, the NFT can become frozen in the contract.

**Recommendation**: Consider using safeTransferFrom() instead of transferFrom().

---

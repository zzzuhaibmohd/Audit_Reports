### User's Orders can be  canceled  by anyone and their ETH can be stolen

When a user makes a WETH order from the router. After calculations, an id is returned. And the router does not save the offer's originator.
Using cancelForETH function, any attacker can call the cancel function by passing the order ID and get all the unfilled funds from the order.

**Recommendation**: Add a require  check that only the user who created the order can cancel it will solve the issue.

---
### Double transfer in the `transferAndCall` function

The transferAndCall function of ERC677 was incorrect. Instead of a single transfer, the function was performing transferring twice.
We can see that both super.transfer(_to, _value); and _transfer(msg.sender, _to, _value); are doing the same thing - transfering _value of tokens from msg.sender to _to.

**Recommendation**: Removing any of both methods of transfer from transferAndCall function will mitigate the vulnerability. 

---
### Unchecked Return Value from "ecrecover"

The function uses ecrecover for signature verification. In the Solidity documentation, it says it will “return zero on error”. So, if it had an issue, it would return 0.
 someone could send an invalid signature, which would result in a zero address returned from "ecrecovery", and the MRC20 contract would send them tokens without a lack of checking for "from" balance.

**Recommendation**: As a fix to Vulnerability, Polygon removed the transferWithSig function.

---
### EIP-712 signatures can be re-used

"buyFromPrivateSaleFor" method has no checks to determine if the EIP-712 signature has been used before.

**Recommendation**: Use a nonce while creating a  signature. When a nonce is injected into a signature, it makes it impossible for re-use, assuming of 
course the nonce feature is done correctly.

---
### Use safeCast for changing types

the _mint function takes an argument "_fCashAmount" of type uint256. Now, in the function,  the value of _fCashAmount is downcasted to uint88.
If value that is greater than uint88 is passed for "_fCashAmount" into the _mint function, downcasting it to uint88 will silently overflow.

**Recommendation**: Consider using OpenZeppelin's SafeCast library to prevent unexpected overflows when casting from uint256.

---
### BLOCK_PERIOD IS INCORRECT

In config.sol of zkSync contracts, The BLOCK_PERIOD is set to 13 seconds in Config.sol.
Ethereum  Since moving to Proof-of-Stake (PoS) after the Merge, block times on Ethereum are fixed at 12 seconds per block (slots).
 By using a  block time of 13 sec, a txn in the Priority Queue expires 5.5 hours earlier than expected.

**Recommendation**: Change the block period to 12 seconds

---
### Insufficient validation of Chainlink Oracle data feed

When fetching prices from latestRoundData, there is not enough validation that ensures the price is not stale.
The getPrice function fetches the price from Chainlink using latestRoundData, there are checks to make sure the price is not negative.

**Recommendation**: Checking the returned answer is not 0. Verify result is within an allowed margin of freshness by checking updatedAt.
 Verify answer is indeed for the last known round.

---

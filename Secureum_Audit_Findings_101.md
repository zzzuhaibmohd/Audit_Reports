### Unhandled return values of transfer and transferFrom

Some implementations of transfer` and transferFrom could return ‘false’ on failure instead of reverting.

**Recommendation**: Check the return value and revert on 0/false or use OpenZeppelin’s SafeERC20 wrapper functions.

---
### Tokens with more than 18 decimal points will cause issues

Maximum number of decimals for each token is 18. However, It is possible to have tokens with more than 18 decimals.

**Recommendation**: Make sure the code won’t fail in case the token’s decimals is more than 18

---
### Token approvals can be stolen in DAOfiV1Router01.addLiquidity()

There is no validation of the address to transfer tokens from, so an attacker could pass in any address with nonzero token approvals to DAOfiV1Router.

**Recommendation**: Transfer tokens from msg.sender instead of lp.sender

---
### DAOfiV1Pair.deposit() accepts deposits of zero, blocking the pool:

Only a single deposit can be made, so no liquidity can ever be added to a pool where deposited == true. The deposit() function does not check for a nonzero 
deposit amount in either token.

**Recommendation**: Require a minimum deposit amount with non-zero checks

---
### Purchasing and committing still possible after launch:

Even after GenesisGroup.launch has successfully been executed, it is still possible to invoke GenesisGroup.purchase and GenesisGroup.commit.

**Recommendation**: adding validation to make sure that these functions cannot be called after the launch.

---
### Unchecked return value for IWETH.transfer call

A call to IWETH.transfer that does not check the return value. 

**Recommendation**: Consider adding a require-statement or using safeTransfer

---
### Reentrancy vulnerability in MetaSwap.swap()

If an attacker is able to reenter swap(), they can execute their own trade using the same tokens and get all the tokens for themselves.

**Recommendation**: Use a simple reentrancy guard

---
### Users can collect interest from SavingsContract by only staking mTokens momentarily

The SAVE contract allows users to deposit mAssets in return for lending yield and swap fees.
the smart contract enforces a minimum timeframe of 30 minutes in which the interest rate will not be updated.
A user who deposits shortly before the end of the timeframe will receive credits at the stale interest

**Recommendation**: Remove the 30 minutes window such that every deposit also updates the exchange rate between credits and tokens.

---
### A reverting fallback function will lock up all payouts:

If any of the recipients of an ETH transfer is a smart contract that reverts, then the entire payout will fail and will be unrecoverable.

**Recommendation**: allow buyers/sellers to initiate the withdrawal on their own using a ‘pull-over-push pattern.’ 

---
### A reverting fallback function will lock up all payouts:

If any of the recipients of an ETH transfer is a smart contract that reverts, then the entire payout will fail and will be unrecoverable.

**Recommendation**: allow buyers/sellers to initiate the withdrawal on their own using a ‘pull-over-push pattern.’ 

---
### Whitelisted tokens limit

function is iterating over all whitelisted tokens. If the number of tokens is too big, a transaction can run out of gas and all funds will be blocked forever.

**Recommendation**: Limiting the number of whitelisted tokens or add a new type of proposal that is used to vote on token removal if the balance of this token is zero.

---
### Queued transactions cannot be canceled

Only the admin can call Timelock.cancelTransaction. There are no functions in Governor that call Timelock.cancelTransaction.

**Recommendation**: Add a function to the Governor that calls Timelock.cancelTransaction.

---
### Lack of chainID validation allows signatures to be re-used across forks

Signatures used in calls to permit in ERC20Permit do not account for chain splits. The chainID is included in the domain separator. 
if the chain forks after deployment, the signed message may be considered valid on both forks.

**Recommendation**: include the chainID opcode in the permit schema.

---
### No incentive for bidders to vote earlier: 

Hermez relies on a voting system that allows anyone to vote with any weight at the last minute. As a result, anyone with a large fund can manipulate the vote. 
An attacker can know exactly how much currency will be necessary to change the outcome of the voting just before it ends.

**Recommendation**: Consider a weighted bid, with a weight decreasing over time.

---
### Lack of two-step procedure for critical operations leaves them error-prone

Several critical operations are done in one function call. This schema is error-prone and can lead to irrevocable mistakes.

**Recommendation**: use a two-step procedure for all non-recoverable critical operations to prevent irrecoverable mistakes

---
### Failed transfer may be overlooked due to lack of contract existence check

The pool fails to check that a contract exists, the pool may assume that failed transactions involving destructed tokens are successful.

**Recommendation**: check the contract’s existence prior to the low-level call 

---
### System always assumes USDC is equivalent to USD

implementation of the getRate method does not use the USDC-USD oracle provided by Chainlink; instead, it assumes 1 USDC is always worth 1 USD.

**Recommendation**: replace the hard-coded integer literal in the getRate method with a call to the relevant Chainlink oracle

---
### Blacklisting Bypass via transferFrom() Function

The transferFrom() function in the contract does not verify that the sender (i.e. the from address) if blacklisted.

**Recommendation**: Implment check on the msg.sender and to addresses

---
### Lack of event emission after sensitive actions

Contract does not emit relevant events after executing the sensitive actions of setting the fundingRate

**Recommendation**: Consider emitting events after sensitive changes take place, to facilitate tracking and notify off-chain clients

---
### Governance parameter changes should not be instant

Many sensitive changes can be made by any account with the WhitelistAdmin role via the functions and the changes are instantly applied without informing 
the user.

**Recommendation**: Consider implementing a time-lock mechanism for such changes to take place. By having a delay between the signal of intent and the actual change

---

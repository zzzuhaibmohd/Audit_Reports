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

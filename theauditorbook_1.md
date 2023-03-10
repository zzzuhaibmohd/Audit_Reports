### UniswapConfig getters return wrong token config if token config does not exist (High)

The UniswapConfig.getTokenConfigBySymbolHash function does not work as getSymbolHashIndex returns 0 if there is no config token for that symbol.
This will always return the token config of the first index (index 0) which is a valid token config for a completely different token.

**Recommendation**: Fix the non-existence check.

---
### Loans of tokens with >18 decimals can result in incorrect collateral calculation (Medium)

Calculation within the collateralRequiredForDrawdown of LoanLib incorrectly assumes the collateral token of a loan to be less than 18 decimals
This can cause an underflow to the power of 10 which will cause the division to yield 0 and thus cause the Loan to calculate 0 as collateral required for the loan.

**Recommendation**: We advise the same paradigm as _toWad to be applied which is secure: LoanLib.sol#L247

---
### Users may unintendedly remove liquidity under a phishing attack. (High)

The removeLiquidity function in Pools.sol uses tx.origin to determine the person who wants to remove liquidity.

**Recommendation**: Consider making the function _removeLiquidity external, which can beutilized by the router, providing information of which person removes 
his liquidity.

---
### Missing access restriction on lockUnits/unlockUnits (High)

The Pool.lockUnits allows anyone to steal pool tokens from a member and assign them to msg.sender.

**Recommendation**: Add access control and require that msg.sender is the router or another authorized party.

---
### Swap token can be traded as fake base token (High)

The Pools.swap function does not check if base is one of the base tokens.
It breaks the accounting for the pool as tokens are transferred in, but the base balance is increased (and token decreased). LPs cannot correctly
withdraw again, and others cannot correctly swap again.

**Recommendation**: Check that base is either USDV or VADER 

---
### Proposals can be cancelled (High)

Anyone can cancel any proposals by calling DAO.cancelProposal(id, id) with oldProposalID == newProposalID.
An attacker can launch a denial of service attack on the DAO governance and prevent any proposals from being executed.

**Recommendation**: Check oldProposalID == newProposalID

---
### Canceled proposals can still be executed (Medium)

A cancelled proposal only sets mapPID_votes to zero but mapPID_timeStart and mapPID_finalising stay the same and pass the checks in finaliseProposal which 
queues them for execution.

**Recommendation**: Set a cancel flag and check for it in finaliseProposal and in execution

---
### Canceled proposals can still be executed (Medium)

A cancelled proposal only sets mapPID_votes to zero but mapPID_timeStart and mapPID_finalising stay the same and pass the checks in finaliseProposal which 
queues them for execution.

**Recommendation**: Set a cancel flag and check for it in finaliseProposal and in execution

---
### pendingWithdrawals not decreased after a withdraw (High)

The variable pendingWithdrawals in the contract Withdrawable is not decreased after the function withdraw is called, which causes the return
value of function getReserveBalance less than it should be.

**Recommendation**: Add pendingWithdrawals = pendingWithdrawals.sub(reserveAmount);

---
### Should check return data from Chainlink aggregators (Medium)

The getEtherPrice function in the contract FSDNetwork fetches the ETH price from a Chainlink aggregator using the latestRoundData function.
However, there are no checks on roundID nor timeStamp, resulting in stale prices.

**Recommendation**: Add checks on the return data with proper revert messages

---
### Call to swapExactTokensForETH in liquidateDai() will always fail (Medium)

Given that there is no prior approval, the call to UniswapV2 router for
swapping will fail because msg.sender has not approved UniswapV2 with
an allowance for the tokens being attempted to swap.

**Recommendation**: Add FSD approval to UniswapV2 with an allowance for the tokens being attempted to swap.

---
### Call to swapExactTokensForETH in liquidateDai() will always fail (Medium)

Given that there is no prior approval, the call to UniswapV2 router for
swapping will fail because msg.sender has not approved UniswapV2 with
an allowance for the tokens being attempted to swap.

**Recommendation**: Add FSD approval to UniswapV2 with an allowance for the tokens being attempted to swap.

---
### Potential griefing with DoS by front-running vault creation with same vaultID (Medium)

The vaultID for a new vault being built is required to be specified by the
user building a vault via the build() function (instead of being assigned by
the Cauldron/protocol).

**Recommendation**: Mitigate this DoS vector by having the Cauldron assign the vauldID instead of user specifying it in the build() operation.

---
### Usage of deprecated ChainLink API in Buoy3Pool (Medium)

The Chainlink API (latestAnswer) used in the Buoy3Pool oracle wrappers
is deprecated:

**Recommendation**: Add the recommended checks:

---
### Safe addresses can only be added but not removed (Medium)

if there a safe listed integration that needs to be later disabled, it cannot be done.

**Recommendation**: Change addSafeAddress() to isSafeAddress() with an additional bool parameter to allow both enabling/disabling of safe addresses.

---
### Using transferFrom on ERC721 tokens

the transferFrom keyword is used instead of safeTransferFrom. If any winner is a contract and is not aware of incoming ERC721 tokens, the sent tokens could be locked.

**Recommendation**: changing transferFrom to safeTransferFrom

---
### Return values of ERC20 transfer and transferFrom are unchecked (Medium)

it could be false if the transferred tokens are not ERC20-compliant

**Recommendation**: Use the SafeERC20 library

---

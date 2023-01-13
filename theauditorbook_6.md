### cancelPromotion is too rigorous (High)

When you cancel a promotion with cancelPromotion() then the promotion
is complete deleted. It also means all the unclaimed tokens (of the previous epochs) will stay
locked in the contract.

**Recommendation**: 
uint256 _remainingRewards = _getRemainingRewards(_promotionId);
delete _promotions[_promotionId];

---
### Contract does not work with fee-on transfer tokens (High)

the actual amount of tokens the contract holds could be less than
promotion.tokensPerEpoch * promotion.numberOfEpochs leading to not
claimable rewards for users claiming later than others.

**Recommendation**: To disable fee-on transfer tokens for the contract, add the following code
check audit hero

---
### Dust Token Balances Cannot Be Claimed By An admin Account (Medium)

Users who have a small claim on rewards for various promotions, may not
feasibly be able to claim these rewards as gas costs could outweigh the sum
they receive in return.

---
### Contract does not work with fee-on transfer tokens (High)

the actual amount of tokens the contract holds could be less than
promotion.tokensPerEpoch * promotion.numberOfEpochs leading to not
claimable rewards for users claiming later than others.

**Recommendation**: To disable fee-on transfer tokens for the contract, add the following code
check audit hero

---
### Wrong implementation of NoYield.sol#emergencyWithdraw() (High)

received is not being assigned prior to L81, therefore, at L81, received is 0.
As a result, the emergencyWithdraw() does not work, in essence.

**Recommendation**: Change to received = IERC20(_asset).balanceOf(address(this));
IERC20(_asset).safeTransfer(_wallet, received);

---
### _from and _to can be the same address on wrap() function (High)

the function does not make sure that _from and _to are not the same address and failure to make this check
in functions with transfer functionality has lead to severe bugs in other protocols since users rewards are updated on such transfers this can be used
to manipulate the system.

**Recommendation**: require(address(_from) != address(_to), "_from and _to cannot be the same")

---
### Loss Of Flash Governance Tokens If They Are Not Withdrawn Before The Next Request (High)

Users who have not called withdrawGovernanceAsset() after they have locked their tokens from a previous proposal will lose their tokens 
if assertGovernanceApproved() is called again with the same target and sender.
Since the new amount is not added to the previous amount, instead
the previous amount is overwritten with the new amount.

**Recommendation**: Consider updating the initial if statement to ensure the pendingFlashDecision for that target and sender is empty,

---
### You can flip governance decisions without extending vote duration (Medium)

The impact here is that a user can, right at the end of the voting period, flip the decision without triggering the logic to extend the vote duration. The
user doesn’t even have to be very sophisticated: they can just send one vote in one transaction to go to 0, then in a subsequent transaction send enough
to flip the vote.

**Recommendation**: Make sure that going to 0 is equivalent to a flip, but going away from 0 isn’t a flip.

---
### You can flip governance decisions without extending vote duration (Medium)

The impact here is that a user can, right at the end of the voting period, flip the decision without triggering the logic to extend the vote duration. The
user doesn’t even have to be very sophisticated: they can just send one vote in one transaction to go to 0, then in a subsequent transaction send enough
to flip the vote.

**Recommendation**: Make sure that going to 0 is equivalent to a flip, but going away from 0 isn’t a flip.

---
### The system can get to a “stuck” state if a bad proposal (proposal that can’t be executed) is accepted (Medium)

The only way a proposal can be done and a new proposal can be registered is to finish the previous proposal by either accepting it and executing it or by rejecting it. 

---

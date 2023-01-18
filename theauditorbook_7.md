### 1. pay() function has callback to msg.sender before important state updates (High)

the pay() function has a callback to the msg.sender in the middle of the function while there are still updates to state that take
place after the callback. The lock modifier guards against reentrancy but not against cross function reentrancy. The callback
before important state changes also violates the Checks Effects Interactions

**Recommendation**:  The callback should be placed at the end of the pay() function after all state updates have taken place.

---
### 2. Malicious Users Can Duplicate Protocol Earned Yield By Transferring wCVX Tokens To Another Account (High)

The exploit can be outlined through the following steps: - Alice receives 100 wCVX tokens from the protocol after wrapping their convex tokens. - At
that point in time, _getDepositedBalance() returns 100 as its result. A checkpoint has also been made on this balance, giving Alice claim to her
fair share of the rewards. - Alice transfers her tokens to her friend Bob who then manually calls user_checkpoint() to update his balance. - Now from
the perspective of the protocol, both Alice and Bob have 100 wCVX tokens as calculated by the _getDepositedBalance() function. - If either Alice or
Bob wants to claim rewards, all they need to do is make sure the 100 wCVX tokens are in their account upon calling getReward(). Afterwards, the
tokens can be transferred out.

**Recommendation**:  Consider implementing the _beforeTokenTransfer() function as shown in the reference contract.

---
### 3. Profile creation can be frontrun (Medium)

The LensHub/PublishingLogic.createProfile function can be frontrun by other whitelisted profile creators. An attacker can observe pending
createProfile transactions and frontrun them, own that handle, and demand ransom from the original transaction creator.

**Recommendation**:  Everyone needs to use flashbots / private transactions but it might not be
available on the deployed chain. A commit/reveal scheme for the handle and the entire profile creation could mitigate this issue.

---
### 4. ConvexStakingWrapper.exitShelter() Will Lock LP Tokens, Preventing Users From Withdrawing (High)

The shelter mechanism provides emergency functionality in an effort to protect users’ funds. The enterShelter function will withdraw all LP
tokens from the pool, transfer them to the shelter contract and activate the shelter for the target LP token. Conversely, the exitShelter function will
deactivate the shelter and transfer all LP tokens back to the ConvexStakingWrapper.sol contract.

Unfortunately, LP tokens aren’t restaked in the pool, causing LP tokens to be stuck within the contract. Users will be unable to withdraw their LP
tokens as the withdraw function attempts to withdrawAndUnwrap LP tokens from the staking pool. As a result, this function will always revert due to 
insufficient staked balance. If other users decide to deposit their LP tokens, then these tokens can be swiped by users who have had their LP tokens
locked in the contract.

**Recommendation**:  Consider re-depositing LP tokens upon calling exitShelter

---
### 5. USDC blacklisted accounts can DoS the withdrawal system (Medium)

withdrawals are queued in an array and processed sequentially in a for loop. However, a
safeTransfer() to USDC blacklisted user will fail. It will also brick the withdrawal system because the
blacklisted user is never cleared.

**Recommendation**:  A user himself withdraws his balance

---

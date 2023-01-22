### 1. Low level call returns true if the address doesn’t exist (Medium)

delegatecall and staticcall return true as their first return value if the account
called is non-existent, as part of the design of the EVM.

**Recommendation**:  you can check that the address is a contract by checking its code size.

---
### 2. ERC777 tokens can bypass depositCap guard (Medium)

When ERC777 token is used as the underlying token for a LiquidityPool,
a depositor can reenter depositFor and bypass the depositCap requirement
check, resulting in higher total deposit than intended by governance.

**Recommendation**:  Add reentrancy guards to depositFor.

---
### 3. _revokeRole doesn’t remove account from roleMember set (Medium)

The function doesn’t remove the address from _roleMembers[role] set, which will mess up with the roleCount

**Recommendation**:  _roles[role].members[account] = false; _roleMembers[role].remove(account);

---
### 4. Existing user’s locked JPEG could be overwritten by new user, causing permanent loss of JPEG funds (High)

A user’s JPEG lock schedule can be overwritten by another user’s if he (the
other user) submits and finalizes a proposal to change the same NFT index’s value.

**Recommendation**:  Release the tokens of the existing schedule. Simple and elegant.

---
### 5. The noContract modifier does not work as expected. (Medium)

the restriction can be bypassed when deploying a smart contract through the smart contract’s constructor call.

**Recommendation**:  Modify the code to require(msg.sender == tx.origin);

---
### 6. ABDKMath64 performs multiplication on results of division (Medium)

Solidity could truncate the results, performing multiplication before
division will prevent rounding/truncation in solidity math.

**Recommendation**:  Consider ordering multiplication first.

---

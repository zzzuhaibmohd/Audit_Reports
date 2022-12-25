### SettV3.transferFrom block lock can be circumvented (Medium)

The SettV3.transferFrom implements a _blockLocked call to prevent users to call several functions at once, for example, deposit and then transferring the tokens.
an attacker can circumvent it using transferFrom

**Recommendation**: The block lock should be on the account that holds the tokens, i.e., on sender (“from” address), not on msg.sender.

---
### Fee on transfer tokens can lead to incorrect approval (Medium)

The createBasket function does not account for tokens with fee on transfer.
the safeApprove call would be approving more tokens than what was received, leading to accounting issues.  

**Recommendation**: It is recommended to find the balance of the current contract before and after the transferFrom

---
### Bonding mechanism allows malicious user to DOS auctions (Medium)

A malicious user can listen to the mempool and immediately bond when an
auction starts, without aim of settling the auction.
As no one can cancel his bond in less than 24h, this will freeze user funds and auction settlement for 24h

**Recommendation**: remove it and let anybody settle the auction.

---
### burn and mintTo in Basket.sol vulnerable to reentrancy (Medium)

The functions mintTo and burn make external calls prior to updating the
state. If a basket contains an ERC777 token, attackers can mint free basket
tokens.

**Recommendation**: Move external calls after state updates.

---
### TridentNFT.permit should always check recoveredAddress != 0 (Medium)

The TridentNFT.permit function ignores the recoveredAddress != 0
check if isApprovedForAll[owner][recoveredAddress] is true.

**Recommendation**: Change the require logic to recoveredAddress != address(0) && (recoveredAddress == owner) || isApprovedForAll[owner] [recoveredAddress]).

---
### Users are susceptible to back-running when depositing ETH to TridenRouter (Medium)

the input parameter does not represent the actual amount of ETH to deposit, and users have to calculate the actual amount and send it to the
router, causing a back-run vulnerability if there are ETH left after the operation.

**Recommendation**:Directly push the remaining ETH to the sender to prevent any ETH left in the router.

---
### Rounding errors will occur for tokens without decimals (Medium)

Some rare tokens have 0 decimals: https://etherscan.io/token/0xcc8fa225d80b9c7d42f96e9570156c65d6caaa25 For these tokens, small losses of precision will be 
amplified by the lack of decimals.

**Recommendation**: Rounding the final getAmountOut division upwards would fix this.

--- 
### transferNotionalFrom doesn’t check from != to (High)

If the “from” and “to” address are the same then the balance of “from” is overwritten by the balance of “to”. This
means the balance of “from” and “to” are increased and no balances are decreased, effectively printing money.

**Recommendation**: Add something like the following: require (f != t,“Same”);

---
### Admin is a single-point of failure without any mitigations (Medium)

Admin role has absolute power across Swivel, Marketplace and
VaultTracker contracts with several onlyOwner functions. There is no
ability to change admin to a new address or renounce it which is helpful for
lost/compromised admin keys or to delegate control to a different
governance/DAO address in future.

**Recommendation**: Ensure admins are reasonably redundant/independent (3/7 or 5/9) multisigs and add transfer/renounce functionality for admin.

---

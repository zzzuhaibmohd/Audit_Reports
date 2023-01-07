### P12V0FactoryUpgradeable::executeMint() issue with non-existent gameCoinAddress and mintId

When a non-existent gameCoinAddress or mintId is passed to the executeMint()
function, the !coinMintRecords[gameCoinAddress][mintId].executed will be false and !false will pass the require.

**Recommendation**: Add a require statement to require coinMintRecords[gameCoinAddress] [mintId].unlockTimestamp != 0.

---
### SSVault.dexType uninitialized value risk 

value 0 can also be uninitialized default value of uint256 type, hence it is recommended to
use 0 as the undefined DEX type and 1, 2, 3 for the actual DEX type values

**Recommendation**: Use Enums to define the types and reserve the value 0 to be the undefined DEX type.

---
### settleOrders() should check if _users is already settled

settleOrders function failed to check the input params, address[] memory users. which can be the same
user that repeat multiple times. By doing this, the single user can occupy other users' chance to get settled for the round
id = 0.

**Recommendation**:  move the require(user.settledRound != getLatestRoundId(), "already settled"); at the beginning of function settleOrder

---

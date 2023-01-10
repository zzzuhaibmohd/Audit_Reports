### Changing NFT contract in the MochiEngine would break the protocol (High)

MochiEngine allows the operator to change the NFT contract. MochiEngine.sol#L91-L93 All the vaults would point to a different NFT
address. As a result, users would not be access their positions. The entire protocol would be broken.

**Recommendation**: Remove the function.

---
### Referrer can drain ReferralFeePoolV0 (High)

function claimRewardAsMochi in ReferralFeePoolV0.sol did not reduce
user reward balance, allowing referrer to claim the same reward repeatedly and thus draining the fee pool.

**Recommendation**: Add the following lines > rewards -= reward[msg.sender]; > reward[msg.sender] = 0;

---
### anyone can create a vault by directly calling the factory (Medium)

There’s no permission control in the vaultFactory. Anyone can create a vault.

**Recommendation**: Recommend to add a check. require(msg.sender == engine, "!engine");

---
### Chainlink’s latestRoundData might return stale or incorrect results (Medium)

consumers of this contract may continue using outdated stale or incorrect data, If there is a problem with Chainlink starting a new
round and finding consensus on the new value for the oracle

**Recommendation**: Add the following checks:
...
( roundId, rawPrice, , updateTime, answeredInRound ) =
AggregatorV3Interface(XXXXX).latestRoundData();
require(rawPrice > 0, "Chainlink price <= 0");
require(updateTime != 0, "Incomplete round");
require(answeredInRound >= roundId, "Stale price");
...

---
### Deposits don’t work with fee-on transfer tokens (Medium)

There are ERC20 tokens that may make certain customizations to their
ERC20 contracts. One type of these tokens is deflationary tokens that charge a certain fee for every transfer() or transferFrom().
The PrizePool._depositTo() function will try to supply more _amount
than was actually transferred. The tx will revert and these tokens cannot be used.

**Recommendation**: One possible mitigation is to measure the asset change right before and after the asset-transferring routines

---
### Claim airdrop repeatedly (High)

In the next part of the function, airdrop[msg.sender] is overwritten with fresh values and airdrop[msg.sender].claimed will be reset to 0.

**Recommendation**: Add the following to validate() : require(validated[msg.sender]== 0,“Already validated.”);

---
### No Transfer Ownership Pattern (Medium)

function checks the new owner is not the zero address and proceeds to write the new owner’s address into the owner’s state variable.

**Recommendation**: Consider implementing a two step process where the owner nominates an account and the nominated account needs to call an acceptOwnership()

---
### Timelock can be bypassed (High)

The purpose of a Timelock contract is to put a limit on the privileges of the
governor, by forcing a two step process with a preset delay time.

the current governor can call Timelock#setGovernor(address _governor) and set a new governor effective immediately.
And the new governor can then call Timelock#setDelay() and change the delay to 0, also effective immediately.

**Recommendation**: Consider making setGovernor and setDelay only callable from the Timelock contract itself.

---

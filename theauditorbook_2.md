### Signatures use only tx ID instead of entire digest (Medium)

The signature check in recoverFulfillSignature() only uses transaction ID
(along with the relayer fee) which can be accidentally reused by the user

**Recommendation**: Evaluate if the signature should contain only tx ID or the entire digest, and
change logic appropriately.

---
### Old yield source still has infinite approval (Medium)

After swapping a yield source, the old yield source still has infinite approval.

**Recommendation**: Decrease approval after swapping the yield source.

---
### Flash loan manipulation on getPoolShareWeight of Utils (High)

The getPoolShareWeight function returns a user’s pool share weight by
calculating how many SPARTAN the user’s LP tokens account for.
However, this approach is vulnerable to flash loan manipulation.

**Recommendation**: recalculate and force the new weight to take effect only after a certain period, e.g., a block time.

---
### Improper access control of claimAllForMember allows anyone to reduce the weight of a member (Medium)

The claimAllForMember function of Dao is permissionless, allowing anyone
to claim the unlocked bonded LP tokens for any member.

**Recommendation**: replace all member to msg.sender to allow only the user himself to claim unlocked bonded LP tokens.

---

### 1. Send ether with call instead of transfer. (Medium)

Use call instead of transfer to send ether. And return value must be checked
if sending ether is successful or not. Sending ether with the transfer is no
longer recommended.

**Recommendation**:  (bool result, ) = payable(_account).call{value: _amount}("“);require(result,”Failed to send Ether");

---
### 2. A pauser can brick the contracts (Medium)

A malicious or compromised pauser can call pause() and
renouncePauser() to brick the contract and all the funds can be frozen.

**Recommendation**:  Consider removing renouncePauser(), or requiring the contract not in paused mode when renouncePauser().

---
### 3. STORAGE COLLISION BETWEEN PROXY AND IMPLEMENTATION (LACK EIP 1967) (High)

In this case, for example, as you inherits from Ownable the variable _owner
is at the first slot and can be overwritten in the implementation.

**Recommendation**: Consider using EIP1967

---
### 4. Add a timelock to setPlatformFee() (Medium)

It is a good practice to give time for users to react and adjust to critical
changes. A timelock provides more guarantees and reduces the level of trust
required, thus decreasing risk for users. It also indicates that the project is
legitimate.

**Recommendation**: Consider adding a timelock to setPlatformFee()

---
### 5. Emergency mode enable/disable issue (Medium)

Owner can trigger emergency mode, perform emergency withdrawal operations without any restrictions and then disable emergency mode.

**Recommendation**: It is recommended to remove bool trigger parameter from triggerEmergencyWithdraw function and set emergency to true after successfully 
executing function.

---

### Ensure zero msg.value if transferring from user and inputToken is not ETH (Medium)

A user that mistakenly calls either create() or addToken() with WETH (or
another ERC20) as the input token, but includes native ETH with the function call will have his native ETH permanently locked in the contract.

**Recommendation**: It is best to ensure that msg.value = 0 in _transferInputTokens()

---
### _totalSupply not updated in _transferMint() and _transferBurn() (Medium)

The functions _transferMint() and _transferBurn() of OverlayToken.sol
don’t update _totalSupply. Whereas the similar functions _mint() and
_burn() do update _totalSupply.

**Recommendation**: Update _totalSupply in _transferMint() and _transferBurn()

---
### Prevent Minting During Emergency Exit (Medium)

Consider a critical incident where a vault is being
drained or in danger of being drained due to a vulnerability within the vault
or its strategies. minting against debt does not seem like a desirable
behaviour at this time.

**Recommendation**: Convert emergency exit check to a modifier and then apply that modifier here.

--- 
### Anyone can call closeLoan() to close the loan (Medium)

the current implementation allows anyone to call closeLoan() anytime after fundLoan().

**Recommendation**: require(msg.sender == _borrower, "ML:DF:NOT_BORROWER");

---
### No access control on assignFees() function in NFTXVaultFactoryUpgradeable contract (Medium)

any user could call the function NFTXVaultFactoryUpgradeable.assignFees() and hence all the fees are updated.

---

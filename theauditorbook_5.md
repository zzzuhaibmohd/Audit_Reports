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

### 1. There is no way to withdraw the ETH paid by minters

There is currently no possible way for the contract deployer/owner to withdraw the ETH that was paid by miners. This means that value will be stuck & lost forever.

**Recommendation**: Add a method to withdraw the value in the contract

---
### 2. If address is a smart contract that can't handle ERC721 tokens they will be stuck after a whitelisted mint

The mintPublic method has a check that allows only EOAs to call it but it is missing in the whitelisted mint methods. This means that if the address that is 
whitelisted is a contract and it calls those functions but it can't handle ERC721 tokens correctly, they will be stuck.

**Recommendation**: change the _mint call to _safeMint.

---
### 3. If address is a smart contract that can't handle ERC721 tokens they will be stuck after a whitelisted mint

The mintPublic method has a check that allows only EOAs to call it but it is missing in the whitelisted mint methods. This means that if the address that is 
whitelisted is a contract and it calls those functions but it can't handle ERC721 tokens correctly, they will be stuck.

**Recommendation**: change the _mint call to _safeMint.

---

---
### Potential resource exhaustion by external calls performed within an unbounded loop

Changes to SoloMargin could make it impossible to execute this code due to the block gas limit.

**Recommendation**: Reconsider or bound the loop

---
### Owners can never be removed

The intention of setOwners() is to replace the current set of owners with a new set of owners.

**Recommendation**: loop through the current set of owners and clear their isOwner booleans

---
### Use of modifiers for repeated checks

It is recommended to use modifiers for common checks within different functions.

**Recommendation**: Use of modifiers for repeated checks

---
### Insufficient use of SafeMath

CurveMath.calculateTrade is used to compute the output amount for a trade. However, although SafeMath is used throughout the codebase to prevent underflows/overflows, 
it is not used in this calculation.

**Recommendation**: Review all critical arithmetic to ensure that it accounts for underflows, overflows, and the loss of precision.

---
### ABIEncoderV2 is not production-ready

The contracts use the new Solidity ABI encoder, ABIEncoderV2. This experimental encoder is not ready for production.

**Recommendation**: use neither ABIEncoderV2 nor any other experimental Solidity feature.

---
### Withdrawn Event Log Poisoning

Calling the withdraw() function will emit the Withdrawn event. No UNI tokens are required as this function can be called with amount = 0 

**Recommendation**: Consider adding a require or if statement preventing the withdraw() function from emitting the Withdrawn event when the amount variable is zero.

---
### Lack of indexed parameters in events:

none of the parameters in the events defined in the contracts are indexed.

**Recommendation**: Consider indexing event parameters to avoid hindering the task of off-chain services searching and filtering for specific events.

---
### Missing error messages in require statements

There are many places where require statements are correctly followed by their error messages, clarifying what was the triggered exception.

**Recommendation**: Consider including specific and informative error messages in all require statements.

---
### Use delete to clear variables:

The Controller contract sets a variable to the zero address in order to clear it. Similarly, the SetToken clears the locker by assigning the zero address.

**Recommendation**: The delete key better conveys the intention and is also more idiomatic. Consider replacing assignments of zero with delete statements.

---

### [G-01] Long revert strings

Shortening revert strings to fit in 32 bytes will decrease gas costs for deployment and gas costs when the revert condition has been met.  
Consider using Custom Errors as they are more gas efficient while allowing developers to describe the error in detail.  

[src/contracts/libraries/BeaconChainProofs.sol#L199](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/libraries/BeaconChainProofs.sol#L199)  
```
require(validatorFields.length == 2**VALIDATOR_FIELD_TREE_HEIGHT, "BeaconChainProofs.verifyValidatorFields: Validator fields has incorrect length");
```
There are 37 instances of this issue.

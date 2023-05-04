Lack of input validation in BeaconChainProofs contract

Links to the affected code - 

1. https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/libraries/BeaconChainProofs.sol#L192
2. https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/libraries/BeaconChainProofs.sol#L221
3. https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/libraries/BeaconChainProofs.sol#L245

The contract lacks proper input validation for certain functions, such as verifyValidatorFields, verifyValidatorBalance, and verifyWithdrawalProofs. This could lead to potential issues with the execution of the functions, and may cause unexpected behavior. To mitigate this, make sure to validate input parameters before using them in any calculation or logic.


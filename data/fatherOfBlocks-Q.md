**src/contracts/permissions/Pausable.sol**
- L43/49 - The whenNotPaused and onlyWhenNotPaused modifiers are created, but they are never used, therefore they should be eliminated since they generate extra gas costs in the deploy and also generate a worse understanding in the code, since there is more code It is difficult to identify which code is used and which is not.


**src/contracts/pods/DelayedWithdrawalRouter.sol**
- L13 - Contract does not follow the Solidity style guide's suggested layout ordering
Add more instances where this happens: The WithdrawalDelayBlocksSet event, should be with all the others in L33.

 
**src/contracts/pods/EigenPod.sol**
- L5/7 - The OwnableUpgradeable and AddressUpgradeable contracts are imported, but they are never used, this when deployed in a blockchain will generate a lot of code in the explorer and generate confusion when it comes to understanding the code.


**src/contracts/pods/EigenPodManager.sol**
- L5/12 - The BeaconProxy and IDegationManager contracts are imported, but they are never used, this when deployed in a blockchain will generate a lot of code in the explorer and generate confusion when it comes to understanding the code.

- L200 - abi.encodePacked() should not be used with dynamic types when passing the result to a hash function such as keccak256()
Use abi.encode() instead which will pad items to 32 bytes, which will prevent hash collisions (e.g. abi.encodePacked(0x123,0x456) => 0x123456 => abi.encodePacked(0x1,0x23456), but abi.encode(0x123,0x456) => 0x0...1230...456). “Unless there is a compelling reason, abi.encode should be preferred”. If there is only one argument to abi.encodePacked() it can often be cast to bytes() or bytes32() instead.
If all arguments are strings and or bytes, bytes.concat() should be used instead.


**src/contracts/interfaces/IEigenPod.sol**
- L6 - IBeaconChainOracle is imported, which is not used in any way within the IEigenPod interface, this generates that when the interface is deployed in a blockchain and in an explorer the entire code is seen, it becomes difficult to understand the code.
In addition to the extra cost of gas that is generated.


**src/contracts/interfaces/IServiceManager.sol**
- L4/5 - Two interfaces are imported that are not used in any way within the IServiceManager interface, this generates that when the interface is deployed in a blockchain and in an explorer the entire code is seen, it becomes difficult to understand the code.
In addition to the extra cost of gas that is generated.


**src/contracts/core/StrategyManager.sol**
- L277/279 - abi.encodePacked() should not be used with dynamic types when passing the result to a hash function such as keccak256()
Use abi.encode() instead which will pad items to 32 bytes, which will prevent hash collisions (e.g. abi.encodePacked(0x123,0x456) => 0x123456 => abi.encodePacked(0x1,0x23456), but abi.encode(0x123,0x456) => 0x0...1230...456). “Unless there is a compelling reason, abi.encode should be preferred”. If there is only one argument to abi.encodePacked() it can often be cast to bytes() or bytes32() instead.
If all arguments are strings and or bytes, bytes.concat() should be used instead.

- L456/498/501/536/560 - Within the completeQueuedWithdrawals() function an iteration is performed through an array and then within the loop that index is used to iterate through multiple arrays. This could generate an error, since they could have different lengths and revert without handling the exception or terminate execution without passing all the values ​​of the arrays.
The same thing happens in slashShares() and in slashQueuedWithdrawal().

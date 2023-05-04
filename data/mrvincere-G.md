## [G-01] No need to initialize variables with default values (i.e. with 0)
If a variable is not set/initialized, it is assumed to have the default value (0, false, 0x0 etc depending on the data type). Explicitly initializing it with its default value is an anti-pattern and wastes gas.

## Proof of Concept:  

### Slasher.sol:128                                    
```for (uint256 i = 0; i < frozenAddresses.length;) {```
### Allocator.sol:10                                   
```for (uint i = 0; i < recipients.length; i++) {```
### StrategyManagerHarness.sol:35         
```for (uint256 i = 0; i < strategiesLength;) {```
### StrategyManagerHarness.sol:76         
```for (uint256 i = 0; i < length; ++i) {```
### VoteWeigherBase.sol:67                     
```for (uint256 i = 0; i < stratsLength;) {```
### BeaconChainOracle.sol:86                 
```for (uint256 i = 0; i < _oracleSigners.length;) {```

#### 79 Instances

## [G-02] x += y costs more gas than x = x + y for state variables(x -= y too than x = x-y)

## Proof Of Concept:
### BeaconChainOracle.sol:110                
```stateRootVotes[blockNumber][stateRoot] += 1;```
### BLSRegistry.sol:257                           
```_totalStake.firstQuorumStake += currentStakes.firstQuorumStake;```
### BLSRegistry.sol:258                           
```_totalStake.secondQuorumStake += currentStakes.secondQuorumStake;```
### StrategyManagerHarness.sol:78         
```res += 1;```
### VoteWeigherBase.sol:48                    
```totalQuorumBips += _quorumBips[I];```
### DelayedWithdrawalRouter.sol:150    
```amountToSend += delayedWithdrawal.amount;```
### MerkleDelegationTerms.sol:57          
```cumulativeClaimedByStakerOfToken[msg.sender][tokensAndAmounts[i].token] += tokensAndAmounts[I].amount;```
### 72 Instances

## [G-03] Do not Calculate Constants
Due to how constant variables are implemented (replacements at compile-time), an expression assigned to a constant variable is recomputed each time that the variable is used, which wastes some gas.

## Proof Of concept:
### HashThreshold.sol:15                           
```uint32 constant disputePeriodBlocks = 1 days / 12 seconds;```
## [G-04] Use solidity version 0.8.19 to gain some gas boost
Upgrade to the latest solidity version 0.8.19 to get additional gas savings.  See latest release for reference: https://blog.soliditylang.org/2023/02/22/solidity-0.8.19-release-announcement/

## Proof Of Concept:

https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/interfaces/IETHPOSDeposit.sol#L12  https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/interfaces/IPauserRegistry.sol#L2

```pragma solidity =0.8.12;```

https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/pods/EigenPod.sol#L2

```pragma solidity =0.8.12;```

## [G-05]  ++i costs less gas compared to i++ or i += 1

++i costs less gas compared to i++ or i += 1 for an unsigned integer, as pre-increment is cheaper (about 5 gas per iteration). This statement is true even with the optimizer enabled.
##Example: 

i++ increments i and returns the initial value of i, which looks like the following:

```
uint public i = 0; 
i++; // == 0 but i == 1
But ++i returns the actual incremented value.
uint i = 0; 
++i; // == 2 and i == 2 too, so no need for a temporary variable
```
In the first case, the compiler has to create a temporary variable (when used) for returning 1 instead of 2.   
## Proof Of Concept:

  https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/middleware/example/HashThreshold.sol#LL88C9-L88C19

```        taskNumber++;```

https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/libraries/BN254.sol#L172

```for (uint256 i = 0; i < 2; i++) {```


https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/script/utils/Allocator.sol#L10

```        for (uint i = 0; i < recipients.length; i++) {```

https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/script/DepositAndDelegate.s.sol#L18

```        for (uint256 i = 0; i < numStaker; i++) {```

  https://github.com/code-423n4/2023-04-eigenlayer
/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/libraries/Merkle.sol#L137

```        for (uint i = 0; i < numNodesInLayer; i++) {```

https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/libraries/BN254.sol#L172

```        for (uint256 i = 0; i < 2; i++) {```

### 30 Instances


## [G-06] Using unchecked blocks to save gas - Increments in for loop can be unchecked ( save 30-40 gas per loop iteration)
The majority of Solidity for loops increment a uint256 variable that starts at 0. These increment operations never need to be checked for over/underflow because the variable will never reach the max number of uint256 (will run out of gas long before that happens). The default over/underflow check wastes gas in every iteration of virtually every for loop.

  ## Proof Of Concept:

https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/core/StrategyManager.sol#LL466C21-L466C22

```        for(uint256 i = 0; i < queuedWithdrawals.length; i++) {```


 
### 63 Instances















 
  


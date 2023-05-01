# EigenLayer Contest

## Findings

| ID  | Title 
| ------------- | ------------- |
| [G-01]  | Event is missing indexed keyword  
| [G-02]  | Don’t use require in if statement  
| [G-03]  | Use one require statement instead of two  

## Gas optimizations

### [G-01] Event is missing indexed keyword

**Context**\
[StrategyManager.sol](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol)\
[EigenPodManager.sol](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPodManager.sol)


**Description**\
Consider using indexed keyword in events with 3 or less parameters with type uint, bool and address. Using the indexedkeyword for value types such as uint, bool, and address saves gas costs

**Recommended Mitigation Steps**\
Add indexed keyword in events
```solidity
event StrategyWhitelisterChanged(address indexed previousAddress, address indexed newAddress);
```

**Instances of this issue**
| File name  | Line 
| ------------- | ------------- |
| [StrategyManager.sol](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L85)  | 85  
| [StrategyManager.sol](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L94)  | 94  
| [EigenPodManager.sol](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPodManager.sol#L58)  | 58  
| [EigenPodManager.sol](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPodManager.sol#L61)  | 61  
| [EigenPodManager.sol](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPodManager.sol#L64)  | 64  
| [EigenPod.sol](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPod.sol#L85)  | 85  
| [EigenPod.sol](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPod.sol#L88)  | 88  
| [EigenPod.sol](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPod.sol#L91)  | 91  
| [EigenPod.sol](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPod.sol#L94)  | 94  
| [EigenPod.sol](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPod.sol#L97)  | 97  

### [G-02] Don’t use require in if statement

**Context**\
[StrategyManager.sol](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L635) - Line 635 

**Description**\
Consider using only require keyword for checks. Require statement checks both conditions at once, and if they are not met, the transaction is reverted. On the other hand, when using an if statement with require inside,  the Solidity compiler generates additional bytecode, which results in higher gas usage.


**Recommended Mitigation Steps**

Before migration steps:

```solidity
if (stakerStrategyShares[depositor][strategy] == 0) {
     require(
        stakerStrategyList[depositor].length < MAX_STAKER_STRATEGY_LIST_LENGTH,
        "StrategyManager._addShares: deposit would exceed MAX_STAKER_STRATEGY_LIST_LENGTH"
    );
    stakerStrategyList[depositor].push(strategy);
}
 ```

 After migration steps:

 ```solidity
 require(
stakerStrategyShares[depositor][strategy] == 0 &&
stakerStrategyList[depositor].length < MAX_STAKER_STRATEGY_LIST_LENGTH,
"StrategyManager._addShares: deposit would exceed MAX_STAKER_STRATEGY_LIST_LENGTH"
 );
stakerStrategyList[depositor].push(strategy);
 ```

### [G-03] Use one require statement instead of two

**Context**\
[StrategyManager.sol](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L684) - Line 684 


**Description**\
Using a single require statement with both conditions combined is more gas efficient than using two separate require statements.
When you use two separate require statements, the Solidity compiler generates two separate JUMPDEST instructions, which increases the bytecode size and gas usage.
On the other hand, when you use a single require statement with both conditions combined, the compiler generates a single JUMPDEST instruction, which reduces the bytecode size and gas usage.


**Recommended Mitigation Steps**

Before migration steps:

```solidity
require(depositor != address(0), "StrategyManager._removeShares: depositor cannot be zero address");
require(shareAmount != 0, "StrategyManager._removeShares: shareAmount should not be zero!");
```

After migration steps:

```solidity
require(depositor != address(0) && shareAmount != 0, "Error");
```

*NOTE: If you consider to use this gas optimization you must change the error message to be more clear.*

**Instances of this issue**

| File name  | Line 
| ------------- | ------------- |
| [StrategyManager.sol](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L342)  | 342  
| [StrategyManager.sol](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L631)  | 631  
| [StrategyManager.sol](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L684)  | 684  

# Gas Optimizations

## Summary

|               | Issue         | Instances     |
| :-------------: |:-------------|:-------------:|
| 1  | Use `uint96` for `withdrawalDelayBlocks` and pack its value with the address variable `strategyWhitelister` | 1 |
| 2  | Multiple address/IDs mappings can be combined into a single mapping of an address/id to a struct | 5 |
| 3  | Reorder the check statements in the `_completeQueuedWithdrawal` function  | 4 |
| 4  | Emit events outside the for loops | 1 |
| 5  | `storage` variable should be cached into `memory` variables instead of re-reading them  |  4 |
| 6  | Use `unchecked` blocks to save gas  | 3 |
| 7  | Using `delete` statement can save gas  | 4 |

## Findings

### 1- Use `uint96` for `withdrawalDelayBlocks` and pack its value with the address variable `strategyWhitelister`:

Because the maximum value allowed for `withdrawalDelayBlocks` variable is `MAX_WITHDRAWAL_DELAY_BLOCKS = 50400` its respective value will not overflow 2^96, so the variable `withdrawalDelayBlocks` can be of type `uint96` (or even less uint64, uint32) and its value can subsequently be packed with the storage variable (address) `strategyWhitelister` to save gas **(Saves 1 SLOT: 2k gas)**.

The following line of codes must be changed :

File: StrategyManagerStorage.sol [Line 36-44](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManagerStorage.sol#L36-L44)

```
/// @notice Permissioned role, which can be changed by the contract owner. Has the ability to edit the strategy whitelist
address public strategyWhitelister;

/**
* @notice Minimum delay enforced by this contract for completing queued withdrawals. Measured in blocks, and adjustable by this contract's owner,
* up to a maximum of `MAX_WITHDRAWAL_DELAY_BLOCKS`. Minimum value is 0 (i.e. no delay enforced).
* @dev Note that the withdrawal delay is not enforced on withdrawals of 'beaconChainETH', as the EigenPods have their own separate delay mechanic
* and we want to avoid stacking multiple enforced delays onto a single withdrawal.
*/
uint256 public withdrawalDelayBlocks;
```

The optimized code will be :

```
/// @notice Permissioned role, which can be changed by the contract owner. Has the ability to edit the strategy whitelist
address public strategyWhitelister;

/**
* @notice Minimum delay enforced by this contract for completing queued withdrawals. Measured in blocks, and adjustable by this contract's owner,
* up to a maximum of `MAX_WITHDRAWAL_DELAY_BLOCKS`. Minimum value is 0 (i.e. no delay enforced).
* @dev Note that the withdrawal delay is not enforced on withdrawals of 'beaconChainETH', as the EigenPods have their own separate delay mechanic
* and we want to avoid stacking multiple enforced delays onto a single withdrawal.
*/
uint96 public withdrawalDelayBlocks;
```

### 2- Multiple address/IDs mappings can be combined into a single mapping of an address/id to a struct :

Saves a storage slot for the mapping. Depending on the circumstances and sizes of types, can avoid a Gsset (20000 gas) per mapping combined. Reads and subsequent writes can also be cheaper when a function requires both values and they both fit in the same storage slot. Finally, if both fields are accessed in the same function, can save ~42 gas per access due to not having to recalculate the key's keccak256 hash (Gkeccak256 - 30 gas) and that calculation's associated stack operations.

There are 5 instances of this issue :

```
File: StrategyManagerStorage.sol

25:     mapping(address => uint256) public nonces;
49:     mapping(address => mapping(IStrategy => uint256)) public stakerStrategyShares;
51:     mapping(address => IStrategy[]) public stakerStrategyList;
55:     mapping(address => uint256) public numWithdrawalsQueued;
68:     mapping(address => uint256) public beaconChainETHSharesToDecrementOnWithdrawal;
```

These mappings could be refactored into the following struct and mapping for example :

```
struct Staker {
    uint256 nonces;
    uint256 numWithdrawalsQueued;
    uint256 beaconChainETHSharesToDecrementOnWithdrawal;
    IStrategy[] stakerStrategyList;
    mapping(IStrategy => uint256) stakerStrategyShares;
}
    
mapping(uint256 => Staker) public stakers;
```

### 3- Reorder the check statements in the `_completeQueuedWithdrawal` function :

The checks statements at the start of the `_completeQueuedWithdrawal` function should be reodered to save gas when the call reverts, in fact as the `slasher.canWithdraw` check makes an external call to anther contract it should be the last check to allow the transaction to revert early without costing more gas (it doesn't make sense to call `slasher.canWithdraw` and then to check if the withdrawer address is correct for example), and its the same for the check on `withdrawalRootPending` which require the call to the `calculateWithdrawalRoot` function beforehand.

The issue occurs in :

File: StrategyManager.sol [Line 746-769](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L746-L769)
```
// find the withdrawalRoot
bytes32 withdrawalRoot = calculateWithdrawalRoot(queuedWithdrawal);

require(
    withdrawalRootPending[withdrawalRoot],
    "StrategyManager.completeQueuedWithdrawal: withdrawal is not pending"
);

require(
    slasher.canWithdraw(queuedWithdrawal.delegatedAddress, queuedWithdrawal.withdrawalStartBlock, middlewareTimesIndex),
    "StrategyManager.completeQueuedWithdrawal: shares pending withdrawal are still slashable"
);

// enforce minimum delay lag (not applied to withdrawals of 'beaconChainETH', since the EigenPods enforce their own delay)
require(queuedWithdrawal.withdrawalStartBlock + withdrawalDelayBlocks <= block.number 
        || queuedWithdrawal.strategies[0] == beaconChainETHStrategy,
    "StrategyManager.completeQueuedWithdrawal: withdrawalDelayBlocks period has not yet passed"
);

require(
    msg.sender == queuedWithdrawal.withdrawerAndNonce.withdrawer,
    "StrategyManager.completeQueuedWithdrawal: only specified withdrawer can complete a queued withdrawal"
);
```

The check statements should be rearranged as follow to save gas :

```
require(
    msg.sender == queuedWithdrawal.withdrawerAndNonce.withdrawer,
    "StrategyManager.completeQueuedWithdrawal: only specified withdrawer can complete a queued withdrawal"
);

// enforce minimum delay lag (not applied to withdrawals of 'beaconChainETH', since the EigenPods enforce their own delay)
require(queuedWithdrawal.withdrawalStartBlock + withdrawalDelayBlocks <= block.number 
        || queuedWithdrawal.strategies[0] == beaconChainETHStrategy,
    "StrategyManager.completeQueuedWithdrawal: withdrawalDelayBlocks period has not yet passed"
);

// find the withdrawalRoot
bytes32 withdrawalRoot = calculateWithdrawalRoot(queuedWithdrawal);

require(
    withdrawalRootPending[withdrawalRoot],
    "StrategyManager.completeQueuedWithdrawal: withdrawal is not pending"
);

require(
    slasher.canWithdraw(queuedWithdrawal.delegatedAddress, queuedWithdrawal.withdrawalStartBlock, middlewareTimesIndex),
    "StrategyManager.completeQueuedWithdrawal: shares pending withdrawal are still slashable"
);
```

### 4- Emit events outside the for loops :

Putting an event inside a for loop means that the event will be emitted at each iteration which will cost a lot of gas in the end, you should consider emitting a global event outside the for loop when possible.

There is 1 instance of this issue :

File: StrategyManager.sol [Line 376](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L376)
```
emit ShareWithdrawalQueued(msg.sender, nonce, strategies[i], shares[i]);
```

The event in the code linked above is emitted at each iteration which can be avoided by emitting a final event after the for loop containing all the strategies and correspanding withdrawed shares as follows :

```
emit SharesWithdrawalQueued(msg.sender, nonce, strategies, shares);
```


### 5- `storage` variable should be cached into `memory` variables instead of re-reading them :

The instances below point to the second+ access of a state variable within a function, the caching of a state variable replaces each Gwarmaccess (100 gas) with a much cheaper stack read, thus saves **100gas** for each instance.

There are 4 instances of this issue :

File: StrategyBase.sol [Line 92-105](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/strategies/StrategyBase.sol#L92-L105)

In the code linked above the value of `totalShares` is read multiple times (3) from storage and it's value does not change so it should be cached into a memory variable in order to save gas by avoiding multiple reads from storage.

File: StrategyBase.sol [Line 173-176](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/strategies/StrategyBase.sol#L173-L176)

In the code linked above the value of `totalShares` is read multiple times (2) from storage and it's value does not change so it should be cached into a memory variable in order to save gas by avoiding multiple reads from storage.

File: StrategyBase.sol [Line 198-201](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/strategies/StrategyBase.sol#L198-L201)

In the code linked above the value of `totalShares` is read multiple times (2) from storage and it's value does not change so it should be cached into a memory variable in order to save gas by avoiding multiple reads from storage.

File: EigenPod.sol [Line 333-355](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPod.sol#L333-L355)

In the code linked above the value of `validatorStatus[validatorIndex]` is read multiple times (2) from storage and it's value does not change so it should be cached into a memory variable in order to save gas by avoiding multiple reads from storage.


### 6- Use `unchecked` blocks to save gas :

Solidity version 0.8+ comes with implicit overflow and underflow checks on unsigned integers. When an overflow or an underflow isn’t possible (as an example, when a comparison is made before the arithmetic operation), some gas can be saved by using an unchecked block.

There are 3 instances of this issue:

File: StrategyManager.sol [Line 193](https://github.com/code-423n4/2023-04-eigenlayer/blob/398cc428541b91948f717482ec973583c9e76232/src/contracts/core/StrategyManager.sol#L193)
```
amount -= debt;
```

In the code linked above the decrement of the `amount` value by `debt` cannot underflow because of the operation in line [190](https://github.com/code-423n4/2023-04-eigenlayer/blob/398cc428541b91948f717482ec973583c9e76232/src/contracts/core/StrategyManager.sol#L190) which ensures that we always have `amount >= debt`, so the operation should be marked as `unchecked` to save gas. 


File: StrategyManager.sol [Line 827](https://github.com/code-423n4/2023-04-eigenlayer/blob/398cc428541b91948f717482ec973583c9e76232/src/contracts/core/StrategyManager.sol#L827)
```
amount -= amountToDecrement;
```

In the code linked above the decrement of the `amount` value by `amountToDecrement` cannot underflow because of the check in line [824](https://github.com/code-423n4/2023-04-eigenlayer/blob/398cc428541b91948f717482ec973583c9e76232/src/contracts/core/StrategyManager.sol#L824) ensures that we always have `amount > amountToDecrement`, so the operation should be marked as `unchecked` to save gas. 


File: StrategyManager.sol [Line 829](https://github.com/code-423n4/2023-04-eigenlayer/blob/398cc428541b91948f717482ec973583c9e76232/src/contracts/core/StrategyManager.sol#L829)
```
beaconChainETHSharesToDecrementOnWithdrawal[staker] = (amountToDecrement - amount);
```

The operation in the code linked above cannot underflow because of the check in line [824](https://github.com/code-423n4/2023-04-eigenlayer/blob/398cc428541b91948f717482ec973583c9e76232/src/contracts/core/StrategyManager.sol#L824), so it should be marked as `unchecked` to save gas. 


### 7- Using `delete` statement can save gas :

When reseting a variable to its default value (0 for uint or false for boolean) it's cost less gas to use the `delete` operator rather than assigning the default value to the variable.

There are 4 instances of this issue :

File: StrategyManager.sol [Line 554](https://github.com/code-423n4/2023-04-eigenlayer/blob/398cc428541b91948f717482ec973583c9e76232/src/contracts/core/StrategyManager.sol#L554)
```
withdrawalRootPending[withdrawalRoot] = false;
```

File: StrategyManager.sol [Line 612](https://github.com/code-423n4/2023-04-eigenlayer/blob/398cc428541b91948f717482ec973583c9e76232/src/contracts/core/StrategyManager.sol#L612)
```
strategyIsWhitelistedForDeposit[strategiesToRemoveFromWhitelist[i]] = false;
```

File: StrategyManager.sol [Line 772](https://github.com/code-423n4/2023-04-eigenlayer/blob/398cc428541b91948f717482ec973583c9e76232/src/contracts/core/StrategyManager.sol#L772)
```
withdrawalRootPending[withdrawalRoot] = false;
```

File: StrategyManager.sol [Line 825](https://github.com/code-423n4/2023-04-eigenlayer/blob/398cc428541b91948f717482ec973583c9e76232/src/contracts/core/StrategyManager.sol#L825)
```
beaconChainETHSharesToDecrementOnWithdrawal[staker] = 0;
```
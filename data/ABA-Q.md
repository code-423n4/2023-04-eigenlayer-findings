# QA Report for contest

## Overview
During the audit, 1 low, 4 non-critical and 3 refactoring issues were found.

### Low Risk Issues

Total: 1 instances over 1 issues

|#|Issue|Instances|
|-|:-|:-:|
| [L-01] | `StrategyManager._removeShares` does not check for 0 address; reverts with misleading message | 1 |

### Refactoring Issues

Total: 5 instances over 3 issues

|#|Issue|Instances|
|-|:-|:-:|
| [R-01]| Reuse `userShares` in `StrategyManager.recordOvercommittedBeaconChainETH` calculation | 1 |
| [R-02]| Use already defined `PAUSE_ALL` instead of `type(uint256).max` | 3 |
| [R-03]| Add `createIfNotExisting` flag on `EigenPodManager.stake` | 1 |

### Non-critical Issues

Total: 8 instances over 4 issues

|#|Issue|Instances|
|-|:-|:-:|
| [NC-01]| If `amount` is 0 the executions passes through in `StrateguManager.recordOvercommittedBeaconChainETH` | 1 |
| [NC-02]| Check that argument `_pauserRegistry` from `Pausable._initializePauser` is a contract | 1 |
| [NC-03]| Remove redundant functions that wrap over other functions from `StrategyBase` | 3 |
| [NC-04]| Misleading or incorrect documentation | 3 |

#
## Low Risk Issues (1)
#

### [L-01] `StrategyManager._removeShares` does not check for 0 address; reverts with misleading message
##### Description

Wehn withdrawing from the project, execution flow reaches `StrategyManager._removeShares`. 
This function takes an strategy object (address) as one of its parameter but it does not check if it is 0.

```Solidity
        // sanity checks on inputs
        require(depositor != address(0), "StrategyManager._removeShares: depositor cannot be zero address");
        require(shareAmount != 0, "StrategyManager._removeShares: shareAmount should not be zero!");
```

Execution will revert but only because of the 0 address strategy not being found in `_removeStrategyFromStakerStrategyList`

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L736
```Solidity
            require(j != stratsLength, "StrategyManager._removeStrategyFromStakerStrategyList: strategy not found");
```

`_removeShares` expects callers to check the address but not all flows do check this. Examples
    - `queueWithdrawal` -> **_removeShares**
    - `slashShares` -> **_removeShares**

As such, if a user mistakenly adds the 0 address in the list of strategies to withdraw from, when calling queueWithdrawal, or the admin when calling slashShares, 
then the misleading _strategy not found_ revert message will be seen.

##### Instances (1)

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L683-L685

##### Recommendation

Add 0 address check in `_removeShares`.

#

#### Refactoring Issues (3)
#

### [R-01] Reuse `userShares` in `StrategyManager.recordOvercommittedBeaconChainETH` calculation
##### Description

In `StrategyManager` at line 191 debt is calculate as so:

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L191
```Solidity
    uint256 debt = amount - userShares;
```

and at line 193 `amount` is then set to its new value:

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L193
```Solidity
    amount -= debt;
```

There is a redundant subtraction here that can be changed to

```Solidity
    amount = userShares;
```

##### Instances (1)

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L191-L193

##### Recommendation

Change line 193 to:

```Solidity
    amount = userShares;
```
#

### [R-02] Use already defined `PAUSE_ALL` instead of `type(uint256).max`
##### Description

The `Pausable` contract declares a `PAUSE_ALL` constant with the value `type(uint256).max`

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/permissions/Pausable.sol#L23
```Solidity
    uint256 constant internal PAUSE_ALL = type(uint256).max;
```

But further down in the contract, in the function `Pausable.pauseAll` that is used to pause all functionalities of a contract, it is not used.
```Solidity
    /**
     * @notice Alias for `pause(type(uint256).max)`.
     */
    function pauseAll() external onlyPauser {
        _paused = type(uint256).max;
        emit Paused(msg.sender, type(uint256).max);
    }
```

##### Instances (3)

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/permissions/Pausable.sol#L78-L84

##### Recommendation

Change `type(uint256).max)` to `PAUSE_ALL` in the contest of `Pausable.pauseAll`.

#

### [R-03] Add "createIfNotExisting" flag on `EigenPodManager.stake`
##### Description

In `EigenPodManager`, the function `stake` creates a pod if the one you want to stake in does not exists (as it's documented to do).
Although this is to be a QoL feature, when staking one does not consider naturally to also create the staking environment. 

Consider adding a flag that if true will create the pod if not existing.

##### Instances

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPodManager.sol#L112

##### Recommendation

Add a flag that if true will create the pod if not existing, do not have this behavior as default.

#

## Non-critical Issues (4)

#

### [NC-01] If `amount` is 0 the executions passes through in `StrateguManager.recordOvercommittedBeaconChainETH`
##### Description

In `StrateguManager.recordOvercommittedBeaconChainETH` there is a check that if amount is not 0 then `_removeShares` should not be called.

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L196-L198
```
    if (amount != 0) {
        _removeShares(overcommittedPodOwner, beaconChainETHStrategyIndex, beaconChainETHStrategy, amount);            
    }
```

This check should include all the leftover code as it would call a `decreaseDelegatedShares` with an empty amount to withdrawal.

##### Instances (1)

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L196-L198

##### Recommendation

Add all the code after the check in the if block.

#

### [NC-02] Check that argument `_pauserRegistry` from `Pausable._initializePauser` is a contract
##### Description

The currently done checks in the function `Pausable._initializePauser` for the `_pauserRegistry` do not include a contract check.
As such, an EOA can be set.

```Solidity
    function _initializePauser(IPauserRegistry _pauserRegistry, uint256 initPausedStatus) internal {
        require(
            address(pauserRegistry) == address(0) && address(_pauserRegistry) != address(0),
            "Pausable._initializePauser: _initializePauser() can only be called once"
        );
```

##### Instances (1)

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/permissions/Pausable.sol#L55-L59

##### Recommendation

Add a check that `_pauserRegistry` should be a contract.

#

### [NC-03] Remove redundant functions that wrap over other functions from `StrategyBase`
##### Description

In `StrategyBase` there are several functions where the documentation indicates

`* @notice In contrast to <next_function>, this function **may** make state modifications`

while simply they simply call the function that, in its turn indicatesthat it does not modify state changes.

```Solidity
    /**
     * @notice Used to convert a number of shares to the equivalent amount of underlying tokens for this strategy.
     * @notice In contrast to `sharesToUnderlying`, this function guarantees no state modifications
     * @param amountShares is the amount of shares to calculate its conversion into the underlying token
     * @dev Implementation for these functions in particular may vary signifcantly for different strategies
     */
    function sharesToUnderlyingView(uint256 amountShares) public view virtual override returns (uint256) {
        if (totalShares == 0) {
            return amountShares;
        } else {
            return (_tokenBalance() * amountShares) / totalShares;
        }
    }


    /**
     * @notice Used to convert a number of shares to the equivalent amount of underlying tokens for this strategy.
     * @notice In contrast to `sharesToUnderlyingView`, this function **may** make state modifications
     * @param amountShares is the amount of shares to calculate its conversion into the underlying token
     * @dev Implementation for these functions in particular may vary signifcantly for different strategies
     */
    function sharesToUnderlying(uint256 amountShares) public view virtual override returns (uint256) {
        return sharesToUnderlyingView(amountShares);
    }
```

All these functions are view so they cannot change state.

##### Instances (3)

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/strategies/StrategyBase.sol#L166-L188
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/strategies/StrategyBase.sol#L190-L213
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/strategies/StrategyBase.sol#L215-L229

##### Recommendation

Leave only one example of each (the one without the `View` in the name, as this is not standard).

#

### [NC-04] Misleading or incorrect documentation
##### Description

There are places in the project where there is either incorrect or misleading code documentation (either via NatSpec or inline).
These need to be corrected

##### Instances (3)

- `StrategyManager.depositBeaconChainETH` declares the `amount` param 2 times, the second time being a leftover from a copy-paste ([code](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L161))

- in `StrategyManager` functions `addStrategiesToDepositWhitelist` and `removeStrategiesFromDepositWhitelist` document that they are `Owner-only function` when in fact they are executable only by the strategy whitelister (`onlyStrategyWhitelister`) ([code](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L591-L619))

- in `BeaconChainProofs.getBalanceFromBalanceRoot` remove incorrect "is" from documentation ([code](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/libraries/BeaconChainProofs.sol#L172))

##### Recommendation

Resolve the mentioned issues

#


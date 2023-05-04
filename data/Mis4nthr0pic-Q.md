# QA/Low

### Issues Template

| Letter |     Name     |    Description     |
| :----: | :----------: | :----------------: |
|   L    |   Low risk   |   Potential risk   |
|   N    | Non-critical | Non risky findings |
|   R    |   Refactor   | Changing the code  |

| Total Found Issues |  2  |
| :----------------: | :-: |

### Non-Critical

| Count  | Explanation                            | Instances |
| :----: | :------------------------------------- | :-------: |
| [N-01] | Natspec contains incorrect information |     2     |
| [N-02] | Missing or incomplete Natspec          |    21     |

| Total Non-Critical Issues |  2  |
| :-----------------------: | :-: |

## [N-01] Natspec contains incorrect information

Comments and natspec should contain accurate information, as they may be relied upon by users or developers to understand function inputs and operation.

### [L160](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#160) - In the natspec for function depositBeaconChainETH, param `amount` is listed twice with two different descriptions.

It appears that the second `amount` is incorrectly included.

```solidity
contracts/StrategyManager.sol

/**
@notice Deposits `amount` of beaconchain ETH into this contract on behalf of `staker`
@param staker is the entity that is restaking in eigenlayer,
@param amount is the amount of beaconchain ETH being restaked,
@param amount is the amount of token to be deposited in the strategy by the depositor
@dev Only callable by EigenPodManager.
*/
function depositBeaconChainETH(address staker, uint256 amount)
external
onlyEigenPodManager
onlyWhenNotPaused(PAUSED_DEPOSITS)
onlyNotFrozen(staker)
nonReentrant
```

### [L52](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#52) - Parameter shares is indicated by the comments to be the total number of shares that depositor has in a particular strategy.

Upon examination, you will note that in the function `_depositIntoStrategy`, the natspec indicates that the returned value shares is the amount of new shares in a given strategy. This value is included in the Deposit event emitted at the end of the function.

_These statements are contradictory and could lead to confusion when establishing off-chain monitoring of the event._

```solidity
contracts/StrategyManager.sol
@notice Emitted when a new deposit occurs on behalf of `depositor`.
@param depositor Is the staker who is depositing funds into EigenLayer.
@param strategy Is the strategy that `depositor` has deposited into.
@param token Is the token that `depositor` deposited.
@param shares Is the number of shares `depositor` has in `strategy`.
event Deposit(address depositor, IERC20 token, IStrategy strategy, uint256 shares);
```

## [N-02] Natspec missing or incomplete for some elements

It is recommended that Solidity contracts are fully annotated using NatSpec for all public interfaces. \*Note that we have chosen to highlight additional internal functions and events that are not fully annotated, because the developer decided to include some natspec for these functions/events.

### [L456](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#456) - `completeQueuedWithdrawals` missing params queuedWithdrawals, tokens, middlewareTimesIndexes, and receiveAsTokens.

```solidity
/**
* @notice Used to complete the specified `queuedWithdrawals`. The function caller must match `queuedWithdrawals[...].withdrawer`
* @dev Array-ified version of `completeQueuedWithdrawal`
* @dev middlewareTimesIndex should be calculated off chain before calling this function by finding the first index that satisfies `slasher.canWithdraw`
*/
function completeQueuedWithdrawals(
    QueuedWithdrawal[] calldata queuedWithdrawals,
    IERC20[][] calldata tokens,
    uint256[] calldata middlewareTimesIndexes,
    bool[] calldata receiveAsTokens
)
    external
    onlyWhenNotPaused(PAUSED_WITHDRAWALS)
    // check that the address that the staker *was delegated to* – at the time that they queued the withdrawal – is not frozen
    nonReentrant
```

### [L581](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#581) - `setWithdrawalDelayBlocks` missing param `withdrawalDelayBlocks`.

```
/// @notice Owner-only function for modifying the value of the `withdrawalDelayBlocks` variable.
function setWithdrawalDelayBlocks(
    uint256 _withdrawalDelayBlocks
) external onlyOwner
```

### [L586](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#586) - `setStrategyWhitelister` missing param newStrategyWhitelister.

```
/// @notice Owner-only function to change the `strategyWhitelister` address.
function setStrategyWhitelister(
    address newStrategyWhitelister
) external onlyOwner
```

### [L592](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#592) - `addStrategiesToDepositWhitelist` missing param strategiesToWhitelist.

```
/// @notice Owner-only function that adds the provided Strategies to the 'whitelist' of strategies that stakers can deposit into
function addStrategiesToDepositWhitelist(
    IStrategy[] calldata strategiesToWhitelist
) external onlyStrategyWhitelister
```

### [L606](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#606) - `removeStrategiesFromDepositWhitelist` missing param strategiesToRemoveFromWhitelist.

```
/// @notice Owner-only function that removes the provided Strategies from the 'whitelist' of strategies that stakers can deposit into
function removeStrategiesFromDepositWhitelist(
    IStrategy[] calldata strategiesToRemoveFromWhitelist
) external onlyStrategyWhitelister
```

### [L623](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#623) - `_addShares` missing param depositor, strategy, and shares.

```
/**
* @notice This function adds `shares` for a given `strategy` to the `depositor` and runs through the necessary update logic.
* @dev In particular, this function calls `delegation.increaseDelegatedShares(depositor, strategy, shares)` to ensure that all
* delegated shares are tracked, increases the stored share amount in `stakerStrategyShares[depositor][strategy]`, and adds `strategy`
* to the `depositor`'s list of strategies, if it is not in the list already.
*/
function _addShares(
    address depositor,
    IStrategy strategy,
    uint256 shares
) internal
```

### [L675](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#675) - `_removeShares` missing param depositor, strategyIndex, strategy, uint256 shareAmount, and return unnamed bool.

```
/**
* @notice Decreases the shares that `depositor` holds in `strategy` by `shareAmount`.
* @dev If the amount of shares represents all of the depositor`s shares in said strategy,
* then the strategy is removed from stakerStrategyList[depositor] and 'true' is returned. Otherwise 'false' is returned.
*/
function _removeShares(
    address depositor,
    uint256 strategyIndex,
    IStrategy strategy,
    uint256 shareAmount
) internal returns (bool)
```

### [L711](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#711) - `_removeStrategyFromStakerStrategyList` missing param depositor, strategyIndex, and strategy.

```
/**
* @notice Removes `strategy` from `depositor`'s dynamic array of strategies, i.e. from `stakerStrategyList[depositor]`
* @dev the provided `strategyIndex` input is optimistically used to find the strategy quickly in the list. If the specified
* index is incorrect, then we revert to a brute-force search.
*/
function _removeStrategyFromStakerStrategyList(
    address depositor,
    uint256 strategyIndex,
    IStrategy strategy
) internal
```

### [L745](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#745) -

`_completeQueuedWithdrawal` missing param queuedWithdrawal, tokens, middlewareTimesIndex and receiveAsTokens.

```
/**
* @notice Internal function for completeing the given `queuedWithdrawal`.
*/
function _completeQueuedWithdrawal(
    QueuedWithdrawal calldata queuedWithdrawal,
    IERC20[] calldata tokens,
    uint256 middlewareTimesIndex,
    bool receiveAsTokens
) internal onlyNotFrozen(queuedWithdrawal.delegatedAddress)
```

### [L808](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#808) - `_undelegate` missing param depositor.

```
/**
* @notice If the `depositor` has no existing shares, then they can `undelegate` themselves.
* This allows people a "hard reset" in their relationship with EigenLayer after withdrawing all of their stake.
*/
function _undelegate(address depositor) internal
```

### [L817](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#817) - `_withdrawBeaconChainETH` missing param staker, recipient, and uint256 amount.

```
/*
* @notice Withdraws `amount` of virtual 'beaconChainETH' shares from `staker`, with any succesfully withdrawn funds going to `recipient`.
* @dev First, the amount is drawn-down by any applicable 'beaconChainETHSharesToDecrementOnWithdrawal' that the staker has,
* before passing any remaining amount (if applicable) onto a call to the `eigenPodManager.withdrawRestakedBeaconChainETH` function.
*/
function _withdrawBeaconChainETH(
    address staker,
    address recipient,
    uint256 amount
) internal
```

### [L838](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#838) - `_setWithdrawalDelayBlocks` missing param `_withdrawalDelayBlocks`.

```
/// @notice internal function for changing the value of `withdrawalDelayBlocks`. Also performs sanity check and emits an event.
function _setWithdrawalDelayBlocks(
    uint256 _withdrawalDelayBlocks
) internal
```

### [L845](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#845) - `_setStrategyWhitelister` missing param `newStrategyWhitelister`.

```
/// @notice Internal function for modifying the `strategyWhitelister`. Used inside of the `setStrategyWhitelister` and `initialize` functions.
function _setStrategyWhitelister(address newStrategyWhitelister) internal
```

### [L870](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#870) - `stakerStrategyListLength` missing param staker and return unnamed uint256.

```
/// @notice Simple getter function that returns `stakerStrategyList[staker].length`.
function stakerStrategyListLength(
    address staker
) external view returns (uint256)
```

### [L876](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#876) - `calculateWithdrawalRoot` missing param queuedWithdrawal and return unnamed bytes32.

```
/// @notice Returns the keccak256 hash of `queuedWithdrawal`.
function calculateWithdrawalRoot(
    QueuedWithdrawal memory queuedWithdrawal
) public pure returns (bytes32)
```

### [L81](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#81) - `WithdrawalCompleted` missing param depositor, nonce, withdrawer, and withdrawalRoot.

```
/// @notice Emitted when a queued withdrawal is completed
event WithdrawalCompleted(address indexed depositor, uint96 nonce, address indexed withdrawer, bytes32 withdrawalRoot);
```

### [L84](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#84) - `StrategyWhitelisterChanged` missing param previousAddress, newAddress.

```
/// @notice Emitted when the `strategyWhitelister` is changed
event StrategyWhitelisterChanged(address previousAddress, address newAddress);
```

### [L87](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#87) - `StrategyAddedToDepositWhitelist` missing param strategy.

```
/// @notice Emitted when a strategy is added to the approved list of strategies for deposit
event StrategyAddedToDepositWhitelist(IStrategy strategy);
```

### [L90](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#90) - `StrategyRemovedFromDepositWhitelist` missing param strategy.

```
/// @notice Emitted when a strategy is removed from the approved list of strategies for deposit
eveGggnt StrategyRemovedFromDepositWhitelist(IStrategy strategy);
```

### [L93](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#93) - `WithdrawalDelayBlocksSet` missing param previousValue, newValue.

```
/// @notice Emitted when the `withdrawalDelayBlocks` variable is modified from `previousValue` to `newValue`.
event WithdrawalDelayBlocksSet(uint256 previousValue, uint256 newValue);
```

### File: src/contracts/core/StrategyManagerStorage.sol

### [L72](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#72) - `constructor` has no natspec comments.

```
constructor(IDelegationManager _delegation, IEigenPodManager _eigenPodManager ISlasher _slasher)

| Total Low issues |
|------------------|

| Risk   | Issues Details                                                                                    | Number        |
|--------|---------------------------------------------------------------------------------------------------|---------------|
| [L-01] | `MAX_STAKER_STRATEGY_LIST_LENGTH` is not implemented correctltly                                  | 1             |
| [L-02] | `_setWithdrawalDelayBlocks()` lacks sanity checks                                                 | 1             |

## [L-01] `MAX_STAKER_STRATEGY_LIST_LENGTH` is not implemented correctltly

#### Impact

The [`_addShares()`](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L629) function in the `StrategyManager.sol` contract is currently implemented in a way that causes it to revert when the depositor's strategy list is equal to `MAX_STAKER_STRATEGY_LIST_LENGTH`. This is not correct, as it should only revert when the list is greater than max length.

#### Proof of Concept

The `stakerStrategyList` mapping has a maximum length of `MAX_STAKER_STRATEGY_LIST_LENGTH`. Therefore, the `_addShares()` function should only revert when `stakerStrategyList[depositor].length > MAX_STAKER_STRATEGY_LIST_LENGTH`:

```solidity
    function _addShares(address depositor, IStrategy strategy, uint256 shares) internal {
        // sanity checks on inputs
        require(depositor != address(0), "StrategyManager._addShares: depositor cannot be zero address");
        require(shares != 0, "StrategyManager._addShares: shares should not be zero!");

        // if they dont have existing shares of this strategy, add it to their strats
        if (stakerStrategyShares[depositor][strategy] == 0) {
            require(
                stakerStrategyList[depositor].length < MAX_STAKER_STRATEGY_LIST_LENGTH,
                "StrategyManager._addShares: deposit would exceed MAX_STAKER_STRATEGY_LIST_LENGTH"
            );
            stakerStrategyList[depositor].push(strategy);
        }

        // add the returned shares to their existing shares for this strategy
        stakerStrategyShares[depositor][strategy] += shares;

        // if applicable, increase delegated shares accordingly
        delegation.increaseDelegatedShares(depositor, strategy, shares);
    }
```

As you can see in the code, the function currently throws when the depositor tries to add the 32nd strategy to their list, which is not consistent with the maximum length of the list.

Note that while this issue may not directly impact users of EigenLayer, it could have an impact on protocols built on top of EigenLayer.
    
#### Tools Used

Manual Review

#### Recommended Mitigation Steps

We recommend to update the _addShares() function as follows:

```solidity
    function _addShares(address depositor, IStrategy strategy, uint256 shares) internal {
        // sanity checks on inputs
        require(depositor != address(0), "StrategyManager._addShares: depositor cannot be zero address");
        require(shares != 0, "StrategyManager._addShares: shares should not be zero!");

        // if they dont have existing shares of this strategy, add it to their strats
        if (stakerStrategyShares[depositor][strategy] == 0) {
            require(
                stakerStrategyList[depositor].length <= MAX_STAKER_STRATEGY_LIST_LENGTH,
                "StrategyManager._addShares: deposit would exceed MAX_STAKER_STRATEGY_LIST_LENGTH"
            );
            stakerStrategyList[depositor].push(strategy);
        }

        // add the returned shares to their existing shares for this strategy
        stakerStrategyShares[depositor][strategy] += shares;

        // if applicable, increase delegated shares accordingly
        delegation.increaseDelegatedShares(depositor, strategy, shares);
    }
```

This updated function will only revert when the depositor's strategy list exceeds `MAX_STAKER_STRATEGY_LIST_LENGTH`.

## [L-02] `_setWithdrawalDelayBlocks()` lacks sanity checks

#### Impact

The [`_setWithdrawalDelayBlocks()`](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L839-L843) function lacks sanity checks to prevent the `withdrawalDelayBlocks` variable from being set to 0, which could allow for malicious withdrawals to be executed instantly.

#### Proof of Concept

The `withdrawalDelayBlocks` variable is used to enforce a 7-day delay window for completing queued withdrawals in the `StrategyManager.sol` contract. This delay is measured in blocks and can be adjusted by the contract owner, but is subject to a maximum value of `MAX_WITHDRAWAL_DELAY_BLOCKS`. However, the function is missing a `MIN_WITHDRAWAL_DELAY_BLOCKS` check, which means that the delay could be set to 0.

```solidity
    function _setWithdrawalDelayBlocks(uint256 _withdrawalDelayBlocks) internal {
        require(_withdrawalDelayBlocks <= MAX_WITHDRAWAL_DELAY_BLOCKS, "StrategyManager.setWithdrawalDelay: _withdrawalDelayBlocks too high");
        emit WithdrawalDelayBlocksSet(withdrawalDelayBlocks, _withdrawalDelayBlocks);
        withdrawalDelayBlocks = _withdrawalDelayBlocks;
    }
```

#### Tools Used

Manual Review

#### Recommended Mitigation Steps

To make the contract more robust, a `MIN_WITHDRAWAL_DELAY_BLOCKS` check should be added to the `_setWithdrawalDelayBlocks()` function to ensure that the withdrawalDelayBlocks variable is not set to 0. This will provide an additional layer of protection against malicious withdrawals.

# Low Risk Issues


## [L-01] Missing checks for address(0x0) in _addShares

the internal function _addShares is missing address(0x0) validation in Istrategy strategy input, this may cause unexpected behavior. because is passing several checking in the differents call, modifiyng the storage.

```
file:src/contracts/core/StrategyManager.sol
function _addShares(
        address depositor,
        IStrategy strategy,
        uint256 shares
    ) internal {
        // sanity checks on inputs
        //@audit nochecking stratgy
        require(
            depositor != address(0),
            "StrategyManager._addShares: depositor cannot be zero address"
        );
        require(
            shares != 0,
            "StrategyManager._addShares: shares should not be zero!"
        );

        // if they dont have existing shares of this strategy, add it to their strats
        if (stakerStrategyShares[depositor][strategy] == 0) {
            require(
                stakerStrategyList[depositor].length <
                    MAX_STAKER_STRATEGY_LIST_LENGTH,
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
https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/core/StrategyManager.sol#L629-L648

recomendation: add require validation 
```
require(strategy != address(0), "address 0 ");
```


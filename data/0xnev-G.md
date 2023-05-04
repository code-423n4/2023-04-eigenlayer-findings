### [G-01] Shift input validation checks to top of function for `StrategyManager.queueWithdrawal`

Checks that involve input validation should come before checks that involve state variables, function calls, and calculations. By doing these checks first, the function is able to revert before wasting gas on external function calls that may ultimately revert if checks does not pass.

Reccomendation:
[StrategyManager.sol#L329-L429](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L329-L429)
```solidity
function queueWithdrawal(
    uint256[] calldata strategyIndexes,
    IStrategy[] calldata strategies,
    uint256[] calldata shares,
    address withdrawer,
    bool undelegateIfPossible
)
    external
    onlyWhenNotPaused(PAUSED_WITHDRAWALS)
    onlyNotFrozen(msg.sender)
    nonReentrant
    returns (bytes32)
{   
    require(strategies.length == shares.length, "StrategyManager.queueWithdrawal: input length mismatch");
    require(withdrawer != address(0), "StrategyManager.queueWithdrawal: cannot withdraw to zero address");

    ...
    
    /// @audit for loop checks can be shifted on top
    for (uint256 i = 0; i < strategies.length;) {
        if (strategies[i] == beaconChainETHStrategy) {
            require(withdrawer == msg.sender,
                "StrategyManager.queueWithdrawal: cannot queue a withdrawal of Beacon Chain ETH to a different address");
            require(strategies.length == 1,
                "StrategyManager.queueWithdrawal: cannot queue a withdrawal including Beacon Chain ETH and other tokens");
            require(shares[i] % GWEI_TO_WEI == 0,
                "StrategyManager.queueWithdrawal: cannot queue a withdrawal of Beacon Chain ETH for an non-whole amount of gwei");
        }   

        // the internal function will return 'true' in the event the strategy was
        // removed from the depositor's array of strategies -- i.e. stakerStrategyList[depositor]
        if (_removeShares(msg.sender, strategyIndexes[strategyIndexIndex], strategies[i], shares[i])) {
            unchecked {
                ++strategyIndexIndex;
            }
        }

        emit ShareWithdrawalQueued(msg.sender, nonce, strategies[i], shares[i]);

        //increment the loop
        unchecked {
            ++i;
        }
    }
    ...

    /// @audit checks can be shifted on top
    if (undelegateIfPossible && stakerStrategyList[msg.sender].length == 0) {
        _undelegate(msg.sender);
    }
    ...
```


### [G-02] Multiple access of mapping/array should use a local variable cache
Caching a mapping's value in a local storage variable when the value is accessed multiple times saves ~42 gas per access due to not having to perform the same offset calculation every time. 

### `stakerStrategyList[depositor]` in `StrategyManager._removeStrategyFromStakerStrategyList`
[StrategyManager.sol#L715-L740](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L715-L740)
```solidity
function _removeStrategyFromStakerStrategyList(address depositor, uint256 strategyIndex, IStrategy strategy) internal {
    // if the strategy matches with the strategy index provided
    /// @audit stakerStrategyList[depositor] can be cached
    if (stakerStrategyList[depositor][strategyIndex] == strategy) {
        // replace the strategy with the last strategy in the list
        /// @audit stakerStrategyList[depositor] can be cached
        stakerStrategyList[depositor][strategyIndex] =
            stakerStrategyList[depositor][stakerStrategyList[depositor].length - 1];
    } else {
        //loop through all of the strategies, find the right one, then replace
        /// @audit stakerStrategyList[depositor] can be cached
        uint256 stratsLength = stakerStrategyList[depositor].length;
        uint256 j = 0;
        for (; j < stratsLength;) {
            /// @audit stakerStrategyList[depositor] can be cached
            if (stakerStrategyList[depositor][j] == strategy) {
                //replace the strategy with the last strategy in the list
                /// @audit stakerStrategyList[depositor] can be cached
                stakerStrategyList[depositor][j] = stakerStrategyList[depositor][stakerStrategyList[depositor].length - 1];
                break;
            }
            unchecked {
                ++j;
            }
        }
        // if we didn't find the strategy, revert
        require(j != stratsLength, "StrategyManager._removeStrategyFromStakerStrategyList: strategy not found");
    }
    // pop off the last entry in the list of strategies
    /// @audit stakerStrategyList[depositor] can be cached
    stakerStrategyList[depositor].pop();
}
```

### `stakerStrategyList[depositor]` and `stakerStrategyShares[depositor]` in `StrategyManager.getDeposits`
[StrategyManager.sol#L857-L868](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L857-L868)
```solidity
function getDeposits(address depositor) external view returns (IStrategy[] memory, uint256[] memory) {
    uint256 strategiesLength = stakerStrategyList[depositor].length;
    uint256[] memory shares = new uint256[](strategiesLength);

    for (uint256 i = 0; i < strategiesLength;) {
        /// @audit stakerStrategyList[depositor] and stakerStrategyShares[depositor] can be cached
        shares[i] = stakerStrategyShares[depositor][stakerStrategyList[depositor][i]];
        unchecked {
            ++i;
        }
    }
    return (stakerStrategyList[depositor], shares);
}
```

### [G-03] State variables should be cached in stack variables rather than re-reading them from storage

### `beaconChainETHStrategy` in `StrategyManager.queueWithdrawal`
[StrategyManager.sol#L359](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L359)
```solidity
function queueWithdrawal(
    uint256[] calldata strategyIndexes,
    IStrategy[] calldata strategies,
    uint256[] calldata shares,
    address withdrawer,
    bool undelegateIfPossible
)
    external
    onlyWhenNotPaused(PAUSED_WITHDRAWALS)
    onlyNotFrozen(msg.sender)
    nonReentrant
    returns (bytes32)
{    
    ...
    for (uint256 i = 0; i < strategies.length;) {
        /// @audit beaconChainETHStrategy can be cached
        if (strategies[i] == beaconChainETHStrategy) {
            require(withdrawer == msg.sender,
                "StrategyManager.queueWithdrawal: cannot queue a withdrawal of Beacon Chain ETH to a different address");
            require(strategies.length == 1,
                "StrategyManager.queueWithdrawal: cannot queue a withdrawal including Beacon Chain ETH and other tokens");
            require(shares[i] % GWEI_TO_WEI == 0,
                "StrategyManager.queueWithdrawal: cannot queue a withdrawal of Beacon Chain ETH for an non-whole amount of gwei");
        }   
    ...
```


### [G-04] Unecessary explicit conversion of `uint256`
[EigenPod.sol#L375](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPod.sol#L375)
[EigenPod.sol#L382](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPod.sol#L382)
[EigenPod.sol#L389](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPod.sol#L389)
[EigenPod.sol#L404](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPod.sol#L404)
[EigenPod.sol#L429](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPod.sol#L429)
```solidity
375:                amountToSend = uint256(withdrawalAmountGwei - REQUIRED_BALANCE_GWEI) * uint256(GWEI_TO_WEI);

382:                eigenPodManager.recordOvercommittedBeaconChainETH(podOwner, beaconChainETHStrategyIndex, uint256(REQUIRED_BALANCE_GWEI - withdrawalAmountGwei) * GWEI_TO_WEI);

389:                amountToSend = uint256(withdrawalAmountGwei - REQUIRED_BALANCE_GWEI) * uint256(GWEI_TO_WEI);

404:                eigenPodManager.restakeBeaconChainETH(podOwner, uint256(withdrawalAmountGwei) * GWEI_TO_WEI);

429:        _sendETH(recipient, uint256(partialWithdrawalAmountGwei) * uint256(GWEI_TO_WEI));
```
`GWEI_TO_WEI` is already initialized as a type `uint256` constant and does not need to be type casted. 

Explicit type conversion of uint64 to uint256 is not required since solidity allows implicit conversion as no information is lost. 
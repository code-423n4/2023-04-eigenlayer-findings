## Summary

### Low Risk Issues List

| Number | Issues Details | Context |
| --- | --- | --- |
| [L-01](#l-01-possible-to-spam-delayedwithdrawalrouter-with-dust-values) | Possible to spam DelayedWithdrawalRouter with dust values | 1   |
| [L-02](#l-02-empty-withdrawal-could-be-created-and-withdrawed) | Empty withdrawal could be created and withdrawed | 1   |
| [L-03](#l-03-missing-emmiting-event) | Missing emmiting event | 2   |

### Non-Critical Issues List

| Number | Issues Details | Context |
| --- | --- | --- |
| [N-01](#n-01-redundant-check) | Redundant check | 1   |
| [N-02](#n-02-redundant-check) | Redundant check | 1   |
| [N-03](#n-03-no-needed-calculation) | No needed calculation | 1   |
| [N-04](#n-04-wrong-comment) | Wrong comment | 1   |
| [N-05](#n-05-wrong-comment) | Wrong comment | 1   |
| [N-06](#n-06-wrong-variable-naming) | Wrong parameter naming | 1   |

* * *

### L-01 Possible to spam DelayedWithdrawalRouter with dust values

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPod.sol#L454

```solidity
    function withdrawBeforeRestaking() external onlyEigenPodOwner hasNeverRestaked {
        mostRecentWithdrawalBlockNumber = uint32(block.number);
        _sendETH(podOwner, address(this).balance);
    }
```

It is possible to create an empty pod and use the 'withdrawBeforeRestaking()' function on the EigenPod contract to spam arbitrary addresses on the DelayedWithdrawalRouter with dust values. This can lead to users having to claim their withdrawals multiple times until a withdrawal with a real value is claimed. Additionally, this can result in a DOS attack on the 'claimableUserDelayedWithdrawals()' view function due to the large number of dusted withdrawals.
To prevent such dust attacks, it is advisable to consider adding a minimum threshold for the 'withdrawBeforeRestaking()' function. This would ensure that only withdrawals with a certain minimum value are processed, thereby avoiding the spamming of arbitrary addresses with dust values.

### L-02 Empty withdrawal could be created and withdrawed

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L329
'queueWithdrawal()' function allows the creation of an empty queued withdrawal. This could potentially mislead external viewers who are monitoring contract events. To avoid confusion and potential misinterpretation, it may be advisable to add a check that the strategies array parameter in 'queueWithdrawal()' is not empty.

### L-03 Missing emmiting event

Consider adding events to the following functions, since analogous functions in the code emit appropriate events:

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPodManager.sol#L139

```solidity
    function recordOvercommittedBeaconChainETH(address podOwner, uint256 beaconChainETHStrategyIndex, uint256 amount) external onlyEigenPod(podOwner) {
        strategyManager.recordOvercommittedBeaconChainETH(podOwner, beaconChainETHStrategyIndex, amount);
    } 
```

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPodManager.sol#L150
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L164
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L182

### N-01 Redundant check

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L196

```solidity
    if (amount != 0) { 
        _removeShares(overcommittedPodOwner, beaconChainETHStrategyIndex, beaconChainETHStrategy, amount);            
    }
```

No need to check for 0 amount here, since _removeShares() function already include such check.

### N-02 Redundant check

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L422

```solidity
    if (undelegateIfPossible && stakerStrategyList[msg.sender].length == 0) { 
        _undelegate(msg.sender);
    }
```

No need to check staker strategy list length since _undelegate() function include such check.

### N-03 No needed calculation

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L193

```solidity
    if (amount > userShares) {
        uint256 debt = amount - userShares;
        beaconChainETHSharesToDecrementOnWithdrawal[overcommittedPodOwner] += debt;
        amount -= debt;
    }
```

Last line in this block could be substituted with more simple:

```solidity
    amount = userShares
```

Due to math equivalence:

```solidity
1. amount -= debt
   debt = amount - userShares

2. amount = amount - (amount - userShares)

3. amount = userShares
```

### N-04 Wrong comment

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L161

```solidity
    /**
     * @notice Deposits `amount` of beaconchain ETH into this contract on behalf of `staker`
     * @param staker is the entity that is restaking in eigenlayer,
     * @param amount is the amount of beaconchain ETH being restaked,
     * @param amount is the amount of token to be deposited in the strategy by the depositor 
     * @dev Only callable by EigenPodManager.
     */
    function depositBeaconChainETH(address staker, uint256 amount)
```

Second comment on @param amount wrong here and should be removed

### N-05 Wrong comment

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L786

```solidity
    // tell the strategy to send the appropriate amount of funds to the depositor 
    queuedWithdrawal.strategies[i].withdraw(
        msg.sender, tokens[i], queuedWithdrawal.shares[i]
    );
```

Comment here stands that funds would be withdrawed to the depositor address, while funds would be sent to the withdrawer address, which is msg.sender here.

### N-06 Wrong variable naming

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L460

```solidity
    function completeQueuedWithdrawals(
        QueuedWithdrawal[] calldata queuedWithdrawals,
        IERC20[][] calldata tokens,
        uint256[] calldata middlewareTimesIndexes,
        bool[] calldata receiveAsTokens 
    ) external
```

In contract code last parameters in completeQueuedWithdrawals() function named as `receiveAsTokens` while docs stand on naming `withdrawAsTokens`:
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/docs/EigenLayer-withdrawal-flow.md
Consider updating naming in code or documentation to remove inconsistency.
## [LOW] _removeStrategyFromStakerStrategyList() should check strategyIndex is not out-of-bounds:

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L715

```solidity
    function _removeStrategyFromStakerStrategyList(address depositor, uint256 strategyIndex, IStrategy strategy) internal {
        // if the strategy matches with the strategy index provided
        if (stakerStrategyList[depositor][strategyIndex] == strategy) {
            // replace the strategy with the last strategy in the list
            stakerStrategyList[depositor][strategyIndex] =
                stakerStrategyList[depositor][stakerStrategyList[depositor].length - 1];
        } else {
            //loop through all of the strategies, find the right one, then replace
            uint256 stratsLength = stakerStrategyList[depositor].length;
            uint256 j = 0;
            for (; j < stratsLength;) {
```

The strategyIndex is used as an optimistic method here, but forget to check it against the stakerStrategyList[depositor].length, which may lead to an array OOB.

## [LOW] No Incentives for watchers to call verifyOvercommittedStake()

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPod.sol#L241

There are no incentives for watchers to call verifyOvercommittedStake(), which may help MEV attackers in some situations.

## [non-critical] completeQueuedWithdrawals() should check queuedWithdrawals.length == tokens.length == middlewareTimesIndexes.length == receiveAsTokens.length

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L456

```solidity
    function completeQueuedWithdrawals(
        QueuedWithdrawal[] calldata queuedWithdrawals,
        IERC20[][] calldata tokens,
        uint256[] calldata middlewareTimesIndexes,
        bool[] calldata receiveAsTokens
    ) external
        onlyWhenNotPaused(PAUSED_WITHDRAWALS)
        // check that the address that the staker *was delegated to* – at the time that they queued the withdrawal – is not frozen
        nonReentrant
    {
        for(uint256 i = 0; i < queuedWithdrawals.length; i++) {
            _completeQueuedWithdrawal(queuedWithdrawals[i], tokens[i], middlewareTimesIndexes[i], receiveAsTokens[i]);
        }
    }
```

## [non-critical] Wrong comment for depositBeaconChainETH in IStrategyManager

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/interfaces/IStrategyManager.sol#L53
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L164

```solidity
     * @dev Only callable by EigenPod for the staker.
     */
    function depositBeaconChainETH(address staker, uint256 amount) external;
```

The comment says depositBeaconChainETH is only callable by EigenPod, but in StrategyManager.sol#L164 it's onlyEigenPodManager:

```solidity
    function depositBeaconChainETH(address staker, uint256 amount)
        external
        onlyEigenPodManager
        onlyWhenNotPaused(PAUSED_DEPOSITS)
        onlyNotFrozen(staker)
        nonReentrant
    {
        // add shares for the enshrined beacon chain ETH strategy
        _addShares(staker, beaconChainETHStrategy, amount);
    }
```
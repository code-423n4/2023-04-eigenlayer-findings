1. Make input in StrategyManager::completeQueuedWithdrawals as a struct

right now the input is not guaranteed to be the same length, it's better to make them in a struct so length checks are not needed

```solidity
function completeQueuedWithdrawals(
        QueuedWithdrawal[] calldata queuedWithdrawals,
        IERC20[][] calldata tokens,
        uint256[] calldata middlewareTimesIndexes,
        bool[] calldata receiveAsTokens
    ) external
```

Recommendation

```solidity
struct Input {
    QueuedWithdrawal a;
    IERC20[] b;
    uint256 c;
    bool d;
}

function completeQueuedWithdrawals(Input[] calldata intputs) {
}
```

-------- \n

2. Same issue as 1.) for slashShares

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L482-L489

-------- \n

3. deposit/withdraw in IStrategy does not need to take token, if only onlyStrategyManager can call and only 1 rewardToken is expected per strategy.

`StrategyBase.sol`

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/strategies/StrategyBase.sol#L78-L86

```solidity
    function deposit(IERC20 token, uint256 amount)
        external
        virtual
        override
        onlyWhenNotPaused(PAUSED_DEPOSITS)
        onlyStrategyManager
        returns (uint256 newShares)
    {
        require(token == underlyingToken, "StrategyBase.deposit: Can only deposit underlyingToken");
```

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/strategies/StrategyBase.sol#L121-L128

```solidity
    function withdraw(address depositor, IERC20 token, uint256 amountShares)
        external
        virtual
        override
        onlyWhenNotPaused(PAUSED_WITHDRAWALS)
        onlyStrategyManager
    {
        require(token == underlyingToken, "StrategyBase.withdraw: Can only withdraw the strategy token");

```

-------- \n


4. Nothing is immediately withdrawable with the use of `DelayedWithdrawalRouter.sol`

However there are 2 doc/comments that still use "immediately withdrawable", which is mis-leading.

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPod.sol#L374

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPod.sol#L389

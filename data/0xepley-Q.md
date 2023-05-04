1): `0` address check There isn't any kind of zero address check in https://github.com/Layr-Labs/eigenlayer-contracts/blob/dbeb85bcd0476e06b8feebf07e33f8a53d54c029/src/contracts/core/StrategyManager.sol#L182 although this function is calling other functions which have `zero` address check, but it will be better and gas saving if you use `zero` address before 

2): `block.number` is store in `uint32` https://github.com/Layr-Labs/eigenlayer-contracts/blob/dbeb85bcd0476e06b8feebf07e33f8a53d54c029/src/contracts/pods/EigenPod.sol#L455 which will runout in the year 2106 after that it won't work

3): No check of `zero address` on `initialize` in the `EigenPodManager.sol`. Check here https://github.com/Layr-Labs/eigenlayer-contracts/blob/dbeb85bcd0476e06b8feebf07e33f8a53d54c029/src/contracts/pods/EigenPodManager.sol#L84

4): https://github.com/Layr-Labs/eigenlayer-contracts/blob/dbeb85bcd0476e06b8feebf07e33f8a53d54c029/src/contracts/strategies/StrategyBase.sol#L78
there isn't any kind of `0` check in the `amount` as it can be zero which shouldn't be in this case

5): In the `withdraw` function as described here https://github.com/Layr-Labs/eigenlayer-contracts/blob/dbeb85bcd0476e06b8feebf07e33f8a53d54c029/src/contracts/strategies/StrategyBase.sol#L121
`amountShares` is not checked it could be 0, which shouldn't be.


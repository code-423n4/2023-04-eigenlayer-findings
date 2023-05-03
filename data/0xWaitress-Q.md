1. make input in StrategyManager::completeQueuedWithdrawals as a struct

right now the input is not guaranteed to be the same length, it's better to make them in a struct so length checks are not needed
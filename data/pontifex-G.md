### GAS-1 Declare `strategiesLength` caching variable in the top of the function body
Declaring `strategiesLength` caching variable in the top of the function body lets use the variable in other statements.
There are three instances:
```solidity
341:    {
342:        require(strategies.length == shares.length, "StrategyManager.queueWithdrawal: input length mismatch");
...
358:        for (uint256 i = 0; i < strategies.length;) {
...    
362:                require(strategies.length == 1,
```
https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/core/StrategyManager.sol#L342-L362
```solidity
494:    {
495:        require(tokens.length == strategies.length, "StrategyManager.slashShares: input length mismatch");
496:        uint256 strategyIndexIndex;
497:        uint256 strategiesLength = strategies.length;
498:        for (uint256 i = 0; i < strategiesLength;) {
```
https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/core/StrategyManager.sol#L494-L498
```solidity
541:    {
542:        require(tokens.length == queuedWithdrawal.strategies.length, "StrategyManager.slashQueuedWithdrawal: input length mismatch");
...
559:        uint256 strategiesLength = queuedWithdrawal.strategies.length;
560:        for (uint256 i = 0; i < strategiesLength;) {
```
https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/core/StrategyManager.sol#L541-L560

### GAS-2 Use `strategiesLength` caching variable instead `queuedWithdrawal.strategies.length`
Using `strategiesLength` caching variable instead `queuedWithdrawal.strategies.length` in the [line #778](https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/core/StrategyManager.sol#L778) will save some gas.
```solidity
775:        uint256 strategiesLength = queuedWithdrawal.strategies.length;
776:        // if the withdrawer has flagged to receive the funds as tokens, withdraw from strategies
777:        if (receiveAsTokens) {
778:            require(tokens.length == queuedWithdrawal.strategies.length, "StrategyManager.completeQueuedWithdrawal: input length mismatch");
```

### GAS-3 Use caching variable for `stakerStrategyShares[depositor][strategy]` in `_addShares` function
Using caching variable for `stakerStrategyShares[depositor][strategy]` in `_addShares` function in `if` in the line #63 and as an operand in the line #644 lets save about 100 gas. In the line #644 it should be look like `stakerStrategyShares[depositor][strategy] = cachedValue + shares;`
```solidity
629:    function _addShares(address depositor, IStrategy strategy, uint256 shares) internal {
...
635:        if (stakerStrategyShares[depositor][strategy] == 0) {
...
644:        stakerStrategyShares[depositor][strategy] += shares;
```
https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/core/StrategyManager.sol#L629-L644

### GAS-4 Use caching for `<array>.length` in loops
Caching `indicesToSkip.length` instead of looking it up in every loop can save some gas.
```solidity
560:        for (uint256 i = 0; i < strategiesLength;) {
561:            // check if the index i matches one of the indices specified in the `indicesToSkip` array
562:            if (indicesToSkipIndex < indicesToSkip.length && indicesToSkip[indicesToSkipIndex] == i) {
```
https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/core/StrategyManager.sol#L562

### GAS-5 Add unchecked {} for subtractions where the operands cannot underflow because of a previous require() or if-statement
require(a <= b); x = b - a => require(a <= b); unchecked { x = b - a }
```solidity
824:            if (amount > amountToDecrement) {
825:                beaconChainETHSharesToDecrementOnWithdrawal[staker] = 0;
826:                // decrease `amount` appropriately, so less is sent at the end
827:                amount -= amountToDecrement;
```
https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/core/StrategyManager.sol#L824-L827

### GAS-6 Use caching variable for `withdrawalDelayBlocks` state variable in the loop
Using the caching variable for `withdrawalDelayBlocks` state variable in the [loop]() lets save gas.
```solidity
142:        while (i < maxNumberOfDelayedWithdrawalsToClaim && (delayedWithdrawalsCompletedBefore + i) < _userWithdrawalsLength) {
...    
146:            if (block.number < delayedWithdrawal.blockCreated + withdrawalDelayBlocks) {
```
https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/core/StrategyManager.sol#L629-L644

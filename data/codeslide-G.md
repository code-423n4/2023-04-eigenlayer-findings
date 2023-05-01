### Gas Optimizations

| Number | Issue                | Instances |
| :----: | :------------------- | :-------: |
| [G-01] | Strict inequalities  |     5     |
| [G-02] | Use defined constant |     2     |

#### [G-01] Strict inequalities

Non-strict inequalities are cheaper than strict ones because, in the EVM, there is no opcode for non-strict inequalities (`>=`, `<=`). When non-strict inequalities are used in Solidity, two operations are performed (e.g., `>` and `=`.) Consider replacing the non-strict inequalities like `>=` with their strict counterpart `>` if doing so does not incur any extra gas usage (e.g., additional arithmetic).

For example:

```solidity
// Before
if (shortest - idx >= 32) {

// After
if (shortest - idx > 31) {
```

```solidity
File: src\contracts\pods\EigenPod.sol

195:    require(validatorCurrentBalanceGwei >= REQUIRED_BALANCE_GWEI,

373:    if (withdrawalAmountGwei >= REQUIRED_BALANCE_GWEI) {

387:    if (withdrawalAmountGwei >= REQUIRED_BALANCE_GWEI) {
```

```solidity
File: src\contracts\strategies\StrategyBase.sol

106:    require(updatedTotalShares >= MIN_NONZERO_TOTAL_SHARES,

139:    require(updatedTotalShares >= MIN_NONZERO_TOTAL_SHARES || updatedTotalShares == 0,
```

#### [G-02] Use defined constant

`type(uint256).max` (for any `uint` size) uses more gas in the distribution process and also for each transaction than usage of a constant.

In `src\contracts\permissions\Pausable.sol` there is a constant defined:

```solidity
23:    uint256 constant internal PAUSE_ALL = type(uint256).max;
```

In the same contract, in the `pauseAll()` function, the constant could be used to save gas (and make the code more readable):

```solidity
82:    _paused = type(uint256).max;
83:    emit Paused(msg.sender, type(uint256).max);
```

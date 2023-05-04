## Low Issues
### L-1 StrategyBase_`MIN_NONZERO_TOTAL_SHARES` constant doesn't support tokens with 1e6 decimals
The minimal value will be too high for tokens with 1e6 decimals (USDT, USDC).
```solidity
28:    uint96 internal constant MIN_NONZERO_TOTAL_SHARES = 1e9;
...
105:        uint256 updatedTotalShares = totalShares + newShares;
106:        require(updatedTotalShares >= MIN_NONZERO_TOTAL_SHARES,
107:            "StrategyBase.deposit: updated totalShares amount would be nonzero but below MIN_NONZERO_TOTAL_SHARES");
```
https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/strategies/StrategyBase.sol#L28
https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/strategies/StrategyBase.sol#L105-L107

## Non Critical

##№ N-1 Redundant Contract Inheritance
The `Initializable` contract is already inherited through the parent contracts `OwnableUpgradeable` and `ReentrancyGuardUpgradeable`:
There are four instances:
```solidity
26: contract StrategyManager is
27:     Initializable,
28:     OwnableUpgradeable,
29:     ReentrancyGuardUpgradeable,
```
https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/core/StrategyManager.sol#L26-L29
```solidity
31: contract EigenPodManager is Initializable, OwnableUpgradeable, Pausable, IEigenPodManager, EigenPodPausingConstants {
```
https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/pods/EigenPodManager.sol#L31
```solidity
34: contract EigenPod is IEigenPod, Initializable, ReentrancyGuardUpgradeable, EigenPodPausingConstants {
```
https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/pods/EigenPod.sol#L34
```solidity
11: contract DelayedWithdrawalRouter is Initializable, OwnableUpgradeable, ReentrancyGuardUpgradeable, Pausable, IDelayedWithdrawalRouter {
```
https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/pods/DelayedWithdrawalRouter.sol#L11
Remove the unnecessary extra inheritance of `Initializable`.

### N-2 Constants should be named with all capital letters with underscores separating words
Immutables also should be named with all capital letters but it is not conventional.
There are two instances of `constant`s:
```solidity
70:    IStrategy public constant beaconChainETHStrategy = IStrategy(0xbeaC0eeEeeeeEEeEeEEEEeeEEeEeeeEeeEEBEaC0);
```
https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/core/StrategyManagerStorage.sol#L70
```solidity
37:    bytes internal constant beaconProxyBytecode = hex"6080604 ... 96c6564"; 
```
https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/pods/EigenPodManager.sol#L37

### N-3 Use `PAUSE_ALL` constant instead `type(uint256).max`
Use readable constant instead of a magic value.
There are two instances:
```solidity
23:    uint256 constant internal PAUSE_ALL = type(uint256).max;
...
78:    /**
79:     * @notice Alias for `pause(type(uint256).max)`.
80:     */
81:    function pauseAll() external onlyPauser {
82:        _paused = type(uint256).max;
83:        emit Paused(msg.sender, type(uint256).max);
84:    }
```
https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/permissions/Pausable.sol#L78-L84

### N-4 StrategyManager.sol#L724_Variable need not be initialized to zero
The default value for variables is zero, so initializing them to zero is superfluous.
```solidity
724:            uint256 j = 0;
```
https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/core/StrategyManager.sol#L724

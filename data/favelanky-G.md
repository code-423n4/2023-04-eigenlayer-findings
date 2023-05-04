| Number | Issues Details                                 | Count |
| :----- | :--------------------------------------------- | :---- |
| [G-1]  | Use assembly to check for address(0)           | 3     |
| [G-2]  | `require` should be at the top of the function | 1     |
| [G-3]  | Useless calculations                           | 1     |

Total number of issues: 5

# Findings

## [G-1] Use assembly to check for address(0)

<details>
<summary><i>There are 3 instances of this issue:</i></summary>

```solidity
File: pods/EigenPodManager.sol

114:  if(address(pod) == address(0)) {

196:  if (address(pod) == address(0)) {

```

https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/pods/EigenPodManager.sol#L114

```solidity
File: permissions/Pausable.sol

57:  address(pauserRegistry) == address(0) && address(_pauserRegistry) != address(0),

```

https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/permissions/Pausable.sol#L57

</details>

## [G-2] `require` should be at the top of the function

Checks that involve constants should come before checks that involve state variables, function calls, and calculations. By doing these checks first, the function is able to revert before wasting a Gcoldsload (2100 gas) in a function that may ultimately revert in the unhappy case.

<details>
<summary><i>There is 1 instances of this issue:</i></summary>

```solidity
File: /pods/EigenPod.sol

	constructor(
		IETHPOSDeposit _ethPOS,
		IDelayedWithdrawalRouter _delayedWithdrawalRouter,
		IEigenPodManager _eigenPodManager,
		uint256 _REQUIRED_BALANCE_WEI
	) {
		ethPOS = _ethPOS;
		delayedWithdrawalRouter = _delayedWithdrawalRouter;
		eigenPodManager = _eigenPodManager;
		REQUIRED_BALANCE_WEI = _REQUIRED_BALANCE_WEI;
		REQUIRED_BALANCE_GWEI = uint64(_REQUIRED_BALANCE_WEI / GWEI_TO_WEI);
		// @gas [require order][4]
		require(
			_REQUIRED_BALANCE_WEI % GWEI_TO_WEI == 0,
			"EigenPod.contructor: _REQUIRED_BALANCE_WEI is not a whole number of gwei"
		);
		_disableInitializers();
	}

```

https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/pods/EigenPod.sol

</details>

## [G-3] Useless calculations

There are no need to subtract debt from `amount` because debt is `uint256 debt = amount - userShares` =>
`amount -= debt` is `amount -= (amount - userShares)` => `amount = userShares`.
This will make code simpler and saves some gas.

<details>
<summary><i>There is 1 instances of this issue:</i></summary>

```solidity
File: /core/StrategyManager.sol

193:             amount -= debt;

```

https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/core/StrategyManager.sol#L193

</details>

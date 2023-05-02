## [G-01] `StrategyBase.sol:withdraw` Cache `_tokenBalance` function

There is 1 instance of this issue:

```jsx
File: /src/contracts/strategies/StrategyBase.sol

	148: uint256 amountToSend;
	149:        if (priorTotalShares == amountShares) {
	150:            amountToSend = _tokenBalance();
	151:        } else {
	152:            amountToSend = (_tokenBalance() * amountShares) / priorTotalShares;
	153:        }
```

- [StrategyBase.sol#L149](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/strategies/StrategyBase.sol#L149)

**Mitigation:**

```diff
File: /src/contracts/strategies/StrategyBase.sol

	148: uint256 amountToSend;
+     uint256 tokenBalance = _tokenBalance();
	149:        if (priorTotalShares == amountShares) {
+	150:            amountToSend = tokenBalance;
	151:        } else {
+	152:            amountToSend = (tokenBalance * amountShares) / priorTotalShares;
	153:        }
```

## **[G‑02] Use assembly to check for `address(0)`**

Saves 6 gas per instance:

```solidity
File: /src/contracts/pods/EigenPodManager.sol

	114: if(address(pod) == address(0)) {
	196: if(address(pod) == address(0)) {
```

- [EigenPodManager.sol](https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/pods/EigenPodManager.sol)

## [G-03] For reset the storage slot `delete` should be used instead of setting the boolean to false.

*There is 2 instance of this issue:*

```solidity
File: /src/contracts/core/StrategyManager.sol

	554: withdrawalRootPending[withdrawalRoot] = false;
	772: withdrawalRootPending[withdrawalRoot] = false;
```

- [StrategyManager.sol#L772](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L772)
- [StrategyManager.sol#L554](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L554)

## [G-04] **Add `unchecked {}` for subtractions where the operands cannot underflow because of a previous `require()` or `if`-statement**

*There is 1 instance of this issue:*

```solidity
File: /src/contracts/core/StrategyManager.sol

/// @audit if-condition on line 824
	827: amount -= amountToDecrement;
```

- [StrategyManager.sol#L827](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L827)

## [G-05] **Save some gas on the nonce increment**

The increment of the nonce in function `depositIntoStrategyWithSignature()` of `StrategyManager.sol` uses a temporary variable.

This is not necessary because the temporary variable isn't used elsewhere.Thus a bit of gas could be saved by using nonce++ 

*There is 1 instance of this issue:*

```solidity
File: /src/contracts/core/StrategyManager.sol

	267: uint256 nonce = nonces[staker];
	268:        bytes32 structHash = keccak256(abi.encode(DEPOSIT_TYPEHASH, strategy, token, amount, nonce, expiry));
	269:        unchecked {
	270:            nonces[staker] = nonce + 1;
	271:        }
```

- [StrategyManager.sol#L267](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L267)

## [G-06] Saving gas by using `calldata` instead of `memory` for read-only arguments in external functions.

When a function with a memory array is called externally, the abi.decode() step has to use a for-loop to copy each index of the calldata to the memory index. Each iteration of this for-loop costs at least 60 gas (i.e. 60 * <mem_array>.length). Using calldata directly, obliviates the need for such a loop in the contract code and runtime execution.

*There is 3 instance of this issue*

```solidity
File: /src/contracts/interfaces/IBeaconChainOracle.sol
	46: function addOracleSigners(address[] memory _oracleSigners) external;
	53: function removeOracleSigners(address[] memory _oracleSigners) external;

File: /src/contracts/interfaceshttps://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/interfaces/IDelayedWithdrawalRouter.sol
	43:  function claimableUserDelayedWithdrawals(address user) external view returns (DelayedWithdrawal[] memory);
```

- [IBeaconChainOracle.sol](https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/interfaces/IBeaconChainOracle.sol)
- [IDelayedWithdrawalRouter.sol](https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/interfaces/IDelayedWithdrawalRouter.sol)
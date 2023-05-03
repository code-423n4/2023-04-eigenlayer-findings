## [G-01] **Declare a constant for the type max rather than evaluate it everytime**

There is 1 instance of this issue:

```solidity
File: /src/contracts/permissions/Pausable.sol
	81: function pauseAll() external onlyPauser {
	82:        _paused = type(uint256).max; //@audit use constant see Line 23
	83:        emit Paused(msg.sender, type(uint256).max); //@audit use constant see Line 23
	84:    }
```

- [Pausable.sol#L81](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/permissions/Pausable.sol#L81)

### **Mitigation**

```diff
File: /src/contracts/permissions/Pausable.sol
	81: function pauseAll() external onlyPauser {
-	82:        _paused = type(uint256).max;
-	83:        emit Paused(msg.sender, type(uint256).max);
+ 82:        _paused = PAUSE_ALL;
+ 83:        emit Paused(msg.sender, PAUSE_ALL);
	84:    }
```

## [G-02] `StrategyBase.sol:withdraw` Cache `_tokenBalance` function

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

## **[G‑03] Use assembly to check for `address(0)`**

`NOTE`: None of these findings where found by [bot findings - Gas](https://gist.github.com/CloudEllie/213965a3448230f5b615e7046f9dd26d)

Saves 6 gas per instance

There is 13 instance of this issues:

```solidity
File: /src/contracts/pods/EigenPodManager.sol

	114: if(address(pod) == address(0)) {
	196: if(address(pod) == address(0)) {

File: src/contracts/permissions/Pausable.sol
	56:   require(
	57:              address(pauserRegistry) == address(0) && address(_pauserRegistry) != address(0),
	58:               "Pausable._initializePauser: _initializePauser() can only be called once"
	59:           );

File: src/contracts/permissions/PauserRegistry.sol
	42:  require(newPauser != address(0), "PauserRegistry._setPauser: zero address input");
	48:  require(newUnpauser != address(0), "PauserRegistry._setUnpauser: zero address input");

File: src/contracts/pods/DelayedWithdrawalRouter.sol
	45:  require(address(_eigenPodManager) != address(0), "DelayedWithdrawalRouter.constructor: _eigenPodManager cannot be zero address");

File: src/contracts/pods/EigenPod.sol
	153: require(_podOwner != address(0), "EigenPod.initialize: podOwner cannot be zero address");

File: src/contracts/core/StrategyManager.sol
	343:  require(withdrawer != address(0), "StrategyManager.queueWithdrawal: cannot withdraw to zero address");
	631:  require(depositor != address(0), "StrategyManager._addShares: depositor cannot be zero address");
	684:  require(depositor != address(0), "StrategyManager._removeShares: depositor cannot be zero address");
```

- [EigenPodManager.sol](https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/pods/EigenPodManager.sol)

## [G-04] For reset the storage slot `delete` should be used instead of setting the boolean to false.

*There is 2 instance of this issues:*

```solidity
File: /src/contracts/core/StrategyManager.sol

	554: withdrawalRootPending[withdrawalRoot] = false;
	772: withdrawalRootPending[withdrawalRoot] = false;
```

- [StrategyManager.sol#L772](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L772)
- [StrategyManager.sol#L554](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L554)

## [G-05] **Add `unchecked {}` for subtractions where the operands cannot underflow because of a previous `require()` or `if`-statement**

`NOTE`: None of these findings where found by [bot findings - Gas](https://gist.github.com/CloudEllie/213965a3448230f5b615e7046f9dd26d)

*There is 1 instance of this issue:*

```solidity
File: /src/contracts/core/StrategyManager.sol

/// @audit if-condition on line 824
	827: amount -= amountToDecrement;
```

- [StrategyManager.sol#L827](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L827)

## [G-06] **Save some gas on the nonce increment**

The increment of the nonce in function `depositIntoStrategyWithSignature()` of `StrategyManager.sol` uses a temporary variable.

This is not necessary because the temporary variable isn't used elsewhere.Thus a bit of gas could be saved by using nonce++ 

`NOTE`: None of these findings where found by [bot findings - Gas](https://gist.github.com/CloudEllie/213965a3448230f5b615e7046f9dd26d)

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

## [G-07] Saving gas by using `calldata` instead of `memory` for read-only arguments in external functions.

When a function with a memory array is called externally, the abi.decode() step has to use a for-loop to copy each index of the calldata to the memory index. Each iteration of this for-loop costs at least 60 gas (i.e. 60 * <mem_array>.length). Using calldata directly, obliviates the need for such a loop in the contract code and runtime execution.

*There is 3 instance of this issues*

```solidity
File: /src/contracts/interfaces/IBeaconChainOracle.sol
	46: function addOracleSigners(address[] memory _oracleSigners) external;
	53: function removeOracleSigners(address[] memory _oracleSigners) external;

File: /src/contracts/interfaceshttps://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/interfaces/IDelayedWithdrawalRouter.sol
	43:  function claimableUserDelayedWithdrawals(address user) external view returns (DelayedWithdrawal[] memory);
```

- [IBeaconChainOracle.sol](https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/interfaces/IBeaconChainOracle.sol)
- [IDelayedWithdrawalRouter.sol](https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/interfaces/IDelayedWithdrawalRouter.sol)

## [G‑08] Multiple `address`/ID mappings can be combined into a single `mapping` of an `address`/ID to a `struct`, where appropriate

Saves a storage slot for the mapping. Depending on the circumstances and sizes of types, can avoid a Gsset (**20000 gas**) per mapping combined. Reads and subsequent writes can also be cheaper when a function requires both values and they both fit in the same storage slot. Finally, if both fields are accessed in the same function, can save **~42 gas per access** due to [not having to recalculate the key's keccak256 hash](https://gist.github.com/IllIllI000/ec23a57daa30a8f8ca8b9681c8ccefb0) (Gkeccak256 - 30 gas) and that calculation's associated stack operations.

*There is 1 instance of this issue:*

```solidity
File: src/contracts/core/StrategyManagerStorage.sol
	49: mapping(address => mapping(IStrategy => uint256)) public stakerStrategyShares;
	50: /// @notice Mapping: staker => array of strategies in which they have nonzero shares
	51: mapping(address => IStrategy[]) public stakerStrategyList;
```

- [StrategyManagerStorage.sol#L49-L51](https://github.com/code-423n4/2023-04-eigenlayer/blob/398cc428541b91948f717482ec973583c9e76232/src/contracts/core/StrategyManagerStorage.sol#L49-L51)

## [G-09] `abi.encode()` is less gas efficient than `abi.encodePacked()`

Changing the `abi.encode` function to `abi.encodePacked` can save gas since the `abi.encode` function pads extra null `bytes` at the end of the call data, which is unnecessary. Also, in general, `abi.encodePacked` is more gas-efficient.

`NOTE`: None of these findings where found by [bot findings - Gas](https://gist.github.com/CloudEllie/213965a3448230f5b615e7046f9dd26d)

There is 1 instance of this issue:

```solidity
File: /src/contracts/core/StrategyManager.sol
	268: bytes32 structHash = keccak256(abi.encode(DEPOSIT_TYPEHASH, strategy, token, amount, nonce, expiry));
```

- [StrategyManager.sol#L268](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L268)
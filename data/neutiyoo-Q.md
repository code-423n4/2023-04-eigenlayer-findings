## [L-01] Missing out-of-bounds access check in `_removeStrategyFromStakerStrategyList` function

**Type**: Data Validation
**Target**: [StrategyManager.sol](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol)

### Description

If the [`_removeStrategyFromStakerStrategyList` function](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L715) is called by the [`recordOvercommittedBeaconChainETH` function](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L182) with invalid `uint256 beaconChainETHStrategyIndex` parameter, then it can cause out-of-bounds access error.

### Proof of Concept

Change the value of `uint256 beaconChainETHStrategyIndex` from `0` to `42` in `StrategyManagerUnit.t.sol` as shown below:

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/test/unit/StrategyManagerUnit.t.sol#L185

```diff
-uint256 beaconChainETHStrategyIndex = 0;
+uint256 beaconChainETHStrategyIndex = 42; // an arbitrary number
```

This modification will trigger the following error:

```text
[FAIL. Reason: Index out of bounds Counterexample: calldata=0x28bdd3e000000000000000000000000000000000000000000000000000000000000000030000000000000000000000000000000000000000000000000000000000000003, args=[3, 3]] testRecordOvercommittedBeaconChainETHSuccessfully(uint256,uint256) (runs: 116, μ: 144482, ~: 144482)
```

### Recommendation

Consider checking out-of-bounds access.

## [L-02] Missing zero address check for `address initOwner` in `initialize` function of `DelayedWithdrawalRouter` contract

**Type**: Data Validation
**Target**: [DelayedWithdrawalRouter.sol](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/DelayedWithdrawalRouter.sol)

### Description

The [`initialize` function](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/DelayedWithdrawalRouter.sol#L50) of the `DelayedWithdrawalRouter` contract does not have a zero address check for the `address initOwner`.

```solidity
    function initialize(address initOwner, IPauserRegistry _pauserRegistry, uint256 initPausedStatus, uint256 _withdrawalDelayBlocks) external initializer {
        _transferOwnership(initOwner);
        _initializePauser(_pauserRegistry, initPausedStatus);
        _setWithdrawalDelayBlocks(_withdrawalDelayBlocks);
    }
```

The code calls the [`_transferOwnership` function](https://github.com/OpenZeppelin/openzeppelin-contracts-upgradeable/blob/6b9807b0639e1dd75e07fa062e9432eb3f35dd8c/contracts/access/OwnableUpgradeable.sol#L79-L87) from the `OwnableUpgradeable.sol` file in the OpenZeppelin library.

```solidity
    /**
     * @dev Transfers ownership of the contract to a new account (`newOwner`).
     * Internal function without access restriction.
     */
    function _transferOwnership(address newOwner) internal virtual {
        address oldOwner = _owner;
        _owner = newOwner;
        emit OwnershipTransferred(oldOwner, newOwner);
    }
```

Note that this function doesn't have a zero address check for the `address newOwner`.

### Recommendation

Consider checking the zero value for the `address initOwner` argument.

## [L-03] Missing zero address check in the constructor of `EigenPodManager` contract

**Type**: Data Validation
**Target**: [EigenPodManager.sol](httpshttps://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPodManager.sol)

### Description

The [constructor](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPodManager.sol#L76) of `EigenPodManager` has no zero value check for the following arguments:

- `IETHPOSDeposit _ethPOS`
- `IBeacon _eigenPodBeacon`
- `IStrategyManager _strategyManager`
- `ISlasher _slasher`

### Recommendation

Consider checking the zero address for the four constructor arguments.

## [L-04] Missing zero address check in the constructor of `EigenPod` contract

**Type**: Data Validation
**Target**: [EigenPod.sol](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPod.sol)

### Description

The [constructor](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPod.sol#L136) of `EigenPod` has no zero address check for the following arguments:

- `IETHPOSDeposit _ethPOS`
- `IDelayedWithdrawalRouter _delayedWithdrawalRouter`
- `IEigenPodManager _eigenPodManager`

### Recommendation

Consider checking the zero address for the three constructor arguments.

## [L-05] Missing zero value check in `deposit` function

**Type**: Data Validation
**Target**: [StrategyBase.sol](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/strategies/StrategyBase.sol)

### Description

The [`deposit` function](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/strategies/StrategyBase.sol#L78) in `StrategyBase.sol` has no zero value check for the `uint256 amount` argument.

### Recommendation

Consider checking the zero value for the `uint256 amount` argument.

## [L-06] Missing array length check in `queueWithdrawal` function

**Type**: Data Validation
**Target**: [StrategyManager.sol](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol)

### Description

The [`queueWithdrawal` function](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L329) in StrategyManager.sol does not validate the length of the `uint256[] strategyIndexes` input argument.

### Recommendation

Consider checking the length of the `uint256[] strategyIndexes` argument.

## [N-01] Misleading comment for `sharesToUnderlying` function

**Type**: Code Quality
**Target**: [StrategyBase.sol](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/strategies/StrategyBase.sol)

### Description

The following comment for the `sharesToUnderlying` function is misleading:

```
* @notice In contrast to `sharesToUnderlyingView`, this function **may** make state modifications
```

The `sharesToUnderlying` function has a `view` modifier. Therefore, it does not modify the state.

### Recommendation

Consider updating the comment for `sharesToUnderlying` function to accurately reflect that it does not modify the state.

## [N-02] Misleading comments for `Merkle` contract

**Type**: Code Quality
**Target**: [Merkle.sol](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/libraries/Merkle.sol)

## Description

There are [misleading comments](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/libraries/Merkle.sol#L6-L19) in the `Merkle.sol`, which is adapted from OpenZeppelin Contracts. The modified implementation uses `sha256` hash function but the comments do not reflect the changes.

```diff
/**
 * @dev These functions deal with verification of Merkle Tree proofs.
 *
 * The tree and the proofs can be generated using our
 * https://github.com/OpenZeppelin/merkle-tree[JavaScript library].
 * You will find a quickstart guide in the readme.
 *
 * WARNING: You should avoid using leaf values that are 64 bytes long prior to
 * hashing, or use a hash function other than keccak256 for hashing leaves.
 * This is because the concatenation of a sorted pair of internal nodes in
 * the merkle tree could be reinterpreted as a leaf value.
 * OpenZeppelin's JavaScript library generates merkle trees that are safe
 * against this attack out of the box.
 */
```

The above comments are incorrect as OpenZeppelin has defined [standard Merkle trees](https://github.com/OpenZeppelin/merkle-tree/blob/7dbf9a11cd69a0cfabf9cca4dbae37d14d30e1a6/README.md#standard-merkle-trees) with the following characteristics:

- The tree is shaped as a [complete binary tree](https://xlinux.nist.gov/dads/HTML/completeBinaryTree.html).
- The leaves are sorted.
- The leaves are the result of ABI encoding a series of values.
- The hash used is Keccak256.
- The leaves are double-hashed to prevent second preimage attacks.

### Recommendation

Consider updating the comments in `Merkle.sol` to accurately reflect the current implementation.

## [L-01] Missing out-of-bounds access check in `_removeStrategyFromStakerStrategyList` function

### Description

**Target**: [StrategyManager.sol](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol)

If the [`_removeStrategyFromStakerStrategyList`](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L715) function is called by the [`recordOvercommittedBeaconChainETH`](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L182) function with invalid `uint256 beaconChainETHStrategyIndex` parameter, then it can cause out-of-bounds access error.

### Proof of Concept

Change the value of `uint256 beaconChainETHStrategyIndex` from `0` to `42` in `StrategyManagerUnit.t.sol` as shown below:

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/test/unit/StrategyManagerUnit.t.sol#L185

```diff
-uint256 beaconChainETHStrategyIndex = 0;
+uint256 beaconChainETHStrategyIndex = 42;
```

This modification will trigger the error as the following:

```text
[FAIL. Reason: Index out of bounds Counterexample: calldata=0x28bdd3e000000000000000000000000000000000000000000000000000000000000000030000000000000000000000000000000000000000000000000000000000000003, args=[3, 3]] testRecordOvercommittedBeaconChainETHSuccessfully(uint256,uint256) (runs: 116, μ: 144482, ~: 144482)
```

### Recommendation

Validate `uint256 strategyIndex` to avoid out-of-bounds access.

## [L-02] Missing zero address check for `address initOwner` in `initialize` function of `DelayedWithdrawalRouter` contract

### Description

**Target**: [DelayedWithdrawalRouter.sol](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/DelayedWithdrawalRouter.sol)

The [`initialize` function](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/DelayedWithdrawalRouter.sol#L50) of `DelayedWithdrawalRouter` has no zero value check for the `address initOwner`.

```solidity
    function initialize(address initOwner, IPauserRegistry _pauserRegistry, uint256 initPausedStatus, uint256 _withdrawalDelayBlocks) external initializer {
        _transferOwnership(initOwner);
        _initializePauser(_pauserRegistry, initPausedStatus);
        _setWithdrawalDelayBlocks(_withdrawalDelayBlocks);
    }
```

It calls the following [`_transferOwnership`](https://github.com/OpenZeppelin/openzeppelin-contracts-upgradeable/blob/6b9807b0639e1dd75e07fa062e9432eb3f35dd8c/contracts/access/OwnableUpgradeable.sol#L79-L87) function from OpenZeppelin `OwnableUpgradeable.sol`.

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

### Recommendation

Consider checking zero value for the `address initOwner` argument.

## [L-03] Missing zero address check in the constructor of `EigenPodManager` contract

### Description

**Target**: [EigenPodManager.sol](httpshttps://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPodManager.sol)

The [constructor](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPodManager.sol#L76) of `EigenPodManager` has no zero value check for the following arguments:

- `IETHPOSDeposit _ethPOS`
- `IBeacon _eigenPodBeacon`
- `IStrategyManager _strategyManager`
- `ISlasher _slasher`

### Recommendation

Consider checking zero address for the 4 constructor arguments.

## [L-04] Missing zero address check in the constructor of `EigenPod` contract

### Description

**Target**: [EigenPod.sol](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPod.sol)

The [constructor](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPod.sol#L136) of `EigenPod` has no zero value check for the following arguments:

- `IETHPOSDeposit _ethPOS`
- `IDelayedWithdrawalRouter _delayedWithdrawalRouter`
- `IEigenPodManager _eigenPodManager`

### Recommendation

Consider checking zero address for the 3 constructor arguments.

## [L-05] Missing zero value check in `deposit` function

### Description

**Target**: [StrategyBase.sol](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/strategies/StrategyBase.sol)

The [`deposit` function](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/strategies/StrategyBase.sol#L78) in `StrategyBase.sol` has no zero value check for the `uint256 amount` argument.

### Recommendation

Consider checking zero value for the `uint256 amount` argument.

## [L-06] Missing array length check in `queueWithdrawal` function

### Description

**Target**: [StrategyManager.sol](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol)

The [`queueWithdrawal` function](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L329) in StrategyManager.sol does not validate the length of the `uint256[] strategyIndexes` input argument.

### Recommendation

Consider checking the length of the `uint256[] strategyIndexes` argument.

## [N-01] Misleading comment for `sharesToUnderlying` function

### Description

**Target**: [StrategyBase.sol](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/strategies/StrategyBase.sol)

Currently, the following comment for the `sharesToUnderlying` function is Misleading.

```
* @notice In contrast to `sharesToUnderlyingView`, this function **may** make state modifications
```

It's because the `sharesToUnderlying` function has `view` modifier, indicating that it does not modify the state.

### Recommendation

Consider fixing the comment for `sharesToUnderlying` function to accurately reflect that it does not modify the state.

## [N-02] Misleading comments for `Merkle` contract

## Description

**Target**: [Merkle.sol](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/libraries/Merkle.sol)

There are [misleading comments](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/libraries/Merkle.sol#L6-L19) in the `Merkle.sol`, which is adapted from OpenZeppelin Contracts. The modified implementation uses `sha256` hash function but the comments doesn't reflect the changes.

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

The above comments are incorrect as OpenZeppelin have defined [standard merkle trees](https://github.com/OpenZeppelin/merkle-tree/blob/7dbf9a11cd69a0cfabf9cca4dbae37d14d30e1a6/README.md#standard-merkle-trees) with the following characteristics:

- The tree is shaped as a [complete binary tree](https://xlinux.nist.gov/dads/HTML/completeBinaryTree.html).
- The leaves are sorted.
- The leaves are the result of ABI encoding a series of values.
- The hash used is Keccak256.
- The leaves are double-hashed to prevent second preimage attacks.

### Recommendation

Consider updating the comments in `Merkle.sol` to accurately reflect the current implementation and removing any misleading information.

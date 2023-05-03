## [L-01] Missing check for out-of-bounds access in `_removeStrategyFromStakerStrategyList` function
### Description

**Target**: [StrategyManager.sol](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol)

If the [`_removeStrategyFromStakerStrategyList`](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L715
) function is called by the [`recordOvercommittedBeaconChainETH`](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L182) function with invalid `uint256 beaconChainETHStrategyIndex` parameter, then it can cause out-of-bounds access error.

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



## [L-02] Missing check for array length in `queueWithdrawal` function

### Description

**Target**: [StrategyManager.sol](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol)

The [`queueWithdrawal`](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L329
) function in StrategyManager.sol does not validate the length of the `uint256[] strategyIndexes` input argument.

### Recommendation
Consider checking the length of the `uint256[] strategyIndexes` argument.



## [L-03] Missing check for zero value in `deposit` function

### Description

**Target**: [StrategyBase.sol](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/strategies/StrategyBase.sol)

The [`deposit`](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/strategies/StrategyBase.sol#L78
) function in `StrategyBase.sol` has no zero value check for the `uint256 amount` argument.

### Recommendation

Consider checking zero value for the `uint256 amount` argument.



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




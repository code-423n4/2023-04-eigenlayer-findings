## [G-01] Optimize `merkleizeSha256` function for gas-efficiency

### Description

Although the current implementation of the [`merkleizeSha256`](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/libraries/Merkle.sol#L129-L153) function in the [`Merkle`](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/libraries/Merkle.sol) contract is correct, it can be more gas-efficient by making use of the following optimizations:

**1. In-place Computation**

The `merkleizeSha256` function can be optimized by using in-place computation to store intermediate hashes at each level of the Merkle tree. This approach eliminates the need to create new arrays, reducing memory usage and gas costs. Note that this optimization requires that the `leaves` array not be used again after it is modified.

This optimization is valid based on the current implementation because the `leaves` array is not used again after it is modified.

**2. Assembly**
The use of assembly code to load the left and right siblings into memory is more gas-efficient than using the `abi.encodePacked` function.

**3. Unchecked Arithmetic**

The use of unchecked arithmetic for `uint i` is more gas-efficient as it skips checks for overflow or underflow. This optimization is safe because `i` is always less than `numNodesInLayer`, meaning that overflow is not possible.

### Proof of Concept

The function `merkleizeSha256Optimized` provided below is an optimized version of the `merkleizeSha256` function.

src/contracts/libraries/MerkleOptimized.sol

```solidity
// SPDX-License-Identifier: MIT
pragma solidity =0.8.12;

library MerkleOptimized {
    /**
     * @notice Returns the Merkle root of a tree created from a set of leaves using SHA256 as its hash function.
     * @param leaves The leaves of the Merkle tree. This parameter is modified during the execution of the function and must not be used again afterwards.
     * @return The Merkle root of the tree.
     *
     * @notice Requires the leaves.length is a power of 2.
     * @dev This is adapted from https://github.com/ethereum-optimism/optimism/blob/e6f1f61c569dbabffa2cfe6129e8e23a8646ffca/packages/contracts/contracts/libraries/utils/Lib_MerkleTree.sol#L13C1-L96
     */
    function merkleizeSha256Optimized(
        bytes32[] memory leaves
    ) internal pure returns (bytes32) {
        // Reserve memory space for our hashes.
        bytes memory buf = new bytes(64);

        // We'll need to keep track of left and right siblings.
        bytes32 leftSibling;
        bytes32 rightSibling;

        // Number of non-empty nodes at the current depth.
        uint256 rowSize = leaves.length;

        // Common sub-expressions
        uint256 halfRowSize; // rowSize / 2

        while (rowSize > 1) {
            halfRowSize = rowSize / 2;

            for (uint256 i = 0; i < halfRowSize; ) {
                leftSibling = leaves[(2 * i)];
                rightSibling = leaves[(2 * i) + 1];
                assembly {
                    mstore(add(buf, 32), leftSibling)
                    mstore(add(buf, 64), rightSibling)
                }

                leaves[i] = sha256(buf);

                unchecked {
                    ++i;
                }
            }

            rowSize = halfRowSize;
        }

        return leaves[0];
    }
}
```

**Impact**

| Function Name            | min  | avg    | median | max     | # calls |
| ------------------------ | ---- | ------ | ------ | ------- | ------- |
| merkleizeSha256          | 2353 | 274987 | 62975  | 1396167 | 10      |
| merkleizeSha256Optimized | 2136 | 238896 | 56158  | 1197190 | 10      |

| Improvement |        |
| ----------- | ------ |
| Minimum     | 9.22%  |
| Average     | 13.12% |
| Median      | 10.82% |
| Maximum     | 14.25% |

The data shows a significant increase in gas efficiency with the use of `merkleizeSha256Optimized` compared to `merkleizeSha256`. It's worth emphasizing that these results are influenced by the input data and execution environment, so the actual improvement may differ in other contexts. Nonetheless, the results provide valuable insight into the potential gas cost savings that can be achieved by leveraging the optimized version of the function.

The test codes are the following:

src/test/unit/Merkle.t.sol

```solidity
// SPDX-License-Identifier: BUSL-1.1
pragma solidity =0.8.12;

import {Test} from "forge-std/Test.sol";
import {Merkle} from "../../contracts/libraries/Merkle.sol";
import {MerkleOptimized} from "../../contracts/libraries/MerkleOptimized.sol";

contract MerkleMock {
    function merkleizeSha256(
        bytes32[] calldata leaves
    ) external pure returns (bytes32) {
        return Merkle.merkleizeSha256(leaves);
    }

    function merkleizeSha256Optimized(
        bytes32[] calldata leaves
    ) external pure returns (bytes32) {
        return MerkleOptimized.merkleizeSha256Optimized(leaves);
    }
}

contract MerkleTest is Test {
    MerkleMock public c;

    function setUp() external {
        c = new MerkleMock();
    }

    function gen(uint256 length) internal pure returns (bytes32[] memory) {
        bytes32[] memory leaves = new bytes32[](length);
        for (uint i = 0; i < length; i++) {
            leaves[i] = bytes32(i);
        }
        return leaves;
    }

    function testMerkleizeSha256Equivalence() external {
        for (uint i = 2; i <= 1024; i *= 2) {
            assertEq(
                c.merkleizeSha256(gen(i)),
                c.merkleizeSha256Optimized(gen(i)),
                "ok"
            );
        }
    }
}
```

### Recommendation

Consider optimizing `merkleizeSha256` by using in-place computation, assembly, and unchecked arithmetic.

## [G-02] Use unchecked arithmetic in `processInclusionProofSha256` and `processInclusionProofKeccak` functions

### Description

The [`processInclusionProofSha256`](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/libraries/Merkle.sol#L99-L121) and [`processInclusionProofKeccak`](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/libraries/Merkle.sol#L48-L70) functions in [`Merkle`](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/libraries/Merkle.sol) contract include unnecessary arithmetic checks for incrementing `uint256 i` in a for-loop.

[src/contracts/libraries/Merkle.sol#L50](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/libraries/Merkle.sol#L50)
[src/contracts/libraries/Merkle.sol#L101](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/libraries/Merkle.sol#L101)

```solidity
for (uint256 i = 32; i <= proof.length; i+=32) {
```

By using unchecked arithmetic, the gas cost of executing these functions can be reduced.

```solidity
unchecked {
    i += 32;
}
```

This optimization is safe because based on the current implementation, overflow is not possible as the length of proof is validated before the function call.

### Recommendation

Consider using unchecked arithmetic for `uint256 i`.

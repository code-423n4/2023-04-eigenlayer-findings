## [G-01] Optimize `merkleizeSha256` function for gas-efficiency

### Description
**Target**: [Merkle.sol](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/libraries/Merkle.sol)

Although the current implementation of the [`merkleizeSha256` function](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/libraries/Merkle.sol#L129-L153
) function is correct, it can be more gas-efficient by making use of the following optimizations:

**1. In-place Computation**

The `merkleizeSha256` function can be optimized by using in-place computation to store intermediate hashes at each level of the Merkle tree. This approach eliminates the need to create new arrays, reducing memory usage and gas costs. 

Note that this optimization requires that the `leaves` array not be used again after it is modified. Based on the current implementation, this optimization is valid, as there are no further usages of the `leaves` array.

**2. Assembly**
The use of assembly code to load the left and right siblings into memory is more gas-efficient than using the `abi.encodePacked` function.

**3. Unchecked Arithmetic**
The use of unchecked arithmetic is more gas-efficient because it skips checks for overflow or underflow.

This is a valid optimization, as it's highly unlikely that `numNodesInLayer` would overflow.

### Proof of Concept

The function `merkleizeSha256Optimized` provided below is an optimized version of the `merkleizeSha256` function.
```solidity
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
```

### Impact

| test/Merkle.t.sol:MerkleMock contract |                 |        |        |         |         |
|---------------------------------------|-----------------|--------|--------|---------|---------|
| Deployment Cost                       | Deployment Size |        |        |         |         |
| 342580                                | 1743            |        |        |         |         |
| Function Name                         | min             | avg    | median | max     | # calls |
| merkleizeSha256                       | 2353            | 274987 | 62975  | 1396167 | 10      |
| merkleizeSha256Optimized              | 2136            | 238896 | 56158  | 1197190 | 10      |

| Improvement |          |
| ----------- | -------- |
| Minimum     | 9.22%    |
| Average     | 13.12%   |
| Median      | 10.82%   |
| Maximum     | 14.25%   |

These percentages show that `merkleizeSha256Optimized` provides a noticeable improvement over `merkleizeSha256`, with the largest improvement being in the maximum execution time. It's worth noting that these percentages are specific to the input data and the environment in which the functions were run, so they may vary in other contexts. Nonetheless, they give a rough idea of the performance improvement that can be expected from using the optimized version of the function.


It should be noted that these percentages are specific to the input data and the environment in which the functions were run, so they may vary in other contexts. Nonetheless, these results give a rough idea of the performance improvement that can be expected from using the optimized version of the function. 

The test codes are the following:
```solidity
// SPDX-License-Identifier: UNLICENSED
pragma solidity =0.8.12;

import "forge-std/Test.sol";
import "../src/Merkle.sol";

contract MerkleMock {
    function merkleizeSha256(
        bytes32[] calldata gen
    ) external pure returns (bytes32) {
        return Merkle.merkleizeSha256(gen);
    }

    function merkleizeSha256Optimized(
        bytes32[] calldata gen
    ) external pure returns (bytes32) {
        return Merkle.merkleizeSha256Optimized(gen);
    }
}

contract MerkleTest is Test {
    MerkleMock public c;

    function setUp() public {
        c = new MerkleMock();
    }

    function gen(uint256 length) internal pure returns (bytes32[] memory) {
        bytes32[] memory leaves = new bytes32[](length);
        for (uint i = 0; i < length; i++) {
            leaves[i] = bytes32(i);
        }
        return leaves;
    }

    function testMerkleizeSha256Equivalence() public {
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

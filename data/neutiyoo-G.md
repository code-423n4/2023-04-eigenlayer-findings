## [G-01] Optimize `merkleizeSha256` function for gas-efficiency

### Description

**Target**: [Merkle.sol](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/libraries/Merkle.sol)

Currently, the [`merkleizeSha256` function](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/libraries/Merkle.sol#L129-L153
) is implemented correctly, but the following optimizations for can be made:

**1. In-place Computation**
The pairwise hashes are computed in-place using a buffer, eliminating the need for creating new arrays to store intermediate hashes at each level of the Merkle tree. This reduces memory usage and gas costs.

This optimization requires that the `leaves` array is not used again after it is modified. Based on the current implementation, this assumption has proven to be valid, as there are no further usages of the `leaves` array.

**2. Fixed-size buffer and Assembly**
Using a fixed-size buffer instead of dynamically allocating memory can be more efficient in terms of gas usage. Also, the use of assembly code to load the left and right siblings into memory can be more efficient than using the `abi.encodePacked` function.

### Recommendation
Consider using a fixed-size buffer to compute the pairwise hashes in-place and updates the input array with the result. This eliminates the need for creating new arrays and reduces the number of iterations needed to compute the final Merkle root. As a result, it is more gas-efficient and performs better for large inputs.


Here is a sample optimized implementation which is adapted from the [Optimism's Lib_MerkleTree.sol](https://github.com/ethereum-optimism/optimism/blob/e6f1f61c569dbabffa2cfe6129e8e23a8646ffca/packages/contracts/contracts/libraries/utils/Lib_MerkleTree.sol#L13-L22).
```solidity
    function merkleizeSha256(
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

            for (uint256 i = 0; i < halfRowSize; i++) {
                leftSibling = leaves[(2 * i)];
                rightSibling = leaves[(2 * i) + 1];
                assembly {
                    mstore(add(buf, 32), leftSibling)
                    mstore(add(buf, 64), rightSibling)
                }

                leaves[i] = sha256(buf);
            }

            rowSize = halfRowSize;
        }

        return leaves[0];
    }
```
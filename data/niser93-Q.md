## N-01 Adding check of function requirements

### Founded in [Merkle.sol](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/libraries/Merkle.sol)

[Merkle.sol#L123-L153](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/libraries/Merkle.sol#L123-L153)

```
/**
@notice this function returns the merkle root of a tree created from a set of leaves using sha256 as its hash function
@param leaves the leaves of the merkle tree
@notice requires the leaves.length is a power of 2
*/ 
function merkleizeSha256(
    bytes32[] memory leaves
) internal pure returns (bytes32) {
    //there are half as many nodes in the layer above the leaves
    uint256 numNodesInLayer = leaves.length / 2;
    //create a layer to store the internal nodes
    bytes32[] memory layer = new bytes32[](numNodesInLayer);
    //fill the layer with the pairwise hashes of the leaves
    for (uint i = 0; i < numNodesInLayer; i++) {
        layer[i] = sha256(abi.encodePacked(leaves[2*i], leaves[2*i+1]));
    }
    //the next layer above has half as many nodes
    numNodesInLayer /= 2;
    //while we haven't computed the root
    while (numNodesInLayer != 0) {
        //overwrite the first numNodesInLayer nodes in layer with the pairwise hashes of their children
        for (uint i = 0; i < numNodesInLayer; i++) {
            layer[i] = sha256(abi.encodePacked(layer[2*i], layer[2*i+1]));
        }
        //the next layer above has half as many nodes
        numNodesInLayer /= 2;
    }
    //the first node in the layer is the root
    return layer[0];
}
```

### Description
Function merkleizeSha256 need to receive leaves paramatere such that leaves.lenght is power of 2. (@notice requires the leaves.length is a power of 2). If not, while loop will not end.

All functions which call merkleizeSha256() pass right paramater. Anyway, could be better, for future implementation, to add require in order to verify this requirement.

You could do that following [How to check if a number is a power of 2](https://stackoverflow.com/questions/600293/how-to-check-if-a-number-is-a-power-of-2)
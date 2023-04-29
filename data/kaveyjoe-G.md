Merkel.sol



Gas usage can be reduced in merkel.sol by optimizing the assembly code for processInclusionProofKeccak() and processInclusionProofSha256()

One optimization would be to replace assembly code with Solidity code to avoid the overhead of the EVM executing assembly code. Also, the div operation inside the loop should be replaced with a right shift (>>) operation for better performance.

Here's an optimized version of processInclusionProofKeccak():


function processInclusionProofKeccak(bytes memory proof, bytes32 leaf, uint256 index) internal pure returns (bytes32) {
    bytes32 computedHash = leaf;
    uint256 remainingBits = index;
    for (uint256 i = 32; i <= proof.length; i += 32) {
        bytes32 proofElement;
        assembly {
            proofElement := mload(add(proof, i))
        }
        uint256 remainingBitsMod2 = remainingBits & 1;
        if (remainingBitsMod2 == 0) {
            computedHash = keccak256(abi.encodePacked(computedHash, proofElement));
        } else {
            computedHash = keccak256(abi.encodePacked(proofElement, computedHash));
        }
        remainingBits >>= 1;
    }
    return computedHash;
}


And here's an optimized version of processInclusionProofSha256():




function processInclusionProofSha256(bytes memory proof, bytes32 leaf, uint256 index) internal view returns (bytes32) {
    bytes32 computedHash = leaf;
    uint256 remainingBits = index;
    for (uint256 i = 32; i <= proof.length; i += 32) {
        bytes32 proofElement;
        assembly {
            proofElement := mload(add(proof, i))
        }
        uint256 remainingBitsMod2 = remainingBits & 1;
        if (remainingBitsMod2 == 0) {
            computedHash = sha256(abi.encodePacked(computedHash, proofElement));
        } else {
            computedHash = sha256(abi.encodePacked(proofElement, computedHash));
        }
        remainingBits >>= 1;
    }
    return computedHash;
}

By making these changes, gas usage should be reduced.
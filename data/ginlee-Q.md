[L-01]abi.encodePacked() should not be used with dynamic types when passing the result to a hash function such as SHA256()
[https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/libraries/Merkle.sol#L138-L146](https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/libraries/Merkle.sol#L138-L146)

When using abi.encodePacked() with dynamic types, such as arrays or strings, the resulting byte array can have a variable length, depending on the values passed in. This can create issues when passing the packed byte array to a hash function like SHA256(), as the length of the input may not be what you expect.

Use abi.encode instead of abi.encodePacked()

[L-02]Consider using OpenZeppelin’s SafeCast library to prevent unexpected overflows when casting from uint256
https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/pods/DelayedWithdrawalRouter.sol#L64

In Solidity, block.number is a uint256 type variable that represents the height of the current block. In this code, converting block.number to a uint32 type variable can be achieved by uint32(block.number).

This type conversion method itself is not a problem, but it is important to note that when converting a uint256 type variable to a uint32 type variable, if the value of the uint256 type variable is greater than the maximum value of the uint32 type (i.e., 2^32-1), the high bits of the data will be truncated, leading to loss of precision. Therefore, if there is a possibility of exceeding the range of the uint32 type when performing the conversion, it is important to handle it carefully to avoid data loss or errors.

Mitigation
Consider using OpenZeppelin’s SafeCast library to prevent unexpected overflows when casting from uint256.
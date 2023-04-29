Caching length in for loops.

https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/core/StrategyManager.sol#L358


Consider a generic example of an array arr and the following loop:


for (uint i = 0; i < arr.length; i++) {
    // do something that doesn't change arr.length}


In the above case, the solidity compiler will always read the length of
the array during each iteration. That is, if it is a storage array, this
is an extra sload operation (100 additional extra gas for each
iteration except for the first) and if it is a memory array, this is an
extra mload operation (3 additional gas for each iteration except for
the first).
This extra costs can be avoided by caching the array length (in stack):


uint length = arr.length;
for (uint i = 0; i < length; i++) {
    // do something that doesn't change arr.length}

       
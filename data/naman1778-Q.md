## Use of bytes.concat() instead of abi.encodePacked()

Rather than using abi.encodePacked for appending bytes, since version 0.8.4, bytes.concat() is enabled

    File: src/contracts/core/StrategyManager.sol

    277:        digestHash = keccak256(abi.encodePacked("\x19\x01", domain_separator, structHash));
    279:        digestHash = keccak256(abi.encodePacked("\x19\x01", DOMAIN_SEPARATOR, structHash));

https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/core/StrategyManager.sol

    File: src/contracts/pods/EigenPodManager.sol

    173: abi.encodePacked(
    174:     beaconProxyBytecode, 
    175:     abi.encode(eigenPodBeacon, "")
    176: )

    200: keccak256(abi.encodePacked(
    201:    beaconProxyBytecode, 
    202:    abi.encode(eigenPodBeacon, "")
    203: )) //bytecode

https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/pods/EigenPodManager.sol

    File: src/contracts/pods/EigenPod.sol

    461: return abi.encodePacked(bytes1(uint8(1)), bytes11(0), address(this));

https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/pods/EigenPod.sol

    File: src/contracts/libraries/Merkle.sol

    138: layer[i] = sha256(abi.encodePacked(leaves[2*i], leaves[2*i+1]));

    146: layer[i] = sha256(abi.encodePacked(layer[2*i], layer[2*i+1]));

https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/libraries/Merkle.sol

## Unused local variables

Unused variables should be removed.

    File: src/contracts/permissions/Pausable.sol

    23: uint256 constant internal PAUSE_ALL = type(uint256).max;

https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/permissions/Pausable.sol


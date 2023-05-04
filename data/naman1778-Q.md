## [N-01] Use of bytes.concat() instead of abi.encodePacked()

Rather than using abi.encodePacked for appending bytes, since version 0.8.4, bytes.concat() is enabled

There are 7 instances of this issue in 4 files:

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

## [N-02] Unused local variables

Unused variables should be removed.

There is 1 instance of this issue in 1 file: 

    File: src/contracts/permissions/Pausable.sol

    23: uint256 constant internal PAUSE_ALL = type(uint256).max;

https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/permissions/Pausable.sol

## [N-03] According to the syntax rules, use => mapping ( instead of => mapping( using spaces as keyword

There are 2 instances of this issue in 2 files:

    File: src/contracts/core/StrategyManagerStorage.sol

    mapping(address => mapping(IStrategy => uint256)) public stakerStrategyShares;

https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/core/StrategyManagerStorage.sol

    File: src/contracts/pods/EigenPod.sol

    mapping(uint40 => mapping(uint64 => bool)) public provenPartialWithdrawal;

https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/pods/EigenPod.sol

## [N-04] Contract Owner Has Too Many Privileges

The owner of the contracts has too many privileges relative to standard users. The consequence is disastrous if the contract owner’s private key has been compromised. And, in the event the key was lost or unrecoverable, no implementation upgrades and system parameter updates will ever be possible.

For a project this grand, it increases the likelihood that the owner will be targeted by an attacker, especially given the insufficient protection on sensitive owner private keys. The concentration of privileges creates a single point of failure; and, here are some of the incidents that could possibly transpire:

Transfer ownership and mess up with all the setter functions, hijacking the entire protocol.

Consider:

-- splitting privileges (e.g. via the multisig option) to ensure that no one address has excessive ownership of the system,
-- clearly documenting the functions and implementations the owner can change,
-- documenting the risks associated with privileged users and single points of failure, and
-- ensuring that users are aware of all the risks associated with the system.
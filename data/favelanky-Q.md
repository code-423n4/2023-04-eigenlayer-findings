# Table of Content

| Number | Issues Details                                         | Count |
| :----- | :----------------------------------------------------- | :---- |
| [L-1]  | Lack of zero address checks                            | 1     |
| [L-2]  | Hardcoded chain id                                     | 1     |
| [L-3]  | Critical address changes should use two-step procedure | 2     |
| [N-1]  | Redundant variable check                               | 1     |
| [N-2]  | Variable never used                                    | 1     |

Total number of issues: 6

# Findings

## [L-1] Lack of zero address checks

There is 1 instances of this issue:

```solidity
File: /pods/EigenPodManager.sol

161:  function updateBeaconChainOracle(IBeaconChainOracle newBeaconChainOracle) external onlyOwner {

```

https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/pods/EigenPodManager.sol#L161

## [L-2] Hardcoded chain id

Hardcoding chain id is not recommended because it can change in the future due to hardfork. This variable is used to create a `DOMAIN_SEPARATOR`.

There is 1 instances of this issue:

```solidity
File: /core/StrategyManager.sol

42:  uint256 immutable ORIGINAL_CHAIN_ID;

```

https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/core/StrategyManager.sol#L42

## [L-3] Critical Address Changes Should Use Two-step Procedure

Maybe it is not critical for pauser, but it is critical for unpauser. Because only unpauser can change pauser and unpauser.

There are 2 instances of this issue:

```solidity
File: /permissions/PauserRegistry.sol

41:  function _setPauser(address newPauser) internal {
42:  require(newPauser != address(0), "PauserRegistry._setPauser: zero address input");
43:  emit PauserChanged(pauser, newPauser);
44:         pauser = newPauser;
45:     }
46:

47:  function _setUnpauser(address newUnpauser) internal {
48:  require(newUnpauser != address(0), "PauserRegistry._setUnpauser: zero address input");
49:  emit UnpauserChanged(unpauser, newUnpauser);
50:         unpauser = newUnpauser;
51:     }

```

https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/permissions/PauserRegistry.sol#L41-L51

## [N-1] Redundant variable check

There is 1 instances of this issue:

`EigenPod` can only be deployed from `EigenPodManager` where `_podOwner` variable is `msg.sender` hence it can't be address(0).

```solidity
File: /pods/EigenPod.sol

153:  require(_podOwner != address(0), "EigenPod.initialize: podOwner cannot be zero address");

```

https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/pods/EigenPod.sol#L153

## [N-2] Variable never used

I think `PAUSE_ALL` was designed to replace usage of `type(uint256).max` but it was never used.

There is 1 instances of this issue:

```solidity
File: /permissions/Pausable.sol

    uint256 constant internal PAUSE_ALL = type(uint256).max;

```

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/permissions/Pausable.sol#L23

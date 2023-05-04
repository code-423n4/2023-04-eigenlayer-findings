|     | Issues                                                                                               | Instances |
| :-- | :--------------------------------------------------------------------------------------------------- | --------: |
| 01  | Shorthand way to write `if` / `else` statement.                                                      |         2 |
| 02  | Don't use of `Block.Timestamp` can be manipulat                                                      |         1 |
| 03  | According to the syntax rules, use `=> mapping ( ` instead of `=> mapping(` using spaces as keyword. |        11 |
| 04  | One line `if` statements. Best Practice                                                              |        7 |

## [01] - Shorthand way to write `if` / `else` statement.

1. Increases readability
2. Shortens the overall SLOC

_The normal `if` / `else` statement can be refactored in a shorthand way to write it:_

There are 2 instances:

[contracts/strategies/StrategyBase.sol#L92-L101](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/strategies/StrategyBase.sol#L92-L101)

```java
        if (totalShares == 0) {
            newShares = amount;
        } else {
            uint256 priorTokenBalance = _tokenBalance() - amount;
            if (priorTokenBalance == 0) {
                newShares = amount;
            } else {
                newShares = (amount * totalShares) / priorTokenBalance;
            }
        }
```

change to:

```java
        if (totalShares == 0) {
            newShares = amount;
        } else {
            uint256 priorTokenBalance = _tokenBalance() - amount;
            newShares = priorTokenBalance == 0 ? amount : (amount * totalShares) / priorTokenBalance;
        }
```

[contracts/strategies/StrategyBase.sol#L149-L153](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/strategies/StrategyBase.sol#L149-L153)

```java
 if (priorTotalShares == amountShares) {
            amountToSend = _tokenBalance();
        } else {
            amountToSend = (_tokenBalance() * amountShares) / priorTotalShares;
        }
```

change to:

```js
amountToSend = priorTotalShares == amountShares ? _tokenBalance(); : (_tokenBalance() * amountShares) / priorTotalShares;
```

## [02] - Don't use of `Block.Timestamp` can be manipulated.

There an instance:

[Docs](https://solidity-by-example.org/hacks/block-timestamp-manipulation/)
_Block timestamps have historically been used for a variety of applications, such as entropy for random numbers (see the Entropy Illusion for further details), locking funds for periods of time, and various state-changing conditional statements that are time-dependent. Miners have the ability to adjust timestamps slightly, which can prove to be dangerous if block timestamps are used incorrectly in smart contracts._

[contracts/core/StrategyManager.sol#L262-L265](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L262-L265)

```ts
require(expiry >=
    block.timestamp, 'StrategyManager.depositIntoStrategyWithSignature: signature expired');
```

Recommended Mitigation Steps:

-   Block timestamps should not be used for entropy or generating random numbers—i.e., they should not be the deciding factor (either directly or through some derivation) for winning a game or changing an important state.
-   Time-sensitive logic is sometimes required; e.g., for unlocking contracts (time-locking), completing an ICO after a few weeks, or enforcing expiry dates. It is sometimes recommended to use block.number and an average block time to estimate times; with a 10 second block time, 1 week equates to approximately, 60480 blocks. Thus, specifying a block number at which to change a contract state can be more secure, as miners are unable to easily manipulate the block number.

## [03] - According to the syntax rules, use `=> mapping ( ` instead of `=> mapping(` using spaces as keyword.

There are 11 instances:

[contracts/core/StrategyManagerStorage.sol#L25](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManagerStorage.sol#L25)

```ts
- mapping(address => uint256) public nonces;
+ mapping ( address => uint256 ) public nonces;
```

[contracts/core/StrategyManagerStorage.sol#L49](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManagerStorage.sol#L49)

```ts
- mapping(address => mapping(IStrategy => uint256)) public stakerStrategyShares;
+ mapping ( address => mapping ( IStrategy => uint256 )) public stakerStrategyShares;
```

[contracts/core/StrategyManagerStorage.sol#L51](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManagerStorage.sol#L51)

```ts
- mapping(address => IStrategy[]) public stakerStrategyList;
+ mapping ( address => IStrategy[] ) public stakerStrategyList;
```

[contracts/core/StrategyManagerStorage.sol#L53](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManagerStorage.sol#L53)

```ts
- mapping(bytes32 => bool) public withdrawalRootPending;
+ mapping ( bytes32 => bool ) public withdrawalRootPending;
```

[contracts/core/StrategyManagerStorage.sol#L55](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManagerStorage.sol#L55)

```ts
- mapping(address => uint256) public numWithdrawalsQueued;
+ mapping ( address => uint256 ) public numWithdrawalsQueued;
```

[contracts/core/StrategyManagerStorage.sol#L57](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManagerStorage.sol#L57)

```ts
- mapping(IStrategy => bool) public strategyIsWhitelistedForDeposit;
+ mapping ( IStrategy => bool ) public strategyIsWhitelistedForDeposit;
```

[contracts/core/StrategyManagerStorage.sol#L68](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManagerStorage.sol#L68)

```ts
- mapping(address => uint256) public beaconChainETHSharesToDecrementOnWithdrawal;
+ mapping ( address => uint256 ) public beaconChainETHSharesToDecrementOnWithdrawal;
```

[contracts/pods/EigenPodManager.sol#L55](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPodManager.sol#L55)

```ts
- mapping(address => IEigenPod) public ownerToPod;
+ mapping ( address => IEigenPod ) public ownerToPod;
```

[contracts/pods/EigenPod.sol#L76](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPod.sol#L76)

```ts
- mapping(uint40 => VALIDATOR_STATUS) public validatorStatus;
+ mapping ( uint40 => VALIDATOR_STATUS ) public validatorStatus;
```

[contracts/pods/EigenPod.sol#L79](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPod.sol#L79)

```ts
- mapping(uint40 => mapping(uint64 => bool)) public provenPartialWithdrawal;
+ mapping (uint40 => mapping ( uint64 => bool )) public provenPartialWithdrawal;
```

[contracts/pods/DelayedWithdrawalRouter.sol#L30](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/DelayedWithdrawalRouter.sol#L30)

```ts
- mapping(address => UserDelayedWithdrawals) internal _userWithdrawals;
+ mapping ( address => UserDelayedWithdrawals ) internal _userWithdrawals;
```

## [04] - One line `if` statements. Best Practice

There are 7 instances:

[contracts/strategies/StrategyBase.sol#L173-L177](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/strategies/StrategyBase.sol#L173-L177)

```ts
if (totalShares == 0) {
    return amountShares;
} else {
    return (_tokenBalance() * amountShares) / totalShares;
}
```

change to:

```ts
if (totalShares == 0) return amountShares;
return (_tokenBalance() * amountShares) / totalShares;
```

[contracts/strategies/StrategyBase.sol#L198-L202](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/strategies/StrategyBase.sol#L198-L202)

```ts
if (tokenBalance == 0 || totalShares == 0) {
    return amountUnderlying;
} else {
    return (amountUnderlying * totalShares) / tokenBalance;
}
```

change to:

```ts
if (tokenBalance == 0 || totalShares == 0) return amountUnderlying;
return (amountUnderlying * totalShares) / tokenBalance;
```

[contracts/pods/EigenPodManager.sol#L114-L117](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPodManager.sol#L114-L117)

```ts
if (address(pod) == address(0)) {
    //deploy a pod if the sender doesn't have one already
    pod = _deployPod();
}
```

change to:

```ts
//deploy a pod if the sender doesn't have one already
if (address(pod) == address(0)) pod = _deployPod();
```

[contracts/pods/EigenPod.sol#L218-L220](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPod.sol#L218-L220)

```ts
if (!hasRestaked) {
    hasRestaked = true;
}
```

change to:

```ts
if (!hasRestaked) hasRestaked = true;
```

[contracts/pods/EigenPod.sol#L417-L419](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPod.sol#L417-L419)

```ts
if (amountToSend != 0) {
    _sendETH(recipient, amountToSend);
}
```

change to:

```ts
if (amountToSend != 0) _sendETH(recipient, amountToSend);
```

[contracts/pods/DelayedWithdrawalRouter.sol#L146-L148](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/DelayedWithdrawalRouter.sol#L146-L148)

```ts
if (block.number < delayedWithdrawal.blockCreated + withdrawalDelayBlocks) {
    break;
}
```

change to:

```java
if (block.number < delayedWithdrawal.blockCreated + withdrawalDelayBlocks) break;
```

[contracts/pods/DelayedWithdrawalRouter.sol#L159-L161](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/DelayedWithdrawalRouter.sol#L159-L161)

```ts
if (amountToSend != 0) {
    AddressUpgradeable.sendValue(payable(recipient), amountToSend);
}
```

change to:

```java
if (amountToSend != 0) AddressUpgradeable.sendValue(payable(recipient), amountToSend);
```

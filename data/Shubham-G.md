## Gas Optimization

| |Issue|Instances|
|-|:-|:-:|
| [GAS-01](#GAS-01) | Emitting events inside a loop consumes a lot of gas  |  |

## [G‑01] Emitting events inside a loop consumes a lot of gas

Emitting every strategy till `strategies.length` consumes a lot of gas which is unnecessary.

```solidity
File: contracts/core/StrategyManager.sol

L:376        emit ShareWithdrawalQueued(msg.sender, nonce, strategies[i], shares[i]);
```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L376

### Recommendation

Create an array to hold the emitted events.
```solidity
struct ShareWithdrawalQueuedEvent {
    address staker;
    uint96 nonce;
    IStrategy strategy;
    uint256 shares;
}
```

Add the event to an array instead of emitting it.
```solidity
withdrawalEvents[i] = ShareWithdrawalQueuedEvent(msg.sender, nonce, strategies[i], shares[i]);
```
Emit all the events after the loop has finished executing. 
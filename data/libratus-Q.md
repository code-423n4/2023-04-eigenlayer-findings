**L-01 _completeQueuedWithdrawal should not assume operator has to delegate to itself**

Operator must not be frozen in order to complete a withdrawal. This is enforced [here](https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/core/StrategyManager.sol#L745) using `onlyNotFrozen(queuedWithdrawal.delegatedAddress)`.

This check assumes that an operator always delegates to himself. This is currently indeed the case in `DelegationManager`, however, since the contract is out of scope, this behavior may change. For additional safety consider adding `onlyNotFrozen(queuedWithdrawal.depositor)`.

**L-02 Owner can renounce ownership**
Openzeppelin's `Ownable` has a function to renounce ownership. Consider overriding this function to disallow renouncing in StrategyManager and EigenPodManager

**NC-01 Use Ownable2Step instead of Ownable**
[Ownable2Step](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/access/Ownable2Step.sol) is a safer alternative of Ownable that can prevent mistakes when transfering ownership.

**NC-02 Block time change**

MAX_WITHDRAWAL_DELAY_BLOCKS is a constant and is set based on 12-second block time. The protocol is trying to be future-proof and even handles the case with chain id changing. 
```solidity
        //if chainid has changed, we must re-compute the domain separator
        if (block.chainid != ORIGINAL_CHAIN_ID) {
```

For consistency it also makes sense to consider a possible block time change and make MAX_WITHDRAWAL_DELAY_BLOCKS modifiable by the admin.

**NC-02 Missing code coverage**
Tests don't cover certain important code paths like:
- https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/core/StrategyManager.sol#L191C1-L193
- https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/core/StrategyManager.sol#L824-L827
- https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/pods/EigenPodManager.sol#L197


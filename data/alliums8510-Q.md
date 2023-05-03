# QA Report


## Low Risk Issues

| | Issue | Instances |
|-|:-|:-:|
| [L-1](#L-1) | Misplaced `eigenPodManager.withdrawFunctionRestakedBeaconChainETH()` | 1 |
| [L-2](#L-2) | Set a `require` statement in stake function  | 1 |


## Non-critical Issues

| | Issue | Instances |
|-|:-|:-:|
| [QA-1](#QA-1) | Code styling - emitted event before code execution | 18 |
| [QA-2](#QA-2) | Return values of external call not checked         | 2 |

<hr/>



<a name="L-1"></a>

### [L-1] Misplaced `eigenPodManager.withdrawFunctionRestakedBeaconChainETH()`

`eigenPodManager.withdrawRestakedBeaconChainETH(staker, recipient, amount);` is called even when the `amountToDecrement` is 0. 
In turn, the function then calls an ETH transfer function where the amount would be passed as 0;


*Instances (1)*:
```solidity
File: /code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol
823:      if (amountToDecrement != 0) {
            if (amount > amountToDecrement) {
                beaconChainETHSharesToDecrementOnWithdrawal[staker] = 0;
                // decrease `amount` appropriately, so less is sent at the end
                amount -= amountToDecrement;
            } else {
                beaconChainETHSharesToDecrementOnWithdrawal[staker] = (amountToDecrement - amount);
                // rather than setting `amount` to 0, just return early
                return;
            }
          }
          // withdraw the beaconChainETH to the recipient
          eigenPodManager.withdrawRestakedBeaconChainETH(staker, recipient, amount);

```
[Link to code](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L821)

*Recommended: Move `eigenPodManager.withdrawRestakedBeaconChainETH(staker, recipient, amount);` inside `if(amountToDecrement != 0)` condition.* 

<hr/>

<a name="L-2"></a>

### [L-2] Set a `require` statement in stake function 

`pod.stake{value: msg.value}` only accepts 32 ETH value. So, it is better to check if the `require(msg.value == 32 ether)` at line 113. Than to perform all the transaction (deploying new pod) only to be reverted at the end.

*Instances (1)*:
```solidity
File: /code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPodManager.sol
112: function stake(bytes calldata pubkey, bytes calldata signature, bytes32 depositDataRoot) external payable {
        IEigenPod pod = ownerToPod[msg.sender];
        if(address(pod) == address(0)) {
            //deploy a pod if the sender doesn't have one already
            pod = _deployPod();
        }
        pod.stake{value: msg.value}(pubkey, signature, depositDataRoot);
    }
```
[Link to code](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPodManager.sol#L112)

<hr/>



<a name="NA-1"></a>

### [NA-1] Code styling - emitted event before code execution

It's a good practice to `emit` events after the oprations are done. Change the placement of emitted event at the end of the code.


*Instances (1)*:
```solidity
File: /code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol
839:      function _setWithdrawalDelayBlocks(uint256 _withdrawalDelayBlocks) internal {
            require(_withdrawalDelayBlocks <= MAX_WITHDRAWAL_DELAY_BLOCKS, "StrategyManager.setWithdrawalDelay: _withdrawalDelayBlocks too high");
            emit WithdrawalDelayBlocksSet(withdrawalDelayBlocks, _withdrawalDelayBlocks);
            withdrawalDelayBlocks = _withdrawalDelayBlocks;
          }
```
[Link to code](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L839)

<hr/>


<a name="NA-2"></a>

### [NA-2] Return values of external call not checked

Appropriate to have return values of the call functions to manage any revert during the operation.

*Instances (3)*:
```solidity
File: /code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPod.sol

161: ethPOS.deposit{value : 32 ether}(pubkey, _podWithdrawalCredentials(), signature, depositDataRoot);
465: delayedWithdrawalRouter.createDelayedWithdrawal{value: amountWei}(podOwner, recipient);
```
[Link to code](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPod.sol#L161)

```solidity
File: /code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPodManager.sol

118: pod.stake{value: msg.value}(pubkey, signature, depositDataRoot);
```
[Link to code](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPodManager.sol#L118)

<hr/>
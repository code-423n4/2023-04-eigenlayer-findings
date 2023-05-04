|      | Issues                                                                    | Instances |
| :--- | :------------------------------------------------------------------------ | --------: |
| G-01 | Consider using `private` functions instead of `modifier` might be better. |        10 |
| G-02 | Revert as early as possible.                                              |         1 |
| G-03 | Shift left by 5 instead of multiplying by 32.                             |         4 |
| G-04 | Shift left by 1 instead of multiplying by 2.                              |         2 |

## [G-01] - Consider using `private` functions instead of `modifier` might be better.

_Can optimize their contracts bytecode size._

-   𝐌𝐨𝐝𝐢𝐟𝐢𝐞𝐫𝐬 𝐚𝐫𝐞 𝐞𝐱𝐩𝐚𝐧𝐝𝐞𝐝 𝐢𝐧𝐥𝐢𝐧𝐞:

When you use a modifier, Solidity copies the entire code of the modifier into every function that calls it.

This means that the modifier code becomes a part of the function and is included in the bytecode generated for that function.

This technically means that the modifier code is replicated in every function where the modifier is used, which can increase the size of the contract's bytecode.

-   𝐏𝐫𝐢𝐯𝐚𝐭𝐞 𝐟𝐮𝐧𝐜𝐭𝐢𝐨𝐧𝐬 𝐚𝐫𝐞 𝐨𝐧𝐥𝐲 𝐰𝐫𝐢𝐭𝐭𝐞𝐧 𝐨𝐧𝐜𝐞:

On the other hand, a private function is only written once and can be called from multiple places in your contract without increasing its size.

This makes it a much more efficient way to perform checks without duplicating code.

There are 10 instances:

[contracts/core/StrategyManager.sol#L104-L107](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L104-L107)

```java
   modifier onlyFrozen(address staker) {
       require(slasher.isFrozen(staker), "StrategyManager.onlyFrozen: staker has not been frozen");
       _;
   }
```

[contracts/core/StrategyManager.sol#L109-L112](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L109-L112)

```java
   modifier onlyEigenPodManager {
       require(address(eigenPodManager) == msg.sender, "StrategyManager.onlyEigenPodManager: not the eigenPodManager");
       _;
   }
```

[contracts/core/StrategyManager.sol#L114-L117](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L114-L117)

```java
   modifier onlyStrategyWhitelister {
       require(msg.sender == strategyWhitelister, "StrategyManager.onlyStrategyWhitelister: not the strategyWhitelister");
       _;
   }
```

[contracts/core/StrategyManager.sol#L119-L122](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L119-L122)

```java
   modifier onlyStrategiesWhitelistedForDeposit(IStrategy strategy) {
       require(strategyIsWhitelistedForDeposit[strategy], "StrategyManager.onlyStrategiesWhitelistedForDeposit: strategy not whitelisted");
       _;
   }
```

[contracts/pods/EigenPod.sol#L99-L102](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPod.sol#L99-L102)

```java
    modifier onlyEigenPodManager {
        require(msg.sender == address(eigenPodManager), "EigenPod.onlyEigenPodManager: not eigenPodManager");
        _;
    }
```

[contracts/pods/EigenPod.sol#L104-L107](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPod.sol#L104-L107)

```java
    modifier onlyEigenPodOwner {
        require(msg.sender == podOwner, "EigenPod.onlyEigenPodOwner: not podOwner");
        _;
    }
```

[contracts/pods/EigenPod.sol#L109-L112](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPod.sol#L109-L112)

```java
    modifier onlyNotFrozen {
        require(!eigenPodManager.slasher().isFrozen(podOwner), "EigenPod.onlyNotFrozen: pod owner is frozen");
        _;
    }
```

[contracts/pods/EigenPod.sol#L114-L117](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPod.sol#L114-L117)

```java
    modifier hasNeverRestaked {
        require(!hasRestaked, "EigenPod.hasNeverRestaked: restaking is enabled");
        _;
    }
```

[contracts/pods/EigenPod.sol#L120-L124](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPod.sol#L120-L124)

```java
    modifier proofIsForValidBlockNumber(uint64 blockNumber) {
        require(blockNumber > mostRecentWithdrawalBlockNumber,
            "EigenPod.proofIsForValidBlockNumber: beacon chain proof must be for block number after mostRecentWithdrawalBlockNumber");
        _;
    }
```

[contracts/pods/EigenPod.sol#L131-L134](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPod.sol#L131-L134)

```java
    modifier onlyWhenNotPaused(uint8 index) {
        require(!IPausable(address(eigenPodManager)).paused(index), "EigenPod.onlyWhenNotPaused: index is paused in EigenPodManager");
        _;
    }
```

## [G-02] - Revert as early as possible.

_You pay gas for everything before the `require()` in the revert case._

The an instance:

[contracts/pods/EigenPod.sol#L147](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPod.sol#L147)

```ts
 ) {
        ethPOS = _ethPOS;
        delayedWithdrawalRouter = _delayedWithdrawalRouter;
        eigenPodManager = _eigenPodManager;
        REQUIRED_BALANCE_WEI = _REQUIRED_BALANCE_WEI;
        REQUIRED_BALANCE_GWEI = uint64(_REQUIRED_BALANCE_WEI / GWEI_TO_WEI);
        require(_REQUIRED_BALANCE_WEI % GWEI_TO_WEI == 0, "EigenPod.contructor: _REQUIRED_BALANCE_WEI is not a whole number of gwei");
        _disableInitializers();
    }
```

Recommended Mitigation Steps:

-   Change line 147 to line 142. After `ethPOS = _ethPOS;`.

## [G-03] - Shift left by 5 instead of multiplying by 32.

_The equivalent of multiplying by 32 is shifting left by 5._

This is due to the fact that the MUL opcode costs 5 gas and the SHL opcode costs 3 gas. Therefore, saving those 2 units of gas is expected.

There are 4 instances:

[contracts/libraries/BeaconChainProofs.sol#L205](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/libraries/BeaconChainProofs.sol#L205)

```ts
require(proof.length ==
    32 *
        (VALIDATOR_TREE_HEIGHT +
            1 +
            BEACON_STATE_FIELD_TREE_HEIGHT), 'BeaconChainProofs.verifyValidatorFields: Proof has incorrect length');
```

change to:

```ts
require(proof.length ==
    (VALIDATOR_TREE_HEIGHT + 1 + BEACON_STATE_FIELD_TREE_HEIGHT) <<
        5, 'BeaconChainProofs.verifyValidatorFields: Proof has incorrect length');
```

[contracts/libraries/BeaconChainProofs.sol#L256](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/libraries/BeaconChainProofs.sol#L256)

```ts
require(proofs.blockHeaderProof.length ==
    32 *
        (BEACON_STATE_FIELD_TREE_HEIGHT +
            BLOCK_ROOTS_TREE_HEIGHT), 'BeaconChainProofs.verifyWithdrawalProofs: blockHeaderProof has incorrect length');
```

change to:

```ts
require(proofs.blockHeaderProof.length ==
    (BEACON_STATE_FIELD_TREE_HEIGHT + BLOCK_ROOTS_TREE_HEIGHT) <<
        5, 'BeaconChainProofs.verifyWithdrawalProofs: blockHeaderProof has incorrect length');
```

[contracts/libraries/BeaconChainProofs.sol#L258](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/libraries/BeaconChainProofs.sol#L258)

```ts
require(proofs.withdrawalProof.length ==
    32 *
        (EXECUTION_PAYLOAD_HEADER_FIELD_TREE_HEIGHT +
            WITHDRAWALS_TREE_HEIGHT +
            1), 'BeaconChainProofs.verifyWithdrawalProofs: withdrawalProof has incorrect length');
```

change to:

```ts
require(proofs.withdrawalProof.length ==
    (EXECUTION_PAYLOAD_HEADER_FIELD_TREE_HEIGHT +
        WITHDRAWALS_TREE_HEIGHT +
        1) <<
        5, 'BeaconChainProofs.verifyWithdrawalProofs: withdrawalProof has incorrect length');
```

[contracts/libraries/BeaconChainProofs.sol#L260](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/libraries/BeaconChainProofs.sol#L260)

```ts
require(proofs.executionPayloadProof.length ==
    32 *
        (BEACON_BLOCK_HEADER_FIELD_TREE_HEIGHT +
            BEACON_BLOCK_BODY_FIELD_TREE_HEIGHT), 'BeaconChainProofs.verifyWithdrawalProofs: executionPayloadProof has incorrect length');
```

change to:

```ts
require(proofs.executionPayloadProof.length ==
    (BEACON_BLOCK_HEADER_FIELD_TREE_HEIGHT +
        BEACON_BLOCK_BODY_FIELD_TREE_HEIGHT) <<
        5, 'BeaconChainProofs.verifyWithdrawalProofs: executionPayloadProof has incorrect length');
```

## [G-04] - Shift left by 1 instead of multiplying by 2.

_The equivalent of multiplying by 2 is shifting left by 1._

_This is due to the fact that the MUL opcode costs 5 gas and the SHL opcode costs 3 gas. Therefore, saving those 2 units of gas is expected._

There are 2 instances:

[contracts/libraries/Merkle.sol#L138](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/libraries/Merkle.sol#L138)

```ts
layer[i] = sha256(abi.encodePacked(leaves[2 * i], leaves[2 * i + 1]));
```

change to:

```ts
layer[i] = sha256(abi.encodePacked(leaves[i << 1], leaves[(i << 1) + 1]));
```

[contracts/libraries/Merkle.sol#L146](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/libraries/Merkle.sol#L146)

```ts
layer[i] = sha256(abi.encodePacked(layer[2 * i], layer[2 * i + 1]));
```

change to:

```ts
layer[i] = sha256(abi.encodePacked(layer[i << 1], layer[(i << 1) + 1]));
```

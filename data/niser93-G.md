# GAS REPORT

| GAS_N°| Title  | Total gas saving|
|:--------:|:------:|:---------------:|
| [GAS-01](#gas-01-avoid-useless-checks)  | Avoid useless checks      | 1233               |
| [GAS-02](#gas-02-x--y-costs-more-gas-than-x--x--y)  | x += y costs more gas than x = x + y      | 408493               |
| [GAS-03](#gas-03-change-variable-declaration-and-ifelse-in-order-to-have-better-path)  | Change variable declaration and if/else in order to have better path      | 24525               |
| [GAS-04](#gas-04-avoid-to-declare-uint256-initial-value)  | Avoid to declare uint256 initial value      | 385               |
| [GAS-05](#gas-05-avoid-to-check-boolean-variables)|  Avoid to check boolean variables      | 7206               |
| [GAS-06](#gas-06---avoid-to-define-variable-which-are-used-once)|  Avoid to define variable which are used once      | 1396               |
| [GAS-07](#gas-07---use-alternative-formulation-in-order-to-avoid-not-using-and-instead-of-or-or-vice-versa)  | Use alternative formulation in order to avoid NOT using AND instead of OR or vice versa      | 497               |
| [GAS-08](#gas-08---2n-can-computed-with-shift)  | 2^n can computed with shift      | 130456               |
| [GAS-09](#gas-09---using-shift-instead-of-multiplication-or-division-by-2)  | Using shift instead of multiplication or division by 2      | 77294               |
| [GAS-10](#gas-10---using-unchecked-shift-instead-of-assembly-div-by-2)  | Using unchecked shift instead of assembly div by 2     | 21702               |
|||
|-| All optimizations applied| 189527

## Optimization details

| Test                             | Name                                                                                                       | Gas     |
|----------------------------------|------------------------------------------------------------------------------------------------------------|---------|
| DelayedWithdrawalRouterUnitTests | testClaimDelayedWithdrawals(uint8,uint8,uint256,address,bool)                                              | 17771   |
| DelayedWithdrawalRouterUnitTests | testClaimDelayedWithdrawalsSomeUnclaimable(uint8,uint8,bool)                                               | -484    |
| DelayedWithdrawalRouterUnitTests | testClaimDelayedWithdrawals_NonzeroToClaim_AttemptToClaimZero(uint256,bool)                                | -3      |
| DelayedWithdrawalRouterUnitTests | testClaimDelayedWithdrawals_RevertsOnAttemptingReentrancy(bool)                                            | -3      |
| DelegationTests                  | testDelegateToBySignature_WithContractWallet_BadSignature(address,uint96,uint96)                           | 5       |
| DepositWithdrawTests             | testForkMainnetDepositSteth()                                                                              | -82740  |
| EigenPodTests                    | testAttemptedWithdrawalAfterVerifyingWithdrawalCredentials()                                               | -1552   |
| EigenPodTests                    | testDeployAndVerifyNewEigenPod()                                                                           | -1552   |
| EigenPodTests                    | testDeployNewEigenPodWithActiveValidator()                                                                 | -1552   |
| EigenPodTests                    | testDoubleFullWithdrawal()                                                                                 | -7024   |
| EigenPodTests                    | testFullWithdrawalFlow()                                                                                   | -4300   |
| EigenPodTests                    | testFullWithdrawalProof()                                                                                  | -71745  |
| EigenPodTests                    | testPartialWithdrawalFlow()                                                                                | -4272   |
| EigenPodTests                    | testProveOverComittedStakeOnWithdrawnValidator()                                                           | -1563   |
| EigenPodTests                    | testProveOverCommittedBalance()                                                                            | -1472   |
| EigenPodTests                    | testProveSingleWithdrawalCredential()                                                                      | -1552   |
| EigenPodTests                    | testProvingMultipleWithdrawalsForSameSlot()                                                                | -6989   |
| EigenPodTests                    | testUpdateSlashedBeaconBalance()                                                                           | -1472   |
| EigenPodTests                    | testVerifyOvercommittedStakeRevertsWhenPaused()                                                            | -1548   |
| EigenPodTests                    | testVerifyWithdrawalCredentialsWithInadequateBalance()                                                     | -4      |
| EigenPodTests                    | testWithdrawBeforeRestakingAfterRestaking()                                                                | -1554   |
| SlasherUnitTests                 | testRecordStakeUpdate_MultipleLinkedListEntries(address,address,uint32,uint32,uint32,uint256)              | -11184  |
| SlasherUnitTests                 | testResetFrozenStatus(uint8,uint256)                                                                       | 11569   |
| StrategyManagerUnitTests         | testDepositBeaconChainETHFailsWhenReentering()                                                             | -8218   |
| StrategyManagerUnitTests         | testDepositIntoStrategyWithSignatureFailsWhenReentering()                                                  | 25      |
| StrategyManagerUnitTests         | testDepositIntoStrategyWithSignatureFailsWhenSignatureInvalid()                                            | 23      |
| StrategyManagerUnitTests         | testDepositIntoStrategyWithSignatureSuccessfully(uint256,uint256)                                          | 5       |
| StrategyManagerUnitTests         | testDepositIntoStrategyWithSignature_WithContractWallet_BadSignature(uint256)                              | 23      |
| StrategyManagerUnitTests         | testDepositIntoStrategyWithSignature_WithContractWallet_NonconformingWallet(uint256,uint8,bytes32,bytes32) | 23      |
| StrategyManagerUnitTests         | testDepositIntoStrategyWithSignature_WithContractWallet_Successfully(uint256,uint256)                      | 35      |
| StrategyManagerUnitTests         | testRecordOvercommittedBeaconChainETHFailsWhenReentering()                                                 | -8218   |
| WithdrawalTests                  | testDelegateToBySignature_WithContractWallet_BadSignature(address,uint96,uint96)                           | -5      |
| --                               | --                                                                                                         |         |
| Total                            |                                                                                                            | -189527 |

## GAS-01 Avoid useless checks

### Founded in [EigenPod.sol](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPod.sol)

[EigenPod.sol#L417-L419](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPod.sol#L417-L419)


```
if (amountToSend != 0) {
    _sendETH(recipient, amountToSend);
}
```

### Description
You can avoid to check if amountToSend is zero.

```diff
-if (amountToSend != 0) {
    _sendETH(recipient, amountToSend);
-}
```

### Proof of concept

When you call _sendETH(recipient, 0), you call:

[EigenPod.sol#L464-L466](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPod.sol#L464-L466)
```
function _sendETH(address recipient, uint256 amountWei) internal {
    delayedWithdrawalRouter.createDelayedWithdrawal{value: amountWei}(podOwner, recipient);
}

i.e.

delayedWithdrawalRouter.createDelayedWithdrawal{value: 0}(podOwner, recipient);
```

which calls

[DelayedWithdrawalRouter.sol#L55-L69](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/DelayedWithdrawalRouter.sol#L55-L69)
```
/** 
* @notice Creates a delayed withdrawal for `msg.value` to the `recipient`.
* @dev Only callable by the `podOwner`'s EigenPod contract.
*/
function createDelayedWithdrawal(address podOwner, address recipient) external payable onlyEigenPod(podOwner) {
    uint224 withdrawalAmount = uint224(msg.value);
    if (withdrawalAmount != 0) {
        DelayedWithdrawal memory delayedWithdrawal = DelayedWithdrawal({
            amount: withdrawalAmount,
            blockCreated: uint32(block.number)
        });
        _userWithdrawals[recipient].delayedWithdrawals.push(delayedWithdrawal);
        emit DelayedWithdrawalCreated(podOwner, recipient, withdrawalAmount, _userWithdrawals[recipient].delayedWithdrawals.length - 1);
    }
}
```

which already check that value is not zero with statement

```
uint224 withdrawalAmount = uint224(msg.value);
    if (withdrawalAmount != 0) {
```

### Test gas profile
Using 
```
forge snapshot
```

```diff
|   Test Name                                                                       | Gas     |
|-----------------------------------------------------------------------------------|---------|
-| testDelegateToBySignature_WithContractWallet_BadSignature(address,uint96,uint96) | 1041072 |
+| testDelegateToBySignature_WithContractWallet_BadSignature(address,uint96,uint96) | 1041077 |
|-----------------------------------------------------------------------------------|---------|
|                                                                                   |       5 |

The average lost gas is 5.
```
                

```diff
|   Test Name                    | Gas      |
|--------------------------------|----------|
-| testForkMainnetDepositSteth() | 20748060 |
+| testForkMainnetDepositSteth() | 20746860 |
|--------------------------------|----------|
|                                |    -1200 |

The saved gas is 1200.
```
                

```diff
|   Test Name                 | Gas      |
|-----------------------------|----------|
-| testDoubleFullWithdrawal() | 45905901 |
+| testDoubleFullWithdrawal() | 45905882 |
|-----------------------------|----------|
|                             |      -19 |

The saved gas is 19.
```
                

```diff
|   Test Name               | Gas      |
|---------------------------|----------|
-| testFullWithdrawalFlow() | 29372354 |
+| testFullWithdrawalFlow() | 29372335 |
|---------------------------|----------|
|                           |      -19 |

The saved gas is 19.
```
                

| Test                 | Name                                                                             | Gas   |
|----------------------|----------------------------------------------------------------------------------|-------|
| DelegationTests      | testDelegateToBySignature_WithContractWallet_BadSignature(address,uint96,uint96) | 5     |
| DepositWithdrawTests | testForkMainnetDepositSteth()                                                    | -1200 |
| EigenPodTests        | testDoubleFullWithdrawal()                                                       | -19   |
| EigenPodTests        | testFullWithdrawalFlow()                                                         | -19   |
| --                   | --                                                                               |       |
| Total                |                                                                                  | -1233 |


## GAS-02 x += y costs more gas than x = x + y

### Founded in [StrategyManager.sol](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol)


[StrategyManager.sol#L193](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L193)
```
amount -= debt;
```

### Founded in [DelayedWithdrawalRouter.sol](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/DelayedWithdrawalRouter.sol)


[DelayedWithdrawalRouter.sol#L150](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/DelayedWithdrawalRouter.sol#L150)
```
amountToSend += delayedWithdrawal.amount;
```

### Description
It was also reported in [Automated findings](https://gist.github.com/CloudEllie/213965a3448230f5b615e7046f9dd26d).
I only report other instaces


### Test gas profile
Using
```
forge snapshot
```

```diff
|   Test Name                                                   | Gas    |
|---------------------------------------------------------------|--------|
-| testClaimDelayedWithdrawalsSomeUnclaimable(uint8,uint8,bool) | 482688 |
+| testClaimDelayedWithdrawalsSomeUnclaimable(uint8,uint8,bool) | 482204 |
|---------------------------------------------------------------|--------|
|                                                               |   -484 |

The average saved gas is 484.
```
                

```diff
|   Test Name                                                                       | Gas     |
|-----------------------------------------------------------------------------------|---------|
-| testDelegateToBySignature_WithContractWallet_BadSignature(address,uint96,uint96) | 1041072 |
+| testDelegateToBySignature_WithContractWallet_BadSignature(address,uint96,uint96) | 1041077 |
|-----------------------------------------------------------------------------------|---------|
|                                                                                   |       5 |

The average lost gas is 5.
```
                

```diff
|   Test Name                                              | Gas     |
|----------------------------------------------------------|---------|
-| testDelegationMultipleStrategies(uint8,address,address) | 2978470 |
+| testDelegationMultipleStrategies(uint8,address,address) | 2570476 |
|----------------------------------------------------------|---------|
|                                                          | -407994 |

The average saved gas is 407994.
```
                

```diff
|   Test Name                                                        | Gas    |
|--------------------------------------------------------------------|--------|
-| testDepositIntoStrategyWithSignatureSuccessfully(uint256,uint256) | 245473 |
+| testDepositIntoStrategyWithSignatureSuccessfully(uint256,uint256) | 245453 |
|--------------------------------------------------------------------|--------|
|                                                                    |    -20 |

The average saved gas is 20.
```
                

| Test                             | Name                                                                             | Gas     |
|----------------------------------|----------------------------------------------------------------------------------|---------|
| DelayedWithdrawalRouterUnitTests | testClaimDelayedWithdrawalsSomeUnclaimable(uint8,uint8,bool)                     | -484    |
| DelegationTests                  | testDelegateToBySignature_WithContractWallet_BadSignature(address,uint96,uint96) | 5       |
| DelegationTests                  | testDelegationMultipleStrategies(uint8,address,address)                          | -407994 |
| StrategyManagerUnitTests         | testDepositIntoStrategyWithSignatureSuccessfully(uint256,uint256)                | -20     |
| --                               | --                                                                               |         |
| Total                            |                                                                                  | -408493 |


## GAS-03 Change variable declaration and if/else in order to have better path

### Founded in [StrategyManager.sol](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol)


[StrategyManager.sol#L273-L280](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L273-L280)
```
bytes32 digestHash;
//if chainid has changed, we must re-compute the domain separator
if (block.chainid != ORIGINAL_CHAIN_ID) {
    bytes32 domain_separator = keccak256(abi.encode(DOMAIN_TYPEHASH, keccak256(bytes("EigenLayer")), block.chainid, address(this)));
    digestHash = keccak256(abi.encodePacked("\x19\x01", domain_separator, structHash));
} else {
    digestHash = keccak256(abi.encodePacked("\x19\x01", DOMAIN_SEPARATOR, structHash));
}
```


### Description
Change domain_separator instead of digestHash in order to use == and avoid to declare digestHash twice.

```diff
- bytes32 digestHash;
- //if chainid has changed, we must re-compute the domain separator
- if (block.chainid != ORIGINAL_CHAIN_ID) {
-     bytes32 domain_separator = keccak256(abi.encode(DOMAIN_TYPEHASH, keccak256(bytes("EigenLayer")), - block.chainid, address(this)));
-     digestHash = keccak256(abi.encodePacked("\x19\x01", domain_separator, structHash));
- } else {
-     digestHash = keccak256(abi.encodePacked("\x19\x01", DOMAIN_SEPARATOR, structHash));
- }
+ bytes32 domain_separator = DOMAIN_SEPARATOR;
+ //if chainid has changed, we must re-compute the domain separator
+ if (block.chainid != ORIGINAL_CHAIN_ID) {
+     domain_separator = keccak256(abi.encode(DOMAIN_TYPEHASH, keccak256(bytes("EigenLayer")), block.chainid, address(this)));
+ }
+ bytes32 digestHash = keccak256(abi.encodePacked("\x19\x01", domain_separator, structHash));
```



### Test gas profile
Using 
```
forge snapshot
```
```diff
|   Test Name                                                                       | Gas     |
|-----------------------------------------------------------------------------------|---------|
-| testDelegateToBySignature_WithContractWallet_BadSignature(address,uint96,uint96) | 1041072 |
+| testDelegateToBySignature_WithContractWallet_BadSignature(address,uint96,uint96) | 1041067 |
|-----------------------------------------------------------------------------------|---------|
|                                                                                   |      -5 |

The average saved gas is 5.
```
                

```diff
|   Test Name                                                                       | Gas     |
|-----------------------------------------------------------------------------------|---------|
-| testDelegateToBySignature_WithContractWallet_Successfully(address,uint96,uint96) | 1125805 |
+| testDelegateToBySignature_WithContractWallet_Successfully(address,uint96,uint96) | 1125825 |
|-----------------------------------------------------------------------------------|---------|
|                                                                                   |      20 |

The average lost gas is 20.
```
                

```diff
|   Test Name                    | Gas      |
|--------------------------------|----------|
-| testForkMainnetDepositSteth() | 20748060 |
+| testForkMainnetDepositSteth() | 20739842 |
|--------------------------------|----------|
|                                |    -8218 |

The saved gas is 8218.
```
                

```diff
|   Test Name                                     | Gas     |
|-------------------------------------------------|---------|
-| testDepositBeaconChainETHFailsWhenReentering() | 6655413 |
+| testDepositBeaconChainETHFailsWhenReentering() | 6647195 |
|-------------------------------------------------|---------|
|                                                 |   -8218 |

The saved gas is 8218.
```
                

```diff
|   Test Name                                                | Gas     |
|------------------------------------------------------------|---------|
-| testDepositIntoStrategyWithSignatureFailsWhenReentering() | 1544130 |
+| testDepositIntoStrategyWithSignatureFailsWhenReentering() | 1544155 |
|------------------------------------------------------------|---------|
|                                                            |      25 |

The lost gas is 25.
```
                

```diff
|   Test Name                                                      | Gas    |
|------------------------------------------------------------------|--------|
-| testDepositIntoStrategyWithSignatureFailsWhenSignatureInvalid() | 105138 |
+| testDepositIntoStrategyWithSignatureFailsWhenSignatureInvalid() | 105161 |
|------------------------------------------------------------------|--------|
|                                                                  |     23 |

The lost gas is 23.
```
                

```diff
|   Test Name                                                        | Gas    |
|--------------------------------------------------------------------|--------|
-| testDepositIntoStrategyWithSignatureSuccessfully(uint256,uint256) | 245473 |
+| testDepositIntoStrategyWithSignatureSuccessfully(uint256,uint256) | 245478 |
|--------------------------------------------------------------------|--------|
|                                                                    |      5 |

The average lost gas is 5.
```
                

```diff
|   Test Name                                                                    | Gas    |
|--------------------------------------------------------------------------------|--------|
-| testDepositIntoStrategyWithSignature_WithContractWallet_BadSignature(uint256) | 522579 |
+| testDepositIntoStrategyWithSignature_WithContractWallet_BadSignature(uint256) | 522602 |
|--------------------------------------------------------------------------------|--------|
|                                                                                |     23 |

The average lost gas is 23.
```
                

```diff
|   Test Name                                                                                                 | Gas    |
|-------------------------------------------------------------------------------------------------------------|--------|
-| testDepositIntoStrategyWithSignature_WithContractWallet_NonconformingWallet(uint256,uint8,bytes32,bytes32) | 197032 |
+| testDepositIntoStrategyWithSignature_WithContractWallet_NonconformingWallet(uint256,uint8,bytes32,bytes32) | 197055 |
|-------------------------------------------------------------------------------------------------------------|--------|
|                                                                                                             |     23 |

The average lost gas is 23.
```
                

```diff
|   Test Name                                                                            | Gas    |
|----------------------------------------------------------------------------------------|--------|
-| testDepositIntoStrategyWithSignature_WithContractWallet_Successfully(uint256,uint256) | 666644 |
+| testDepositIntoStrategyWithSignature_WithContractWallet_Successfully(uint256,uint256) | 666669 |
|----------------------------------------------------------------------------------------|--------|
|                                                                                        |     25 |

The average lost gas is 25.
```
                

```diff
|   Test Name                                                 | Gas     |
|-------------------------------------------------------------|---------|
-| testRecordOvercommittedBeaconChainETHFailsWhenReentering() | 6641937 |
+| testRecordOvercommittedBeaconChainETHFailsWhenReentering() | 6633719 |
|-------------------------------------------------------------|---------|
|                                                             |   -8218 |

The saved gas is 8218.
```
                

```diff
|   Test Name                                                                       | Gas     |
|-----------------------------------------------------------------------------------|---------|
-| testDelegateToBySignature_WithContractWallet_BadSignature(address,uint96,uint96) | 1041397 |
+| testDelegateToBySignature_WithContractWallet_BadSignature(address,uint96,uint96) | 1041387 |
|-----------------------------------------------------------------------------------|---------|
|                                                                                   |     -10 |

The average saved gas is 10.
```
                

| Test                     | Name                                                                                                       | Gas    |
|--------------------------|------------------------------------------------------------------------------------------------------------|--------|
| DelegationTests          | testDelegateToBySignature_WithContractWallet_BadSignature(address,uint96,uint96)                           | -5     |
| DelegationTests          | testDelegateToBySignature_WithContractWallet_Successfully(address,uint96,uint96)                           | 20     |
| DepositWithdrawTests     | testForkMainnetDepositSteth()                                                                              | -8218  |
| StrategyManagerUnitTests | testDepositBeaconChainETHFailsWhenReentering()                                                             | -8218  |
| StrategyManagerUnitTests | testDepositIntoStrategyWithSignatureFailsWhenReentering()                                                  | 25     |
| StrategyManagerUnitTests | testDepositIntoStrategyWithSignatureFailsWhenSignatureInvalid()                                            | 23     |
| StrategyManagerUnitTests | testDepositIntoStrategyWithSignatureSuccessfully(uint256,uint256)                                          | 5      |
| StrategyManagerUnitTests | testDepositIntoStrategyWithSignature_WithContractWallet_BadSignature(uint256)                              | 23     |
| StrategyManagerUnitTests | testDepositIntoStrategyWithSignature_WithContractWallet_NonconformingWallet(uint256,uint8,bytes32,bytes32) | 23     |
| StrategyManagerUnitTests | testDepositIntoStrategyWithSignature_WithContractWallet_Successfully(uint256,uint256)                      | 25     |
| StrategyManagerUnitTests | testRecordOvercommittedBeaconChainETHFailsWhenReentering()                                                 | -8218  |
| WithdrawalTests          | testDelegateToBySignature_WithContractWallet_BadSignature(address,uint96,uint96)                           | -10    |
| --                       | --                                                                                                         |        |
| Total                    |                                                                                                            | -24525 |


## GAS-04 Avoid to declare uint256 initial value

### Founded in [StrategyManager.sol](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol)


[StrategyManager.sol#L724](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L724)

```
uint256 j = 0;
```

#### Description
Uint are initialized by default with Zero and this costs less than initialize manually those variables.

[StrategyManager.sol#L724](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L724)

```
- uint256 j = 0;
+ uint256 j;
```

#### Test gas profile

```
forge snapshot
```

```diff
|   Test Name                                                                       | Gas     |
|-----------------------------------------------------------------------------------|---------|
-| testDelegateToBySignature_WithContractWallet_BadSignature(address,uint96,uint96) | 1041072 |
+| testDelegateToBySignature_WithContractWallet_BadSignature(address,uint96,uint96) | 1041067 |
|-----------------------------------------------------------------------------------|---------|
|                                                                                   |      -5 |

The average saved gas is 5.
```
                

```diff
|   Test Name                                                                            | Gas   |
|----------------------------------------------------------------------------------------|-------|
-| testDecreaseDelegatedSharesFromNonStrategyManagerAddress(address,address[],uint256[]) | 83134 |
+| testDecreaseDelegatedSharesFromNonStrategyManagerAddress(address,address[],uint256[]) | 82754 |
|----------------------------------------------------------------------------------------|-------|
|                                                                                        |  -380 |

The average saved gas is 380.
```
                

| Test                | Name                                                                                  | Gas   |
|---------------------|---------------------------------------------------------------------------------------|-------|
| DelegationTests     | testDelegateToBySignature_WithContractWallet_BadSignature(address,uint96,uint96)      | -5    |
| DelegationUnitTests | testDecreaseDelegatedSharesFromNonStrategyManagerAddress(address,address[],uint256[]) | -380  |
| --                  | --                                                                                    |       |
| Total               |                                                                                       | -385  |


## GAS-05 Avoid to check boolean variables

### Founded in [EigenPod.sol](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPod.sol)


[EigenPod.sol#L218-L220](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPod.sol#L218-L220)

```
if (!hasRestaked) {
    hasRestaked = true;
}
```

#### Description
if hasRestaked wasn't setted, it becames true.
if hasRestaked is false, it becames true.
if hasRestaked is true, it remains true.

So, you could avoid to check its value.

[EigenPod.sol#L218-L220](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPod.sol#L218-L220)

```diff
-if (!hasRestaked) {
    hasRestaked = true;
-}
```

#### Test gas profile

```
forge snapshot
```


```diff
|   Test Name                                                                       | Gas     |
|-----------------------------------------------------------------------------------|---------|
-| testDelegateToBySignature_WithContractWallet_BadSignature(address,uint96,uint96) | 1041072 |
+| testDelegateToBySignature_WithContractWallet_BadSignature(address,uint96,uint96) | 1041077 |
|-----------------------------------------------------------------------------------|---------|
|                                                                                   |       5 |

The average lost gas is 5.
```
                

```diff
|   Test Name                                                                            | Gas   |
|----------------------------------------------------------------------------------------|-------|
-| testDecreaseDelegatedSharesFromNonStrategyManagerAddress(address,address[],uint256[]) | 83134 |
+| testDecreaseDelegatedSharesFromNonStrategyManagerAddress(address,address[],uint256[]) | 82754 |
|----------------------------------------------------------------------------------------|-------|
|                                                                                        |  -380 |

The average saved gas is 380.
```
                

```diff
|   Test Name                    | Gas      |
|--------------------------------|----------|
-| testForkMainnetDepositSteth() | 20748060 |
+| testForkMainnetDepositSteth() | 20743049 |
|--------------------------------|----------|
|                                |    -5011 |

The saved gas is 5011.
```
                

```diff
|   Test Name                                                   | Gas      |
|---------------------------------------------------------------|----------|
-| testAttemptedWithdrawalAfterVerifyingWithdrawalCredentials() | 13924138 |
+| testAttemptedWithdrawalAfterVerifyingWithdrawalCredentials() | 13923998 |
|---------------------------------------------------------------|----------|
|                                                               |     -140 |

The saved gas is 140.
```
                

```diff
|   Test Name                       | Gas      |
|-----------------------------------|----------|
-| testDeployAndVerifyNewEigenPod() | 13918016 |
+| testDeployAndVerifyNewEigenPod() | 13917876 |
|-----------------------------------|----------|
|                                   |     -140 |

The saved gas is 140.
```
                

```diff
|   Test Name                                 | Gas      |
|---------------------------------------------|----------|
-| testDeployNewEigenPodWithActiveValidator() | 24054672 |
+| testDeployNewEigenPodWithActiveValidator() | 24054532 |
|---------------------------------------------|----------|
|                                             |     -140 |

The saved gas is 140.
```
                

```diff
|   Test Name                 | Gas      |
|-----------------------------|----------|
-| testDoubleFullWithdrawal() | 45905901 |
+| testDoubleFullWithdrawal() | 45905761 |
|-----------------------------|----------|
|                             |     -140 |

The saved gas is 140.
```
                

```diff
|   Test Name               | Gas      |
|---------------------------|----------|
-| testFullWithdrawalFlow() | 29372354 |
+| testFullWithdrawalFlow() | 29372214 |
|---------------------------|----------|
|                           |     -140 |

The saved gas is 140.
```
                

```diff
|   Test Name                  | Gas      |
|------------------------------|----------|
-| testPartialWithdrawalFlow() | 29533623 |
+| testPartialWithdrawalFlow() | 29533483 |
|------------------------------|----------|
|                              |     -140 |

The saved gas is 140.
```
                

```diff
|   Test Name                                       | Gas      |
|---------------------------------------------------|----------|
-| testProveOverComittedStakeOnWithdrawnValidator() | 24163595 |
+| testProveOverComittedStakeOnWithdrawnValidator() | 24163455 |
|---------------------------------------------------|----------|
|                                                   |     -140 |

The saved gas is 140.
```
                

```diff
|   Test Name                      | Gas      |
|----------------------------------|----------|
-| testProveOverCommittedBalance() | 24453410 |
+| testProveOverCommittedBalance() | 24453270 |
|----------------------------------|----------|
|                                  |     -140 |

The saved gas is 140.
```
                

```diff
|   Test Name                            | Gas      |
|----------------------------------------|----------|
-| testProveSingleWithdrawalCredential() | 14015510 |
+| testProveSingleWithdrawalCredential() | 14015370 |
|----------------------------------------|----------|
|                                        |     -140 |

The saved gas is 140.
```
                

```diff
|   Test Name                                  | Gas      |
|----------------------------------------------|----------|
-| testProvingMultipleWithdrawalsForSameSlot() | 46099773 |
+| testProvingMultipleWithdrawalsForSameSlot() | 46099633 |
|----------------------------------------------|----------|
|                                              |     -140 |

The saved gas is 140.
```
                

```diff
|   Test Name                       | Gas      |
|-----------------------------------|----------|
-| testUpdateSlashedBeaconBalance() | 24327399 |
+| testUpdateSlashedBeaconBalance() | 24327259 |
|-----------------------------------|----------|
|                                   |     -140 |

The saved gas is 140.
```
                

```diff
|   Test Name                                      | Gas      |
|--------------------------------------------------|----------|
-| testVerifyOvercommittedStakeRevertsWhenPaused() | 24379033 |
+| testVerifyOvercommittedStakeRevertsWhenPaused() | 24378893 |
|--------------------------------------------------|----------|
|                                                  |     -140 |

The saved gas is 140.
```
                

```diff
|   Test Name                                  | Gas      |
|----------------------------------------------|----------|
-| testWithdrawBeforeRestakingAfterRestaking() | 13902091 |
+| testWithdrawBeforeRestakingAfterRestaking() | 13901951 |
|----------------------------------------------|----------|
|                                              |     -140 |

The saved gas is 140.
```
                

| Test                 | Name                                                                                  | Gas   |
|----------------------|---------------------------------------------------------------------------------------|-------|
| DelegationTests      | testDelegateToBySignature_WithContractWallet_BadSignature(address,uint96,uint96)      | 5     |
| DelegationUnitTests  | testDecreaseDelegatedSharesFromNonStrategyManagerAddress(address,address[],uint256[]) | -380  |
| DepositWithdrawTests | testForkMainnetDepositSteth()                                                         | -5011 |
| EigenPodTests        | testAttemptedWithdrawalAfterVerifyingWithdrawalCredentials()                          | -140  |
| EigenPodTests        | testDeployAndVerifyNewEigenPod()                                                      | -140  |
| EigenPodTests        | testDeployNewEigenPodWithActiveValidator()                                            | -140  |
| EigenPodTests        | testDoubleFullWithdrawal()                                                            | -140  |
| EigenPodTests        | testFullWithdrawalFlow()                                                              | -140  |
| EigenPodTests        | testPartialWithdrawalFlow()                                                           | -140  |
| EigenPodTests        | testProveOverComittedStakeOnWithdrawnValidator()                                      | -140  |
| EigenPodTests        | testProveOverCommittedBalance()                                                       | -140  |
| EigenPodTests        | testProveSingleWithdrawalCredential()                                                 | -140  |
| EigenPodTests        | testProvingMultipleWithdrawalsForSameSlot()                                           | -140  |
| EigenPodTests        | testUpdateSlashedBeaconBalance()                                                      | -140  |
| EigenPodTests        | testVerifyOvercommittedStakeRevertsWhenPaused()                                       | -140  |
| EigenPodTests        | testWithdrawBeforeRestakingAfterRestaking()                                           | -140  |
| --                   | --                                                                                    |       |
| Total                |                                                                                       | -7206 |

## GAS-06 - Avoid to define variable which are used once

### Founded in [EigenPod.sol](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPod.sol)


[EigenPod.sol#L269-L270](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPod.sol#L269-L270)

```
uint64 slashedStatus = Endian.fromLittleEndianUint64(validatorFields[BeaconChainProofs.VALIDATOR_SLASHED_INDEX]);
require(slashedStatus == 1, "EigenPod.verifyOvercommittedStake: Validator must be slashed to be overcommitted");
```

#### Description
Avoid to declare variable if you use it once.

[EigenPod.sol#L218-L220](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPod.sol#L218-L220)

```diff
- uint64 slashedStatus = Endian.fromLittleEndianUint64(validatorFields[BeaconChainProofs.VALIDATOR_SLASHED_INDEX]);
- require(slashedStatus == 1, "EigenPod.verifyOvercommittedStake: Validator must be slashed to be overcommitted");
+ require(
+    Endian.fromLittleEndianUint64(validatorFields[BeaconChainProofs.VALIDATOR_SLASHED_INDEX]) == 1,
+    "EigenPod.verifyOvercommittedStake: Validator must be slashed to be overcommitted");

```

#### Test gas profile

```
forge snapshot
```

```diff
|   Test Name                                                                       | Gas     |
|-----------------------------------------------------------------------------------|---------|
-| testDelegateToBySignature_WithContractWallet_BadSignature(address,uint96,uint96) | 1041072 |
+| testDelegateToBySignature_WithContractWallet_BadSignature(address,uint96,uint96) | 1041077 |
|-----------------------------------------------------------------------------------|---------|
|                                                                                   |       5 |

The average lost gas is 5.
```
                

```diff
|   Test Name                    | Gas      |
|--------------------------------|----------|
-| testForkMainnetDepositSteth() | 20748060 |
+| testForkMainnetDepositSteth() | 20746660 |
|--------------------------------|----------|
|                                |    -1400 |

The saved gas is 1400.
```
                

```diff
|   Test Name                                       | Gas      |
|---------------------------------------------------|----------|
-| testProveOverComittedStakeOnWithdrawnValidator() | 24163595 |
+| testProveOverComittedStakeOnWithdrawnValidator() | 24163584 |
|---------------------------------------------------|----------|
|                                                   |      -11 |

The saved gas is 11.
```
                

```diff
|   Test Name                                                                       | Gas     |
|-----------------------------------------------------------------------------------|---------|
-| testDelegateToBySignature_WithContractWallet_Successfully(address,uint96,uint96) | 1126154 |
+| testDelegateToBySignature_WithContractWallet_Successfully(address,uint96,uint96) | 1126164 |
|-----------------------------------------------------------------------------------|---------|
|                                                                                   |      10 |

The average lost gas is 10.
```
                

| Test                 | Name                                                                             | Gas   |
|----------------------|----------------------------------------------------------------------------------|-------|
| DelegationTests      | testDelegateToBySignature_WithContractWallet_BadSignature(address,uint96,uint96) | 5     |
| DepositWithdrawTests | testForkMainnetDepositSteth()                                                    | -1400 |
| EigenPodTests        | testProveOverComittedStakeOnWithdrawnValidator()                                 | -11   |
| WithdrawalTests      | testDelegateToBySignature_WithContractWallet_Successfully(address,uint96,uint96) | 10    |
| --                   | --                                                                               |       |
| Total                |                                                                                  | -1396 |

## GAS-07 - Use alternative formulation in order to avoid NOT using AND instead of OR or vice versa

### Founded in [DelayedWithdrawalRouter.sol](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/DelayedWithdrawalRouter.sol)


[DelayedWithdrawalRouter.sol#L142](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/DelayedWithdrawalRouter.sol#L142)

```
while (i < maxNumberOfDelayedWithdrawalsToClaim && (delayedWithdrawalsCompletedBefore + i) < _userWithdrawalsLength) {

```

#### Description
It's possible to change boolean formula in order to use AND instead of OR.
This should save gas.

[DelayedWithdrawalRouter.sol#L142](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/DelayedWithdrawalRouter.sol#L142)

```diff
- while (i < maxNumberOfDelayedWithdrawalsToClaim && (delayedWithdrawalsCompletedBefore + i) < _userWithdrawalsLength) {
+ while (!(i >= maxNumberOfDelayedWithdrawalsToClaim || (delayedWithdrawalsCompletedBefore + i) >= _userWithdrawalsLength)) {
```

### Proof of concept

In general

#### Two variables

```
(a || b) = !(!(a || b)) = !(!a && !b)
```

| a  | b  | (a or b)  | !a and !b  | !(!a and !b)|
|:--:|:--:|:---------:|:----------:|:------------:|
| 0  | 0  | 0         | 1          | 0            |
| 0  | 1  | 1         | 0          | 1            |
| 1  | 0  | 1         | 0          | 1            |
| 1  | 1  | 1         | 0          | 1            |


#### Three variables

```
(a || b || c) = !(!(a || b || c)) = !(!a && !b && !c)
```

| a  | b  | c | (a or b or c)  | !a and !b and !c  | !(!a and !b and !c)|
|:--:|:--:|:-:|:--------------:|:-----------------:|:------------------:|
| 0  | 0  | 0 | 0              | 1                 | 0                  |
| 0  | 0  | 1 | 1              | 0                 | 1                  |
| 0  | 1  | 0 | 1              | 0                 | 1                  |
| 0  | 1  | 1 | 1              | 0                 | 1                  |
| 1  | 0  | 0 | 1              | 0                 | 1                  |
| 1  | 0  | 1 | 1              | 0                 | 1                  |
| 1  | 1  | 0 | 1              | 0                 | 1                  |
| 1  | 1  | 1 | 1              | 0                 | 1                  |

#### Test gas profile

```
forge snapshot
```

```diff
|   Test Name                                                   | Gas    |
|---------------------------------------------------------------|--------|
-| testClaimDelayedWithdrawalsSomeUnclaimable(uint8,uint8,bool) | 482688 |
+| testClaimDelayedWithdrawalsSomeUnclaimable(uint8,uint8,bool) | 482204 |
|---------------------------------------------------------------|--------|
|                                                               |   -484 |

The average saved gas is 484.
```
                

```diff
|   Test Name                                                                  | Gas    |
|------------------------------------------------------------------------------|--------|
-| testClaimDelayedWithdrawals_NonzeroToClaim_AttemptToClaimZero(uint256,bool) | 279655 |
+| testClaimDelayedWithdrawals_NonzeroToClaim_AttemptToClaimZero(uint256,bool) | 279652 |
|------------------------------------------------------------------------------|--------|
|                                                                              |     -3 |

The average saved gas is 3.
```
                

```diff
|   Test Name                                                      | Gas    |
|------------------------------------------------------------------|--------|
-| testClaimDelayedWithdrawals_RevertsOnAttemptingReentrancy(bool) | 286788 |
+| testClaimDelayedWithdrawals_RevertsOnAttemptingReentrancy(bool) | 286785 |
|------------------------------------------------------------------|--------|
|                                                                  |     -3 |

The average saved gas is 3.
```
                

```diff
|   Test Name                                                                       | Gas     |
|-----------------------------------------------------------------------------------|---------|
-| testDelegateToBySignature_WithContractWallet_BadSignature(address,uint96,uint96) | 1041072 |
+| testDelegateToBySignature_WithContractWallet_BadSignature(address,uint96,uint96) | 1041077 |
|-----------------------------------------------------------------------------------|---------|
|                                                                                   |       5 |

The average lost gas is 5.
```
                

```diff
|   Test Name                 | Gas      |
|-----------------------------|----------|
-| testDoubleFullWithdrawal() | 45905901 |
+| testDoubleFullWithdrawal() | 45905898 |
|-----------------------------|----------|
|                             |       -3 |

The saved gas is 3.
```
                

```diff
|   Test Name               | Gas      |
|---------------------------|----------|
-| testFullWithdrawalFlow() | 29372354 |
+| testFullWithdrawalFlow() | 29372351 |
|---------------------------|----------|
|                           |       -3 |

The saved gas is 3.
```
                

```diff
|   Test Name                  | Gas      |
|------------------------------|----------|
-| testPartialWithdrawalFlow() | 29533623 |
+| testPartialWithdrawalFlow() | 29533620 |
|------------------------------|----------|
|                              |       -3 |

The saved gas is 3.
```
                

```diff
|   Test Name                                  | Gas      |
|----------------------------------------------|----------|
-| testProvingMultipleWithdrawalsForSameSlot() | 46099773 |
+| testProvingMultipleWithdrawalsForSameSlot() | 46099770 |
|----------------------------------------------|----------|
|                                              |       -3 |

The saved gas is 3.
```
                

| Test                             | Name                                                                             | Gas   |
|----------------------------------|----------------------------------------------------------------------------------|-------|
| DelayedWithdrawalRouterUnitTests | testClaimDelayedWithdrawalsSomeUnclaimable(uint8,uint8,bool)                     | -484  |
| DelayedWithdrawalRouterUnitTests | testClaimDelayedWithdrawals_NonzeroToClaim_AttemptToClaimZero(uint256,bool)      | -3    |
| DelayedWithdrawalRouterUnitTests | testClaimDelayedWithdrawals_RevertsOnAttemptingReentrancy(bool)                  | -3    |
| DelegationTests                  | testDelegateToBySignature_WithContractWallet_BadSignature(address,uint96,uint96) | 5     |
| EigenPodTests                    | testDoubleFullWithdrawal()                                                       | -3    |
| EigenPodTests                    | testFullWithdrawalFlow()                                                         | -3    |
| EigenPodTests                    | testPartialWithdrawalFlow()                                                      | -3    |
| EigenPodTests                    | testProvingMultipleWithdrawalsForSameSlot()                                      | -3    |
| --                               | --                                                                               |       |
| Total                            |                                                                                  | -497  |



## GAS-08 - 2^n can computed with shift

### Founded in [BeaconChainProofs.sol](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/libraries/BeaconChainProofs.sol)


[BeaconChainProofs.sol#L131](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/libraries/BeaconChainProofs.sol#L131)

```
bytes32[] memory paddedHeaderFields = new bytes32[](2**BEACON_BLOCK_HEADER_FIELD_TREE_HEIGHT);
```

[BeaconChainProofs.sol#L141](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/libraries/BeaconChainProofs.sol#L141)

```
bytes32[] memory paddedBeaconStateFields = new bytes32[](2**BEACON_STATE_FIELD_TREE_HEIGHT);
```

[BeaconChainProofs.sol#L151](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/libraries/BeaconChainProofs.sol#L151)

```
bytes32[] memory paddedValidatorFields = new bytes32[](2**VALIDATOR_FIELD_TREE_HEIGHT);
```

[BeaconChainProofs.sol#L161](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/libraries/BeaconChainProofs.sol#L161)

```
bytes32[] memory paddedEth1DataFields = new bytes32[](2**ETH1_DATA_FIELD_TREE_HEIGHT);
```

[BeaconChainProofs.sol#L199](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/libraries/BeaconChainProofs.sol#L199)

```
require(validatorFields.length == 2**VALIDATOR_FIELD_TREE_HEIGHT, "BeaconChainProofs.verifyValidatorFields: Validator fields has incorrect length");
```

[BeaconChainProofs.sol#L250-L253](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/libraries/BeaconChainProofs.sol#L250-L253)

```
require(withdrawalFields.length == 2**WITHDRAWAL_FIELD_TREE_HEIGHT, "BeaconChainProofs.verifyWithdrawalProofs: withdrawalFields has incorrect length");

require(proofs.blockHeaderRootIndex < 2**BLOCK_ROOTS_TREE_HEIGHT, "BeaconChainProofs.verifyWithdrawalProofs: blockRootIndex is too large");
require(proofs.withdrawalIndex < 2**WITHDRAWALS_TREE_HEIGHT, "BeaconChainProofs.verifyWithdrawalProofs: withdrawalIndex is too large");
```

#### Description
Using shift instead of power saves gas.
2^n is same of 1 << n

[BeaconChainProofs.sol#L131](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/libraries/BeaconChainProofs.sol#L131)

```diff
- bytes32[] memory paddedHeaderFields = new bytes32[](2**BEACON_BLOCK_HEADER_FIELD_TREE_HEIGHT);
+ bytes32[] memory paddedHeaderFields = new bytes32[](1 << BEACON_BLOCK_HEADER_FIELD_TREE_HEIGHT);
```

[BeaconChainProofs.sol#L141](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/libraries/BeaconChainProofs.sol#L141)

```diff
- bytes32[] memory paddedBeaconStateFields = new bytes32[](2**BEACON_STATE_FIELD_TREE_HEIGHT);
+ bytes32[] memory paddedBeaconStateFields = new bytes32[](1 << BEACON_STATE_FIELD_TREE_HEIGHT);
```

[BeaconChainProofs.sol#L151](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/libraries/BeaconChainProofs.sol#L151)

```diff
- bytes32[] memory paddedValidatorFields = new bytes32[](2**VALIDATOR_FIELD_TREE_HEIGHT);
+ bytes32[] memory paddedValidatorFields = new bytes32[](1 << VALIDATOR_FIELD_TREE_HEIGHT);
```

[BeaconChainProofs.sol#L161](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/libraries/BeaconChainProofs.sol#L161)

```diff
- bytes32[] memory paddedEth1DataFields = new bytes32[](2**ETH1_DATA_FIELD_TREE_HEIGHT);
+ bytes32[] memory paddedEth1DataFields = new bytes32[](1<<ETH1_DATA_FIELD_TREE_HEIGHT);
```

[BeaconChainProofs.sol#L199](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/libraries/BeaconChainProofs.sol#L199)

```diff
- require(validatorFields.length == 2**VALIDATOR_FIELD_TREE_HEIGHT, "BeaconChainProofs.verifyValidatorFields: Validator fields has incorrect length");
+ require(validatorFields.length == 1 << VALIDATOR_FIELD_TREE_HEIGHT, "BeaconChainProofs.verifyValidatorFields: Validator fields has incorrect length");
```

[BeaconChainProofs.sol#L250-L253](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/libraries/BeaconChainProofs.sol#L250-L253)

```diff
- require(withdrawalFields.length == 2**WITHDRAWAL_FIELD_TREE_HEIGHT, "BeaconChainProofs.verifyWithdrawalProofs: withdrawalFields has incorrect length");

- require(proofs.blockHeaderRootIndex < 2**BLOCK_ROOTS_TREE_HEIGHT, "BeaconChainProofs.verifyWithdrawalProofs: blockRootIndex is too large");
- require(proofs.withdrawalIndex < 2**WITHDRAWALS_TREE_HEIGHT, "BeaconChainProofs.verifyWithdrawalProofs: withdrawalIndex is too large");
+ require(withdrawalFields.length == 1 << WITHDRAWAL_FIELD_TREE_HEIGHT, "BeaconChainProofs.verifyWithdrawalProofs: withdrawalFields has incorrect length");

+ require(proofs.blockHeaderRootIndex < 1 << BLOCK_ROOTS_TREE_HEIGHT, "BeaconChainProofs.verifyWithdrawalProofs: blockRootIndex is too large");
+ require(proofs.withdrawalIndex < 1 << WITHDRAWALS_TREE_HEIGHT, "BeaconChainProofs.verifyWithdrawalProofs: withdrawalIndex is too large");
```

#### Test gas profile

```
forge snapshot
```
```diff
|   Test Name                                                   | Gas    |
|---------------------------------------------------------------|--------|
-| testClaimDelayedWithdrawalsSomeUnclaimable(uint8,uint8,bool) | 482688 |
+| testClaimDelayedWithdrawalsSomeUnclaimable(uint8,uint8,bool) | 483103 |
|---------------------------------------------------------------|--------|
|                                                               |    415 |

The average lost gas is 415.
```
                

```diff
|   Test Name                                                                       | Gas     |
|-----------------------------------------------------------------------------------|---------|
-| testDelegateToBySignature_WithContractWallet_BadSignature(address,uint96,uint96) | 1041072 |
+| testDelegateToBySignature_WithContractWallet_BadSignature(address,uint96,uint96) | 1041077 |
|-----------------------------------------------------------------------------------|---------|
|                                                                                   |       5 |

The average lost gas is 5.
```
                

```diff
|   Test Name                                                                            | Gas   |
|----------------------------------------------------------------------------------------|-------|
-| testDecreaseDelegatedSharesFromNonStrategyManagerAddress(address,address[],uint256[]) | 83134 |
+| testDecreaseDelegatedSharesFromNonStrategyManagerAddress(address,address[],uint256[]) | 82754 |
|----------------------------------------------------------------------------------------|-------|
|                                                                                        |  -380 |

The average saved gas is 380.
```
                

```diff
|   Test Name                    | Gas      |
|--------------------------------|----------|
-| testForkMainnetDepositSteth() | 20748060 |
+| testForkMainnetDepositSteth() | 20691963 |
|--------------------------------|----------|
|                                |   -56097 |

The saved gas is 56097.
```
                

```diff
|   Test Name                                                   | Gas      |
|---------------------------------------------------------------|----------|
-| testAttemptedWithdrawalAfterVerifyingWithdrawalCredentials() | 13924138 |
+| testAttemptedWithdrawalAfterVerifyingWithdrawalCredentials() | 13923916 |
|---------------------------------------------------------------|----------|
|                                                               |     -222 |

The saved gas is 222.
```
                

```diff
|   Test Name                       | Gas      |
|-----------------------------------|----------|
-| testDeployAndVerifyNewEigenPod() | 13918016 |
+| testDeployAndVerifyNewEigenPod() | 13917794 |
|-----------------------------------|----------|
|                                   |     -222 |

The saved gas is 222.
```
                

```diff
|   Test Name                                 | Gas      |
|---------------------------------------------|----------|
-| testDeployNewEigenPodWithActiveValidator() | 24054672 |
+| testDeployNewEigenPodWithActiveValidator() | 24054450 |
|---------------------------------------------|----------|
|                                             |     -222 |

The saved gas is 222.
```
                

```diff
|   Test Name                 | Gas      |
|-----------------------------|----------|
-| testDoubleFullWithdrawal() | 45905901 |
+| testDoubleFullWithdrawal() | 45903935 |
|-----------------------------|----------|
|                             |    -1966 |

The saved gas is 1966.
```
                

```diff
|   Test Name               | Gas      |
|---------------------------|----------|
-| testFullWithdrawalFlow() | 29372354 |
+| testFullWithdrawalFlow() | 29371260 |
|---------------------------|----------|
|                           |    -1094 |

The saved gas is 1094.
```
                

```diff
|   Test Name                | Gas      |
|----------------------------|----------|
-| testFullWithdrawalProof() | 13943822 |
+| testFullWithdrawalProof() | 13889090 |
|----------------------------|----------|
|                            |   -54732 |

The saved gas is 54732.
```
                

```diff
|   Test Name                  | Gas      |
|------------------------------|----------|
-| testPartialWithdrawalFlow() | 29533623 |
+| testPartialWithdrawalFlow() | 29532529 |
|------------------------------|----------|
|                              |    -1094 |

The saved gas is 1094.
```
                

```diff
|   Test Name                                       | Gas      |
|---------------------------------------------------|----------|
-| testProveOverComittedStakeOnWithdrawnValidator() | 24163595 |
+| testProveOverComittedStakeOnWithdrawnValidator() | 24163369 |
|---------------------------------------------------|----------|
|                                                   |     -226 |

The saved gas is 226.
```
                

```diff
|   Test Name                      | Gas      |
|----------------------------------|----------|
-| testProveOverCommittedBalance() | 24453410 |
+| testProveOverCommittedBalance() | 24453184 |
|----------------------------------|----------|
|                                  |     -226 |

The saved gas is 226.
```
                

```diff
|   Test Name                            | Gas      |
|----------------------------------------|----------|
-| testProveSingleWithdrawalCredential() | 14015510 |
+| testProveSingleWithdrawalCredential() | 14015288 |
|----------------------------------------|----------|
|                                        |     -222 |

The saved gas is 222.
```
                

```diff
|   Test Name                                  | Gas      |
|----------------------------------------------|----------|
-| testProvingMultipleWithdrawalsForSameSlot() | 46099773 |
+| testProvingMultipleWithdrawalsForSameSlot() | 46097807 |
|----------------------------------------------|----------|
|                                              |    -1966 |

The saved gas is 1966.
```
                

```diff
|   Test Name                       | Gas      |
|-----------------------------------|----------|
-| testUpdateSlashedBeaconBalance() | 24327399 |
+| testUpdateSlashedBeaconBalance() | 24327173 |
|-----------------------------------|----------|
|                                   |     -226 |

The saved gas is 226.
```
                

```diff
|   Test Name                                      | Gas      |
|--------------------------------------------------|----------|
-| testVerifyOvercommittedStakeRevertsWhenPaused() | 24379033 |
+| testVerifyOvercommittedStakeRevertsWhenPaused() | 24378811 |
|--------------------------------------------------|----------|
|                                                  |     -222 |

The saved gas is 222.
```
                

```diff
|   Test Name                                             | Gas      |
|---------------------------------------------------------|----------|
-| testVerifyWithdrawalCredentialsWithInadequateBalance() | 13674667 |
+| testVerifyWithdrawalCredentialsWithInadequateBalance() | 13674663 |
|---------------------------------------------------------|----------|
|                                                         |       -4 |

The saved gas is 4.
```
                

```diff
|   Test Name                                  | Gas      |
|----------------------------------------------|----------|
-| testWithdrawBeforeRestakingAfterRestaking() | 13902091 |
+| testWithdrawBeforeRestakingAfterRestaking() | 13901869 |
|----------------------------------------------|----------|
|                                              |     -222 |

The saved gas is 222.
```
                

```diff
|   Test Name                           | Gas    |
|---------------------------------------|--------|
-| testResetFrozenStatus(uint8,uint256) | 178413 |
+| testResetFrozenStatus(uint8,uint256) | 166845 |
|---------------------------------------|--------|
|                                       | -11568 |

The average saved gas is 11568.
```
                

```diff
|   Test Name                                                                            | Gas    |
|----------------------------------------------------------------------------------------|--------|
-| testDepositIntoStrategyWithSignature_WithContractWallet_Successfully(uint256,uint256) | 666644 |
+| testDepositIntoStrategyWithSignature_WithContractWallet_Successfully(uint256,uint256) | 666664 |
|----------------------------------------------------------------------------------------|--------|
|                                                                                        |     20 |

The average lost gas is 20.
```
                

```diff
|   Test Name                                                                       | Gas     |
|-----------------------------------------------------------------------------------|---------|
-| testDelegateToBySignature_WithContractWallet_BadSignature(address,uint96,uint96) | 1041397 |
+| testDelegateToBySignature_WithContractWallet_BadSignature(address,uint96,uint96) | 1041392 |
|-----------------------------------------------------------------------------------|---------|
|                                                                                   |      -5 |

The average saved gas is 5.
```
                

```diff
|   Test Name                                                                       | Gas     |
|-----------------------------------------------------------------------------------|---------|
-| testDelegateToBySignature_WithContractWallet_Successfully(address,uint96,uint96) | 1126154 |
+| testDelegateToBySignature_WithContractWallet_Successfully(address,uint96,uint96) | 1126174 |
|-----------------------------------------------------------------------------------|---------|
|                                                                                   |      20 |

The average lost gas is 20.
```
                

| Test                             | Name                                                                                  | Gas     |
|----------------------------------|---------------------------------------------------------------------------------------|---------|
| DelayedWithdrawalRouterUnitTests | testClaimDelayedWithdrawalsSomeUnclaimable(uint8,uint8,bool)                          | 415     |
| DelegationTests                  | testDelegateToBySignature_WithContractWallet_BadSignature(address,uint96,uint96)      | 5       |
| DelegationUnitTests              | testDecreaseDelegatedSharesFromNonStrategyManagerAddress(address,address[],uint256[]) | -380    |
| DepositWithdrawTests             | testForkMainnetDepositSteth()                                                         | -56097  |
| EigenPodTests                    | testAttemptedWithdrawalAfterVerifyingWithdrawalCredentials()                          | -222    |
| EigenPodTests                    | testDeployAndVerifyNewEigenPod()                                                      | -222    |
| EigenPodTests                    | testDeployNewEigenPodWithActiveValidator()                                            | -222    |
| EigenPodTests                    | testDoubleFullWithdrawal()                                                            | -1966   |
| EigenPodTests                    | testFullWithdrawalFlow()                                                              | -1094   |
| EigenPodTests                    | testFullWithdrawalProof()                                                             | -54732  |
| EigenPodTests                    | testPartialWithdrawalFlow()                                                           | -1094   |
| EigenPodTests                    | testProveOverComittedStakeOnWithdrawnValidator()                                      | -226    |
| EigenPodTests                    | testProveOverCommittedBalance()                                                       | -226    |
| EigenPodTests                    | testProveSingleWithdrawalCredential()                                                 | -222    |
| EigenPodTests                    | testProvingMultipleWithdrawalsForSameSlot()                                           | -1966   |
| EigenPodTests                    | testUpdateSlashedBeaconBalance()                                                      | -226    |
| EigenPodTests                    | testVerifyOvercommittedStakeRevertsWhenPaused()                                       | -222    |
| EigenPodTests                    | testVerifyWithdrawalCredentialsWithInadequateBalance()                                | -4      |
| EigenPodTests                    | testWithdrawBeforeRestakingAfterRestaking()                                           | -222    |
| SlasherUnitTests                 | testResetFrozenStatus(uint8,uint256)                                                  | -11568  |
| StrategyManagerUnitTests         | testDepositIntoStrategyWithSignature_WithContractWallet_Successfully(uint256,uint256) | 20      |
| WithdrawalTests                  | testDelegateToBySignature_WithContractWallet_BadSignature(address,uint96,uint96)      | -5      |
| WithdrawalTests                  | testDelegateToBySignature_WithContractWallet_Successfully(address,uint96,uint96)      | 20      |
| --                               | --                                                                                    |         |
| Total                            |                                                                                       | -130456 |




## GAS-09 - Using shift instead of multiplication or division by 2

### Founded in [Merkle.sol](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/libraries/Merkle.sol)


[Merkle.sol#L129-L153](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/libraries/Merkle.sol#L129-L153)

```
function merkleizeSha256(
    bytes32[] memory leaves
) internal pure returns (bytes32) {
    //there are half as many nodes in the layer above the leaves
    uint256 numNodesInLayer = leaves.length / 2;
    //create a layer to store the internal nodes
    bytes32[] memory layer = new bytes32[](numNodesInLayer);
    //fill the layer with the pairwise hashes of the leaves
    for (uint i = 0; i < numNodesInLayer; i++) {
        layer[i] = sha256(abi.encodePacked(leaves[2*i], leaves[2*i+1]));
    }
    //the next layer above has half as many nodes
    numNodesInLayer /= 2;
    //while we haven't computed the root
    while (numNodesInLayer != 0) {
        //overwrite the first numNodesInLayer nodes in layer with the pairwise hashes of their children
        for (uint i = 0; i < numNodesInLayer; i++) {
            layer[i] = sha256(abi.encodePacked(layer[2*i], layer[2*i+1]));
        }
        //the next layer above has half as many nodes
        numNodesInLayer /= 2;
    }
    //the first node in the layer is the root
    return layer[0];
}
```

#### Description
Using shift instead of multiplication saves gas.
n/2 is same of n >> 1
n*2 is same of n << 1

It was also reported in [Automated findings](https://gist.github.com/CloudEllie/213965a3448230f5b615e7046f9dd26d).
I only report other instaces


[Merkle.sol#L129-L153](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/libraries/Merkle.sol#L129-L153)

```diff
function merkleizeSha256(
    bytes32[] memory leaves
) internal pure returns (bytes32) {
    //there are half as many nodes in the layer above the leaves
-    uint256 numNodesInLayer = leaves.length / 2;
+    uint256 numNodesInLayer = leaves.length >> 1;
    //create a layer to store the internal nodes
    bytes32[] memory layer = new bytes32[](numNodesInLayer);
    //fill the layer with the pairwise hashes of the leaves
    for (uint i = 0; i < numNodesInLayer; i++) {
-        layer[i] = sha256(abi.encodePacked(leaves[2*i], leaves[2*i+1]));
+        layer[i] = sha256(abi.encodePacked(leaves[i << 1], leaves[(i<<1)+1]));
    }
    //the next layer above has half as many nodes
-    numNodesInLayer /= 2;
+    numNodesInLayer = numNodesInLayer >> 1;
    //while we haven't computed the root
    while (numNodesInLayer != 0) {
        //overwrite the first numNodesInLayer nodes in layer with the pairwise hashes of their children
        for (uint i = 0; i < numNodesInLayer; i++) {
-            layer[i] = sha256(abi.encodePacked(layer[2*i], layer[2*i+1]));
+            layer[i] = sha256(abi.encodePacked(layer[i << 1], layer[(i<<1)+1]));
        }
        //the next layer above has half as many nodes
-        numNodesInLayer /= 2;
+        numNodesInLayer = numNodesInLayer >> 1;
    }
    //the first node in the layer is the root
    return layer[0];
}
```

#### Test gas profile

```
forge snapshot
```
```diff
|   Test Name                                                   | Gas    |
|---------------------------------------------------------------|--------|
-| testClaimDelayedWithdrawalsSomeUnclaimable(uint8,uint8,bool) | 482688 |
+| testClaimDelayedWithdrawalsSomeUnclaimable(uint8,uint8,bool) | 483103 |
|---------------------------------------------------------------|--------|
|                                                               |    415 |

The average lost gas is 415.
```
                

```diff
|   Test Name                                                                       | Gas     |
|-----------------------------------------------------------------------------------|---------|
-| testDelegateToBySignature_WithContractWallet_BadSignature(address,uint96,uint96) | 1041072 |
+| testDelegateToBySignature_WithContractWallet_BadSignature(address,uint96,uint96) | 1041077 |
|-----------------------------------------------------------------------------------|---------|
|                                                                                   |       5 |

The average lost gas is 5.
```
                

```diff
|   Test Name                                                                       | Gas     |
|-----------------------------------------------------------------------------------|---------|
-| testDelegateToBySignature_WithContractWallet_Successfully(address,uint96,uint96) | 1125805 |
+| testDelegateToBySignature_WithContractWallet_Successfully(address,uint96,uint96) | 1125815 |
|-----------------------------------------------------------------------------------|---------|
|                                                                                   |      10 |

The average lost gas is 10.
```
                

```diff
|   Test Name                    | Gas      |
|--------------------------------|----------|
-| testForkMainnetDepositSteth() | 20748060 |
+| testForkMainnetDepositSteth() | 20738449 |
|--------------------------------|----------|
|                                |    -9611 |

The saved gas is 9611.
```
                

```diff
|   Test Name                                                   | Gas      |
|---------------------------------------------------------------|----------|
-| testAttemptedWithdrawalAfterVerifyingWithdrawalCredentials() | 13924138 |
+| testAttemptedWithdrawalAfterVerifyingWithdrawalCredentials() | 13922791 |
|---------------------------------------------------------------|----------|
|                                                               |    -1347 |

The saved gas is 1347.
```
                

```diff
|   Test Name                       | Gas      |
|-----------------------------------|----------|
-| testDeployAndVerifyNewEigenPod() | 13918016 |
+| testDeployAndVerifyNewEigenPod() | 13916669 |
|-----------------------------------|----------|
|                                   |    -1347 |

The saved gas is 1347.
```
                

```diff
|   Test Name                                 | Gas      |
|---------------------------------------------|----------|
-| testDeployNewEigenPodWithActiveValidator() | 24054672 |
+| testDeployNewEigenPodWithActiveValidator() | 24053325 |
|---------------------------------------------|----------|
|                                             |    -1347 |

The saved gas is 1347.
```
                

```diff
|   Test Name                 | Gas      |
|-----------------------------|----------|
-| testDoubleFullWithdrawal() | 45905901 |
+| testDoubleFullWithdrawal() | 45900566 |
|-----------------------------|----------|
|                             |    -5335 |

The saved gas is 5335.
```
                

```diff
|   Test Name               | Gas      |
|---------------------------|----------|
-| testFullWithdrawalFlow() | 29372354 |
+| testFullWithdrawalFlow() | 29369013 |
|---------------------------|----------|
|                           |    -3341 |

The saved gas is 3341.
```
                

```diff
|   Test Name                | Gas      |
|----------------------------|----------|
-| testFullWithdrawalProof() | 13943822 |
+| testFullWithdrawalProof() | 13927956 |
|----------------------------|----------|
|                            |   -15866 |

The saved gas is 15866.
```
                

```diff
|   Test Name                  | Gas      |
|------------------------------|----------|
-| testPartialWithdrawalFlow() | 29533623 |
+| testPartialWithdrawalFlow() | 29530282 |
|------------------------------|----------|
|                              |    -3341 |

The saved gas is 3341.
```
                

```diff
|   Test Name                                       | Gas      |
|---------------------------------------------------|----------|
-| testProveOverComittedStakeOnWithdrawnValidator() | 24163595 |
+| testProveOverComittedStakeOnWithdrawnValidator() | 24162248 |
|---------------------------------------------------|----------|
|                                                   |    -1347 |

The saved gas is 1347.
```
                

```diff
|   Test Name                      | Gas      |
|----------------------------------|----------|
-| testProveOverCommittedBalance() | 24453410 |
+| testProveOverCommittedBalance() | 24452063 |
|----------------------------------|----------|
|                                  |    -1347 |

The saved gas is 1347.
```
                

```diff
|   Test Name                            | Gas      |
|----------------------------------------|----------|
-| testProveSingleWithdrawalCredential() | 14015510 |
+| testProveSingleWithdrawalCredential() | 14014163 |
|----------------------------------------|----------|
|                                        |    -1347 |

The saved gas is 1347.
```
                

```diff
|   Test Name                                  | Gas      |
|----------------------------------------------|----------|
-| testProvingMultipleWithdrawalsForSameSlot() | 46099773 |
+| testProvingMultipleWithdrawalsForSameSlot() | 46094438 |
|----------------------------------------------|----------|
|                                              |    -5335 |

The saved gas is 5335.
```
                

```diff
|   Test Name                       | Gas      |
|-----------------------------------|----------|
-| testUpdateSlashedBeaconBalance() | 24327399 |
+| testUpdateSlashedBeaconBalance() | 24326052 |
|-----------------------------------|----------|
|                                   |    -1347 |

The saved gas is 1347.
```
                

```diff
|   Test Name                                      | Gas      |
|--------------------------------------------------|----------|
-| testVerifyOvercommittedStakeRevertsWhenPaused() | 24379033 |
+| testVerifyOvercommittedStakeRevertsWhenPaused() | 24377686 |
|--------------------------------------------------|----------|
|                                                  |    -1347 |

The saved gas is 1347.
```
                

```diff
|   Test Name                                  | Gas      |
|----------------------------------------------|----------|
-| testWithdrawBeforeRestakingAfterRestaking() | 13902091 |
+| testWithdrawBeforeRestakingAfterRestaking() | 13900744 |
|----------------------------------------------|----------|
|                                              |    -1347 |

The saved gas is 1347.
```
                

```diff
|   Test Name                                                                                    | Gas    |
|------------------------------------------------------------------------------------------------|--------|
-| testRecordStakeUpdate_MultipleLinkedListEntries(address,address,uint32,uint32,uint32,uint256) | 584630 |
+| testRecordStakeUpdate_MultipleLinkedListEntries(address,address,uint32,uint32,uint32,uint256) | 573446 |
|------------------------------------------------------------------------------------------------|--------|
|                                                                                                | -11184 |

The average saved gas is 11184.
```
                

```diff
|   Test Name                           | Gas    |
|---------------------------------------|--------|
-| testResetFrozenStatus(uint8,uint256) | 178413 |
+| testResetFrozenStatus(uint8,uint256) | 166845 |
|---------------------------------------|--------|
|                                       | -11568 |

The average saved gas is 11568.
```
                

```diff
|   Test Name                                                        | Gas    |
|--------------------------------------------------------------------|--------|
-| testDepositIntoStrategyWithSignatureSuccessfully(uint256,uint256) | 245473 |
+| testDepositIntoStrategyWithSignatureSuccessfully(uint256,uint256) | 245453 |
|--------------------------------------------------------------------|--------|
|                                                                    |    -20 |

The average saved gas is 20.
```
                

| Test                             | Name                                                                                          | Gas    |
|----------------------------------|-----------------------------------------------------------------------------------------------|--------|
| DelayedWithdrawalRouterUnitTests | testClaimDelayedWithdrawalsSomeUnclaimable(uint8,uint8,bool)                                  | 415    |
| DelegationTests                  | testDelegateToBySignature_WithContractWallet_BadSignature(address,uint96,uint96)              | 5      |
| DelegationTests                  | testDelegateToBySignature_WithContractWallet_Successfully(address,uint96,uint96)              | 10     |
| DepositWithdrawTests             | testForkMainnetDepositSteth()                                                                 | -9611  |
| EigenPodTests                    | testAttemptedWithdrawalAfterVerifyingWithdrawalCredentials()                                  | -1347  |
| EigenPodTests                    | testDeployAndVerifyNewEigenPod()                                                              | -1347  |
| EigenPodTests                    | testDeployNewEigenPodWithActiveValidator()                                                    | -1347  |
| EigenPodTests                    | testDoubleFullWithdrawal()                                                                    | -5335  |
| EigenPodTests                    | testFullWithdrawalFlow()                                                                      | -3341  |
| EigenPodTests                    | testFullWithdrawalProof()                                                                     | -15866 |
| EigenPodTests                    | testPartialWithdrawalFlow()                                                                   | -3341  |
| EigenPodTests                    | testProveOverComittedStakeOnWithdrawnValidator()                                              | -1347  |
| EigenPodTests                    | testProveOverCommittedBalance()                                                               | -1347  |
| EigenPodTests                    | testProveSingleWithdrawalCredential()                                                         | -1347  |
| EigenPodTests                    | testProvingMultipleWithdrawalsForSameSlot()                                                   | -5335  |
| EigenPodTests                    | testUpdateSlashedBeaconBalance()                                                              | -1347  |
| EigenPodTests                    | testVerifyOvercommittedStakeRevertsWhenPaused()                                               | -1347  |
| EigenPodTests                    | testWithdrawBeforeRestakingAfterRestaking()                                                   | -1347  |
| SlasherUnitTests                 | testRecordStakeUpdate_MultipleLinkedListEntries(address,address,uint32,uint32,uint32,uint256) | -11184 |
| SlasherUnitTests                 | testResetFrozenStatus(uint8,uint256)                                                          | -11568 |
| StrategyManagerUnitTests         | testDepositIntoStrategyWithSignatureSuccessfully(uint256,uint256)                             | -20    |
| --                               | --                                                                                            |        |
| Total                            |                                                                                               | -77294 |



## GAS-10 - Using unchecked shift instead of assembly div by 2

### Founded in [Merkle.sol](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/libraries/Merkle.sol)


[Merkle.sol#L99-L121](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/libraries/Merkle.sol#L99-L121)

```
function processInclusionProofSha256(bytes memory proof, bytes32 leaf, uint256 index) internal view returns (bytes32) {
    bytes32[1] memory computedHash = [leaf];
    for (uint256 i = 32; i <= proof.length; i+=32) {
        if(index % 2 == 0) {
            // if ith bit of index is 0, then computedHash is a left sibling
            assembly {
                mstore(0x00, mload(computedHash))
                mstore(0x20, mload(add(proof, i)))
                if iszero(staticcall(sub(gas(), 2000), 2, 0x00, 0x40, computedHash, 0x20)) {revert(0, 0)}
                index := div(index, 2)
            }
        } else {
            // if ith bit of index is 1, then computedHash is a right sibling
            assembly {
                mstore(0x00, mload(add(proof, i)))
                mstore(0x20, mload(computedHash))
                if iszero(staticcall(sub(gas(), 2000), 2, 0x00, 0x40, computedHash, 0x20)) {revert(0, 0)}
                index := div(index, 2)
            }            
        }
    }
    return computedHash[0];
}
```

#### Description
Using shift inside unchecked {} instead of assembly division saves gas.


[Merkle.sol#L99-L121](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/libraries/Merkle.sol#L99-L121)

```diff
function processInclusionProofSha256(bytes memory proof, bytes32 leaf, uint256 index) internal view returns (bytes32) {
    bytes32[1] memory computedHash = [leaf];
    for (uint256 i = 32; i <= proof.length; i+=32) {
        if(index % 2 == 0) {
            // if ith bit of index is 0, then computedHash is a left sibling
            assembly {
                mstore(0x00, mload(computedHash))
                mstore(0x20, mload(add(proof, i)))
                if iszero(staticcall(sub(gas(), 2000), 2, 0x00, 0x40, computedHash, 0x20)) {revert(0, 0)}
-               index := div(index, 2)
            }
        } else {
            // if ith bit of index is 1, then computedHash is a right sibling
            assembly {
                mstore(0x00, mload(add(proof, i)))
                mstore(0x20, mload(computedHash))
                if iszero(staticcall(sub(gas(), 2000), 2, 0x00, 0x40, computedHash, 0x20)) {revert(0, 0)}
-               index := div(index, 2)
            }            
        }
+       unchecked{
+           index = index >> 1;
+       }
    }
    return computedHash[0];
}
```

#### Test gas profile

```
forge snapshot
```

```diff
|   Test Name                                                    | Gas    |
|----------------------------------------------------------------|--------|
-| testClaimDelayedWithdrawals(uint8,uint8,uint256,address,bool) | 334768 |
+| testClaimDelayedWithdrawals(uint8,uint8,uint256,address,bool) | 334734 |
|----------------------------------------------------------------|--------|
|                                                                |    -34 |

The average saved gas is 34.
```
                

```diff
|   Test Name                                                                            | Gas   |
|----------------------------------------------------------------------------------------|-------|
-| testDecreaseDelegatedSharesFromNonStrategyManagerAddress(address,address[],uint256[]) | 83134 |
+| testDecreaseDelegatedSharesFromNonStrategyManagerAddress(address,address[],uint256[]) | 83466 |
|----------------------------------------------------------------------------------------|-------|
|                                                                                        |   332 |

The average lost gas is 332.
```
                

```diff
|   Test Name                    | Gas      |
|--------------------------------|----------|
-| testForkMainnetDepositSteth() | 20748060 |
+| testForkMainnetDepositSteth() | 20746860 |
|--------------------------------|----------|
|                                |    -1200 |

The saved gas is 1200.
```
                

```diff
|   Test Name                                                   | Gas      |
|---------------------------------------------------------------|----------|
-| testAttemptedWithdrawalAfterVerifyingWithdrawalCredentials() | 13924138 |
+| testAttemptedWithdrawalAfterVerifyingWithdrawalCredentials() | 13924295 |
|---------------------------------------------------------------|----------|
|                                                               |      157 |

The lost gas is 157.
```
                

```diff
|   Test Name                       | Gas      |
|-----------------------------------|----------|
-| testDeployAndVerifyNewEigenPod() | 13918016 |
+| testDeployAndVerifyNewEigenPod() | 13918173 |
|-----------------------------------|----------|
|                                   |      157 |

The lost gas is 157.
```
                

```diff
|   Test Name                                 | Gas      |
|---------------------------------------------|----------|
-| testDeployNewEigenPodWithActiveValidator() | 24054672 |
+| testDeployNewEigenPodWithActiveValidator() | 24054829 |
|---------------------------------------------|----------|
|                                             |      157 |

The lost gas is 157.
```
                

```diff
|   Test Name                 | Gas      |
|-----------------------------|----------|
-| testDoubleFullWithdrawal() | 45905901 |
+| testDoubleFullWithdrawal() | 45906340 |
|-----------------------------|----------|
|                             |      439 |

The lost gas is 439.
```
                

```diff
|   Test Name               | Gas      |
|---------------------------|----------|
-| testFullWithdrawalFlow() | 29372354 |
+| testFullWithdrawalFlow() | 29372651 |
|---------------------------|----------|
|                           |      297 |

The lost gas is 297.
```
                

```diff
|   Test Name                | Gas      |
|----------------------------|----------|
-| testFullWithdrawalProof() | 13943822 |
+| testFullWithdrawalProof() | 13942685 |
|----------------------------|----------|
|                            |    -1137 |

The saved gas is 1137.
```
                

```diff
|   Test Name                  | Gas      |
|------------------------------|----------|
-| testPartialWithdrawalFlow() | 29533623 |
+| testPartialWithdrawalFlow() | 29533929 |
|------------------------------|----------|
|                              |      306 |

The lost gas is 306.
```
                

```diff
|   Test Name                                       | Gas      |
|---------------------------------------------------|----------|
-| testProveOverComittedStakeOnWithdrawnValidator() | 24163595 |
+| testProveOverComittedStakeOnWithdrawnValidator() | 24163756 |
|---------------------------------------------------|----------|
|                                                   |      161 |

The lost gas is 161.
```
                

```diff
|   Test Name                      | Gas      |
|----------------------------------|----------|
-| testProveOverCommittedBalance() | 24453410 |
+| testProveOverCommittedBalance() | 24453651 |
|----------------------------------|----------|
|                                  |      241 |

The lost gas is 241.
```
                

```diff
|   Test Name                            | Gas      |
|----------------------------------------|----------|
-| testProveSingleWithdrawalCredential() | 14015510 |
+| testProveSingleWithdrawalCredential() | 14015667 |
|----------------------------------------|----------|
|                                        |      157 |

The lost gas is 157.
```
                

```diff
|   Test Name                                  | Gas      |
|----------------------------------------------|----------|
-| testProvingMultipleWithdrawalsForSameSlot() | 46099773 |
+| testProvingMultipleWithdrawalsForSameSlot() | 46100228 |
|----------------------------------------------|----------|
|                                              |      455 |

The lost gas is 455.
```
                

```diff
|   Test Name                       | Gas      |
|-----------------------------------|----------|
-| testUpdateSlashedBeaconBalance() | 24327399 |
+| testUpdateSlashedBeaconBalance() | 24327640 |
|-----------------------------------|----------|
|                                   |      241 |

The lost gas is 241.
```
                

```diff
|   Test Name                                      | Gas      |
|--------------------------------------------------|----------|
-| testVerifyOvercommittedStakeRevertsWhenPaused() | 24379033 |
+| testVerifyOvercommittedStakeRevertsWhenPaused() | 24379194 |
|--------------------------------------------------|----------|
|                                                  |      161 |

The lost gas is 161.
```
                

```diff
|   Test Name                                  | Gas      |
|----------------------------------------------|----------|
-| testWithdrawBeforeRestakingAfterRestaking() | 13902091 |
+| testWithdrawBeforeRestakingAfterRestaking() | 13902246 |
|----------------------------------------------|----------|
|                                              |      155 |

The lost gas is 155.
```
                

```diff
|   Test Name                                                                                    | Gas    |
|------------------------------------------------------------------------------------------------|--------|
-| testRecordStakeUpdate_MultipleLinkedListEntries(address,address,uint32,uint32,uint32,uint256) | 584630 |
+| testRecordStakeUpdate_MultipleLinkedListEntries(address,address,uint32,uint32,uint32,uint256) | 573446 |
|------------------------------------------------------------------------------------------------|--------|
|                                                                                                | -11184 |

The average saved gas is 11184.
```
                

```diff
|   Test Name                           | Gas    |
|---------------------------------------|--------|
-| testResetFrozenStatus(uint8,uint256) | 178413 |
+| testResetFrozenStatus(uint8,uint256) | 166845 |
|---------------------------------------|--------|
|                                       | -11568 |

The average saved gas is 11568.
```
                

```diff
|   Test Name                                       | Gas    |
|---------------------------------------------------|--------|
-| testQueueWithdrawalBeaconChainETHToSelf(uint128) | 172244 |
+| testQueueWithdrawalBeaconChainETHToSelf(uint128) | 172249 |
|---------------------------------------------------|--------|
|                                                   |      5 |

The average lost gas is 5.
```
                

| Test                             | Name                                                                                          | Gas    |
|----------------------------------|-----------------------------------------------------------------------------------------------|--------|
| DelayedWithdrawalRouterUnitTests | testClaimDelayedWithdrawals(uint8,uint8,uint256,address,bool)                                 | -34    |
| DelegationUnitTests              | testDecreaseDelegatedSharesFromNonStrategyManagerAddress(address,address[],uint256[])         | 332    |
| DepositWithdrawTests             | testForkMainnetDepositSteth()                                                                 | -1200  |
| EigenPodTests                    | testAttemptedWithdrawalAfterVerifyingWithdrawalCredentials()                                  | 157    |
| EigenPodTests                    | testDeployAndVerifyNewEigenPod()                                                              | 157    |
| EigenPodTests                    | testDeployNewEigenPodWithActiveValidator()                                                    | 157    |
| EigenPodTests                    | testDoubleFullWithdrawal()                                                                    | 439    |
| EigenPodTests                    | testFullWithdrawalFlow()                                                                      | 297    |
| EigenPodTests                    | testFullWithdrawalProof()                                                                     | -1137  |
| EigenPodTests                    | testPartialWithdrawalFlow()                                                                   | 306    |
| EigenPodTests                    | testProveOverComittedStakeOnWithdrawnValidator()                                              | 161    |
| EigenPodTests                    | testProveOverCommittedBalance()                                                               | 241    |
| EigenPodTests                    | testProveSingleWithdrawalCredential()                                                         | 157    |
| EigenPodTests                    | testProvingMultipleWithdrawalsForSameSlot()                                                   | 455    |
| EigenPodTests                    | testUpdateSlashedBeaconBalance()                                                              | 241    |
| EigenPodTests                    | testVerifyOvercommittedStakeRevertsWhenPaused()                                               | 161    |
| EigenPodTests                    | testWithdrawBeforeRestakingAfterRestaking()                                                   | 155    |
| SlasherUnitTests                 | testRecordStakeUpdate_MultipleLinkedListEntries(address,address,uint32,uint32,uint32,uint256) | -11184 |
| SlasherUnitTests                 | testResetFrozenStatus(uint8,uint256)                                                          | -11568 |
| StrategyManagerUnitTests         | testQueueWithdrawalBeaconChainETHToSelf(uint128)                                              | 5      |
| --                               | --                                                                                            |        |
| Total                            |                                                                                               | -21702 |
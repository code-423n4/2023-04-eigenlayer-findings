## [L-01] `Endian::fromLittleEndianUint64` allows unsafe casting
### Description

The input to [`Endian::fromLittleEndianUint64`](https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/libraries/Endian.sol#L5-L19) is not validated. It should check that the number to be cast is not greater than `2^64` in little endian notation.

### Impact

In the event this library is used to cast a number greater that `2^64`, the casting will be done incorrectly; however; this is not currently the case and so we evaluate this as **LOW**.

### Proof of Concept

For instance, if this library attempts to cast `2^65` in little endian, the output will be 1 in big endian.

### Recommended Mitigation Steps

Add the corresponding check to revert for casting of numbers greater than `2^64` in little endian:

```diff

    // In Endian.fromLittleEndianUint64(bytes32)
+ if(n & 0xFFFFFFFFFFFFFFFF000000000000000000000000000000000000000000000000 != n){
+ return ERROR_LITTLE_ENDIAN_UNSAFE_UINT64_CAST()
+ }
    n = uint64(uint256(lenum >> 192));
    //...
```

## [NC-01] `EigenPod::stake`: should be refactored to allow any stake and not just 32 ether
`EigenPod::stake`, callable only through `EigenPodManager::stake`, allows the caller to stake [32 ether only](https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/pods/EigenPod.sol#L160), no more or less.

According to the sponsor, it is valid that a user could stake a different amount through the ETH2Deposit contract if they really wanted to. Therefore, to improve UX, this function can be refactored to allow ether deposits of any value.

## [NC-02] Rename `EigenPod::mostRecentWithdrawalBlockNumber` to `mostRecentWithdrawalBeforeRestakingBlockNumber`
EigenPods essentially switch between 2 modalities, with only (1) possible before launch of the EigenLayer Beacon Chain Oracle:
\1) users can re-point withdrawal credentials but not yet prove they have been re-pointed via the `EigenPod::verifyWithdrawalCredentialsAndBalance`. Partial & full withdrawals will increment their EigenPod balance, and they can then call `EigenPod::withdrawBeforeRestaking` to withdraw any ETH in the EigenPod. Once a user proves re-pointing of withdrawal credentials for the first time (which must be proven after `EigenPod::mostRecentWithdrawalBlockNumber` because they could have repointed => withdrawn => used the ETH for something else prior to `EigenPod::mostRecentWithdrawalBlockNumber`) then the modality is switched to:
\2) users can no longer call `EigenPod::withdrawBeforeRestaking` and must use `EigenPod::withdrawRestakedBeaconChainETH`.

Therefore, `EigenPos::mostRecentWithdrawalBlockNumber` should be more properly called `mostRecentWithdrawalBeforeRestakingBlockNumber`.
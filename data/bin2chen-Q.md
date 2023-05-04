L-01:slashShares() Missing to determine wether GWEI
```solidity
    function slashShares(
        address slashedAddress,
        address recipient,
        IStrategy[] calldata strategies,
        IERC20[] calldata tokens,
        uint256[] calldata strategyIndexes,
        uint256[] calldata shareAmounts
    )
        external
        onlyOwner
        onlyFrozen(slashedAddress)
        nonReentrant
    {
...
            if (strategies[i] == beaconChainETHStrategy) {
+                require(shareAmounts[i] % GWEI_TO_WEI == 0,"bad shares")
                 //withdraw the beaconChainETH to the recipient
                _withdrawBeaconChainETH(slashedAddress, recipient, shareAmounts[i]);
            }    
```

L-02:_completeQueuedWithdrawal() Missing judgment if `beaconChainETHStrategy` cannot be directly `_addShares` 
```solidity
    function _completeQueuedWithdrawal(QueuedWithdrawal calldata queuedWithdrawal, IERC20[] calldata tokens, uint256 middlewareTimesIndex, bool receiveAsTokens) onlyNotFrozen(queuedWithdrawal.delegatedAddress) internal {

        } else {

            // else increase their shares
            for (uint256 i = 0; i < strategiesLength;) {
+               require(queuedWithdrawal.strategies[i]!=beaconChainETHStrategy,"beaconChainETHStrategy can't deposit");
                _addShares(msg.sender, queuedWithdrawal.strategies[i], queuedWithdrawal.shares[i]);
                unchecked {
                    ++i;
                }
            }
        }
        emit WithdrawalCompleted(queuedWithdrawal.depositor, queuedWithdrawal.withdrawerAndNonce.nonce, msg.sender, withdrawalRoot);
    }    
```
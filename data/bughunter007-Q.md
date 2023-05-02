L1) The function claimableUserDelayedWithdrawals of DelayedWithDrawlRouter.sol is not functioning as per function specification comments.

https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/pods/DelayedWithdrawalRouter.sol#L109-L119

The comments section for claimableUserDelayedWithdrawals function says following
//Getter function to get all delayedWithdrawals that are currently claimable by the `user`
But the function doesn't actually returns currently claimable withdrawls as there is no block number verification. It actually returns all claimable withdrawls (withdrawls that can be claimed in current block + withdrawls that can be claimed in future blocks)

Mitigation: Add block number verification in the for loop as shown below. claimableDelayedWithdrawals array will have to be re-sized.
if (block.number < claimableDelayedWithdrawals[i].blockCreated + withdrawalDelayBlocks) {
                break;
            }

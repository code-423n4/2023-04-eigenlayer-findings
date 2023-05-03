# **EigenLayer QA report**

## **Table of Contents**

1.  **Low Issues**

            - L-01 Missing equivalence checks in setters
            - L-02 Measures for preventing out of gas error should be implemented
            - L-03 The idea of having a pauser and an unpauser hints a technical flaw
            - L-04 Inconsistency of the \_gap in contracts in scope most contracts have it fixed at 48 even if less than that could be used to fit the best practise of having 50 storage gaps
            - L-05 Unfair slashing of frozen user's shares
            - L-06 Lack of zero address checks for immutable addresses in constructor
            - L-07 Validate array length matching before execution to avoid reverts
            - L-08 Unsafe downcast of msg.value in DelayedWithdrawalRouter.createDelayedWithdrawal() could truncate `podOwners` input
            - L-09 No sanity check in DelayedWithdrawalRouter.userDelayedWithdrawalByIndex()
            - L-10 block.number can be manipulated by miners
            - L-11 isContract() modifier can be bypassed through constructor
            - L-12 Consider isContract checks in constructors

2.  **Non-Critical Issues**

            - NC-01 Wrong concept in `EigenPod._processFullWithdrawal()` due to check's inclusivity
            - NC-02 Some Typos were missed by the bot
            - NC-03 Wrong naming of function's parameters
            - NC-04 Confusing error message
            - NC-05 Some events are missing indexed fields
            - NC-06 uint256 can be used instead of uint
            - NC-07 Finally, best practices of source file layout should be followed

# Low Issues

## L-01 Missing equivalence checks in setters

Setter functions are missing checks to validate if the new value being set is the same as the current value already set in the contract. Such checks will showcase mismatches between on-chain and off-chain states.

### Proof of Concept

Multiple instances in code, below are a few

[PauserRegistry.sol#L31-L52:](https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/permissions/PauserRegistry.sol#L31-L52)

```solidity
    /// @notice Sets new pauser - only callable by unpauser, as the unpauser is expected to be kept more secure, e.g. being a multisig with a higher threshold
    function setPauser(address newPauser) external onlyUnpauser {
        _setPauser(newPauser);
    }

    /// @notice Sets new unpauser - only callable by unpauser, as the unpauser is expected to be kept more secure, e.g. being a multisig with a higher threshold
    function setUnpauser(address newUnpauser) external onlyUnpauser {
        _setUnpauser(newUnpauser);
    }

    function _setPauser(address newPauser) internal {
        require(newPauser != address(0), "PauserRegistry._setPauser: zero address input");
        emit PauserChanged(pauser, newPauser);
        pauser = newPauser;
    }

    function _setUnpauser(address newUnpauser) internal {
        require(newUnpauser != address(0), "PauserRegistry._setUnpauser: zero address input");
        emit UnpauserChanged(unpauser, newUnpauser);
        unpauser = newUnpauser;
    }
}
```

### Recommendation

This may hinder detecting discrepancies between on-chain and off-chain states leading to flawed assumptions of on-chain state and protocol behavior.

## L-02 Measures for preventing out of gas error should be implemented

### Proof of Concept

Due to a lot of computation in the function with the keccak hash and all that, advisable a check should be made to know the maximum length that could be provided so as not to caus e the execution to error out due to non-avalability of gas, this length value should then be used and a maximum length of leaves.length in protocol and sanity checks should be introduced to make sure that any leaves array passed in as a parameter should not have a length more than this.

[Merkle.merkleizeSha256();](https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/libraries/Merkle.sol#L123-L153)

```solidity
    /**
     @notice this function returns the merkle root of a tree created from a set of leaves using sha256 as its hash function
     @param leaves the leaves of the merkle tree

     @notice requires the leaves.length is a power of 2
     */
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

### Recommendation

Bound the number of leaves.length that can be aggregated per call, and allow them to be processed in batches (if needed)

## L-03 The idea of having a pauser and an unpauser hints a technical flaw

### Proof of Concept

One could argue that the idea of having a pauser and an unpauser is just unnecessary complexity if only the unpauser is going to be kept really secure, also the protocol seem to let only the unpaser be able call either of the setters for both pauser/unPauser addresses. This functionality should be instead joined and used as one role that's _effectively_ secured
[PauserRegistry.sol#L31-L52:](https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/permissions/PauserRegistry.sol#L31-L52)

```solidity
    /// @notice Sets new pauser - only callable by unpauser, as the unpauser is expected to be kept more secure, e.g. being a multisig with a higher threshold
    function setPauser(address newPauser) external onlyUnpauser {
        _setPauser(newPauser);
    }

    /// @notice Sets new unpauser - only callable by unpauser, as the unpauser is expected to be kept more secure, e.g. being a multisig with a higher threshold
    function setUnpauser(address newUnpauser) external onlyUnpauser {
        _setUnpauser(newUnpauser);
    }

    function _setPauser(address newPauser) internal {
        require(newPauser != address(0), "PauserRegistry._setPauser: zero address input");
        emit PauserChanged(pauser, newPauser);
        pauser = newPauser;
    }

    function _setUnpauser(address newUnpauser) internal {
        require(newUnpauser != address(0), "PauserRegistry._setUnpauser: zero address input");
        emit UnpauserChanged(unpauser, newUnpauser);
        unpauser = newUnpauser;
    }
}
```

### Recommendation

Thorougly think this through and implement the best case for users and protocol

## L-04 Inconsistency of the \_gap in contracts in scope most contracts have it fixed at 48 even if less than that could be used to fit the best practise of having 50 storage gaps

### Proof of Concept

Multiple contracts in scope implement the `_gap` but they are all defaulted to 48 except for one

### Recommendation

Check this and implement the `_gap` correctly

## L-05 Unfair slashing of frozen user's shares

This is submitted as a low, as it's just my suggestion on how the slashing of delegated users shares is unfair

### Proof of Concept

[IStrategyManager.sol#L167-L186](https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/interfaces/IStrategyManager.sol#L167-L186)
If slashing any staker's shares delegated to a frozen operator is allowed, that means honest users could be at the end of the knife even if they are not in control of what the people they've delegated to could do, also as it stands there are no provisions for removing a delegation, so this just means that even if users would like to undelegate themselves in order not to be affected by this they can't do that.

```solidity
    /**
     * @notice Slashes the shares of a 'frozen' operator (or a staker delegated to one)
     * @param slashedAddress is the frozen address that is having its shares slashed
     * @param strategyIndexes is a list of the indices in `stakerStrategyList[msg.sender]` that correspond to the strategies
     * for which `msg.sender` is withdrawing 100% of their shares
     * @param recipient The slashed funds are withdrawn as tokens to this address.
     * @dev strategies are removed from `stakerStrategyList` by swapping the last entry with the entry to be removed, then
     * popping off the last entry in `stakerStrategyList`. The simplest way to calculate the correct `strategyIndexes` to input
     * is to order the strategies *for which `msg.sender` is withdrawing 100% of their shares* from highest index in
     * `stakerStrategyList` to lowest index
     */
    function slashShares(
        address slashedAddress,
        address recipient,
        IStrategy[] calldata strategies,
        IERC20[] calldata tokens,
        uint256[] calldata strategyIndexes,
        uint256[] calldata shareAmounts
    )
        external;
```

### Recommendation

Sponsors should think this through and at the very least a timeframe should be introduced and any user who delegates to another before they get frozen should be affected by the slashing of shares, whereas any that still delegates to an already frozen address should be subjected to the share slashing(though i doubt any one would do this, except by mistake in which case this is valid cuse there are no provisions to undelegate ones self).

Another idea is to just incorporate a way of a user undelegating an address they've delegated to

## L-06 Lack of zero address checks for immutable addresses in constructor

### Proof of Concept

Multiple instances in codes, which include [EigenPodsol#constructor():](https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/pods/EigenPod.sol#L136-L149)

```solidity
    constructor(
        IETHPOSDeposit _ethPOS,
        IDelayedWithdrawalRouter _delayedWithdrawalRouter,
        IEigenPodManager _eigenPodManager,
        uint256 _REQUIRED_BALANCE_WEI
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

[StrategyBase.sol#constructor():](https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/strategies/StrategyBase.sol#L45-L49)

```
    /// @notice Since this contract is designed to be initializable, the constructor simply sets `strategyManager`, the only immutable variable.
    constructor(IStrategyManager _strategyManager) {
        strategyManager = _strategyManager;
        _disableInitializers();
    }
```

Constructors should check the address written in an immutable address variable is not the zero address when the contract is being deployed by the team.

For example if the `strategyManager` from `strategyBase` gets set to 0x0 it breaks all functionality by the `strategyManager`

### Recommendation

## L-07 Validate array length matching before execution to avoid reverts

Note that there are multiple instances of this within contracts in scope but more rampant in the [StrategyManager.sol, ](https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/core/StrategyManager.sol) [EigenPod.sol](https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/pods/EigenPod.sol) contracts

### Proof of Concept

Multiple contracts in scope have many functions that accept multiple arrays that contain the necessary data for execution in their respective routes. However, in some cases some of the array lengths are checked against each other whereas in some cases they are not checked against each other and in some cases the checks are not complete , in the sense that three arrays are provided but only two are checked against each other, but these arrays need to be of the same length because individual elements in the arrays are intended to be matched at the same indices:

[EigenPod.verifyAndProcessWithdrawal();](https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/pods/EigenPod.sol#L296-L359)

```solidity
    /**
     * @notice This function records a full withdrawal on behalf of one of the Ethereum validators for this EigenPod
     * @param withdrawalProofs is the information needed to check the veracity of the block number and withdrawal being proven
     * @param validatorFieldsProof is the information needed to check the veracity of the validator fields being proven
     * @param withdrawalFields are the fields of the withdrawal being proven
     * @param validatorFields are the fields of the validator being proven
     * @param beaconChainETHStrategyIndex is the index of the beaconChainETHStrategy for the pod owner for the callback to
     *        the EigenPodManager to the StrategyManager in case it must be removed from the podOwner's list of strategies
     */
    function verifyAndProcessWithdrawal(
        BeaconChainProofs.WithdrawalProofs calldata withdrawalProofs,
        bytes calldata validatorFieldsProof,
        bytes32[] calldata validatorFields,
        bytes32[] calldata withdrawalFields,
        uint256 beaconChainETHStrategyIndex,
        uint64 oracleBlockNumber
    )
        external
        onlyWhenNotPaused(PAUSED_EIGENPODS_VERIFY_WITHDRAWAL)
        onlyNotFrozen
        /**
         * Check that the provided block number being proven against is after the `mostRecentWithdrawalBlockNumber`.
         * Without this check, there is an edge case where a user proves a past withdrawal for a validator whose funds they already withdrew,
         * as a way to "withdraw the same funds twice" without providing adequate proof.
         * Note that this check is not made using the oracleBlockNumber as in the `verifyWithdrawalCredentials` proof; instead this proof
         * proof is made for the block number of the withdrawal, which may be within 8192 slots of the oracleBlockNumber.
         * This difference in modifier usage is OK, since it is still not possible to `verifyAndProcessWithdrawal` against a slot that occurred
         * *prior* to the proof provided in the `verifyWithdrawalCredentials` function.
         */
        proofIsForValidBlockNumber(Endian.fromLittleEndianUint64(withdrawalProofs.blockNumberRoot))
    {
        /**
         * If the validator status is inactive, then withdrawal credentials were never verified for the validator,
         * and thus we cannot know that the validator is related to this EigenPod at all!
         */
        uint40 validatorIndex = uint40(Endian.fromLittleEndianUint64(withdrawalFields[BeaconChainProofs.WITHDRAWAL_VALIDATOR_INDEX_INDEX]));

        require(validatorStatus[validatorIndex] != VALIDATOR_STATUS.INACTIVE,
            "EigenPod.verifyOvercommittedStake: Validator never proven to have withdrawal credentials pointed to this contract");

        // fetch the beacon state root for the specified block
        bytes32 beaconStateRoot = eigenPodManager.getBeaconChainStateRoot(oracleBlockNumber);

        // Verifying the withdrawal as well as the slot
        BeaconChainProofs.verifyWithdrawalProofs(beaconStateRoot, withdrawalProofs, withdrawalFields);
        // Verifying the validator fields, specifically the withdrawable epoch
        BeaconChainProofs.verifyValidatorFields(validatorIndex, beaconStateRoot, validatorFieldsProof, validatorFields);

        uint64 withdrawalAmountGwei = Endian.fromLittleEndianUint64(withdrawalFields[BeaconChainProofs.WITHDRAWAL_VALIDATOR_AMOUNT_INDEX]);

        //check if the withdrawal occured after mostRecentWithdrawalBlockNumber
        uint64 slot = Endian.fromLittleEndianUint64(withdrawalProofs.slotRoot);

        /**
         * if the validator's withdrawable epoch is less than or equal to the slot's epoch, then the validator has fully withdrawn because
         * a full withdrawal is only processable after the withdrawable epoch has passed.
         */
        // reference: uint64 withdrawableEpoch = Endian.fromLittleEndianUint64(validatorFields[BeaconChainProofs.VALIDATOR_WITHDRAWABLE_EPOCH_INDEX]);
        if (Endian.fromLittleEndianUint64(validatorFields[BeaconChainProofs.VALIDATOR_WITHDRAWABLE_EPOCH_INDEX]) <= slot/BeaconChainProofs.SLOTS_PER_EPOCH) {
            _processFullWithdrawal(withdrawalAmountGwei, validatorIndex, beaconChainETHStrategyIndex, podOwner, validatorStatus[validatorIndex]);
        } else {
            _processPartialWithdrawal(slot, withdrawalAmountGwei, validatorIndex, podOwner);
        }
    }
```

Note that in the above example function, all different calldata arrays were utilizing the same index to retrieve the correct element. A common practice in such cases is to confirm that the sizes of the arrays match before continuing with the execution of the rest of the transaction to avoid costly reverts that could happen due to “Index out of bounds” error.

Siunce protocol might rely on 3rd party offchain APIs to construct these array payloads for users, a mishap in just any one of them could cause this issue.

### Recommendation

Implement a check on the array lengths so they match.

## L-08 Unsafe downcast of msg.value in DelayedWithdrawalRouter.createDelayedWithdrawal() could truncate `podOwners` input

When a type is downcast to a smaller type, the higher order bits are truncated, effectively applying a modulo to the original value. Without any other checks, this wrapping will lead to unexpected behaviour and bugs

### Proof of Concept

[DelayedWithdrawalRouter.createDelayedWithdrawal()](https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/pods/DelayedWithdrawalRouter.sol#L55-L69)

```solidity
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

This should be a medium severity issue, but Only submitting this as a low cause the chance of this happening is pretty low, since the issue with the `createDelayedWithdrawal()` function is only mathematically possible but the possibility of this happening is very low, since the value is being truncated to a high uint, in this case uint224. And it also needs the call to happen wit the value being set as the `msg.value`
But the unsafe downcasting of msg.value, would lead to truncation of the `podOwners` input and undesired bahaviour.

### Recommendation

Just use uint256, or use openzeppelin safeCasting.

https://docs.openzeppelin.com/contracts/3.x/api/utils#SafeCast

## L-09 No sanity check in DelayedWithdrawalRouter.userDelayedWithdrawalByIndex()

### Proof of Concept

A sanity check should be introduced to make sure that the index provided is smaller than the length of the array `_userWithdrawals[user].delayedWithdrawals.length` and reverting if that's not the case

[ DelayedWithdrawalRouter.userDelayedWithdrawalByIndex() ;](https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/pods/DelayedWithdrawalRouter.sol#L121-L124)

```solidity
    /// @notice Getter function for fetching the delayedWithdrawal at the `index`th entry from the `_userWithdrawals[user].delayedWithdrawals` array
    function userDelayedWithdrawalByIndex(address user, uint256 index) external view returns (DelayedWithdrawal memory) {
        return _userWithdrawals[user].delayedWithdrawals[index];
    }

```

### Recommendation

Introduce a check

## L-10 block.number can be manipulated by miners

### Proof of Concept

[StrategyManager.\_completeQueuedWithdrawal():](https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/core/StrategyManager.sol#L742-L805)

```solidity
    /**
     * @notice Internal function for completing the given `queuedWithdrawal`.
     */
    function _completeQueuedWithdrawal(QueuedWithdrawal calldata queuedWithdrawal, IERC20[] calldata tokens, uint256 middlewareTimesIndex, bool receiveAsTokens) onlyNotFrozen(queuedWithdrawal.delegatedAddress) internal {
        // find the withdrawalRoot
        bytes32 withdrawalRoot = calculateWithdrawalRoot(queuedWithdrawal);

        // verify that the queued withdrawal is pending
        require(
            withdrawalRootPending[withdrawalRoot],
            "StrategyManager.completeQueuedWithdrawal: withdrawal is not pending"
        );

        require(
            slasher.canWithdraw(queuedWithdrawal.delegatedAddress, queuedWithdrawal.withdrawalStartBlock, middlewareTimesIndex),
            "StrategyManager.completeQueuedWithdrawal: shares pending withdrawal are still slashable"
        );

        // enforce minimum delay lag (not applied to withdrawals of 'beaconChainETH', since the EigenPods enforce their own delay)
        require(queuedWithdrawal.withdrawalStartBlock + withdrawalDelayBlocks <= block.number
                || queuedWithdrawal.strategies[0] == beaconChainETHStrategy,
            "StrategyManager.completeQueuedWithdrawal: withdrawalDelayBlocks period has not yet passed"
        );

        require(
            msg.sender == queuedWithdrawal.withdrawerAndNonce.withdrawer,
            "StrategyManager.completeQueuedWithdrawal: only specified withdrawer can complete a queued withdrawal"
        );

        // reset the storage slot in mapping of queued withdrawals
        withdrawalRootPending[withdrawalRoot] = false;

        // store length for gas savings
        uint256 strategiesLength = queuedWithdrawal.strategies.length;
        // if the withdrawer has flagged to receive the funds as tokens, withdraw from strategies
        if (receiveAsTokens) {
            require(tokens.length == queuedWithdrawal.strategies.length, "StrategyManager.completeQueuedWithdrawal: input length mismatch");
            // actually withdraw the funds
            for (uint256 i = 0; i < strategiesLength;) {
                if (queuedWithdrawal.strategies[i] == beaconChainETHStrategy) {

                    // if the strategy is the beaconchaineth strat, then withdraw through the EigenPod flow
                    _withdrawBeaconChainETH(queuedWithdrawal.depositor, msg.sender, queuedWithdrawal.shares[i]);
                } else {
                    // tell the strategy to send the appropriate amount of funds to the depositor
                    queuedWithdrawal.strategies[i].withdraw(
                        msg.sender, tokens[i], queuedWithdrawal.shares[i]
                    );
                }
                unchecked {
                    ++i;
                }
            }
        } else {
            // else increase their shares
            for (uint256 i = 0; i < strategiesLength;) {
                _addShares(msg.sender, queuedWithdrawal.strategies[i], queuedWithdrawal.shares[i]);
                unchecked {
                    ++i;
                }
            }
        }
        emit WithdrawalCompleted(queuedWithdrawal.depositor, queuedWithdrawal.withdrawerAndNonce.nonce, msg.sender, withdrawalRoot);
    }
```

On the protocol level, timestamps are a variable submitted by miners. The protocol only guarantees its monotonicity. Therefore, they are non-trivially manipulable by miners for purposes of MEV.
It is a risk for critical logic of the contract to be dependent on such a variable, in this case the `withdrawalStartBlock + withdrawalDelayBlocks` is being checked against ` block.number` However, one should know that such manipulation would widely affect the Ethereum ecosystem.
Therefore, there is a low chance miners would take such a risk.
This serves as a notice of the possibility of miner’s manipulating of the timestamp for the purposes of MEV.

### Recommendation

The past general recommendation regarding block.timestamp has been to consider block.number as a better gauge of time. However, with
the upcoming merge and forks, it may become unreliable for this in the mid to long-term right now.
For a contract that aims to operate consistently following the merge, block.timestamp
may be the less risky option compared to block.number in regards to references for elapsed time.The rule of thumb regarding the usage of `block.number` is to make sure that it is for references to longer time frames, i.e. nothing under 1 hour.

## L-11 isContract() modifier can be bypassed through constructor

### Proof of Concept

[StrategyManager.sol#L283-L292:](https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/core/StrategyManager.sol#L283-L292)

```
        /**
         * check validity of signature:
         * 1) if `staker` is an EOA, then `signature` must be a valid ECSDA signature from `staker`,
         * indicating their intention for this action
         * 2) if `staker` is a contract, then `signature` must will be checked according to EIP-1271
         */
        if (Address.isContract(staker)) {
            require(IERC1271(staker).isValidSignature(digestHash, signature) == ERC1271_MAGICVALUE,
                "StrategyManager.depositIntoStrategyWithSignature: ERC1271 signature verification failed");
        } else {
```

As seen `isContract()` is called to ensure that `staker` is contract and if otherwise the below is executed

```
            require(ECDSA.recover(digestHash, signature) == staker,
                "StrategyManager.depositIntoStrategyWithSignature: signature not from staker");
        }
```

Though i can't find a suitable attack vector for this, the team should know that isContract can be called from the constructor

### Recommendation

Team should at the very least note this

## L-12 Consider isContract checks in constructors

### Proof of Concept

Several addresses are assigned in the contract constructors and assigned to immutable variables. A successful deployment is sensitive to these addresses being assigned correctly for the current network, and that addresses were specified in the correct order. Consider adding checks, as aggressively as possible for the use case, to help ensure the deployment configuration is correct.

### Recommendation

Add more aggressive checks

# Non-critical Issues

## NC-01 Wrong concept in `EigenPod._processFullWithdrawal()` due to check's inclusivity

### Proof of Concept

Take a look at [EigenPod.\_processFullWithdrawal();](https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/pods/EigenPod.sol#L361-L420)

```solidity
    function _processFullWithdrawal(
        uint64 withdrawalAmountGwei,
        uint40 validatorIndex,
        uint256 beaconChainETHStrategyIndex,
        address recipient,
        VALIDATOR_STATUS status
    ) internal {
        uint256 amountToSend;

        // if the validator has not previously been proven to be "overcommitted"
        if (status == VALIDATOR_STATUS.ACTIVE) {
            // if the withdrawal amount is greater than the REQUIRED_BALANCE_GWEI (i.e. the amount restaked on EigenLayer, per ETH validator)
            if (withdrawalAmountGwei >= REQUIRED_BALANCE_GWEI) {//@audit
                // then the excess is immediately withdrawable
                amountToSend = uint256(withdrawalAmountGwei - REQUIRED_BALANCE_GWEI) * uint256(GWEI_TO_WEI);
                // and the extra execution layer ETH in the contract is REQUIRED_BALANCE_GWEI, which must be withdrawn through EigenLayer's normal withdrawal process
                restakedExecutionLayerGwei += REQUIRED_BALANCE_GWEI;
            } else {
                // otherwise, just use the full withdrawal amount to continue to "back" the podOwner's remaining shares in EigenLayer (i.e. none is instantly withdrawable)
                restakedExecutionLayerGwei += withdrawalAmountGwei;
                // remove and undelegate 'extra' (i.e. "overcommitted") shares in EigenLayer
                eigenPodManager.recordOvercommittedBeaconChainETH(podOwner, beaconChainETHStrategyIndex, uint256(REQUIRED_BALANCE_GWEI - withdrawalAmountGwei) * GWEI_TO_WEI);
            }
        // if the validator *has* previously been proven to be "overcommitted"
        } else if (status == VALIDATOR_STATUS.OVERCOMMITTED) {
            // if the withdrawal amount is greater than the REQUIRED_BALANCE_GWEI (i.e. the amount restaked on EigenLayer, per ETH validator)
            if (withdrawalAmountGwei >= REQUIRED_BALANCE_GWEI) {//@audit
                // then the excess is immediately withdrawable
                amountToSend = uint256(withdrawalAmountGwei - REQUIRED_BALANCE_GWEI) * uint256(GWEI_TO_WEI);
                // and the extra execution layer ETH in the contract is REQUIRED_BALANCE_GWEI, which must be withdrawn through EigenLayer's normal withdrawal process
                restakedExecutionLayerGwei += REQUIRED_BALANCE_GWEI;
                /**
                 * since in `verifyOvercommittedStake` the podOwner's beaconChainETH shares are decremented by `REQUIRED_BALANCE_WEI`, we must reverse the process here,
                 * in order to allow the podOwner to complete their withdrawal through EigenLayer's normal withdrawal process
                 */
                eigenPodManager.restakeBeaconChainETH(podOwner, REQUIRED_BALANCE_WEI);
            } else {
                // otherwise, just use the full withdrawal amount to continue to "back" the podOwner's remaining shares in EigenLayer (i.e. none is instantly withdrawable)
                restakedExecutionLayerGwei += withdrawalAmountGwei;
                /**
                 * since in `verifyOvercommittedStake` the podOwner's beaconChainETH shares are decremented by `REQUIRED_BALANCE_WEI`, we must reverse the process here,
                 * in order to allow the podOwner to complete their withdrawal through EigenLayer's normal withdrawal process
                 */
                eigenPodManager.restakeBeaconChainETH(podOwner, uint256(withdrawalAmountGwei) * GWEI_TO_WEI);
            }
        // If the validator status is withdrawn, they have already processed their ETH withdrawal
        }  else {
            revert("EigenPod.verifyBeaconChainFullWithdrawal: VALIDATOR_STATUS is WITHDRAWN or invalid VALIDATOR_STATUS");
        }

        // set the ETH validator status to withdrawn
        validatorStatus[validatorIndex] = VALIDATOR_STATUS.WITHDRAWN;

        emit FullWithdrawalRedeemed(validatorIndex, recipient, withdrawalAmountGwei);

        // send ETH to the `recipient`, if applicable
        if (amountToSend != 0) {
            _sendETH(recipient, amountToSend);
        }
    }
```

L373 & L387:
`            if (withdrawalAmountGwei >= REQUIRED_BALANCE_GWEI) {`
This check should not be inclusive as a subtraction should be made only when `withdrawalAmountGwei  > REQUIRED_BALANCE_GWEI` and not when `withdrawalAmountGwei  = REQUIRED_BALANCE_GWEI` cause in the latter case the difference is only 0 and `amountToSend is = uint256(0) * uint256(GWEI_TO_WEI)`, which is 0 and essentially this seems like an unnecessary calculation and break of contract's logic.

### Recommendation

Think this through, seems like a minor change or most importantly a check should be made for which of the execution costs the least between the `if & else` block and then sponsors could decide where to add the case when `withdrawalAmountGwei  = REQUIRED_BALANCE_GWEI` if the former then code can be left as it is, otherwise a little change should be implemented

## NC-02 Some Typos were missed by the bot

### Proof of Concept

Though been reported in the bot report a few instances of typos were missed by the bot
[IDelayedWithdrawalRouter.createDelayedWithdrawal()](https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/interfaces/IDelayedWithdrawalRouter.sol)

```solidity
    /**
     * @notice Creates an delayed withdrawal for `msg.value` to the `recipient`.
     * @dev Only callable by the `podOwner`'s EigenPod contract.
     */
    function createDelayedWithdrawal(address podOwner, address recipient) external payable;
```

### Recommendation

Change from:

```solidity
    /**
     * @notice Creates `AN` delayed withdrawal for `msg.value` to the `recipient`.
     * @dev Only callable by the `podOwner`'s EigenPod contract.
     */
    function createDelayedWithdrawal(address podOwner, address recipient) external payable;
```

to:

```solidity
    /**
     * @notice Creates `A` delayed withdrawal for `msg.value` to the `recipient`.
     * @dev Only callable by the `podOwner`'s EigenPod contract.
     */
    function createDelayedWithdrawal(address podOwner, address recipient) external payable;
```

## NC-03 Wrong naming of function's parameters

### Proof of Concept

[StrategyBase.withdraw():](https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/strategies/StrategyBase.sol#L61C1-L112)

```solidity
    /**
     * @notice Used to withdraw tokens from this Strategy, to the `depositor`'s address
     * @param token is the ERC20 token being transferred out
     * @param amountShares is the amount of shares being withdrawn
     * @dev This function is only callable by the strategyManager contract. It is invoked inside of the strategyManager's
     * other functions, and individual share balances are recorded in the strategyManager as well.
     */
    function withdraw(address depositor, IERC20 token, uint256 amountShares)
        external
        virtual
        override
        onlyWhenNotPaused(PAUSED_WITHDRAWALS)
        onlyStrategyManager
    {
        require(token == underlyingToken, "StrategyBase.withdraw: Can only withdraw the strategy token");
        // copy `totalShares` value to memory, prior to any decrease
        uint256 priorTotalShares = totalShares;
        require(
            amountShares <= priorTotalShares,
            "StrategyBase.withdraw: amountShares must be less than or equal to totalShares"
        );

        // Calculate the value that `totalShares` will decrease to as a result of the withdrawal
        uint256 updatedTotalShares = priorTotalShares - amountShares;
        // check to avoid edge case where share rate can be massively inflated as a 'griefing' sort of attack
        require(updatedTotalShares >= MIN_NONZERO_TOTAL_SHARES || updatedTotalShares == 0,
            "StrategyBase.withdraw: updated totalShares amount would be nonzero but below MIN_NONZERO_TOTAL_SHARES");
        // Actually decrease the `totalShares` value
        totalShares = updatedTotalShares;

        /**
         * @notice calculation of amountToSend *mirrors* `sharesToUnderlying(amountShares)`, but is different since the `totalShares` has already
         * been decremented. Specifically, notice how we use `priorTotalShares` here instead of `totalShares`.
         */
        uint256 amountToSend;
        if (priorTotalShares == amountShares) {
            amountToSend = _tokenBalance();
        } else {
            amountToSend = (_tokenBalance() * amountShares) / priorTotalShares;
        }

        underlyingToken.safeTransfer(depositor, amountToSend);
    }
```

The withdrawer is being named as `depositor` in this case which is just unnecessary confusion.

### Recommendation

Change this to `withdrawer`

## NC-04 Confusing error message

### Proof of Concept

[StrategyManager.sol#L760-L764:](https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/core/StrategyManager.sol#L760-L764)

```solidity
        // enforce minimum delay lag (not applied to withdrawals of 'beaconChainETH', since the EigenPods enforce their own delay)
        require(queuedWithdrawal.withdrawalStartBlock + withdrawalDelayBlocks <= block.number
                || queuedWithdrawal.strategies[0] == beaconChainETHStrategy,
            "StrategyManager.completeQueuedWithdrawal: withdrawalDelayBlocks period has not yet passed"
        );
```

### Recommendation

The error message only account for one of the checks, advisably more details should be provided as execution could be reverted due to the fact that `queuedWithdrawal.strategies[0] != beaconChainETHStrategy` but error message would say execution was reverted cause withdrawalDelayBlocks period has not yet passed, which is false in this case

## NC-05 Some events are missing indexed fields

### Proof of Concept

Multiple instances within code

### Recommendation

Index should be added to events when possible

## NC-06 uint256 can be used instead of uint

### Proof of Concept

Multiple instances within code in/out of scope, for example:

```
File: src/contracts/libraries/Merkle.sol

137:          for (uint i = 0; i < numNodesInLayer; i++) {

145:              for (uint i = 0; i < numNodesInLayer; i++) {
```

### Recommendation

For explicitness and consistency, uint256 can be used instead uint.

NB: this also eases readability of code

## NC-07 Finally, best practices of source file layout should be followed

### Proof of Concept

Multiple instances within code in/out of scope

### Recommendation

I recommend following best practices of solidity source file layout for readability.
Reference: https://docs.soliditylang.org/en/v0.8.15/style-guide.html#order-of-layout

This best practices is to layout a contract elements in following order:
Pragma statements, Import statements, Interfaces, Libraries, Contracts

Inside each contract, library or interface, use the following order:
Type declarations, State variables, Events, Modifiers, Functions

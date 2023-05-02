| | issue |
| ----------- | ----------- |
| 1 | [expressions for constant values such as a call to keccak256(), should use immutable rather than constant](#1-expressions-for-constant-values-such-as-a-call-to-keccak256-should-use-immutable-rather-than-constant) |
| 2 | [Multiple address/ID mappings can be combined into a single mapping of an address/ID to a struct, where appropriate](#2-multiple-addressid-mappings-can-be-combined-into-a-single-mapping-of-an-addressid-to-a-struct-where-appropriate-extra-to-what-bot-found) |
| 3 | [state variables should be cached in stack variables rather than re-reading them from storage](#3-state-variables-should-be-cached-in-stack-variables-rather-than-re-reading-them-from-storage) |
| 4 | [not using the named return variables when a function returns, wastes deployment gas](#4-not-using-the-named-return-variables-when-a-function-returns-wastes-deployment-gas) |
| 5 | [it costs more gas to initialize non-constant/non-immutable variables to zero than to let the default of zero be applied](#5-it-costs-more-gas-to-initialize-non-constantnon-immutable-variables-to-zero-than-to-let-the-default-of-zero-be-applied) |
| 6 | [require() or revert() statements that check input arguments should be at the top of the function](#6-require-or-revert-statements-that-check-input-arguments-should-be-at-the-top-of-the-function-other-than-the-ones-in-known-issues) |
| 7 | [use a more recent version of solidity](#7-use-a-more-recent-version-of-solidity) |
| 8 | [using `calldata` instead of `memory` for read-only arguments in external functions saves gas](#8-using-calldata-instead-of-memory-for-read-only-arguments-in-external-functions-saves-gas) |
| 9 | [internal or private functions not called by the contract or inherited ones should be removed to save deployment gas](#9-internal-or-private-functions-not-called-by-the-contract-or-inherited-ones-should-be-removed-to-save-deployment-gas) |
| 10 | [abi.encode() is less efficient than abi.encodepacked()](#10-abiencode-is-less-efficient-than-abiencodepacked) |
| 11 | [ Ternary over if ... else](#11-ternary-over-if--else) |
| 12 | [Use assembly to check for address(0)](#12-use-assembly-to-check-for-address0) |
| 13 | [before some functions we should check some variables for possible gas save](#13-before-some-functions-we-should-check-some-variables-for-possible-gas-save) |
| 14 | [instead of calculating a statevar with keccak256() every time the contract is made pre calculate them before and only give the result to a constant](#14-instead-of-calculating-keccak256-every-time-the-constant-is-used-pre-calculate-them-before-and-only-give-the-result-to-a-constant) |
| 15 | [Non-strict inequalities are cheaper than strict ones](#15-non-strict-inequalities-are-cheaper-than-strict-ones) |
| 16 | [part of the code can be pre calculated](#16-part-of-the-code-can-be-pre-calculated) |
| 17 | [Use selfbalance() instead of address(this).balance](#17-use-selfbalance-instead-of-addressthisbalance) |
| 18 | [state variables can be sorted better to reduce gas usage](#18-state-variables-can-be-sorted-better-to-reduce-gas-usage) |

## 1. expressions for constant values such as a call to keccak256(), should use immutable rather than constant

- [StrategyManagerStorage.sol#L17](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManagerStorage.sol#L17)
- [StrategyManagerStorage.sol#L20](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManagerStorage.sol#L20)


## 2. Multiple address/ID mappings can be combined into a single mapping of an address/ID to a struct, where appropriate (extra to what bot found)

Saves a storage slot for the mapping. Depending on the circumstances and sizes of types, can avoid a Gsset (20000 gas) per mapping combined. Reads and subsequent writes can also be cheaper when a function requires both values and they both fit in the same storage slot. Finally, if both fields are accessed in the same function, can save ~42 gas per access due to not having to recalculate the key’s keccak256 hash (Gkeccak256 - 30 gas) and that calculation’s associated stack operations. 

`nonces` and `stakerStrategyShares` and `stakerStrategyList` and `numWithdrawalsQueued` and `beaconChainETHSharesToDecrementOnWithdrawal` all can be combined. this will not save any storage slot but the access gas save will be big gas save.
- [StrategyManagerStorage.sol#L25-L68](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManagerStorage.sol#L25-L68)


## 3. state variables should be cached in stack variables rather than re-reading them from storage

Caching of a state variable replace each Gwarmaccess (100 gas) with a much cheaper stack read. Other less obvious fixes/optimizations include having local memory caches of state variable structs, or having local caches of state variable contracts/addresses. 

`validatorStatus[validatorIndex]` has a high probability to be read twice from storage in #L333 and #L355. this can be avoided by caching it before #L333
- [EigenPod.sol#L333-L355](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPod.sol#L333-L355)

`withdrawalDelayBlocks` can be cached before the loop because it doesnt change, but it is being read every time in loop
- [DelayedWithdrawalRouter.sol#L146](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/DelayedWithdrawalRouter.sol#L146)


## 4. not using the named return variables when a function returns, wastes deployment gas

- [StrategyManager.sol#L658](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L658)

- [StrategyBase.sol#L84](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/strategies/StrategyBase.sol#L84)


## 5. it costs more gas to initialize non-constant/non-immutable variables to zero than to let the default of zero be applied

- [StrategyManager.sol#L38](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L38)
- [StrategyManager.sol#L358](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L358)
- [StrategyManager.sol#L466](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L466)
- [StrategyManager.sol#L498](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L498)
- [StrategyManager.sol#L557](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L557)
- [StrategyManager.sol#L560](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L560)
- [StrategyManager.sol#L594](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L594)
- [StrategyManager.sol#L609](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L609)
- [StrategyManager.sol#L724](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L724)
- [StrategyManager.sol#L780](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L780)
- [StrategyManager.sol#L797](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L797)
- [StrategyManager.sol#L861](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L861)

- [StrategyBase.sol#L22](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/strategies/StrategyBase.sol#L22)

- [BeaconChainProofs.sol#L65](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/libraries/BeaconChainProofs.sol#L65)
- [BeaconChainProofs.sol#L133](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/libraries/BeaconChainProofs.sol#L133)
- [BeaconChainProofs.sol#L143](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/libraries/BeaconChainProofs.sol#L143)
- [BeaconChainProofs.sol#L153](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/libraries/BeaconChainProofs.sol#L153)
- [BeaconChainProofs.sol#L163](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/libraries/BeaconChainProofs.sol#L163)

- [Merkle.sol#L137](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/libraries/Merkle.sol#L137)
- [Merkle.sol#L145](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/libraries/Merkle.sol#L145)

- [Pausable.sol#L22](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/permissions/Pausable.sol#L22)

- [DelayedWithdrawalRouter.sol#L16](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/DelayedWithdrawalRouter.sol#L16)
- [DelayedWithdrawalRouter.sol#L115](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/DelayedWithdrawalRouter.sol#L115)
- [DelayedWithdrawalRouter.sol#L138](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/DelayedWithdrawalRouter.sol#L138)
- [DelayedWithdrawalRouter.sol#L141](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/DelayedWithdrawalRouter.sol#L141)


## 6. require() or revert() statements that check input arguments should be at the top of the function (other than the ones in known issues)

Checks that involve constants should come before checks that involve state variables, function calls, and calculations. By doing these checks first, the function is able to revert before wasting a Gcoldsload (2100 gas*) in a function that may ultimately revert in the unhappy case.

require in #L766 is much cheaper than all other above it in the function, so it is better to check it first to save gas.
- [StrategyManager.sol#L766](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L766)


## 7. use a more recent version of solidity

use 0.8.19 for a bunch of small improvements that result in big gas saves combined

Use a solidity version of at least 0.8.13 to get the ability to use `using for` with a list of free functions
Use a solidity version of at least 0.8.15 because the conditions necessary for inlining are relaxed. Benchmarks show that the change significantly decreases the bytecode size (which impacts the deployment cost) while the effect on the runtime gas usage is smaller.
Use a solidity version of at least 0.8.17 to  get prevention of the incorrect removal of storage writes before calls to Yul functions that conditionally terminate the external EVM call; Simplify the starting offset of zero-length operations to zero. More efficient overflow checks for multiplication.


## 8. using `calldata` instead of `memory` for read-only arguments in external functions saves gas

When a function with a memory array is called externally, the abi.decode() step has to use a for-loop to copy each index of the calldata to the memory index. Each iteration of this for-loop costs at least 60 gas (i.e. 60 * <mem_array>.length). Using calldata directly, obliviates the need for such a loop in the contract code and runtime execution. 

- [StrategyManager.sol#L254](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L254)
- [StrategyManager.sol#L876](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L876)


## 9. internal or private functions not called by the contract or inherited ones should be removed to save deployment gas

if the functions are required by the interface, the contract should inherit from that interface and use override keyword


## 10. abi.encode() is less efficient than abi.encodepacked()

- [EigenPodManager.sol#L175](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPodManager.sol#L175)
- [EigenPodManager.sol#L202](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPodManager.sol#L202)


## 11. Ternary over if ... else

Using ternary operator instead of the if else statement saves gas.

- [StrategyManager.sol#L275-L279](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L275-L279)
- [StrategyManager.sol#L289-L294](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L289-L294)
- [StrategyManager.sol#L507-L513](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L507-L513)
- [StrategyManager.sol#L567-L572](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L567-L572)
- [StrategyManager.sol#L781-L789](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L781-L789)
- [StrategyManager.sol#L824-L831](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L824-L831)

- [StrategyBase.sol#L96-L99](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/strategies/StrategyBase.sol#L96-L99)
- [StrategyBase.sol#L149-L152](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/strategies/StrategyBase.sol#L149-L152)
- [StrategyBase.sol#L173-L177](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/strategies/StrategyBase.sol#L173-L177)
- [StrategyBase.sol#L198-L201](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/strategies/StrategyBase.sol#L198-L201)


## 12. Use assembly to check for address(0)

saves 6 gas per instance

- [StrategyManager.sol#L343](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L343)
- [StrategyManager.sol#L631](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L631)
- [StrategyManager.sol#L684](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L684)

- [EigenPodManager.sol#L114](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPodManager.sol#L114)
- [EigenPodManager.sol#L196](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPodManager.sol#L196)
- [EigenPodManager.sol#L211](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPodManager.sol#L211)

- [EigenPod.sol#L153](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPod.sol#L153)

- [Pausable.sol#L57](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/permissions/Pausable.sol#L57) 2 instances

- [PauserRegistry.sol#L42](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/permissions/PauserRegistry.sol#L42)
- [PauserRegistry.sol#L48](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/permissions/PauserRegistry.sol#L48)

- [DelayedWithdrawalRouter.sol#L45](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/DelayedWithdrawalRouter.sol#L45)


## 13. before some functions we should check some variables for possible gas save

before transfer we should check for amount being 0 so the function doesnt run when its not gonna do anything

`amount` should be checked
- [StrategyManager.sol#L661](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L661)


## 14. instead of calculating keccak256() every time the constant is used pre calculate them before and only give the result to a constant

stops usage of a keccak256() every time the constant is used

- [StrategyManagerStorage.sol#L17](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManagerStorage.sol#L17)
- [StrategyManagerStorage.sol#L20](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManagerStorage.sol#L20)


## 15. Non-strict inequalities are cheaper than strict ones

In the EVM, there is no opcode for non-strict inequalities (>=, <=) and two operations are performed (> + = or < + =).
consider replacing >= with the strict counterpart > and add `- 1` to the second side

- [StrategyManager.sol#L263](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L263)
- [StrategyManager.sol#L690](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L690)
- [StrategyManager.sol#L761](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L761)
- [StrategyManager.sol#L840](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L840)

- [StrategyBase.sol#L106](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/strategies/StrategyBase.sol#L106)
- [StrategyBase.sol#L132](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/strategies/StrategyBase.sol#L132)
- [StrategyBase.sol#L139](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/strategies/StrategyBase.sol#L139)

- [EigenPod.sol#L195](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPod.sol#L195)
- [EigenPod.sol#L249](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPod.sol#L249)
- [EigenPod.sol#L373](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPod.sol#L373)
- [EigenPod.sol#L387](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPod.sol#L387)
- [EigenPod.sol#L354](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPod.sol#L354)

- [DelayedWithdrawalRouter.sol#L133](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/DelayedWithdrawalRouter.sol#L133) 2 instances
- [DelayedWithdrawalRouter.sol#L167](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/DelayedWithdrawalRouter.sol#L167)

- [Merkle.sol#L50](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/libraries/Merkle.sol#L50)
- [Merkle.sol#L101](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/libraries/Merkle.sol#L101)


## 16. part of the code can be pre calculated

these parts of the code can be pre calculated and given to the contract as constants this will stop the use of extra operations
even if its for code readability consider putting comments instead.

`keccak256(bytes("EigenLayer"))` can be precalculated to stop using a extra keccak256 operation
- [StrategyManager.sol#L150](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L150)
- [StrategyManager.sol#L276](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L276)

`2**BEACON_BLOCK_HEADER_FIELD_TREE_HEIGHT`
- [BeaconChainProofs.sol#L131](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/libraries/BeaconChainProofs.sol#L131)

`2**BEACON_STATE_FIELD_TREE_HEIGHT`
- [BeaconChainProofs.sol#L141](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/libraries/BeaconChainProofs.sol#L141)

`2**VALIDATOR_FIELD_TREE_HEIGHT`
- [BeaconChainProofs.sol#L151](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/libraries/BeaconChainProofs.sol#L151)

`2**ETH1_DATA_FIELD_TREE_HEIGHT`
- [BeaconChainProofs.sol#L161](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/libraries/BeaconChainProofs.sol#L161)
- [BeaconChainProofs.sol#L199](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/libraries/BeaconChainProofs.sol#L199)

`32 * ((VALIDATOR_TREE_HEIGHT + 1) + BEACON_STATE_FIELD_TREE_HEIGHT)`
- [BeaconChainProofs.sol#L205](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/libraries/BeaconChainProofs.sol#L205)

`32 * ((BALANCE_TREE_HEIGHT + 1) + BEACON_STATE_FIELD_TREE_HEIGHT`
- [BeaconChainProofs.sol#L227](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/libraries/BeaconChainProofs.sol#L227)

`2**BLOCK_ROOTS_TREE_HEIGHT`
- [BeaconChainProofs.sol#L252](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/libraries/BeaconChainProofs.sol#L252)

`2**WITHDRAWAL_FIELD_TREE_HEIGHT`
- [BeaconChainProofs.sol#L250](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/libraries/BeaconChainProofs.sol#L250)

`2**WITHDRAWALS_TREE_HEIGHT`
- [BeaconChainProofs.sol#L253](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/libraries/BeaconChainProofs.sol#L253)

`32 * (BEACON_STATE_FIELD_TREE_HEIGHT + BLOCK_ROOTS_TREE_HEIGHT)`
- [BeaconChainProofs.sol#L256](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/libraries/BeaconChainProofs.sol#L256)

`32 * (EXECUTION_PAYLOAD_HEADER_FIELD_TREE_HEIGHT + WITHDRAWALS_TREE_HEIGHT + 1)`
- [BeaconChainProofs.sol#L258](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/libraries/BeaconChainProofs.sol#L258)

`32 * (BEACON_BLOCK_HEADER_FIELD_TREE_HEIGHT + BEACON_BLOCK_BODY_FIELD_TREE_HEIGHT)`
- [BeaconChainProofs.sol#L260](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/libraries/BeaconChainProofs.sol#L260)


## 17. Use selfbalance() instead of address(this).balance

Use assembly when getting a contract's balance of ETH.

You can use selfbalance() instead of address(this).balance when getting your contract's balance of ETH to save gas. Additionally, you can use balance(address) instead of address.balance() when getting an external contract's balance of ETH.

Saves 15 gas when checking internal balance, 6 for external

- [EigenPod.sol#L456](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPod.sol#L456)


## 18. state variables can be sorted better to reduce gas usage

If variables occupying the same slot are both written the same function or by the constructor, avoids a separate Gsset (20000 gas). Reads of the variables are also cheaper. saves ~84 gas

`podOwner` and `mostRecentWithdrawalBlockNumber` are in the same slot and `restakedExecutionLayerGwei` and `hasRestaked` are in another slot together. but this is not the most optimal way and we can bring `hasRestaked` near `podOwner` because they are used in the same function twice and these reads gonna be cheaper. `restakedExecutionLayerGwei` and `hasRestaked` are never both accessed in the same function.
so bring `hasRestaked` #L73 near `podOwner` #L59 and `mostRecentWithdrawalBlockNumber` #L66

- [EigenPod.sol#L59-L73](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPod.sol#L59-L73)
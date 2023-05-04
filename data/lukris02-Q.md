# QA Report for EigenLayer contest
## Overview
During the audit, 5 low and 11 non-critical issues were found.

№ | Title | Risk Rating  | Instance Count
--- | --- | --- | ---
L-1 | Misleading comment | Low | 1
L-2 | Event should be emitted | Low | 2
L-3 | Add a timelock to critical functions | Low | 5
L-4 | Use SafeCast Library | Low | 2
L-5 | _userWithdrawals[recipient].delayedWithdrawals can only be increased | Low | 1
NC-1 | Use mixedCase for functions, state variables and modifiers | Non-Critical | 4
NC-2 | Wrong modifier order in  function declaration | Non-Critical | 1
NC-3 | Visibility is not set | Non-Critical | 1
NC-4 | Lack of event emission in initialize() function | Non-Critical | 5
NC-5 | Function parameter descriptions are in the wrong order. | Non-Critical | 4
NC-6 | No space between the control structures | Non-Critical | 4
NC-7 | Inconsistency when using uint and uint256 | Non-Critical | 2
NC-8 | Prevent zero transfers | Non-Critical | 4
NC-9 | Drop each function argument onto its own line | Non-Critical | 4
NC-10 | Explicitly import ```@openzeppelin/contracts/utils/AddressUpgradeable.sol``` | Non-Critical | 1
NC-11 | Missing leading underscores | Non-Critical | 18

## Low Risk Findings(5)
### L-1. Misleading comment
##### Description
There are two different descriptions for the function parameter ```amount```.
##### Instances
- https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/interfaces/IStrategyManager.sol#L51-L52

##### Recommendation
Extra description should be removed.
#
### L-2. Event should be emitted
##### Description
The governor functions that change critical parameters should emit events. Events allow capturing the changed parameters so that off-chain tools/interfaces can register such changes and allow users to evaluate them and consider if they would like to engage/exit based on how they perceive the changes as affecting the trustworthiness of the protocol or profitability of the implemented financial services. The alternative of directly querying on-chain contract state for such changes is not considered practical for most users/usages.
##### Instances
- [```function slashShares(```](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L482) 
- [```function slashQueuedWithdrawal(address recipient, QueuedWithdrawal calldata queuedWithdrawal, IERC20[] calldata tokens, uint256[] calldata indicesToSkip)```](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L536) 

##### Recommendation
Consider adding events for these functions.
#
### L-3. Add a timelock to critical functions
##### Description
Giving users time to react and adjust to critical changes in protocol provides more guarantees and increases the transparency of the protocol.
##### Instances
- [```function setWithdrawalDelayBlocks(uint256 _withdrawalDelayBlocks) external onlyOwner {```](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L582) 
- [```function setStrategyWhitelister(address newStrategyWhitelister) external onlyOwner {```](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L587) 
- [```function setWithdrawalDelayBlocks(uint256 newValue) external onlyOwner {```](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/DelayedWithdrawalRouter.sol#L100) 
- [```function setPauser(address newPauser) external onlyUnpauser {```](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/permissions/PauserRegistry.sol#L32) 
- [```function setUnpauser(address newUnpauser) external onlyUnpauser {```](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/permissions/PauserRegistry.sol#L37) 

##### Recommendation
Consider adding a timelock.
#
### L-4. Use SafeCast Library
##### Description
Downcasting from uint256/int256 in Solidity does not revert on overflow. This can easily result in undesired exploitation or bugs, since developers usually assume that overflows raise errors. SafeCast restores this intuition by reverting the transaction when such an operation overflows.
##### Instances
- [```REQUIRED_BALANCE_GWEI = uint64(_REQUIRED_BALANCE_WEI / GWEI_TO_WEI);```](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPod.sol#L146)
- [```restakedExecutionLayerGwei -= uint64(amountWei / GWEI_TO_WEI);```](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPod.sol#L445) 

##### Recommendation
It is better to use [safe casting library](https://docs.openzeppelin.com/contracts/3.x/api/utils#SafeCast).
#
### L-5. _userWithdrawals[recipient].delayedWithdrawals can only be increased
##### Description
```_userWithdrawals[recipient].delayedWithdrawals``` can only be increased. There is ```push``` but no ```pop``` or ```delete```. This can cause out-of-gas error in loops.
##### Instances
- [```_userWithdrawals[recipient].delayedWithdrawals.push(delayedWithdrawal);```](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/DelayedWithdrawalRouter.sol#L66) 

##### Recommendation
Consider implementing a function that allows reducing ```_userWithdrawals[recipient].delayedWithdrawals```.
#
## Non-Critical Risk Findings(11)
### NC-1. Use mixedCase for functions, state variables and modifiers
##### Description
According to [Naming Conventions](https://docs.soliditylang.org/en/v0.8.19/style-guide.html#naming-conventions), functions, state variables, and modifiers should use mixedCase ([See1](https://docs.soliditylang.org/en/v0.8.19/style-guide.html#function-names), ([See2](https://docs.soliditylang.org/en/v0.8.19/style-guide.html#local-and-state-variable-names),  and [See3](https://docs.soliditylang.org/en/v0.8.19/style-guide.html#modifier-names)).
##### Instances
- [```uint256 _REQUIRED_BALANCE_WEI```](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPod.sol#L140)
- [```event DepositEvent(bytes pubkey, bytes withdrawal_credentials, bytes amount, bytes signature, bytes index);```](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/interfaces/IETHPOSDeposit.sol#L19) (withdrawal_credentials)
- [```bytes calldata withdrawal_credentials,```](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/interfaces/IETHPOSDeposit.sol#L29)
- [```bytes32 deposit_data_root```](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/interfaces/IETHPOSDeposit.sol#L31)

##### Recommendation
For example, change to:
```uint256 requiredBalanceWei```
#
### NC-2. Wrong modifier order in  function declaration
##### Description
According to [Style Guide](https://docs.soliditylang.org/en/v0.8.19/style-guide.html#function-declaration), the modifier order for a function should be:
1. Visibility
2. Mutability
3. Virtual
4. Override
5. Custom modifiers

##### Instances
- [```function _completeQueuedWithdrawal(QueuedWithdrawal calldata queuedWithdrawal, IERC20[] calldata tokens, uint256 middlewareTimesIndex, bool receiveAsTokens) onlyNotFrozen(queuedWithdrawal.delegatedAddress) internal```](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L745) 

##### Recommendation
Change to ```function _completeQueuedWithdrawal(QueuedWithdrawal calldata queuedWithdrawal, IERC20[] calldata tokens, uint256 middlewareTimesIndex, bool receiveAsTokens) internal onlyNotFrozen(queuedWithdrawal.delegatedAddress)```.
#
### NC-3. Visibility is not set
##### Instances
- [```uint256 immutable ORIGINAL_CHAIN_ID;```](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L42) 

##### Recommendation
It is better to specify visibility explicitly.
#
### NC-4. Lack of event emission in initialize() function
##### Description
Lack of event emission complicates recording the init parameters for off-chain monitoring and reduces transparency.
##### Instances
- [```function initialize(address initialOwner, address initialStrategyWhitelister, IPauserRegistry _pauserRegistry, uint256 initialPausedStatus, uint256 _withdrawalDelayBlocks)```](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L146) 
- [```function initialize(IERC20 _underlyingToken, IPauserRegistry _pauserRegistry) public virtual initializer {```](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/strategies/StrategyBase.sol#L51) 
- [```function initialize(```](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPodManager.sol#L84) 
- [```function initialize(address _podOwner) external initializer {```](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPod.sol#L152) 
- [```function initialize(address initOwner, IPauserRegistry _pauserRegistry, uint256 initPausedStatus, uint256 _withdrawalDelayBlocks) external initializer {```](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/DelayedWithdrawalRouter.sol#L49) 

##### Recommendation
Consider emitting an event in the initialize() function
#
### NC-5. Function parameter descriptions are in the wrong order.
##### Description
The order of the description of the parameters does not match the order of the parameters in the function.
##### Instances
- [```* @param strategy Is the strategy that `depositor` has deposited into.```](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L50) (```strategy``` should be after ```token```)
- [```* @param _slasher The primary slashing contract of EigenLayer.```](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L126) (```_slasher``` should be after ```_eigenPodManager```)
- [```* @param strategyIndexes is a list of the indices in `stakerStrategyList[msg.sender]` that correspond to the strategies```](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L474) (```strategyIndexes``` should be after ```recipient```)
- [```* @param strategyIndexes is a list of the indices in `stakerStrategyList[msg.sender]` that correspond to the strategies```](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/interfaces/IStrategyManager.sol#L170) (```strategyIndexes``` should be after ```recipient```)

##### Recommendation
Change the order.
#
### NC-6. No space between the control structures
##### Description
According to [Style Guide](https://docs.soliditylang.org/en/v0.8.16/style-guide.html#control-structures), there should be a single space between the control structures ```if```, ```while```, and ```for``` and the parenthetic block representing the conditional.
##### Instances
- [```for(uint256 i = 0; i < queuedWithdrawals.length; i++) {```](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L466) 
- [```if(address(pod) == address(0)) {```](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPodManager.sol#L114) 
- [```if(index % 2 == 0) {```](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/libraries/Merkle.sol#L51) 
- [```if(index % 2 == 0) {```](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/libraries/Merkle.sol#L102) 

##### Recommendation
Change:
```
if(...) {
    ...
}
```
to:
```
if (...) {
    ...
}
```
#
### NC-7. Inconsistency when using uint and uint256
##### Description
Some variables is declared as ```uint``` and some as ```uint256```.
##### Instances
- [```for (uint i = 0; i < numNodesInLayer; i++) {```](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/libraries/Merkle.sol#L137)
- [```for (uint i = 0; i < numNodesInLayer; i++) {```](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/libraries/Merkle.sol#L145) 
- in other cases ```uint256``` is used

##### Recommendation
Stick to one style.
#
### NC-8. Prevent zero transfers
##### Description
Check that amount to transfer > 0.
##### Instances
- [```shares = _depositIntoStrategy(msg.sender, strategy, token, amount);```](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L227) 
- [```shares = _depositIntoStrategy(staker, strategy, token, amount);```](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L297) 
- [```token.safeTransferFrom(msg.sender, address(strategy), amount);```](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L661) 
- [```underlyingToken.safeTransfer(depositor, amountToSend);```](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/strategies/StrategyBase.sol#L155) 

#
### NC-9. Drop each function argument onto its own line
##### Description
According to [Style Guide](https://docs.soliditylang.org/en/v0.8.19/style-guide.html#function-declaration), for long function declarations, it is recommended to drop each argument onto its own line at the same indentation level as the function body. The closing parenthesis and opening bracket should be placed on their own line as well at the same indentation level as the function declaration.
##### Instances
- [```function initialize(address initialOwner, address initialStrategyWhitelister, IPauserRegistry _pauserRegistry, uint256 initialPausedStatus, uint256 _withdrawalDelayBlocks)```](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L146) 
- [```function completeQueuedWithdrawal(QueuedWithdrawal calldata queuedWithdrawal, IERC20[] calldata tokens, uint256 middlewareTimesIndex, bool receiveAsTokens)```](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L442) 
- [```function slashQueuedWithdrawal(address recipient, QueuedWithdrawal calldata queuedWithdrawal, IERC20[] calldata tokens, uint256[] calldata indicesToSkip)```](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L536) 
- [```function _completeQueuedWithdrawal(QueuedWithdrawal calldata queuedWithdrawal, IERC20[] calldata tokens, uint256 middlewareTimesIndex, bool receiveAsTokens) onlyNotFrozen(queuedWithdrawal.delegatedAddress) internal {```](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L745) 

#
### NC-10. Explicitly import ```@openzeppelin/contracts/utils/AddressUpgradeable.sol```
##### Description
Although ```@openzeppelin/contracts/proxy/utils/Initializable.sol``` imports ```"../../utils/AddressUpgradeable.sol"```, a good coding practice is explicitly import  ```@openzeppelin/contracts/utils/AddressUpgradeable.sol``` when using it.
##### Instances
- [```AddressUpgradeable.sendValue(payable(recipient), amountToSend);```](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/DelayedWithdrawalRouter.sol#L160) 

##### Recommendation
Add ```import "@openzeppelin-upgrades/contracts/proxy/utils/AddressUpgradeable.sol;```
#
### NC-11. Missing leading underscores 
##### Description
Internal and private constants, immutables and functions should have a leading underscore.
##### Instances
- [```uint256 internal constant GWEI_TO_WEI = 1e9;```](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L35) 
- [```uint8 internal constant PAUSED_DEPOSITS = 0;```](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L38) 
- [```uint8 internal constant PAUSED_WITHDRAWALS = 1;```](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L40) 
- [```uint256 immutable ORIGINAL_CHAIN_ID;```](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L42) 
- [```bytes4 constant internal ERC1271_MAGICVALUE = 0x1626ba7e;```](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L45) 
- [```uint8 internal constant MAX_STAKER_STRATEGY_LIST_LENGTH = 32;```](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManagerStorage.sol#L28) 
- [```uint8 internal constant PAUSED_DEPOSITS = 0;```](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/strategies/StrategyBase.sol#L22) 
- [```uint8 internal constant PAUSED_WITHDRAWALS = 1;```](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/strategies/StrategyBase.sol#L23) 
- [```uint96 internal constant MIN_NONZERO_TOTAL_SHARES = 1e9;```](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/strategies/StrategyBase.sol#L28) 
- [```bytes internal constant beaconProxyBytecode = ...;```](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPodManager.sol#L37) 
- [```uint256 internal constant GWEI_TO_WEI = 1e9;```](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPod.sol#L38) 
- [```uint256 internal constant VERIFY_OVERCOMMITTED_WINDOW_BLOCKS = 50400;```](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPod.sol#L41) 
- [```uint8 internal constant PAUSED_NEW_EIGENPODS = 0;```](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPodPausingConstants.sol#L10) 
- [```uint8 internal constant PAUSED_WITHDRAW_RESTAKED_ETH = 1;```](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPodPausingConstants.sol#L12) 
- [```uint8 internal constant PAUSED_EIGENPODS_VERIFY_CREDENTIALS = 2;```](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPodPausingConstants.sol#L15) 
- [```uint8 internal constant PAUSED_EIGENPODS_VERIFY_OVERCOMMITTED = 3;```](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPodPausingConstants.sol#L17) 
- [```uint8 internal constant PAUSED_EIGENPODS_VERIFY_WITHDRAWAL = 4;```](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPodPausingConstants.sol#L19) 
- [```uint8 internal constant PAUSED_DELAYED_WITHDRAWAL_CLAIMS = 0;```](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/DelayedWithdrawalRouter.sol#L16) 

##### Recommendation
Add leading underscores where needed.
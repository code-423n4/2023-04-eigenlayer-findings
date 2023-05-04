# Report
## Low Risk ##
### [L-1]: Use Address.sendValue() without import @openzeppelin/contracts/utils/Address.sol
**Context:**

1. ```AddressUpgradeable.sendValue(payable(recipient), amountToSend);``` [L160](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/DelayedWithdrawalRouter.sol#L160) 

**Description:**

Although @openzeppelin-upgrades/contracts/proxy/utils/Initializable.sol is imported in DelayedWithdrawalRouter.sol. It is a good coding practice to import @openzeppelin/contracts/utils/Address.sol when it is used.

**Recommendation:**

Add import @openzeppelin/contracts/utils/Address.sol.

### [L-2]: Use Ownable2StepUpgradeable instead of OwnableUpgradeable contract
**Context:**

1. ```import "@openzeppelin-upgrades/contracts/access/OwnableUpgradeable.sol";``` [L7](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L7) 

**Description:**

There is another Openzeppelin Ownable contract Ownable2StepUpgradeable.sol. It has transferOwnership function, but it is more secure due to 2-stage ownership transfer.

### [L-3]: Misleading comment
**Context:**
1. ```* @param amount is the amount of beaconchain ETH being restaked,``` [L51](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/interfaces/IStrategyManager.sol#L51)
1. ```* @param amount is the amount of token to be deposited in the strategy by the depositor``` [L52](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/interfaces/IStrategyManager.sol#L52)

### [L-4]: Event should be emitted
**Context:**
1. ```function slashShares(``` [L482](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L482) 
1. ```function slashQueuedWithdrawal(address recipient, QueuedWithdrawal calldata queuedWithdrawal, IERC20[] calldata tokens, uint256[] calldata indicesToSkip)``` [L536](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L536) 

**Description:**
Events are generally emitted when sensitive changes are made to the contracts.

**Recommendation:**
Consider adding events for these functions.

### [L-5]: Unsafe casting may overflow
**Context:**
 1. ```REQUIRED_BALANCE_GWEI = uint64(_REQUIRED_BALANCE_WEI / GWEI_TO_WEI);```[L146](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPod.sol#L146)
1. ```restakedExecutionLayerGwei -= uint64(amountWei / GWEI_TO_WEI);```[L445](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPod.sol#L445) 

**Description:**
While Solidity 0.8.x checks for overflows on arithmetic operations, it does not do so for casting.

**Recommendation:**
Use OpenZeppelin’s SafeCast library to prevent unexpected overflows.

## Non-Critical Issues ##
### [N-1]: Splitting require() statements
**Context:**

1. ```address(pauserRegistry) == address(0) && address(_pauserRegistry) != address(0),``` [L57](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/permissions/Pausable.sol#L57)

**Description:**

Only ```address(pauserRegistry) == address(0)``` check that _initializePauser() can only be called once. Another condition```address(_pauserRegistry) != address(0)``` check input value and this does not apply to the message "Pausable._initializePauser: _initializePauser() can only be called once".

**Recommendation:**
Change:
```
require(
            address(pauserRegistry) == address(0) && address(_pauserRegistry) != address(0),
            "Pausable._initializePauser: _initializePauser() can only be called once"
        );
```

To:
```
require(address(pauserRegistry) == address(0), "Pausable._initializePauser: _initializePauser() can only be called once");
require(address(_pauserRegistry) != address(0), "Pausable._initializePauser: _pauserRegistry cannot be address(0x0)"))
```

### [N-2]: Wrong order of NatSpec param tags.
**Context:**

1. ```* @param strategy Is the strategy that `depositor` has deposited into.``` [L50](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L50) (swap strategy and token)
1. ```* @param _slasher The primary slashing contract of EigenLayer.``` [L126](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L126) (swap _slasher and _eigenPodManager)
1. ```* @param strategyIndexes is a list of the indices in `stakerStrategyList[msg.sender]` that correspond to the strategies``` [L474](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L474) (swap strategyIndexes and recipient)
1. ```* @param strategyIndexes is a list of the indices in `stakerStrategyList[msg.sender]` that correspond to the strategies``` [L170](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/interfaces/IStrategyManager.sol#L170) (strategyIndexes must be after recipient)

### [N-3]: Visibility modifier must be first in list of modifiers
**Context:**

1. ```function _completeQueuedWithdrawal(QueuedWithdrawal calldata queuedWithdrawal, IERC20[] calldata tokens, uint256 middlewareTimesIndex, bool receiveAsTokens) onlyNotFrozen(queuedWithdrawal.delegatedAddress) internal {``` [L745](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L745)

**Recommendation:**
Change to 
```function _completeQueuedWithdrawal(QueuedWithdrawal calldata queuedWithdrawal, IERC20[] calldata tokens, uint256 middlewareTimesIndex, bool receiveAsTokens) internal onlyNotFrozen(queuedWithdrawal.delegatedAddress) {```

### [N-4]: Use of immutable instead of constant keccak expression
**Context:**

1. ```bytes32 public constant DOMAIN_TYPEHASH =``` [L17](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManagerStorage.sol#L17) 
1. ```bytes32 public constant DEPOSIT_TYPEHASH =``` [L20](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManagerStorage.sol#L20) 

**Description:**

According to [official solidity documentation](https://docs.soliditylang.org/en/v0.8.17/contracts.html#constant-and-immutable-state-variables) for a constant variable, the expression assigned to it is copied to all the places where it is accessed and also re-evaluated each time. It is recommended to use immutable instead. 

### [N-5]: Follow Solidity standard naming conventions
**Context:**

1. ```uint256 _REQUIRED_BALANCE_WEI``` [L140](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPod.sol#L140) (function param name must be in mixedCase)
1. ```event DepositEvent(bytes pubkey, bytes withdrawal_credentials, bytes amount, bytes signature, bytes index);``` [L19](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/interfaces/IETHPOSDeposit.sol#L19) (withdrawal_credentials - function param name must be in mixedCase)
1. ```bytes calldata withdrawal_credentials,``` [L29](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/interfaces/IETHPOSDeposit.sol#L29) (function param name must be in mixedCase)
1. ```bytes32 deposit_data_root``` [L31](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/interfaces/IETHPOSDeposit.sol#L31) (function param name must be in mixedCase)

**Description:**

The above codes don't follow [Solidity's standard naming convention](https://docs.soliditylang.org/en/v0.8.17/style-guide.html#naming-conventions).  
- Structs should be named using the CapWords style. Examples: MyCoin, Position, PositionXY.
- Events should be named using the CapWords style. Examples: Deposit, Transfer, Approval, BeforeTransfer, AfterTransfer.
- Functions should use mixedCase. Examples: getBalance, transfer, verifyOwner, addMember, changeOwner.
- Function arguments should use mixedCase. Examples: initialSupply, account, recipientAddress, senderAddress, newOwner.
- Local and State Variable should use mixedCase. Examples: totalSupply, remainingSupply, balancesOf, creatorAddress, isPreSale, tokenExchangeRate.
- Constants should be named with all capital letters with underscores separating words. Examples: MAX_BLOCKS, TOKEN_NAME, TOKEN_TICKER, CONTRACT_VERSION.
- Modifier should use mixedCase. Examples: onlyBy, onlyAfter, onlyDuringThePreSale.
- Enums, in the style of simple type declarations, should be named using the CapWords style. Examples: TokenGroup, Frame, HashStyle, CharacterLocation.

### [N-6]: Add a timelock to critical functions
**Context:**

1. ```function setWithdrawalDelayBlocks(uint256 _withdrawalDelayBlocks) external onlyOwner {``` [L582](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L582) 
1. ```function setStrategyWhitelister(address newStrategyWhitelister) external onlyOwner {``` [L587](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L587) 
1. ```function setWithdrawalDelayBlocks(uint256 newValue) external onlyOwner {``` [L100](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/DelayedWithdrawalRouter.sol#L100) 
1. ```function setPauser(address newPauser) external onlyUnpauser {``` [L32](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/permissions/PauserRegistry.sol#L32) 
1. ```function setUnpauser(address newUnpauser) external onlyUnpauser {``` [L37](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/permissions/PauserRegistry.sol#L37) 

**Description:**

It is a good practice to give time for users to react and adjust to critical changes. It also indicates that the project is legitimate (less risk of a malicious owner making a sandwich attack on a user). 

### [N-7]: Missing leading underscores
**Context:**

1. ```uint256 internal constant GWEI_TO_WEI = 1e9;``` [L35](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L35) 
1. ```uint8 internal constant PAUSED_DEPOSITS = 0;``` [L38](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L38) 
1. ```uint8 internal constant PAUSED_WITHDRAWALS = 1;``` [L40](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L40) 
1. ```uint256 immutable ORIGINAL_CHAIN_ID;``` [L42](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L42) 
 1. ```bytes4 constant internal ERC1271_MAGICVALUE = 0x1626ba7e;``` [L45](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L45) 
1. ```uint8 internal constant MAX_STAKER_STRATEGY_LIST_LENGTH = 32;``` [L28](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManagerStorage.sol#L28) 
1. ```uint8 internal constant PAUSED_DEPOSITS = 0;``` [L22](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/strategies/StrategyBase.sol#L22) 
1. ```uint8 internal constant PAUSED_WITHDRAWALS = 1;``` [L23](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/strategies/StrategyBase.sol#L23) 
1. ```uint96 internal constant MIN_NONZERO_TOTAL_SHARES = 1e9;``` [L28](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/strategies/StrategyBase.sol#L28) 
1. ```uint256 internal constant GWEI_TO_WEI = 1e9;``` [L38](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPod.sol#L38) 
1. ```uint256 internal constant VERIFY_OVERCOMMITTED_WINDOW_BLOCKS = 50400;``` [L41](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPod.sol#L41) 
1. ```uint8 internal constant PAUSED_NEW_EIGENPODS = 0;``` [L10](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPodPausingConstants.sol#L10) 
1. ```uint8 internal constant PAUSED_WITHDRAW_RESTAKED_ETH = 1;``` [L12](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPodPausingConstants.sol#L12) 
1. ```uint8 internal constant PAUSED_EIGENPODS_VERIFY_CREDENTIALS = 2;``` [L15](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPodPausingConstants.sol#L15) 
1. ```uint8 internal constant PAUSED_EIGENPODS_VERIFY_OVERCOMMITTED = 3;``` [L17](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPodPausingConstants.sol#L17) 
1. ```uint8 internal constant PAUSED_EIGENPODS_VERIFY_WITHDRAWAL = 4;``` [L19](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPodPausingConstants.sol#L19) 
1. ```uint8 internal constant PAUSED_DELAYED_WITHDRAWAL_CLAIMS = 0;``` [L16](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/DelayedWithdrawalRouter.sol#L16) 


**Description:**

Internal and private functions, state variables, constants, and immutables should starting with an underscore.

### [N-8]: Mark visibility of variables
**Context:**

1. ```uint256 immutable ORIGINAL_CHAIN_ID;``` [L42](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L42) 

### [N-9]: Missing events in initialize() functions
**Context:**

1. ```function initialize(address initialOwner, address initialStrategyWhitelister, IPauserRegistry _pauserRegistry, uint256 initialPausedStatus, uint256 _withdrawalDelayBlocks)``` [L146](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L146) 
1. ```function initialize(IERC20 _underlyingToken, IPauserRegistry _pauserRegistry) public virtual initializer {``` [L51](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/strategies/StrategyBase.sol#L51) 
1. ```function initialize(``` [L84](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPodManager.sol#L84) 
1. ```function initialize(address _podOwner) external initializer {``` [L152](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPod.sol#L152) 
1. ```function initialize(address initOwner, IPauserRegistry _pauserRegistry, uint256 initPausedStatus, uint256 _withdrawalDelayBlocks) external initializer {``` [L49](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/DelayedWithdrawalRouter.sol#L49) 

**Description:**

Events ara missing in initialize() functions for off-chain monitoring tools to monitor this on-chain change. 

**Recommendation:**

Add an init event and emit that at the end of init() function.

### [N-10]: Recommendations for long function declarations
**Context:**

1. ```function initialize(address initialOwner, address initialStrategyWhitelister, IPauserRegistry _pauserRegistry, uint256 initialPausedStatus, uint256 _withdrawalDelayBlocks)``` [L146](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L146) 
1. ```function completeQueuedWithdrawal(QueuedWithdrawal calldata queuedWithdrawal, IERC20[] calldata tokens, uint256 middlewareTimesIndex, bool receiveAsTokens)``` [L442](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L442) 
1. ```function slashQueuedWithdrawal(address recipient, QueuedWithdrawal calldata queuedWithdrawal, IERC20[] calldata tokens, uint256[] calldata indicesToSkip)``` [L536](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L536) 
1. ```function _completeQueuedWithdrawal(QueuedWithdrawal calldata queuedWithdrawal, IERC20[] calldata tokens, uint256 middlewareTimesIndex, bool receiveAsTokens) onlyNotFrozen(queuedWithdrawal.delegatedAddress) internal {``` [L745](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L745) 

**Description:**

According to [official solidity documentation](https://docs.soliditylang.org/en/v0.8.19/style-guide.html#function-declaration):
1. For long function declarations, it is recommended to drop each argument onto its own line at the same indentation level as the function body. The closing parenthesis and opening bracket should be placed on their own line as well at the same indentation level as the function declaration.
1. If a long function declaration has modifiers, then each modifier should be dropped to its own line.

### [N-11]: Tags param for events are missing
**Context:**

1. ```/// @notice Emitted when a queued withdrawal is completed``` [L81](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L81)
1. ```/// @notice Emitted when the `strategyWhitelister` is changed``` [L84](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L84)
1. ```/// @notice Emitted when a strategy is added to the approved list of strategies for deposit``` [L87](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L87) 
1. ```/// @notice Emitted when a strategy is removed from the approved list of strategies for deposit``` [L90](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L90) 
1. ```/// @notice Emitted when the `withdrawalDelayBlocks` variable is modified from `previousValue` to `newValue`.``` [L93](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L93) 

**Description:**
Missing in Automated findings.

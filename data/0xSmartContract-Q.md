## Summary
### Issues List
| Number |Issues Details|Context|
|:--:|:-------|:--:|
|L-01|The duration of a block on the Ethereum network is not exactly 12 seconds| 2 |
|L-02|Missing Event for  initialize | 9 |
|L-03|It is more appropriate to use `AddressUpgradeable.sol` instead of OpenZeppelin `Address.sol` import for the project. | 1 |
|N-04|Repeated validation logic can be converted into a function modifier|9|
|N-05|Use a single file for all system-wide constants| 73 |
|N-06|Imports should be sorted in alphabetical order (Solidity Stle Guide) | All Contracts|
|N-07|For functions, follow Solidity standard naming conventions (internal function style rule)| 9 |

Total 7 issues


### [L-01] The duration of a block on the Ethereum network is not exactly 12 seconds.

The value `MAX_WITHDRAWAL_DELAY_BLOCKS` is used to control the upper digit of the maximum delay time;

```solidity


src/contracts/core/StrategyManagerStorage.sol:
  44      uint256 public withdrawalDelayBlocks;
  45:     // the number of 12-second blocks in one week (60 * 60 * 24 * 7 / 12 = 50,400)
  46:     uint256 public constant MAX_WITHDRAWAL_DELAY_BLOCKS = 50400;
  47  

src/contracts/pods/DelayedWithdrawalRouter.sol:
  22      uint256 public withdrawalDelayBlocks;
  23:     // the number of 12-second blocks in one week (60 * 60 * 24 * 7 / 12 = 50,400)
  24:     uint256 public constant MAX_WITHDRAWAL_DELAY_BLOCKS = 50400;


```


The value `MAX_WITHDRAWAL_DELAY_BLOCKS` is used to control the upper digit of the maximum delay time. Used in the `_setWithdrawalDelayBlocks()` function;

```solidity

src/contracts/pods/DelayedWithdrawalRouter.sol:
  165      /// @notice internal function for changing the value of `withdrawalDelayBlocks`. Also performs sanity check and emits an event.
  166:     function _setWithdrawalDelayBlocks(uint256 newValue) internal {
  167:         require(newValue <= MAX_WITHDRAWAL_DELAY_BLOCKS, "DelayedWithdrawalRouter._setWithdrawalDelayBlocks: newValue too large");
  168:         emit WithdrawalDelayBlocksSet(withdrawalDelayBlocks, newValue);
  169:         withdrawalDelayBlocks = newValue;
  170:     }

```


An Ethereum block time is calculated as a maximum of 12 seconds but not exactly, a block time on the Ethereum network is higher (12.20 seconds these days), a deviation of about 10% is above average, This should be mentioned in the NatSpec comments and Documentation;


```js
Date	            Value
April 30, 2023	    12.10
April 29, 2023	    12.11
April 28, 2023	    12.10
April 27, 2023	    12.11
April 26, 2023	    12.10
April 25, 2023	    12.10
April 24, 2023	    12.09
April 23, 2023	    12.09
April 22, 2023	    12.09
April 21, 2023	    12.17
April 20, 2023	    12.20

```

[ycharts.com](https://ycharts.com/indicators/ethereum_average_block_time#:~:text=Ethereum%20Average%20Block%20Time%20is,9.30%25%20from%20one%20year%20ago.)


If there is an increase in the block times in the future, the 7-day waiting period loses its meaning as the waiting time definition in the project cannot exceed 12 seconds.


**Recommendation:**

The `setWithdrawalDelayBlocks()` function allows the wait time to be determined by `onlyOwner`, and the `MAX_WITHDRAWAL_DELAY_BLOCKS` value should be updated according to future developments in the Ethereum network


```diff
src/contracts/pods/DelayedWithdrawalRouter.sol:
   99      /// @notice Owner-only function for modifying the value of the `withdrawalDelayBlocks` variable.
  100:     function setWithdrawalDelayBlocks(uint256 newValue) external onlyOwner {
  101:         _setWithdrawalDelayBlocks(newValue);
  102:     }


+ 	function setMax_Withdrawal_Delay_Blocks(uint256 newValue) external onlyOwner {
+	    _setMax_Withdrawal_Delay_Blocks(newValue);
+	 }



```

### [L-02] Missing Event for  initialize

**Context:**
```solidity

9 results

src/contracts/core/StrategyManager.sol:
  145       */
  146:     function initialize(address initialOwner, address initialStrategyWhitelister, IPauserRegistry _pauserRegistry, uint256 initialPausedStatus, uint256 _withdrawalDelayBlocks)
  147:         external
  148:         initializer
  149:     {
  150:         DOMAIN_SEPARATOR = keccak256(abi.encode(DOMAIN_TYPEHASH, keccak256(bytes("EigenLayer")), ORIGINAL_CHAIN_ID, address(this)));
  151:         _initializePauser(_pauserRegistry, initialPausedStatus);
  152:         _transferOwnership(initialOwner);
  153:         _setStrategyWhitelister(initialStrategyWhitelister);
  154:         _setWithdrawalDelayBlocks(_withdrawalDelayBlocks);
  155:     }

src/contracts/pods/DelayedWithdrawalRouter.sol:
  48  
  49:     function initialize(address initOwner, IPauserRegistry _pauserRegistry, uint256 initPausedStatus, uint256 _withdrawalDelayBlocks) external initializer {
  50:         _transferOwnership(initOwner);
  51:         _initializePauser(_pauserRegistry, initPausedStatus);
  52:         _setWithdrawalDelayBlocks(_withdrawalDelayBlocks);
  53:     }


src/contracts/pods/EigenPod.sol:
  151      /// @notice Used to initialize the pointers to addresses crucial to the pod's functionality. Called on construction by the EigenPodManager.
  152:     function initialize(address _podOwner) external initializer {
  153:         require(_podOwner != address(0), "EigenPod.initialize: podOwner cannot be zero address");
  154:         podOwner = _podOwner;
  155:     }


src/contracts/pods/EigenPodManager.sol:
  83  
  84:     function initialize(
  85:         IBeaconChainOracle _beaconChainOracle,
  86:         address initialOwner,
  87:         IPauserRegistry _pauserRegistry,
  88:         uint256 _initPausedStatus
  89:     ) external initializer {
  90:         _updateBeaconChainOracle(_beaconChainOracle);
  91:         _transferOwnership(initialOwner);
  92:         _initializePauser(_pauserRegistry, _initPausedStatus);
  93:     }



src/contracts/strategies/StrategyBase.sol:
  50  
  51:     function initialize(IERC20 _underlyingToken, IPauserRegistry _pauserRegistry) public virtual initializer {
  52:         _initializeStrategyBase(_underlyingToken, _pauserRegistry);
  53:     }


src/contracts/core/StrategyManager.sol:
  128       */
  129:     constructor(IDelegationManager _delegation, IEigenPodManager _eigenPodManager, ISlasher _slasher)
  130:         StrategyManagerStorage(_delegation, _eigenPodManager, _slasher)
  131:     {
  132:         _disableInitializers();
  133:         ORIGINAL_CHAIN_ID = block.chainid;
  134:     }


src/contracts/core/StrategyManagerStorage.sol:
  71  
  72:     constructor(IDelegationManager _delegation, IEigenPodManager _eigenPodManager, ISlasher _slasher) {
  73:         delegation = _delegation;
  74:         eigenPodManager = _eigenPodManager;
  75:         slasher = _slasher;
  76:     }


src/contracts/permissions/PauserRegistry.sol:
  25  
  26:     constructor(address _pauser, address _unpauser) {
  27:         _setPauser(_pauser);
  28:         _setUnpauser(_unpauser);
  29:     }


src/contracts/pods/DelayedWithdrawalRouter.sol:
  43  
  44:     constructor(IEigenPodManager _eigenPodManager) {
  45:         require(address(_eigenPodManager) != address(0), "DelayedWithdrawalRouter.constructor: _eigenPodManager cannot be zero address");
  46:         eigenPodManager = _eigenPodManager;
  47:     }



```

**Description:**
Events help non-contract tools to track changes, and events prevent users from being surprised by changes
Issuing event-emit during initialization is a detail that many projects skip

**Recommendation:**
Add Event-Emit

### [L-03] It is more appropriate to use `AddressUpgradeable.sol` instead of OpenZeppelin `Address.sol` import for the project.

```solidity

src/contracts/core/StrategyManager.sol:
  5: import "@openzeppelin/contracts/utils/Address.sol";

```

**Description:**
Using `AddressUpgradeable.sol` instead of `Address.sol` import from OpenZeppel is more upgradable , because the project is upgradable

**Recommendation:**
Use AddressUpgradable.sol instead Address.sol

https://github.com/OpenZeppelin/openzeppelin-contracts-upgradeable/blob/master/contracts/utils/AddressUpgradeable.sol

### [N-04] Repeated validation logic can be converted into a function modifier

If a query or logic is repeated over many lines, using a modifier improves the readability and reusability of the code
(!= address(0))

```solidity

9 results - 6 files

src/contracts/core/StrategyManager.sol:
  343:         require(withdrawer != address(0), "StrategyManager.queueWithdrawal: cannot withdraw to zero address");

  631:         require(depositor != address(0), "StrategyManager._addShares: depositor cannot be zero address");

  684:         require(depositor != address(0), "StrategyManager._removeShares: depositor cannot be zero address");

src/contracts/permissions/Pausable.sol:
  57:             address(pauserRegistry) == address(0) && address(_pauserRegistry) != address(0),

src/contracts/permissions/PauserRegistry.sol:
  42:         require(newPauser != address(0), "PauserRegistry._setPauser: zero address input");

  48:         require(newUnpauser != address(0), "PauserRegistry._setUnpauser: zero address input");

src/contracts/pods/DelayedWithdrawalRouter.sol:
  45:         require(address(_eigenPodManager) != address(0), "DelayedWithdrawalRouter.constructor: _eigenPodManager cannot be zero address");

src/contracts/pods/EigenPod.sol:
  153:         require(_podOwner != address(0), "EigenPod.initialize: podOwner cannot be zero address");

src/contracts/pods/EigenPodManager.sol:
  211:         return address(ownerToPod[podOwner]) != address(0);

```



### [N-05] Use a single file for all system-wide constants

There are many addresses and constants used in the system. It is recommended to put the most used ones in one file (for example constants.sol, use inheritance to access these values)

  This will help with readability and easier maintenance for future changes. This also helps with any issues, as some of these hard-coded values are admin addresses.

constants.sol
Use and import this file in contracts that require access to these values. This is just a suggestion, in some use cases this may result in higher gas usage in the distribution.

```solidity

73 results - 9 files

src/contracts/core/StrategyManager.sol:
  35:     uint256 internal constant GWEI_TO_WEI = 1e9;
  38:     uint8 internal constant PAUSED_DEPOSITS = 0;
  40:     uint8 internal constant PAUSED_WITHDRAWALS = 1;
  45:     bytes4 constant internal ERC1271_MAGICVALUE = 0x1626ba7e;

src/contracts/core/StrategyManagerStorage.sol:
  17:     bytes32 public constant DOMAIN_TYPEHASH =
  20:     bytes32 public constant DEPOSIT_TYPEHASH =
  28:     uint8 internal constant MAX_STAKER_STRATEGY_LIST_LENGTH = 32;
  46:     uint256 public constant MAX_WITHDRAWAL_DELAY_BLOCKS = 50400;
  70:     IStrategy public constant beaconChainETHStrategy = IStrategy(0xbeaC0eeEeeeeEEeEeEEEEeeEEeEeeeEeeEEBEaC0);

src/contracts/libraries/BeaconChainProofs.sol:
   14:     uint256 internal constant NUM_BEACON_BLOCK_HEADER_FIELDS = 5;
   15:     uint256 internal constant BEACON_BLOCK_HEADER_FIELD_TREE_HEIGHT = 3;
   17:     uint256 internal constant NUM_BEACON_BLOCK_BODY_FIELDS = 11;
   18:     uint256 internal constant BEACON_BLOCK_BODY_FIELD_TREE_HEIGHT = 4;
   20:     uint256 internal constant NUM_BEACON_STATE_FIELDS = 21;
   21:     uint256 internal constant BEACON_STATE_FIELD_TREE_HEIGHT = 5;
   23:     uint256 internal constant NUM_ETH1_DATA_FIELDS = 3;
   24:     uint256 internal constant ETH1_DATA_FIELD_TREE_HEIGHT = 2;
   26:     uint256 internal constant NUM_VALIDATOR_FIELDS = 8;
   27:     uint256 internal constant VALIDATOR_FIELD_TREE_HEIGHT = 3;
   29:     uint256 internal constant NUM_EXECUTION_PAYLOAD_HEADER_FIELDS = 15;
   30:     uint256 internal constant EXECUTION_PAYLOAD_HEADER_FIELD_TREE_HEIGHT = 4;
   33:     uint256 internal constant NUM_EXECUTION_PAYLOAD_FIELDS = 15;
   34:     uint256 internal constant EXECUTION_PAYLOAD_FIELD_TREE_HEIGHT = 4;
   38:     uint256 internal constant HISTORICAL_ROOTS_TREE_HEIGHT = 24;
   41:     uint256 internal constant HISTORICAL_BATCH_TREE_HEIGHT = 1;
   44:     uint256 internal constant STATE_ROOTS_TREE_HEIGHT = 13;
   45:     uint256 internal constant BLOCK_ROOTS_TREE_HEIGHT = 13;
   48:     uint256 internal constant NUM_WITHDRAWAL_FIELDS = 4;
   50:     uint256 internal constant WITHDRAWAL_FIELD_TREE_HEIGHT = 2;
   52:     uint256 internal constant VALIDATOR_TREE_HEIGHT = 40;
   54:     uint256 internal constant BALANCE_TREE_HEIGHT = 38;
   57:     uint256 internal constant WITHDRAWALS_TREE_HEIGHT = 4;
   60:     uint256 internal constant EXECUTION_PAYLOAD_INDEX = 9;
   63:     uint256 internal constant STATE_ROOT_INDEX = 3;
   64:     uint256 internal constant PROPOSER_INDEX_INDEX = 1;
   65:     uint256 internal constant SLOT_INDEX = 0;
   66:     uint256 internal constant BODY_ROOT_INDEX = 4;
   68:     uint256 internal constant STATE_ROOTS_INDEX = 6;
   69:     uint256 internal constant BLOCK_ROOTS_INDEX = 5;
   70:     uint256 internal constant HISTORICAL_ROOTS_INDEX = 7;
   71:     uint256 internal constant ETH_1_ROOT_INDEX = 8;
   72:     uint256 internal constant VALIDATOR_TREE_ROOT_INDEX = 11;
   73:     uint256 internal constant BALANCE_INDEX = 12;
   74:     uint256 internal constant EXECUTION_PAYLOAD_HEADER_INDEX = 24;
   75:     uint256 internal constant HISTORICAL_BATCH_STATE_ROOT_INDEX = 1;
   78:     uint256 internal constant VALIDATOR_WITHDRAWAL_CREDENTIALS_INDEX = 1;
   79:     uint256 internal constant VALIDATOR_BALANCE_INDEX = 2;
   80:     uint256 internal constant VALIDATOR_SLASHED_INDEX = 3;
   81:     uint256 internal constant VALIDATOR_WITHDRAWABLE_EPOCH_INDEX = 7;
   84:     uint256 internal constant BLOCK_NUMBER_INDEX = 6;
   85:     uint256 internal constant WITHDRAWALS_ROOT_INDEX = 14;
   88:     uint256 internal constant WITHDRAWALS_INDEX = 14;
   91:     uint256 internal constant WITHDRAWAL_VALIDATOR_INDEX_INDEX = 1;
   92:     uint256 internal constant WITHDRAWAL_VALIDATOR_AMOUNT_INDEX = 3;
   95:     uint256 internal constant HISTORICALBATCH_STATEROOTS_INDEX = 1;
   98:     uint256 internal constant SLOTS_PER_EPOCH = 32;
  100:     bytes8 internal constant UINT64_MASK = 0xffffffffffffffff;

src/contracts/permissions/Pausable.sol:
  21  
  22:     uint256 constant internal UNPAUSE_ALL = 0;
  23:     uint256 constant internal PAUSE_ALL = type(uint256).max;
  24  

src/contracts/pods/DelayedWithdrawalRouter.sol:
  16:     uint8 internal constant PAUSED_DELAYED_WITHDRAWAL_CLAIMS = 0;
  24:     uint256 public constant MAX_WITHDRAWAL_DELAY_BLOCKS = 50400;
  25  

src/contracts/pods/EigenPod.sol:
  38:     uint256 internal constant GWEI_TO_WEI = 1e9;
  41:     uint256 internal constant VERIFY_OVERCOMMITTED_WINDOW_BLOCKS = 50400;

src/contracts/pods/EigenPodManager.sol:
  37:     bytes internal constant beaconProxyBytecode = hex"608060405260405161090e3803806

src/contracts/pods/EigenPodPausingConstants.sol:
  10:     uint8 internal constant PAUSED_NEW_EIGENPODS = 0;
  12:     uint8 internal constant PAUSED_WITHDRAW_RESTAKED_ETH = 1;
  15:     uint8 internal constant PAUSED_EIGENPODS_VERIFY_CREDENTIALS = 2;
  17:     uint8 internal constant PAUSED_EIGENPODS_VERIFY_OVERCOMMITTED = 3;
  19:     uint8 internal constant PAUSED_EIGENPODS_VERIFY_WITHDRAWAL = 4;
  20  }

src/contracts/strategies/StrategyBase.sol:
  22:     uint8 internal constant PAUSED_DEPOSITS = 0;
  23:     uint8 internal constant PAUSED_WITHDRAWALS = 1;
  28:     uint96 internal constant MIN_NONZERO_TOTAL_SHARES = 1e9;


```

### [N-06] Imports should be sorted in alphabetical order (Solidity Stle Guide)

Imports should be sorted in alphabetical order. The same import should not be repeated. Use the import “./filename”; syntax for external contract dependencies.

Explained with example from Strategy Manager.sol file

```solidity

src/contracts/core/StrategyManager.sol:
   
   4: import "@openzeppelin/contracts/interfaces/IERC1271.sol";
   5: import "@openzeppelin/contracts/utils/Address.sol";
   6: import "@openzeppelin-upgrades/contracts/proxy/utils/Initializable.sol";
   7: import "@openzeppelin-upgrades/contracts/access/OwnableUpgradeable.sol";
   8: import "@openzeppelin-upgrades/contracts/security/ReentrancyGuardUpgradeable.sol";
   9: import "@openzeppelin/contracts/token/ERC20/utils/SafeERC20.sol";
  10: import "@openzeppelin/contracts/utils/cryptography/ECDSA.sol";
  11: import "../interfaces/IEigenPodManager.sol";
  12: import "../permissions/Pausable.sol";
  13: import "./StrategyManagerStorage.sol";

```

Recommendation;

Sorted in alphabetical order

```solidity

import "@openzeppelin/contracts/interfaces/IERC1271.sol";
import "@openzeppelin/contracts/token/ERC20/utils/SafeERC20.sol";
import "@openzeppelin/contracts/utils/Address.sol";
import "@openzeppelin/contracts/utils/cryptography/ECDSA.sol";
import "@openzeppelin-upgrades/contracts/access/OwnableUpgradeable.sol";
import "@openzeppelin-upgrades/contracts/proxy/utils/Initializable.sol";
import "@openzeppelin-upgrades/contracts/security/ReentrancyGuardUpgradeable.sol";
import "../interfaces/IEigenPodManager.sol";
import "../permissions/Pausable.sol";
import "./StrategyManagerStorage.sol";

```


### [N-07] For functions, follow Solidity standard naming conventions (internal function style rule)

**Context:**
```solidity

9 results - 3 files

src/contracts/libraries/BeaconChainProofs.sol:
  130:     function computePhase0BeaconBlockHeaderRoot(bytes32[NUM_BEACON_BLOCK_HEADER_FIELDS] calldata blockHeaderFields) internal pure returns(bytes32) {
  140:     function computePhase0BeaconStateRoot(bytes32[NUM_BEACON_STATE_FIELDS] calldata beaconStateFields) internal pure returns(bytes32) {
  150:     function computePhase0ValidatorRoot(bytes32[NUM_VALIDATOR_FIELDS] calldata validatorFields) internal pure returns(bytes32) {  
  160:     function computePhase0Eth1DataRoot(bytes32[NUM_ETH1_DATA_FIELDS] calldata eth1DataFields) internal pure returns(bytes32) {  
  178:    function getBalanceFromBalanceRoot(uint40 validatorIndex, bytes32 balanceRoot) internal pure returns (uint64) {

src/contracts/libraries/Endian.sol:
  5:     function fromLittleEndianUint64(bytes32 lenum) internal pure returns (uint64 n) {

src/contracts/libraries/Merkle.sol:
   80:     function verifyInclusionSha256(bytes memory proof,bytes32 root,bytes32 leaf,uint256 index) internal view returns (bool) {
   94:     function processInclusionProofSha256(bytes memory proof, bytes32 leaf, uint256 index) internal view returns (bytes32) {
  124:     function merkleizeSha256(bytes32[] memory leaves) internal pure returns (bytes32) {



```


**Description:**
The above codes don't follow Solidity's standard naming convention,

internal and private functions : the mixedCase format starting with an underscore (_mixedCase starting with an underscore)



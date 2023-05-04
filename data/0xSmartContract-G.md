### Gas Optimizations List
| Number | Optimization Details | Context |
|:--:|:-------| :-----:|
| [G-01] | Remove or replace unused state variables|19 |
| [G-02] |Missing `zero-address` check in `constructor`| 4 |
| [G-03] |Use ``assembly`` to write _address storage values_  |7 |
| [G-04] |Use nested if and, avoid multiple check combinations |2 |
| [G-05] |Sort Solidity operations using short-circuit mode|1|
| [G-06] |Using ``delete`` to set ``bool`` values to their default values saves gas  |3 |
| [G-07] |Ternary operation is cheaper than if-else statement| 2 |

Total 7 issues


### [G-01] Remove or replace unused state variables

When all in-scope and out-of-scope projects are examined, it is seen that the ``constant`` state variables stated below are never used anywhere. 

Removing these state variables will save contract deployment cost.

19 results - 1 file:
```solidity
src\contracts\libraries\BeaconChainProofs.sol:

17:     uint256 internal constant NUM_BEACON_BLOCK_BODY_FIELDS = 11;


29:     uint256 internal constant NUM_EXECUTION_PAYLOAD_HEADER_FIELDS = 15;


33:     uint256 internal constant NUM_EXECUTION_PAYLOAD_FIELDS = 15;
34:     uint256 internal constant EXECUTION_PAYLOAD_FIELD_TREE_HEIGHT = 4;


37      // HISTORICAL_ROOTS_LIMIT	 = 2**24, so tree height is 24
38:     uint256 internal constant HISTORICAL_ROOTS_TREE_HEIGHT = 24;


40      // HISTORICAL_BATCH is root of state_roots and block_root, so number of leaves =  2^1
41:     uint256 internal constant HISTORICAL_BATCH_TREE_HEIGHT = 1;


43      // SLOTS_PER_HISTORICAL_ROOT = 2**13, so tree height is 13
44:     uint256 internal constant STATE_ROOTS_TREE_HEIGHT = 13;


48:     uint256 internal constant NUM_WITHDRAWAL_FIELDS = 4;


62     // in beacon block header
63:     uint256 internal constant STATE_ROOT_INDEX = 3;
64:     uint256 internal constant PROPOSER_INDEX_INDEX = 1;


67     // in beacon state
68:     uint256 internal constant STATE_ROOTS_INDEX = 6;
70:     uint256 internal constant HISTORICAL_ROOTS_INDEX = 7;
71:     uint256 internal constant ETH_1_ROOT_INDEX = 8;
74:     uint256 internal constant EXECUTION_PAYLOAD_HEADER_INDEX = 24;
75:     uint256 internal constant HISTORICAL_BATCH_STATE_ROOT_INDEX = 1;


77     // in validator
79:     uint256 internal constant VALIDATOR_BALANCE_INDEX = 2;


83     // in exection payload header
85:     uint256 internal constant WITHDRAWALS_ROOT_INDEX = 14;


94     //In historicalBatch
95:     uint256 internal constant HISTORICALBATCH_STATEROOTS_INDEX = 1;


100:    bytes8 internal constant UINT64_MASK = 0xffffffffffffffff;

```

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/libraries/BeaconChainProofs.sol#L17


### [G-02] Missing `zero-address` check in `constructor`

While immutable addresses are assigned in the constructor, there is no zero address control. If these immutable state variables are initialized with a possible value of "0", it means that the contract must be redistributed. This possibility means gas consumption. 

Because of the EVM design, zero value control is the most error-prone value control, as zero value is assigned in case of no value input.

Also, since the constant value will be changed once, adding a zero value control will not cause high gas consumption.

4 results - 4 files:
```solidity
src\contracts\core\StrategyManagerStorage.sol:

  72:     constructor(IDelegationManager _delegation, IEigenPodManager _eigenPodManager, ISlasher _slasher) {
  73:         delegation = _delegation;
  74:         eigenPodManager = _eigenPodManager;
  75:         slasher = _slasher;
  76:     }

```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManagerStorage.sol#L72-L76


```solidity
src\contracts\pods\EigenPod.sol:

  136:     constructor(
  137:         IETHPOSDeposit _ethPOS,
  138:         IDelayedWithdrawalRouter _delayedWithdrawalRouter,
  139:         IEigenPodManager _eigenPodManager,
  140:         uint256 _REQUIRED_BALANCE_WEI
  141:     ) {
  142:         ethPOS = _ethPOS;
  143:         delayedWithdrawalRouter = _delayedWithdrawalRouter;
  144:         eigenPodManager = _eigenPodManager;
  145:         REQUIRED_BALANCE_WEI = _REQUIRED_BALANCE_WEI;
  146:         REQUIRED_BALANCE_GWEI = uint64(_REQUIRED_BALANCE_WEI / GWEI_TO_WEI);
  147:         require(_REQUIRED_BALANCE_WEI % GWEI_TO_WEI == 0, "EigenPod.contructor: _REQUIRED_BALANCE_WEI is not a whole number of gwei");
  148:         _disableInitializers();
  149:     }

```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPod.sol#L136-L149


```solidity
src\contracts\pods\EigenPodManager.sol:

  76:     constructor(IETHPOSDeposit _ethPOS, IBeacon _eigenPodBeacon, IStrategyManager _strategyManager, ISlasher _slasher) {
  77:         ethPOS = _ethPOS;
  78:         eigenPodBeacon = _eigenPodBeacon;
  79:         strategyManager = _strategyManager;
  80:         slasher = _slasher;
  81:         _disableInitializers();
  82:     }

```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPodManager.sol#L76-L82


```solidity
src\contracts\strategies\StrategyBase.sol:

  46:     constructor(IStrategyManager _strategyManager) {
  47:         strategyManager = _strategyManager;
  48:         _disableInitializers();
  49:     }

```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/strategies/StrategyBase.sol#L46-L49


**Recommendation step:**
It is recommended to perform a zero value check for critical value assignments.

Add zero check for immutable values when assigning values in critical constructor.



### [G-03] Use ``assembly`` to write _address storage values_ 

There are 7 examples of the subject in the contracts within the scope, and with this optimization ~ 807 gas savings are achieved.

**src\contracts\core\StrategyManagerStorage.sol**

| StrategyManager.sol      |  before   |  after  | gas saved|
|:-------------------------|:---------:|:-------:|:--------:|
| |   128247  |  127999 |    248   |

```solidity
  851:     function _setStrategyWhitelister(address newStrategyWhitelister) internal {
  853:         strategyWhitelister = newStrategyWhitelister;

```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L848


**src\contracts\strategies\StrategyBase.sol**

| SStrategyBase.sol      |  before   |  after  | gas saved|
|:-------------------------|:---------:|:-------:|:--------:|
| |   50479  |  50272 |    207   |

```solidity
  56:     function _initializeStrategyBase(IERC20 _underlyingToken, IPauserRegistry _pauserRegistry) internal onlyInitializing {
  57:         underlyingToken = _underlyingToken;

```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/strategies/StrategyBase.sol#L57



**src\contracts\pods\EigenPodManager.sol**

| EigenPodManager.sol      |  before   |  after  | gas saved|
|:-------------------------|:---------:|:-------:|:--------:|
| |   97661  |  97465 |    196   |

```solidity
   186:     function _updateBeaconChainOracle(IBeaconChainOracle newBeaconChainOracle) internal {
  187:         beaconChainOracle = newBeaconChainOracle;

```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPodManager.sol#L187


**src\contracts\pods\EigenPod.sol**

| EigenPod.sol      |  before   |  after  | gas saved|
|:-------------------------|:---------:|:-------:|:--------:|
| |   46517  |  46469 |    48   |


```solidity
  152:     function initialize(address _podOwner) external initializer {
  154:         podOwner = _podOwner;

```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPod.sol#L154


**src\contracts\permissions\Pausable.sol**
```solidity
  55:     function _initializePauser(IPauserRegistry _pauserRegistry, uint256 initPausedStatus) internal {
  62:         pauserRegistry = _pauserRegistry;

```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/permissions/Pausable.sol#L62



**src\contracts\permissions\PauserRegistry.sol**

| PauserRegistry.sol      |  before   |  after  | gas saved|
|:-------------------------|:---------:|:-------:|:--------:|
| |   4886  |  4778 |    108   |


```solidity
  41:     function _setPauser(address newPauser) internal {
  44:         pauser = newPauser;


  47:     function _setUnpauser(address newUnpauser) internal {
  50:         unpauser = newUnpauser;

```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/permissions/PauserRegistry.sol#L44


**Recommendation Code:**
```diff
src\contracts\permissions\PauserRegistry.sol:

  47:     function _setUnpauser(address newUnpauser) internal {
- 50:         unpauser = newUnpauser;
+                  assembly {                      
+                      sstore(unpauser.slot, newUnpauser)
+                  }       

```


### [G-04] Use nested if and, avoid multiple check combinations

Using nested is cheaper than using && multiple check combinations. There are more advantages, such as easier to read code and better coverage reports.

**src\contracts\core\StrategyManager.sol**

| StrategyManager.sol      |  before   |  after  | gas saved|
|:-------------------------|:---------:|:-------:|:--------:|
| |   43887  |  43875 |    12   |


```diff
  329:     function queueWithdrawal(
- 422:         if (undelegateIfPossible && stakerStrategyList[msg.sender].length == 0) {
+              if (undelegateIfPossible) {
+                  if (stakerStrategyList[msg.sender].length == 0) {
  423:             _undelegate(msg.sender);
  424:             }
+              }

```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L422-L424

**src\contracts\core\StrategyManager.sol**

| StrategyManager.sol      |  before   |  after  | gas saved|
|:-------------------------|:---------:|:-------:|:--------:|
| |   10505    |  10497   |    8    |



```diff
  539:     function slashQueuedWithdrawal(address recipient, QueuedWithdrawal calldata queuedWithdrawal, IERC20[] calldata tokens, uint256[] calldata indicesToSkip)
- 565:             if (indicesToSkipIndex < indicesToSkip.length && indicesToSkip[indicesToSkipIndex] == i) {
+ 566:             if (indicesToSkipIndex < indicesToSkip.length) {
+ 567:                 if ( indicesToSkip[indicesToSkipIndex] == i) {
  568:                     unchecked {
  569:                     ++indicesToSkipIndex;
  570:                     }
+ 571:                 }
  572              } else {

```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L562-L566


### [G-05] Sort Solidity operations using short-circuit mode

Short-circuiting is a solidity contract development model that uses ```OR/AND``` logic to sequence different cost operations. It puts low gas cost operations in the front and high gas cost operations in the back, so that if the front is low If the cost operation is feasible, you can skip (short-circuit) the subsequent high-cost Ethereum virtual machine operation.

```
//f(x) is a low gas cost operation 
//g(y) is a high gas cost operation 

//Sort operations with different gas costs as follows 
f(x) || g(y) 
f(x) && g(y)
```

1 result - 1 file:

| StrategyManager.sol      |  before   |  after  | gas saved|
|:-------------------------|:---------:|:-------:|:--------:|
| |   9267  |  9264 |    3   |


**src\contracts\strategies\StrategyBase.sol**

```solidity
  121:     function withdraw(address depositor, IERC20 token, uint256 amountShares)

  139:         require(updatedTotalShares >= MIN_NONZERO_TOTAL_SHARES || updatedTotalShares == 0,
  140:         "StrategyBase.withdraw: updated totalShares amount would be nonzero but below MIN_NONZERO_TOTAL_SHARES");

```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/strategies/StrategyBase.sol#L139-L140


```diff
src\contracts\strategies\StrategyBase.sol:

  121:     function withdraw(address depositor, IERC20 token, uint256 amountShares)

- 139:         require(updatedTotalShares >= MIN_NONZERO_TOTAL_SHARES || updatedTotalShares == 0,
+ 139:         require(updatedTotalShares == 0 || updatedTotalShares >= MIN_NONZERO_TOTAL_SHARES,
  140:         "StrategyBase.withdraw: updated totalShares amount would be nonzero but below MIN_NONZERO_TOTAL_SHARES");

```


### [G-06] Using ``delete`` to set ``bool`` values to their default values saves gas

3 results - 1 file:

```solidity
src\contracts\core\StrategyManager.sol:

  554      // reset the storage slot in mapping of queued withdrawals
  555:     withdrawalRootPending[withdrawalRoot] = false;
        
 
  614:     strategyIsWhitelistedForDeposit[strategiesToRemoveFromWhitelist[i]] = false;


  774      // reset the storage slot in mapping of queued withdrawals
  775:     withdrawalRootPending[withdrawalRoot] = false;

```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L554
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L612
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L772


```diff
src\contracts\core\StrategyManager.sol:

  554      // reset the storage slot in mapping of queued withdrawals
- 555:     withdrawalRootPending[withdrawalRoot] = false;
+ 555:     delete withdrawalRootPending[withdrawalRoot];
        
 
- 614:     strategyIsWhitelistedForDeposit[strategiesToRemoveFromWhitelist[i]] = false;
+ 614:     delete strategyIsWhitelistedForDeposit[strategiesToRemoveFromWhitelist[i]];


  774      // reset the storage slot in mapping of queued withdrawals
- 775:     withdrawalRootPending[withdrawalRoot] = false;
+ 775:     delete withdrawalRootPending[withdrawalRoot];

```


### [G-07] Ternary operation is cheaper than if-else statement

There are instances where a ternary operation can be used instead of if-else statement. In these cases, using ternary operation will save modest amounts of gas.

2 results - 1 file:

```solidity
src\contracts\strategies\StrategyBase.sol

  173:     function sharesToUnderlyingView(uint256 amountShares) public view virtual override returns (uint256) {
  174:         if (totalShares == 0) {
  175:             return amountShares;
  176:         } else {
  177:             return (_tokenBalance() * amountShares) / totalShares;
  178:         }
  179:     }

```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/strategies/StrategyBase.sol#L172-L178

```diff
src\contracts\strategies\StrategyBase.sol

  173:     function sharesToUnderlyingView(uint256 amountShares) public view virtual override returns (uint256) {
- 174:         if (totalShares == 0) {
- 175:             return amountShares;
- 176:         } else {
- 177:             return (_tokenBalance() * amountShares) / totalShares;
- 178:         }
+              return (totalShares == 0) ? amountShares : (_tokenBalance() * amountShares) / totalShares;
  179:     }

```


```solidity
src\contracts\strategies\StrategyBase.sol

  197:     function underlyingToSharesView(uint256 amountUnderlying) public view virtual returns (uint256) {
  198:         uint256 tokenBalance = _tokenBalance();
  199:         if (tokenBalance == 0 || totalShares == 0) {
  200:             return amountUnderlying;
  201:         } else {
  202:             return (amountUnderlying * totalShares) / tokenBalance;
  203:         }
  204:     }

```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/strategies/StrategyBase.sol#L196-L203

```diff
src\contracts\strategies\StrategyBase.sol

  197:     function underlyingToSharesView(uint256 amountUnderlying) public view virtual returns (uint256) {
  198:         uint256 tokenBalance = _tokenBalance();
- 199:         if (tokenBalance == 0 || totalShares == 0) {
- 200:             return amountUnderlying;
- 201:         } else {
- 202:             return (amountUnderlying * totalShares) / tokenBalance;
- 203:         }
+              return (tokenBalance == 0 || totalShares == 0) ? amountUnderlying : (amountUnderlying * totalShares) / tokenBalance;
  204:     }

```
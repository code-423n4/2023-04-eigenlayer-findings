## QA
---

### Layout Order [1]

- The best-practices for layout within a contract is the following order: state variables, events, modifiers, constructor and functions.

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/DelayedWithdrawalRouter.sol

```solidity
// event coming before state variables
13:    event WithdrawalDelayBlocksSet(uint256 previousValue, uint256 newValue);
```

---

### Function Visibility [2]

- Order of Functions: Ordering helps readers identify which functions they can call and to find the constructor and fallback definitions easier. Functions should be grouped according to their visibility and ordered: constructor, receive function (if exists), fallback function (if exists), public, external, internal, private. Within a grouping, place the view and pure functions last.

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/interfaces/IServiceManager.sol

```solidity
// external view function coming before external non-view ones
13:    function taskNumber() external view returns (uint32);
```

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/interfaces/IEigenPod.sol

```solidity
// non-view external functions should come before external view ones
59:    function initialize(address owner) external;
62:    function stake(bytes calldata pubkey, bytes calldata signature, bytes32 depositDataRoot) external payable;
70:    function withdrawRestakedBeaconChainETH(address recipient, uint256 amount) external;
98:    function verifyWithdrawalCredentialsAndBalance(
118:    function verifyOvercommittedStake(
135:    function verifyAndProcessWithdrawal(
145:    function withdrawBeforeRestaking() external;
```

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/interfaces/IBeaconChainOracle.sol

```solidity
// non-view external functions should come before external view ones
39:    function setThreshold(uint256 _threshold) external;
46:    function addOracleSigners(address[] memory _oracleSigners) external;
53:    function removeOracleSigners(address[] memory _oracleSigners) external;
61:    function voteForBeaconChainStateRoot(uint64 blockNumber, bytes32 stateRoot) external;
```

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/permissions/Pausable.sol

```solidity
the order of all the visibility functions are inverted, internals are coming first, then external and then public. should be the other way
```

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPod.sol

```solidity
// external functions coming after internal ones
437:    function withdrawRestakedBeaconChainETH(
454:    function withdrawBeforeRestaking() external onlyEigenPodOwner hasNeverRestaked {
```

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPodManager.sol

```solidity
// public functions coming after internal ones
193:    function getPod(address podOwner) public view returns (IEigenPod) {
210:    function hasPod(address podOwner) public view returns (bool) {

// external function coming after internal ones
215:    function getBeaconChainStateRoot(uint64 blockNumber) external view returns(bytes32) {
```

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/strategies/StrategyBase.sol

```solidity
// internal functions coming before external ones
56:    function _initializeStrategyBase(IERC20 _underlyingToken, IPauserRegistry _pauserRegistry) internal onlyInitializing {

// external functions coming before public ones
78:    function deposit(IERC20 token, uint256 amount)
121:    function withdraw(address depositor, IERC20 token, uint256 amountShares)
162:    function explanation() external pure virtual override returns (string memory) {
211:    function underlyingToShares(uint256 amountUnderlying) external view virtual returns (uint256) {
219:    function userUnderlyingView(address user) external view virtual returns (uint256) {
227:    function userUnderlying(address user) external virtual returns (uint256) {
```

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol

```solidity
// internal functions coming before public and external ones
629:    function _addShares(address depositor, IStrategy strategy, uint256 shares) internal {
655:    function _depositIntoStrategy(address depositor, IStrategy strategy, IERC20 token, uint256 amount)
679:    function _removeShares(address depositor, uint256 strategyIndex, IStrategy strategy, uint256 shareAmount)
715:    function _removeStrategyFromStakerStrategyList(address depositor, uint256 strategyIndex, IStrategy strategy) internal {
745:    function _completeQueuedWithdrawal(QueuedWithdrawal calldata queuedWithdrawal, IERC20[] calldata tokens, uint256 middlewareTimesIndex, bool receiveAsTokens) onlyNotFrozen(queuedWithdrawal.delegatedAddress) internal {
811:    function _undelegate(address depositor) internal {
821:    function _withdrawBeaconChainETH(address staker, address recipient, uint256 amount) internal {
839:    function _setWithdrawalDelayBlocks(uint256 _withdrawalDelayBlocks) internal {
846:    function _setStrategyWhitelister(address newStrategyWhitelister) internal {

// public functions should come before all the other ones
876:    function calculateWithdrawalRoot(QueuedWithdrawal memory queuedWithdrawal) public pure returns (bytes32) {
```

---

### natSpec missing [3]

Some functions are missing @params or @returns. Specification Format.” These are written with a triple slash (///) or a double asterisk block(/** ... */) directly above function declarations or statements to generate documentation in JSON format for developers and end-users. It is recommended that Solidity contracts are fully annotated using NatSpec for all public interfaces (everything in the ABI). These comments contain different types of tags:
- @title: A title that should describe the contract/interface @author: The name of the author (for contract, interface) 
- @notice: Explain to an end user what this does (for contract, interface, function, public state variable, event) 
- @dev: Explain to a developer any extra details (for contract, interface, function, state variable, event) 
- @param: Documents a parameter (just like in doxygen) and must be followed by parameter name (for function, event)
- @return: Documents the return variables of a contract’s function (function, public state variable)
- @inheritdoc: Copies all missing tags from the base function and must be followed by the contract name (for function, public state variable)
- @custom…: Custom tag, semantics is application-defined (for everywhere)

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/interfaces/IServiceManager.sol

```solidity
// @return missing
13:    function taskNumber() external view returns (uint32);
28:    function latestServeUntilBlock() external view returns (uint32);

// @param missing
16:    function freezeOperator(address operator) external;
19:    function recordFirstStakeUpdate(address operator, uint32 serveUntilBlock) external;
22:    function recordStakeUpdate(address operator, uint32 updateBlock, uint32 serveUntilBlock, uint256 prevElement) external;
25:    function recordLastStakeUpdateAndRevokeSlashingAbility(address operator, uint32 serveUntilBlock) external;

30:    function owner() external view returns (address);
```

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/interfaces/IDelegationManager.sol

```solidity
// @param missing
38:    function delegateToBySignature(address staker, address operator, uint256 expiry, bytes memory signature) external;
45:    function undelegate(address staker) external;
60:    function increaseDelegatedShares(address staker, IStrategy strategy, uint256 shares) external;
66:    function decreaseDelegatedShares(

// @param and @return missing
48:    function delegatedTo(address staker) external view returns (address);
51:    function delegationTerms(address operator) external view returns (IDelegationTerms);
54:    function operatorShares(address operator, IStrategy strategy) external view returns (uint256);
73:    function isDelegated(address staker) external view returns (bool);
76:    function isNotDelegated(address staker) external view returns (bool);
79:    function isOperator(address operator) external view returns (bool);

```

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/interfaces/IPauserRegistry.sol

```solidity
// @return missing
10:    function pauser() external view returns (address);
13:    function unpauser() external view returns (address);
```

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/interfaces/IEigenPodManager.sol

```solidity
// @param missing
61:    function updateBeaconChainOracle(IBeaconChainOracle newBeaconChainOracle) external;

64:    function ownerToPod(address podOwner) external view returns(IEigenPod);

67:    function getPod(address podOwner) external view returns(IEigenPod);

70:    function beaconChainOracle() external view returns(IBeaconChainOracle);    

73:    function getBeaconChainStateRoot(uint64 blockNumber) external view returns(bytes32);

76:    function strategyManager() external view returns(IStrategyManager);

79:    function slasher() external view returns(ISlasher);

81:    function hasPod(address podOwner) external view returns (bool);
```

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/interfaces/IEigenPod.sol

```solidity
40:    enum PARTIAL_WITHDRAWAL_CLAIM_STATUS {

47:    function REQUIRED_BALANCE_GWEI() external view returns(uint64);

50:    function REQUIRED_BALANCE_WEI() external view returns(uint256);

53:    function validatorStatus(uint40 validatorIndex) external view returns(VALIDATOR_STATUS);

56:    function restakedExecutionLayerGwei() external view returns(uint64);

59:    function initialize(address owner) external;

62:    function stake(bytes calldata pubkey, bytes calldata signature, bytes32 depositDataRoot) external payable;

// @param missing
70:    function withdrawRestakedBeaconChainETH(address recipient, uint256 amount) external;

73:    function eigenPodManager() external view returns (IEigenPodManager);

76:    function podOwner() external view returns (address);

79:    function hasRestaked() external view returns (bool);

82:    function mostRecentWithdrawalBlockNumber() external view returns (uint64);

86:    function provenPartialWithdrawal(uint40 validatorIndex, uint64 slot) external view returns (bool);
```

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/interfaces/IDelayedWithdrawalRouter.sol

```solidity
6:    struct DelayedWithdrawal {

12:    struct UserDelayedWithdrawals {

21:    function createDelayedWithdrawal(address podOwner, address recipient) external payable;

37:    function setWithdrawalDelayBlocks(uint256 newValue) external;

40:    function userWithdrawals(address user) external view returns (UserDelayedWithdrawals memory);

43:    function claimableUserDelayedWithdrawals(address user) external view returns (DelayedWithdrawal[] memory);

46:    function userDelayedWithdrawalByIndex(address user, uint256 index) external view returns (DelayedWithdrawal memory);

49:    function userWithdrawalsLength(address user) external view returns (uint256);

52:    function canClaimDelayedWithdrawal(address user, uint256 index) external view returns (bool);

58:    function withdrawalDelayBlocks() external view returns (uint256);
```

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/interfaces/IETHPOSDeposit.sol

```solidity
19:    event DepositEvent(bytes pubkey, bytes withdrawal_credentials, bytes amount, bytes signature, bytes index);
```

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/interfaces/IStrategyManager.sol

```solidity
15:    struct WithdrawerAndNonce {

26:    struct QueuedWithdrawal {

// @return missing
43:    function depositIntoStrategy(IStrategy strategy, IERC20 token, uint256 amount)
82:    function depositIntoStrategyWithSignature(
126:    function queueWithdrawal(

94:    function stakerStrategyShares(address user, IStrategy strategy) external view returns (uint256 shares);

// @param missing
100:    function getDeposits(address depositor) external view returns (IStrategy[] memory, uint256[] memory);
159:    function completeQueuedWithdrawals(
178:    function completeQueuedWithdrawals(

202:    function calculateWithdrawalRoot(

210:    function addStrategiesToDepositWhitelist(IStrategy[] calldata strategiesToWhitelist) external;

213:    function removeStrategiesFromDepositWhitelist(IStrategy[] calldata strategiesToRemoveFromWhitelist) external;

216:    function delegation() external view returns (IDelegationManager);

219:    function slasher() external view returns (ISlasher);

222:    function beaconChainETHStrategy() external view returns (IStrategy);

225:    function withdrawalDelayBlocks() external view returns (uint256);
```

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/interfaces/IStrategy.sol

```solidity
// @return missing
37:    function sharesToUnderlying(uint256 amountShares) external returns (uint256);
45:    function underlyingToShares(uint256 amountUnderlying) external returns (uint256);
59:    function sharesToUnderlyingView(uint256 amountShares) external view returns (uint256);
67:    function underlyingToSharesView(uint256 amountUnderlying) external view returns (uint256);
76:    function underlyingToken() external view returns (IERC20);
79:    function totalShares() external view returns (uint256);
82:    function explanation() external view returns (string memory);

// @param and @return missing
51:    function userUnderlying(address user) external returns (uint256);
73:    function userUnderlyingView(address user) external view returns (uint256);
```

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/libraries/Endian.sol

```solidity
4:      library Endian {

5:    function fromLittleEndianUint64(
```

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/libraries/BeaconChainProofs.sol

```solidity
104:    struct WithdrawalProofs {

119:    struct ValidatorFieldsAndBalanceProofs {

125:    struct ValidatorFieldsProof {

130:    function computePhase0BeaconBlockHeaderRoot(bytes32[NUM_BEACON_BLOCK_HEADER_FIELDS] calldata blockHeaderFields) internal pure returns(bytes32) {

140:    function computePhase0BeaconStateRoot(bytes32[NUM_BEACON_STATE_FIELDS] calldata beaconStateFields) internal pure returns(bytes32) {

150:    function computePhase0ValidatorRoot(bytes32[NUM_VALIDATOR_FIELDS] calldata validatorFields) internal pure returns(bytes32) {  

160:    function computePhase0Eth1DataRoot(bytes32[NUM_ETH1_DATA_FIELDS] calldata eth1DataFields) internal pure returns(bytes32) {  

// @returns missing
178:   function getBalanceFromBalanceRoot(uint40 validatorIndex, bytes32 balanceRoot) internal pure returns (uint64) {
```

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/permissions/PauserRegistry.sol

```solidity
17:    event PauserChanged(address previousPauser, address newPauser);

19:    event UnpauserChanged(address previousUnpauser, address newUnpauser);

21:    modifier onlyUnpauser() {

26:    constructor(address _pauser, address _unpauser) {

// @param missing
32:    function setPauser(address newPauser) external onlyUnpauser {
37:    function setUnpauser(address newUnpauser) external onlyUnpauser {

41:    function _setPauser(address newPauser) internal {

47:    function _setUnpauser(address newUnpauser) internal {
```

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/permissions/Pausable.sol

```solidity
// @params missing
26:    event Paused(address indexed account, uint256 newPausedStatus);
29:    event Unpaused(address indexed account, uint256 newPausedStatus);
49:    modifier onlyWhenNotPaused(uint8 index) {
55:    function _initializePauser(IPauserRegistry _pauserRegistry, uint256 initPausedStatus) internal {

// @notice incomplete
32:    modifier onlyPauser() {
37:    modifier onlyUnpauser() {

// @returns missing
100:    function paused() public view virtual returns (uint256) {

// @params and @returns missing
105:    function paused(uint8 index) public view virtual returns (bool) {
```

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/DelayedWithdrawalRouter.sol

```solidity
// @params missing
13:    event WithdrawalDelayBlocksSet(uint256 previousValue, uint256 newValue);
33:    event DelayedWithdrawalCreated(address podOwner, address recipient, uint256 amount, uint256 index);
36:    event DelayedWithdrawalsClaimed(address recipient, uint256 amountClaimed, uint256 delayedWithdrawalsCompleted);
39:    modifier onlyEigenPod(address podOwner) {

44:    constructor(IEigenPodManager _eigenPodManager) {

49:    function initialize(address initOwner, IPauserRegistry _pauserRegistry, uint256 initPausedStatus, uint256 _withdrawalDelayBlocks) external initializer {

// @param missing
59:    function createDelayedWithdrawal(address podOwner, address recipient) external payable onlyEigenPod(podOwner) {
100:    function setWithdrawalDelayBlocks(uint256 newValue) external onlyOwner {
105:    function userWithdrawals(address user) external view returns (UserDelayedWithdrawals memory) {

// @param and @returns missing
110:    function claimableUserDelayedWithdrawals(address user) external view returns (DelayedWithdrawal[] memory) {
122:    function userDelayedWithdrawalByIndex(address user, uint256 index) external view returns (DelayedWithdrawal memory) {
127:    function userWithdrawalsLength(address user) external view returns (uint256) {
132:    function canClaimDelayedWithdrawal(address user, uint256 index) external view returns (bool) {

// @param missing
137:    function _claimDelayedWithdrawals(address recipient, uint256 maxNumberOfDelayedWithdrawalsToClaim) internal {
166:    function _setWithdrawalDelayBlocks(uint256 newValue) internal {
```

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPod.sol

```solidity
82:    event EigenPodStaked(bytes pubkey);

85:    event ValidatorRestaked(uint40 validatorIndex);

88:    event ValidatorOvercommitted(uint40 validatorIndex);

91:    event FullWithdrawalRedeemed(uint40 validatorIndex, address indexed recipient, uint64 withdrawalAmountGwei);

94:    event PartialWithdrawalRedeemed(uint40 validatorIndex, address indexed recipient, uint64 partialWithdrawalAmountGwei);

97:    event RestakedBeaconChainETHWithdrawn(address indexed recipient, uint256 amount);

99:    modifier onlyEigenPodManager {

104:    modifier onlyEigenPodOwner {

109:    modifier onlyNotFrozen {

114:    modifier hasNeverRestaked {

120:    modifier proofIsForValidBlockNumber(uint64 blockNumber) {

// @param misisng
131:    modifier onlyWhenNotPaused(uint8 index) {
152:    function initialize(address _podOwner) external initializer {
158:    function stake(bytes calldata pubkey, bytes calldata signature, bytes32 depositDataRoot) external payable onlyEigenPodManager {
437:    function withdrawRestakedBeaconChainETH(

136:    constructor(

361:    function _processFullWithdrawal(

422:    function _processPartialWithdrawal(uint64 withdrawalHappenedSlot, uint64 partialWithdrawalAmountGwei, uint40 validatorIndex, address recipient) internal {

// @returns missing
460:    function _podWithdrawalCredentials() internal view returns(bytes memory) {

464:    function _sendETH(address recipient, uint256 amountWei) internal {
```

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPodManager.sol

```solidity
58:    event BeaconOracleUpdated(address indexed newOracleAddress);

61:    event PodDeployed(address indexed eigenPod, address indexed podOwner);

64:    event BeaconChainETHDeposited(address indexed podOwner, uint256 amount);

66:    modifier onlyEigenPod(address podOwner) {

71:    modifier onlyStrategyManager {

76:    constructor(IETHPOSDeposit _ethPOS, IBeacon _eigenPodBeacon, IStrategyManager _strategyManager, ISlasher _slasher) {

84:    function initialize(

166:    function _deployPod() internal onlyWhenNotPaused(PAUSED_NEW_EIGENPODS) returns (IEigenPod) {

186:    function _updateBeaconChainOracle(IBeaconChainOracle newBeaconChainOracle) internal {

193:    function getPod(address podOwner) public view returns (IEigenPod) {

210:    function hasPod(address podOwner) public view returns (bool) {

215:    function getBeaconChainStateRoot(uint64 blockNumber) external view returns(bytes32) {
```

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/strategies/StrategyBase.sol

```solidity
// @params missing
46:    constructor(IStrategyManager _strategyManager) {
56:    function _initializeStrategyBase(IERC20 _underlyingToken, IPauserRegistry _pauserRegistry) internal onlyInitializing {

51:    function initialize(IERC20 _underlyingToken, IPauserRegistry _pauserRegistry) public virtual initializer {

// @param `depositor` missing
121:    function withdraw(address depositor, IERC20 token, uint256 amountShares)

// @returns missing
162:    function explanation() external pure virtual override returns (string memory) {
172:    function sharesToUnderlyingView(uint256 amountShares) public view virtual override returns (uint256) {
186:    function sharesToUnderlying(uint256 amountShares) public view virtual override returns (uint256) {
196:    function underlyingToSharesView(uint256 amountUnderlying) public view virtual returns (uint256) {
211:    function underlyingToShares(uint256 amountUnderlying) external view virtual returns (uint256) {
241:    function _tokenBalance() internal view virtual returns (uint256) {

// @param and @return missing
219:    function userUnderlyingView(address user) external view virtual returns (uint256) {
227:    function userUnderlying(address user) external virtual returns (uint256) {
235:    function shares(address user) public view virtual returns (uint256) {
```

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManagerStorage.sol

```solidity
72:    constructor(IDelegationManager _delegation, IEigenPodManager _eigenPodManager, ISlasher _slasher) {
```

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol

```solidity
// @params missing
82:    event WithdrawalCompleted(address indexed depositor, uint96 nonce, address indexed withdrawer, bytes32 withdrawalRoot);
85:    event StrategyWhitelisterChanged(address previousAddress, address newAddress);
88:    event StrategyAddedToDepositWhitelist(IStrategy strategy);
91:    event StrategyRemovedFromDepositWhitelist(IStrategy strategy);
94:    event WithdrawalDelayBlocksSet(uint256 previousValue, uint256 newValue);
96:    modifier onlyNotFrozen(address staker) {
104:    modifier onlyFrozen(address staker) {
109:    modifier onlyEigenPodManager {
114:    modifier onlyStrategyWhitelister {
119:    modifier onlyStrategiesWhitelistedForDeposit(IStrategy strategy) {

// @param `initialPausedStatus` misssing
146:    function initialize(address initialOwner, address initialStrategyWhitelister, IPauserRegistry _pauserRegistry, uint256 initialPausedStatus, uint256 _withdrawalDelayBlocks)

// @params withdrawer and undelegateIfPossible missing
329:    function queueWithdrawal(

// @params missing
456:    function completeQueuedWithdrawals(

// @params missing
482:    function slashShares(
582:    function setWithdrawalDelayBlocks(uint256 _withdrawalDelayBlocks) external onlyOwner {
587:    function setStrategyWhitelister(address newStrategyWhitelister) external onlyOwner {
592:    function addStrategiesToDepositWhitelist(IStrategy[] calldata strategiesToWhitelist) external onlyStrategyWhitelister {
607:    function removeStrategiesFromDepositWhitelist(IStrategy[] calldata strategiesToRemoveFromWhitelist) external onlyStrategyWhitelister {
629:    function _addShares(address depositor, IStrategy strategy, uint256 shares) internal {
655:    function _depositIntoStrategy(address depositor, IStrategy strategy, IERC20 token, uint256 amount)
715:    function _removeStrategyFromStakerStrategyList(address depositor, uint256 strategyIndex, IStrategy strategy) internal {
745:    function _completeQueuedWithdrawal(QueuedWithdrawal calldata queuedWithdrawal, IERC20[] calldata tokens, uint256 middlewareTimesIndex, bool receiveAsTokens) onlyNotFrozen(queuedWithdrawal.delegatedAddress) internal {
811:    function _undelegate(address depositor) internal {
821:    function _withdrawBeaconChainETH(address staker, address recipient, uint256 amount) internal {
839:    function _setWithdrawalDelayBlocks(uint256 _withdrawalDelayBlocks) internal {
846:    function _setStrategyWhitelister(address newStrategyWhitelister) internal {
857:    function getDeposits(address depositor) external view returns (IStrategy[] memory, uint256[] memory) {

// @params and @return missing
679:    function _removeShares(address depositor, uint256 strategyIndex, IStrategy strategy, uint256 shareAmount)
871:    function stakerStrategyListLength(address staker) external view returns (uint256) {
876:    function calculateWithdrawalRoot(QueuedWithdrawal memory queuedWithdrawal) public pure returns (bytes32) {
```

---

### State variable and function names [4]

- Variables should be named according to their specifications
- private and internal `variables` should preppend with `underline`
- private and internal `functions` should preppend with `underline`
- constant state variables should be UPPER_CASE

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/libraries/Endian.sol

```solidity
// private and internal `functions` should preppend with `underline`
5:    function fromLittleEndianUint64(
```

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/libraries/Merkle.sol

```solidity
// private and internal `functions` should preppend with `underline`
29:    function verifyInclusionKeccak(
48:    function processInclusionProofKeccak(bytes memory proof, bytes32 leaf, uint256 index) internal pure returns (bytes32) {
80:    function verifyInclusionSha256(
99:    function processInclusionProofSha256(bytes memory proof, bytes32 leaf, uint256 index) internal view returns (bytes32) {
129:    function merkleizeSha256(
```

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/libraries/BeaconChainProofs.sol

```solidity
// private and internal `functions` should preppend with `underline`
130:    function computePhase0BeaconBlockHeaderRoot(bytes32[NUM_BEACON_BLOCK_HEADER_FIELDS] calldata blockHeaderFields) internal pure returns(bytes32) {
140:    function computePhase0BeaconStateRoot(bytes32[NUM_BEACON_STATE_FIELDS] calldata beaconStateFields) internal pure returns(bytes32) {
150:    function computePhase0ValidatorRoot(bytes32[NUM_VALIDATOR_FIELDS] calldata validatorFields) internal pure returns(bytes32) {  
160:    function computePhase0Eth1DataRoot(bytes32[NUM_ETH1_DATA_FIELDS] calldata eth1DataFields) internal pure returns(bytes32) {  
178:   function getBalanceFromBalanceRoot(uint40 validatorIndex, bytes32 balanceRoot) internal pure returns (uint64) {
192:    function verifyValidatorFields(
221:    function verifyValidatorBalance(
245:    function verifyWithdrawalProofs(
```
---

### Initialized variables [5]

- Variables are default initialized with 0 for `uint / int`, 0x0 for `address` and false for `bool`

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/DelayedWithdrawalRouter.sol

```solidity
// uint is already initialized at 0
16:    uint8 internal constant PAUSED_DELAYED_WITHDRAWAL_CLAIMS = 0;
138:        uint256 amountToSend = 0;
141:        uint256 i = 0;
```

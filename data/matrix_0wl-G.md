## Gas Optimizations

|        | Issue                                                                                                                                               |
| ------ | :-------------------------------------------------------------------------------------------------------------------------------------------------- |
| GAS-1  | Use `selfbalance()` instead of `address(this).balance`                                                                                              |
| GAS-2  | `ABI.ENCODE()` IS LESS EFFICIENT THAN `ABI.ENCODEPACKED()`                                                                                          |
| GAS-3  | Use assembly to check for `address(0)`                                                                                                              |
| GAS-4  | BEFORE SOME FUNCTIONS, WE SHOULD CHECK SOME VARIABLES FOR POSSIBLE GAS SAVE                                                                         |
| GAS-5  | USING CALLDATA INSTEAD OF MEMORY FOR READ-ONLY ARGUMENTS IN EXTERNAL FUNCTIONS SAVES GAS                                                            |
| GAS-6  | Setting the constructor to payable                                                                                                                  |
| GAS-7  | DIV BY 0                                                                                                                                            |
| GAS-8  | DUPLICATED REQUIRE()/REVERT() CHECKS SHOULD BE REFACTORED TO A MODIFIER OR FUNCTION                                                                 |
| GAS-9  | USE FUNCTION INSTEAD OF MODIFIERS                                                                                                                   |
| GAS-10 | INSTEAD OF CALCULATING A STATEVAR WITH KECCAK256() EVERY TIME THE CONTRACT IS MADE PRE CALCULATE THEM BEFORE AND ONLY GIVE THE RESULT TO A CONSTANT |
| GAS-11 | INTERNAL FUNCTIONS NOT CALLED BY THE CONTRACT SHOULD BE REMOVED TO SAVE DEPLOYMENT GAS                                                              |
| GAS-12 | CONSTANT VALUES SUCH AS A CALL TO KECCAK256(), SHOULD USE IMMUTABLE RATHER THAN CONSTANT                                                            |
| GAS-13 | KECCAK256() SHOULD ONLY NEED TO BE CALLED ON A SPECIFIC STRING LITERAL ONCE                                                                         |
| GAS-14 | MAKING CONSTANT VARIABLES PRIVATE WILL SAVE GAS DURING DEPLOYMENT                                                                                   |
| GAS-15 | MODIFIERS ARE REDUNDANT IF USED ONLY ONCE OR NOT USED AT ALL                                                                                        |
| GAS-16 | The increment in for loop postcondition can be made unchecked                                                                                       |
| GAS-17 | Proper data types                                                                                                                                   |
| GAS-18 | Use a more recent version of solidity                                                                                                               |
| GAS-19 | Reorder structure layout                                                                                                                            |
| GAS-20 | Add `unchecked {}` for subtractions where the operands cannot underflow because of a previous `require()`, `revert` or `if` statement               |
| GAS-21 | Using storage instead of memory for structs/arrays saves gas                                                                                        |
| GAS-22 | TERNARY OPERATION IS CHEAPER THAN IF-ELSE STATEMENT                                                                                                 |
| GAS-23 | USE BYTES32 INSTEAD OF STRING                                                                                                                       |
| GAS-24 | `internal` functions not called by the contract should be removed                                                                                   |

### [GAS-1] Use `selfbalance()` instead of `address(this).balance`

#### Description:

Use selfbalance() instead of address(this).balance when getting your contract’s balance of ETH to save gas.Use assembly when getting a contract's balance of ETH.

You can use `selfbalance()` instead of `address(this).balance` when getting your contract's balance of ETH to save gas.

Additionally, you can use `balance(address)` instead of `address.balance()` when getting an external contract's balance of ETH.

_Saves 15 gas when checking internal balance, 6 for external_

#### **Proof Of Concept**

```solidity
File: src/contracts/pods/EigenPod.sol

456:         _sendETH(podOwner, address(this).balance);

```

### [GAS-2] `ABI.ENCODE()` IS LESS EFFICIENT THAN `ABI.ENCODEPACKED()`

#### Description:

Use `abi.encodePacked()` where possible to save gas

#### **Proof Of Concept**

```solidity
File: src/contracts/core/StrategyManager.sol

150:         DOMAIN_SEPARATOR = keccak256(abi.encode(DOMAIN_TYPEHASH, keccak256(bytes("EigenLayer")), ORIGINAL_CHAIN_ID, address(this)));

268:         bytes32 structHash = keccak256(abi.encode(DEPOSIT_TYPEHASH, strategy, token, amount, nonce, expiry));

276:             bytes32 domain_separator = keccak256(abi.encode(DOMAIN_TYPEHASH, keccak256(bytes("EigenLayer")), block.chainid, address(this)));

```

```solidity
File: src/contracts/pods/EigenPodManager.sol

175:                         abi.encode(eigenPodBeacon, "")

202:                         abi.encode(eigenPodBeacon, "")

```

### [GAS-3] Use assembly to check for `address(0)`

#### Description:

_Saves 6 gas per instance_

#### **Proof Of Concept**

```solidity
File: src/contracts/core/StrategyManager.sol

343:         require(withdrawer != address(0), "StrategyManager.queueWithdrawal: cannot withdraw to zero address");

631:         require(depositor != address(0), "StrategyManager._addShares: depositor cannot be zero address");

684:         require(depositor != address(0), "StrategyManager._removeShares: depositor cannot be zero address");

```

```solidity
File: src/contracts/permissions/Pausable.sol

57:             address(pauserRegistry) == address(0) && address(_pauserRegistry) != address(0),

57:             address(pauserRegistry) == address(0) && address(_pauserRegistry) != address(0),

```

```solidity
File: src/contracts/permissions/PauserRegistry.sol

42:         require(newPauser != address(0), "PauserRegistry._setPauser: zero address input");

48:         require(newUnpauser != address(0), "PauserRegistry._setUnpauser: zero address input");

```

```solidity
File: src/contracts/pods/DelayedWithdrawalRouter.sol

45:         require(address(_eigenPodManager) != address(0), "DelayedWithdrawalRouter.constructor: _eigenPodManager cannot be zero address");

```

```solidity
File: src/contracts/pods/EigenPod.sol

153:         require(_podOwner != address(0), "EigenPod.initialize: podOwner cannot be zero address");

```

```solidity
File: src/contracts/pods/EigenPodManager.sol

114:         if(address(pod) == address(0)) {

196:         if (address(pod) == address(0)) {

211:         return address(ownerToPod[podOwner]) != address(0);

217:         require(stateRoot != bytes32(0), "EigenPodManager.getBeaconChainStateRoot: state root at blockNumber not yet finalized");

```

### [GAS-4] BEFORE SOME FUNCTIONS, WE SHOULD CHECK SOME VARIABLES FOR POSSIBLE GAS SAVE

#### Description:

Before transfer, we should check for amount being 0 so the function doesnt run when its not gonna do anything.

#### **Proof Of Concept**

```solidity
File: src/contracts/core/StrategyManager.sol

152:         _transferOwnership(initialOwner);

```

```solidity
File: src/contracts/pods/DelayedWithdrawalRouter.sol

50:         _transferOwnership(initOwner);

```

```solidity
File: src/contracts/pods/EigenPodManager.sol

91:         _transferOwnership(initialOwner);

```

### [GAS-5] USING CALLDATA INSTEAD OF MEMORY FOR READ-ONLY ARGUMENTS IN EXTERNAL FUNCTIONS SAVES GAS

#### Description:

Mark data types as `calldata` instead of `memory` where possible. This makes it so that the data is not automatically loaded into memory. If the data passed into the function does not need to be changed (like updating values in an array), it can be passed in as `calldata`. The one exception to this is if the argument must later be passed into another function that takes an argument that specifies `memory` storage.
[Source](https://forum.openzeppelin.com/t/a-collection-of-gas-optimisation-tricks/19966/6)
[Source 2](https://dev.to/juanxavier/advanced-gas-optimizations-tips-for-solidity-1j2f)

#### **Proof Of Concept**

```solidity
File: src/contracts/core/StrategyManager.sol

254:         bytes memory signature

876:     function calculateWithdrawalRoot(QueuedWithdrawal memory queuedWithdrawal) public pure returns (bytes32) {

```

```solidity
File: src/contracts/interfaces/IBeaconChainOracle.sol

46:     function addOracleSigners(address[] memory _oracleSigners) external;

53:     function removeOracleSigners(address[] memory _oracleSigners) external;

```

```solidity
File: src/contracts/interfaces/IDelegationManager.sol

38:     function delegateToBySignature(address staker, address operator, uint256 expiry, bytes memory signature) external;

```

```solidity
File: src/contracts/interfaces/IEigenPod.sol

101:         BeaconChainProofs.ValidatorFieldsAndBalanceProofs memory proofs,

```

```solidity
File: src/contracts/interfaces/IStrategyManager.sol

88:         bytes memory signature

203:         QueuedWithdrawal memory queuedWithdrawal

```

### [GAS-6] Setting the constructor to payable

#### Description:

Saves ~13 gas per instance

#### **Proof Of Concept**

```solidity
File: src/contracts/core/StrategyManager.sol

129:     constructor(IDelegationManager _delegation, IEigenPodManager _eigenPodManager, ISlasher _slasher)

```

```solidity
File: src/contracts/core/StrategyManagerStorage.sol

72:     constructor(IDelegationManager _delegation, IEigenPodManager _eigenPodManager, ISlasher _slasher) {

```

```solidity
File: src/contracts/permissions/PauserRegistry.sol

26:     constructor(address _pauser, address _unpauser) {

```

```solidity
File: src/contracts/pods/DelayedWithdrawalRouter.sol

44:     constructor(IEigenPodManager _eigenPodManager) {

```

```solidity
File: src/contracts/pods/EigenPod.sol

136:     constructor(

```

```solidity
File: src/contracts/pods/EigenPodManager.sol

76:     constructor(IETHPOSDeposit _ethPOS, IBeacon _eigenPodBeacon, IStrategyManager _strategyManager, ISlasher _slasher) {

```

```solidity
File: src/contracts/strategies/StrategyBase.sol

46:     constructor(IStrategyManager _strategyManager) {

```

### [GAS-7] DIV BY 0

#### Description:

Division by 0 can lead to accidentally revert.

#### **Proof Of Concept**

```solidity
File: src/contracts/strategies/StrategyBase.sol

152:             amountToSend = (_tokenBalance() * amountShares) / priorTotalShares;

201:             return (amountUnderlying * totalShares) / tokenBalance;

```

### [GAS-8] DUPLICATED REQUIRE()/REVERT() CHECKS SHOULD BE REFACTORED TO A MODIFIER OR FUNCTION

#### **Proof Of Concept**

```solidity
File: src/contracts/libraries/Merkle.sol

107:                     if iszero(staticcall(sub(gas(), 2000), 2, 0x00, 0x40, computedHash, 0x20)) {revert(0, 0)}

115:                     if iszero(staticcall(sub(gas(), 2000), 2, 0x00, 0x40, computedHash, 0x20)) {revert(0, 0)}

```

### [GAS-9] USE FUNCTION INSTEAD OF MODIFIERS

#### **Proof Of Concept**

```solidity
File: src/contracts/core/StrategyManager.sol

96:     modifier onlyNotFrozen(address staker) {

104:     modifier onlyFrozen(address staker) {

109:     modifier onlyEigenPodManager {

114:     modifier onlyStrategyWhitelister {

119:     modifier onlyStrategiesWhitelistedForDeposit(IStrategy strategy) {

```

```solidity
File: src/contracts/permissions/Pausable.sol

32:     modifier onlyPauser() {

37:     modifier onlyUnpauser() {

43:     modifier whenNotPaused() {

49:     modifier onlyWhenNotPaused(uint8 index) {

```

```solidity
File: src/contracts/permissions/PauserRegistry.sol

21:     modifier onlyUnpauser() {

```

```solidity
File: src/contracts/pods/DelayedWithdrawalRouter.sol

39:     modifier onlyEigenPod(address podOwner) {

```

```solidity
File: src/contracts/pods/EigenPod.sol

99:     modifier onlyEigenPodManager {

104:     modifier onlyEigenPodOwner {

109:     modifier onlyNotFrozen {

114:     modifier hasNeverRestaked {

120:     modifier proofIsForValidBlockNumber(uint64 blockNumber) {

131:     modifier onlyWhenNotPaused(uint8 index) {

```

```solidity
File: src/contracts/pods/EigenPodManager.sol

66:     modifier onlyEigenPod(address podOwner) {

71:     modifier onlyStrategyManager {

```

```solidity
File: src/contracts/strategies/StrategyBase.sol

40:     modifier onlyStrategyManager() {

```

### [GAS-10] INSTEAD OF CALCULATING A STATEVAR WITH KECCAK256() EVERY TIME THE CONTRACT IS MADE PRE CALCULATE THEM BEFORE AND ONLY GIVE THE RESULT TO A CONSTANT

#### **Proof Of Concept**

```solidity
File: src/contracts/core/StrategyManagerStorage.sol

18:         keccak256("EIP712Domain(string name,uint256 chainId,address verifyingContract)");

21:         keccak256("Deposit(address strategy,address token,uint256 amount,uint256 nonce,uint256 expiry)");

```

### [GAS-11] INTERNAL FUNCTIONS NOT CALLED BY THE CONTRACT SHOULD BE REMOVED TO SAVE DEPLOYMENT GAS

#### **Proof Of Concept**

```solidity
File: src/contracts/libraries/BeaconChainProofs.sol

130:     function computePhase0BeaconBlockHeaderRoot(bytes32[NUM_BEACON_BLOCK_HEADER_FIELDS] calldata blockHeaderFields) internal pure returns(bytes32) {

140:     function computePhase0BeaconStateRoot(bytes32[NUM_BEACON_STATE_FIELDS] calldata beaconStateFields) internal pure returns(bytes32) {

150:     function computePhase0ValidatorRoot(bytes32[NUM_VALIDATOR_FIELDS] calldata validatorFields) internal pure returns(bytes32) {

160:     function computePhase0Eth1DataRoot(bytes32[NUM_ETH1_DATA_FIELDS] calldata eth1DataFields) internal pure returns(bytes32) {

178:    function getBalanceFromBalanceRoot(uint40 validatorIndex, bytes32 balanceRoot) internal pure returns (uint64) {

```

```solidity
File: src/contracts/strategies/StrategyBase.sol

56:     function _initializeStrategyBase(IERC20 _underlyingToken, IPauserRegistry _pauserRegistry) internal onlyInitializing {

241:     function _tokenBalance() internal view virtual returns (uint256) {

```

### [GAS-12] CONSTANT VALUES SUCH AS A CALL TO KECCAK256(), SHOULD USE IMMUTABLE RATHER THAN CONSTANT

#### **Proof Of Concept**

```solidity
File: src/contracts/core/StrategyManagerStorage.sol

18:         keccak256("EIP712Domain(string name,uint256 chainId,address verifyingContract)");

21:         keccak256("Deposit(address strategy,address token,uint256 amount,uint256 nonce,uint256 expiry)");

```

### [GAS-13] KECCAK256() SHOULD ONLY NEED TO BE CALLED ON A SPECIFIC STRING LITERAL ONCE

#### Description:

It should be saved to an immutable variable, and the variable used instead. If the hash is being used as a part of a function selector, the cast to bytes4 should also only be done once.

#### **Proof Of Concept**

```solidity
File: src/contracts/core/StrategyManagerStorage.sol

18:         keccak256("EIP712Domain(string name,uint256 chainId,address verifyingContract)");

21:         keccak256("Deposit(address strategy,address token,uint256 amount,uint256 nonce,uint256 expiry)");

```

### [GAS-14] MAKING CONSTANT VARIABLES PRIVATE WILL SAVE GAS DURING DEPLOYMENT

#### Description:

When constants are marked public, extra getter functions are created, increasing the deployment cost. Marking these functions private will decrease gas cost. One can still read these variables through the source code. If they need to be accessed by an external contract, a separate single getter function can be used to return all constants as a tuple. There are four instances of public constants.

#### **Proof Of Concept**

```solidity
File: src/contracts/core/StrategyManagerStorage.sol

17:     bytes32 public constant DOMAIN_TYPEHASH =

20:     bytes32 public constant DEPOSIT_TYPEHASH =

46:     uint256 public constant MAX_WITHDRAWAL_DELAY_BLOCKS = 50400;

70:     IStrategy public constant beaconChainETHStrategy = IStrategy(0xbeaC0eeEeeeeEEeEeEEEEeeEEeEeeeEeeEEBEaC0);

```

```solidity
File: src/contracts/pods/DelayedWithdrawalRouter.sol

24:     uint256 public constant MAX_WITHDRAWAL_DELAY_BLOCKS = 50400;

```

### [GAS-15] MODIFIERS ARE REDUNDANT IF USED ONLY ONCE OR NOT USED AT ALL

#### **Proof Of Concept**

```solidity
File: src/contracts/permissions/Pausable.sol

37:     modifier onlyUnpauser() {

43:     modifier whenNotPaused() {

49:     modifier onlyWhenNotPaused(uint8 index) {

```

```solidity
File: src/contracts/pods/DelayedWithdrawalRouter.sol

39:     modifier onlyEigenPod(address podOwner) {

```

```solidity
File: src/contracts/pods/EigenPodManager.sol

71:     modifier onlyStrategyManager {

```

### [GAS-16] The increment in for loop postcondition can be made unchecked

#### Description:

This is only relevant if you are using the default solidity checked arithmetic.
the for loop postcondition, i.e., `i++` involves checked arithmetic, which is not required. This is because the value of i is always strictly less than `length <= 2**256 - 1`. Therefore, the theoretical maximum value of i to enter the for-loop body is `2**256 - 2`. This means that the `i++` in the for loop can never overflow. Regardless, the overflow checks are performed by the compiler.

Unfortunately, the Solidity optimizer is not smart enough to detect this and remove the checks.One can manually do this.

[Source](https://forum.openzeppelin.com/t/a-collection-of-gas-optimisation-tricks/19966/6)

#### **Proof Of Concept**

```solidity
File: src/contracts/core/StrategyManager.sol

466:         for(uint256 i = 0; i < queuedWithdrawals.length; i++) {

```

```solidity
File: src/contracts/libraries/Merkle.sol

137:         for (uint i = 0; i < numNodesInLayer; i++) {

145:             for (uint i = 0; i < numNodesInLayer; i++) {

```

```solidity
File: src/contracts/pods/DelayedWithdrawalRouter.sol

115:         for (uint256 i = 0; i < claimableDelayedWithdrawalsLength; i++) {

```

### [GAS-17] Proper data types

#### Description:

In Solidity, some data types are more expensive than others. It’s important to be aware of the most efficient type that can be used. Here are a few rules about data types.

Type uint should be used in place of type string whenever possible.

Type uint256 takes less gas to store than uint8

Type bytes should be used over byte[]

If the length of bytes can be limited, use the lowest amount possible from bytes1 to bytes32.

Type bytes32 is cheaper to use than type string and bytes.

If data can fit into 32 bytes, then you should use bytes32 datatype rather than bytes or strings as it is cheaper in solidity.

#### **Proof Of Concept**

```solidity
File: src/contracts/core/StrategyManager.sol

38:     uint8 internal constant PAUSED_DEPOSITS = 0;

40:     uint8 internal constant PAUSED_WITHDRAWALS = 1;

45:     bytes4 constant internal ERC1271_MAGICVALUE = 0x1626ba7e;

164:     function depositBeaconChainETH(address staker, uint256 amount)

254:         bytes memory signature

260:         returns (uint256 shares)

348:         uint96 nonce = uint96(numWithdrawalsQueued[msg.sender]);

592:     function addStrategiesToDepositWhitelist(IStrategy[] calldata strategiesToWhitelist) external onlyStrategyWhitelister {

```

```solidity
File: src/contracts/interfaces/IBeaconChainOracle.sol

10:     function latestConfirmedOracleBlockNumber() external view returns(uint64);

13:     function beaconStateRootAtBlockNumber(uint64 blockNumber) external view returns(bytes32);

19:     function hasVoted(uint64 blockNumber, address oracleSigner) external view returns(bool);

61:     function voteForBeaconChainStateRoot(uint64 blockNumber, bytes32 stateRoot) external;

```

```solidity
File: src/contracts/interfaces/IDelayedWithdrawalRouter.sol

7:         uint224 amount;

8:         uint32 blockCreated;

```

```solidity
File: src/contracts/interfaces/IEigenPod.sol

33:         uint32 creationBlockNumber;

35:         uint32 fraudproofPeriodEndBlockNumber;

37:         uint64 partialWithdrawalAmountGwei;

47:     function REQUIRED_BALANCE_GWEI() external view returns(uint64);

53:     function validatorStatus(uint40 validatorIndex) external view returns(VALIDATOR_STATUS);

56:     function restakedExecutionLayerGwei() external view returns(uint64);

82:     function mostRecentWithdrawalBlockNumber() external view returns (uint64);

86:     function provenPartialWithdrawal(uint40 validatorIndex, uint64 slot) external view returns (bool);

99:         uint64 oracleBlockNumber,

100:         uint40 validatorIndex,

102:         bytes32[] calldata validatorFields

119:         uint40 validatorIndex,

123:         uint64 oracleBlockNumber

137:         bytes calldata validatorFieldsProof,

141:         uint64 oracleBlockNumber

```

```solidity
File: src/contracts/interfaces/IPausable.sol

39:     function paused(uint8 index) external view returns (bool);

```

```solidity
File: src/contracts/interfaces/IServiceManager.sol

13:     function taskNumber() external view returns (uint32);

19:     function recordFirstStakeUpdate(address operator, uint32 serveUntilBlock) external;

22:     function recordStakeUpdate(address operator, uint32 updateBlock, uint32 serveUntilBlock, uint256 prevElement) external;

25:     function recordLastStakeUpdateAndRevokeSlashingAbility(address operator, uint32 serveUntilBlock) external;

28:     function latestServeUntilBlock() external view returns (uint32);

```

```solidity
File: src/contracts/interfaces/ISlasher.sol

13:         uint32 stalestUpdateBlock;

15:         uint32 latestServeUntilBlock;

21:         uint32 contractCanSlashOperatorUntilBlock;

23:         uint32 latestUpdateBlock;

53:     function recordFirstStakeUpdate(address operator, uint32 serveUntilBlock) external;

65:     function recordStakeUpdate(address operator, uint32 updateBlock, uint32 serveUntilBlock, uint256 insertAfter) external;

75:     function recordLastStakeUpdateAndRevokeSlashingAbility(address operator, uint32 serveUntilBlock) external;

90:     function contractCanSlashOperatorUntilBlock(address operator, address serviceContract) external view returns (uint32);

93:     function latestUpdateBlock(address operator, address serviceContract) external view returns (uint32);

110:     function canWithdraw(address operator, uint32 withdrawalStartBlock, uint256 middlewareTimesIndex) external returns(bool);

127:     function getMiddlewareTimesIndexBlock(address operator, uint32 index) external view returns(uint32);

130:     function getMiddlewareTimesIndexServeUntilBlock(address operator, uint32 index) external view returns(uint32);

```

```solidity
File: src/contracts/interfaces/IStrategyManager.sol

17:         uint96 nonce;

31:         uint32 withdrawalStartBlock;

```

```solidity
File: src/contracts/libraries/BeaconChainProofs.sol

100:     bytes8 internal constant UINT64_MASK = 0xffffffffffffffff;

105:         bytes blockHeaderProof;

106:         bytes withdrawalProof;

107:         bytes slotProof;

108:         bytes executionPayloadProof;

109:         bytes blockNumberProof;

110:         uint64 blockHeaderRootIndex;

111:         uint64 withdrawalIndex;

120:         bytes validatorFieldsProof;

121:         bytes validatorBalanceProof;

126:         bytes validatorProof;

127:         uint40 validatorIndex;

181:         uint64 validatorBalance = Endian.fromLittleEndianUint64(validatorBalanceLittleEndian);

193:         uint40 validatorIndex,

195:         bytes calldata proof,

222:         uint40 validatorIndex,

223:         bytes32 beaconStateRoot,

224:         bytes calldata proof,

225:         bytes32 balanceRoot

233:         uint256 balanceIndex = uint256(validatorIndex/4);

233:         uint256 balanceIndex = uint256(validatorIndex/4);

```

```solidity
File: src/contracts/libraries/Endian.sol

6:         bytes32 lenum

7:     ) internal pure returns (uint64 n) {

9:         n = uint64(uint256(lenum >> 192));

9:         n = uint64(uint256(lenum >> 192));

```

```solidity
File: src/contracts/libraries/Merkle.sol

30:         bytes memory proof,

31:         bytes32 root,

32:         bytes32 leaf,

33:         uint256 index

48:     function processInclusionProofKeccak(bytes memory proof, bytes32 leaf, uint256 index) internal pure returns (bytes32) {

48:     function processInclusionProofKeccak(bytes memory proof, bytes32 leaf, uint256 index) internal pure returns (bytes32) {

48:     function processInclusionProofKeccak(bytes memory proof, bytes32 leaf, uint256 index) internal pure returns (bytes32) {

48:     function processInclusionProofKeccak(bytes memory proof, bytes32 leaf, uint256 index) internal pure returns (bytes32) {

49:         bytes32 computedHash = leaf;

50:         for (uint256 i = 32; i <= proof.length; i+=32) {

81:         bytes memory proof,

82:         bytes32 root,

83:         bytes32 leaf,

84:         uint256 index

99:     function processInclusionProofSha256(bytes memory proof, bytes32 leaf, uint256 index) internal view returns (bytes32) {

99:     function processInclusionProofSha256(bytes memory proof, bytes32 leaf, uint256 index) internal view returns (bytes32) {

99:     function processInclusionProofSha256(bytes memory proof, bytes32 leaf, uint256 index) internal view returns (bytes32) {

99:     function processInclusionProofSha256(bytes memory proof, bytes32 leaf, uint256 index) internal view returns (bytes32) {

100:         bytes32[1] memory computedHash = [leaf];

101:         for (uint256 i = 32; i <= proof.length; i+=32) {

130:         bytes32[] memory leaves

130:         bytes32[] memory leaves

131:     ) internal pure returns (bytes32) {

133:         uint256 numNodesInLayer = leaves.length / 2;

135:         bytes32[] memory layer = new bytes32[](numNodesInLayer);

135:         bytes32[] memory layer = new bytes32[](numNodesInLayer);

135:         bytes32[] memory layer = new bytes32[](numNodesInLayer);

135:         bytes32[] memory layer = new bytes32[](numNodesInLayer);

137:         for (uint i = 0; i < numNodesInLayer; i++) {

145:             for (uint i = 0; i < numNodesInLayer; i++) {

```

```solidity
File: src/contracts/permissions/Pausable.sol

20:     uint256 private _paused;

22:     uint256 constant internal UNPAUSE_ALL = 0;

23:     uint256 constant internal PAUSE_ALL = type(uint256).max;

23:     uint256 constant internal PAUSE_ALL = type(uint256).max;

26:     event Paused(address indexed account, uint256 newPausedStatus);

29:     event Unpaused(address indexed account, uint256 newPausedStatus);

49:     modifier onlyWhenNotPaused(uint8 index) {

55:     function _initializePauser(IPauserRegistry _pauserRegistry, uint256 initPausedStatus) internal {

71:     function pause(uint256 newPausedStatus) external onlyPauser {

82:         _paused = type(uint256).max;

83:         emit Paused(msg.sender, type(uint256).max);

92:     function unpause(uint256 newPausedStatus) external onlyUnpauser {

100:     function paused() public view virtual returns (uint256) {

105:     function paused(uint8 index) public view virtual returns (bool) {

106:         uint256 mask = 1 << index;

115:     uint256[48] private __gap;

```

```solidity
File: src/contracts/pods/DelayedWithdrawalRouter.sol

13:     event WithdrawalDelayBlocksSet(uint256 previousValue, uint256 newValue);

13:     event WithdrawalDelayBlocksSet(uint256 previousValue, uint256 newValue);

16:     uint8 internal constant PAUSED_DELAYED_WITHDRAWAL_CLAIMS = 0;

22:     uint256 public withdrawalDelayBlocks;

24:     uint256 public constant MAX_WITHDRAWAL_DELAY_BLOCKS = 50400;

33:     event DelayedWithdrawalCreated(address podOwner, address recipient, uint256 amount, uint256 index);

33:     event DelayedWithdrawalCreated(address podOwner, address recipient, uint256 amount, uint256 index);

36:     event DelayedWithdrawalsClaimed(address recipient, uint256 amountClaimed, uint256 delayedWithdrawalsCompleted);

36:     event DelayedWithdrawalsClaimed(address recipient, uint256 amountClaimed, uint256 delayedWithdrawalsCompleted);

49:     function initialize(address initOwner, IPauserRegistry _pauserRegistry, uint256 initPausedStatus, uint256 _withdrawalDelayBlocks) external initializer {

49:     function initialize(address initOwner, IPauserRegistry _pauserRegistry, uint256 initPausedStatus, uint256 _withdrawalDelayBlocks) external initializer {

60:         uint224 withdrawalAmount = uint224(msg.value);

60:         uint224 withdrawalAmount = uint224(msg.value);

64:                 blockCreated: uint32(block.number)

79:     function claimDelayedWithdrawals(address recipient, uint256 maxNumberOfDelayedWithdrawalsToClaim)

91:     function claimDelayedWithdrawals(uint256 maxNumberOfDelayedWithdrawalsToClaim)

100:     function setWithdrawalDelayBlocks(uint256 newValue) external onlyOwner {

110:     function claimableUserDelayedWithdrawals(address user) external view returns (DelayedWithdrawal[] memory) {

111:         uint256 delayedWithdrawalsCompleted = _userWithdrawals[user].delayedWithdrawalsCompleted;

112:         uint256 delayedWithdrawalsLength = _userWithdrawals[user].delayedWithdrawals.length;

113:         uint256 claimableDelayedWithdrawalsLength = delayedWithdrawalsLength - delayedWithdrawalsCompleted;

114:         DelayedWithdrawal[] memory claimableDelayedWithdrawals = new DelayedWithdrawal[](claimableDelayedWithdrawalsLength);

114:         DelayedWithdrawal[] memory claimableDelayedWithdrawals = new DelayedWithdrawal[](claimableDelayedWithdrawalsLength);

115:         for (uint256 i = 0; i < claimableDelayedWithdrawalsLength; i++) {

122:     function userDelayedWithdrawalByIndex(address user, uint256 index) external view returns (DelayedWithdrawal memory) {

127:     function userWithdrawalsLength(address user) external view returns (uint256) {

132:     function canClaimDelayedWithdrawal(address user, uint256 index) external view returns (bool) {

137:     function _claimDelayedWithdrawals(address recipient, uint256 maxNumberOfDelayedWithdrawalsToClaim) internal {

138:         uint256 amountToSend = 0;

139:         uint256 delayedWithdrawalsCompletedBefore = _userWithdrawals[recipient].delayedWithdrawalsCompleted;

140:         uint256 _userWithdrawalsLength = _userWithdrawals[recipient].delayedWithdrawals.length;

141:         uint256 i = 0;

166:     function _setWithdrawalDelayBlocks(uint256 newValue) internal {

177:     uint256[48] private __gap;

```

```solidity
File: src/contracts/pods/EigenPod.sol

35:     using BytesLib for bytes;

38:     uint256 internal constant GWEI_TO_WEI = 1e9;

41:     uint256 internal constant VERIFY_OVERCOMMITTED_WINDOW_BLOCKS = 50400;

53:     uint64 public immutable REQUIRED_BALANCE_GWEI;

56:     uint256 public immutable REQUIRED_BALANCE_WEI;

66:     uint64 public mostRecentWithdrawalBlockNumber;

70:     uint64 public restakedExecutionLayerGwei;

76:     mapping(uint40 => VALIDATOR_STATUS) public validatorStatus;

79:     mapping(uint40 => mapping(uint64 => bool)) public provenPartialWithdrawal;

79:     mapping(uint40 => mapping(uint64 => bool)) public provenPartialWithdrawal;

82:     event EigenPodStaked(bytes pubkey);

85:     event ValidatorRestaked(uint40 validatorIndex);

88:     event ValidatorOvercommitted(uint40 validatorIndex);

91:     event FullWithdrawalRedeemed(uint40 validatorIndex, address indexed recipient, uint64 withdrawalAmountGwei);

91:     event FullWithdrawalRedeemed(uint40 validatorIndex, address indexed recipient, uint64 withdrawalAmountGwei);

94:     event PartialWithdrawalRedeemed(uint40 validatorIndex, address indexed recipient, uint64 partialWithdrawalAmountGwei);

94:     event PartialWithdrawalRedeemed(uint40 validatorIndex, address indexed recipient, uint64 partialWithdrawalAmountGwei);

97:     event RestakedBeaconChainETHWithdrawn(address indexed recipient, uint256 amount);

120:     modifier proofIsForValidBlockNumber(uint64 blockNumber) {

131:     modifier onlyWhenNotPaused(uint8 index) {

140:         uint256 _REQUIRED_BALANCE_WEI

146:         REQUIRED_BALANCE_GWEI = uint64(_REQUIRED_BALANCE_WEI / GWEI_TO_WEI);

158:     function stake(bytes calldata pubkey, bytes calldata signature, bytes32 depositDataRoot) external payable onlyEigenPodManager {

158:     function stake(bytes calldata pubkey, bytes calldata signature, bytes32 depositDataRoot) external payable onlyEigenPodManager {

158:     function stake(bytes calldata pubkey, bytes calldata signature, bytes32 depositDataRoot) external payable onlyEigenPodManager {

176:         uint64 oracleBlockNumber,

177:         uint40 validatorIndex,

179:         bytes32[] calldata validatorFields

179:         bytes32[] calldata validatorFields

189:         require(validatorFields[BeaconChainProofs.VALIDATOR_WITHDRAWAL_CREDENTIALS_INDEX] == bytes32(_podWithdrawalCredentials()),

192:         uint64 validatorCurrentBalanceGwei = BeaconChainProofs.getBalanceFromBalanceRoot(validatorIndex, proofs.balanceRoot);

199:         bytes32 beaconStateRoot = eigenPodManager.getBeaconChainStateRoot(oracleBlockNumber);

242:         uint40 validatorIndex,

244:         bytes32[] calldata validatorFields,

244:         bytes32[] calldata validatorFields,

245:         uint256 beaconChainETHStrategyIndex,

246:         uint64 oracleBlockNumber

255:         uint64 validatorCurrentBalanceGwei = BeaconChainProofs.getBalanceFromBalanceRoot(validatorIndex, proofs.balanceRoot);

261:         bytes32 beaconStateRoot = eigenPodManager.getBeaconChainStateRoot(oracleBlockNumber);

269:             uint64 slashedStatus = Endian.fromLittleEndianUint64(validatorFields[BeaconChainProofs.VALIDATOR_SLASHED_INDEX]);

307:         bytes calldata validatorFieldsProof,

308:         bytes32[] calldata validatorFields,

308:         bytes32[] calldata validatorFields,

309:         bytes32[] calldata withdrawalFields,

309:         bytes32[] calldata withdrawalFields,

310:         uint256 beaconChainETHStrategyIndex,

311:         uint64 oracleBlockNumber

331:         uint40 validatorIndex = uint40(Endian.fromLittleEndianUint64(withdrawalFields[BeaconChainProofs.WITHDRAWAL_VALIDATOR_INDEX_INDEX]));

331:         uint40 validatorIndex = uint40(Endian.fromLittleEndianUint64(withdrawalFields[BeaconChainProofs.WITHDRAWAL_VALIDATOR_INDEX_INDEX]));

337:         bytes32 beaconStateRoot = eigenPodManager.getBeaconChainStateRoot(oracleBlockNumber);

344:         uint64 withdrawalAmountGwei = Endian.fromLittleEndianUint64(withdrawalFields[BeaconChainProofs.WITHDRAWAL_VALIDATOR_AMOUNT_INDEX]);

347:         uint64 slot = Endian.fromLittleEndianUint64(withdrawalProofs.slotRoot);

362:         uint64 withdrawalAmountGwei,

363:         uint40 validatorIndex,

364:         uint256 beaconChainETHStrategyIndex,

368:         uint256 amountToSend;

375:                 amountToSend = uint256(withdrawalAmountGwei - REQUIRED_BALANCE_GWEI) * uint256(GWEI_TO_WEI);

375:                 amountToSend = uint256(withdrawalAmountGwei - REQUIRED_BALANCE_GWEI) * uint256(GWEI_TO_WEI);

382:                 eigenPodManager.recordOvercommittedBeaconChainETH(podOwner, beaconChainETHStrategyIndex, uint256(REQUIRED_BALANCE_GWEI - withdrawalAmountGwei) * GWEI_TO_WEI);

389:                 amountToSend = uint256(withdrawalAmountGwei - REQUIRED_BALANCE_GWEI) * uint256(GWEI_TO_WEI);

389:                 amountToSend = uint256(withdrawalAmountGwei - REQUIRED_BALANCE_GWEI) * uint256(GWEI_TO_WEI);

404:                 eigenPodManager.restakeBeaconChainETH(podOwner, uint256(withdrawalAmountGwei) * GWEI_TO_WEI);

422:     function _processPartialWithdrawal(uint64 withdrawalHappenedSlot, uint64 partialWithdrawalAmountGwei, uint40 validatorIndex, address recipient) internal {

422:     function _processPartialWithdrawal(uint64 withdrawalHappenedSlot, uint64 partialWithdrawalAmountGwei, uint40 validatorIndex, address recipient) internal {

422:     function _processPartialWithdrawal(uint64 withdrawalHappenedSlot, uint64 partialWithdrawalAmountGwei, uint40 validatorIndex, address recipient) internal {

429:         _sendETH(recipient, uint256(partialWithdrawalAmountGwei) * uint256(GWEI_TO_WEI));

429:         _sendETH(recipient, uint256(partialWithdrawalAmountGwei) * uint256(GWEI_TO_WEI));

439:         uint256 amountWei

445:         restakedExecutionLayerGwei -= uint64(amountWei / GWEI_TO_WEI);

455:         mostRecentWithdrawalBlockNumber = uint32(block.number);

460:     function _podWithdrawalCredentials() internal view returns(bytes memory) {

461:         return abi.encodePacked(bytes1(uint8(1)), bytes11(0), address(this));

461:         return abi.encodePacked(bytes1(uint8(1)), bytes11(0), address(this));

461:         return abi.encodePacked(bytes1(uint8(1)), bytes11(0), address(this));

464:     function _sendETH(address recipient, uint256 amountWei) internal {

473:     uint256[46] private __gap;

```

```solidity
File: src/contracts/pods/EigenPodManager.sol

37:     bytes internal constant beaconProxyBytecode = hex"608060405260405161090e38038061090e83398101604081905261002291610460565b61002e82826000610035565b505061058a565b61003e83610100565b6040516001600160a01b038416907f1cf3b03a6cf19fa2baba4df148e9dcabedea7f8a5c07840e207e5c089be95d3e90600090a260008251118061007f5750805b156100fb576100f9836001600160a01b0316635c60da1b6040518163ffffffff1660e01b8152600401602060405180830381865afa1580156100c5573d6000803e3d6000fd5b505050506040513d601f19601f820116820180604052508101906100e99190610520565b836102a360201b6100291760201c565b505b505050565b610113816102cf60201b6100551760201c565b6101725760405162461bcd60e51b815260206004820152602560248201527f455243313936373a206e657720626561636f6e206973206e6f74206120636f6e6044820152641d1c9858dd60da1b60648201526084015b60405180910390fd5b6101e6816001600160a01b0316635c60da1b6040518163ffffffff1660e01b8152600401602060405180830381865afa1580156101b3573d6000803e3d6000fd5b505050506040513d601f19601f820116820180604052508101906101d79190610520565b6102cf60201b6100551760201c565b61024b5760405162461bcd60e51b815260206004820152603060248201527f455243313936373a20626561636f6e20696d706c656d656e746174696f6e206960448201526f1cc81b9bdd08184818dbdb9d1c9858dd60821b6064820152608401610169565b806102827fa3f0ad74e5423aebfd80d3ef4346578335a9a72aeaee59ff6cb3582b35133d5060001b6102de60201b6100641760201c565b80546001600160a01b0319166001600160a01b039290921691909117905550565b60606102c883836040518060600160405280602781526020016108e7602791396102e1565b9392505050565b6001600160a01b03163b151590565b90565b6060600080856001600160a01b0316856040516102fe919061053b565b600060405180830381855af49150503d8060008114610339576040519150601f19603f3d011682016040523d82523d6000602084013e61033e565b606091505b5090925090506103508683838761035a565b9695505050505050565b606083156103c65782516103bf576001600160a01b0385163b6103bf5760405162461bcd60e51b815260206004820152601d60248201527f416464726573733a2063616c6c20746f206e6f6e2d636f6e74726163740000006044820152606401610169565b50816103d0565b6103d083836103d8565b949350505050565b8151156103e85781518083602001fd5b8060405162461bcd60e51b81526004016101699190610557565b80516001600160a01b038116811461041957600080fd5b919050565b634e487b7160e01b600052604160045260246000fd5b60005b8381101561044f578181015183820152602001610437565b838111156100f95750506000910152565b6000806040838503121561047357600080fd5b61047c83610402565b60208401519092506001600160401b038082111561049957600080fd5b818501915085601f8301126104ad57600080fd5b8151818111156104bf576104bf61041e565b604051601f8201601f19908116603f011681019083821181831017156104e7576104e761041e565b8160405282815288602084870101111561050057600080fd5b610511836020830160208801610434565b80955050505050509250929050565b60006020828403121561053257600080fd5b6102c882610402565b6000825161054d818460208701610434565b9190910192915050565b6020815260008251806020840152610576816040850160208701610434565b601f01601f19169190910160400192915050565b61034e806105996000396000f3fe60806040523661001357610011610017565b005b6100115b610027610022610067565b610100565b565b606061004e83836040518060600160405280602781526020016102f260279139610124565b9392505050565b6001600160a01b03163b151590565b90565b600061009a7fa3f0ad74e5423aebfd80d3ef4346578335a9a72aeaee59ff6cb3582b35133d50546001600160a01b031690565b6001600160a01b0316635c60da1b6040518163ffffffff1660e01b8152600401602060405180830381865afa1580156100d7573d6000803e3d6000fd5b505050506040513d601f19601f820116820180604052508101906100fb9190610249565b905090565b3660008037600080366000845af43d6000803e80801561011f573d6000f35b3d6000fd5b6060600080856001600160a01b03168560405161014191906102a2565b600060405180830381855af49150503d806000811461017c576040519150601f19603f3d011682016040523d82523d6000602084013e610181565b606091505b50915091506101928683838761019c565b9695505050505050565b6060831561020d578251610206576001600160a01b0385163b6102065760405162461bcd60e51b815260206004820152601d60248201527f416464726573733a2063616c6c20746f206e6f6e2d636f6e747261637400000060448201526064015b60405180910390fd5b5081610217565b610217838361021f565b949350505050565b81511561022f5781518083602001fd5b8060405162461bcd60e51b81526004016101fd91906102be565b60006020828403121561025b57600080fd5b81516001600160a01b038116811461004e57600080fd5b60005b8381101561028d578181015183820152602001610275565b8381111561029c576000848401525b50505050565b600082516102b4818460208701610272565b9190910192915050565b60208152600082518060208401526102dd816040850160208701610272565b601f01601f1916919091016040019291505056fe416464726573733a206c6f772d6c6576656c2064656c65676174652063616c6c206661696c6564a2646970667358221220d51e81d3bc5ed20a26aeb05dce7e825c503b2061aa78628027300c8d65b9d89a64736f6c634300080c0033416464726573733a206c6f772d6c6576656c2064656c65676174652063616c6c206661696c6564";

64:     event BeaconChainETHDeposited(address indexed podOwner, uint256 amount);

88:         uint256 _initPausedStatus

112:     function stake(bytes calldata pubkey, bytes calldata signature, bytes32 depositDataRoot) external payable {

112:     function stake(bytes calldata pubkey, bytes calldata signature, bytes32 depositDataRoot) external payable {

112:     function stake(bytes calldata pubkey, bytes calldata signature, bytes32 depositDataRoot) external payable {

127:     function restakeBeaconChainETH(address podOwner, uint256 amount) external onlyEigenPod(podOwner) {

139:     function recordOvercommittedBeaconChainETH(address podOwner, uint256 beaconChainETHStrategyIndex, uint256 amount) external onlyEigenPod(podOwner) {

139:     function recordOvercommittedBeaconChainETH(address podOwner, uint256 beaconChainETHStrategyIndex, uint256 amount) external onlyEigenPod(podOwner) {

150:     function withdrawRestakedBeaconChainETH(address podOwner, address recipient, uint256 amount)

171:                     bytes32(uint256(uint160(msg.sender))),

171:                     bytes32(uint256(uint160(msg.sender))),

171:                     bytes32(uint256(uint160(msg.sender))),

199:                     bytes32(uint256(uint160(podOwner))), //salt

199:                     bytes32(uint256(uint160(podOwner))), //salt

199:                     bytes32(uint256(uint160(podOwner))), //salt

215:     function getBeaconChainStateRoot(uint64 blockNumber) external view returns(bytes32) {

215:     function getBeaconChainStateRoot(uint64 blockNumber) external view returns(bytes32) {

216:         bytes32 stateRoot = beaconChainOracle.beaconStateRootAtBlockNumber(blockNumber);

217:         require(stateRoot != bytes32(0), "EigenPodManager.getBeaconChainStateRoot: state root at blockNumber not yet finalized");

226:     uint256[48] private __gap;

```

```solidity
File: src/contracts/pods/EigenPodPausingConstants.sol

10:     uint8 internal constant PAUSED_NEW_EIGENPODS = 0;

12:     uint8 internal constant PAUSED_WITHDRAW_RESTAKED_ETH = 1;

15:     uint8 internal constant PAUSED_EIGENPODS_VERIFY_CREDENTIALS = 2;

17:     uint8 internal constant PAUSED_EIGENPODS_VERIFY_OVERCOMMITTED = 3;

19:     uint8 internal constant PAUSED_EIGENPODS_VERIFY_WITHDRAWAL = 4;

```

```solidity
File: src/contracts/strategies/StrategyBase.sol

22:     uint8 internal constant PAUSED_DEPOSITS = 0;

23:     uint8 internal constant PAUSED_WITHDRAWALS = 1;

28:     uint96 internal constant MIN_NONZERO_TOTAL_SHARES = 1e9;

37:     uint256 public totalShares;

78:     function deposit(IERC20 token, uint256 amount)

84:         returns (uint256 newShares)

95:             uint256 priorTokenBalance = _tokenBalance() - amount;

105:         uint256 updatedTotalShares = totalShares + newShares;

121:     function withdraw(address depositor, IERC20 token, uint256 amountShares)

130:         uint256 priorTotalShares = totalShares;

137:         uint256 updatedTotalShares = priorTotalShares - amountShares;

148:         uint256 amountToSend;

162:     function explanation() external pure virtual override returns (string memory) {

172:     function sharesToUnderlyingView(uint256 amountShares) public view virtual override returns (uint256) {

172:     function sharesToUnderlyingView(uint256 amountShares) public view virtual override returns (uint256) {

186:     function sharesToUnderlying(uint256 amountShares) public view virtual override returns (uint256) {

186:     function sharesToUnderlying(uint256 amountShares) public view virtual override returns (uint256) {

196:     function underlyingToSharesView(uint256 amountUnderlying) public view virtual returns (uint256) {

196:     function underlyingToSharesView(uint256 amountUnderlying) public view virtual returns (uint256) {

197:         uint256 tokenBalance = _tokenBalance();

211:     function underlyingToShares(uint256 amountUnderlying) external view virtual returns (uint256) {

211:     function underlyingToShares(uint256 amountUnderlying) external view virtual returns (uint256) {

219:     function userUnderlyingView(address user) external view virtual returns (uint256) {

227:     function userUnderlying(address user) external virtual returns (uint256) {

235:     function shares(address user) public view virtual returns (uint256) {

241:     function _tokenBalance() internal view virtual returns (uint256) {

250:     uint256[48] private __gap;

```

### [GAS-18] Use a more recent version of solidity

#### Description:

Using at least `0.8.10` will save gas due to skipped extcodesize check if there is a return value. Currently the contracts are compiled using version `0.8.7` (Foundry default). It is easily changeable to `0.8.17` using the command

#### **Proof Of Concept**

```solidity
File: src/contracts/core/StrategyManager.sol

2: pragma solidity =0.8.12;

```

```solidity
File: src/contracts/core/StrategyManagerStorage.sol

2: pragma solidity =0.8.12;

```

```solidity
File: src/contracts/interfaces/IBeaconChainOracle.sol

2: pragma solidity =0.8.12;

```

```solidity
File: src/contracts/interfaces/IDelayedWithdrawalRouter.sol

2: pragma solidity =0.8.12;

```

```solidity
File: src/contracts/interfaces/IDelegationManager.sol

2: pragma solidity =0.8.12;

```

```solidity
File: src/contracts/interfaces/IETHPOSDeposit.sol

12: pragma solidity =0.8.12;

```

```solidity
File: src/contracts/interfaces/IEigenPod.sol

2: pragma solidity =0.8.12;

```

```solidity
File: src/contracts/interfaces/IEigenPodManager.sol

2: pragma solidity =0.8.12;

```

```solidity
File: src/contracts/interfaces/IPausable.sol

2: pragma solidity =0.8.12;

```

```solidity
File: src/contracts/interfaces/IPauserRegistry.sol

2: pragma solidity =0.8.12;

```

```solidity
File: src/contracts/interfaces/IServiceManager.sol

2: pragma solidity =0.8.12;

```

```solidity
File: src/contracts/interfaces/ISlasher.sol

2: pragma solidity =0.8.12;

```

```solidity
File: src/contracts/interfaces/IStrategy.sol

2: pragma solidity =0.8.12;

```

```solidity
File: src/contracts/interfaces/IStrategyManager.sol

2: pragma solidity =0.8.12;

```

```solidity
File: src/contracts/libraries/BeaconChainProofs.sol

3: pragma solidity =0.8.12;

```

```solidity
File: src/contracts/libraries/Endian.sol

2: pragma solidity =0.8.12;

```

```solidity
File: src/contracts/libraries/Merkle.sol

4: pragma solidity =0.8.12;

```

```solidity
File: src/contracts/permissions/Pausable.sol

3: pragma solidity =0.8.12;

```

```solidity
File: src/contracts/permissions/PauserRegistry.sol

2: pragma solidity =0.8.12;

```

```solidity
File: src/contracts/pods/DelayedWithdrawalRouter.sol

2: pragma solidity =0.8.12;

```

```solidity
File: src/contracts/pods/EigenPod.sol

2: pragma solidity =0.8.12;

```

```solidity
File: src/contracts/pods/EigenPodManager.sol

2: pragma solidity =0.8.12;

```

```solidity
File: src/contracts/pods/EigenPodPausingConstants.sol

2: pragma solidity =0.8.12;

```

```solidity
File: src/contracts/strategies/StrategyBase.sol

2: pragma solidity =0.8.12;

```

### [GAS-19] Reorder structure layout

#### Description:

Structures could be optimized moving the position of certain values in order to save a lot slots.

For example Enums are represented by integers; the possibility listed first by 0, the next by 1, and so forth. An enum type just acts like uintN, where N is the smallest legal value large enough to accomodate all the possibilities.

[Source](https://ethdebug.github.io/solidity-data-representation)

[Source](hhttps://docs.soliditylang.org/en/v0.8.17/internals/layout_in_storage.html#storage-inplace-encoding)

#### **Proof Of Concept**

```solidity
File: src/contracts/interfaces/IStrategyManager.sol

26:     struct QueuedWithdrawal {

```

### [GAS-20] Add `unchecked {}` for subtractions where the operands cannot underflow because of a previous `require()`, `revert` or `if` statement

#### Description:

Solidity version 0.8+ comes with implicit overflow and underflow checks on unsigned integers. When an overflow or an underflow isn’t possible (as an example, when a comparison is made before the arithmetic operation), some gas can be saved by using an unchecked block [see resource](https://github.com/ethereum/solidity/issues/10695)

`require(a <= b); x = b - a => require(a <= b); unchecked { x = b - a }`

`if(a <= b); x = b - a => if(a <= b); unchecked { x = b - a }`

This will stop the check for overflow and underflow so it will save gas

#### **Proof Of Concept**

```solidity
File: src/contracts/core/StrategyManager.sol

190:         if (amount > userShares) {
              uint256 debt = amount - userShares;
            beaconChainETHSharesToDecrementOnWithdrawal[overcommittedPodOwner] += debt;
            amount -= debt;
        }

824:             if (amount > amountToDecrement) {
                beaconChainETHSharesToDecrementOnWithdrawal[staker] = 0;
                // decrease `amount` appropriately, so less is sent at the end
                amount -= amountToDecrement;
}

```

```solidity
File: src/contracts/pods/EigenPod.sol

373:             if (withdrawalAmountGwei >= REQUIRED_BALANCE_GWEI) {
                // then the excess is immediately withdrawable
                amountToSend = uint256(withdrawalAmountGwei - REQUIRED_BALANCE_GWEI) * uint256(GWEI_TO_WEI);
                // and the extra execution layer ETH in the contract is REQUIRED_BALANCE_GWEI, which must be withdrawn through EigenLayer's normal withdrawal process
                restakedExecutionLayerGwei += REQUIRED_BALANCE_GWEI;

387:             if (withdrawalAmountGwei >= REQUIRED_BALANCE_GWEI) {
                    // then the excess is immediately withdrawable
                amountToSend = uint256(withdrawalAmountGwei - REQUIRED_BALANCE_GWEI) * uint256(GWEI_TO_WEI);
                // and the extra execution layer ETH in the contract is REQUIRED_BALANCE_GWEI, which must be withdrawn through EigenLayer's normal withdrawal process
                restakedExecutionLayerGwei += REQUIRED_BALANCE_GWEI;
                /**
                 * since in `verifyOvercommittedStake` the podOwner's beaconChainETH shares are decremented by `REQUIRED_BALANCE_WEI`, we must reverse the process here,
                 * in order to allow the podOwner to complete their withdrawal through EigenLayer's normal withdrawal process
                 */
                eigenPodManager.restakeBeaconChainETH(podOwner, REQUIRED_BALANCE_WEI);

```

```solidity
File: src/contracts/strategies/StrategyBase.sol

106:         require(updatedTotalShares >= MIN_NONZERO_TOTAL_SHARES,

131:         require(

139:         require(updatedTotalShares >= MIN_NONZERO_TOTAL_SHARES || updatedTotalShares == 0,

```

### [GAS-21] Using storage instead of memory for structs/arrays saves gas

#### Description:

When fetching data from a storage location, assigning the data to a memory variable causes all fields of the struct/array to be read from storage, which incurs a Gcoldsload (2100 gas) for each field of the struct/array. If the fields are read from the new memory variable, they incur an additional `MLOAD` rather than a cheap stack read. Instead of declearing the variable with the memory keyword, declaring the variable with the storage keyword and caching any fields that need to be re-read in stack variables, will be much cheaper, only incuring the Gcoldsload for the fields actually read.

The only time it makes sense to read the whole struct/array into a memory variable, is if the full struct/array is being returned by the function, is being passed to a function that requires memory, or if the array/struct is being read from another memory array/struct

#### **Proof Of Concept**

```solidity
File: src/contracts/core/StrategyManager.sol

200:         IStrategy[] memory strategies = new IStrategy[](1);

202:         uint256[] memory shareAmounts = new uint256[](1);

254:         bytes memory signature

387:         QueuedWithdrawal memory queuedWithdrawal;

390:             WithdrawerAndNonce memory withdrawerAndNonce = WithdrawerAndNonce({

857:     function getDeposits(address depositor) external view returns (IStrategy[] memory, uint256[] memory) {

857:     function getDeposits(address depositor) external view returns (IStrategy[] memory, uint256[] memory) {

859:         uint256[] memory shares = new uint256[](strategiesLength);

876:     function calculateWithdrawalRoot(QueuedWithdrawal memory queuedWithdrawal) public pure returns (bytes32) {

```

```solidity
File: src/contracts/interfaces/IBeaconChainOracle.sol

46:     function addOracleSigners(address[] memory _oracleSigners) external;

53:     function removeOracleSigners(address[] memory _oracleSigners) external;

```

```solidity
File: src/contracts/interfaces/IDelayedWithdrawalRouter.sol

40:     function userWithdrawals(address user) external view returns (UserDelayedWithdrawals memory);

43:     function claimableUserDelayedWithdrawals(address user) external view returns (DelayedWithdrawal[] memory);

46:     function userDelayedWithdrawalByIndex(address user, uint256 index) external view returns (DelayedWithdrawal memory);

```

```solidity
File: src/contracts/interfaces/IDelegationManager.sol

38:     function delegateToBySignature(address staker, address operator, uint256 expiry, bytes memory signature) external;

```

```solidity
File: src/contracts/interfaces/IETHPOSDeposit.sol

40:     function get_deposit_count() external view returns (bytes memory);

```

```solidity
File: src/contracts/interfaces/IEigenPod.sol

101:         BeaconChainProofs.ValidatorFieldsAndBalanceProofs memory proofs,

```

```solidity
File: src/contracts/interfaces/ISlasher.sol

121:     function operatorToMiddlewareTimes(address operator, uint256 arrayIndex) external view returns (MiddlewareTimes memory);

```

```solidity
File: src/contracts/interfaces/IStrategy.sol

82:     function explanation() external view returns (string memory);

```

```solidity
File: src/contracts/interfaces/IStrategyManager.sol

88:         bytes memory signature

100:     function getDeposits(address depositor) external view returns (IStrategy[] memory, uint256[] memory);

100:     function getDeposits(address depositor) external view returns (IStrategy[] memory, uint256[] memory);

203:         QueuedWithdrawal memory queuedWithdrawal

```

```solidity
File: src/contracts/libraries/BeaconChainProofs.sol

131:         bytes32[] memory paddedHeaderFields = new bytes32[](2**BEACON_BLOCK_HEADER_FIELD_TREE_HEIGHT);

141:         bytes32[] memory paddedBeaconStateFields = new bytes32[](2**BEACON_STATE_FIELD_TREE_HEIGHT);

151:         bytes32[] memory paddedValidatorFields = new bytes32[](2**VALIDATOR_FIELD_TREE_HEIGHT);

161:         bytes32[] memory paddedEth1DataFields = new bytes32[](2**ETH1_DATA_FIELD_TREE_HEIGHT);

```

```solidity
File: src/contracts/libraries/Merkle.sol

30:         bytes memory proof,

48:     function processInclusionProofKeccak(bytes memory proof, bytes32 leaf, uint256 index) internal pure returns (bytes32) {

81:         bytes memory proof,

99:     function processInclusionProofSha256(bytes memory proof, bytes32 leaf, uint256 index) internal view returns (bytes32) {

100:         bytes32[1] memory computedHash = [leaf];

130:         bytes32[] memory leaves

135:         bytes32[] memory layer = new bytes32[](numNodesInLayer);

```

```solidity
File: src/contracts/pods/DelayedWithdrawalRouter.sol

62:             DelayedWithdrawal memory delayedWithdrawal = DelayedWithdrawal({

105:     function userWithdrawals(address user) external view returns (UserDelayedWithdrawals memory) {

110:     function claimableUserDelayedWithdrawals(address user) external view returns (DelayedWithdrawal[] memory) {

114:         DelayedWithdrawal[] memory claimableDelayedWithdrawals = new DelayedWithdrawal[](claimableDelayedWithdrawalsLength);

122:     function userDelayedWithdrawalByIndex(address user, uint256 index) external view returns (DelayedWithdrawal memory) {

144:             DelayedWithdrawal memory delayedWithdrawal = _userWithdrawals[recipient].delayedWithdrawals[delayedWithdrawalsCompletedBefore + i];

```

```solidity
File: src/contracts/pods/EigenPod.sol

460:     function _podWithdrawalCredentials() internal view returns(bytes memory) {

```

```solidity
File: src/contracts/strategies/StrategyBase.sol

162:     function explanation() external pure virtual override returns (string memory) {

```

### [GAS-22] TERNARY OPERATION IS CHEAPER THAN IF-ELSE STATEMENT

#### Description:

There are instances where a ternary operation can be used instead of if-else statement. In these cases, using ternary operation will save modest amounts of gas.
Note that this optimization seems to be dependent on usage of a more recent Solidity version. The following gas savings are on version 0.8.17.
[Code4arena example](https://code4rena.com/reports/2022-10-traderjoe/#g-03-ternary-operation-is-cheaper-than-if-else-statement)

#### **Proof Of Concept**

```solidity
File: src/contracts/pods/EigenPod.sol

354:    if (Endian.fromLittleEndianUint64(validatorFields[BeaconChainProofs.VALIDATOR_WITHDRAWABLE_EPOCH_INDEX]) <= slot/BeaconChainProofs.SLOTS_PER_EPOCH) {
            _processFullWithdrawal(withdrawalAmountGwei, validatorIndex, beaconChainETHStrategyIndex, podOwner, validatorStatus[validatorIndex]);
        } else {
            _processPartialWithdrawal(slot, withdrawalAmountGwei, validatorIndex, podOwner);
        }

```

```solidity
File: src/contracts/strategies/StrategyBase.sol

149:    if (priorTotalShares == amountShares) {
            amountToSend = _tokenBalance();
        } else {
            amountToSend = (_tokenBalance() * amountShares) / priorTotalShares;
        }

173:    if (totalShares == 0) {
            return amountShares;
        } else {
            return (_tokenBalance() * amountShares) / totalShares;
        }

198:    if (tokenBalance == 0 || totalShares == 0) {
            return amountUnderlying;
        } else {
            return (amountUnderlying * totalShares) / tokenBalance;
        }

```

### [GAS-23] USE BYTES32 INSTEAD OF STRING

#### Description:

Use bytes32 instead of string to save gas whenever possible. String is a dynamic data structure and therefore is more gas consuming then bytes32.

#### **Proof Of Concept**

```solidity
File: src/contracts/core/StrategyManagerStorage.sol

18:         keccak256("EIP712Domain(string name,uint256 chainId,address verifyingContract)");

```

```solidity
File: src/contracts/interfaces/IStrategy.sol

82:     function explanation() external view returns (string memory);

```

```solidity
File: src/contracts/strategies/StrategyBase.sol

162:     function explanation() external pure virtual override returns (string memory) {

```

### [GAS-24] `internal` functions not called by the contract should be removed

#### Description:

If the functions are required by an interface, the contract should inherit from that interface and use the `override` keyword

#### **Proof Of Concept**

```solidity
File: src/contracts/libraries/BeaconChainProofs.sol

130:     function computePhase0BeaconBlockHeaderRoot(bytes32[NUM_BEACON_BLOCK_HEADER_FIELDS] calldata blockHeaderFields) internal pure returns(bytes32) {

140:     function computePhase0BeaconStateRoot(bytes32[NUM_BEACON_STATE_FIELDS] calldata beaconStateFields) internal pure returns(bytes32) {

150:     function computePhase0ValidatorRoot(bytes32[NUM_VALIDATOR_FIELDS] calldata validatorFields) internal pure returns(bytes32) {

160:     function computePhase0Eth1DataRoot(bytes32[NUM_ETH1_DATA_FIELDS] calldata eth1DataFields) internal pure returns(bytes32) {

178:    function getBalanceFromBalanceRoot(uint40 validatorIndex, bytes32 balanceRoot) internal pure returns (uint64) {

192:     function verifyValidatorFields(

221:     function verifyValidatorBalance(

245:     function verifyWithdrawalProofs(

```

```solidity
File: src/contracts/libraries/Endian.sol

5:     function fromLittleEndianUint64(

```

```solidity
File: src/contracts/libraries/Merkle.sol

29:     function verifyInclusionKeccak(

80:     function verifyInclusionSha256(

129:     function merkleizeSha256(

```

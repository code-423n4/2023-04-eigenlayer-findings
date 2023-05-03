## Gas

## Summary
|Number | Problems  | instance |
|------ |-----------|----------|
|[Gas-1]| Using storage instead of memory for structs/arrays saves gas | 4
|[Gas-2]| Expressions for constant values such as a call to keccak256(), should use immutable rather than constant|2
|[Gas‑3]| ++i/i++ should be unchecked{++i}/unchecked{i++} when it is not possible for them to overflow, as is the case when used in for- and while-loops|2
|[Gas-4]| >= costs less gas than >|2
|[Gas-5]|Use double if statements instead of &&|2
|[Gas-6]|Make 3 event parameters indexed when possible|15
|[Gas-7] |public functions to external|2
|[Gas-8]| Use calldata instead of memory|3
|[Gas-9]|Use of Bit shift operators|3
|[Gas-10]|There is no need to initialize variables with default values|24
[Gass-11]| abi.encode() is less efficient than abi.encodePacked()|6
| [Gas-12]|Avoid using state variable in emit|1
|[Gas-13]| Save gas with the use of the import statement|7




## Detials

### [Gas-1] Using storage instead of memory for structs/arrays saves gas
```solidity
200     IStrategy[] memory strategies = new IStrategy[](1);
```
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/core/StrategyManager.sol#L200

```solidity
62      DelayedWithdrawal memory delayedWithdrawal = DelayedWithdrawal({

114     DelayedWithdrawal[] memory claimableDelayedWithdrawals = new DelayedWithdrawal[](claimableDelayedWithdrawalsLength);

144     DelayedWithdrawal memory delayedWithdrawal = _userWithdrawals[recipient].delayedWithdrawals[delayedWithdrawalsCompletedBefore + i];
```
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/pods/DelayedWithdrawalRouter.sol#L62
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/pods/DelayedWithdrawalRouter.sol#L114
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/pods/DelayedWithdrawalRouter.sol#L144

### [Gas-2] Expressions for constant values such as a call to keccak256(), should use immutable rather than constant

```solidity
17       bytes32 public constant DEPOSIT_TYPEHASH =
        keccak256("Deposit(address strategy,address token,uint256 amount,uint256 nonce,uint256 expiry)");
20      bytes32 public constant DEPOSIT_TYPEHASH =
        keccak256("Deposit(address strategy,address token,uint256 amount,uint256 nonce,uint256 expiry)");        

```
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/core/StrategyManagerStorage.sol#l17
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/core/StrategyManagerStorage.sol#l20


### [Gas‑3] ++i/i++ should be unchecked{++i}/unchecked{i++} when it is not possible for them to overflow, as is the case when used in for- and while-loops
# this is miss from bots
```solidity

50     for (uint256 i = 32; i <= proof.length; i+=32) {

101    for (uint256 i = 32; i <= proof.length; i+=32) {    
```
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/libraries/Merkle.sol#l50
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/libraries/Merkle.sol#101

### [Gas-4] >= costs less gas than >
The compiler uses opcodes GT and ISZERO for solidity code that uses >, but only requires LT for >=, which saves 3 gas

```solidity

190     if (amount > userShares) {

824     if (amount > amountToDecrement) {
```
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/core/StrategyManager.sol#190
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/core/StrategyManager.sol#824

### [Gas-5]Use double if statements instead of &&
```solidity

422    if (undelegateIfPossible && stakerStrategyList[msg.sender].length == 0) {

562    if (indicesToSkipIndex < indicesToSkip.length && indicesToSkip[indicesToSkipIndex] == i) {
```
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/core/StrategyManager.sol#422
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/core/StrategyManager.sol#562

### [Gas-6]Make 3 event parameters indexed when possible
It's the most gas efficient to make up to 3 event parameters indexed. If there are less than 3 parameters, you need to make all parameters indexed.

```solidity
13     event WithdrawalDelayBlocksSet(uint256 previousValue, uint256 newValue);

33     event DelayedWithdrawalCreated(address podOwner, address recipient, uint256 amount, uint256 index);

36     event DelayedWithdrawalsClaimed(address recipient, uint256 amountClaimed, uint256 delayedWithdrawalsCompleted);
```
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/pods/DelayedWithdrawalRouter.sol#13
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/pods/DelayedWithdrawalRouter.sol#33
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/pods/DelayedWithdrawalRouter.sol#36


```solidity
82     event EigenPodStaked(bytes pubkey);

85     event ValidatorRestaked(uint40 validatorIndex);

88     event ValidatorOvercommitted(uint40 validatorIndex);

91      event FullWithdrawalRedeemed(uint40 validatorIndex, address indexed recipient, uint64 withdrawalAmountGwei);

94      event PartialWithdrawalRedeemed(uint40 validatorIndex, address indexed recipient, uint64 partialWithdrawalAmountGwei);

97      event RestakedBeaconChainETHWithdrawn(address indexed recipient, uint256 amount);
```
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/pods/EigenPod.sol#82
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/pods/EigenPod.sol#85
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/pods/EigenPod.sol#88
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/pods/EigenPod.sol#91
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/pods/EigenPod.sol#94
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/pods/EigenPod.sol#97

```solidity
64     event BeaconChainETHDeposited(address indexed podOwner, uint256 amount);
```
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/pods/EigenPodManager.sol#L64

```solidity
26     event Paused(address indexed account, uint256 newPausedStatus);

29     event Unpaused(address indexed account, uint256 newPausedStatus);
```
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/permissions/Pausable.sol#26
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/permissions/Pausable.sol#29

```solidity
17     event PauserChanged(address previousPauser, address newPauser);

19     event UnpauserChanged(address previousUnpauser, address newUnpauser);

```
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/permissions/PauserRegistry.sol#l17
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/permissions/PauserRegistry.sol#l19

```solidity
19     event DepositEvent(bytes pubkey, bytes withdrawal_credentials, bytes amount, bytes signature, bytes index);
```
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/interfaces/IETHPOSDeposit.sol#L19


### [Gas-7] public functions to external
External call cost is less expensive than of public functions.
Contracts are allowed to override their parents’ functions and change the visibility from external to public.

```solidity
172            function sharesToUnderlyingView(uint256 amountShares) public view virtual override returns (uint256) {

186            function sharesToUnderlying(uint256 amountShares) public view virtual override returns (uint256) {
```
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/strategies/StrategyBase.sol#l172
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/strategies/StrategyBase.sol#l186


### [Gas-8] Use calldata instead of memory
Some methods are declared as external but the arguments are defined as memory instead of as calldata.

```solidity
46     function addOracleSigners(address[] memory _oracleSigners) external;

53     function removeOracleSigners(address[] memory _oracleSigners) external;
```
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/interfaces/IBeaconChainOracle.sol#l46
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/interfaces/IBeaconChainOracle.sol#l53

```solidity
38          function delegateToBySignature(address staker, address operator, uint256 expiry, bytes memory signature) external;
```
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/interfaces/IDelegationManager.sol#L38

### [Gas-9]Use of Bit shift operators
Check if arithmethic operations can be achieved using bitwise operators, if yes, implement same operations using bitwise operators and compare the gas consumed. Usually bitwise logic will be cheaper (ofc we cannot use bitwise everywhere, there some limitations)


```solidity
51     if(index % 2 == 0) {

102    if(index % 2 == 0) {
```
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/libraries/Merkle.sol#l51
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/libraries/Merkle.sol#l102

```solidity
179     uint256 bitShiftAmount = (validatorIndex % 4) * 64;
```
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/libraries/BeaconChainProofs.sol#L179

### [Gas-10]There is no need to initialize variables with default values

```solidity

38     uint8 internal constant PAUSED_DEPOSITS = 0;

358    for (uint256 i = 0; i < strategies.length;) {

466    for(uint256 i = 0; i < queuedWithdrawals.length; i++) {

498    for (uint256 i = 0; i < strategiesLength;) {

560    for (uint256 i = 0; i < strategiesLength;) {

594    for (uint256 i = 0; i < strategiesToWhitelistLength;) {

609    for (uint256 i = 0; i < strategiesToRemoveFromWhitelistLength;) {

780    for (uint256 i = 0; i < strategiesLength;) {

797    for (uint256 i = 0; i < strategiesLength;) {

861    for (uint256 i = 0; i < strategiesLength;) {        
```
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/core/StrategyManager.sol#l38
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/core/StrategyManager.sol#l358
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/core/StrategyManager.sol#l466
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/core/StrategyManager.sol#l498
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/core/StrategyManager.sol#l560
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/core/StrategyManager.sol#l594
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/core/StrategyManager.sol#l609
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/core/StrategyManager.sol#l780
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/core/StrategyManager.sol#l797
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/core/StrategyManager.sol#l861


```solidity
22     uint8 internal constant PAUSED_DEPOSITS = 0;
```
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/strategies/StrategyBase.sol#L22


```solidity
16      uint8 internal constant PAUSED_DELAYED_WITHDRAWAL_CLAIMS = 0;

115     for (uint256 i = 0; i < claimableDelayedWithdrawalsLength; i++) {

138     uint256 amountToSend = 0

141     uint256 i = 0;
```
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/pods/DelayedWithdrawalRouter.sol#l16
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/pods/DelayedWithdrawalRouter.sol#l115
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/pods/DelayedWithdrawalRouter.sol#l138
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/pods/DelayedWithdrawalRouter.sol#l141

```solidity
10      uint8 internal constant PAUSED_NEW_EIGENPODS = 0;
```
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/pods/EigenPodPausingConstants.sol#L10

```solidity
22      uint256 constant internal UNPAUSE_ALL = 0;
```
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/permissions/Pausable.sol#L22

```solidity
65      uint256 internal constant SLOT_INDEX = 0;

133     for (uint256 i = 0; i < NUM_BEACON_BLOCK_HEADER_FIELDS; ++i) {

143     for (uint256 i = 0; i < NUM_BEACON_STATE_FIELDS; ++i) {

153     for (uint256 i = 0; i < NUM_VALIDATOR_FIELDS; ++i) {

163     for (uint256 i = 0; i < ETH1_DATA_FIELD_TREE_HEIGHT; ++i) {            
```
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/libraries/BeaconChainProofs.sol#l65
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/libraries/BeaconChainProofs.sol#l133
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/libraries/BeaconChainProofs.sol#l143
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/libraries/BeaconChainProofs.sol#l153
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/libraries/BeaconChainProofs.sol#l163

```solidity
137     for (uint i = 0; i < numNodesInLayer; i++) {

145     for (uint i = 0; i < numNodesInLayer; i++) {
```
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/libraries/Merkle.sol#l137
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/libraries/Merkle.sol#l145

### [Gass-11] abi.encode() is less efficient than abi.encodePacked()

```solidity
175     abi.encode(eigenPodBeacon, "")

202     abi.encode(eigenPodBeacon, "")
```
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/pods/EigenPodManager.sol#l175
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/pods/EigenPodManager.sol#l202

```solidity
150     DOMAIN_SEPARATOR = keccak256(abi.encode(DOMAIN_TYPEHASH, keccak256(bytes("EigenLayer")), ORIGINAL_CHAIN_ID, address(this)));

268     bytes32 structHash = keccak256(abi.encode(DEPOSIT_TYPEHASH, strategy, token, amount, nonce, expiry));

276     bytes32 domain_separator = keccak256(abi.encode(DOMAIN_TYPEHASH, keccak256(bytes("EigenLayer")), block.chainid, address(this)));

879     abi.encode(
```
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/core/StrategyManager.sol#l150
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/core/StrategyManager.sol#l268
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/core/StrategyManager.sol#l276
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/core/StrategyManager.sol#l879

### [Gas-12]Avoid using state variable in emit

```solidity
168     emit WithdrawalDelayBlocksSet(withdrawalDelayBlocks, newValue);
```
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/pods/DelayedWithdrawalRouter.sol#L168

### [Gas-13] Save gas with the use of the import statement
With the import statement, it saves gas to specifically import only the parts of the contracts, not the complete ones.

```solidity
import "@openzeppelin/contracts/interfaces/IERC1271.sol";
import "@openzeppelin/contracts/utils/Address.sol";
import "@openzeppelin-upgrades/contracts/proxy/utils/Initializable.sol";
import "@openzeppelin-upgrades/contracts/access/OwnableUpgradeable.sol";
import "@openzeppelin-upgrades/contracts/security/ReentrancyGuardUpgradeable.sol";
import "@openzeppelin/contracts/token/ERC20/utils/SafeERC20.sol";
import "@openzeppelin/contracts/utils/cryptography/ECDSA.sol";
```
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/core/StrategyManager.sol#L4-L10

```solidity
import "@openzeppelin/contracts/token/ERC20/IERC20.sol";
import "@openzeppelin/contracts/token/ERC20/utils/SafeERC20.sol";
import "@openzeppelin-upgrades/contracts/proxy/utils/Initializable.sol";
```
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/strategies/StrategyBase.sol#L6-L8

```solidity
import "@openzeppelin-upgrades/contracts/proxy/utils/Initializable.sol";
import "@openzeppelin-upgrades/contracts/access/OwnableUpgradeable.sol";
import "@openzeppelin-upgrades/contracts/security/ReentrancyGuardUpgradeable.sol";
```
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/pods/DelayedWithdrawalRouter.sol#L4-L6

```solidity
import "@openzeppelin-upgrades/contracts/proxy/utils/Initializable.sol";
import "@openzeppelin-upgrades/contracts/access/OwnableUpgradeable.sol";
import "@openzeppelin-upgrades/contracts/security/ReentrancyGuardUpgradeable.sol";
import "@openzeppelin-upgrades/contracts/utils/AddressUpgradeable.sol";
```
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/pods/EigenPod.sol#L4-L7

```solidity
import "@openzeppelin/contracts/utils/Create2.sol";
import "@openzeppelin/contracts/proxy/beacon/BeaconProxy.sol";
import "@openzeppelin/contracts/proxy/beacon/IBeacon.sol";

import "@openzeppelin-upgrades/contracts/proxy/utils/Initializable.sol";
import "@openzeppelin-upgrades/contracts/access/OwnableUpgradeable.sol";
```
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/pods/EigenPodManager.sol#L4-L9

```solidity
File: /src/contracts/interfaces/IServiceManager.sol
import "@openzeppelin/contracts/token/ERC20/IERC20.sol";
```
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/interfaces/IServiceManager.sol#L4

```solidity
import "@openzeppelin/contracts/token/ERC20/IERC20.sol";
```
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/interfaces/IStrategy.sol#L4


 
| num | issue | instance |
|-----|-------|----------|
|[G‑01]| internal functions only called once can be inlined to save gas | 13 |
| [G‑02] | The result of function calls should be cached rather than re-calling the function | 2|
|[G-03] |>= costs less gas than >| 3|
|[G‑04]| ++i/i++ should be unchecked{++i}/unchecked{i++} when it is not possible for them to overflow, as is the case when used in for- and while-loops|2|
|[G-05] |Expressions for constant values such as a call to keccak256(), should use immutable rather than constant|2|
|[G-06]| Using storage instead of memory for structs/arrays saves gas|6|
|[G-07] |Save gas with the use of the import statement|7|
|[G-08]|Avoid using state variable in emit|1|
|[G-09]| abi.encode() is less efficient than abi.encodePacked()|6|
|[G-10]|There is no need to initialize variables with default values|24|
| [G-11]|Use of Bit shift operators|3|
|[G-12] |Use calldata instead of memory|3|
|[G-13]| public functions to external|2|
|[G-14]|Make 3 event parameters indexed when possible|23|
|[G-15]|Use double if statements instead of &&|2|
|[G-16]|use more recent version of solidity in each contract| 24|







## [G‑01] internal functions only called once can be inlined to save gas




###  Note : these are missed from automation bots.

```solidity
130      function computePhase0BeaconBlockHeaderRoot(bytes32[NUM_BEACON_BLOCK_HEADER_FIELDS] calldata blockHeaderFields) internal pure returns(bytes32) {
```

https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/libraries/BeaconChainProofs.sol#L130

```solidity

150       function computePhase0ValidatorRoot(bytes32[NUM_VALIDATOR_FIELDS] calldata validatorFields) internal pure returns(bytes32) {

```
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/libraries/BeaconChainProofs.sol#L150

```solidity
160       function computePhase0Eth1DataRoot(bytes32[NUM_ETH1_DATA_FIELDS] calldata eth1DataFields) internal pure returns(bytes32) {

```

https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/libraries/BeaconChainProofs.sol#L160

```solidity

178       function getBalanceFromBalanceRoot(uint40 validatorIndex, bytes32 balanceRoot) internal pure returns (uint64) {

```

https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/libraries/BeaconChainProofs.sol#L178

```solidity
192       function verifyValidatorFields(
        uint40 validatorIndex,
        bytes32 beaconStateRoot,
        bytes calldata proof, 
        bytes32[] calldata validatorFields
    ) internal view {
```
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/libraries/BeaconChainProofs.sol#L192

```solidity
221       function verifyValidatorBalance(
        uint40 validatorIndex,
        bytes32 beaconStateRoot,
        bytes calldata proof,
        bytes32 balanceRoot
    ) internal view {
```
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/libraries/BeaconChainProofs.sol#L221

```solidity

245       function verifyWithdrawalProofs(
        bytes32 beaconStateRoot,
        WithdrawalProofs calldata proofs,
        bytes32[] calldata withdrawalFields
    ) internal view {
```
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/libraries/BeaconChainProofs.solL245


```solidity
5      function fromLittleEndianUint64(
        bytes32 lenum
    ) internal pure returns (uint64 n) {
```

https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/libraries/Endian.sol#L5-L7

```solidity
29        function verifyInclusionKeccak(
        bytes memory proof,
        bytes32 root,
        bytes32 leaf,
        uint256 index
    ) internal pure returns (bool) {





```

https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/libraries/Merkle.sol

```solidity

48        function processInclusionProofKeccak(bytes memory proof, bytes32 leaf, uint256 index) internal pure returns (bytes32) {
```
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/libraries/Merkle.solL#48

```solidity
80     function verifyInclusionSha256(
        bytes memory proof,
        bytes32 root,
        bytes32 leaf,
        uint256 index
    ) internal view returns (bool) {

```
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/libraries/Merkle.sol#L80

```solidity

99      function processInclusionProofSha256(bytes memory proof, bytes32 leaf, uint256 index) internal view returns (bytes32) {

```
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/libraries/Merkle.sol#L99

```solidity

129      function merkleizeSha256(
        bytes32[] memory leaves
    ) internal pure returns (bytes32) {

```
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/libraries/Merkle.sol#L129


## [G‑02] The result of function calls should be cached rather than re-calling the function
The instances below point to the second+ call of the function within a single function

```solidity

            amountToSend = _tokenBalance();
        } else {
            amountToSend = (_tokenBalance() * amountShares) / priorTotalShares;
```
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/strategies/StrategyBase.sol#L150-L152


``` solidity
121     require(blockNumber > mostRecentWithdrawalBlockNumber,
```
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/pods/EigenPod.sol#L121


## [G-03] >= costs less gas than >
The compiler uses opcodes GT and ISZERO for solidity code that uses >, but only requires LT for >=, which saves 3 gas

```solidity

824     if (amount > amountToDecrement) {
```
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/core/StrategyManager.sol#824

```solidity

    require(blockNumber > mostRecentWithdrawalBlockNumber,
            "EigenPod.proofIsForValidBlockNumber: beacon chain proof must be for block number after mostRecentWithdrawalBlockNumber");
        _;
    }

```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPod.sol#L121

``` solidity

190     if (amount > userShares) {
```
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/core/StrategyManager.sol#190


## [G‑04] ++i/i++ should be unchecked{++i}/unchecked{i++} when it is not possible for them to overflow, as is the case when used in for- and while-loops
### Note : These are missed from bots.


```solidity
101    for (uint256 i = 32; i <= proof.length; i+=32) {    
```
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/libraries/Merkle.sol#L101

```solidity
50     for (uint256 i = 32; i <= proof.length; i+=32) {
```

https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/libraries/Merkle.sol#L50


## [G-05] Expressions for constant values such as a call to keccak256(), should use immutable rather than constant

```solidity
17       bytes32 public constant DEPOSIT_TYPEHASH =
        keccak256("Deposit(address strategy,address token,uint256 amount,uint256 nonce,uint256 expiry)");
20         

```
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/core/StrategyManagerStorage.sol#L17

```solidity

 bytes32 public constant DEPOSIT_TYPEHASH =
        keccak256("Deposit(address strategy,address token,uint256 amount,uint256 nonce,uint256 expiry)");
```        
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/core/StrategyManagerStorage.sol#L20


## [G-06] Using storage instead of memory for structs/arrays saves gas

```solidity
200     IStrategy[] memory strategies = new IStrategy[](1);
```
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/core/StrategyManager.sol#L200

```solidity

62      DelayedWithdrawal memory delayedWithdrawal = DelayedWithdrawal({
```
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/pods/DelayedWithdrawalRouter.sol#L62

```solidity
114     DelayedWithdrawal[] memory claimableDelayedWithdrawals = new DelayedWithdrawal[](claimableDelayedWithdrawalsLength);
```
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/pods/DelayedWithdrawalRouter.sol#L114

```solidity
144     DelayedWithdrawal memory delayedWithdrawal = _userWithdrawals[recipient].delayedWithdrawals[delayedWithdrawalsCompletedBefore + i];
```
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/pods/DelayedWithdrawalRouter.sol#L144

## [G-07] Save gas with the use of the import statement
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

import "@openzeppelin/contracts/token/ERC20/IERC20.sol";
```
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/interfaces/IServiceManager.sol#L4

```solidity
import "@openzeppelin/contracts/token/ERC20/IERC20.sol";
```
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/interfaces/IStrategy.sol#L4

[G-08]Avoid using state variable in emit

```solidity
168     emit WithdrawalDelayBlocksSet(withdrawalDelayBlocks, newValue);
```
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/pods/DelayedWithdrawalRouter.sol#L168

## [G-09] abi.encode() is less efficient than abi.encodePacked()

```solidity

175     abi.encode(eigenPodBeacon, "")


```
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/pods/EigenPodManager.sol#L175

```solidity
202     abi.encode(eigenPodBeacon, "")

```

https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/pods/EigenPodManager.sol#L202

```solidity

150     DOMAIN_SEPARATOR = keccak256(abi.encode(DOMAIN_TYPEHASH, keccak256(bytes("EigenLayer")), ORIGINAL_CHAIN_ID, address(this)));
```
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/core/StrategyManager.sol#L150


``` solidity
276     bytes32 domain_separator = keccak256(abi.encode(DOMAIN_TYPEHASH, keccak256(bytes("EigenLayer")), block.chainid, address(this)));
```
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/core/StrategyManager.sol#L276
```solidity
879     abi.encode(   queuedWithdrawal.strategies,
                    queuedWithdrawal.shares,
                    queuedWithdrawal.depositor,
                    queuedWithdrawal.withdrawerAndNonce,
                    queuedWithdrawal.withdrawalStartBlock,
                    queuedWithdrawal.delegatedAddress
                )
```
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/core/StrategyManager.sol#L879

```solidity

268     bytes32 structHash = keccak256(abi.encode(DEPOSIT_TYPEHASH, strategy, token, amount, nonce, expiry));
```

https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/core/StrategyManager.sol#L268


## [G-10]There is no need to initialize variables with default values

```solidity

38     uint8 internal constant PAUSED_DEPOSITS = 0;
```
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/core/StrategyManager.sol#L38

```solidity
358    for (uint256 i = 0; i < strategies.length;) {
```
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/core/StrategyManager.sol#L358

```solidity
466    for(uint256 i = 0; i < queuedWithdrawals.length; i++) {
```
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/core/StrategyManager.sol#L466

```solidity
498    for (uint256 i = 0; i < strategiesLength;) {
```
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/core/StrategyManager.sol#L498

```solidity
560    for (uint256 i = 0; i < strategiesLength;) {
```
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/core/StrategyManager.sol#L560

```solidity
594    for (uint256 i = 0; i < strategiesToWhitelistLength;) {
```
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/core/StrategyManager.sol#L594

```solidity
609    for (uint256 i = 0; i < strategiesToRemoveFromWhitelistLength;) {
```
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/core/StrategyManager.sol#L609

```solidity
780    for (uint256 i = 0; i < strategiesLength;) {
```
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/core/StrategyManager.sol#L780

```solidity
797    for (uint256 i = 0; i < strategiesLength;) {
```
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/core/StrategyManager.sol#L797

```solidity
861    for (uint256 i = 0; i < strategiesLength;) {        
```
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/core/StrategyManager.sol#L861

```solidity

22     uint8 internal constant PAUSED_DEPOSITS = 0;
```
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/strategies/StrategyBase.sol#L22

```solidity

16      uint8 internal constant PAUSED_DELAYED_WITHDRAWAL_CLAIMS = 0;
```
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/pods/DelayedWithdrawalRouter.sol#L16

```solidity
115     for (uint256 i = 0; i < claimableDelayedWithdrawalsLength; i++) {
```
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/pods/DelayedWithdrawalRouter.sol#L115

```solidity
138     uint256 amountToSend = 0
```
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/pods/DelayedWithdrawalRouter.sol#L138

```solidity
141     uint256 i = 0;
```
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/pods/DelayedWithdrawalRouter.sol#L41

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

```
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/libraries/BeaconChainProofs.sol#L65

```solidity
133     for (uint256 i = 0; i < NUM_BEACON_BLOCK_HEADER_FIELDS; ++i) {
```
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/libraries/BeaconChainProofs.sol#L133

```solidity
143     for (uint256 i = 0; i < NUM_BEACON_STATE_FIELDS; ++i) {
```
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/libraries/BeaconChainProofs.sol#L143

```solidity
153     for (uint256 i = 0; i < NUM_VALIDATOR_FIELDS; ++i) {
```
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/libraries/BeaconChainProofs.sol#L153

```solidity
163     for (uint256 i = 0; i < ETH1_DATA_FIELD_TREE_HEIGHT; ++i) {            
```
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/libraries/BeaconChainProofs.sol163

```solidity

137     for (uint i = 0; i < numNodesInLayer; i++) {
```
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/libraries/Merkle.sol#L137

```solidity
145     for (uint i = 0; i < numNodesInLayer; i++) {
```
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/libraries/Merkle.sol#L145

##  [G-11]Use of Bit shift operators
Check if arithmethic operations can be achieved using bitwise operators, if yes, implement same operations using bitwise operators and compare the gas consumed. Usually bitwise logic will be cheaper (ofc we cannot use bitwise everywhere, there some limitations)


```solidity
51     if(index % 2 == 0) {
```
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/libraries/Merkle.sol#L51

```solidity
102    if(index % 2 == 0) {
```
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/libraries/Merkle.sol#L102

```solidity

179     uint256 bitShiftAmount = (validatorIndex % 4) * 64;
```
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/libraries/BeaconChainProofs.sol#L179

## [G-12] Use calldata instead of memory
Some methods are declared as external but the arguments are defined as memory instead of as calldata.

```solidity
46     function addOracleSigners(address[] memory _oracleSigners) external;
```
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/interfaces/IBeaconChainOracle.sol#L46

```solidity
53     function removeOracleSigners(address[] memory _oracleSigners) external;
```
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/interfaces/IBeaconChainOracle.sol#L53

```solidity
38          function delegateToBySignature(address staker, address operator, uint256 expiry, bytes memory signature) external;
```
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/interfaces/IDelegationManager.sol#L38


## [G-13] public functions to external
External call cost is less expensive than of public functions.
Contracts are allowed to override their parents’ functions and change the visibility from external to public.

```solidity

172            function sharesToUnderlyingView(uint256 amountShares) public view virtual override returns (uint256) {
```    
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/strategies/StrategyBase.sol#L172

```solidity
186            function sharesToUnderlying(uint256 amountShares) public view virtual override returns (uint256) {
```
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/strategies/StrategyBase.sol#L186

# [G-14]Make 3 event parameters indexed when possible
It's the most gas efficient to make up to 3 event parameters indexed. If there are less than 3 parameters, you need to make all parameters indexed.

```solidity
13     event WithdrawalDelayBlocksSet(uint256 previousValue, uint256 newValue);
```
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/pods/DelayedWithdrawalRouter.sol#L13

```solidity
33     event DelayedWithdrawalCreated(address podOwner, address recipient, uint256 amount, uint256 index);
```
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/pods/DelayedWithdrawalRouter.sol#L33


```solidity
36     event DelayedWithdrawalsClaimed(address recipient, uint256 amountClaimed, uint256 delayedWithdrawalsCompleted);
```
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/pods/DelayedWithdrawalRouter.sol#L36

```solidity
82     event EigenPodStaked(bytes pubkey);
```
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/pods/EigenPod.sol#L82

```solidity
85     event ValidatorRestaked(uint40 validatorIndex);
```
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/pods/EigenPod.sol#L85

```solidity
88     event ValidatorOvercommitted(uint40 validatorIndex);
```
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/pods/EigenPod.sol#L88

```solidity
91      event FullWithdrawalRedeemed(uint40 validatorIndex, address indexed recipient, uint64 withdrawalAmountGwei);
```
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/pods/EigenPod.sol#L91

```solidity
94      event PartialWithdrawalRedeemed(uint40 validatorIndex, address indexed recipient, uint64 partialWithdrawalAmountGwei);
```
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/pods/EigenPod.sol#L94

```solidity
97      event RestakedBeaconChainETHWithdrawn(address indexed recipient, uint256 amount);
```
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/pods/EigenPod.sol#L97

```solidity
64     event BeaconChainETHDeposited(address indexed podOwner, uint256 amount);
```
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/pods/EigenPodManager.sol#L64

```solidity
26     event Paused(address indexed account, uint256 newPausedStatus);
```
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/permissions/Pausable.sol#L26

```solidity
29     event Unpaused(address indexed account, uint256 newPausedStatus);
```
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/permissions/Pausable.sol#L29

```solidity
17     event PauserChanged(address previousPauser, address newPauser);
```
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/permissions/PauserRegistry.sol#L17

```solidity
19     event UnpauserChanged(address previousUnpauser, address newUnpauser);
```
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/permissions/PauserRegistry.sol#L19

```solidity
19     event DepositEvent(bytes pubkey, bytes withdrawal_credentials, bytes amount, bytes signature, bytes index);
```
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/interfaces/IETHPOSDeposit.sol#L19



## [G-15]Use double if statements instead of &&

```solidity
422    if (undelegateIfPossible && stakerStrategyList[msg.sender].length == 0) {
```    
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/core/StrategyManager.sol#L422

```solidity

562    if (indicesToSkipIndex < indicesToSkip.length && indicesToSkip[indicesToSkipIndex] == i) {
```
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/core/StrategyManager.sol#L562https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/core/StrategyManager.sol

## [G-16] Use recent version of solidity

### Note : in each contract.



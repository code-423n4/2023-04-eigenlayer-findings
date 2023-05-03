# Gas optimization

## Note : The First 2 Types Miss from Bots [G-01],[G-02]

# Summary

|       | Issue      |  Instances   |
|----- |------------|----------    |
|[G‑01]|++i/i++ should be unchecked{++i}/unchecked{i++} when it is not possible for them to overflow, as is the case when used in for- and while-loops | 2
|[G‑02]|internal functions only called once can be inlined to save gas | 13
|[G-03]|Make 3 event parameters indexed when possible | 23
|[G-04]|Use double if statements instead of && | 2
|[G-05]| public functions to external | 2
|[G-06]| Use calldata instead of memory | 5
|[G-07]| >= costs less gas than > | 3
|[G-08]|Use of Bit shift operators | 3
|[G-09]|There is no need to initialize variables with default values | 24
|[G-10]|Amounts should be checked for 0 before calling a transfer | 1
|[G-11]| abi.encode() is less efficient than abi.encodePacked() | 4
|[G-12]| Remove the initializer modifier | 5
|[G-13]|Avoid using state variable in emit | 1
|[G-14]| Change public state variable visibility to private | 2
|[G-15]| Save gas with the use of the import statement | 7
|[G-16]| State variables can be packed to use fewer storage slots | 2
|[G-17]| Access mappings directly rather than using accessor functions | 4
|[G-18]| Using storage instead of memory for structs/arrays saves gas | 4
|[G‑19]| The result of function calls should be cached rather than re-calling the function | 1
|[G-20]| Use constants instead of type(uintx).max | 3
|[G-21]| Expressions for constant values such as a call to keccak256(), should use immutable rather than constant | 2

## [G‑01] ++i/i++ should be unchecked{++i}/unchecked{i++} when it is not possible for them to overflow, as is the case when used in for- and while-loops

```solidity
File: /src/contracts/libraries/Merkle.sol
50     for (uint256 i = 32; i <= proof.length; i+=32) {

101    for (uint256 i = 32; i <= proof.length; i+=32) {    
```

https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/libraries/Merkle.sol

## [G‑02] internal functions only called once can be inlined to save gas

Not inlining costs 20 to 40 gas because of two extra JUMP instructions and additional stack operations needed for function calls.

```solidity
File: /src/contracts/libraries/BeaconChainProofs.sol,
130      function computePhase0BeaconBlockHeaderRoot(bytes32[NUM_BEACON_BLOCK_HEADER_FIELDS] calldata blockHeaderFields) internal pure returns(bytes32) {

150       function computePhase0ValidatorRoot(bytes32[NUM_VALIDATOR_FIELDS] calldata validatorFields) internal pure returns(bytes32) {

160       function computePhase0Eth1DataRoot(bytes32[NUM_ETH1_DATA_FIELDS] calldata eth1DataFields) internal pure returns(bytes32) {

178       function getBalanceFromBalanceRoot(uint40 validatorIndex, bytes32 balanceRoot) internal pure returns (uint64) {

192       function verifyValidatorFields(
        uint40 validatorIndex,
        bytes32 beaconStateRoot,
        bytes calldata proof, 
        bytes32[] calldata validatorFields
    ) internal view {

221       function verifyValidatorBalance(
        uint40 validatorIndex,
        bytes32 beaconStateRoot,
        bytes calldata proof,
        bytes32 balanceRoot
    ) internal view {

245       function verifyWithdrawalProofs(
        bytes32 beaconStateRoot,
        WithdrawalProofs calldata proofs,
        bytes32[] calldata withdrawalFields
    ) internal view {


```

https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/libraries/BeaconChainProofs.sol

```solidity
File: /src/contracts/libraries/Endian.sol
5      function fromLittleEndianUint64(
        bytes32 lenum
    ) internal pure returns (uint64 n) {
```

https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/libraries/Endian.sol#L5-L7

```solidity
File: /src/contracts/libraries/Merkle.sol
29        function verifyInclusionKeccak(
        bytes memory proof,
        bytes32 root,
        bytes32 leaf,
        uint256 index
    ) internal pure returns (bool) {

48        function processInclusionProofKeccak(bytes memory proof, bytes32 leaf, uint256 index) internal pure returns (bytes32) {

80     function verifyInclusionSha256(
        bytes memory proof,
        bytes32 root,
        bytes32 leaf,
        uint256 index
    ) internal view returns (bool) {

99      function processInclusionProofSha256(bytes memory proof, bytes32 leaf, uint256 index) internal view returns (bytes32) {

129      function merkleizeSha256(
        bytes32[] memory leaves
    ) internal pure returns (bytes32) {

```

https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/libraries/Merkle.sol

## [G-03]Make 3 event parameters indexed when possible

It's the most gas efficient to make up to 3 event parameters indexed. If there are less than 3 parameters, you need to make all parameters indexed.

• event UserMetadataEmitted(uint256 indexed userId, bytes32 indexed key, bytes value);

• event UserMetadataEmitted(uint256 indexed userId, bytes32 indexed key, bytes indexed value);

```solidity
File: /src/contracts/core/StrategyManager.sol
54     event Deposit(
        address depositor, IERC20 token, IStrategy strategy, uint256 shares
    );

65     event ShareWithdrawalQueued(
        address depositor, uint96 nonce, IStrategy strategy, uint256 shares
    );

77     event WithdrawalQueued(
        address depositor, uint96 nonce, address withdrawer, address delegatedAddress, bytes32 withdrawalRoot
    );

82     event WithdrawalCompleted(address indexed depositor, uint96 nonce, address indexed withdrawer, bytes32 withdrawalRoot);

85     event StrategyWhitelisterChanged(address previousAddress, address newAddress);

88     event StrategyAddedToDepositWhitelist(IStrategy strategy);

91     event StrategyRemovedFromDepositWhitelist(IStrategy strategy);

94     event WithdrawalDelayBlocksSet(uint256 previousValue, uint256 newValue);

```

https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/core/StrategyManager.sol

```solidity
File: /src/contracts/pods/DelayedWithdrawalRouter.sol
13     event WithdrawalDelayBlocksSet(uint256 previousValue, uint256 newValue);

33     event DelayedWithdrawalCreated(address podOwner, address recipient, uint256 amount, uint256 index);

36     event DelayedWithdrawalsClaimed(address recipient, uint256 amountClaimed, uint256 delayedWithdrawalsCompleted);
```

https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/pods/DelayedWithdrawalRouter.sol

```solidity
File: /src/contracts/pods/EigenPod.sol
82     event EigenPodStaked(bytes pubkey);

85     event ValidatorRestaked(uint40 validatorIndex);

88     event ValidatorOvercommitted(uint40 validatorIndex);

91      event FullWithdrawalRedeemed(uint40 validatorIndex, address indexed recipient, uint64 withdrawalAmountGwei);

94      event PartialWithdrawalRedeemed(uint40 validatorIndex, address indexed recipient, uint64 partialWithdrawalAmountGwei);

97      event RestakedBeaconChainETHWithdrawn(address indexed recipient, uint256 amount);
```

https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/pods/EigenPod.sol

```solidity
File: /src/contracts/pods/EigenPodManager.sol
64     event BeaconChainETHDeposited(address indexed podOwner, uint256 amount);
```

https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/pods/EigenPodManager.sol#L64

```solidity
File: /src/contracts/permissions/Pausable.sol
26     event Paused(address indexed account, uint256 newPausedStatus);

29     event Unpaused(address indexed account, uint256 newPausedStatus);
```

https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/permissions/Pausable.sol

```solidity
File: /src/contracts/permissions/PauserRegistry.sol
17     event PauserChanged(address previousPauser, address newPauser);

19     event UnpauserChanged(address previousUnpauser, address newUnpauser);

```

https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/permissions/PauserRegistry.sol

```solidity
File: /src/contracts/interfaces/IETHPOSDeposit.sol
19     event DepositEvent(bytes pubkey, bytes withdrawal_credentials, bytes amount, bytes signature, bytes index);
```

https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/interfaces/IETHPOSDeposit.sol#L19

## [G-04]Use double if statements instead of &&

If the if statement has a logical AND and is not followed by an else statement, it can be replaced with 2 if statements.

pragma solidity 0.8.10;

contract NestedIfTest {

    //Execution cost: 22334 gas
    function funcBad(uint256 input) public pure returns (string memory) { 
       if (input<10 && input>0 && input!=6){ 
           return "If condition passed";
       } 

   }

    //Execution cost: 22294 gas
    function funcGood(uint256 input) public pure returns (string memory) { 
    if (input<10) { 
        if (input>0){
            if (input!=6){
                return "If condition passed";
            }
        }
    }
}
}

```solidity
File: /src/contracts/core/StrategyManager.sol
422    if (undelegateIfPossible && stakerStrategyList[msg.sender].length == 0) {

562    if (indicesToSkipIndex < indicesToSkip.length && indicesToSkip[indicesToSkipIndex] == i) {
```

https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/core/StrategyManager.sol

## [G-05] public functions to external

External call cost is less expensive than of public functions.
Contracts are allowed to override their parents’ functions and change the visibility from external to public.
The following functions could be set external to save gas and improve code quality:

```solidity
File: /src/contracts/strategies/StrategyBase.sol
172            function sharesToUnderlyingView(uint256 amountShares) public view virtual override returns (uint256) {

186            function sharesToUnderlying(uint256 amountShares) public view virtual override returns (uint256) {
```

https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/strategies/StrategyBase.sol

## [G-06] Use calldata instead of memory

Some methods are declared as external but the arguments are defined as memory instead of as calldata.
By marking the function as external it is possible to use calldata in the arguments shown below and save significant gas.

```solidity
File: /src/contracts/core/StrategyManager.sol
248       function depositIntoStrategyWithSignature(
        IStrategy strategy,
        IERC20 token,
        uint256 amount,
        address staker,
        uint256 expiry,
        bytes memory signature
    )
        external
        onlyWhenNotPaused(PAUSED_DEPOSITS)
        onlyNotFrozen(staker)
        nonReentrant
        returns (uint256 shares)
    {
```

https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/core/StrategyManager.sol#L248-L261

```solidity
File: /src/contracts/interfaces/IBeaconChainOracle.sol
46     function addOracleSigners(address[] memory _oracleSigners) external;

53     function removeOracleSigners(address[] memory _oracleSigners) external;
```

https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/interfaces/IBeaconChainOracle.sol

```solidity
File: /src/contracts/interfaces/IDelegationManager.sol
38          function delegateToBySignature(address staker, address operator, uint256 expiry, bytes memory signature) external;
```

https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/interfaces/IDelegationManager.sol#L38

```solidity
File: /src/contracts/interfaces/IStrategyManager.sol
82        function depositIntoStrategyWithSignature(
        IStrategy strategy,
        IERC20 token,
        uint256 amount,
        address staker,
        uint256 expiry,
        bytes memory signature
    )
        external
        returns (uint256 shares)
```

https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/interfaces/IStrategyManager.sol#L82-L91

## [G-07] >= costs less gas than >

The compiler uses opcodes GT and ISZERO for solidity code that uses >, but only requires LT for >=, which saves 3 gas

```solidity
File: /src/contracts/core/StrategyManager.sol
190     if (amount > userShares) {

824     if (amount > amountToDecrement) {
```

https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/core/StrategyManager.sol

```solidity
File: /src/contracts/pods/EigenPod.sol

121     require(blockNumber > mostRecentWithdrawalBlockNumber,
```

https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/pods/EigenPod.sol#L121

## [G-08]Use of Bit shift operators

Check if arithmethic operations can be achieved using bitwise operators, if yes, implement same operations using bitwise operators and compare the gas consumed. Usually bitwise logic will be cheaper (ofc we cannot use bitwise everywhere, there some limitations)

Note: Bit shift << and >> operators are not among the arithmetic ones, and thus don’t revert on overflow.
exmple:

- uint256 alpha = someVar / 256;
- uint256 beta = someVar % 256;

- uint256 alpha = someVar >> 8;

- uint256 beta = someVar & 0xff;

- uint 256 alpha = delta / 2;
- uint 256 beta = delta / 4;
- uint 256 gamma = delta * 8;

- uint 256 alpha = delta >> 1;

- uint 256 beta = delta >> 2;
- uint 256 gamma = delta << 3;

```solidity
File: /src/contracts/libraries/BeaconChainProofs.sol
179     uint256 bitShiftAmount = (validatorIndex % 4) * 64;
```
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/libraries/BeaconChainProofs.sol#L179

```solidity
File: /src/contracts/libraries/Merkle.sol
51     if(index % 2 == 0) {

102    if(index % 2 == 0) {
```
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/libraries/Merkle.sol

## [G-09]There is no need to initialize variables with default values

  If the variable is not set/initialized, it is assumed to have a default value (0, false, 0x0, etc., depending on the data type). If you explicitly initialize it with its default value, you are just wasting gas.
  
  uint256 foo = 0;//bad, expensive
  uint256 bar;//good, cheap

```solidity
File: /src/contracts/core/StrategyManager.sol

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

https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/core/StrategyManager.sol

```solidity
File: /src/contracts/strategies/StrategyBase.sol
22     uint8 internal constant PAUSED_DEPOSITS = 0;
```

https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/strategies/StrategyBase.sol#L22

```solidity
File: /src/contracts/pods/DelayedWithdrawalRouter.sol
16      uint8 internal constant PAUSED_DELAYED_WITHDRAWAL_CLAIMS = 0;

115     for (uint256 i = 0; i < claimableDelayedWithdrawalsLength; i++) {

138     uint256 amountToSend = 0

141     uint256 i = 0;
```

https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/pods/DelayedWithdrawalRouter.sol

```solidity
File: /src/contracts/pods/EigenPodPausingConstants.sol
10      uint8 internal constant PAUSED_NEW_EIGENPODS = 0;
```

https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/pods/EigenPodPausingConstants.sol#L10

```solidity
File: /src/contracts/permissions/Pausable.sol
22      uint256 constant internal UNPAUSE_ALL = 0;
```

https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/permissions/Pausable.sol#L22

```solidity
File: /src/contracts/libraries/BeaconChainProofs.sol
65      uint256 internal constant SLOT_INDEX = 0;

133     for (uint256 i = 0; i < NUM_BEACON_BLOCK_HEADER_FIELDS; ++i) {

143     for (uint256 i = 0; i < NUM_BEACON_STATE_FIELDS; ++i) {

153     for (uint256 i = 0; i < NUM_VALIDATOR_FIELDS; ++i) {

163     for (uint256 i = 0; i < ETH1_DATA_FIELD_TREE_HEIGHT; ++i) {            
```

https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/libraries/BeaconChainProofs.sol

```solidity
File: /src/contracts/libraries/Merkle.sol
137     for (uint i = 0; i < numNodesInLayer; i++) {

145     for (uint i = 0; i < numNodesInLayer; i++) {
```

https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/libraries/Merkle.sol

## [G-10]Amounts should be checked for 0 before calling a transfer

Checking non-zero transfer values can avoid an expensive external call and save gas.
While this is done at some places, it's not consistently done in the solution.

```solidity
File: /src/contracts/strategies/StrategyBase.sol

155     underlyingToken.safeTransfer(depositor, amountToSend);
```

https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/strategies/StrategyBase.sol#L155

## [G-11] abi.encode() is less efficient than abi.encodePacked()

```solidity
File: /src/contracts/core/StrategyManager.sol
150     DOMAIN_SEPARATOR = keccak256(abi.encode(DOMAIN_TYPEHASH, keccak256(bytes("EigenLayer")), ORIGINAL_CHAIN_ID, address(this)));

268     bytes32 structHash = keccak256(abi.encode(DEPOSIT_TYPEHASH, strategy, token, amount, nonce, expiry));

276     bytes32 domain_separator = keccak256(abi.encode(DOMAIN_TYPEHASH, keccak256(bytes("EigenLayer")), block.chainid, address(this)));

879     abi.encode(
```

https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/core/StrategyManager.sol

```solidity
File: /src/contracts/pods/EigenPodManager.sol
175     abi.encode(eigenPodBeacon, "")

202     abi.encode(eigenPodBeacon, "")
```

https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/pods/EigenPodManager.sol

## [G-12] Remove the initializer modifier

If we can just ensure that the initialize() function could only be called from within the constructor, we shouldn’t need to worry about it getting called again.

```solidity
File: /src/contracts/core/StrategyManager.sol
146      function initialize(address initialOwner, address initialStrategyWhitelister, IPauserRegistry _pauserRegistry, uint256 initialPausedStatus, uint256 _withdrawalDelayBlocks)
        external
        initializer
    {
```

https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/core/StrategyManager.sol#L146-L149

```solidity
File: /src/contracts/strategies/StrategyBase.sol
51     function initialize(IERC20 _underlyingToken, IPauserRegistry _pauserRegistry) public virtual initializer {
```

https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/strategies/StrategyBase.sol#L51

```solidity
File: /src/contracts/pods/DelayedWithdrawalRouter.sol
49     function initialize(address initOwner, IPauserRegistry _pauserRegistry, uint256 initPausedStatus, uint256 _withdrawalDelayBlocks) external initializer {
```

https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/pods/DelayedWithdrawalRouter.sol#L49

```solidity
File: /src/contracts/pods/EigenPod.sol
152     function initialize(address _podOwner) external initializer {
```

https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/pods/EigenPod.sol#L152

```solidity
File: /src/contracts/pods/EigenPodManager.sol
84     function initialize(
        IBeaconChainOracle _beaconChainOracle,
        address initialOwner,
        IPauserRegistry _pauserRegistry,
        uint256 _initPausedStatus
    ) external initializer {
```

https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/pods/EigenPodManager.sol#L84-L89

## [G-13]Avoid using state variable in emit

```solidity
File: /src/contracts/pods/DelayedWithdrawalRouter.sol
168     emit WithdrawalDelayBlocksSet(withdrawalDelayBlocks, newValue);
```

https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/pods/DelayedWithdrawalRouter.sol#L168

## [G-14] Change public state variable visibility to private

 If it is preferred to change the visibility of the owner

```solidity
File: /src/contracts/pods/EigenPod.sol
59       address public podOwner;
```

https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/pods/EigenPod.sol#L59

```solidity
File: /src/contracts/pods/EigenPodManager.sol
55     mapping(address => IEigenPod) public ownerToPod;
```

https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/pods/EigenPodManager.sol#L55

## [G-15] Save gas with the use of the import statement

With the import statement, it saves gas to specifically import only the parts of the contracts, not the complete ones.

```solidity
File: /src/contracts/core/StrategyManager.sol
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
File: /src/contracts/strategies/StrategyBase.sol
import "@openzeppelin/contracts/token/ERC20/IERC20.sol";
import "@openzeppelin/contracts/token/ERC20/utils/SafeERC20.sol";
import "@openzeppelin-upgrades/contracts/proxy/utils/Initializable.sol";
```

https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/strategies/StrategyBase.sol#L6-L8

```solidity
File: /src/contracts/pods/DelayedWithdrawalRouter.sol
import "@openzeppelin-upgrades/contracts/proxy/utils/Initializable.sol";
import "@openzeppelin-upgrades/contracts/access/OwnableUpgradeable.sol";
import "@openzeppelin-upgrades/contracts/security/ReentrancyGuardUpgradeable.sol";
```

https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/pods/DelayedWithdrawalRouter.sol#L4-L6

```solidity
File: /src/contracts/pods/EigenPod.sol
import "@openzeppelin-upgrades/contracts/proxy/utils/Initializable.sol";
import "@openzeppelin-upgrades/contracts/access/OwnableUpgradeable.sol";
import "@openzeppelin-upgrades/contracts/security/ReentrancyGuardUpgradeable.sol";
import "@openzeppelin-upgrades/contracts/utils/AddressUpgradeable.sol";
```

https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/pods/EigenPod.sol#L4-L7

```solidity
File: /src/contracts/pods/EigenPodManager.sol
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

## [G-16] State variables can be packed to use fewer storage slots

The EVM works with 32 byte words. Variables less than 32 bytes can be declared next to eachother in storage and this will pack the values together into a single 32 byte storage slot (if the values combined are <= 32 bytes). If the variables packed together are retrieved together in functions we will effectively save ~2000 gas with every subsequent SLOAD for that storage slot. This is due to us incurring a Gwarmaccess (100 gas) versus a Gcoldsload (2100 gas).

```solidity
File: /src/contracts/core/StrategyManager.sol
    uint256 internal constant GWEI_TO_WEI = 1e9;

    // index for flag that pauses deposits when set
    uint8 internal constant PAUSED_DEPOSITS = 0;
    // index for flag that pauses withdrawals when set
    uint8 internal constant PAUSED_WITHDRAWALS = 1;

    uint256 immutable ORIGINAL_CHAIN_ID;

    // bytes4(keccak256("isValidSignature(bytes32,bytes)")
    bytes4 constant internal ERC1271_MAGICVALUE = 0x1626ba7e;

    Here use 4 slots
```

### Solve . use this code

```solidity
File: /src/contracts/core/StrategyManager.sol
    uint256 internal constant GWEI_TO_WEI = 1e9;
    uint256 immutable ORIGINAL_CHAIN_ID;

    // index for flag that pauses deposits when set
    uint8 internal constant PAUSED_DEPOSITS = 0;
    // index for flag that pauses withdrawals when set
    uint8 internal constant PAUSED_WITHDRAWALS = 1;
    // bytes4(keccak256("isValidSignature(bytes32,bytes)")
    bytes4 constant internal ERC1271_MAGICVALUE = 0x1626ba7e;

    Here use 3 slots
```

https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/core/StrategyManager.sol#L35-L45

```solidity
 File: /src/contracts/pods/EigenPod.sol
    // CONSTANTS + IMMUTABLES
    uint256 internal constant GWEI_TO_WEI = 1e9;

    /// @notice Maximum "staleness" of a Beacon Chain state root against which `verifyOvercommittedStake` may be proven. 7 days in blocks.
    uint256 internal constant VERIFY_OVERCOMMITTED_WINDOW_BLOCKS = 50400;

    /// @notice This is the beacon chain deposit contract
    IETHPOSDeposit public immutable ethPOS;

    /// @notice Contract used for withdrawal routing, to provide an extra "safety net" mechanism
    IDelayedWithdrawalRouter public immutable delayedWithdrawalRouter;

    /// @notice The single EigenPodManager for EigenLayer
    IEigenPodManager public immutable eigenPodManager;

    /// @notice The amount of eth, in gwei, that is restaked per validator
    uint64 public immutable REQUIRED_BALANCE_GWEI;

    /// @notice The amount of eth, in wei, that is restaked per ETH validator into EigenLayer
    uint256 public immutable REQUIRED_BALANCE_WEI;

    /// @notice The owner of this EigenPod
    address public podOwner;

    /**
     * @notice The latest block number at which the pod owner withdrew the balance of the pod.
     * @dev This variable is only updated when the `withdraw` function is called, which can only occur before `hasRestaked` is set to true for this pod.
     * Proofs for this pod are only valid against Beacon Chain state roots corresponding to blocks after the stored `mostRecentWithdrawalBlockNumber`.
     */
    uint64 public mostRecentWithdrawalBlockNumber;

    // STORAGE VARIABLES
    /// @notice the amount of execution layer ETH in this contract that is staked in EigenLayer (i.e. withdrawn from the Beacon Chain but not from EigenLayer), 
    uint64 public restakedExecutionLayerGwei;

    /// @notice an indicator of whether or not the podOwner has ever "fully restaked" by successfully calling `verifyCorrectWithdrawalCredentials`.
    bool public hasRestaked;
```

https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/pods/EigenPod.sol#L37-L73

## [G-17] Access mappings directly rather than using accessor functions

Saves having to do two JUMP instructions, along with stack setup
<span style="color: red;">_userWithdrawals</span> is mapping

```solidity
File: /src/contracts/pods/DelayedWithdrawalRouter.sol
66       _userWithdrawals[recipient].delayedWithdrawals.push(delayedWithdrawal);
       emit DelayedWithdrawalCreated(podOwner, recipient, withdrawalAmount,_userWithdrawals[recipient].delayedWithdrawals.length - 1);
```

https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/pods/DelayedWithdrawalRouter.sol#L66-L67
<span style="color: red;">_userWithdrawals</span> is mappin

```solidity
File: /src/contracts/pods/DelayedWithdrawalRouter.sol
111     uint256 delayedWithdrawalsCompleted = _userWithdrawals[user].delayedWithdrawalsCompleted;
        uint256 delayedWithdrawalsLength = _userWithdrawals[user].delayedWithdrawals.length;
        uint256 claimableDelayedWithdrawalsLength = delayedWithdrawalsLength - delayedWithdrawalsCompleted;
        DelayedWithdrawal[] memory claimableDelayedWithdrawals = new DelayedWithdrawal[](claimableDelayedWithdrawalsLength);
        for (uint256 i = 0; i < claimableDelayedWithdrawalsLength; i++) {
            claimableDelayedWithdrawals[i] = _userWithdrawals[user].delayedWithdrawals[delayedWithdrawalsCompleted + i];
```

https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/pods/DelayedWithdrawalRouter.sol#L111-L116
<span style="color: red;">_userWithdrawals</span> is mappin

```solidity
File: /src/contracts/pods/DelayedWithdrawalRouter.sol
133       return ((index >= _userWithdrawals[user].delayedWithdrawalsCompleted) && (block.number >= _userWithdrawals[user].delayedWithdrawals[index].blockCreated + withdrawalDelayBlocks));
```

https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/pods/DelayedWithdrawalRouter.sol#L133

<span style="color: red;">_userWithdrawals</span> is mappin

```solidity
File: /src/contracts/pods/DelayedWithdrawalRouter.sol
139     uint256 delayedWithdrawalsCompletedBefore = _userWithdrawals[recipient].delayedWithdrawalsCompleted;
        uint256 _userWithdrawalsLength = _userWithdrawals[recipient].delayedWithdrawals.length;
        uint256 i = 0;
        while (i < maxNumberOfDelayedWithdrawalsToClaim && (delayedWithdrawalsCompletedBefore + i) < _userWithdrawalsLength) {
            // copy delayedWithdrawal from storage to memory
            DelayedWithdrawal memory delayedWithdrawal = _userWithdrawals[recipient].delayedWithdrawals[delayedWithdrawalsCompletedBefore + i];
            // check if delayedWithdrawal can be claimed. break the loop as soon as a delayedWithdrawal cannot be claimed
            if (block.number < delayedWithdrawal.blockCreated + withdrawalDelayBlocks) {
                break;
            }
            // otherwise, the delayedWithdrawal can be claimed, in which case we increase the amountToSend and increment i
            amountToSend += delayedWithdrawal.amount;
            // increment i to account for the delayedWithdrawal being claimed
            unchecked {
                ++i;
            }
        }
        // mark the i delayedWithdrawals as claimed
        _userWithdrawals[recipient].delayedWithdrawalsCompleted = delayedWithdrawalsCompletedBefore + i;
```

https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/pods/DelayedWithdrawalRouter.sol#L139-L157

## [G-18] Using storage instead of memory for structs/arrays saves gas

  When fetching data from a storage location, assigning the data to a memory variable causes all fields of the struct/array to be read from storage, which incurs a Gcoldsload (2100 gas) for each field of the struct/array. If the fields are read from the new memory variable, they incur an additional MLOAD rather than a cheap stack read. Instead of declearing the variable with the memory keyword, declaring the variable with the storage keyword and caching any fields that need to be re-read in stack variables, will be much cheaper, only incuring the Gcoldsload for the fields actually read. The only time it makes sense to read the whole struct/array into a memory variable, is if the full struct/array is being returned by the function, is being passed to a function that requires memory, or if the array/struct is being read from another memory array/struct

```solidity
File: /src/contracts/core/StrategyManager.sol
200     IStrategy[] memory strategies = new IStrategy[](1);
```

https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/core/StrategyManager.sol#L200

```solidity
File: /src/contracts/pods/DelayedWithdrawalRouter.sol
62      DelayedWithdrawal memory delayedWithdrawal = DelayedWithdrawal({

114     DelayedWithdrawal[] memory claimableDelayedWithdrawals = new DelayedWithdrawal[](claimableDelayedWithdrawalsLength);

144     DelayedWithdrawal memory delayedWithdrawal = _userWithdrawals[recipient].delayedWithdrawals[delayedWithdrawalsCompletedBefore + i];
```

https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/pods/DelayedWithdrawalRouter.sol

## [G‑19] The result of function calls should be cached rather than re-calling the function

The instances below point to the second+ call of the function within a single function

```solidity
File: /src/contracts/strategies/StrategyBase.sol
            amountToSend = _tokenBalance();
        } else {
            amountToSend = (_tokenBalance() * amountShares) / priorTotalShares;
```

https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/strategies/StrategyBase.sol#L150-L152

## [G-20] Use constants instead of type(uintx).max

type(uint120).max or type(uint112).max, etc. it uses more gas in the distribution process and also for each transaction than constant usage.

```solidity
File: /src/contracts/permissions/Pausable.sol
23      uint256 constant internal PAUSE_ALL = type(uint256).max;

82     _paused = type(uint256).max;

83        emit Paused(msg.sender, type(uint256).max);
```

https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/permissions/Pausable.sol

## [G-21] Expressions for constant values such as a call to keccak256(), should use immutable rather than constant

```solidity
17       bytes32 public constant DEPOSIT_TYPEHASH =
        keccak256("Deposit(address strategy,address token,uint256 amount,uint256 nonce,uint256 expiry)");
20      bytes32 public constant DEPOSIT_TYPEHASH =
        keccak256("Deposit(address strategy,address token,uint256 amount,uint256 nonce,uint256 expiry)");        

```

https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/core/StrategyManagerStorage.sol

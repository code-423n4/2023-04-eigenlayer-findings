# GAS OPTIMIZATION

| NUMBER | PROBLEMS | CASE |
|-----|----------|----------|
|[G-01]| USE DELETE INSTEAD OF SET TO DEFAULT VALUE (FALSE OR 0). | 11 |
|[G-02]| >= COSTS LESS GAS THAN > | 3  |
|[G-03]| PUBLIC FUNCTIONS TO EXTERNAL | 2  |
|[G-04]| SAVE GAS WITH THE USE OF THE IMPORT STATEMENT | 20 |
|[G-05]| MAKE 3 EVENT PARAMETERS INDEXED WHEN POSSIBLE | 15 |
|[G-06]| USE DOUBLE IF STATEMENTS INSTEAD OF && | 2 |
|[G-07]| PUBLIC FUNCTIONS TO EXTERNAL| 2 |
|[G-08]| USE CALLDATA INSTEAD OF MEMORY| 3 |
|[G-09]| USE OF BIT SHIFT OPERATORS| 3 |
|[G-10]|THERE IS NO NEED TO INITIALIZE VARIABLES WITH DEFAULT VALUES| 24 |
|[G-11]| abi.encode() IS LESS EFFICIENT THAN abi.encodePacked()| 6 |
|[G-12]| USE MORE RECENT VERSIOIN OF SOLIDITY | all |



### [G-01]  USE DELETE INSTEAD OF SET TO DEFAULT VALUE (FALSE OR 0).
```solidity
16      uint8 internal constant PAUSED_DELAYED_WITHDRAWAL_CLAIMS = 0;  
138     uint256 amountToSend = 0;
141     uint256 i = 0;    
```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/DelayedWithdrawalRouter.sol#L16
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/DelayedWithdrawalRouter.sol#L138
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/DelayedWithdrawalRouter.sol#L141

```solidity
10     uint8 internal constant PAUSED_NEW_EIGENPODS = 0;
```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPodPausingConstants.sol#L10

```solidity
22      uint256 internal constant UNPAUSE_ALL = 0;     
```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/permissions/Pausable.sol#L22

```solidity
22      uint8 internal constant PAUSED_DEPOSITS = 0;  
```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/strategies/StrategyBase.sol#L22

```solidity
38       uint8 internal constant PAUSED_DEPOSITS = 0;
557      uint256 indicesToSkipIndex = 0;
724      uint256 j = 0;  
```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L38
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L557
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L724

```solidity
554      withdrawalRootPending[withdrawalRoot] = false;
772      withdrawalRootPending[withdrawalRoot] = false;

```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L554
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L772
### [G-02]  >= COSTS LESS GAS THAN >
```solidity
190     if (amount > userShares) 
814     if (amount > amountToDecrement) 
```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L190
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L814

```solidity
121    require(
            blockNumber > mostRecentWithdrawalBlockNumber,
```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPod.sol#L121
### [G-03]  PUBLIC FUNCTIONS TO EXTERNAL
```solidity
172     function sharesToUnderlyingView(uint256 amountShares) public view virtual override returns (uint256) 
186      function sharesToUnderlying(uint256 amountShares) public view virtual override returns (uint256)

```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/strategies/StrategyBase.sol#L172
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/strategies/StrategyBase.sol#L186
### [G-04]  SAVE GAS WITH THE USE OF THE IMPORT STATEMENT
```solidity
7         import "../interfaces/IEigenPodManager.sol";
8         import "../interfaces/IDelayedWithdrawalRouter.sol";
```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/DelayedWithdrawalRouter.sol#L7
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/DelayedWithdrawalRouter.sol#L8
```splidity

9         import "../libraries/BeaconChainProofs.sol";
10        import "../libraries/BytesLib.sol";
11        import "../libraries/Endian.sol";
13        import "../interfaces/IETHPOSDeposit.sol";
14        import "../interfaces/IEigenPodManager.sol";
15        import "../interfaces/IEigenPod.sol";
16        import "../interfaces/IDelayedWithdrawalRouter.sol";
17        import "../interfaces/IPausable.sol";

```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPod.sol#L9
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPod.sol#L10
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPod.sol#L11
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPod.sol#L13
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPod.sol#L14
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPod.sol#L15
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPod.sol#L16
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPod.sol#L17

```solidity
5        import "@openzeppelin/contracts/proxy/beacon/BeaconProxy.sol";
6        import "@openzeppelin/contracts/proxy/beacon/IBeacon.sol";\
11       import "../interfaces/IStrategyManager.sol";
12       import "../interfaces/IDelegationManager.sol";
13       import "../interfaces/IEigenPodManager.sol";
14       import "../interfaces/IETHPOSDeposit.sol";
15       import "../interfaces/IEigenPod.sol";
17       import "../interfaces/IBeaconChainOracle.sol";
```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPodManager.sol#L5
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPodManager.sol#L6
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPodManager.sol#L11
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPodManager.sol#L12
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPodManager.sol#L13
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPodManager.sol#L14
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPodManager.sol#L15
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPodManager.sol#L17

```solidity
5        import "./IEigenPodManager.sol";
6        import "./IBeaconChainOracle.sol";
```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/interfaces/IEigenPod.sol#L5
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/interfaces/IEigenPod.sol#L6
### [G-05]  MAKE 3 EVENT PARAMETERS INDEXED WHEN POSSIBLE
It's the most gas efficient to make up to 3 event parameters indexed. If there are less than 3 parameters, you need to make all parameters indexed.

```solidity
26     event Paused(address indexed account, uint256 newPausedStatus);
29     event Unpaused(address indexed account, uint256 newPausedStatus);
```
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/permissions/Pausable.sol#L26
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/permissions/Pausable.sol#L29

```solidity
17     event PauserChanged(address previousPauser, address newPauser);
19     event UnpauserChanged(address previousUnpauser, address newUnpauser);

```
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/permissions/PauserRegistry.sol#17
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/permissions/PauserRegistry.sol#19

```solidity
19     event DepositEvent(bytes pubkey, bytes withdrawal_credentials, bytes amount, bytes signature, bytes index);
```
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/interfaces/IETHPOSDeposit.sol#L19

```solidity
13     event WithdrawalDelayBlocksSet(uint256 previousValue, uint256 newValue);
33     event DelayedWithdrawalCreated(address podOwner, address recipient, uint256 amount, uint256 index);
36     event DelayedWithdrawalsClaimed(address recipient, uint256 amountClaimed, uint256 delayedWithdrawalsCompleted);
```
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/pods/DelayedWithdrawalRouter.sol#L13
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/pods/DelayedWithdrawalRouter.sol#L33
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/pods/DelayedWithdrawalRouter.sol#L36

```solidity
82     event EigenPodStaked(bytes pubkey);
85     event ValidatorRestaked(uint40 validatorIndex);
88     event ValidatorOvercommitted(uint40 validatorIndex);
91     event FullWithdrawalRedeemed(uint40 validatorIndex, address indexed recipient, uint64 withdrawalAmountGwei);
94     event PartialWithdrawalRedeemed(uint40 validatorIndex, address indexed recipient, uint64 partialWithdrawalAmountGwei);
97     event RestakedBeaconChainETHWithdrawn(address indexed recipient, uint256 amount);
```
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/pods/EigenPod.sol#L82
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/pods/EigenPod.sol#L85
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/pods/EigenPod.sol#L88
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/pods/EigenPod.sol#L91
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/pods/EigenPod.sol#L94
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/pods/EigenPod.sol#L97

```solidity

64     event BeaconChainETHDeposited(address indexed podOwner, uint256 amount);
```
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/pods/EigenPodManager.sol#L64
### [G-06]  USE DOUBLE IF STATEMENTS INSTEAD OF &&
```solidity
422    if (undelegateIfPossible && stakerStrategyList[msg.sender].length == 0) {
562    if (indicesToSkipIndex < indicesToSkip.length && indicesToSkip[indicesToSkipIndex] == i) {
```
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/core/StrategyManager.sol#L422
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/core/StrategyManager.sol#L562
### [G-07]  PUBLIC FUNCTIONS TO EXTERNAL
External call cost is less expensive than of public functions.
Contracts are allowed to override their parents’ functions and change the visibility from external to public.

```solidity
172            function sharesToUnderlyingView(uint256 amountShares) public view virtual override returns (uint256) {
186            function sharesToUnderlying(uint256 amountShares) public view virtual override returns (uint256) {
```
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/strategies/StrategyBase.sol#L172
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/strategies/StrategyBase.sol#186
### [G-08]  USE CALLDATA INSTEAD OF MEMORY
Some methods are declared as external but the arguments are defined as memory instead of as calldata.

```solidity
46     function addOracleSigners(address[] memory _oracleSigners) external;
53     function removeOracleSigners(address[] memory _oracleSigners) external;
```
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/interfaces/IBeaconChainOracle.sol#L46
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/interfaces/IBeaconChainOracle.sol#L53

```solidity
38          function delegateToBySignature(address staker, address operator, uint256 expiry, bytes memory signature) external;
```
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/interfaces/IDelegationManager.sol#L38
### [G-09]  USE OF BIT SHIFT OPERATORS

Check if arithmethic operations can be achieved using bitwise operators, if yes, implement same operations using bitwise operators and compare the gas consumed. Usually bitwise logic will be cheaper (ofc we cannot use bitwise everywhere, there some limitations)


```solidity
51     if(index % 2 == 0) {

102    if(index % 2 == 0) {
```
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/libraries/Merkle.sol#L51
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/libraries/Merkle.sol#L102

```solidity
179     uint256 bitShiftAmount = (validatorIndex % 4) * 64;
```
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/libraries/BeaconChainProofs.sol#L179
### [G-10]  THERE IS NO NEED TO INITIALIZE VARIABLES WITH DEFAULT VALUES
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
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/core/StrategyManager.sol#L38
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/core/StrategyManager.sol#L358
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/core/StrategyManager.sol#L466
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/core/StrategyManager.sol#L498
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/core/StrategyManager.sol#L560
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/core/StrategyManager.sol#L594
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/core/StrategyManager.sol#L609
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/core/StrategyManager.sol#L780
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/core/StrategyManager.sol#L797
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/core/StrategyManager.sol#L861

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
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/pods/DelayedWithdrawalRouter.sol#L16
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/pods/DelayedWithdrawalRouter.sol#L115
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/pods/DelayedWithdrawalRouter.sol#L138
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/pods/DelayedWithdrawalRouter.sol#L141

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
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/libraries/BeaconChainProofs.sol#65
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/libraries/BeaconChainProofs.sol#133
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/libraries/BeaconChainProofs.sol#143
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/libraries/BeaconChainProofs.sol#153
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/libraries/BeaconChainProofs.sol#163

```solidity
137     for (uint i = 0; i < numNodesInLayer; i++) {
145     for (uint i = 0; i < numNodesInLayer; i++) {
```
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/libraries/Merkle.sol#L137
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/libraries/Merkle.sol#L145
### [G-11]  abi.encode() IS LESS EFFICIENT THAN abi.encodePacked()
```solidity
150     DOMAIN_SEPARATOR = keccak256(abi.encode(DOMAIN_TYPEHASH, keccak256(bytes("EigenLayer")), ORIGINAL_CHAIN_ID, address(this)));
268     bytes32 structHash = keccak256(abi.encode(DEPOSIT_TYPEHASH, strategy, token, amount, nonce, expiry));
276     bytes32 domain_separator = keccak256(abi.encode(DOMAIN_TYPEHASH, keccak256(bytes("EigenLayer")), block.chainid, address(this)));
879     abi.encode(
```
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/core/StrategyManager.sol#L150
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/core/StrategyManager.sol#L268
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/core/StrategyManager.sol#L276
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/core/StrategyManager.sol#L879

```solidity

175     abi.encode(eigenPodBeacon, "")
202     abi.encode(eigenPodBeacon, "")
```
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/pods/EigenPodManager.sol#L175
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/pods/EigenPodManager.sol#L202

### [G-12]USE MORE RECENT VERSIOIN OF SOLIDITY

Note: This is Issue happen in every solidity file



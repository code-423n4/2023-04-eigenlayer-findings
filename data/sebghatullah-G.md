## Summary 

### Gas Optimization

|no |Issue|Instances|
|----|-------|------|
| [G-01] |  Use hardcode address instead address(this) | 6 | - |  
| [G-02] |  Using storage instead of memory for structs/arrays saves gas | 12 | - |  
| [G-03] |  State variables can be packed to use fewer storage slots | 5 | - |  
| [G-04] |  Save gas with the use of the import statement| 20 | - | 
| [G-05] |  Change public state variable visibility to private| 19 | - |  
| [G-06] |  public functions to external| 2 | - |  
| [G-07] |  Make 3 event parameters indexed when possible| 19 | - |  
| [G-08] |  Use double if statements instead of &&| 2 | - |  
| [G-09] |  >= costs less gas than >| 3 | - |  
| [G-10] |  There is no need to initialize variables with default values| 38 | - |  
| [G-11] |  Use delete instead of set to default value (false or 0).| 11 | - |  
| [G-12] |  Use calldata instead of memory| 3 | - |  
| [G-13] |  Use of Bit shift operators| 3 | - | 
| [G-14] |  abi.encode() is less efficient than abi.encodePacked()| 6 | - | 
| [G-15] |  Avoid using state variable in emit| 1 | - | 
| [G-16] |  Expressions for constant values such as a call to keccak256(), should use immutable rather than constant | 2 | - |
| [G-17] |  ++i/i++ should be unchecked{++i}/unchecked{i++} when it is not possible for them to overflow, as is the case when used in for- and while-loops| 2 | - |  
| [G-18] |  internal functions only called once can be inlined to save gas| 14 | - | 
| [G-19] |  Using a positive conditional flow to save a NOT opcode | 2 | - | 
| [G-20] |  Cache array length outside of loop | 5 | - | 
| [G-21] |  Do not calculate constants | 6 | - | 
| [G-22] |  Multiple accesses of a mapping/array should use a local variable cache | 29 | - |
| [G-23] |  Remove the initializer modifier | 5 | - |
| [G-24] |  Use constants instead of type(uintx).max| 3 | - |
| [G-25] |  USE ASSEMBLY TO CHECK FOR ADDRESS(0) | 11 | - |
| [G-26] |  Use function instead of modifiers | 13 | - |




### [G-01]  Use hardcode address instead address(this)
```solidity
file:    EigenPod.sol
461      return abi.encodePacked(bytes1(uint8(1)), bytes11(0), address(this));
456      _sendETH(podOwner, address(this).balance);
```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPod.sol#L461
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPod.sol#L456

```solidity
file:
236      return strategyManager.stakerStrategyShares(user, IStrategy(address(this)));
242       return underlyingToken.balanceOf(address(this));
```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/strategies/StrategyBase.sol#L236
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/strategies/StrategyBase.sol#L242

```solidity
file:
150     DOMAIN_SEPARATOR = keccak256(abi.encode(DOMAIN_TYPEHASH, keccak256(bytes("EigenLayer")), ORIGINAL_CHAIN_ID, address(this)));
276     bytes32 domain_separator = keccak256(abi.encode(DOMAIN_TYPEHASH, keccak256(bytes("EigenLayer")), block.chainid, address(this)));
```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L150
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L276


### [G-02]  Using storage instead of memory for structs/arrays saves gas
```solidity
file:     BeaconChainProofs.sol
131       bytes32[] memory paddedHeaderFields = new bytes32[]
141       bytes32[] memory paddedBeaconStateFields = new bytes32[]
151        bytes32[] memory paddedValidatorFields = new bytes32[]
161        bytes32[] memory paddedEth1DataFields = new bytes32[]
```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/libraries/BeaconChainProofs.sol#L131
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/libraries/BeaconChainProofs.sol#L141
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/libraries/BeaconChainProofs.sol#L151
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/libraries/BeaconChainProofs.sol#L161
```solidity
file:  IBeaconChainOracle.sol 
46     function addOracleSigners(address[] memory _oracleSigners) external
53      function removeOracleSigners(address[] memory _oracleSigners) external;
```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/interfaces/IBeaconChainOracle.sol#L46
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/interfaces/IBeaconChainOracle.sol#L53

```solidity
file:     Merkle.sol
100       bytes32[1] memory computedHash = [leaf];
130       bytes32[] memory leaves
135       bytes32[] memory layer = new bytes32[](numNodesInLayer);
```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/libraries/Merkle.sol#L100
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/libraries/Merkle.sol#L130
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/libraries/Merkle.sol#L135
```solidity
file:    StrategyManager.sol
200      IStrategy[] memory strategies = new IStrategy[](1);
202      uint256[] memory shareAmounts = new uint256[](1);
859      uint256[] memory shares = new uint256[](strategiesLength);

```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L200
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L202
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L859



### [G-03]   State variables can be packed to use fewer storage slots

```solidity
file:   EigenPod.sol
38      uint256 internal constant GWEI_TO_WEI = 1e9;

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
```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPod.sol#L38-L59

```solidity
file:     EigenPodPausingConstants.sol
10        uint8 internal constant PAUSED_NEW_EIGENPODS = 0;
    /// @notice Index for flag that pauses the `withdrawRestakedBeaconChainETH` function *of the EigenPodManager* when set. See EigenPodManager code for details.
    uint8 internal constant PAUSED_WITHDRAW_RESTAKED_ETH = 1;

    /// @notice Index for flag that pauses the `verifyCorrectWithdrawalCredentials` function *of the EigenPods* when set. see EigenPod code for details.
    uint8 internal constant PAUSED_EIGENPODS_VERIFY_CREDENTIALS = 2;
    /// @notice Index for flag that pauses the `verifyOvercommittedStake` function *of the EigenPods* when set. see EigenPod code for details.
    uint8 internal constant PAUSED_EIGENPODS_VERIFY_OVERCOMMITTED = 3;
    /// @notice Index for flag that pauses the `verifyBeaconChainFullWithdrawal` function *of the EigenPods* when set. see EigenPod code for details.
    uint8 internal constant PAUSED_EIGENPODS_VERIFY_WITHDRAWAL = 4;

```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPodPausingConstants.sol#L10-L19
```solidity
file:     PauserRegistry.sol
12          address public pauser;

    /// @notice Unique address that holds the unpauser role. Capable of changing *both* the pauser and unpauser addresses.
    address public unpauser;

```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/permissions/PauserRegistry.sol#L12-L15
```solidity
file:     StrategyBase.sol
22        uint8 internal constant PAUSED_DEPOSITS = 0;
    uint8 internal constant PAUSED_WITHDRAWALS = 1;
    /*
     * as long as at least *some* shares exist, this is the minimum number.
     * i.e. `totalShares` must exist in the set {0, [MIN_NONZERO_TOTAL_SHARES, type(uint256).max]}
     */
    uint96 internal constant MIN_NONZERO_TOTAL_SHARES = 1e9;
```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/strategies/StrategyBase.sol#L22-L28
```solidity
file:    StrategyManager.sol
35       uint256 internal constant GWEI_TO_WEI = 1e9;

    // index for flag that pauses deposits when set
    uint8 internal constant PAUSED_DEPOSITS = 0;
    // index for flag that pauses withdrawals when set
    uint8 internal constant PAUSED_WITHDRAWALS = 1;

    uint256 immutable ORIGINAL_CHAIN_ID;

    // bytes4(keccak256("isValidSignature(bytes32,bytes)")
    bytes4 internal constant ERC1271_MAGICVALUE = 0x1626ba7e;

```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L35-L45

### [G-04]  Save gas with the use of the import statement
```solidity
file:     DelayedWithdrawalRouter.sol
7         import "../interfaces/IEigenPodManager.sol";
8         import "../interfaces/IDelayedWithdrawalRouter.sol";
```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/DelayedWithdrawalRouter.sol#L7
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/DelayedWithdrawalRouter.sol#L8
```splidity
file:     EigenPod.sol
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
file:    EigenPodManager.sol
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
file:
5        import "./IEigenPodManager.sol";
6        import "./IBeaconChainOracle.sol";
```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/interfaces/IEigenPod.sol#L5
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/interfaces/IEigenPod.sol#L6


### [G-05]  Change public state variable visibility to private

```solidity
file:    DelayedWithdrawalRouter.sol
22       uint256 public withdrawalDelayBlocks;
24       uint256 public constant MAX_WITHDRAWAL_DELAY_BLOCKS = 50400;
27       IEigenPodManager public immutable eigenPodManager;
```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/DelayedWithdrawalRouter.sol#L22
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/DelayedWithdrawalRouter.sol#L24
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/DelayedWithdrawalRouter.sol#L27

```solidity
file:   EigenPod.sol
50      IEigenPodManager public immutable eigenPodManager;
53      uint64 public immutable REQUIRED_BALANCE_GWEI;
56      uint256 public immutable REQUIRED_BALANCE_WEI;
59      address public podOwner;
66      uint64 public mostRecentWithdrawalBlockNumber;
70      uint64 public restakedExecutionLayerGwei;
73      bool public hasRestaked;
```

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPod.sol#L50
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPod.sol#L53
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPod.sol#L56
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPod.sol#L59
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPod.sol#L66
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPod.sol#L70
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPod.sol#L73

```solidity
file:    PauserRegistry.sol
12       address public pauser;
15       address public unpauser;

```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/permissions/PauserRegistry.sol#L12
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/permissions/PauserRegistry.sol#L15

```solidity
file:   StrategyBase.sol
37      uint256 public totalShares;
```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/strategies/StrategyBase.sol#L37

```solidity
file:
17      bytes32 public constant DOMAIN_TYPEHASH
20      bytes32 public constant DEPOSIT_TYPEHASH 
23      bytes32 public DOMAIN_SEPARATOR;
36      address public strategyWhitelister;
44      uint256 public withdrawalDelayBlocks;
46      uint256 public constant MAX_WITHDRAWAL_DELAY_BLOCKS
```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManagerStorage.sol#L17
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManagerStorage.sol#L20
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManagerStorage.sol#L23
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManagerStorage.sol#L36
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManagerStorage.sol#L44
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManagerStorage.sol#L46

### [G-06]  public functions to external
```solidity
file:
172     function sharesToUnderlyingView(uint256 amountShares) public view virtual override returns (uint256) 
186      function sharesToUnderlying(uint256 amountShares) public view virtual override returns (uint256)

```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/strategies/StrategyBase.sol#L172
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/strategies/StrategyBase.sol#L186

### [G-07] Make 3 event parameters indexed when possible

```solidity
file:   DelayedWithdrawalRouter.sol
13      event WithdrawalDelayBlocksSet(uint256 previousValue, uint256 newValue);
36      event DelayedWithdrawalsClaimed(
        address recipient,
        uint256 amountClaimed,
        uint256 delayedWithdrawalsCompleted
    );
```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/DelayedWithdrawalRouter.sol#L13
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/DelayedWithdrawalRouter.sol#L36

```solidity
file:   EigenPod.sol
82      event EigenPodStaked(bytes pubkey);
85      event ValidatorRestaked(uint40 validatorIndex);
88      event ValidatorOvercommitted(uint40 validatorIndex);
91      event FullWithdrawalRedeemed(uint40 validatorIndex, address indexed recipient, uint64 withdrawalAmountGwei);
94      event PartialWithdrawalRedeemed(uint40 validatorIndex, address indexed recipient, uint64 partialWithdrawalAmountGwei);
97      event RestakedBeaconChainETHWithdrawn(address indexed recipient, uint256 amount);
```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPod.sol#L82
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPod.sol#L85
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPod.sol#L88
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPod.sol#L91
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPod.sol#L94
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPod.sol#L97

```solidity
file:    EigenPodManager.sol
58       event BeaconOracleUpdated(address indexed newOracleAddress);
61       event PodDeployed(address indexed eigenPod, address indexed podOwner);
64       event BeaconChainETHDeposited(address indexed podOwner, uint256 amount);
```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPodManager.sol#L58
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPodManager.sol#L61
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPodManager.sol#L64

```solidity
file:  Pausable.sol
26     event Paused(address indexed account, uint256 newPausedStatus);
29     event Unpaused(address indexed account, uint256 newPausedStatus);
```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/permissions/Pausable.sol#L26
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/permissions/Pausable.sol#L29
```solidity
file:   PauserRegistry.sol
17      event PauserChanged(address previousPauser, address newPauser);
19      event UnpauserChanged(address previousUnpauser, address newUnpauser);
```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/permissions/PauserRegistry.sol#L17
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/permissions/PauserRegistry.sol#L19
```solidity
file:  StrategyManager.sol
85     event StrategyWhitelisterChanged(address previousAddress, address newAddress);
88     event StrategyAddedToDepositWhitelist(IStrategy strategy);
91     event StrategyRemovedFromDepositWhitelist(IStrategy strategy);
94     event WithdrawalDelayBlocksSet(uint256 previousValue, uint256 newValue);
```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L85
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L88
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L91
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L94

### [G-08] Use double if statements instead of &&
```solidity
file:     StrategyManager.sol
524        if (
            undelegateIfPossible && stakerStrategyList[msg.sender].length == 0)
526         if (
                indicesToSkipIndex < indicesToSkip.length &&
                indicesToSkip[indicesToSkipIndex] == i)             
```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L422
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L562

### [G-09]  >= costs less gas than >

```solidity
file:   StrategyManager.sol
190     if (amount > userShares) 
814     if (amount > amountToDecrement) 
```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L824
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L190

```solidity
file:  EigenPod.sol
121    require(
            blockNumber > mostRecentWithdrawalBlockNumber,
```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPod.sol#L121

### [G-10] There is no need to initialize variables with default values

```solidity
file:  BeaconChainProofs.sol
62     uint256 internal constant SLOT_INDEX = 0;
133    for (uint256 i = 0; i < NUM_BEACON_BLOCK_HEADER_FIELDS; ++i) 
143    for (uint256 i = 0; i < NUM_BEACON_STATE_FIELDS; ++i)
153    for (uint256 i = 0; i < NUM_VALIDATOR_FIELDS; ++i)
163    for (uint256 i = 0; i < ETH1_DATA_FIELD_TREE_HEIGHT; ++i) 
```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/libraries/BeaconChainProofs.sol#L65
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/libraries/BeaconChainProofs.sol#L133
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/libraries/BeaconChainProofs.sol#L143
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/libraries/BeaconChainProofs.sol#L153
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/libraries/BeaconChainProofs.sol#L163

```solidity
file:  DelayedWithdrawalRouter.sol 
16     uint8 internal constant PAUSED_DELAYED_WITHDRAWAL_CLAIMS = 0;
61     if (withdrawalAmount != 0) 
115    for (uint256 i = 0; i < claimableDelayedWithdrawalsLength; i++)
138    uint256 amountToSend = 0;
141    uint256 i = 0;
159    if (amountToSend != 0)

```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/DelayedWithdrawalRouter.sol#L16
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/DelayedWithdrawalRouter.sol#L61
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/DelayedWithdrawalRouter.sol#L115
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/DelayedWithdrawalRouter.sol#L138
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/DelayedWithdrawalRouter.sol#L141
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/DelayedWithdrawalRouter.sol#L159

```solidity
file:  EigenPod.sol
417    if (amountToSend != 0)
```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPod.sol#L417

```solidity
file:   EigenPodPausingConstants.sol 
10      uint8 internal constant PAUSED_NEW_EIGENPODS = 0;
```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPodPausingConstants.sol#L10

```solidity
file:   Merkle.sol
51      if (index % 2 == 0)
102     if(index % 2 == 0)
137     for (uint i = 0; i < numNodesInLayer; i++) 
143     while (numNodesInLayer != 0)
145     for (uint i = 0; i < numNodesInLayer; i++)
```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/libraries/Merkle.sol#L51
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/libraries/Merkle.sol#L102
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/libraries/Merkle.sol#L137
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/libraries/Merkle.sol#L143
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/libraries/Merkle.sol#L145

```solidity
file:  Pausable.sol
22     uint256 internal constant UNPAUSE_ALL = 0;  
```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/permissions/Pausable.sol#L22


```solidity
file:   StrategyBase.sol
22      uint8 internal constant PAUSED_DEPOSITS = 0;  
120     newShares != 0, 
```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/strategies/StrategyBase.sol#L22
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/strategies/StrategyBase.sol#L104

```solidity
file:   StrategyManager.sol
38      uint8 internal constant PAUSED_DEPOSITS = 0;
196     if (amount != 0)
358     for (uint256 i = 0; i < strategies.length; )
498     for (uint256 i = 0; i < queuedWithdrawals.length; i++)
622     for (uint256 i = 0; i < strategiesLength; )
557     uint256 indicesToSkipIndex = 0;
560     for (uint256 i = 0; i < strategiesLength; )
594     for (uint256 i = 0; i < strategiesToWhitelistLength; )
609     for (uint256 i = 0; i < strategiesToRemoveFromWhitelistLength; )
632     require(shares != 0, "StrategyManager._addShares: shares should not be zero!");
685     require(shareAmount != 0, "StrategyManager._removeShares: shareAmount should not be zero!");
724     uint256 j = 0;
780    for (uint256 i = 0; i < strategiesLength; ) 
797    for (uint256 i = 0; i < strategiesLength; )
823    if (amountToDecrement != 0)
825    beaconChainETHSharesToDecrementOnWithdrawal[staker] = 0;
861    for (uint256 i = 0; i < strategiesLength; )
```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L38
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L196
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L358
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L466
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L498
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L557
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L560
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L594
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L609
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L632
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L685
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L724
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L780
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L797
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L823
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L825
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L861



### [G-11]  Use delete instead of set to default value (false or 0).
```solidity
file:   DelayedWithdrawalRouter.sol
16      uint8 internal constant PAUSED_DELAYED_WITHDRAWAL_CLAIMS = 0;  
138     uint256 amountToSend = 0;
141     uint256 i = 0;    
```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/DelayedWithdrawalRouter.sol#L16
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/DelayedWithdrawalRouter.sol#L138
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/DelayedWithdrawalRouter.sol#L141

```solidity
file:  EigenPodPausingConstants.sol
10     uint8 internal constant PAUSED_NEW_EIGENPODS = 0;
```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPodPausingConstants.sol#L10

```solidity
file:   Pausable.sol
22      uint256 internal constant UNPAUSE_ALL = 0;     
```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/permissions/Pausable.sol#L22

```solidity
file:   StrategyBase.sol
22      uint8 internal constant PAUSED_DEPOSITS = 0;  
```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/strategies/StrategyBase.sol#L22

```solidity
file:    StrategyManager.sol
38       uint8 internal constant PAUSED_DEPOSITS = 0;
557      uint256 indicesToSkipIndex = 0;
724      uint256 j = 0;  
```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L38
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L557
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L724

```solidity
file:    StrategyManager.sol
554      withdrawalRootPending[withdrawalRoot] = false;
772      withdrawalRootPending[withdrawalRoot] = false;

```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L554
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L772

### [G-12] Use calldata instead of memory
Some methods are declared as external but the arguments are defined as memory instead of as calldata.

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


###  [G-13] Use of Bit shift operators
Check if arithmethic operations can be achieved using bitwise operators, if yes, implement same operations using bitwise operators and compare the gas consumed. Usually bitwise logic will be cheaper (ofc we cannot use bitwise everywhere, there some limitations)

```solidity
File:  Merkle.sol
51     if(index % 2 == 0) {

102    if(index % 2 == 0) {
```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/libraries/Merkle.sol#L51
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/libraries/Merkle.sol#L102

```solidity
File:   BeaconChainProofs.sol
179     uint256 bitShiftAmount = (validatorIndex % 4) * 64;
```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/libraries/BeaconChainProofs.sol#L179

###  [G-14] abi.encode() is less efficient than abi.encodePacked()

```solidity
File:   EigenPodManager.sol
175     abi.encode(eigenPodBeacon, "")

202     abi.encode(eigenPodBeacon, "")
```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPodManager.sol#L175
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPodManager.sol#L202

```solidity
File:   StrategyManager.sol
150     DOMAIN_SEPARATOR = keccak256(abi.encode(DOMAIN_TYPEHASH, keccak256(bytes("EigenLayer")), ORIGINAL_CHAIN_ID, address(this)));

268     bytes32 structHash = keccak256(abi.encode(DEPOSIT_TYPEHASH, strategy, token, amount, nonce, expiry));

276     bytes32 domain_separator = keccak256(abi.encode(DOMAIN_TYPEHASH, keccak256(bytes("EigenLayer")), block.chainid, address(this)));

879     abi.encode(
```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L150
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L268
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L276
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L879

### [G-15] Avoid using state variable in emit

```solidity
file:   DelayedWithdrawalRouter
168     emit WithdrawalDelayBlocksSet(withdrawalDelayBlocks, newValue);
```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/DelayedWithdrawalRouter.sol#L168

### [G-16] Expressions for constant values such as a call to keccak256(), should use immutable rather than constant

```solidity
file:   StrategyManagerStorage.sol
17      bytes32 public constant DEPOSIT_TYPEHASH =
        keccak256("Deposit(address strategy,address token,uint256 amount,uint256 nonce,uint256 expiry)");
20      bytes32 public constant DEPOSIT_TYPEHASH =
        keccak256("Deposit(address strategy,address token,uint256 amount,uint256 nonce,uint256 expiry)");        

```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManagerStorage.sol#L17-L18
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManagerStorage.sol#L20-L21

## [G‑17] ++i/i++ should be unchecked{++i}/unchecked{i++} when it is not possible for them to overflow, as is the case when used in for- and while-loops
# Note Miss bots
```solidity
File:  Merkle.sol
50     for (uint256 i = 32; i <= proof.length; i+=32) {
101    for (uint256 i = 32; i <= proof.length; i+=32) {    
```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/libraries/Merkle.sol#L50
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/libraries/Merkle.sol#L101

###  [G-18] internal functions only called once can be inlined to save gas
# NOTE Miss bots 
```solidity
file:    BeaconChainProofs.sol
130      function computePhase0BeaconBlockHeaderRoot(bytes32[NUM_BEACON_BLOCK_HEADER_FIELDS] calldata blockHeaderFields) internal pure returns(bytes32)
140      function computePhase0BeaconStateRoot(bytes32[NUM_BEACON_STATE_FIELDS] calldata beaconStateFields) internal pure returns(bytes32)
150      function computePhase0ValidatorRoot(bytes32[NUM_VALIDATOR_FIELDS] calldata validatorFields) internal pure returns(bytes32) 
160      function computePhase0Eth1DataRoot(bytes32[NUM_ETH1_DATA_FIELDS] calldata eth1DataFields) internal pure returns(bytes32) 
178       function getBalanceFromBalanceRoot(uint40 validatorIndex, bytes32 balanceRoot) internal pure returns (uint64)
221       function verifyValidatorBalance(
        uint40 validatorIndex,
        bytes32 beaconStateRoot,
        bytes calldata proof,
        bytes32 balanceRoot
    ) internal view
245     function verifyWithdrawalProofs(
        bytes32 beaconStateRoot,
        WithdrawalProofs calldata proofs,
        bytes32[] calldata withdrawalFields
    ) internal view

```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/libraries/BeaconChainProofs.sol#L130
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/libraries/BeaconChainProofs.sol#L140
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/libraries/BeaconChainProofs.sol#L150
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/libraries/BeaconChainProofs.sol#L160
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/libraries/BeaconChainProofs.sol#L178
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/libraries/BeaconChainProofs.sol#L221-L226
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/libraries/BeaconChainProofs.sol#L245-L249

```solidity
file:    Endian.sol
5         function fromLittleEndianUint64(
        bytes32 lenum
    ) internal pure returns (uint64 n)
```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/libraries/Endian.sol#L5-L7


```solidity
file:   Merkle.sol
29      function verifyInclusionKeccak(
        bytes memory proof,
        bytes32 root,
        bytes32 leaf,
        uint256 index
    ) internal pure returns (bool)  
48      function processInclusionProofKeccak(bytes memory proof, bytes32 leaf, uint256 index) internal pure returns (bytes32) 
80      function verifyInclusionSha256(
        bytes memory proof,
        bytes32 root,
        bytes32 leaf,
        uint256 index
    ) internal view returns (bool) 
99     function processInclusionProofSha256(bytes memory proof, bytes32 leaf, uint256 index) internal view returns (bytes32) 
129         function merkleizeSha256(
        bytes32[] memory leaves
    ) internal pure returns (bytes32)
```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/libraries/Merkle.sol#L29-L34
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/libraries/Merkle.sol#L48
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/libraries/Merkle.sol#L80-L85
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/libraries/Merkle.sol#L99
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/libraries/Merkle.sol#L129-L131

```solidity
file: Pausable.sol
55    function _initializePauser(IPauserRegistry _pauserRegistry, uint256 initPausedStatus) internal  
```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/permissions/Pausable.sol#L55

### [G-19] Using a positive conditional flow to save a NOT opcode 

```solidity
file:  EigenPod.sol 
218    if (!hasRestaked) {
            hasRestaked = true;
        }
```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPod.sol#L218
```solidity
file:
596     if (!strategyIsWhitelistedForDeposit[strategiesToWhitelist[i]]) 
```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L596

### [G-20]   Cache array length outside of loop
```solidity
file:   BeaconChainProofs.sol
196     bytes32[] calldata validatorFields
248     bytes32[] calldata withdrawalFields

```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/libraries/BeaconChainProofs.sol#L196
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/libraries/BeaconChainProofs.sol#L248
```solidity
file:   StrategyManager.sol
331     IStrategy[] calldata strategies,
332     uint256[] calldata shares,
607     function removeStrategiesFromDepositWhitelist(IStrategy[] calldata strategiesToRemoveFromWhitelist) external onlyStrategyWhitelister 

```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L332
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L607

### [G-21] 	Do not calculate constants
```solidity
file:   BeaconChainProofs.sol
14    uint256 internal constant NUM_BEACON_BLOCK_HEADER_FIELDS = 5;
    uint256 internal constant BEACON_BLOCK_HEADER_FIELD_TREE_HEIGHT = 3;

    uint256 internal constant NUM_BEACON_BLOCK_BODY_FIELDS = 11;
    uint256 internal constant BEACON_BLOCK_BODY_FIELD_TREE_HEIGHT = 4;

    uint256 internal constant NUM_BEACON_STATE_FIELDS = 21;
    uint256 internal constant BEACON_STATE_FIELD_TREE_HEIGHT = 5;

    uint256 internal constant NUM_ETH1_DATA_FIELDS = 3;
    uint256 internal constant ETH1_DATA_FIELD_TREE_HEIGHT = 2;

    uint256 internal constant NUM_VALIDATOR_FIELDS = 8;
    uint256 internal constant VALIDATOR_FIELD_TREE_HEIGHT = 3;

    uint256 internal constant NUM_EXECUTION_PAYLOAD_HEADER_FIELDS = 15;
    uint256 internal constant EXECUTION_PAYLOAD_HEADER_FIELD_TREE_HEIGHT = 4;


    uint256 internal constant NUM_EXECUTION_PAYLOAD_FIELDS = 15;
    uint256 internal constant EXECUTION_PAYLOAD_FIELD_TREE_HEIGHT = 4;


    // HISTORICAL_ROOTS_LIMIT	 = 2**24, so tree height is 24
    uint256 internal constant HISTORICAL_ROOTS_TREE_HEIGHT = 24;

    // HISTORICAL_BATCH is root of state_roots and block_root, so number of leaves =  2^1
    uint256 internal constant HISTORICAL_BATCH_TREE_HEIGHT = 1;

    // SLOTS_PER_HISTORICAL_ROOT = 2**13, so tree height is 13
    uint256 internal constant STATE_ROOTS_TREE_HEIGHT = 13;
    uint256 internal constant BLOCK_ROOTS_TREE_HEIGHT = 13;


    uint256 internal constant NUM_WITHDRAWAL_FIELDS = 4;
    // tree height for hash tree of an individual withdrawal container
    uint256 internal constant WITHDRAWAL_FIELD_TREE_HEIGHT = 2;

    uint256 internal constant VALIDATOR_TREE_HEIGHT = 40;
    //refer to the eigenlayer-cli proof library.  Despite being the same dimensions as the validator tree, the balance tree is merkleized differently
    uint256 internal constant BALANCE_TREE_HEIGHT = 38;

    // MAX_WITHDRAWALS_PER_PAYLOAD = 2**4, making tree height = 4
    uint256 internal constant WITHDRAWALS_TREE_HEIGHT = 4;

    //in beacon block body
    uint256 internal constant EXECUTION_PAYLOAD_INDEX = 9;

    // in beacon block header
    uint256 internal constant STATE_ROOT_INDEX = 3;
    uint256 internal constant PROPOSER_INDEX_INDEX = 1;
    uint256 internal constant SLOT_INDEX = 0;
    uint256 internal constant BODY_ROOT_INDEX = 4;
    // in beacon state
    uint256 internal constant STATE_ROOTS_INDEX = 6;
    uint256 internal constant BLOCK_ROOTS_INDEX = 5;
    uint256 internal constant HISTORICAL_ROOTS_INDEX = 7;
    uint256 internal constant ETH_1_ROOT_INDEX = 8;
    uint256 internal constant VALIDATOR_TREE_ROOT_INDEX = 11;
    uint256 internal constant BALANCE_INDEX = 12;
    uint256 internal constant EXECUTION_PAYLOAD_HEADER_INDEX = 24;
    uint256 internal constant HISTORICAL_BATCH_STATE_ROOT_INDEX = 1;

    // in validator
    uint256 internal constant VALIDATOR_WITHDRAWAL_CREDENTIALS_INDEX = 1;
    uint256 internal constant VALIDATOR_BALANCE_INDEX = 2;
    uint256 internal constant VALIDATOR_SLASHED_INDEX = 3;
    uint256 internal constant VALIDATOR_WITHDRAWABLE_EPOCH_INDEX = 7;
    
    // in exection payload header
    uint256 internal constant BLOCK_NUMBER_INDEX = 6;
    uint256 internal constant WITHDRAWALS_ROOT_INDEX = 14;

    //in execution payload
    uint256 internal constant WITHDRAWALS_INDEX = 14;

    // in withdrawal
    uint256 internal constant WITHDRAWAL_VALIDATOR_INDEX_INDEX = 1;
    uint256 internal constant WITHDRAWAL_VALIDATOR_AMOUNT_INDEX = 3;

    //In historicalBatch
    uint256 internal constant HISTORICALBATCH_STATEROOTS_INDEX = 1;

    //Misc Constants
    uint256 internal constant SLOTS_PER_EPOCH = 32;

    bytes8 internal constant UINT64_MASK = 0xffffffffffffffff;
```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/libraries/BeaconChainProofs.sol#L14-L100

```solidity
file:    DelayedWithdrawalRouter.sol
22          uint8 internal constant PAUSED_DELAYED_WITHDRAWAL_CLAIMS = 0;

    /**
     * @notice Delay enforced by this contract for completing any delayedWithdrawal. Measured in blocks, and adjustable by this contract's owner,
     * up to a maximum of `MAX_WITHDRAWAL_DELAY_BLOCKS`. Minimum value is 0 (i.e. no delay enforced).
     */
    uint256 public withdrawalDelayBlocks;
    // the number of 12-second blocks in one week (60 * 60 * 24 * 7 / 12 = 50,400)
    uint256 public constant MAX_WITHDRAWAL_DELAY_BLOCKS = 50400;

```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/DelayedWithdrawalRouter.sol#L16-L24

```solidity
file:    EigenPodPausingConstants.sol
10        uint8 internal constant PAUSED_NEW_EIGENPODS = 0;
    /// @notice Index for flag that pauses the `withdrawRestakedBeaconChainETH` function *of the EigenPodManager* when set. See EigenPodManager code for details.
    uint8 internal constant PAUSED_WITHDRAW_RESTAKED_ETH = 1;

    /// @notice Index for flag that pauses the `verifyCorrectWithdrawalCredentials` function *of the EigenPods* when set. see EigenPod code for details.
    uint8 internal constant PAUSED_EIGENPODS_VERIFY_CREDENTIALS = 2;
    /// @notice Index for flag that pauses the `verifyOvercommittedStake` function *of the EigenPods* when set. see EigenPod code for details.
    uint8 internal constant PAUSED_EIGENPODS_VERIFY_OVERCOMMITTED = 3;
    /// @notice Index for flag that pauses the `verifyBeaconChainFullWithdrawal` function *of the EigenPods* when set. see EigenPod code for details.
    uint8 internal constant PAUSED_EIGENPODS_VERIFY_WITHDRAWAL = 4;
```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPodPausingConstants.sol#L10-L19

```solidity
file:       Pausable.sol
22          uint256 internal constant UNPAUSE_ALL = 0;
            uint256 internal constant PAUSE_ALL = type(uint256).max;
```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/permissions/Pausable.sol#L22-L23
```solidity
file:    StrategyBase.sol
22          uint8 internal constant PAUSED_DEPOSITS = 0;
    uint8 internal constant PAUSED_WITHDRAWALS = 1;
    /*
     * as long as at least *some* shares exist, this is the minimum number.
     * i.e. `totalShares` must exist in the set {0, [MIN_NONZERO_TOTAL_SHARES, type(uint256).max]}
     */
    uint96 internal constant MIN_NONZERO_TOTAL_SHARES = 1e9;

```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/strategies/StrategyBase.sol#L22-L28

```solidity
file:    StrategyManager.sol
35          uint256 internal constant GWEI_TO_WEI = 1e9;

    // index for flag that pauses deposits when set
    uint8 internal constant PAUSED_DEPOSITS = 0;
    // index for flag that pauses withdrawals when set
    uint8 internal constant PAUSED_WITHDRAWALS = 1;

    uint256 immutable ORIGINAL_CHAIN_ID;

    // bytes4(keccak256("isValidSignature(bytes32,bytes)")
    bytes4 internal constant ERC1271_MAGICVALUE = 0x1626ba7e;
```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L35-L40

### [G-22] Multiple accesses of a mapping/array should use a local variable cache
```solidity
file:    DelayedWithdrawalRouter.sol
66       _userWithdrawals[recipient].delayedWithdrawals.push(
67      _userWithdrawals[recipient].delayedWithdrawals.length - 1
106      return _userWithdrawals[user];
111      uint256  uint256 delayedWithdrawalsCompleted = _userWithdrawals[user].delayedWithdrawalsCompleted;
112      uint256 delayedWithdrawalsLength = _userWithdrawals[user].delayedWithdrawals.length;
116       claimableDelayedWithdrawals[i] = _userWithdrawals[user].delayedWithdrawals[delayedWithdrawalsCompleted + i];
123        return _userWithdrawals[user].delayedWithdrawals[index];
128        return _userWithdrawals[user].delayedWithdrawals.length;
133       return ((index >= _userWithdrawals[user].delayedWithdrawalsCompleted) && (block.number >= _userWithdrawals[user].delayedWithdrawals[index].blockCreated + withdrawalDelayBlocks));
139        uint256 delayedWithdrawalsCompletedBefore = _userWithdrawals[recipient].delayedWithdrawalsCompleted;
140        uint256 _userWithdrawalsLength = _userWithdrawals[recipient].delayedWithdrawals.length;
142        while (i < maxNumberOfDelayedWithdrawalsToClaim && (delayedWithdrawalsCompletedBefore + i) < _userWithdrawalsLength) {
144        DelayedWithdrawal memory delayedWithdrawal = _userWithdrawals[recipient].delayedWithdrawals[delayedWithdrawalsCompletedBefore + i];
157        _userWithdrawals[recipient].delayedWithdrawalsCompleted =
            delayedWithdrawalsCompletedBefore +
            i;
```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/DelayedWithdrawalRouter.sol#L66
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/DelayedWithdrawalRouter.sol#L67
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/DelayedWithdrawalRouter.sol#L106
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/DelayedWithdrawalRouter.sol#L111
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/DelayedWithdrawalRouter.sol#L112
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/DelayedWithdrawalRouter.sol#L116
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/DelayedWithdrawalRouter.sol#L123
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/DelayedWithdrawalRouter.sol#L128
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/DelayedWithdrawalRouter.sol#L133
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/DelayedWithdrawalRouter.sol#L139
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/DelayedWithdrawalRouter.sol#L140
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/DelayedWithdrawalRouter.sol#L142
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/DelayedWithdrawalRouter.sol#L144
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/DelayedWithdrawalRouter.sol#L157

```solidity
file:     pods/EigenPod.sol
186       validatorStatus[validatorIndex] == VALIDATOR_STATUS.INACTIVE,
215       validatorStatus[validatorIndex] = VALIDATOR_STATUS.ACTIVE;
252       require(validatorStatus[validatorIndex] == VALIDATOR_STATUS.ACTIVE, "EigenPod.verifyOvercommittedStake: Validator not active");
288       validatorStatus[validatorIndex] = VALIDATOR_STATUS.OVEpods/EigenPod.solRCOMMITTED;
333       require(validatorStatus[validatorIndex] != VALIDATOR_STATUS.INACTIVE,
412       validatorStatus[validatorIndex] = VALIDATOR_STATUS.WITHDRAWN;
```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPod.sol#L186
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPod.sol#L215
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPod.sol#L252
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPod.sol#L288
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPod.sol#L333
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPod.sol#L412

```solidity
file:
67       require(address(ownerToPod[podOwner]) == msg.sender, "EigenPodManager.onlyEigenPod: not a pod");
113      IEigenPod pod = ownerToPod[msg.sender];
153      ownerToPod[podOwner].withdrawRestakedBeaconChainETH(recipient, amount);
181      ownerToPod[msg.sender] = pod;
194      IEigenPod pod = ownerToPod[podOwner];
211      return address(ownerToPod[podOwner]) != address(0);
```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPodManager.sol#L67
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPodManager.sol#L113
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPodManager.sol#L153
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPodManager.sol#L181
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPodManager.sol#L194
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPodManager.sol#L211

```solidity
file:     BeaconChainProofs.sol
130       function computePhase0BeaconBlockHeaderRoot(bytes32[NUM_BEACON_BLOCK_HEADER_FIELDS] calldata blockHeaderFields) internal pure returns(bytes32) {
        bytes32[] memory paddedHeaderFields = new bytes32[](2**BEACON_BLOCK_HEADER_FIELD_TREE_HEIGHT);
        
        for (uint256 i = 0; i < NUM_BEACON_BLOCK_HEADER_FIELDS; ++i) {
            paddedHeaderFields[i] = blockHeaderFields[i];
        }
```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/libraries/BeaconChainProofs.sol#L130-L135

```solidity
file:
189     require(validatorFields[BeaconChainProofs.VALIDATOR_WITHDRAWAL_CREDENTIALS_INDEX] == bytes32(_podWithdrawalCredentials()),
200             BeaconChainProofs.verifyValidatorFields(
            validatorIndex,
            beaconStateRoot,
            proofs.validatorFieldsProof,
            validatorFields
        );

```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPod.sol#L189
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPod.sol#L200-L205

### [G-23] Remove the initializer modifier
```solidity
file:    DelayedWithdrawalRouter.sol
49       function initialize(address initOwner, IPauserRegistry _pauserRegistry, uint256 initPausedStatus, uint256 _withdrawalDelayBlocks) external initializer {
```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/DelayedWithdrawalRouter.sol#L49

```solidity
file:    EigenPod.sol
152      function initialize(address _podOwner) external initializer {

```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPod.sol#L152

```solidity
file:     EigenPodManager.sol
89        external initializer {
```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPodManager.sol#L89

```solidity
file:   StrategyBase.sol
51      function initialize(IERC20 _underlyingToken, IPauserRegistry _pauserRegistry) public virtual initializer {
```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/strategies/StrategyBase.sol#L51

```solidity
file:       StrategyManager.sol
146         function initialize(address initialOwner, address initialStrategyWhitelister, IPauserRegistry _pauserRegistry, uint256 initialPausedStatus, uint256 _withdrawalDelayBlocks)
        external
        initializer
    {
```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L146-L149

### [G-24] Use constants instead of type(uintx).max
```solidity
file:   Pausable.sol
23      uint256 internal constant PAUSE_ALL = type(uint256).max;
82      _paused = type(uint256).max;
83       emit Paused(msg.sender, type(uint256).max);
```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/permissions/Pausable.sol#L23
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/permissions/Pausable.sol#L82
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/permissions/Pausable.sol#L83

### [G-25] USE ASSEMBLY TO CHECK FOR ADDRESS(0) 

```solidity
file:   StrategyManager.sol
343     require(withdrawer != address(0), "StrategyManager.queueWithdrawal: cannot withdraw to zero address");
631     require(depositor != address(0), "StrategyManager._addShares: depositor cannot be zero address");
684     require(depositor != address(0), "StrategyManager._removeShares: depositor cannot be zero address");
```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L343
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L631
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L684

```solidity
file:   EigenPodManager.sol
114     if(address(pod) == address(0))
196     if (address(pod) == address(0)) 
211     return address(ownerToPod[podOwner]) != address(0);
```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPodManager.sol#L114
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPodManager.sol#L196
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPodManager.sol#L211

```solidity
file:    EigenPod.sol
153      require(_podOwner != address(0), "EigenPod.initialize: podOwner cannot be zero address");
```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPod.sol#L153

```solidity
file:    DelayedWithdrawalRouter.sol
45       require(address(_eigenPodManager) != address(0), "DelayedWithdrawalRouter.constructor: _eigenPodManager cannot be zero address");
```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/DelayedWithdrawalRouter.sol#L45

```solidity
file:   Pausable.sol 
57      address(pauserRegistry) == address(0) && address(_pauserRegistry) != address(0),
```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/permissions/Pausable.sol#L57

```solidity
file:   PauserRegistry.sol
42      require(newPauser != address(0), "PauserRegistry._setPauser: zero address input");
48      require(newUnpauser != address(0), "PauserRegistry._setUnpauser: zero address input");
```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/permissions/PauserRegistry.sol#L42
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/permissions/PauserRegistry.sol#L48


### [G-26]   Use function instead of modifiers
```solidity
file:  StrategyManager.sol
96     modifier onlyNotFrozen(address staker)
104    modifier onlyFrozen(address staker)
119    modifier onlyStrategiesWhitelistedForDeposit(IStrategy strategy)

```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L96
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L104
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L119

```solidity
file:  StrategyBase.sol
40     modifier onlyStrategyManager()
```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/strategies/StrategyBase.sol#L40

```solidity
file:   EigenPodManager.sol
66      modifier onlyEigenPod(address podOwner)
```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPodManager.sol#L66

```solidity
file:  EigenPod.sol 
120    modifier proofIsForValidBlockNumber(uint64 blockNumber)
131    modifier onlyWhenNotPaused(uint8 index) 
```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPod.sol#L120
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPod.sol#L131

```solidity
file:   DelayedWithdrawalRouter.sol
39      modifier onlyEigenPod(address podOwner) 
```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/DelayedWithdrawalRouter.sol#L39

```solidity
file:   Pausable.sol
32      modifier onlyPauser() 
37      modifier onlyUnpauser()
43      modifier whenNotPaused()   
49      modifier onlyWhenNotPaused(uint8 index)
```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/permissions/Pausable.sol#L32
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/permissions/Pausable.sol#L37
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/permissions/Pausable.sol#L43
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/permissions/Pausable.sol#L49

```solidity
file:  PauserRegistry.sol
21     modifier onlyUnpauser()
```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/permissions/PauserRegistry.sol#L21

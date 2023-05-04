### Low risk issues
| |Issue|Instances| |
|-|:-|:-:|:-:|
| [L&#x2011;01] | For immutable variables, Zero address checks are missing in constructor | 12 |
| [L&#x2011;02] | In initialize function, Zero address checks are missing | 11 |


### Non-Critical issues
| |Issue|Instances| |
|-|:-|:-:|:-:|
| [N&#x2011;01] | Use named parameters for mapping type declarations | 11 |


### Low risk issues
### [L&#x2011;01]  For immutable variables, Zero address checks are missing in constructor
Zero address checks should be used in the constructors, to avoid the risk of setting a storage variable as zero address at the time of deployment.
There are 12 instances of this issue:

1)In StrategyManagerStorage.sol contract, delegation, eigenPodManager, slasher are immutable variables.
  
```solidity
File: src/contracts/core/StrategyManagerStorage.sol

72    constructor(IDelegationManager _delegation, IEigenPodManager _eigenPodManager, ISlasher _slasher) {
73        delegation = _delegation;
74        eigenPodManager = _eigenPodManager;
75        slasher = _slasher;
76    }
```
[Link to code](https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/core/StrategyManagerStorage.sol#L72-L76)

### Recommended Mitigation steps
Add address(0) check in constructor.

```solidity
File: src/contracts/core/StrategyManagerStorage.sol

    constructor(IDelegationManager _delegation, IEigenPodManager _eigenPodManager, ISlasher _slasher) {
+       require(address(_delegation) != address(0), "invalid address");
+       require(address(_eigenPodManager) != address(0), "invalid address");
+       require(address(_slasher) != address(0), "invalid address");
        delegation = _delegation;
        eigenPodManager = _eigenPodManager;
        slasher = _slasher;
    }
```

2)In StrategyBase.sol contract, strategyManager is immutable variables.
  
```solidity
File: src/contracts/strategies/StrategyBase.sol

46    constructor(IStrategyManager _strategyManager) {
47        strategyManager = _strategyManager;
48        _disableInitializers();
49    }
```
[Link to code](https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/strategies/StrategyBase.sol#L46-L49)

### Recommended Mitigation steps
Add address(0) check in constructor.

```solidity
File: src/contracts/strategies/StrategyBase.sol

    constructor(IStrategyManager _strategyManager) {
+       require(address(_strategyManager) != address(0), "invalid address");
        strategyManager = _strategyManager;
        _disableInitializers();
    }
```

3)In EigenPodManager.sol contract, ethPOS , eigenPodBeacon , strategyManager and slasher are immutable variables.
  
```solidity
File: src/contracts/pods/EigenPodManager.sol

76    constructor(IETHPOSDeposit _ethPOS, IBeacon _eigenPodBeacon, IStrategyManager _strategyManager, ISlasher 
        _slasher) {
77        ethPOS = _ethPOS;
78        eigenPodBeacon = _eigenPodBeacon;
79        strategyManager = _strategyManager;
80        slasher = _slasher;
81        _disableInitializers();
82    }
```
[Link to code](https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/pods/EigenPodManager.sol#L76-L82)

### Recommended Mitigation steps
Add address(0) check in constructor.

```solidity
File: src/contracts/pods/EigenPodManager.sol

    constructor(IETHPOSDeposit _ethPOS, IBeacon _eigenPodBeacon, IStrategyManager _strategyManager, ISlasher 
     _slasher) {
+       require(address(_ethPOS) != address(0), "invalid address");
+       require(address(_eigenPodBeacon) != address(0), "invalid address");
+       require(address(_strategyManager) != address(0), "invalid address");
+       require(address(_slasher) != address(0), "invalid address");
        ethPOS = _ethPOS;
        eigenPodBeacon = _eigenPodBeacon;
        strategyManager = _strategyManager;
        slasher = _slasher;
        _disableInitializers();
    }
```

4)In EigenPod.sol contract, ethPOS , delayedWithdrawalRouter , eigenPodManager and _REQUIRED_BALANCE_WEI are immutable variables.
  
```solidity
File: src/contracts/pods/EigenPod.sol

136    constructor(
137        IETHPOSDeposit _ethPOS,
138        IDelayedWithdrawalRouter _delayedWithdrawalRouter,
139        IEigenPodManager _eigenPodManager,
140        uint256 _REQUIRED_BALANCE_WEI
141    ) {
142        ethPOS = _ethPOS;
143        delayedWithdrawalRouter = _delayedWithdrawalRouter;
144        eigenPodManager = _eigenPodManager;
145        REQUIRED_BALANCE_WEI = _REQUIRED_BALANCE_WEI;
146        REQUIRED_BALANCE_GWEI = uint64(_REQUIRED_BALANCE_WEI / GWEI_TO_WEI);
147        require(_REQUIRED_BALANCE_WEI % GWEI_TO_WEI == 0, "EigenPod.contructor: _REQUIRED_BALANCE_WEI is not a 
           whole number of gwei");
148        _disableInitializers();
149    }
```
[Link to code](https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/pods/EigenPod.sol#L136-L149)


### Recommended Mitigation steps
Add address(0) check in constructor also check _REQUIRED_BALANCE_WEI should not be equal to zero.

```solidity
File: src/contracts/pods/EigenPod.sol

    constructor(
        IETHPOSDeposit _ethPOS,
        IDelayedWithdrawalRouter _delayedWithdrawalRouter,
        IEigenPodManager _eigenPodManager,
        uint256 _REQUIRED_BALANCE_WEI
    ) {
+       require(address(_ethPOS) != address(0), "invalid address");
+       require(address(_delayedWithdrawalRouter) != address(0), "invalid address");
+       require(address(_eigenPodManager) != address(0), "invalid address");
+       require(_REQUIRED_BALANCE_WEI != 0, "invalid value");
        ethPOS = _ethPOS;
        delayedWithdrawalRouter = _delayedWithdrawalRouter;
        eigenPodManager = _eigenPodManager;
        REQUIRED_BALANCE_WEI = _REQUIRED_BALANCE_WEI;
        REQUIRED_BALANCE_GWEI = uint64(_REQUIRED_BALANCE_WEI / GWEI_TO_WEI);
        require(_REQUIRED_BALANCE_WEI % GWEI_TO_WEI == 0, "EigenPod.contructor: _REQUIRED_BALANCE_WEI is not a 
         whole number of gwei");
        _disableInitializers();
    }
```

### [L&#x2011;02]  In initialize function, Zero address checks are missing
Initialize function can only be called once in smart contract. Any mistake in setting address variables can cause in redeployment of contract therefore address(0) checks are recommended. 
There are 11 instances of this issue:

1)In StrategyManager.sol contract,

```solidity
File: src/contracts/core/StrategyManager.sol

146    function initialize(address initialOwner, address initialStrategyWhitelister, IPauserRegistry 
           _pauserRegistry, uint256 initialPausedStatus, uint256 _withdrawalDelayBlocks)
147        external
148        initializer
149    {
150        DOMAIN_SEPARATOR = keccak256(abi.encode(DOMAIN_TYPEHASH, keccak256(bytes("EigenLayer")), 
          ORIGINAL_CHAIN_ID, address(this)));
151        _initializePauser(_pauserRegistry, initialPausedStatus);
152        _transferOwnership(initialOwner);
153        _setStrategyWhitelister(initialStrategyWhitelister);
154        _setWithdrawalDelayBlocks(_withdrawalDelayBlocks);
155    }
```
[Link to code](https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/core/StrategyManager.sol#L146-L155)

### Recommended Mitigation steps
Add address(0) check in initialize function.

```solidity
File: src/contracts/core/StrategyManager.sol

    function initialize(address initialOwner, address initialStrategyWhitelister, IPauserRegistry 
          _pauserRegistry, uint256 initialPausedStatus, uint256 _withdrawalDelayBlocks)
        external
        initializer
    {
+       require(initialOwner != address(0), "invalid address");
+       require(initialStrategyWhitelister != address(0), "invalid address");
+       require(address(_pauserRegistry) != address(0), "invalid address");
+       require(_withdrawalDelayBlocks != 0, "invalid value");

        DOMAIN_SEPARATOR = keccak256(abi.encode(DOMAIN_TYPEHASH, keccak256(bytes("EigenLayer")), 
           ORIGINAL_CHAIN_ID, address(this)));
        _initializePauser(_pauserRegistry, initialPausedStatus);
        _transferOwnership(initialOwner);
        _setStrategyWhitelister(initialStrategyWhitelister);
        _setWithdrawalDelayBlocks(_withdrawalDelayBlocks);
    }

```

2)In StrategyBase.sol,

```solidity
File: src/contracts/strategies/StrategyBase.sol

51    function initialize(IERC20 _underlyingToken, IPauserRegistry _pauserRegistry) public virtual initializer {
52        _initializeStrategyBase(_underlyingToken, _pauserRegistry);
53    }
```
[Link to code](https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/strategies/StrategyBase.sol#L51-L53)

### Recommended Mitigation steps
Add address(0) check in initialize function.

```solidity
File: src/contracts/strategies/StrategyBase.sol

    function initialize(IERC20 _underlyingToken, IPauserRegistry _pauserRegistry) public virtual initializer {
+        require(address(_underlyingToken) != address(0), "invalid address");
+        require(address(_pauserRegistry) != address(0), "invalid address");
        _initializeStrategyBase(_underlyingToken, _pauserRegistry);
    }
```

3)In EigenPodManager.sol,

```solidity
File: src/contracts/pods/EigenPodManager.sol

84    function initialize(
85        IBeaconChainOracle _beaconChainOracle,
86        address initialOwner,
87        IPauserRegistry _pauserRegistry,
88        uint256 _initPausedStatus
89    ) external initializer {
90        _updateBeaconChainOracle(_beaconChainOracle);
91        _transferOwnership(initialOwner);
92        _initializePauser(_pauserRegistry, _initPausedStatus);
93    }
```
[Link to code](https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/pods/EigenPodManager.sol#L84-L93)

### Recommended Mitigation steps
Add address(0) check in initialize function.

```solidity
File: src/contracts/pods/EigenPodManager.sol

    function initialize(
        IBeaconChainOracle _beaconChainOracle,
        address initialOwner,
        IPauserRegistry _pauserRegistry,
        uint256 _initPausedStatus
    ) external initializer {
+        require(address(_beaconChainOracle) != address(0), "invalid address");
+        require(initialOwner != address(0), "invalid address");
+        require(address(_pauserRegistry) != address(0), "invalid address");
        _updateBeaconChainOracle(_beaconChainOracle);
        _transferOwnership(initialOwner);
        _initializePauser(_pauserRegistry, _initPausedStatus);
    }
```

4)In DelayedWithdrawalRouter.sol,

```solidity
File: src/contracts/pods/DelayedWithdrawalRouter.sol

49    function initialize(address initOwner, IPauserRegistry _pauserRegistry, uint256 initPausedStatus, uint256 
          _withdrawalDelayBlocks) external initializer {
50        _transferOwnership(initOwner);
51        _initializePauser(_pauserRegistry, initPausedStatus);
52        _setWithdrawalDelayBlocks(_withdrawalDelayBlocks);
53    }
```
[Link to code](https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/pods/DelayedWithdrawalRouter.sol#L49-L53)

### Recommended Mitigation steps
Add address(0) check in initialize function.

```solidity
File: src/contracts/pods/DelayedWithdrawalRouter.sol

    function initialize(address initOwner, IPauserRegistry _pauserRegistry, uint256 initPausedStatus, uint256 
           _withdrawalDelayBlocks) external initializer {
+        require(initOwner != address(0), "invalid address");
+        require(address(_pauserRegistry) != address(0), "invalid address");
        _transferOwnership(initOwner);
        _initializePauser(_pauserRegistry, initPausedStatus);
        _setWithdrawalDelayBlocks(_withdrawalDelayBlocks);
    }
```



### Non-Critical issues
### [N&#x2011;01]  Use named parameters for mapping type declarations
Consider using named parameters in mappings (e.g. mapping(address account => uint256 balance)) to improve readability. This feature is present since Solidity 0.8.18.
There are 11 instances of this issue.

```solidity
File: src/contracts/core/StrategyManagerStorage.sol

25    mapping(address => uint256) public nonces;

49    mapping(address => mapping(IStrategy => uint256)) public stakerStrategyShares;

51    mapping(address => IStrategy[]) public stakerStrategyList;

53    mapping(bytes32 => bool) public withdrawalRootPending;

55    mapping(address => uint256) public numWithdrawalsQueued;

57    mapping(IStrategy => bool) public strategyIsWhitelistedForDeposit;

68    mapping(address => uint256) public beaconChainETHSharesToDecrementOnWithdrawal;
```

```solidity
File: src/contracts/pods/EigenPodManager.sol

55    mapping(address => IEigenPod) public ownerToPod;
```

```solidity
File: src/contracts/pods/EigenPod.sol

76    mapping(uint40 => VALIDATOR_STATUS) public validatorStatus;

79    mapping(uint40 => mapping(uint64 => bool)) public provenPartialWithdrawal;
```

```solidity
File: src/contracts/pods/DelayedWithdrawalRouter.sol

30    mapping(address => UserDelayedWithdrawals) internal _userWithdrawals;
```

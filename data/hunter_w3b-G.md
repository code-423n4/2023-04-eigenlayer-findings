## Summary

### Gas Optimizations

|               | Issue                                                                                                        | Instances | Total Gas Saved |
| ------------- | :----------------------------------------------------------------------------------------------------------- | :-------: | :-------------: |
| [G&#x2011;01] | Use assembly to write `address storage` values                                                               |    16     |        -        |
| [G&#x2011;02] | Avoid `contract existence` checks by using solidity version 0.8.10 or later                                  |     3     |       300       |
| [G&#x2011;03] | Use nested if and, avoid `multiple` check combinations                                                       |     3     |        -        |
| [G&#x2011;04] | Not using the named return variables when a function returns, `wastes` deployment gas                        |     7     |        -        |
| [G&#x2011;05] | Expressions for `constant` values such as a call to `keccak256()`, should use immutable rather than constant |     2     |        -        |
| [G&#x2011;06] | Public `functions` not called by the contract should be declared `external` instead                          |     2     |        -        |
| [G&#x2011;07] | Use solidity version `0.8.19` to gain some gas boost                                                         |    24     |      2112       |
| [G&#x2011;08] | Stack variable used as a cheaper cache for `state variables` is `only used once`                             |     4     |       12        |
| [G&#x2011;09] | Before some functions, we should `check some variables` for possible gas save                                |     1     |        -        |
| [G&#x2011;10] | The result of function calls should be cached rather than re-calling the function                            |     5     |        -        |
| [G&#x2011;11] | `Duplicated` require()/if() checks should be refactored to a modifier or function                            |     5     |        -        |
| [G&#x2011;12] | A `modifier` used only once and not being inherited should be `inlined` to save gas                          |     7     |        -        |
| [G&#x2011;13] | `Keccak256()` should only need tobe called on a specific string literal once                                 |     2     |        -        |
| [G&#x2011;14] | Using `calldata` instead of `memory` for read-only arguments in external functions saves gas                 |     2     |       600       |
| [G&#x2011;15] | Using `storage` instead of memory for structs/arrays saves gas                                               |    13     |        -        |
| [G&#x2011;16] | Remove the `initializer` modifier                                                                            |     5     |        -        |
| [G&#x2011;17] | Use in constants instead of `type(uintx).max`                                                                |     2     |        -        |
| [G&#x2011;18] | Use assembly to check for `address(0)`                                                                       |    11     |        -        |
| [G&#x2011;19] | `abi.encode()` is less efficient than `abi.encodePacked()`                                                   |     6     |       600       |
| [G&#x2011;20] | Use `selfbalance()` instead of `address(this).balance`                                                       |     1     |        -        |
| [G&#x2011;21] | Use double require instead of using `&&`                                                                     |     1     |        9        |
| [G&#x2011;22] | Use hardcoded address instead `address(this)`                                                                |     4     |        -        |
| [G&#x2011;23] | It Costs More Gas To Initialize Variables To Zero Than To Let The Default Of Zero Be `Applied`               |    23     |        -        |

Total: 149 instances over 23 issues

## Gas Optimizations

## [G-01] Use `assembly` to write address `storage` values

### Details

There are **16** instances of this issue.

### Github Permalinks

```solidity
File: /contracts/core/StrategyManagerStorage.sol

73        delegation = _delegation;

74        eigenPodManager = _eigenPodManager;

75        slasher = _slasher;
```

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManagerStorage.sol

```solidity
File: /contracts/strategies/StrategyBase.sol

47        strategyManager = _strategyManager;
```

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/strategies/StrategyBase.sol#L47

```solidity
File: /contracts/pods/EigenPodManager.sol

77        ethPOS = _ethPOS;

78        eigenPodBeacon = _eigenPodBeacon;

79        strategyManager = _strategyManager;

80        slasher = _slasher;
```

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPodManager.sol

```solidity
File: /contracts/pods/EigenPod.sol

142        ethPOS = _ethPOS;

143        delayedWithdrawalRouter = _delayedWithdrawalRouter;

144        eigenPodManager = _eigenPodManager;

154        podOwner = _podOwner;
```

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPod.sol

```solidity
File: /contracts/pods/DelayedWithdrawalRouter.sol

46        eigenPodManager = _eigenPodManager;
```

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/DelayedWithdrawalRouter.sol#L46

```solidity
File: /contracts/permissions/Pausable.sol

62        pauserRegistry = _pauserRegistry;
```

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/permissions/Pausable.sol#L62

```solidity
File: /contracts/permissions/PauserRegistry.sol

44        pauser = newPauser;

50        unpauser = newUnpauser;
```

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/permissions/PauserRegistry.sol

### Recommendation Code

```solidity
File: /contracts/core/StrategyManagerStorage.sol

72    constructor(IDelegationManager _delegation, IEigenPodManager _eigenPodManager, ISlasher _slasher) {
- 73        delegation = _delegation;

+         assembly {
+               sstore(delegation.slot, _delegation)
+            }
```

## [G-02] Avoid `contract` existence checks by using low level calls

### Summary

Prior to 0.8.10 the compiler inserted extra code, including EXTCODESIZE (100 gas), to check for contract existence for external function calls. In more recent solidity versions, the compiler will not insert these checks if the external call has a return value. Similar behavior can be achieved in earlier versions by using low-level calls, since low level calls never check for contract existence.

### Details

There are **3** instances of this issue.

### Github Permalinks

```solidity
File: /contracts/core/StrategyManager.sol

/// @audit  isValidSignature()
290            require(IERC1271(staker).isValidSignature(digestHash, signature) == ERC1271_MAGICVALUE,
```

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L290

```solidity
File: /contracts/pods/EigenPodManager.sol

/// @audit deploy()
168     IEigenPod(
                   Create2.deploy(
                      0,
                     bytes32(uint256(uint160(msg.sender))),


/// @audit computeAddress()
198 IEigenPod(
                Create2.computeAddress(
                    bytes32(uint256(uint160(podOwner))), //salt
                    keccak256(abi.encodePacked(
                        beaconProxyBytecode,
                        abi.encode(eigenPodBeacon, "")
```

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPodManager.sol

## [G-03] Use nested if and, avoid `multiple` check combinations

### Summary

Using nested is cheaper than using && multiple check combinations. There are more advantages, such as easier to read code and better coverage reports.

### Details

There are **3** instances of this issue.

### Github Permalinks

```solidity
File: /contracts/core/StrategyManager.sol

422        if (undelegateIfPossible && stakerStrategyList[msg.sender].length == 0) {

562            if (indicesToSkipIndex < indicesToSkip.length && indicesToSkip[indicesToSkipIndex] == i) {
```

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol

```solidity
File: /contracts/pods/DelayedWithdrawalRouter

142        while (i < maxNumberOfDelayedWithdrawalsToClaim && (delayedWithdrawalsCompletedBefore + i) < _userWithdrawalsLength) {
```

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/DelayedWithdrawalRouter.sol#L142

### Recommendation Code

```solidity
File: /contracts/core/StrategyManager.sol

-422        if (undelegateIfPossible && stakerStrategyList[msg.sender].length == 0) {

+           if (undelegateIfPossible) {
+                if (stakerStrategyList[msg.sender].length == 0){
+          }
```

## [G-04] Not using the named return variables when a function returns, `wastes` deployment gas

Do not use return at the end of the function:

### Details

There are **7** instances of this issue.

### Github Permalinks

```solidity
File: /contracts/core/StrategyManager.sol

225        returns (uint256 shares)

260        returns (uint256 shares)

658        returns (uint256 shares)
```

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol

```solidity
File: /contracts/strategies/StrategyBase.sol

84        returns (uint256 newShares)
```

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/strategies/StrategyBase.sol#L84

```solidity
File: /contracts/libraries/Endian.sol

7    ) internal pure returns (uint64 n) {
```

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/libraries/Endian.sol#L7

```solidity
File: /contracts/interfaces/IStrategyManager.sol

91        returns (uint256 shares);

/// @audit shares
94    function stakerStrategyShares(address user, IStrategy strategy) external view returns (uint256 shares);

```

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/interfaces/IStrategyManager.sol

### Recommendation Code

```solidity
File: /contracts/strategies/StrategyBase.sol

-84        returns (uint256 newShares)

+          returns (uint256)
```

## [G-05] Expressions for `constant` values such as a call to `keccak256()`, should use immutable rather than constant

### Details

There are **2** instances of this issue.

### Github Permalinks

```solidity
File: /contracts/core/StrategyManagerStorage.sol

17    bytes32 public constant DOMAIN_TYPEHASH =
        keccak256("EIP712Domain(string name,uint256 chainId,address verifyingContract)");

20    bytes32 public constant DEPOSIT_TYPEHASH =
        keccak256("Deposit(address strategy,address token,uint256 amount,uint256 nonce,uint256 expiry)");
```

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManagerStorage.sol

## [G-6] Public `functions` not called by the contract should be declared `external` instead

Contracts are allowed to override their parents' functions and change the visibility from external to public and can save gas by doing so.
public functions not called in the contract itself should be declared externals.

### Details

There are **8** instances of this issue.

### Github Permalinks

```solidity
File: /contracts/strategies/StrategyBase.sol

/// @audit initialize()
51    function initialize(IERC20 _underlyingToken, IPauserRegistry _pauserRegistry) public virtual initializer {

```

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/strategies/StrategyBase.sol#L51

```solidity
File: /contracts/pods/EigenPodManager.sol

/// @audit getPod
193    function getPod(address podOwner) public view returns (IEigenPod) {
```

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPodManager.sol#L193

## [G-7] Use solidity version `0.8.19` to gain some gas boost

### Summary

Upgrade to the latest solidity version 0.8.19 to get additional gas savings.

See latest release for reference: https://blog.soliditylang.org/2023/02/22/solidity-0.8.19-release-announcement/

### Details

There are **24** instances of this issue.

### Github Permalinks

```solidity
File: /contracts/core/StrategyManager.sol

2   pragma solidity =0.8.12;
```

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L2

```solidity
File: /contracts/core/StrategyManagerStorage.sol

2   pragma solidity =0.8.12;
```

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManagerStorage.sol#L2

```solidity
File: /contracts/strategies/StrategyBase.sol

2   pragma solidity =0.8.12;
```

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/strategies/StrategyBase.sol#L2

```solidity
File: /contracts/pods/EigenPodManager.sol

2   pragma solidity =0.8.12;
```

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPodManager.sol#L2

```solidity
File: /contracts/pods/EigenPod.sol

2   pragma solidity =0.8.12;
```

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPod.sol#L2

```solidity
File: /contracts/pods/EigenPodPausingConstants.sol

2   pragma solidity =0.8.12;
```

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPodPausingConstants.sol#L2

```solidity
File: /contracts/pods/DelayedWithdrawalRouter.sol

2   pragma solidity =0.8.12;
```

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/DelayedWithdrawalRouter.sol#L2

```solidity
File: /contracts/permissions/Pausable.sol

3   pragma solidity =0.8.12;
```

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/permissions/Pausable.sol#L3

```solidity
File: /contracts/permissions/PauserRegistry.sol

2   pragma solidity =0.8.12;
```

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/permissions/PauserRegistry.sol#L2

```solidity
File: /contracts/libraries/BeaconChainProofs.sol

3   pragma solidity =0.8.12;
```

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/libraries/BeaconChainProofs.sol#L3

```solidity
File: /contracts/libraries/Merkle.sol

4   pragma solidity =0.8.12;
```

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/libraries/Merkle.sol#L4

```solidity
File: /contracts/libraries/Endian.sol

2   pragma solidity =0.8.12;
```

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/libraries/Endian.sol#L2

```solidity
File: /contracts/interfaces/ISlasher.sol

2   pragma solidity =0.8.12;
```

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/interfaces/ISlasher.sol#L2

```solidity
File: /contracts/interfaces/IPausable.sol

2   pragma solidity =0.8.12;
```

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/interfaces/IPausable.sol#L2

```solidity
File: /contracts/interfaces/IBeaconChainOracle.sol

2   pragma solidity =0.8.12;
```

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/interfaces/IBeaconChainOracle.sol#L2

```solidity
File: /contracts/interfaces/IStrategy.sol

2   pragma solidity =0.8.12;
```

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/interfaces/IStrategy.sol#L2

```solidity
File: /contracts/interfaces/IStrategyManager.sol

2   pragma solidity =0.8.12;
```

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/interfaces/IStrategyManager.sol#L2

```solidity
File: /contracts/interfaces/IETHPOSDeposit.sol

12   pragma solidity =0.8.12;
```

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/interfaces/IETHPOSDeposit.sol#L12

```solidity
File: /contracts/interfaces/IDelayedWithdrawalRouter.sol

2   pragma solidity =0.8.12;
```

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/interfaces/IDelayedWithdrawalRouter.sol#L2

```solidity
File: /contracts/interfaces/IEigenPod.sol

2   pragma solidity =0.8.12;
```

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/interfaces/IEigenPod.sol#L2

```solidity
File: /contracts/interfaces/IEigenPodManager.sol

2   pragma solidity =0.8.12;
```

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/interfaces/IEigenPodManager.sol#L2

```solidity
File: /contracts/interfaces/IPauserRegistry.sol

2   pragma solidity =0.8.12;
```

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/interfaces/IPauserRegistry.sol#L2

```solidity
File: /contracts/interfaces/IDelegationManager

2   pragma solidity =0.8.12;
```

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/interfaces/IDelegationManager.sol#L2

```solidity
File: /contracts/interfaces/IServiceManager.sol

2   pragma solidity =0.8.12;
```

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/interfaces/IServiceManager.sol#L2

## [G-8] Stack `variable` used as a cheaper cache for a state variable is only used once

### Summary

if a state variable used once use instead stack variable

If the variable is only accessed once, it's cheaper to use the state variable directly that one time, and save the 3 gas the extra stack assignment would spend:

### Details

There are **4** instances of this issue.

### Github Permalinks

```solidity
File: /contracts/core/StrategyManager.sol

35    uint256 internal constant GWEI_TO_WEI = 1e9;

45    bytes4 constant internal ERC1271_MAGICVALUE = 0x1626ba7e;
```

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol

```solidity
File: /contracts/strategies/StrategyBase.sol

22    uint8 internal constant PAUSED_DEPOSITS = 0;

23    uint8 internal constant PAUSED_WITHDRAWALS = 1;
```

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/strategies/StrategyBase.sol

## [G-9] Before some functions, we should check `some variables` for possible gas save

### Summary

Before transfer, we should check for amount being 0 so the function doesn't run when its not gonna do anything:

### Details

There are **1** instances of this issue.

### Github Permalinks

```solidity
File: /contracts/core/StrategyManager.sol

/// @audit amount
661        token.safeTransferFrom(msg.sender, address(strategy), amount);
```

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L661

## [G-10] The result of `function` calls should be cached rather than re-calling the function

### Summary

The instances below point to the second+ call of the function within a single function

### Details

There are **5** instances of this issue.

### Github Permalinks

```solidity
File: /contracts/core/StrategyManager.sol


289        if (Address.isContract(staker)) {

370            if (_removeShares(msg.sender, strategyIndexes[strategyIndexIndex], strategies[i], shares[i])) {

501            if (_removeShares(slashedAddress, strategyIndexes[strategyIndexIndex], strategies[i], shareAmounts[i])) {

```

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol

```solidity
File: /contracts/pods/EigenPodManager.sol

196        if (address(pod) == address(0)) {

```

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPodManager.sol#L196

```solidity
File: /contracts/pods/EigenPod.sol

354        if (Endian.fromLittleEndianUint64(validatorFields[BeaconChainProofs.VALIDATOR_WITHDRAWABLE_EPOCH_INDEX]) <= slot/BeaconChainProofs.SLOTS_PER_EPOCH) {

```

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPod.sol#L354

## [G-11] Duplicated `require()/if()` checks should be refactored to a modifier or function

### Details

There are **5** instances of this issue.

### Github Permalinks

```solidity
File: /contracts/core/StrategyManager.sol

359            if (strategies[i] == beaconChainETHStrategy) {
507            if (strategies[i] == beaconChainETHStrategy) {


548        require(
            withdrawalRootPending[withdrawalRoot],
751        require(
            withdrawalRootPending[withdrawalRoot],
```

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol

```solidity
File: /contracts/strategies/StrategyBase.sol

92         if (totalShares == 0) {
173        if (totalShares == 0) {

```

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/strategies/StrategyBase.sol#L92

```solidity
File: /contracts/pods/EigenPod.sol

373            if (withdrawalAmountGwei >= REQUIRED_BALANCE_GWEI) {
387            if (withdrawalAmountGwei >= REQUIRED_BALANCE_GWEI) {
```

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPod.sol#L373

```solidity
File: /contracts/pods/EigenPodManager.sol

114        if(address(pod) == address(0)) {
196        if (address(pod) == address(0)) {
```

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPodManager.sol

## [G-12] A `modifier` used only once and not being inherited should be `inlined` to save gas

### Details

There are **1** instances of this issue.

### Github Permalinks

```solidity
File: /contracts/pods/EigenPodManager.sol


71    modifier onlyStrategyManager {
```

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPodManager.sol#L71

```solidity
File: /contracts/pods/EigenPod.sol

104    modifier onlyEigenPodOwner {

109    modifier onlyNotFrozen {

114    modifier hasNeverRestaked {
```

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPod.sol

```solidity
File: /contracts/pods/DelayedWithdrawalRouter.sol#L39

39    modifier onlyEigenPod(address podOwner) {
```

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/DelayedWithdrawalRouter.sol#L39

```solidity
File: /contracts/permissions/Pausable.sol

37    modifier onlyUnpauser() {

49    modifier onlyWhenNotPaused(uint8 index) {
```

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/permissions/Pausable.sol

## [G-13]  `Keccak256()` should only need to be called on a specific string literal once

### Summary

It should be saved to an immutable variable, and the variable used instead. If the hash is being used as a part of a function selector, the cast to bytes4 should also only be done once.

### Details

There are **2** instances of this issue.

### Github Permalinks

```solidity
File: /contracts/core/StrategyManagerStorage.sol

18    bytes32 public constant DOMAIN_TYPEHASH =
        keccak256("EIP712Domain(string name,uint256 chainId,address verifyingContract)");

21    bytes32 public constant DEPOSIT_TYPEHASH =
        keccak256("Deposit(address strategy,address token,uint256 amount,uint256 nonce,uint256 expiry)");
```

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManagerStorage.sol

## [G-14] Using `calldata` instead of `memory` for read-only arguments in external functions saves gas

### Summary

When a function with a memory array is called externally, the abi.decode ()  step has to use a for-loop to copy each index of the calldata to the memory index. Each iteration of this for-loop costs at least 60 gas (i.e. 60 \* <mem_array>.length). Using calldata directly, obliviates the need for such a loop in the contract code and runtime execution.
https://github.com/prepo-io/prepo-monorepo/blob/feat/2022-12-prepo/apps/smart-contracts/core/contracts/DepositHook.sol#L75

### Details

There are **2** instances of this issue.

### Github Permalinks

```solidity
File: /contracts/interfaces/IBeaconChainOracle.sol

46    function addOracleSigners(address[] memory _oracleSigners) external;

53    function removeOracleSigners(address[] memory _oracleSigners) external;
```

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/interfaces/IBeaconChainOracle.sol

## [G-15] Using `storage` instead of memory for structs/arrays saves gas

### Summary

When fetching data from a storage location, assigning the data to a memory variable causes all fields of the struct/array to be read from
storage, which incurs a Gcoldsload (2100 gas) for each field of the struct/array.

If the fields are read from the new memory variable, they incur an additional MLOAD rather than a cheap stack read.

Instead of declearing the variable with the memory keyword, declaring the variable with the storage keyword and caching any fields that need to
be re-read in stack variables, will be much cheaper, only incuring the Gcoldsload for the fields actually read.

The only time it makes sense to read the whole struct/array into a memory variable, is if the full struct/array is being returned by the function, is being passed to a function that requires memory, or if the array/struct is being read from another memory array/struct

### Details

There are **13** instances of this issue.

### Github Permalinks

```solidity
File: /contracts/interfaces/IStrategyManager.sol

/// @audit QueuedWithdrawal
203        QueuedWithdrawal memory queuedWithdrawal
```

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/interfaces/IStrategyManager.sol#L203

```solidity
File: /contracts/interfaces/IDelayedWithdrawalRouter.sol

/// @audit  UserDelayedWithdrawals
40    function userWithdrawals(address user) external view returns (UserDelayedWithdrawals memory);


/// @audit UserDelayedWithdrawals
46    function userDelayedWithdrawalByIndex(address user, uint256 index) external view returns (DelayedWithdrawal memory);
```

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/interfaces/IDelayedWithdrawalRouter.sol

```solidity
File: /contracts/core/StrategyManager.sol

200        IStrategy[] memory strategies = new IStrategy[](1);

202        uint256[] memory shareAmounts = new uint256[](1);

859        uint256[] memory shares = new uint256[](strategiesLength);
```

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol

```solidity
File: /contracts/pods/DelayedWithdrawalRouter.sol

114        DelayedWithdrawal[] memory claimableDelayedWithdrawals = new DelayedWithdrawal[](claimableDelayedWithdrawalsLength);
```

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/DelayedWithdrawalRouter.sol#L114

```solidity
File: /contracts/libraries/BeaconChainProofs.sol

131        bytes32[] memory paddedHeaderFields = new bytes32[](2**BEACON_BLOCK_HEADER_FIELD_TREE_HEIGHT);

141        bytes32[] memory paddedBeaconStateFields = new bytes32[](2**BEACON_STATE_FIELD_TREE_HEIGHT);

151        bytes32[] memory paddedValidatorFields = new bytes32[](2**VALIDATOR_FIELD_TREE_HEIGHT);

161        bytes32[] memory paddedEth1DataFields = new bytes32[](2**ETH1_DATA_FIELD_TREE_HEIGHT);
```

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/libraries/BeaconChainProofs.sol

```solidity
File: /contracts/libraries/Merkle.sol

130        bytes32[] memory leaves

135        bytes32[] memory layer = new bytes32[](numNodesInLayer);
```

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/libraries/Merkle.sol

## [G-16] Remove the `initializer` modifier

### Summary

If we can just ensure that the initialize() function could only be called from within the constructor, we shouldn’t need to worry about it getting called again.

In the EVM, the constructor’s job is actually to return the bytecode that will live at the contract’s address. So, while inside a constructor, your address (address(this)) will be the deployment address, but there will be no bytecode at that address! So if we check address(this).code.length before the constructor has finished, even from within a delegatecall, we will get 0. So now let’s update our initialize() function to only run if we are inside a constructor:

Now the Proxy contract’s constructor can still delegatecall initialize(), but if anyone attempts to call it again (after deployment) through the Proxy instance, or tries to call it directly on the above instance, it will revert because address(this).code.length will be nonzero.

Also, because we no longer need to write to any state to track whether initialize() has been called, we can avoid the 20k storage gas cost. In fact, the cost for checking our own code size is only 2 gas, which means we have a 10,000x gas savings over the standard version. Pretty neat!

### Details

There are **5** instances of this issue.

### Github Permalinks

```solidity
File: /contracts/core/StrategyManager.sol

148        initializer
```

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L148

```solidity
File: /contracts/strategies/StrategyBase.sol

51    function initialize(IERC20 _underlyingToken, IPauserRegistry _pauserRegistry) public virtual initializer {
```

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/strategies/StrategyBase.sol#L51

```solidity
File: /contracts/pods/EigenPodManager.sol

89    ) external initializer {
```

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPodManager.sol#L89

```solidity
File: /contracts/pods/EigenPod.sol

152    function initialize(address _podOwner) external initializer {
```

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPod.sol#L152

```solidity
File: /contracts/pods/DelayedWithdrawalRouter.sol

49    function initialize(address initOwner, IPauserRegistry _pauserRegistry, uint256 initPausedStatus, uint256 _withdrawalDelayBlocks) external initializer {
```

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/DelayedWithdrawalRouter.sol#L49

## [G-17] Use constants instead of `type(uintx).max`

### Summary

type(uint256).max it uses more gas in the distribution process and also for each transaction than constant usage.

### Details

There are **2** instances of this issue.

### Github Permalinks

```solidity
File: /contracts/permissions/Pausable.sol

82        _paused = type(uint256).max;

83        emit Paused(msg.sender, type(uint256).max);
```

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/permissions/Pausable.sol

## [G-18] Use assembly to check for `address(0)`

### Details

There are **11** instances of this issue.

### Github Permalinks

```solidity
File: /contracts/core/StrategyManager.sol

/// @audit  withdrawer != address(0)
343        require(withdrawer != address(0), "StrategyManager.queueWithdrawal: cannot withdraw to zero address");

/// @audit  depositor != address(0)
631        require(depositor != address(0), "StrategyManager._addShares: depositor cannot be zero address");

/// @audit  depositor != address(0)
684        require(depositor != address(0), "StrategyManager._removeShares: depositor cannot be zero address");
```

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol

```solidity
File: /contracts/pods/EigenPodManager.sol

114        if(address(pod) == address(0)) {

196        if (address(pod) == address(0)) {

211        return address(ownerToPod[podOwner]) != address(0);
```

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPodManager.sol

```solidity
File: /contracts/pods/EigenPod.sol

153        require(_podOwner != address(0), "EigenPod.initialize: podOwner cannot be zero address");
```

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPod.sol#L153

```solidity
File: /contracts/pods/DelayedWithdrawalRouter.sol

45        require(address(_eigenPodManager) != address(0), "DelayedWithdrawalRouter.constructor: _eigenPodManager cannot be zero address");
```

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/DelayedWithdrawalRouter.sol#L45

```solidity
File: /contracts/permissions/Pausable.sol

57            address(pauserRegistry) == address(0) && address(_pauserRegistry) != address(0),
```

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/permissions/Pausable.sol#L57

```solidity
File: /contracts/permissions/PauserRegistry.sol

42        require(newPauser != address(0), "PauserRegistry._setPauser: zero address input");

48        require(newUnpauser != address(0), "PauserRegistry._setUnpauser: zero address input");
```

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/permissions/PauserRegistry.sol

## [G-19] `abi.encode()` is less efficient than `abi.encodePacked()`

### Details

There are **6** instances of this issue.

### Github Permalinks

```solidity
File: /contracts/core/StrategyManager.sol

/// @audit abi.encode
150        DOMAIN_SEPARATOR = keccak256(abi.encode(DOMAIN_TYPEHASH, keccak256(bytes("EigenLayer")), ORIGINAL_CHAIN_ID, address(this)));

/// @audit abi.encode
268        bytes32 structHash = keccak256(abi.encode(DEPOSIT_TYPEHASH, strategy, token, amount, nonce, expiry));


/// @audit abi.encode
276            bytes32 domain_separator = keccak256(abi.encode(DOMAIN_TYPEHASH, keccak256(bytes("EigenLayer")), block.chainid, address(this)));


/// @audit abi.encode
879                abi.encode(
```

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol

```solidity
File: /contracts/pods/EigenPodManager.sol

/// @audit abi.encode
175                        abi.encode(eigenPodBeacon, "")

/// @audit abi.encode
202                        abi.encode(eigenPodBeacon, "")
```

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPodManager.sol

## [G-20] Use `selfbalance()` instead of `address(this).balance`

### Details

There are **1** instances of this issue.

### Github Permalinks

```solidity
File: /contracts/pods/EigenPod.sol

/// @audit  address(this).balance
456        _sendETH(podOwner, address(this).balance);
```

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPod.sol#L456

## [G-21] Use double require instead of using `&&`

### Summary

Using double require instead of operator && can save more gas.
When having a require statement with 2 or more expressions needed, place the expression that cost less gas first.

### Details

There are **1** instances of this issue.

### Github Permalinks

```solidity
File: /contracts/permissions/Pausable.sol

57  require(
               address(pauserRegistry) == address(0) && address(_pauserRegistry) != address(0),
               "Pausable._initializePauser: _initializePauser() can only be called once"
            );
```

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/permissions/Pausable.sol#L57

### Recommendation Code

```solidity
File: /contracts/permissions/Pausable.sol

56  require(
-57               address(pauserRegistry) == address(0) && address(_pauserRegistry) != address(0),
58             "Pausable._initializePauser: _initializePauser() can only be called once"
59           );

+           require(address(pauserRegistry) == address(0));
+           require(address(_pauserRegistry) != address(0));
```

## [G-22] Use hardcoded address instead `address(this)`

### Summary

Instead of using address(this), it is more gas-efficient to pre-calculate and use the hardcoded address. Foundry's script.sol and solmate's LibRlp.sol contracts can help achieve this.

References: https://book.getfoundry.sh/reference/forge-std/compute-create-address

https://twitter.com/transmissions11/status/1518507047943245824

### Details

There are **4** instances of this issue.

### Github Permalinks

```solidity
File: /contracts/core/StrategyManager.sol

/// @audit address(this)
150        DOMAIN_SEPARATOR = keccak256(abi.encode(DOMAIN_TYPEHASH, keccak256(bytes("EigenLayer")), ORIGINAL_CHAIN_ID, address(this)));


/// @audit address(this)
276            bytes32 domain_separator = keccak256(abi.encode(DOMAIN_TYPEHASH, keccak256(bytes("EigenLayer")), block.chainid, address(this)));

```

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol

```solidity
File: /contracts/strategies/StrategyBase.sol

/// @audit address(this)
236        return strategyManager.stakerStrategyShares(user, IStrategy(address(this)));


/// @audit address(this)
242        return underlyingToken.balanceOf(address(this));
```

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/strategies/StrategyBase.sol

## [G-23] It Costs More Gas To Initialize Variables To Zero Than To Let The Default Of Zero Be Applied

### Details

There are **23** instances of this issue.

### Github Permalinks

```solidity
File: /contracts/core/StrategyManager.sol

38    uint8 internal constant PAUSED_DEPOSITS = 0;

358        for (uint256 i = 0; i < strategies.length;) {

466        for(uint256 i = 0; i < queuedWithdrawals.length; i++) {

498        for (uint256 i = 0; i < strategiesLength;) {

560        for (uint256 i = 0; i < strategiesLength;) {

594        for (uint256 i = 0; i < strategiesToWhitelistLength;) {

609        for (uint256 i = 0; i < strategiesToRemoveFromWhitelistLength;) {

724            uint256 j = 0;

780            for (uint256 i = 0; i < strategiesLength;) {

797            for (uint256 i = 0; i < strategiesLength;) {

861        for (uint256 i = 0; i < strategiesLength;) {
```

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol

```solidity
File: /contracts/strategies/StrategyBase.sol

22    uint8 internal constant PAUSED_DEPOSITS = 0;
```

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/strategies/StrategyBase.sol

```solidity
File: /contracts/pods/DelayedWithdrawalRouter.sol

16    uint8 internal constant PAUSED_DELAYED_WITHDRAWAL_CLAIMS = 0;

115        for (uint256 i = 0; i < claimableDelayedWithdrawalsLength; i++) {

138        uint256 amountToSend = 0;

141        uint256 i = 0;
```

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/DelayedWithdrawalRouter.sol

```solidity
File: /contracts/permissions/Pausable.sol

22    uint256 constant internal UNPAUSE_ALL = 0;
```

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/permissions/Pausable.sol

```solidity
File: /contracts/libraries/BeaconChainProofs.sol

65    uint256 internal constant SLOT_INDEX = 0;

133        for (uint256 i = 0; i < NUM_BEACON_BLOCK_HEADER_FIELDS; ++i) {

143        for (uint256 i = 0; i < NUM_BEACON_STATE_FIELDS; ++i) {

153        for (uint256 i = 0; i < NUM_VALIDATOR_FIELDS; ++i) {

163        for (uint256 i = 0; i < ETH1_DATA_FIELD_TREE_HEIGHT; ++i) {
```

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/libraries/BeaconChainProofs.sol

```solidity
File: /contracts/libraries/Merkle.sol

137        for (uint i = 0; i < numNodesInLayer; i++) {

145            for (uint i = 0; i < numNodesInLayer; i++) {
```

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/libraries/Merkle.sol

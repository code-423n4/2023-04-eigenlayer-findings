Report contents changed:  # LOW FINDINGS

| LOW COUNT| ISSUES | INSTANCES|
|-------|-----|--------|
| [L-1]| initialize() functions could be front run  | 6 |
| [L-2]| Missing Event for initialize  | 2 |
| [L-3]| Use right way to convert bytes to bytes32 | 1 |
| [L-4]| Owner can renounce the ownership   | - |
| [L-5]| LOW LEVEL CALLS WITH SOLIDITY VERSION 0.8.14 CAN RESULT IN OPTIMISER BUG  | 2 |
| [L-6]| OUTDATED COMPILER  | 24 |
| [L-7]| Lack of Sanity/Threshold/Limit Checks  | 5 |
| [L-8]| Function Calls in Loop Could Lead to Denial of Service  | 7 |
| [L-9]| Project Upgrade and Stop Scenario should be  | - |
| [L-10]|  Front running attacks by the onlyOwner | 1 |
| [L-11]| Even with the onlyOwner or owner_only modifier, it is best practice to use the re-entrancy pattern| 1 |
| [L-12]|  Unused Modifiers block  | 2 |
| [L-13]| Vulnerable to cross-chain replay attacks due to static DOMAIN_SEPARATOR/domainSeparator | 3 |
| [L-14]| Insufficient coverage | - |
| [L-15]| Missing Contract-existence Checks Before Low-level Calls | 2 |


 # NON CRITICAL FINDINGS

| NC COUNT| ISSUES | INSTANCES|
|-------|-----|--------|
| [NC-1]| immutable should be uppercase  | 8 |
| [NC-2]| Missing NATSPEC  | - |
| [NC-3]| For functions, follow Solidity standard naming conventions (internal function style rule) | 9 |
| [NC-4]| Need Fuzzing test for unchecked  | 13 |
| [NC-5]| NO SAME VALUE INPUT CONTROL  | 2 |
| [NC-6]| According to the syntax rules, use => mapping ( instead of => mapping( using spaces as keyword  | 5 |
| [NC-7]| Use SMTChecker   | - |
| [NC-8]| Assembly Codes Specific – Should Have Comments | 3 |
| [NC-9]| Shorthand way to write if / else statement| 7 |
| [NC-10]| Don't use named return variables its confusing  | 2 |
| [NC-11]| Constants should be in uppercase | 1 |
| [NC-12]| Shorter inheritance list  | 2 |
| [NC-13]| Include return parameters in NatSpec comments| - |
| [NC-14]| Constants should be defined rather than calculating every time   | 3 |
| [NC-15]| Use constants instead of type(uintx).max | 3 |
| [NC-16]| Tokens accidentally sent to the contract cannot be recovered  | - |
| [NC-17]| Add a timelock to critical functions  | 1 |
| [NC-18]| NatSpec comments should be increased in contracts  | - |
| [NC-19]| Not recommended to use numbers in function names    | - |
| [NC-20]| For critical changes emit both old and new values   | - |



##

## [L-1] initialize() functions could be front run 

initialize() function can be called anybody when the contract is not initialized.
More importantly, if someone else runs this function, they will have full authority because of the transferOwnership function

```solidity
FILE: 2023-04-eigenlayer/src/contracts/core/StrategyManager.sol

146: function initialize(address initialOwner, address initialStrategyWhitelister, IPauserRegistry _pauserRegistry, uint256 initialPausedStatus, uint256 _withdrawalDelayBlocks)
        external
        initializer

```
[StrategyManager.sol#L146-L148](https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/core/StrategyManager.sol#L146-L148)

```solidity
FILE: 2023-04-eigenlayer/src/contracts/strategies/StrategyBase.sol

51: function initialize(IERC20 _underlyingToken, IPauserRegistry _pauserRegistry) public virtual initializer {
        _initializeStrategyBase(_underlyingToken, _pauserRegistry);
    }

```
[StrategyBase.sol#L51-L53](https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/strategies/StrategyBase.sol#L51-L53)

```solidity
FILE: 2023-04-eigenlayer/src/contracts/pods/EigenPod.sol

152: function initialize(address _podOwner) external initializer {

```
[EigenPod.sol#L152](https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/pods/EigenPod.sol#L152)

```solidity
FILE: 2023-04-eigenlayer/src/contracts/pods/EigenPodManager.sol

function initialize(
        IBeaconChainOracle _beaconChainOracle,
        address initialOwner,
        IPauserRegistry _pauserRegistry,
        uint256 _initPausedStatus
    ) external initializer {


```
[EigenPodManager.sol#L84-L89](https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/pods/EigenPodManager.sol#L84-L89)

https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/pods/DelayedWithdrawalRouter.sol#L49

##

## [L-2] Missing Event for initialize

```solidity
FILE: 2023-04-eigenlayer/src/contracts/pods/EigenPod.sol

function initialize(address _podOwner) external initializer {
        require(_podOwner != address(0), "EigenPod.initialize: podOwner cannot be zero address");
        podOwner = _podOwner;
    }

```
[EigenPod.sol#L152-L155](https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/pods/EigenPod.sol#L152-L155)

```solidity
FILE: 2023-04-eigenlayer/src/contracts/strategies/StrategyBase.sol

56: function _initializeStrategyBase(IERC20 _underlyingToken, IPauserRegistry _pauserRegistry) internal onlyInitializing {
        underlyingToken = _underlyingToken;
        _initializePauser(_pauserRegistry, UNPAUSE_ALL);
    }

```
[StrategyBase.sol#L56-L59](https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/strategies/StrategyBase.sol#L56-L59)


### Description
Events help non-contract tools to track changes, and events prevent users from being surprised by changes.

### Recommendation
Add Event-Emit 

##

## [L-3] Use right way to convert bytes to bytes32 

If _podWithdrawalCredentials() returns a value of type bytes, then using bytes32(_podWithdrawalCredentials()) to convert it to a bytes32 variable is not the correct way to do it.

You should first convert the bytes value to a bytes32 value. One way to do this is by using the abi.encodePacked function to concatenate the bytes value with a zero-padded bytes value of length 32, and then using the bytes32 typecast to convert the result to a bytes32 variable

```solidity
FILE: 2023-04-eigenlayer/src/contracts/pods/EigenPod.sol

189:  require(validatorFields[BeaconChainProofs.VALIDATOR_WITHDRAWAL_CREDENTIALS_INDEX] == bytes32(_podWithdrawalCredentials()),

```
[EigenPod.sol#L189](https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/pods/EigenPod.sol#L189)

### Recommended Mitigation:

```solidity
bytes memory podWithdrawalCreds = _podWithdrawalCredentials();
require(podWithdrawalCreds.length <= 32, "Pod withdrawal credentials must not exceed 32 bytes");
bytes32 podWithdrawalCreds32 = bytes32(uint256(abi.encodePacked(podWithdrawalCreds, bytes32(0))));

```

##

## [L-4] Owner can renounce the ownership 

# Description:
Typically, the contract’s owner is the account that deploys the contract. As a result, the owner is able to perform certain privileged activities.

The StakeHouse Ownable used in this project contract implements renounceOwnership. This can represent a certain risk if the ownership is renounced for any other reason than by design. Renouncing ownership will leave the contract without an owner, thereby removing any functionality that is only available to the owner.

```solidity

File: lib/openzeppelin-contracts-upgradeable/contracts/access/OwnableUpgradeable.sol

66       function renounceOwnership() public virtual onlyOwner {
67           _transferOwnership(address(0));
68:      }


```
https://github.com/code-423n4/2023-04-eigenlayer/blob/398cc428541b91948f717482ec973583c9e76232/lib/openzeppelin-contracts-upgradeable/contracts/access/OwnableUpgradeable.sol#L66-L68

```solidity
FILE: 2023-04-eigenlayer/src/contracts/core/StrategyManager.sol

7: import "@openzeppelin-upgrades/contracts/access/OwnableUpgradeable.sol";

```
[StrategyManager.sol#L7](https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/core/StrategyManager.sol#L7)

```solidity
FILE: 2023-04-eigenlayer/src/contracts/pods/EigenPodManager.sol

9: import "@openzeppelin-upgrades/contracts/access/OwnableUpgradeable.sol";

```
[EigenPodManager.sol#L9](https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/pods/EigenPodManager.sol#L9)

```solidity
FILE: 2023-04-eigenlayer/src/contracts/pods/EigenPod.sol

5: import "@openzeppelin-upgrades/contracts/access/OwnableUpgradeable.sol";

FILE: 2023-04-eigenlayer/src/contracts/pods/DelayedWithdrawalRouter.sol

5: import "@openzeppelin-upgrades/contracts/access/OwnableUpgradeable.sol";

```

### Recommended Mitigation:

We recommend to either reimplement the function to disable it or to clearly specify if it is part of the contract design

##

## [L-5] LOW LEVEL CALLS WITH SOLIDITY VERSION 0.8.14 CAN RESULT IN OPTIMISER BUG

The project contracts in scope are using low level calls with solidity version before 0.8.14 which can result in optimizer bug

https://medium.com/certora/overly-optimistic-optimizer-certora-bug-disclosure-2101e3f7994d

Simliar findings in Code4rena contests for reference:
https://code4rena.com/reports/2022-06-illuminate/#5-low-level-calls-with-solidity-version-0814-can-result-in-optimiser-bug

```solidity
FILE: 2023-04-eigenlayer/src/contracts/libraries/Merkle.sol

107:  if iszero(staticcall(sub(gas(), 2000), 2, 0x00, 0x40, computedHash, 0x20)) {revert(0, 0)}
115:  if iszero(staticcall(sub(gas(), 2000), 2, 0x00, 0x40, computedHash, 0x20)) {revert(0, 0)

```
[Merkle.sol#L107](https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/libraries/Merkle.sol#L107)


### Recommended Mitigation Steps
Consider upgrading to at least solidity v0.8.15.

## [L-6] OUTDATED COMPILER

The pragma version used are: 0.8.12

The minimum required version must be 0.8.19; otherwise, contracts will be affected by the following important bug fixes:

0.8.14:

ABI Encoder: When ABI-encoding values from calldata that contain nested arrays, correctly validate the nested array length against calldatasize() in all cases.
Override Checker: Allow changing data location for parameters only when overriding external functions.

0.8.15

Code Generation: Avoid writing dirty bytes to storage when copying bytes arrays.
Yul Optimizer: Keep all memory side-effects of inline assembly blocks.

0.8.16

Code Generation: Fix data corruption that affected ABI-encoding of calldata values represented by tuples: structs at any nesting level; argument lists of external functions, events and errors; return value lists of external functions. The 32 leading bytes of the first dynamically-encoded value in the tuple would get zeroed when the last component contained a statically-encoded array.

0.8.17
Yul Optimizer: Prevent the incorrect removal of storage writes before calls to Yul functions that conditionally terminate the external EVM call.
Apart from these, there are several minor bug fixes and improvements

```solidity
FILE: 2023-04-eigenlayer/src/contracts/core/StrategyManager.sol

2: pragma solidity =0.8.12;

FILE: 2023-04-eigenlayer/src/contracts/strategies/StrategyBase.sol

2: pragma solidity =0.8.12;

FILE: 2023-04-eigenlayer/src/contracts/pods/EigenPod.sol

2: pragma solidity =0.8.12;

FILE: 2023-04-eigenlayer/src/contracts/pods/EigenPodManager.sol

2: pragma solidity =0.8.12;

FILE: 2023-04-eigenlayer/src/contracts/libraries/BeaconChainProofs.sol

3: pragma solidity =0.8.12;

FILE: 2023-04-eigenlayer/src/contracts/pods/DelayedWithdrawalRouter.sol

2: pragma solidity =0.8.12;

```
##

## [L-7] Lack of Sanity/Threshold/Limit Checks

Devoid of sanity/threshold/limit checks, critical parameters can be configured to invalid values, causing a variety of issues and breaking expected interactions within/between contracts. Consider adding proper uint256 validation as well as zero address checks for critical changes. A worst case scenario would render the contract needing to be re-deployed in the event of human/accidental errors that involve value assignments to immutable variables. If the validation procedure is unclear or too complex to implement on-chain, document the potential issues that could produce invalid values

```solidity
FILE: 2023-04-eigenlayer/src/contracts/core/StrategyManager.sol

function initialize(address initialOwner, address initialStrategyWhitelister, IPauserRegistry _pauserRegistry, uint256 initialPausedStatus, uint256 _withdrawalDelayBlocks)
        external
        initializer
    {
        DOMAIN_SEPARATOR = keccak256(abi.encode(DOMAIN_TYPEHASH, keccak256(bytes("EigenLayer")), ORIGINAL_CHAIN_ID, address(this)));
        _initializePauser(_pauserRegistry, initialPausedStatus);
        _transferOwnership(initialOwner);
        _setStrategyWhitelister(initialStrategyWhitelister);
        _setWithdrawalDelayBlocks(_withdrawalDelayBlocks);
    }

582: function setWithdrawalDelayBlocks(uint256 _withdrawalDelayBlocks) external onlyOwner {
        _setWithdrawalDelayBlocks(_withdrawalDelayBlocks);
    }

```
[StrategyManager.sol#L146-L155](https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/core/StrategyManager.sol#L146-L155)

https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/core/StrategyManager.sol#L655-L672


```solidity
FILE: 2023-04-eigenlayer/src/contracts/pods/EigenPodManager.sol

127: function restakeBeaconChainETH(address podOwner, uint256 amount) external onlyEigenPod(podOwner) {
        strategyManager.depositBeaconChainETH(podOwner, amount);
        emit BeaconChainETHDeposited(podOwner, amount);
    }

139: function recordOvercommittedBeaconChainETH(address podOwner, uint256 beaconChainETHStrategyIndex, uint256 amount) external onlyEigenPod(podOwner) {
        strategyManager.recordOvercommittedBeaconChainETH(podOwner, beaconChainETHStrategyIndex, amount);
    }

```
[EigenPodManager.sol#L127-L130](https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/pods/EigenPodManager.sol#L127-L130)

##

## [L-8] Function Calls in Loop Could Lead to Denial of Service

Function calls made in unbounded loop are error-prone with potential resource exhaustion as it can trap the contract due to the gas limitations or failed transactions


```solidity

        for (uint256 i = 0; i < strategiesToWhitelistLength;) {
            // change storage and emit event only if strategy is not already in whitelist
            if (!strategyIsWhitelistedForDeposit[strategiesToWhitelist[i]]) {
                strategyIsWhitelistedForDeposit[strategiesToWhitelist[i]] = true;
                emit StrategyAddedToDepositWhitelist(strategiesToWhitelist[i]);
            }
            unchecked {
                ++i;
            }


```
[StrategyManager.sol#L594-L602](https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/core/StrategyManager.sol#L594-L602)

https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/core/StrategyManager.sol#L609-L617

https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/core/StrategyManager.sol#L466-L468

https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/core/StrategyManager.sol#L498-L505




##

## [L-9] Project Upgrade and Stop Scenario should be

At the start of the project, the system may need to be stopped or upgraded, I suggest you have a script beforehand and add it to the documentation. This can also be called an ” EMERGENCY STOP (CIRCUIT BREAKER) PATTERN “.

https://github.com/maxwoe/solidity_patterns/blob/master/security/EmergencyStop.sol


##

## [L-10] Front running attacks by the onlyOwner


```solidity
FILE: 2023-04-eigenlayer/src/contracts/core/StrategyManager.sol

587: function setStrategyWhitelister(address newStrategyWhitelister) external onlyOwner {
        _setStrategyWhitelister(newStrategyWhitelister);
    }

```
[StrategyManager.sol#L587-L589](https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/core/StrategyManager.sol#L587-L589)


##

## [L-11] Even with the onlyOwner or owner_only modifier, it is best practice to use the re-entrancy pattern

It's still good practice to apply the reentry model as a precautionary measure in case the code is changed in the future to remove the onlyOwner modifier or the contract is used as a base contract for other contracts.

Using the reentry modifier provides an additional layer of security and ensures that your code is protected from potential reentry attacks regardless of any other security measures you take.

So even if you followed the "check-effects-interactions" pattern correctly, it's still considered good practice to use the reentry modifier


```solidity
FILE: 2023-04-eigenlayer/src/contracts/core/StrategyManager.sol

587: function setStrategyWhitelister(address newStrategyWhitelister) external onlyOwner {
        _setStrategyWhitelister(newStrategyWhitelister);
    }

```
[StrategyManager.sol#L587-L589](https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/core/StrategyManager.sol#L587-L589)


##

## [L-12] Unused Modifiers block

If a modifier is intended to enforce a certain condition on a function, but it's not actually used, this can lead to security issues or unintended behavior in the contract. Therefore, it's a good practice to remove any unused modifiers from the contract to keep the code clean and reduce the risk of bugs or vulnerabilities.

```solidity
FILE: 2023-04-eigenlayer/src/contracts/permissions/Pausable.sol

43: modifier whenNotPaused() {
        require(_paused == 0, "Pausable: contract is paused");
        _;
    }

49: modifier onlyWhenNotPaused(uint8 index) {
        require(!paused(index), "Pausable: index is paused");
        _;
    }

```
[Pausable.sol#L43-L52](https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/permissions/Pausable.sol#L43-L52)

### Recommended Mitigations:

Remove any unused modifiers from the contract to keep the code clean and reduce the risk of bugs or vulnerabilities

##

## [L-13] Vulnerable to cross-chain replay attacks due to static DOMAIN_SEPARATOR/domainSeparator

See this [issue](https://github.com/code-423n4/2021-04-maple-findings/issues/2) from a prior contest for details

```solidity
FILE: 2023-04-eigenlayer/src/contracts/core/StrategyManager.sol

150: DOMAIN_SEPARATOR = keccak256(abi.encode(DOMAIN_TYPEHASH, keccak256(bytes("EigenLayer")), ORIGINAL_CHAIN_ID, address(this)));
276: bytes32 domain_separator = keccak256(abi.encode(DOMAIN_TYPEHASH, keccak256(bytes("EigenLayer")), block.chainid, address(this)));

```
[StrategyManager.sol#L150](https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/core/StrategyManager.sol#L150)

##

## [L-14] Insufficient coverage

Description: The test coverage rate of the project is ~93%. Testing all functions is best practice in terms of security criteria. Due to its capacity, test coverage is expected to be 100%

```
Scoping Details

- What is the overall line coverage percentage provided by your tests?:  95

```

##

## [L-15] Missing Contract-existence Checks Before Low-level Calls

Low-level calls return success if there is no code present at the specified address.

```solidity
FILE: 2023-04-eigenlayer/src/contracts/libraries/Merkle.sol

107:  if iszero(staticcall(sub(gas(), 2000), 2, 0x00, 0x40, computedHash, 0x20)) {revert(0, 0)}
115:  if iszero(staticcall(sub(gas(), 2000), 2, 0x00, 0x40, computedHash, 0x20)) {revert(0, 0)

```
[Merkle.sol#L107](https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/libraries/Merkle.sol#L107)





##

# NON CRITICAL FINDINGS

##

## [NC-1] immutable should be uppercase

```solidity
FILE: 2023-04-eigenlayer/src/contracts/pods/EigenPod.sol

44: IETHPOSDeposit public immutable ethPOS;
47: IDelayedWithdrawalRouter public immutable delayedWithdrawalRouter;
50: IEigenPodManager public immutable eigenPodManager;

```
[EigenPod.sol#L44](https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/pods/EigenPod.sol#L44)

```solidity
FILE: 2023-04-eigenlayer/src/contracts/pods/EigenPodManager.sol

40: IETHPOSDeposit public immutable ethPOS;
43: IBeacon public immutable eigenPodBeacon;
46: IStrategyManager public immutable strategyManager;
49: ISlasher public immutable slasher;
52: IBeaconChainOracle public beaconChainOracle;

```
[EigenPodManager.sol#L40](https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/pods/EigenPodManager.sol#L40)


### Recommended Mitigation

```solidity
FILE: 2023-04-eigenlayer/src/contracts/pods/EigenPod.sol

- 44: IETHPOSDeposit public immutable ethPOS;
+ 44: IETHPOSDeposit public immutable ETHPOS;

```

##

## [NC-2] Missing NATSPEC

https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/core/StrategyManager.sol#L104-L122

https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/pods/EigenPodManager.sol#L76-L93

https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/permissions/PauserRegistry.sol#L17-L29

##

## [NC-3] For functions, follow Solidity standard naming conventions (internal function style rule)

### Description
The above codes don’t follow Solidity’s standard naming convention,

internal and private functions : the mixedCase format starting with an underscore (_mixedCase starting with an underscore)

```solidity
FILE: 2023-04-eigenlayer/src/contracts/libraries/BeaconChainProofs.sol

130: function computePhase0BeaconBlockHeaderRoot(bytes32[NUM_BEACON_BLOCK_HEADER_FIELDS] calldata blockHeaderFields) internal pure returns(bytes32) {

140: function computePhase0BeaconStateRoot(bytes32[NUM_BEACON_STATE_FIELDS] calldata beaconStateFields) internal pure returns(bytes32) {

150: function computePhase0ValidatorRoot(bytes32[NUM_VALIDATOR_FIELDS] calldata validatorFields) internal pure returns(bytes32) {  

160: function computePhase0Eth1DataRoot(bytes32[NUM_ETH1_DATA_FIELDS] calldata eth1DataFields) internal pure returns(bytes32) {  

178: function getBalanceFromBalanceRoot(uint40 validatorIndex, bytes32 balanceRoot) internal pure returns (uint64) {

192: function verifyValidatorFields(
        uint40 validatorIndex,
        bytes32 beaconStateRoot,
        bytes calldata proof, 
        bytes32[] calldata validatorFields
    ) internal view {

221: function verifyValidatorBalance(
        uint40 validatorIndex,
        bytes32 beaconStateRoot,
        bytes calldata proof,
        bytes32 balanceRoot
    ) internal view {

245:  function verifyWithdrawalProofs(
        bytes32 beaconStateRoot,
        WithdrawalProofs calldata proofs,
        bytes32[] calldata withdrawalFields
    ) internal view {


```
[BeaconChainProofs.sol#L130](https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/libraries/BeaconChainProofs.sol#L130)

https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/libraries/Endian.sol#L5-L7

##

## [NC-4] Need Fuzzing test for unchecked

```solidity
FILE: 2023-04-eigenlayer/src/contracts/core/StrategyManager.sol

269:  unchecked {
379:  unchecked {
395:  unchecked {
517:  unchecked {
563:  unchecked {
574:  unchecked {
600:  unchecked {
615:  unchecked {
692:  unchecked {
731:  unchecked {
791:  unchecked {
799:  unchecked {
863:  unchecked {


```
[StrategyManager.sol#L269](https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/core/StrategyManager.sol#L269)

##

## [NC-5] NO SAME VALUE INPUT CONTROL

```solidity
FILE: 2023-04-eigenlayer/src/contracts/core/StrategyManager.sol

587: function setStrategyWhitelister(address newStrategyWhitelister) external onlyOwner {
        _setStrategyWhitelister(newStrategyWhitelister);
    }

```
[StrategyManager.sol#L587-L589](https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/core/StrategyManager.sol#L587-L589)

```solidity

File: src/contracts/pods/DelayedWithdrawalRouter.sol

100      function setWithdrawalDelayBlocks(uint256 newValue) external onlyOwner {
101          _setWithdrawalDelayBlocks(newValue);
102:     }
https://github.com/code-423n4/2023-04-eigenlayer/blob/398cc428541b91948f717482ec973583c9e76232/src/contracts/pods/DelayedWithdrawalRouter.sol#L100-L102

```

```solidity
File: src/contracts/pods/EigenPodManager.sol

161      function updateBeaconChainOracle(IBeaconChainOracle newBeaconChainOracle) external onlyOwner {
162          _updateBeaconChainOracle(newBeaconChainOracle);
163:     }
https://github.com/code-423n4/2023-04-eigenlayer/blob/398cc428541b91948f717482ec973583c9e76232/src/contracts/pods/EigenPodManager.sol#L161-L163

```

```solidity

File: lib/openzeppelin-contracts/contracts/access/Ownable.sol

69       function transferOwnership(address newOwner) public virtual onlyOwner {
70           require(newOwner != address(0), "Ownable: new owner is the zero address");
71           _transferOwnership(newOwner);
72:      }

```
[Ownable.sol#L61-L63](https://github.com/code-423n4/2023-04-eigenlayer/blob/398cc428541b91948f717482ec973583c9e76232/lib/openzeppelin-contracts/contracts/access/Ownable.sol#L61-L63)

##

## [NC-6] According to the syntax rules, use => mapping ( instead of => mapping( using spaces as keyword


```solidity
FILE: 2023-04-eigenlayer/src/contracts/core/StrategyManagerStorage.sol

25:  mapping(address => uint256) public nonces;

FILE: 2023-04-eigenlayer/src/contracts/pods/EigenPodManager.sol

55:  mapping(address => IEigenPod) public ownerToPod;

FILE: 2023-04-eigenlayer/src/contracts/pods/EigenPod.sol

76: mapping(uint40 => VALIDATOR_STATUS) public validatorStatus;

79: mapping(uint40 => mapping(uint64 => bool)) public provenPartialWithdrawal;

FILE: 2023-04-eigenlayer/src/contracts/pods/DelayedWithdrawalRouter.sol

30: mapping(address => UserDelayedWithdrawals) internal _userWithdrawals;

```

https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/core/StrategyManagerStorage.sol#L49-L57

## [NC-7] Use SMTChecker

The highest tier of smart contract behavior assurance is formal mathematical verification. All assertions that are made are guaranteed to be true across all inputs → The quality of your asserts is the quality of your verification

https://twitter.com/0xOwenThurm/status/1614359896350425088?t=dbG9gHFigBX85Rv29lOjIQ&s=19

##


## [NC-8] Assembly Codes Specific – Should Have Comments

Since this is a low level language that is more difficult to parse by readers, include extensive documentation, comments on the rationale behind its use, clearly explaining what each assembly instruction does.

This will make it easier for users to trust the code, for reviewers to validate the code, and for developers to build on or update the code.

Note that using Assembly removes several important security features of Solidity, which can make the code more insecure and more error-prone


```solidity
FILE: 2023-04-eigenlayer/src/contracts/libraries/Merkle.sol

53:  assembly {
61:  assembly {
104: assembly {
112: assembly {

```
[Merkle.sol#L53](https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/libraries/Merkle.sol#L53)

##


##

## [NC-9] Shorthand way to write if / else statement

The normal if / else statement can be refactored in a shorthand way to write it:

Increases readability
Shortens the overall SLOC


```solidity
FILE: 2023-04-eigenlayer/src/contracts/core/StrategyManager.sol

289: if (Address.isContract(staker)) {
            require(IERC1271(staker).isValidSignature(digestHash, signature) == ERC1271_MAGICVALUE,
                "StrategyManager.depositIntoStrategyWithSignature: ERC1271 signature verification failed");
        } else {
            require(ECDSA.recover(digestHash, signature) == staker,
                "StrategyManager.depositIntoStrategyWithSignature: signature not from staker");
        }


567: if (queuedWithdrawal.strategies[i] == beaconChainETHStrategy){
                     //withdraw the beaconChainETH to the recipient
                    _withdrawBeaconChainETH(queuedWithdrawal.depositor, recipient, queuedWithdrawal.shares[i]);
                } else {
                    // tell the strategy to send the appropriate amount of funds to the recipient
                    queuedWithdrawal.strategies[i].withdraw(recipient, tokens[i], queuedWithdrawal.shares[i]);
                }

781: if (queuedWithdrawal.strategies[i] == beaconChainETHStrategy) {

                    // if the strategy is the beaconchaineth strat, then withdraw through the EigenPod flow
                    _withdrawBeaconChainETH(queuedWithdrawal.depositor, msg.sender, queuedWithdrawal.shares[i]);
                } else {
                    // tell the strategy to send the appropriate amount of funds to the depositor
                    queuedWithdrawal.strategies[i].withdraw(
                        msg.sender, tokens[i], queuedWithdrawal.shares[i]
                    );
                }


```


```solidity
FILE: 2023-04-eigenlayer/src/contracts/strategies/StrategyBase.sol

96: if (priorTokenBalance == 0) {
                newShares = amount;
            } else {
                newShares = (amount * totalShares) / priorTokenBalance;
            }

149: if (priorTotalShares == amountShares) {
            amountToSend = _tokenBalance();
        } else {
            amountToSend = (_tokenBalance() * amountShares) / priorTotalShares;
        }

173: if (totalShares == 0) {
            return amountShares;
        } else {
            return (_tokenBalance() * amountShares) / totalShares;
        }

198: if (tokenBalance == 0 || totalShares == 0) {
            return amountUnderlying;
        } else {
            return (amountUnderlying * totalShares) / tokenBalance;
        }


```
[StrategyBase.sol#L96-L100](https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/strategies/StrategyBase.sol#L96-L100)


##

## [NC-10] Don't use named return variables its confusing

```solidity

220: function depositIntoStrategy(IStrategy strategy, IERC20 token, uint256 amount)
        external
        onlyWhenNotPaused(PAUSED_DEPOSITS)
        onlyNotFrozen(msg.sender)
        nonReentrant
        returns (uint256 shares)

```
[StrategyManager.sol#L220-L225)](https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/core/StrategyManager.sol#L220-L225)

https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/core/StrategyManager.sol#L248-L260

##

## [NC-11] Constants should be in uppercase

https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/pods/EigenPodManager.sol#L37

##



## [NC-12] Shorter inheritance list

``solidity
FILE: 2023-04-eigenlayer/src/contracts/core/StrategyManager.sol

26: contract StrategyManager is
    Initializable,
    OwnableUpgradeable,
    ReentrancyGuardUpgradeable,
    Pausable,
    StrategyManagerStorage
{

```
[StrategyManager.sol#L26-L32](https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/core/StrategyManager.sol#L26-L32)

```solidity
FILE: 2023-04-eigenlayer/src/contracts/pods/EigenPodManager.sol

31: contract EigenPodManager is Initializable, OwnableUpgradeable, Pausable, IEigenPodManager, EigenPodPausingConstants {

```
[EigenPodManager.sol#L31](https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/pods/EigenPodManager.sol#L31)

##

## [NC-13] Include return parameters in NatSpec comments

### Context
All Contracts

### Description
It is recommended that Solidity contracts are fully annotated using NatSpec for all public interfaces (everything in the ABI). It is clearly stated in the Solidity official documentation. In complex projects such as Defi, the interpretation of all functions and their arguments and returns is important for code readability and auditability.

https://docs.soliditylang.org/en/v0.8.15/natspec-format.html

### Recommendation
Include return parameters in NatSpec comments

### Recommendation Code Style: (from Uniswap3)

    /// @notice Adds liquidity for the given recipient/tickLower/tickUpper position
    /// @dev The caller of this method receives a callback in the form of IUniswapV3MintCallback#uniswapV3MintCallback
    /// in which they must pay any token0 or token1 owed for the liquidity. The amount of token0/token1 due depends
    /// on tickLower, tickUpper, the amount of liquidity, and the current price.
    /// @param recipient The address for which the liquidity will be created
    /// @param tickLower The lower tick of the position in which to add liquidity
    /// @param tickUpper The upper tick of the position in which to add liquidity
    /// @param amount The amount of liquidity to mint
    /// @param data Any data that should be passed through to the callback
    /// @return amount0 The amount of token0 that was paid to mint the given amount of liquidity. Matches the value in the callback
    /// @return amount1 The amount of token1 that was paid to mint the given amount of liquidity. Matches the value in the callback
    function mint(
        address recipient,
        int24 tickLower,
        int24 tickUpper,
        uint128 amount,
        bytes calldata data
    ) external returns (uint256 amount0, uint256 amount1);

##

## [NC-14] Constants should be defined rather than calculating every time 

Defining constants can make the code more readable and easier to maintain. By giving a name to a constant value, it can make the code more self-explanatory and help other developers understand the purpose and intent of the code more easily

```solidity
FILE: 2023-04-eigenlayer/src/contracts/core/StrategyManagerStorage.sol

17: bytes32 public constant DOMAIN_TYPEHASH =
        keccak256("EIP712Domain(string name,uint256 chainId,address verifyingContract)");

20: bytes32 public constant DEPOSIT_TYPEHASH =
        keccak256("Deposit(address strategy,address token,uint256 amount,uint256 nonce,uint256 expiry)");
```
[StrategyManagerStorage.sol#L17-L18](https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/core/StrategyManagerStorage.sol#L17-L18)

```solidity
FILE: 2023-04-eigenlayer/src/contracts/permissions/Pausable.sol

23: uint256 constant internal PAUSE_ALL = type(uint256).max;

```
[Pausable.sol#L23](https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/permissions/Pausable.sol#L23)

##

## [NC-15] Use constants instead of type(uintx).max

https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/permissions/Pausable.sol#L23

https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/permissions/Pausable.sol#L82-L83

##

## [NC-16] Tokens accidentally sent to the contract cannot be recovered

It can’t be recovered if the tokens accidentally arrive at the contract address, which has happened to many popular projects, so I recommend adding a recovery code to your critical contracts.

Recommended Mitigation Steps
Add this code:

```solidity
 /**
  * @notice Sends ERC20 tokens trapped in contract to external address
  * @dev Onlyowner is allowed to make this function call
  * @param account is the receiving address
  * @param externalToken is the token being sent
  * @param amount is the quantity being sent
  * @return boolean value indicating whether the operation succeeded.
  *
 */
  function rescueERC20(address account, address externalToken, uint256 amount) public onlyOwner returns (bool) {
    IERC20(externalToken).transfer(account, amount);
    return true;
  }
}

```
##

## [NC-17] Add a timelock to critical functions

It is a good practice to give time for users to react and adjust to critical changes. A timelock provides more guarantees and reduces the level of trust required, thus decreasing risk for users. It also indicates that the project is legitimate (less risk of a malicious owner making a sandwich attack on a user). Consider adding a timelock to:

```solidity
FILE: 2023-04-eigenlayer/src/contracts/core/StrategyManager.sol

587: function setStrategyWhitelister(address newStrategyWhitelister) external onlyOwner {
        _setStrategyWhitelister(newStrategyWhitelister);
    }

```
[StrategyManager.sol#L587-L589](https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/core/StrategyManager.sol#L587-L589)

##

## [NC-18] NatSpec comments should be increased in contracts

Context
All Contracts

Description:
It is recommended that Solidity contracts are fully annotated using NatSpec for all public interfaces (everything in the ABI). It is clearly stated in the Solidity official documentation.

In complex projects such as Defi, the interpretation of all functions and their arguments and returns is important for code readability and auditability. https://docs.soliditylang.org/en/v0.8.15/natspec-format.html

Recommendation
NatSpec comments should be increased in contracts.

##

## [NC-19] Not recommended to use numbers in function names 

Tt can make the function name less descriptive and harder to understand. It's better to use a descriptive title that accurately reflects the purpose of the function. This can make the code more readable and understandable for others who may be reviewing or working with the code

https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/libraries/BeaconChainProofs.sol#L160

https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/libraries/BeaconChainProofs.sol#L150

https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/libraries/BeaconChainProofs.sol#L140

https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/libraries/BeaconChainProofs.sol#L130

https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/libraries/Merkle.sol#L80

https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/libraries/Merkle.sol#L99

##

## [NC-20] For critical changes emit both old and new values 

```solidity
FILE: 2023-04-eigenlayer/src/contracts/pods/EigenPodManager.sol

186: function _updateBeaconChainOracle(IBeaconChainOracle newBeaconChainOracle) internal {
        beaconChainOracle = newBeaconChainOracle;
        emit BeaconOracleUpdated(address(newBeaconChainOracle));
    }

```
[EigenPodManager.sol#L186-L189](https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/pods/EigenPodManager.sol#L186-L189)



















[L‑01]	Events are missing sender information	6
[L‑02]	The owner is a single point of failure and a centralization risk	27
[L‑03]	Use Ownable2Step's transfer function rather than Ownable's for transfers of ownership	2
[L‑04]	Unsafe downcast	3
[L‑05]	Loss of precision	4
[L‑06]	Upgradeable contract not initialized	6
[L‑07]	Array lengths not checked	3
[L‑08]	Missing checks for address(0x0) when assigning values to address state variables	1
[L‑09]	abi.encodePacked() should not be used with dynamic types when passing the result to a hash function such as keccak256()	2
[L‑10]	Use Ownable2Step rather than Ownable	3

[N‑01]	Constants in comparisons should appear on the left side	118
[N‑02]	Variables need not be initialized to zero	15
[N‑03]	Imports could be organized more systematically	13
[N‑04]	Large numeric literals should use underscores for readability	14
[N‑05]	Upgradeable contract is missing a __gap[50] storage variable to allow for new storage variables in later versions	1
[N‑06]	Import declarations should import specific identifiers, rather than the whole file	71
[N‑07]	Missing initializer modifier on constructor	1
[N‑08]	Unused file	1
[N‑09]	The nonReentrant modifier should occur before all other modifiers	9
[N‑10]	Adding a return statement when the function defines a named return variable, is redundant	2
[N‑11]	public functions not called by the contract should be declared external instead	1
[N‑12]	constants should be defined rather than using magic numbers	56
[N‑13]	Cast is more restrictive than the type of the variable being assigned	1
[N‑14]	Numeric values having to do with time should use time units for readability	1
[N‑15]	Use a more recent version of solidity	3
[N‑16]	Expressions for constant values such as a call to keccak256(), should use immutable rather than constant	2
[N‑17]	Constant redefined elsewhere	6
[N‑18]	Inconsistent spacing in comments	24
[N‑19]	Lines are too long	23
[N‑20]	Variable names that consist of all capital letters should be reserved for constant/immutable variables	2
[N‑21]	Typos	21
[N‑22]	Misplaced SPDX identifier	1
[N‑23]	File is missing NatSpec	1
[N‑24]	NatSpec is incomplete	41
[N‑25]	Event is missing indexed fields	23
[N‑26]	Consider using delete rather than assigning zero to clear values	1
[N‑27]	Avoid the use of sensitive terms	31
[N‑28]	Contracts should have full test coverage	1
[N‑29]	Large or complicated code bases should implement invariant tests	1
[N‑30]	Function ordering does not follow the Solidity style guide	7
[N‑31]	Contract does not follow the Solidity style guide's suggested layout ordering	7
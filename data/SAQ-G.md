## Summary

### Gas Optimization

| no | Issue |Instances||
|-|:-|:-:|:-:|
| [G-01] | State variables should be cached in stack variables rather than re-reading them from storage | 23 | - |
| [G-02] | Multiple accesses of a mapping/array should use a local variable cache | 22 | - |
| [G-03] | internal functions only called once can be inlined to save gas | 13 | - |
| [G-04] 0| Add unchecked {} for subtractions where the operands cannot underflow because of a previous require() or if-statement | 16 | - |
| [G-05] | Using private rather than public for constants, saves gas | 12 | - |
| [G-06] | USE A MORE RECENT VERSION OF SOLIDITY | 24 | - |
| [G-07] | PUBLIC FUNCTIONS TO EXTERNAL  | 5 | - |
| [G-08] | USING FIXED BYTES IS CHEAPER THAN USING STRING | 2 | - |
| [G-09] | EXPRESSIONS FOR CONSTANT VALUES SUCH AS A CALL TO KECCAK256(),SHOULD USE IMMUTABLE RATHER THAN CONSTANT | 2 |- |
| [G-10] | NOT USING THE NAMED RETURN VARIABLES WHEN A FUNCTION RETURNS, WASTES DEPLOYMENT GAS | 4 | - |
| [G-11] | USING CALLDATA INSTEAD OF MEMORY FOR READ-ONLY ARGUMENTS IN EXTERNAL FUNCTIONS SAVES GAS | 7 | - |
| [G-12] | INSTEAD OF CALCULATING A STATEVAR WITH KECCAKE256() EVERY TIME THE CONTRACT IS MADE PRE CALCULATE THEM BEFORE AND ONLY GIVE THE RESULT TO A CONSTANT | 2 | - |
| [G-13] | Remove the initializer modifier | 4 | - |
| [G-14] | Duplicated require()/if() checks should be refactored to a modifier or function | 3 | - |
| [G-15] | Use nested if and, avoid multiple check combinations | 2 | - |
| [G-16] | A modifier used only once and not being inherited should be inlined to save gas | 1 | - |
| [G-17] | Use selfbalance() instead of address(this).balance | 1 | - |
| [G-18] | abi.encode() is less efficient than abi.encodepacked() | 6 | - |
| [G-19] | USE ASSEMBLY TO CHECK FOR ADDRESS(0) | 7 | - |


## Gas Optimizations  

## [G-1] State variables should be cached in stack variables rather than re-reading them from storage

### Details

The instances below point to the second+ access of a state variable within a function. Caching of a state variable replaces each Gwarmaccess (100 gas) with a much cheaper stack read. Other less obvious fixes/optimizations include having local memory caches of state variable structs, or having local caches of state variable contracts/addresses.

```solidity
file: /contracts/pods/EigenPodManager.sol

187    beaconChainOracle = newBeaconChainOracle;

```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPodManager.sol#L187


```solidity
File:   /contracts/pods/EigenPodManager.sol

181     ownerToPod[msg.sender] = pod;

```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPodManager.sol#L181


```solidity
file: /contracts/pods/EigenPod.sol

154      podOwner = _podOwner;

```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPod.sol#L154


```solidity
file: /contracts/pods/EigenPod.sol

455     mostRecentWithdrawalBlockNumber = uint32(block.number);

```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPod.sol#L455


```solidity
file: /contracts/pods/EigenPod.sol

377     restakedExecutionLayerGwei += REQUIRED_BALANCE_GWEI;

380     restakedExecutionLayerGwei += withdrawalAmountGwei;

391     restakedExecutionLayerGwei += REQUIRED_BALANCE_GWEI;

399     restakedExecutionLayerGwei += withdrawalAmountGwei;

444     restakedExecutionLayerGwei -= uint64(amountWei / GWEI_TO_WEI);

```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPod.sol#


```solidity
file: /contracts/pods/EigenPod.sol

219      hasRestaked = true;

```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPod.sol#L219



```solidity
file: /contracts/pods/EigenPod.sol

412      validatorStatus[validatorIndex] = VALIDATOR_STATUS.WITHDRAWN;

```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPod.sol#L412


```solidity
file: /contracts/pods/EigenPod.sol

425       provenPartialWithdrawal[validatorIndex][withdrawalHappenedSlot] = true;

```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPod.sol#L425


```solidity
file: /contracts/pods/DelayedWithdrawalRouter.sol
168     emit WithdrawalDelayBlocksSet(withdrawalDelayBlocks, newValue);
        
169     withdrawalDelayBlocks = newValue;

```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/DelayedWithdrawalRouter.sol#L168-L169


```solidity
file: /contracts/permissions/Pausable.sol

62       pauserRegistry = _pauserRegistry;

```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/permissions/Pausable.sol#L62


```solidity 
file: /contracts/permissions/Pausable.sol
60       _paused = initPausedStatus;

74       _paused = newPausedStatus;

82       _paused = type(uint256).max;

95       _paused = newPausedStatus;

``` 
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/permissions/Pausable.sol


```solidity
file: /contracts/permissions/PauserRegistry.sol

43       emit PauserChanged(pauser, newPauser);

44        pauser = newPauser;

```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/permissions/PauserRegistry.sol#L43-L44


```solidity
file: /contracts/permissions/PauserRegistry.sol

49        emit UnpauserChanged(unpauser, newUnpauser);

50        unpauser = newUnpauser;

```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/permissions/PauserRegistry.sol#L49-L50

---------------------------------------------------------------------------------------------------------------------------


## [G-2] Multiple accesses of a mapping/array should use a local variable cache

### Details


The instances below point to the second+ access of a value inside a mapping/array, within a function. Caching a mapping's value in a local storage or calldata variable when the value is accessed multiple times, saves ~42 gas per access due to not having to recalculate the key's keccak256 hash (Gkeccak256 - 30 gas) and that calculation's associated stack operations. Caching an array's struct avoids recalculating the array offsets into memory/calldata


```solidity
file: /contracts/core/StrategyManager.sol

201       strategies[0] = beaconChainETHStrategy;


        require(queuedWithdrawal.withdrawalStartBlock + withdrawalDelayBlocks <= block.number 
762               || queuedWithdrawal.strategies[0] == beaconChainETHStrategy,
            "StrategyManager.completeQueuedWithdrawal: withdrawalDelayBlocks period has not yet passed"
        );


```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L201

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L762


```solidity
file: /contracts/core/StrategyManager.sol

415       withdrawalRootPending[withdrawalRoot] = true;

549       withdrawalRootPending[withdrawalRoot],

554       withdrawalRootPending[withdrawalRoot] = false;

751       withdrawalRootPending[withdrawalRoot],

772       withdrawalRootPending[withdrawalRoot] = false;


```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L415

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L549

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L554

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L751

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L772



```solidity
file: /contracts/core/StrategyManager.sol

635      if (stakerStrategyShares[depositor][strategy] == 0) {

644      stakerStrategyShares[depositor][strategy] += shares;    

688      uint256 userShares = stakerStrategyShares[depositor][strategy];

697      stakerStrategyShares[depositor][strategy] = userShares;


```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L635

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L644

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L688

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L697


```solidity
file: /contracts/pods/EigenPodManager.sol

67        require(address(ownerToPod[podOwner]) == msg.sender, "EigenPodManager.onlyEigenPod: not a pod");

153       ownerToPod[podOwner].withdrawRestakedBeaconChainETH(recipient, amount);

194       IEigenPod pod = ownerToPod[podOwner];

211       return address(ownerToPod[podOwner]) != address(0);

```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPodManager.sol#L67

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPodManager.sol#L153

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPodManager.sol#L194

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPodManager.sol#L211


```solidity
file: /contracts/pods/EigenPod.sol

186       require(validatorStatus[validatorIndex] == VALIDATOR_STATUS.INACTIVE,

215       validatorStatus[validatorIndex] = VALIDATOR_STATUS.ACTIVE;

252       require(validatorStatus[validatorIndex] == VALIDATOR_STATUS.ACTIVE, "EigenPod.verifyOvercommittedStake: Validator not active");

288       validatorStatus[validatorIndex] = VALIDATOR_STATUS.OVERCOMMITTED;

333       require(validatorStatus[validatorIndex] != VALIDATOR_STATUS.INACTIV

355       _processFullWithdrawal(withdrawalAmountGwei, validatorIndex, beaconChainETHStrategyIndex, podOwner, validatorStatus[validatorIndex]);

412         validatorStatus[validatorIndex] = VALIDATOR_STATUS.WITHDRAWN;

```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPod.sol#L186

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPod.sol#L215

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPod.sol#L252

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPod.sol#L288

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPod.sol#L333

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPod.sol#L355

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPod.sol#L412

---------------------------------------------------------------------------------------------------------------------------

## [G-3]  internal functions only called once can be inlined to save gas

Not inlining costs 20 to 40 gas because of two extra JUMP instructions and additional stack operations needed for function calls.


```solidity
file: /contracts/core/StrategyManager.sol

839     function _setWithdrawalDelayBlocks(uint256 _withdrawalDelayBlocks) internal {


```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L839


```solidity
file: /contracts/permissions/Pausable.sol

55       function _initializePauser(IPauserRegistry _pauserRegistry, uint256 initPausedStatus) internal {

```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/permissions/Pausable.sol#L55


```solidity
file: /contracts/libraries/BeaconChainProofs.sol

130       function computePhase0BeaconBlockHeaderRoot(bytes32[NUM_BEACON_BLOCK_HEADER_FIELDS] calldata blockHeaderFields) internal pure returns(bytes32) {

140      function computePhase0BeaconStateRoot(bytes32[NUM_BEACON_STATE_FIELDS] calldata beaconStateFields) internal pure returns(bytes32) {

150      function computePhase0ValidatorRoot(bytes32[NUM_VALIDATOR_FIELDS] calldata validatorFields) internal pure returns(bytes32) {  

160       function computePhase0Eth1DataRoot(bytes32[NUM_ETH1_DATA_FIELDS] calldata eth1DataFields) internal pure returns(bytes32) {  

178     function getBalanceFromBalanceRoot(uint40 validatorIndex, bytes32 balanceRoot) internal pure returns (uint64) {

```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/libraries/BeaconChainProofs.sol#L130

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/libraries/BeaconChainProofs.sol#L140

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/libraries/BeaconChainProofs.sol#L150

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/libraries/BeaconChainProofs.sol#L160

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/libraries/BeaconChainProofs.sol#L178


```solidity
file: /contracts/libraries/Merkle.sol

29   function verifyInclusionKeccak(
        bytes memory proof,
        bytes32 root,
        bytes32 leaf,
        uint256 index
    ) internal pure returns (bool) {

48      function processInclusionProofKeccak(bytes memory proof, bytes32 leaf, uint256 index) internal pure returns (bytes32) {

80     function verifyInclusionSha256(
        bytes memory proof,
        bytes32 root,
        bytes32 leaf,
        uint256 index
    ) internal view returns (bool) {

99       function processInclusionProofSha256(bytes memory proof, bytes32 leaf, uint256 index) internal view returns (bytes32) {        

129    function merkleizeSha256(
        bytes32[] memory leaves
    ) internal pure returns (bytes32) {

```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/libraries/Merkle.sol#L29

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/libraries/Merkle.sol#L48

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/libraries/Merkle.sol#L80

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/libraries/Merkle.sol#L99

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/libraries/Merkle.sol#L129


```solidity
file: /contracts/libraries/Endian.sol

5     function fromLittleEndianUint64(
         bytes32 lenum
    ) internal pure returns (uint64 n) {

```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/libraries/Endian.sol#L5

---------------------------------------------------------------------------------------------------------------------------


## [G-4] Add unchecked {} for subtractions where the operands cannot underflow because of a previous require() or if-statement


```solidity
file: /contracts/core/StrategyManager.sol

829      beaconChainETHSharesToDecrementOnWithdrawal[staker] = (amountToDecrement - amount);


```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L829

---------------------------------------------------------------------------------------------------------------------------

## [G-5] Using private rather than public for constants, saves gas

If needed, the values can be read from the verified contract source code, or if there are multiple values there can be a single getter function that returns a tuple of the values of all currently-public constants. Saves 3406-3606 gas in deployment gas due to the compiler not having to create non-payable getter functions for deployment calldata, not having to store the bytes of the value outside of where it's used, and not adding another entry to the method ID table


```solidity
file: /contracts/core/StrategyManagerStorage.sol

31    IDelegationManager public immutable delegation;

32    IEigenPodManager public immutable eigenPodManager;

33    ISlasher public immutable slasher;


70       IStrategy public constant beaconChainETHStrategy = IStrategy(0xbeaC0eeEeeeeEEeEeEEEEeeEEeEeeeEeeEEBEaC0);


```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManagerStorage.sol#L70

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManagerStorage.sol#L31

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManagerStorage.sol#L32

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManagerStorage.sol#L33


```solidity
file: /contracts/pods/EigenPod.sol

44    IETHPOSDeposit public immutable ethPOS;

47    IDelayedWithdrawalRouter public immutable delayedWithdrawalRouter;

50     IEigenPodManager public immutable eigenPodManager;

```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPod.sol#L44

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPod.sol#L47

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPod.sol#L50


```solidity
file: /contracts/pods/EigenPodManager.sol

40    IETHPOSDeposit public immutable ethPOS;
    
  
43   IBeacon public immutable eigenPodBeacon;

  
46    IStrategyManager public immutable strategyManager;

  
49    ISlasher public immutable slasher;


```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPodManager.sol#L40-L49


```solidity
file: /contracts/pods/DelayedWithdrawalRouter.sol

27     IEigenPodManager public immutable eigenPodManager;

```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/DelayedWithdrawalRouter.sol#L27

---------------------------------------------------------------------------------------------------------------------------

## [G-6] USE A MORE RECENT VERSION OF SOLIDITY


```solidity
file: /contracts/core/StrategyManager.sol

2    pragma solidity =0.8.12;

```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L2


```solidity
file: /contracts/core/StrategyManagerStorage.sol

2     pragma solidity =0.8.12;

```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManagerStorage.sol#L2


```solidity
file: /contracts/pods/EigenPodManager.sol

2     pragma solidity =0.8.12;

```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPodManager.sol#L2

```solidity
file: /contracts/pods/EigenPodPausingConstants.sol

2     pragma solidity =0.8.12;

```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPodPausingConstants.sol#L2


```solidity
file: /contracts/pods/EigenPod.sol

2     pragma solidity =0.8.12;

```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPod.sol#L2

```solidity
file: /contracts/pods/EigenPodPausingConstants.sol

2     pragma solidity =0.8.12;

```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPodPausingConstants.sol#L2


```solidity
file: /contracts/pods/DelayedWithdrawalRouter.sol

2     pragma solidity =0.8.12;

```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/DelayedWithdrawalRouter.sol#L2



```solidity
file: /contracts/permissions/Pausable.sol

3     pragma solidity =0.8.12;

```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/permissions/Pausable.sol#L3


```solidity
file: /contracts/permissions/PauserRegistry.sol

2     pragma solidity =0.8.12;

```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/permissions/PauserRegistry.sol#L2



```solidity
file: /contracts/libraries/BeaconChainProofs.sol

3     pragma solidity =0.8.12;

```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/libraries/BeaconChainProofs.sol#L3



```solidity
file: /contracts/libraries/Merkle.sol

4     pragma solidity =0.8.12;

```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/libraries/Merkle.sol#L4


```solidity
file: /contracts/libraries/Endian.sol

2     pragma solidity =0.8.12;

```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/libraries/Endian.sol#L2


```solidity
file: /contracts/interfaces/ISlasher.sol

2     pragma solidity =0.8.12;

```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/interfaces/ISlasher.sol#L2


```solidity
file: /contracts/interfaces/IPausable.sol

2     pragma solidity =0.8.12;

```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/interfaces/IPausable.sol#L2


```solidity
file: /contracts/interfaces/IBeaconChainOracle.sol

2     pragma solidity =0.8.12;

```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/interfaces/IBeaconChainOracle.sol#L2


```solidity
file: /contracts/interfaces/IStrategy.sol

2     pragma solidity =0.8.12;

```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/interfaces/IStrategy.sol#L2


```solidity
file: /contracts/interfaces/IStrategyManager.sol

2     pragma solidity =0.8.12;

```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/interfaces/IStrategyManager.sol#L2


```solidity
file: /contracts/interfaces/IETHPOSDeposit.sol

12     pragma solidity =0.8.12;

```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/interfaces/IETHPOSDeposit.sol#L12


```solidity
file: /contracts/interfaces/IDelayedWithdrawalRouter.sol

2     pragma solidity =0.8.12;

```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/interfaces/IDelayedWithdrawalRouter.sol#L2


```solidity
file: /contracts/interfaces/IEigenPod.sol

2     pragma solidity =0.8.12;

```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/interfaces/IEigenPod.sol#L2


```solidity
file: /contracts/interfaces/IEigenPodManager.sol

2     pragma solidity =0.8.12;

```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/interfaces/IEigenPodManager.sol#L2


```solidity
file: /contracts/interfaces/IPauserRegistry.sol

2     pragma solidity =0.8.12;

```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/interfaces/IPauserRegistry.sol#L2


```solidity
file: /contracts/interfaces/IDelegationManager.sol

2     pragma solidity =0.8.12;

```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/interfaces/IDelegationManager.sol#L2


```solidity
file: /contracts/interfaces/IServiceManager.sol

2     pragma solidity =0.8.12;

```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/interfaces/IServiceManager.sol#L2

---------------------------------------------------------------------------------------------------------------------------

## [G-7] PUBLIC FUNCTIONS TO EXTERNAL  


```solidity
file: /contracts/core/StrategyManager.sol

876    function calculateWithdrawalRoot(QueuedWithdrawal memory queuedWithdrawal) public pure returns (bytes32) {

```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L876


```solidity
file: /contracts/pods/EigenPodManager.sol

193      function getPod(address podOwner) public view returns (IEigenPod) {

```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPodManager.sol#L193



```solidity
file: /contracts/pods/EigenPodManager.sol

210     function hasPod(address podOwner) public view returns (bool) {

```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPodManager.sol#L210


```solidity
file: /contracts/permissions/Pausable.sol

100    function paused() public view virtual returns (uint256) {

105    function paused(uint8 index) public view virtual returns (bool) {

```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/permissions/Pausable.sol#L100

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/permissions/Pausable.sol#L105

---------------------------------------------------------------------------------------------------------------------------

## [G-8] USING FIXED BYTES IS CHEAPER THAN USING STRING


As a rule of thumb, use bytes for arbitrary-length raw byte data and string for arbitrary-length string (UTF-8) data.
If you can limit the length to a certain number of bytes, always use one of bytes1 to bytes32 because they are much cheaper.
  
```solidity
file: /contracts/core/StrategyManagerStorage.sol

18     keccak256("EIP712Domain(string name,uint256 chainId,address verifyingContract)");

```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManagerStorage.sol#L18


```solidity
file: /contracts/interfaces/IStrategy.sol

82      function explanation() external view returns (string memory);

```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/interfaces/IStrategy.sol#L82

---------------------------------------------------------------------------------------------------------------------------

## [G-9] EXPRESSIONS FOR CONSTANT VALUES SUCH AS A CALL TO KECCAK256(), SHOULD USE IMMUTABLE RATHER THAN CONSTANT


```solidity
file: /contracts/core/StrategyManagerStorage.sol

17     bytes32 public constant DOMAIN_TYPEHASH =
18          keccak256("EIP712Domain(string name,uint256 chainId,address verifyingContract)");

```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManagerStorage.sol#L17-L18


```solidity
file: /contracts/core/StrategyManagerStorage.sol

20      bytes32 public constant DEPOSIT_TYPEHASH =
21           keccak256("Deposit(address strategy,address token,uint256 amount,uint256 nonce,uint256 expiry)");

```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManagerStorage.sol#L20-L21

---------------------------------------------------------------------------------------------------------------------------

## [G-10] NOT USING THE NAMED RETURN VARIABLES WHEN A FUNCTION RETURNS, WASTES DEPLOYMENT GAS


```solidity
file: /contracts/core/StrategyManager.sol
        
225       returns (uint256 shares)


260        returns (uint256 shares)

658       returns (uint256 shares)

```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L225

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L260   

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L658


```solidity
file: /contracts/interfaces/IStrategyManager.sol

91         returns (uint256 shares);

```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/interfaces/IStrategyManager.sol#L91

---------------------------------------------------------------------------------------------------------------------------

## [G-11] USING CALLDATA INSTEAD OF MEMORY FOR READ-ONLY ARGUMENTS IN EXTERNAL FUNCTIONS SAVES GAS

```solidity
file: /contracts/core/StrategyManager.sol

876     function calculateWithdrawalRoot(QueuedWithdrawal memory queuedWithdrawal) public pure returns (bytes32) {

```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L876


```solidity
file: /contracts/interfaces/IBeaconChainOracle.sol

46       function addOracleSigners(address[] memory _oracleSigners) external;

53       function removeOracleSigners(address[] memory _oracleSigners) external;

```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/interfaces/IBeaconChainOracle.sol#L46

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/interfaces/IBeaconChainOracle.sol#L53


```solidity
file: /contracts/interfaces/IStrategyManager.sol

82      function depositIntoStrategyWithSignature(
        IStrategy strategy,
        IERC20 token,
        uint256 amount,
        address staker,
        uint256 expiry,
        bytes memory signature
    )
        external
        returns (uint256 shares);

```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/interfaces/IStrategyManager.sol#L82-L91


```solidity
file: /contracts/interfaces/IStrategyManager.sol

202     function calculateWithdrawalRoot(
          QueuedWithdrawal memory queuedWithdrawal
    )
        external
        pure
        returns (bytes32);

```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/interfaces/IStrategyManager.sol#L202-L207


```solidity
file: /contracts/interfaces/IEigenPod.sol

98     function verifyWithdrawalCredentialsAndBalance(
        uint64 oracleBlockNumber,
        uint40 validatorIndex,
        BeaconChainProofs.ValidatorFieldsAndBalanceProofs memory proofs,
        bytes32[] calldata validatorFields
    ) external;

```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/interfaces/IEigenPod.sol#L98-L103


```solidity
file: /contracts/interfaces/IDelegationManager.sol

38     function delegateToBySignature(address staker, address operator, uint256 expiry, bytes memory signature) external;

```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/interfaces/IDelegationManager.sol#L38

---------------------------------------------------------------------------------------------------------------------------

## [G-12] INSTEAD OF CALCULATING A STATEVAR WITH KECCAKE256() EVERY TIME THE CONTRACT IS MADE PRE CALCULATE THEM BEFORE AND ONLY GIVE THE RESULT TO A CONSTANT


```solidity
file: /contracts/core/StrategyManagerStorage.sol

17       bytes32 public constant DOMAIN_TYPEHASH =
18            keccak256("EIP712Domain(string name,uint256 chainId,address verifyingContract)");

``` 
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManagerStorage.sol#L17-L18


```solidity
file: /contracts/core/StrategyManagerStorage.sol

20       bytes32 public constant DEPOSIT_TYPEHASH =
21            keccak256("Deposit(address strategy,address token,uint256 amount,uint256 nonce,uint256 expiry)");

```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManagerStorage.sol#L17-L18

---------------------------------------------------------------------------------------------------------------------------

## [G-13] Remove the initializer modifier

If we can just ensure that the initialize() function could only be called from within the constructor, we shouldn't need to worry about it getting called again.


```solidity
file: /contracts/core/StrategyManager.sol

146        function initialize(address initialOwner, address initialStrategyWhitelister,  IPauserRegistry_pauserRegistry,uint256 initialPausedStatus, uint256 _withdrawalDelayBlocks)
        external
        initializer
    {

```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L146-L148


```solidity
file: /contracts/pods/EigenPodManager.sol

84    function initialize(
        IBeaconChainOracle _beaconChainOracle,
        address initialOwner,
        IPauserRegistry _pauserRegistry,
        uint256 _initPausedStatus
    ) external initializer {

```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPodManager.sol#L84-L89


```solidity
file: /contracts/pods/DelayedWithdrawalRouter.sol

49       function initialize(address initOwner, IPauserRegistry _pauserRegistry, uint256 initPausedStatus, uint256 _withdrawalDelayBlocks) external initializer {

```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/DelayedWithdrawalRouter.sol#L49


```solidity
file: /contracts/pods/EigenPod.sol

152       function initialize(address _podOwner) external initializer {

```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPod.sol#L152

---------------------------------------------------------------------------------------------------------------------------

## [G-14] Duplicated require()/if() checks should be refactored to a modifier or function


```solidity
file: /contracts/core/StrategyManager.sol

359       if (strategies[i] == beaconChainETHStrategy) {

507       if (strategies[i] == beaconChainETHStrategy) {

```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L359


```solidity
file: /contracts/core/StrategyManager.sol
             
567       if (queuedWithdrawal.strategies[i] == beaconChainETHStrategy){

781       if (queuedWithdrawal.strategies[i] == beaconChainETHStrategy){

```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L567

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L781


```solidity
file: /contracts/pods/EigenPod.sol

373      if (withdrawalAmountGwei >= REQUIRED_BALANCE_GWEI) {

387      if (withdrawalAmountGwei >= REQUIRED_BALANCE_GWEI) {

```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPod.sol#L373

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPod.sol#L387

---------------------------------------------------------------------------------------------------------------------------

## [G-15] Use nested if and, avoid multiple check combinations


Using nested is cheaper than using && multiple check combinations. There are more advantages, such as easier to read code and better coverage reports.


```solidity
file: /contracts/core/StrategyManager.sol

422     if (undelegateIfPossible && stakerStrategyList[msg.sender].length == 0) {

562     if (indicesToSkipIndex < indicesToSkip.length && indicesToSkip[indicesToSkipIndex] == i) {        

```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L422

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L562


## [G-16] A modifier used only once and not being inherited should be inlined to save gas 


```solidity
file: /contracts/permissions/Pausable.sol

37    modifier onlyUnpauser() {

```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/permissions/Pausable.sol#L37


## [G-17] Use selfbalance() instead of address(this).balance

```solidity
file : /contracts/pods/EigenPod.sol

456       _sendETH(podOwner, address(this).balance);

```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPod.sol#L456

---------------------------------------------------------------------------------------------------------------------------

## [G-18] abi.encode() is less efficient than  >  abi.encodepacked()


```solidity
file: /contracts/core/StrategyManager.sol

150      DOMAIN_SEPARATOR = keccak256(abi.encode(DOMAIN_TYPEHASH, keccak256(bytes("EigenLayer")), ORIGINAL_CHAIN_ID, address(this)));

268     bytes32 structHash = keccak256(abi.encode(DEPOSIT_TYPEHASH, strategy, token, amount, nonce, expiry));

276     bytes32 domain_separator = keccak256(abi.encode(DOMAIN_TYPEHASH, keccak256(bytes("EigenLayer")), block.chainid, address(this)));

879          abi.encode(

```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L150

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L268

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L276

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L879


```solidity
file: /contracts/pods/EigenPodManager.sol

175          abi.encode(eigenPodBeacon, "")

202          abi.encode(eigenPodBeacon, "")

```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPodManager.sol#L175

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPodManager.sol#L202

---------------------------------------------------------------------------------------------------------------------------

## [G-19] USE ASSEMBLY TO CHECK FOR ADDRESS(0)  


```solidity
file: /contracts/pods/EigenPodManager.sol

114     if(address(pod) == address(0)) {

196    if (address(pod) == address(0)) {

```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPodManager.sol#L114

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPodManager.sol#L196


```solidity
file: /contracts/pods/EigenPod.sol

153      require(_podOwner != address(0), "EigenPod.initialize: podOwner cannot be zero address");

```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPod.sol#L153


```solidity
file: /contracts/pods/DelayedWithdrawalRouter.sol

45    require(address(_eigenPodManager) != address(0), "DelayedWithdrawalRouter.constructor: _eigenPodManager cannot be zero address");

```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/DelayedWithdrawalRouter.sol#L45


```solidity
file: /contracts/permissions/Pausable.sol

56          require(
57            address(pauserRegistry) == address(0) && address(_pauserRegistry) != address(0),

```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/permissions/Pausable.sol#L56-L57


```solidity
file: /contracts/permissions/PauserRegistry.sol

42     require(newPauser != address(0), "PauserRegistry._setPauser: zero address input");

48     require(newUnpauser != address(0), "PauserRegistry._setUnpauser: zero address input");

```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/permissions/PauserRegistry.sol#L42

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/permissions/PauserRegistry.sol#L48

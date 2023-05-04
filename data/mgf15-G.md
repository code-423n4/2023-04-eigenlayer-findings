## Gas Optimizations


| |Issue|Instances|
|-|:-|:-:|
| [GAS-1](#GAS-1) | Use assembly to check for `address(0)` | 11 |
| [GAS-2](#GAS-2) | Use hardcoded address instead `address(this)` | 6 |
| [GAS-3](#GAS-3) | `abi.encode()` is less efficient than `abi.encodepacked()` | 6 |
| [GAS-4](#GAS-4) | Shifting cheaper than division | 2 |
| [GAS-5](#GAS-5) | Save gas with the use of the import statement | 54 |
| [GAS-6](#GAS-6) | `>=` costs less gas than `>` | 31 |
| [GAS-7](#GAS-7) | Don't initialize variables with default value | 25 |


### [GAS-1]  Use assembly to check for `address(0)`



```solidity
File:2023-04-eigenlayer/src/contracts/core/StrategyManager.sol
343:        require(withdrawer != address(0), "StrategyManager.queueWithdrawal: cannot withdraw to zero address");
631:        require(depositor != address(0), "StrategyManager._addShares: depositor cannot be zero address");
684:        require(depositor != address(0), "StrategyManager._removeShares: depositor cannot be zero address");
```
```solidity
File:2023-04-eigenlayer/src/contracts/strategies/StrategyBase.sol
41:        require(msg.sender == address(strategyManager), "StrategyBase.onlyStrategyManager");
```
```solidity
File:2023-04-eigenlayer/src/contracts/pods/EigenPodManager.sol
72:        require(msg.sender == address(strategyManager), "EigenPodManager.onlyStrategyManager: not strategyManager");
114:        if(address(pod) == address(0)) {
196:        if (address(pod) == address(0)) {
211:        return address(ownerToPod[podOwner]) != address(0);
```
```solidity
File:2023-04-eigenlayer/src/contracts/pods/EigenPod.sol
100:        require(msg.sender == address(eigenPodManager), "EigenPod.onlyEigenPodManager: not eigenPodManager");
153:        require(_podOwner != address(0), "EigenPod.initialize: podOwner cannot be zero address");
```
```solidity
File:2023-04-eigenlayer/src/contracts/pods/DelayedWithdrawalRouter.sol
45:        require(address(_eigenPodManager) != address(0), "DelayedWithdrawalRouter.constructor: _eigenPodManager cannot be zero address");
```
*Instances (11)*

### [GAS-2]  Use hardcoded address instead `address(this)`

Instead of using address(this), it is more gas-efficient to pre-calculate and use the hardcoded address. Foundry's script.sol and solmate's LibRlp.sol contracts can help achieve this.References: https://book.getfoundry.sh/reference/forge-std/compute-create-address

```solidity
File:2023-04-eigenlayer/src/contracts/core/StrategyManager.sol
150:        DOMAIN_SEPARATOR = keccak256(abi.encode(DOMAIN_TYPEHASH, keccak256(bytes("EigenLayer")), ORIGINAL_CHAIN_ID, address(this)));
276:            bytes32 domain_separator = keccak256(abi.encode(DOMAIN_TYPEHASH, keccak256(bytes("EigenLayer")), block.chainid, address(this)));
```
```solidity
File:2023-04-eigenlayer/src/contracts/strategies/StrategyBase.sol
236:        return strategyManager.stakerStrategyShares(user, IStrategy(address(this)));
242:        return underlyingToken.balanceOf(address(this));
```
```solidity
File:2023-04-eigenlayer/src/contracts/pods/EigenPod.sol
456:        _sendETH(podOwner, address(this).balance);
461:        return abi.encodePacked(bytes1(uint8(1)), bytes11(0), address(this));
```
*Instances (6)*

### [GAS-3]  `abi.encode()` is less efficient than `abi.encodepacked()`

See for more information: https://github.com/ConnorBlockchain/Solidity-Encode-Gas-Comparison

```solidity
File:2023-04-eigenlayer/src/contracts/core/StrategyManager.sol
150:        DOMAIN_SEPARATOR = keccak256(abi.encode(DOMAIN_TYPEHASH, keccak256(bytes("EigenLayer")), ORIGINAL_CHAIN_ID, address(this)));
268:        bytes32 structHash = keccak256(abi.encode(DEPOSIT_TYPEHASH, strategy, token, amount, nonce, expiry));
276:            bytes32 domain_separator = keccak256(abi.encode(DOMAIN_TYPEHASH, keccak256(bytes("EigenLayer")), block.chainid, address(this)));
879:                abi.encode(
```
```solidity
File:2023-04-eigenlayer/src/contracts/pods/EigenPodManager.sol
175:                        abi.encode(eigenPodBeacon, "")
202:                        abi.encode(eigenPodBeacon, "")
```
*Instances (6)*

### [GAS-4]  Shifting cheaper than division

A division by 2 can be calculated by shifting one to the right. While the DIV opcode uses 5 gas, the SHR opcode only uses 3 gas. Furthermore, Solidity’s division operation also includes a division-by-0 prevention which is bypassed using shifting.

```solidity
File:2023-04-eigenlayer/src/contracts/libraries/Merkle.sol
141:        numNodesInLayer /= 2;
149:            numNodesInLayer /= 2;
```
*Instances (2)*

### [GAS-5]  Save gas with the use of the import statement

With the import statement, it saves gas to specifically import only the parts of the contracts, not the complete ones.

```solidity
File:2023-04-eigenlayer/src/contracts/core/StrategyManager.sol
4:import "@openzeppelin/contracts/interfaces/IERC1271.sol";
5:import "@openzeppelin/contracts/utils/Address.sol";
6:import "@openzeppelin-upgrades/contracts/proxy/utils/Initializable.sol";
7:import "@openzeppelin-upgrades/contracts/access/OwnableUpgradeable.sol";
8:import "@openzeppelin-upgrades/contracts/security/ReentrancyGuardUpgradeable.sol";
9:import "@openzeppelin/contracts/token/ERC20/utils/SafeERC20.sol";
10:import "@openzeppelin/contracts/utils/cryptography/ECDSA.sol";
11:import "../interfaces/IEigenPodManager.sol";
12:import "../permissions/Pausable.sol";
13:import "./StrategyManagerStorage.sol";
```
```solidity
File:2023-04-eigenlayer/src/contracts/core/StrategyManagerStorage.sol
4:import "../interfaces/IStrategyManager.sol";
5:import "../interfaces/IStrategy.sol";
6:import "../interfaces/IEigenPodManager.sol";
7:import "../interfaces/IDelegationManager.sol";
8:import "../interfaces/ISlasher.sol";
```
```solidity
File:2023-04-eigenlayer/src/contracts/strategies/StrategyBase.sol
4:import "../interfaces/IStrategyManager.sol";
5:import "../permissions/Pausable.sol";
6:import "@openzeppelin/contracts/token/ERC20/IERC20.sol";
7:import "@openzeppelin/contracts/token/ERC20/utils/SafeERC20.sol";
8:import "@openzeppelin-upgrades/contracts/proxy/utils/Initializable.sol";
```
```solidity
File:2023-04-eigenlayer/src/contracts/pods/EigenPodManager.sol
4:import "@openzeppelin/contracts/utils/Create2.sol";
5:import "@openzeppelin/contracts/proxy/beacon/BeaconProxy.sol";
6:import "@openzeppelin/contracts/proxy/beacon/IBeacon.sol";
8:import "@openzeppelin-upgrades/contracts/proxy/utils/Initializable.sol";
9:import "@openzeppelin-upgrades/contracts/access/OwnableUpgradeable.sol";
11:import "../interfaces/IStrategyManager.sol";
12:import "../interfaces/IDelegationManager.sol";
13:import "../interfaces/IEigenPodManager.sol";
14:import "../interfaces/IETHPOSDeposit.sol";
15:import "../interfaces/IEigenPod.sol";
17:import "../interfaces/IBeaconChainOracle.sol";
19:import "../permissions/Pausable.sol";
20:import "./EigenPodPausingConstants.sol";
```
```solidity
File:2023-04-eigenlayer/src/contracts/pods/EigenPod.sol
4:import "@openzeppelin-upgrades/contracts/proxy/utils/Initializable.sol";
5:import "@openzeppelin-upgrades/contracts/access/OwnableUpgradeable.sol";
6:import "@openzeppelin-upgrades/contracts/security/ReentrancyGuardUpgradeable.sol";
7:import "@openzeppelin-upgrades/contracts/utils/AddressUpgradeable.sol";
9:import "../libraries/BeaconChainProofs.sol";
10:import "../libraries/BytesLib.sol";
11:import "../libraries/Endian.sol";
13:import "../interfaces/IETHPOSDeposit.sol";
14:import "../interfaces/IEigenPodManager.sol";
15:import "../interfaces/IEigenPod.sol";
16:import "../interfaces/IDelayedWithdrawalRouter.sol";
17:import "../interfaces/IPausable.sol";
19:import "./EigenPodPausingConstants.sol";
```
```solidity
File:2023-04-eigenlayer/src/contracts/pods/DelayedWithdrawalRouter.sol
4:import "@openzeppelin-upgrades/contracts/proxy/utils/Initializable.sol";
5:import "@openzeppelin-upgrades/contracts/access/OwnableUpgradeable.sol";
6:import "@openzeppelin-upgrades/contracts/security/ReentrancyGuardUpgradeable.sol";
7:import "../interfaces/IEigenPodManager.sol";
8:import "../interfaces/IDelayedWithdrawalRouter.sol";
9:import "../permissions/Pausable.sol";
```
```solidity
File:2023-04-eigenlayer/src/contracts/libraries/BeaconChainProofs.sol
5:import "./Merkle.sol";
6:import "../libraries/Endian.sol";
```
*Instances (54)*

### [GAS-6]  `>=` costs less gas than `>`

```solidity
File:2023-04-eigenlayer/src/contracts/core/StrategyManager.sol
190:        if (amount > userShares) {
358:        for (uint256 i = 0; i < strategies.length;) {
466:        for(uint256 i = 0; i < queuedWithdrawals.length; i++) {
498:        for (uint256 i = 0; i < strategiesLength;) {
560:        for (uint256 i = 0; i < strategiesLength;) {
562:            if (indicesToSkipIndex < indicesToSkip.length && indicesToSkip[indicesToSkipIndex] == i) {
594:        for (uint256 i = 0; i < strategiesToWhitelistLength;) {
609:        for (uint256 i = 0; i < strategiesToRemoveFromWhitelistLength;) {
637:                stakerStrategyList[depositor].length < MAX_STAKER_STRATEGY_LIST_LENGTH,
725:            for (; j < stratsLength;) {
780:            for (uint256 i = 0; i < strategiesLength;) {
797:            for (uint256 i = 0; i < strategiesLength;) {
824:            if (amount > amountToDecrement) {
861:        for (uint256 i = 0; i < strategiesLength;) {
```
```solidity
File:2023-04-eigenlayer/src/contracts/pods/EigenPod.sol
121:        require(blockNumber > mostRecentWithdrawalBlockNumber,
257:        require(validatorCurrentBalanceGwei < REQUIRED_BALANCE_GWEI,
```
```solidity
File:2023-04-eigenlayer/src/contracts/pods/DelayedWithdrawalRouter.sol
115:        for (uint256 i = 0; i < claimableDelayedWithdrawalsLength; i++) {
142:        while (i < maxNumberOfDelayedWithdrawalsToClaim && (delayedWithdrawalsCompletedBefore + i) < _userWithdrawalsLength) {
146:            if (block.number < delayedWithdrawal.blockCreated + withdrawalDelayBlocks) {
```
```solidity
File:2023-04-eigenlayer/src/contracts/libraries/Merkle.sol
137:        for (uint i = 0; i < numNodesInLayer; i++) {
145:            for (uint i = 0; i < numNodesInLayer; i++) {
```
```solidity
File:2023-04-eigenlayer/src/contracts/libraries/BeaconChainProofs.sol
133:        for (uint256 i = 0; i < NUM_BEACON_BLOCK_HEADER_FIELDS; ++i) {
143:        for (uint256 i = 0; i < NUM_BEACON_STATE_FIELDS; ++i) {
153:        for (uint256 i = 0; i < NUM_VALIDATOR_FIELDS; ++i) {
163:        for (uint256 i = 0; i < ETH1_DATA_FIELD_TREE_HEIGHT; ++i) {
206:        uint256 index = (VALIDATOR_TREE_ROOT_INDEX << (VALIDATOR_TREE_HEIGHT + 1)) | uint256(validatorIndex);
234:        balanceIndex = (BALANCE_INDEX << (BALANCE_TREE_HEIGHT + 1)) | balanceIndex;
252:        require(proofs.blockHeaderRootIndex < 2**BLOCK_ROOTS_TREE_HEIGHT, "BeaconChainProofs.verifyWithdrawalProofs: blockRootIndex is too large");
253:        require(proofs.withdrawalIndex < 2**WITHDRAWALS_TREE_HEIGHT, "BeaconChainProofs.verifyWithdrawalProofs: withdrawalIndex is too large");
267:        uint256 blockHeaderIndex = BLOCK_ROOTS_INDEX << (BLOCK_ROOTS_TREE_HEIGHT)  | uint256(proofs.blockHeaderRootIndex);
291:        uint256 withdrawalIndex = WITHDRAWALS_INDEX << (WITHDRAWALS_TREE_HEIGHT + 1) | uint256(proofs.withdrawalIndex);
```
*Instances (31)*

### [GAS-7]  Don't initialize variables with default value


```solidity
File:2023-04-eigenlayer/src/contracts/core/StrategyManager.sol
38:    uint8 internal constant PAUSED_DEPOSITS = 0;
358:        for (uint256 i = 0; i < strategies.length;) {
466:        for(uint256 i = 0; i < queuedWithdrawals.length; i++) {
498:        for (uint256 i = 0; i < strategiesLength;) {
557:        uint256 indicesToSkipIndex = 0;
560:        for (uint256 i = 0; i < strategiesLength;) {
594:        for (uint256 i = 0; i < strategiesToWhitelistLength;) {
609:        for (uint256 i = 0; i < strategiesToRemoveFromWhitelistLength;) {
724:            uint256 j = 0;
780:            for (uint256 i = 0; i < strategiesLength;) {
797:            for (uint256 i = 0; i < strategiesLength;) {
861:        for (uint256 i = 0; i < strategiesLength;) {
```
```solidity
File:2023-04-eigenlayer/src/contracts/strategies/StrategyBase.sol
22:    uint8 internal constant PAUSED_DEPOSITS = 0;
```
```solidity
File:2023-04-eigenlayer/src/contracts/pods/EigenPodPausingConstants.sol
10:    uint8 internal constant PAUSED_NEW_EIGENPODS = 0;
```
```solidity
File:2023-04-eigenlayer/src/contracts/pods/DelayedWithdrawalRouter.sol
16:    uint8 internal constant PAUSED_DELAYED_WITHDRAWAL_CLAIMS = 0;
115:        for (uint256 i = 0; i < claimableDelayedWithdrawalsLength; i++) {
138:        uint256 amountToSend = 0;
141:        uint256 i = 0;
```
```solidity
File:2023-04-eigenlayer/src/contracts/libraries/Merkle.sol
137:        for (uint i = 0; i < numNodesInLayer; i++) {
145:            for (uint i = 0; i < numNodesInLayer; i++) {
```
```solidity
File:2023-04-eigenlayer/src/contracts/libraries/BeaconChainProofs.sol
65:    uint256 internal constant SLOT_INDEX = 0;
133:        for (uint256 i = 0; i < NUM_BEACON_BLOCK_HEADER_FIELDS; ++i) {
143:        for (uint256 i = 0; i < NUM_BEACON_STATE_FIELDS; ++i) {
153:        for (uint256 i = 0; i < NUM_VALIDATOR_FIELDS; ++i) {
163:        for (uint256 i = 0; i < ETH1_DATA_FIELD_TREE_HEIGHT; ++i) {
```
*Instances (25)*

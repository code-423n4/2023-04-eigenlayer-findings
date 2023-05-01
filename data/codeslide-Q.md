### Low Risk Issues List

| Number | Issue                   | Instances |
| :----: | :---------------------- | :-------: |
| [L-01] | Unsafe casting of uints |     3     |

#### [L-01] Unsafe casting of uints

Downcasting from `uint256` in Solidity does not revert on overflow. This can result in undesired exploitation or bugs, since developers usually assume that overflows raise errors. [OpenZeppelin's SafeCast](https://docs.openzeppelin.com/contracts/3.x/api/utils#SafeCast) restores this intuition by reverting the transaction when such an operation overflows. Using this library instead of the unchecked operations eliminates an entire class of bugs, so it’s recommended to always use it.

For example:

```solidity
// Before
REQUIRED_BALANCE_GWEI = uint64(_REQUIRED_BALANCE_WEI / GWEI_TO_WEI);

// After
REQUIRED_BALANCE_GWEI = toUint64(_REQUIRED_BALANCE_WEI / GWEI_TO_WEI);
```

```solidity
File: src\contracts\libraries\Endian.sol

9:    n = uint64(uint256(lenum >> 192));
```

```solidity
File: src\contracts\pods\EigenPod.sol

146:    REQUIRED_BALANCE_GWEI = uint64(_REQUIRED_BALANCE_WEI / GWEI_TO_WEI);

445:    restakedExecutionLayerGwei -= uint64(amountWei / GWEI_TO_WEI);
```

### Non Critical Issues

| Number | Issue | Instances |
| :-: | :-- | :-: |
| [NC-01] | State variables should have an explicit visibility modifier | 1 |
| [NC-02] | Inconsistent code formatting | 33 |
| [NC-03] | Use the complete name of data types | 2 |
| [NC-04] | Trailing whitespace | 29 |
| [NC-05] | Inconsistent indentation | 3 |
| [NC-06] | Use meaningful variable names | 3 |

#### [NC-01] State variables should have an explicit visibility modifier

```solidity
File: src\contracts\core\StrategyManager.sol

42:    uint256 immutable ORIGINAL_CHAIN_ID;
```

#### [NC-02] Inconsistent code formatting

This code base exhibits formatting inconsistencies that may be interpreted by a reader as a lack of attention to detail, which could reduce confidence and trust in the protocol. The lack of consistent formatting also makes reading and maintaining the code more difficult.

To increase readability and maintainability, and to increase confidence in the protocol and the team behind it, the Solidity source code should use consistent formatting throughout.

Mitigation:

1. Follow the [Solidity Style Guide (SSG)](https://docs.soliditylang.org/en/latest/style-guide.htm)
2. Use `forge fmt`, [prettier-solidity](https://github.com/prettier-solidity/prettier-plugin-solidity), or an equivalent solution to automatically format Solidity code.

```solidity
File: src\contracts\core\StrategyManager.sol

// need a space after for
466:    for(uint256 i = 0; i < queuedWithdrawals.length; i++) {

// else should be on the same line as the closing curly brace for the if
510:    }
511:    else {

// need space before opening curly brace
567:    if (queuedWithdrawal.strategies[i] == beaconChainETHStrategy){
```

```solidity
File: src\contracts\pods\EigenPodManager.sol

// need a space after if
114:    if(address(pod) == address(0)) {
```

```solidity
File: src\contracts\interfaces\IBeaconChainOracle.sol

// need a space after returns

10:    function latestConfirmedOracleBlockNumber() external view returns(uint64);

13:    function beaconStateRootAtBlockNumber(uint64 blockNumber) external view returns(bytes32);

16:    function isOracleSigner(address _oracleSigner) external view returns(bool);

19:    function hasVoted(uint64 blockNumber, address oracleSigner) external view returns(bool);

22:    function stateRootVotes(uint64 blockNumber, bytes32 stateRoot) external view returns(uint256);

25:    function totalOracleSigners() external view returns(uint256);

33:    function threshold() external view returns(uint256);
```

```solidity
File: src\contracts\libraries\Merkle.sol

// need spaces surrounding += operator
50:    for (uint256 i = 32; i <= proof.length; i+=32) {
// need a space after if
51:    if(index % 2 == 0) {

// need spaces surrounding += operator
101:    for (uint256 i = 32; i <= proof.length; i+=32) {
// need a space after if
102:    if(index % 2 == 0) {

// need spaces surrounding * and + operators
146:    layer[i] = sha256(abi.encodePacked(layer[2*i], layer[2*i+1]));
```

```solidity
File: src\contracts\libraries\BeaconChainProofs.sol

// need a space after returns
132:    function computePhase0BeaconBlockHeaderRoot(bytes32[NUM_BEACON_BLOCK_HEADER_FIELDS] calldata blockHeaderFields) internal pure returns(bytes32) {

// need spaces surrounding ** operator
131:    bytes32[] memory paddedHeaderFields = new bytes32[](2**BEACON_BLOCK_HEADER_FIELD_TREE_HEIGHT);

// need a space after returns
140:    function computePhase0BeaconStateRoot(bytes32[NUM_BEACON_STATE_FIELDS] calldata beaconStateFields) internal pure returns(bytes32) {

// need spaces surrounding ** operator
141:    bytes32[] memory paddedBeaconStateFields = new bytes32[](2**BEACON_STATE_FIELD_TREE_HEIGHT);

// need a space after returns
150:    function computePhase0ValidatorRoot(bytes32[NUM_VALIDATOR_FIELDS] calldata validatorFields) internal pure returns(bytes32) {

// need spaces surrounding ** operator
151:    bytes32[] memory paddedValidatorFields = new bytes32[](2**VALIDATOR_FIELD_TREE_HEIGHT);

// need a space after returns
160:    function computePhase0Eth1DataRoot(bytes32[NUM_ETH1_DATA_FIELDS] calldata eth1DataFields) internal pure returns(bytes32) {

// need spaces surrounding ** operator
161:    bytes32[] memory paddedEth1DataFields = new bytes32[](2**ETH1_DATA_FIELD_TREE_HEIGHT);

// need spaces surrounding ** operator
199:    require(validatorFields.length == 2**VALIDATOR_FIELD_TREE_HEIGHT, "BeaconChainProofs.verifyValidatorFields: Validator fields has incorrect length");

// need spaces surrounding / operator
233:    uint256 balanceIndex = uint256(validatorIndex/4);

// need spaces surrounding ** operator
250:     require(withdrawalFields.length == 2**WITHDRAWAL_FIELD_TREE_HEIGHT, "BeaconChainProofs.verifyWithdrawalProofs: withdrawalFields has incorrect length");
252:    require(proofs.blockHeaderRootIndex < 2**BLOCK_ROOTS_TREE_HEIGHT, "BeaconChainProofs.verifyWithdrawalProofs: blockRootIndex is too large");
253:    require(proofs.withdrawalIndex < 2**WITHDRAWALS_TREE_HEIGHT, "BeaconChainProofs.verifyWithdrawalProofs: withdrawalIndex is too large");

// should only be one space on either side of | operator
267:    uint256 blockHeaderIndex = BLOCK_ROOTS_INDEX << (BLOCK_ROOTS_TREE_HEIGHT)  | uint256(proofs.blockHeaderRootIndex);

// need spaces surrounding | operator; remove space preceding semicolon
276:    uint256 executionPayloadIndex = BODY_ROOT_INDEX << (BEACON_BLOCK_BODY_FIELD_TREE_HEIGHT)| EXECUTION_PAYLOAD_INDEX ;
```

```solidity
File: src\contracts\libraries\BeaconChainProofs.sol

// need spaces surrounding / operator
354:    if (Endian.fromLittleEndianUint64(validatorFields[BeaconChainProofs.VALIDATOR_WITHDRAWABLE_EPOCH_INDEX]) <= slot/BeaconChainProofs.SLOTS_PER_EPOCH) {
```

#### [NC-03] Use the complete name of data types

Subtle bugs can occur when just `uint` or `int` is used. For example, when hashing the signature of a function and use `uint` instead of `uint256` for a parameter type.

Remediation recommendations:

1. Replace `uint` with `uint256`. Replace `int` with `int256`.
2. Use `forge fmt`, which will automatically change `uint` to `uint256` and `int` to `int256`.

```solidity
File: src\contracts\libraries\Merkle.sol

137:    for (uint i = 0; i < numNodesInLayer; i++) {

145:    for (uint i = 0; i < numNodesInLayer; i++) {
```

#### [NC-04] Trailing whitespace

The vast majority of the code omits trailing whitespace, but some lines have one to many trailing whitespace characters. This is another example of formatting inconsistencies that may be interpreted by a reader as a lack of attention to detail, which could reduce confidence and trust in the protocol.

To increase confidence in the protocol and the team behind it, the Solidity source code should use consistent formatting throughout.

Mitigation:

Use `forge fmt`, [prettier-solidity](https://github.com/prettier-solidity/prettier-plugin-solidity), or an equivalent solution to automatically format Solidity code and trim trailing whitespace.

1. [src\contracts\interfaces\IEigenPodManager.sol:22](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/interfaces/IEigenPodManager.sol#L22)
1. [src\contracts\interfaces\IEigenPodManager.sol:70](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/interfaces/IEigenPodManager.sol#L70)
1. [src\contracts\interfaces\IDelayedWithdrawalRouter.sol:44](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/interfaces/IDelayedWithdrawalRouter.sol#L44)
1. [src\contracts\interfaces\IStrategyManager.sol:122](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/interfaces/IStrategyManager.sol#L122)
1. [src\contracts\interfaces\IStrategyManager.sol:134](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/interfaces/IStrategyManager.sol#L134)
1. [src\contracts\interfaces\IStrategyManager.sol:153](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/interfaces/IStrategyManager.sol#L153)
1. [src\contracts\libraries\Merkle.sol:24](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/libraries/Merkle.sol#L24)
1. [src\contracts\libraries\Merkle.sol:66](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/libraries/Merkle.sol#L66)
1. [src\contracts\libraries\Merkle.sol:75](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/libraries/Merkle.sol#L75)
1. [src\contracts\libraries\Merkle.sol:92](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/libraries/Merkle.sol#L92)
1. [src\contracts\libraries\BeaconChainProofs.sol:132](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/libraries/BeaconChainProofs.sol#L132)
1. [src\contracts\libraries\BeaconChainProofs.sol:146](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/libraries/BeaconChainProofs.sol#L146)
1. [src\contracts\libraries\BeaconChainProofs.sol:150](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/libraries/BeaconChainProofs.sol#L150)
1. [src\contracts\libraries\BeaconChainProofs.sol:160](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/libraries/BeaconChainProofs.sol#L160)
1. [src\contracts\libraries\BeaconChainProofs.sol:195](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/libraries/BeaconChainProofs.sol#L195)
1. [src\contracts\libraries\BeaconChainProofs.sol:198](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/libraries/BeaconChainProofs.sol#L198)
1. [src\contracts\libraries\BeaconChainProofs.sol:254](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/libraries/BeaconChainProofs.sol#L254)
1. [src\contracts\pods\EigenPodManager.sol:41](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPodManager.sol#L41)
1. [src\contracts\pods\EigenPodManager.sol:53](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPodManager.sol#L53)
1. [src\contracts\pods\EigenPod.sol:217](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPod.sol#L217)
1. [src\contracts\pods\EigenPod.sol:234](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPod.sol#L234)
1. [src\contracts\pods\EigenPod.sol:255](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPod.sol#L255)
1. [src\contracts\pods\EigenPod.sol:259](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPod.sol#L259)
1. [src\contracts\pods\EigenPod.sol:262](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPod.sol#L262)
1. [src\contracts\pods\EigenPod.sol:306](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPod.sol#L306)
1. [src\contracts\pods\EigenPod.sol:332](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPod.sol#L332)
1. [src\contracts\core\StrategyManager.sol:344](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L344)
1. [src\contracts\core\StrategyManager.sol:349](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L349)
1. [src\contracts\core\StrategyManager.sol:366](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L366)

#### [NC-05] Inconsistent indentation

The vast majority of the code uses consistent 4-space indentation, but there are some lines that deviate from this.

To increase confidence in the protocol and the team behind it, the Solidity source code should use consistent formatting throughout.

Mitigation:

Use `forge fmt`, [prettier-solidity](https://github.com/prettier-solidity/prettier-plugin-solidity), or an equivalent solution to automatically format Solidity code with consistent indentation.

1. [src\contracts\pods\EigenPod.sol:248](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPod.sol#L248)
1. [src\contracts\pods\EigenPod.sol:280](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPod.sol#L280)
1. [src\contracts\strategies\StrategyBase.sol:27](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/strategies/StrategyBase.sol#L27)

#### [NC-06] Use meaningful variable names

To make reading and understanding the code easier, it is recommended to use meaningful variable names. The vast majority of the code uses good, verbose variable names. The following variable names could be improved.

```solidity
File: src\contracts\interfaces\IDelegationManager.sol

24:    function registerAsOperator(IDelegationTerms dt) external;
```

```solidity
File: src\contracts\libraries\Endian.sol

6:    bytes32 lenum
7:    ) internal pure returns (uint64 n) {
```

## [L-01] Missing zero address check

### Description

There are missing zero address checks as the following:

#### 1. The `initialize` function in the `DelayedWithdrawalRouter` contract

Missing zero address check:

- `address initOwner`

[src/contracts/pods/DelayedWithdrawalRouter.sol#L49-L53](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/DelayedWithdrawalRouter.sol#L49-L53)

```solidity
    function initialize(address initOwner, IPauserRegistry _pauserRegistry, uint256 initPausedStatus, uint256 _withdrawalDelayBlocks) external initializer {
        _transferOwnership(initOwner);
        _initializePauser(_pauserRegistry, initPausedStatus);
        _setWithdrawalDelayBlocks(_withdrawalDelayBlocks);
    }
```

The `initialize` function invokes the `_transferOwnership` function, which is imported from the OpenZeppelin `OwnableUpgradeable.sol` file. The function is defined as follows:

[OpenZeppelin/openzeppelin-contracts-upgradeable/blob/6b9807b0639e1dd75e07fa062e9432eb3f35dd8c/contracts/access/OwnableUpgradeable.sol#L83-L87](https://github.com/OpenZeppelin/openzeppelin-contracts-upgradeable/blob/6b9807b0639e1dd75e07fa062e9432eb3f35dd8c/contracts/access/OwnableUpgradeable.sol#L83-L87)

```solidity
    function _transferOwnership(address newOwner) internal virtual {
        address oldOwner = _owner;
        _owner = newOwner;
        emit OwnershipTransferred(oldOwner, newOwner);
    }
```

It is important to note that the `_transferOwnership` function does not include a zero address check for the `newOwner` parameter.

#### 2. The `initialize` function in the `EigenPodManager` contract

Missing zero address check:

- `IBeaconChainOracle _beaconChainOracle`
- `address initialOwner`

[src/contracts/pods/EigenPodManager.sol#L84-L93](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPodManager.sol#L84-L93)

```solidity
    function initialize(
        IBeaconChainOracle _beaconChainOracle,
        address initialOwner,
        IPauserRegistry _pauserRegistry,
        uint256 _initPausedStatus
    ) external initializer {
        _updateBeaconChainOracle(_beaconChainOracle);
        _transferOwnership(initialOwner);
        _initializePauser(_pauserRegistry, _initPausedStatus);
    }
```

#### 3. The `initialize` function in the `StrategyManager` contract

Missing zero address check:

- `address initialOwner`
- `address initialStrategyWhitelister`

[src/contracts/core/StrategyManager.sol#L146-L155](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L146-L155)

```solidity
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
```

#### 4. The `initialize` function in the `StrategyBase` contract

Missing zero address check:

- `IERC20 _underlyingToken`

[src/contracts/strategies/StrategyBase.sol#L51-L59](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/strategies/StrategyBase.sol#L51-L59)

```solidity
    function initialize(IERC20 _underlyingToken, IPauserRegistry _pauserRegistry) public virtual initializer {
        _initializeStrategyBase(_underlyingToken, _pauserRegistry);
    }

    /// @notice Sets the `underlyingToken` and `pauserRegistry` for the strategy.
    function _initializeStrategyBase(IERC20 _underlyingToken, IPauserRegistry _pauserRegistry) internal onlyInitializing {
        underlyingToken = _underlyingToken;
        _initializePauser(_pauserRegistry, UNPAUSE_ALL);
    }
```

#### 5. The constructor in the `StrategyManagerStorage` contract

Missing zero address check:

- `IDelegationManager _delegation`
- `IEigenPodManager _eigenPodManager`
- `ISlasher _slasher`

[src/contracts/core/StrategyManagerStorage.sol#L72-L76](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManagerStorage.sol#L72-L76)

```solidity
    constructor(IDelegationManager _delegation, IEigenPodManager _eigenPodManager, ISlasher _slasher) {
        delegation = _delegation;
        eigenPodManager = _eigenPodManager;
        slasher = _slasher;
    }
```

#### 6. The constructor in the `StrategyBase` contract

Missing zero address check:

- `IStrategyManager _strategyManager`

[src/contracts/strategies/StrategyBase.sol#L46-L49](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/strategies/StrategyBase.sol#L46-L49)

```solidity
    constructor(IStrategyManager _strategyManager) {
        strategyManager = _strategyManager;
        _disableInitializers();
    }
```

#### 7. The constructor in the `EigenPodManager` contract

Missing zero address check:

- `IETHPOSDeposit _ethPOS`
- `IBeacon _eigenPodBeacon`
- `IStrategyManager _strategyManager`
- `ISlasher _slasher`

[src/contracts/pods/EigenPodManager.sol#L76-L82](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPodManager.sol#L76-L82)

```solidity
    constructor(IETHPOSDeposit _ethPOS, IBeacon _eigenPodBeacon, IStrategyManager _strategyManager, ISlasher _slasher) {
        ethPOS = _ethPOS;
        eigenPodBeacon = _eigenPodBeacon;
        strategyManager = _strategyManager;
        slasher = _slasher;
        _disableInitializers();
    }
```

#### 8. The constructor in the `EigenPod` contract

Missing zero address check:

- `IETHPOSDeposit _ethPOS`
- `IDelayedWithdrawalRouter _delayedWithdrawalRouter`
- `IEigenPodManager _eigenPodManager`

[src/contracts/pods/EigenPod.sol#L136-L149](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPod.sol#L136-L149)

```solidity
    constructor(
        IETHPOSDeposit _ethPOS,
        IDelayedWithdrawalRouter _delayedWithdrawalRouter,
        IEigenPodManager _eigenPodManager,
        uint256 _REQUIRED_BALANCE_WEI
    ) {
        ethPOS = _ethPOS;
        delayedWithdrawalRouter = _delayedWithdrawalRouter;
        eigenPodManager = _eigenPodManager;
        REQUIRED_BALANCE_WEI = _REQUIRED_BALANCE_WEI;
        REQUIRED_BALANCE_GWEI = uint64(_REQUIRED_BALANCE_WEI / GWEI_TO_WEI);
        require(_REQUIRED_BALANCE_WEI % GWEI_TO_WEI == 0, "EigenPod.contructor: _REQUIRED_BALANCE_WEI is not a whole number of gwei");
        _disableInitializers();
    }
```

### Recommendation

Add zero address checks.

## [L-02] Missing out-of-bounds access check in the `_removeStrategyFromStakerStrategyList` function

### Description

The `_removeStrategyFromStakerStrategyList` function in the `StrategyManager` contract can cause an out-of-bounds access error because there is no validation for the `strategyIndex` parameter.

[src/contracts/core/StrategyManager.sol#L715-L740](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L715-L740)

```solidity
       function _removeStrategyFromStakerStrategyList(address depositor, uint256 strategyIndex, IStrategy strategy) internal {
        // if the strategy matches with the strategy index provided
        if (stakerStrategyList[depositor][strategyIndex] == strategy) {
            // replace the strategy with the last strategy in the list
            stakerStrategyList[depositor][strategyIndex] =
                stakerStrategyList[depositor][stakerStrategyList[depositor].length - 1];
        } else {
            //loop through all of the strategies, find the right one, then replace
            uint256 stratsLength = stakerStrategyList[depositor].length;
            uint256 j = 0;
            for (; j < stratsLength;) {
                if (stakerStrategyList[depositor][j] == strategy) {
                    //replace the strategy with the last strategy in the list
                    stakerStrategyList[depositor][j] = stakerStrategyList[depositor][stakerStrategyList[depositor].length - 1];
                    break;
                }
                unchecked {
                    ++j;
                }
            }
            // if we didn't find the strategy, revert
            require(j != stratsLength, "StrategyManager._removeStrategyFromStakerStrategyList: strategy not found");
        }
        // pop off the last entry in the list of strategies
        stakerStrategyList[depositor].pop();
    }
```

### Proof of Concept

Change the value of `uint256 beaconChainETHStrategyIndex` from `0` to `42` in `StrategyManagerUnit.t.sol` as shown below:

[src/test/unit/StrategyManagerUnit.t.sol#L185](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/test/unit/StrategyManagerUnit.t.sol#L185)

```diff
-uint256 beaconChainETHStrategyIndex = 0;
+uint256 beaconChainETHStrategyIndex = 42; // an arbitrary number
```

This modification will trigger the following error:

```text
[FAIL. Reason: Index out of bounds Counterexample: calldata=0x28bdd3e000000000000000000000000000000000000000000000000000000000000000030000000000000000000000000000000000000000000000000000000000000003, args=[3, 3]] testRecordOvercommittedBeaconChainETHSuccessfully(uint256,uint256) (runs: 116, μ: 144482, ~: 144482)
```

### Recommendation

Add an out-of-bounds access check for the `strategyIndex` parameter.

## [L-03] Missing array length check in the `queueWithdrawal` function

### Description

The `queueWithdrawal` function in the `StrategyManager` contract does not validate the length of the `uint256[] strategyIndexes` parameter.

[src/contracts/core/src/contracts/core/StrategyManager.sol#L329-L429](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L329-L429)

```solidity
 function queueWithdrawal(
        uint256[] calldata strategyIndexes,
        IStrategy[] calldata strategies,
        uint256[] calldata shares,
        address withdrawer,
        bool undelegateIfPossible
    )
        external
        onlyWhenNotPaused(PAUSED_WITHDRAWALS)
        onlyNotFrozen(msg.sender)
        nonReentrant
        returns (bytes32)
    {
        require(strategies.length == shares.length, "StrategyManager.queueWithdrawal: input length mismatch");
        require(withdrawer != address(0), "StrategyManager.queueWithdrawal: cannot withdraw to zero address");
        ...
```

Currently, the function assumes that the length of `strategyIndexes` is equal to the length of `strategies` and `shares`, which may not always be the case.

### Recommendation

Add an array length check for the `uint256[] strategyIndexes` parameter.

## [N-01] Misleading comment for the `sharesToUnderlying` function

### Description

The following comment for the `sharesToUnderlying` function in the `StrategyBase` contract is misleading:

[src/contracts/strategies/StrategyBase.sol#L182](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/strategies/StrategyBase.sol#L182)

```text
* @notice In contrast to `sharesToUnderlyingView`, this function **may** make state modifications
```

The `sharesToUnderlying` function has a `view` modifier. Therefore, it does not modify the state.

### Recommendation

Update the comment for `sharesToUnderlying` function to accurately reflect that it does not modify the state.

## [N-02] Misleading comment for the `Merkle` contract

## Description

There is a misleading comment for the `Merkle` contract. The modified implementation uses `sha256` hash function but the comment does not reflect the changes.

[src/contracts/libraries/Merkle.sol#L6-L19](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/libraries/Merkle.sol#L6-L19)

```text
/**
 * @dev These functions deal with verification of Merkle Tree proofs.
 *
 * The tree and the proofs can be generated using our
 * https://github.com/OpenZeppelin/merkle-tree[JavaScript library].
 * You will find a quickstart guide in the readme.
 *
 * WARNING: You should avoid using leaf values that are 64 bytes long prior to
 * hashing, or use a hash function other than keccak256 for hashing leaves.
 * This is because the concatenation of a sorted pair of internal nodes in
 * the merkle tree could be reinterpreted as a leaf value.
 * OpenZeppelin's JavaScript library generates merkle trees that are safe
 * against this attack out of the box.
 */
```

The above comment is incorrect as OpenZeppelin has defined [standard Merkle trees](https://github.com/OpenZeppelin/merkle-tree/blob/7dbf9a11cd69a0cfabf9cca4dbae37d14d30e1a6/README.md#standard-merkle-trees) with the following characteristics:

- The tree is shaped as a [complete binary tree](https://xlinux.nist.gov/dads/HTML/completeBinaryTree.html).
- The leaves are sorted.
- The leaves are the result of ABI encoding a series of values.
- The hash used is Keccak256.
- The leaves are double-hashed to prevent second preimage attacks.

### Recommendation

Update the comment for the `Merkle` contract to accurately reflect the current implementation.

## [N-03] Redundant and misleading comment for the `amount` parameter of the `depositBeaconChainETH` function

### Description

The comment for the `depositBeaconChainETH` function contains a redundant and misleading comment for the `amount` parameter. The second occurrence of `amount` in the comment may confuse developers.

#### 1. `StrategyManager.sol`

[src/contracts/core/StrategyManager.sol#L157-L173](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L157-L173)

```solidity
    /**
     * @notice Deposits `amount` of beaconchain ETH into this contract on behalf of `staker`
     * @param staker is the entity that is restaking in eigenlayer,
     * @param amount is the amount of beaconchain ETH being restaked,
     * @param amount is the amount of token to be deposited in the strategy by the depositor
     * @dev Only callable by EigenPodManager.
     */
    function depositBeaconChainETH(address staker, uint256 amount)
        external
        onlyEigenPodManager
        onlyWhenNotPaused(PAUSED_DEPOSITS)
        onlyNotFrozen(staker)
        nonReentrant
    {
        // add shares for the enshrined beacon chain ETH strategy
        _addShares(staker, beaconChainETHStrategy, amount);
    }
```

#### 2. `IStrategyManager.sol`

[src/contracts/interfaces/IStrategyManager.sol#L48-L55](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/interfaces/IStrategyManager.sol#L48-L55)

```solidity
    /**
     * @notice Deposits `amount` of beaconchain ETH into this contract on behalf of `staker`
     * @param staker is the entity that is restaking in eigenlayer,
     * @param amount is the amount of beaconchain ETH being restaked,
     * @param amount is the amount of token to be deposited in the strategy by the depositor
     * @dev Only callable by EigenPod for the staker.
     */
    function depositBeaconChainETH(address staker, uint256 amount) external;
```

### Recommendation

Remove the second occurrence of `amount` from the comment in both `StrategyManager.sol` and `IStrategyManager.sol`.

```diff
-    * @param amount is the amount of token to be deposited in the strategy by the depositor
```

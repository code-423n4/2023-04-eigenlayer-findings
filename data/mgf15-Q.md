## Non Critical & Low Issues

| |Issue|Instances|
|-|:-|:-:|
| [Low-1](#Low-1) | `abi.encodePacked()` should not be used with dynamic types when passing the result to a hash function such as `keccak256()` | 1 |
| [Low-2](#Low-2) | Initializers could be front-run | 8 |
| [Low-3](#Low-3) | Loss of precision | 5 |
| [Low-4](#Low-4) | Calls inside a loop | 5 |
| [Low-5](#Low-5) | Missing calls to __ReentrancyGuard_init functions of inherited contracts | 1 |



### [Low-1]  `abi.encodePacked()` should not be used with dynamic types when passing the result to a hash function such as `keccak256()`

Use `abi.encode()` instead which will pad items to 32 bytes, which will [prevent hash collisions](https://docs.soliditylang.org/en/v0.8.13/abi-spec.html#non-standard-packed-mode) (e.g. `abi.encodePacked(0x123,0x456)` => `0x123456` => `abi.encodePacked(0x1,0x23456)`, but `abi.encode(0x123,0x456)` => `0x0...1230...456`). Unless there is a compelling reason, `abi.encode` should be preferred. If there is only one argument to `abi.encodePacked()` it can often be cast to `bytes()` or `bytes32()` [instead](https://ethereum.stackexchange.com/questions/30912/how-to-compare-strings-in-solidity#answer-82739).
If all arguments are strings and or bytes, `bytes.concat()` should be used instead

```solidity
File:2023-04-eigenlayer/src/contracts/core/StrategyManager.sol
279:            digestHash = keccak256(abi.encodePacked("\x19\x01", DOMAIN_SEPARATOR, structHash));
```
*Instances (1)*

### [Low-2]  Initializers could be front-run

Initializers could be front-run, allowing an attacker to either set their own values, take ownership of the contract, and in the best case forcing a re-deployment

```solidity
File:2023-04-eigenlayer/src/contracts/core/StrategyManager.sol
146:    function initialize(address initialOwner, address initialStrategyWhitelister, IPauserRegistry _pauserRegistry, uint256 initialPausedStatus, uint256 _withdrawalDelayBlocks)
148:        initializer
```

```solidity
File:2023-04-eigenlayer/src/contracts/strategies/StrategyBase.sol
51:    function initialize(IERC20 _underlyingToken, IPauserRegistry _pauserRegistry) public virtual initializer {
```

```solidity
File:2023-04-eigenlayer/src/contracts/pods/EigenPodManager.sol
84:    function initialize(
89:    ) external initializer {
179:        pod.initialize(msg.sender);
```

```solidity
File:2023-04-eigenlayer/src/contracts/pods/EigenPod.sol
152:    function initialize(address _podOwner) external initializer {
```
```
File:2023-04-eigenlayer/src/contracts/pods/DelayedWithdrawalRouter.sol
49:    function initialize(address initOwner, IPauserRegistry _pauserRegistry, uint256 initPausedStatus, uint256 _withdrawalDelayBlocks) external initializer {
```
*Instances (8)*

### [Low-3]  Loss of precision

Division by large numbers may result in the result being zero, due to solidity not supporting fractions. Consider requiring a minimum amount for the numerator to ensure that it is always larger than the denominator


```solidity
File:2023-04-eigenlayer/src/contracts/pods/EigenPod.sol
146:        REQUIRED_BALANCE_GWEI = uint64(_REQUIRED_BALANCE_WEI / GWEI_TO_WEI);
354:        if (Endian.fromLittleEndianUint64(validatorFields[BeaconChainProofs.VALIDATOR_WITHDRAWABLE_EPOCH_INDEX]) <= slot/BeaconChainProofs.SLOTS_PER_EPOCH) {
445:        restakedExecutionLayerGwei -= uint64(amountWei / GWEI_TO_WEI);
```

```solidity
File:2023-04-eigenlayer/src/contracts/libraries/Merkle.sol
133:        uint256 numNodesInLayer = leaves.length / 2;
```

```solidity
File:2023-04-eigenlayer/src/contracts/libraries/BeaconChainProofs.sol
233:        uint256 balanceIndex = uint256(validatorIndex/4);
```
*Instances (5)*

### [Low-4] Calls inside a loop

Calls inside a loop might lead to a denial-of-service attack.
If one of the destinations has a fallback function that reverts, the function will always revert.

```solidity
File:2023-04-eigenlayer/src/contracts/core/StrategyManager.sol

467:            _completeQueuedWithdrawal(queuedWithdrawals[i], tokens[i], middlewareTimesIndexes[i], receiveAsTokens[i]);


569:                    _withdrawBeaconChainETH(queuedWithdrawal.depositor, recipient, queuedWithdrawal.shares[i]);
572:                    queuedWithdrawal.strategies[i].withdraw(recipient, tokens[i], queuedWithdrawal.shares[i]);

784:                    _withdrawBeaconChainETH(queuedWithdrawal.depositor, msg.sender, queuedWithdrawal.shares[i]);

787:		queuedWithdrawal.strategies[i].withdraw(
788:                        msg.sender, tokens[i], queuedWithdrawal.shares[i]
789:                    );
```
*Instances (5)*

### [Low-5] Missing calls to __ReentrancyGuard_init functions of inherited contracts

Most contracts use the delegateCall proxy pattern and hence their implementations require the use of initialize() functions instead of constructors. This requires derived contracts to call the corresponding init functions of their inherited base contracts. This is done in most places except a few.
Impact

The inherited base classes do not get initialized which may lead to undefined behavior.

Missing call to __ReentrancyGuard_init
```solidity
File:2023-04-eigenlayer/src/contracts/core/StrategyManager.sol

146:    function initialize(address initialOwner, address initialStrategyWhitelister, IPauserRegistry _pauserRegistry, uint256 initialPausedStatus, uint256 _withdrawalDelayBlocks)
147:        external
148:        initializer
149:    {
150:        DOMAIN_SEPARATOR = keccak256(abi.encode(DOMAIN_TYPEHASH, keccak256(bytes("EigenLayer")), ORIGINAL_CHAIN_ID, address(this)));
151:        _initializePauser(_pauserRegistry, initialPausedStatus);
152:        _transferOwnership(initialOwner);
153:        _setStrategyWhitelister(initialStrategyWhitelister);
154:        _setWithdrawalDelayBlocks(_withdrawalDelayBlocks);
155:    }
```

| |Issue|Instances|
|-|:-|:-:|
| [NC-1](#NC-1) | The `nonReentrant` `modifier` should occur before all other modifiers | 28 |
| [NC-2](#NC-2) | Use encode instead of encodePacked for hashig | 4 |
| [NC-3](#NC-3) | Lines are too long | 276 |
| [NC-4](#NC-4) | Using while for unbounded loops isn’t recommended | 2 |
| [NC-5](#NC-5) | Lack of event emission after critical initialize() functions | 3 |



### [NC-1]  The `nonReentrant` `modifier` should occur before all other modifiers

This is a best-practice to protect against reentrancy in other modifiers

```solidity
File:2023-04-eigenlayer/src/contracts/core/StrategyManager.sol
304:    function undelegate() external {
582:    function setWithdrawalDelayBlocks(uint256 _withdrawalDelayBlocks) external onlyOwner {
587:    function setStrategyWhitelister(address newStrategyWhitelister) external onlyOwner {
592:    function addStrategiesToDepositWhitelist(IStrategy[] calldata strategiesToWhitelist) external onlyStrategyWhitelister {
607:    function removeStrategiesFromDepositWhitelist(IStrategy[] calldata strategiesToRemoveFromWhitelist) external onlyStrategyWhitelister {
857:    function getDeposits(address depositor) external view returns (IStrategy[] memory, uint256[] memory) {
871:    function stakerStrategyListLength(address staker) external view returns (uint256) {
```

```solidity
File:2023-04-eigenlayer/src/contracts/strategies/StrategyBase.sol
162:    function explanation() external pure virtual override returns (string memory) {
211:    function underlyingToShares(uint256 amountUnderlying) external view virtual returns (uint256) {
219:    function userUnderlyingView(address user) external view virtual returns (uint256) {
227:    function userUnderlying(address user) external virtual returns (uint256) {
```

```solidity
File:2023-04-eigenlayer/src/contracts/pods/EigenPodManager.sol
99:    function createPod() external {
112:    function stake(bytes calldata pubkey, bytes calldata signature, bytes32 depositDataRoot) external payable {
127:    function restakeBeaconChainETH(address podOwner, uint256 amount) external onlyEigenPod(podOwner) {
139:    function recordOvercommittedBeaconChainETH(address podOwner, uint256 beaconChainETHStrategyIndex, uint256 amount) external onlyEigenPod(podOwner) {
161:    function updateBeaconChainOracle(IBeaconChainOracle newBeaconChainOracle) external onlyOwner {
215:    function getBeaconChainStateRoot(uint64 blockNumber) external view returns(bytes32) {
```

```solidity
File:2023-04-eigenlayer/src/contracts/pods/EigenPod.sol
152:    function initialize(address _podOwner) external initializer {
158:    function stake(bytes calldata pubkey, bytes calldata signature, bytes32 depositDataRoot) external payable onlyEigenPodManager {
454:    function withdrawBeforeRestaking() external onlyEigenPodOwner hasNeverRestaked {
```

```solidity
File:2023-04-eigenlayer/src/contracts/pods/DelayedWithdrawalRouter.sol
49:    function initialize(address initOwner, IPauserRegistry _pauserRegistry, uint256 initPausedStatus, uint256 _withdrawalDelayBlocks) external initializer {
59:    function createDelayedWithdrawal(address podOwner, address recipient) external payable onlyEigenPod(podOwner) {
100:    function setWithdrawalDelayBlocks(uint256 newValue) external onlyOwner {
105:    function userWithdrawals(address user) external view returns (UserDelayedWithdrawals memory) {
110:    function claimableUserDelayedWithdrawals(address user) external view returns (DelayedWithdrawal[] memory) {
122:    function userDelayedWithdrawalByIndex(address user, uint256 index) external view returns (DelayedWithdrawal memory) {
127:    function userWithdrawalsLength(address user) external view returns (uint256) {
132:    function canClaimDelayedWithdrawal(address user, uint256 index) external view returns (bool) {
```
*Instances (28)*

### [NC-2]  Use encode instead of encodePacked for hashig

Use of abi.encodePacked is safe, but unnecessary and not recommended. abi.encodePacked can result in hash collisions when used with two dynamic arguments (string/bytes).

```solidity
File:2023-04-eigenlayer/src/contracts/core/StrategyManager.sol
277:            digestHash = keccak256(abi.encodePacked("\x19\x01", domain_separator, structHash));
279:            digestHash = keccak256(abi.encodePacked("\x19\x01", DOMAIN_SEPARATOR, structHash));
```

```solidity
File:2023-04-eigenlayer/src/contracts/libraries/Merkle.sol
138:            layer[i] = sha256(abi.encodePacked(leaves[2*i], leaves[2*i+1]));
146:                layer[i] = sha256(abi.encodePacked(layer[2*i], layer[2*i+1]));
```
*Instances (4)*

### [NC-3]  Lines are too long

Usually lines in source code are limited to [80](https://softwareengineering.stackexchange.com/questions/148677/why-is-80-characters-the-standard-limit-for-code-width) characters. Today's screens are much larger so it's reasonable to stretch this in some cases. Since the files will most likely reside in GitHub, and GitHub starts using a scroll bar in all cases when the length is over [164](https://github.com/aizatto/character-length) characters, the lines below should be split when they reach that length

```solidity
File:2023-04-eigenlayer/src/contracts/core/StrategyManager.sol
8:import "@openzeppelin-upgrades/contracts/security/ReentrancyGuardUpgradeable.sol";
78:        address depositor, uint96 nonce, address withdrawer, address delegatedAddress, bytes32 withdrawalRoot
82:    event WithdrawalCompleted(address indexed depositor, uint96 nonce, address indexed withdrawer, bytes32 withdrawalRoot);
85:    event StrategyWhitelisterChanged(address previousAddress, address newAddress);
99:            "StrategyManager.onlyNotFrozen: staker has been frozen and may be subject to slashing"
105:        require(slasher.isFrozen(staker), "StrategyManager.onlyFrozen: staker has not been frozen");
110:        require(address(eigenPodManager) == msg.sender, "StrategyManager.onlyEigenPodManager: not the eigenPodManager");
115:        require(msg.sender == strategyWhitelister, "StrategyManager.onlyStrategyWhitelister: not the strategyWhitelister");
120:        require(strategyIsWhitelistedForDeposit[strategy], "StrategyManager.onlyStrategiesWhitelistedForDeposit: strategy not whitelisted");
129:    constructor(IDelegationManager _delegation, IEigenPodManager _eigenPodManager, ISlasher _slasher)
146:    function initialize(address initialOwner, address initialStrategyWhitelister, IPauserRegistry _pauserRegistry, uint256 initialPausedStatus, uint256 _withdrawalDelayBlocks)
150:        DOMAIN_SEPARATOR = keccak256(abi.encode(DOMAIN_TYPEHASH, keccak256(bytes("EigenLayer")), ORIGINAL_CHAIN_ID, address(this)));
182:    function recordOvercommittedBeaconChainETH(address overcommittedPodOwner, uint256 beaconChainETHStrategyIndex, uint256 amount)
188:        uint256 userShares = stakerStrategyShares[overcommittedPodOwner][beaconChainETHStrategy];
192:            beaconChainETHSharesToDecrementOnWithdrawal[overcommittedPodOwner] += debt;
197:            _removeShares(overcommittedPodOwner, beaconChainETHStrategyIndex, beaconChainETHStrategy, amount);
205:        delegation.decreaseDelegatedShares(overcommittedPodOwner, strategies, shareAmounts);
220:    function depositIntoStrategy(IStrategy strategy, IERC20 token, uint256 amount)
264:            "StrategyManager.depositIntoStrategyWithSignature: signature expired"
268:        bytes32 structHash = keccak256(abi.encode(DEPOSIT_TYPEHASH, strategy, token, amount, nonce, expiry));
276:            bytes32 domain_separator = keccak256(abi.encode(DOMAIN_TYPEHASH, keccak256(bytes("EigenLayer")), block.chainid, address(this)));
277:            digestHash = keccak256(abi.encodePacked("\x19\x01", domain_separator, structHash));
279:            digestHash = keccak256(abi.encodePacked("\x19\x01", DOMAIN_SEPARATOR, structHash));
290:            require(IERC1271(staker).isValidSignature(digestHash, signature) == ERC1271_MAGICVALUE,
291:                "StrategyManager.depositIntoStrategyWithSignature: ERC1271 signature verification failed");
294:                "StrategyManager.depositIntoStrategyWithSignature: signature not from staker");
342:        require(strategies.length == shares.length, "StrategyManager.queueWithdrawal: input length mismatch");
343:        require(withdrawer != address(0), "StrategyManager.queueWithdrawal: cannot withdraw to zero address");
361:                    "StrategyManager.queueWithdrawal: cannot queue a withdrawal of Beacon Chain ETH to a different address");
363:                    "StrategyManager.queueWithdrawal: cannot queue a withdrawal including Beacon Chain ETH and other tokens");
365:                    "StrategyManager.queueWithdrawal: cannot queue a withdrawal of Beacon Chain ETH for an non-whole amount of gwei");
370:            if (_removeShares(msg.sender, strategyIndexes[strategyIndexIndex], strategies[i], shares[i])) {
376:            emit ShareWithdrawalQueued(msg.sender, nonce, strategies[i], shares[i]);
422:        if (undelegateIfPossible && stakerStrategyList[msg.sender].length == 0) {
426:        emit WithdrawalQueued(msg.sender, nonce, withdrawer, delegatedAddress, withdrawalRoot);
442:    function completeQueuedWithdrawal(QueuedWithdrawal calldata queuedWithdrawal, IERC20[] calldata tokens, uint256 middlewareTimesIndex, bool receiveAsTokens)
448:        _completeQueuedWithdrawal(queuedWithdrawal, tokens, middlewareTimesIndex, receiveAsTokens);
467:            _completeQueuedWithdrawal(queuedWithdrawals[i], tokens[i], middlewareTimesIndexes[i], receiveAsTokens[i]);
495:        require(tokens.length == strategies.length, "StrategyManager.slashShares: input length mismatch");
501:            if (_removeShares(slashedAddress, strategyIndexes[strategyIndexIndex], strategies[i], shareAmounts[i])) {
509:                _withdrawBeaconChainETH(slashedAddress, recipient, shareAmounts[i]);
523:        delegation.decreaseDelegatedShares(slashedAddress, strategies, shareAmounts);
536:    function slashQueuedWithdrawal(address recipient, QueuedWithdrawal calldata queuedWithdrawal, IERC20[] calldata tokens, uint256[] calldata indicesToSkip)
542:        require(tokens.length == queuedWithdrawal.strategies.length, "StrategyManager.slashQueuedWithdrawal: input length mismatch");
562:            if (indicesToSkipIndex < indicesToSkip.length && indicesToSkip[indicesToSkipIndex] == i) {
569:                    _withdrawBeaconChainETH(queuedWithdrawal.depositor, recipient, queuedWithdrawal.shares[i]);
572:                    queuedWithdrawal.strategies[i].withdraw(recipient, tokens[i], queuedWithdrawal.shares[i]);
582:    function setWithdrawalDelayBlocks(uint256 _withdrawalDelayBlocks) external onlyOwner {
587:    function setStrategyWhitelister(address newStrategyWhitelister) external onlyOwner {
592:    function addStrategiesToDepositWhitelist(IStrategy[] calldata strategiesToWhitelist) external onlyStrategyWhitelister {
597:                strategyIsWhitelistedForDeposit[strategiesToWhitelist[i]] = true;
607:    function removeStrategiesFromDepositWhitelist(IStrategy[] calldata strategiesToRemoveFromWhitelist) external onlyStrategyWhitelister {
608:        uint256 strategiesToRemoveFromWhitelistLength = strategiesToRemoveFromWhitelist.length;
611:            if (strategyIsWhitelistedForDeposit[strategiesToRemoveFromWhitelist[i]]) {
612:                strategyIsWhitelistedForDeposit[strategiesToRemoveFromWhitelist[i]] = false;
613:                emit StrategyRemovedFromDepositWhitelist(strategiesToRemoveFromWhitelist[i]);
629:    function _addShares(address depositor, IStrategy strategy, uint256 shares) internal {
631:        require(depositor != address(0), "StrategyManager._addShares: depositor cannot be zero address");
632:        require(shares != 0, "StrategyManager._addShares: shares should not be zero!");
637:                stakerStrategyList[depositor].length < MAX_STAKER_STRATEGY_LIST_LENGTH,
638:                "StrategyManager._addShares: deposit would exceed MAX_STAKER_STRATEGY_LIST_LENGTH"
655:    function _depositIntoStrategy(address depositor, IStrategy strategy, IERC20 token, uint256 amount)
679:    function _removeShares(address depositor, uint256 strategyIndex, IStrategy strategy, uint256 shareAmount)
684:        require(depositor != address(0), "StrategyManager._removeShares: depositor cannot be zero address");
685:        require(shareAmount != 0, "StrategyManager._removeShares: shareAmount should not be zero!");
690:        require(shareAmount <= userShares, "StrategyManager._removeShares: shareAmount too high");
701:            _removeStrategyFromStakerStrategyList(depositor, strategyIndex, strategy);
715:    function _removeStrategyFromStakerStrategyList(address depositor, uint256 strategyIndex, IStrategy strategy) internal {
720:                stakerStrategyList[depositor][stakerStrategyList[depositor].length - 1];
722:            //loop through all of the strategies, find the right one, then replace
728:                    stakerStrategyList[depositor][j] = stakerStrategyList[depositor][stakerStrategyList[depositor].length - 1];
736:            require(j != stratsLength, "StrategyManager._removeStrategyFromStakerStrategyList: strategy not found");
745:    function _completeQueuedWithdrawal(QueuedWithdrawal calldata queuedWithdrawal, IERC20[] calldata tokens, uint256 middlewareTimesIndex, bool receiveAsTokens) onlyNotFrozen(queuedWithdrawal.delegatedAddress) internal {
752:            "StrategyManager.completeQueuedWithdrawal: withdrawal is not pending"
756:            slasher.canWithdraw(queuedWithdrawal.delegatedAddress, queuedWithdrawal.withdrawalStartBlock, middlewareTimesIndex),
757:            "StrategyManager.completeQueuedWithdrawal: shares pending withdrawal are still slashable"
761:        require(queuedWithdrawal.withdrawalStartBlock + withdrawalDelayBlocks <= block.number
763:            "StrategyManager.completeQueuedWithdrawal: withdrawalDelayBlocks period has not yet passed"
768:            "StrategyManager.completeQueuedWithdrawal: only specified withdrawer can complete a queued withdrawal"
778:            require(tokens.length == queuedWithdrawal.strategies.length, "StrategyManager.completeQueuedWithdrawal: input length mismatch");
784:                    _withdrawBeaconChainETH(queuedWithdrawal.depositor, msg.sender, queuedWithdrawal.shares[i]);
798:                _addShares(msg.sender, queuedWithdrawal.strategies[i], queuedWithdrawal.shares[i]);
804:        emit WithdrawalCompleted(queuedWithdrawal.depositor, queuedWithdrawal.withdrawerAndNonce.nonce, msg.sender, withdrawalRoot);
812:        require(stakerStrategyList[depositor].length == 0, "StrategyManager._undelegate: depositor has active deposits");
821:    function _withdrawBeaconChainETH(address staker, address recipient, uint256 amount) internal {
822:        uint256 amountToDecrement = beaconChainETHSharesToDecrementOnWithdrawal[staker];
829:                beaconChainETHSharesToDecrementOnWithdrawal[staker] = (amountToDecrement - amount);
835:        eigenPodManager.withdrawRestakedBeaconChainETH(staker, recipient, amount);
839:    function _setWithdrawalDelayBlocks(uint256 _withdrawalDelayBlocks) internal {
840:        require(_withdrawalDelayBlocks <= MAX_WITHDRAWAL_DELAY_BLOCKS, "StrategyManager.setWithdrawalDelay: _withdrawalDelayBlocks too high");
841:        emit WithdrawalDelayBlocksSet(withdrawalDelayBlocks, _withdrawalDelayBlocks);
847:        emit StrategyWhitelisterChanged(strategyWhitelister, newStrategyWhitelister);
857:    function getDeposits(address depositor) external view returns (IStrategy[] memory, uint256[] memory) {
862:            shares[i] = stakerStrategyShares[depositor][stakerStrategyList[depositor][i]];
871:    function stakerStrategyListLength(address staker) external view returns (uint256) {
876:    function calculateWithdrawalRoot(QueuedWithdrawal memory queuedWithdrawal) public pure returns (bytes32) {
```

```solidity
File:2023-04-eigenlayer/src/contracts/core/StrategyManagerStorage.sol
18:        keccak256("EIP712Domain(string name,uint256 chainId,address verifyingContract)");
21:        keccak256("Deposit(address strategy,address token,uint256 amount,uint256 nonce,uint256 expiry)");
49:    mapping(address => mapping(IStrategy => uint256)) public stakerStrategyShares;
68:    mapping(address => uint256) public beaconChainETHSharesToDecrementOnWithdrawal;
70:    IStrategy public constant beaconChainETHStrategy = IStrategy(0xbeaC0eeEeeeeEEeEeEEEEeeEEeEeeeEeeEEBEaC0);
72:    constructor(IDelegationManager _delegation, IEigenPodManager _eigenPodManager, ISlasher _slasher) {
```

```solidity
File:2023-04-eigenlayer/src/contracts/strategies/StrategyBase.sol
41:        require(msg.sender == address(strategyManager), "StrategyBase.onlyStrategyManager");
51:    function initialize(IERC20 _underlyingToken, IPauserRegistry _pauserRegistry) public virtual initializer {
56:    function _initializeStrategyBase(IERC20 _underlyingToken, IPauserRegistry _pauserRegistry) internal onlyInitializing {
86:        require(token == underlyingToken, "StrategyBase.deposit: Can only deposit underlyingToken");
104:        require(newShares != 0, "StrategyBase.deposit: newShares cannot be zero");
107:            "StrategyBase.deposit: updated totalShares amount would be nonzero but below MIN_NONZERO_TOTAL_SHARES");
128:        require(token == underlyingToken, "StrategyBase.withdraw: Can only withdraw the strategy token");
133:            "StrategyBase.withdraw: amountShares must be less than or equal to totalShares"
139:        require(updatedTotalShares >= MIN_NONZERO_TOTAL_SHARES || updatedTotalShares == 0,
140:            "StrategyBase.withdraw: updated totalShares amount would be nonzero but below MIN_NONZERO_TOTAL_SHARES");
162:    function explanation() external pure virtual override returns (string memory) {
163:        return "Base Strategy implementation to inherit from for more complex implementations";
172:    function sharesToUnderlyingView(uint256 amountShares) public view virtual override returns (uint256) {
186:    function sharesToUnderlying(uint256 amountShares) public view virtual override returns (uint256) {
196:    function underlyingToSharesView(uint256 amountUnderlying) public view virtual returns (uint256) {
211:    function underlyingToShares(uint256 amountUnderlying) external view virtual returns (uint256) {
219:    function userUnderlyingView(address user) external view virtual returns (uint256) {
236:        return strategyManager.stakerStrategyShares(user, IStrategy(address(this)));
```

```solidity
File:2023-04-eigenlayer/src/contracts/pods/EigenPodManager.sol
31:contract EigenPodManager is Initializable, OwnableUpgradeable, Pausable, IEigenPodManager, EigenPodPausingConstants {
37:    bytes internal constant beaconProxyBytecode = hex"608060405260405161090e38038061090e83398101604081905261002291610460565b61002e82826000610035565b505061058a565b61003e83610100565b6040516001600160a01b038416907f1cf3b03a6cf19fa2baba4df148e9dcabedea7f8a5c07840e207e5c089be95d3e90600090a260008251118061007f5750805b156100fb576100f9836001600160a01b0316635c60da1b6040518163ffffffff1660e01b8152600401602060405180830381865afa1580156100c5573d6000803e3d6000fd5b505050506040513d601f19601f820116820180604052508101906100e99190610520565b836102a360201b6100291760201c565b505b505050565b610113816102cf60201b6100551760201c565b6101725760405162461bcd60e51b815260206004820152602560248201527f455243313936373a206e657720626561636f6e206973206e6f74206120636f6e6044820152641d1c9858dd60da1b60648201526084015b60405180910390fd5b6101e6816001600160a01b0316635c60da1b6040518163ffffffff1660e01b8152600401602060405180830381865afa1580156101b3573d6000803e3d6000fd5b505050506040513d601f19601f820116820180604052508101906101d79190610520565b6102cf60201b6100551760201c565b61024b5760405162461bcd60e51b815260206004820152603060248201527f455243313936373a20626561636f6e20696d706c656d656e746174696f6e206960448201526f1cc81b9bdd08184818dbdb9d1c9858dd60821b6064820152608401610169565b806102827fa3f0ad74e5423aebfd80d3ef4346578335a9a72aeaee59ff6cb3582b35133d5060001b6102de60201b6100641760201c565b80546001600160a01b0319166001600160a01b039290921691909117905550565b60606102c883836040518060600160405280602781526020016108e7602791396102e1565b9392505050565b6001600160a01b03163b151590565b90565b6060600080856001600160a01b0316856040516102fe919061053b565b600060405180830381855af49150503d8060008114610339576040519150601f19603f3d011682016040523d82523d6000602084013e61033e565b606091505b5090925090506103508683838761035a565b9695505050505050565b606083156103c65782516103bf576001600160a01b0385163b6103bf5760405162461bcd60e51b815260206004820152601d60248201527f416464726573733a2063616c6c20746f206e6f6e2d636f6e74726163740000006044820152606401610169565b50816103d0565b6103d083836103d8565b949350505050565b8151156103e85781518083602001fd5b8060405162461bcd60e51b81526004016101699190610557565b80516001600160a01b038116811461041957600080fd5b919050565b634e487b7160e01b600052604160045260246000fd5b60005b8381101561044f578181015183820152602001610437565b838111156100f95750506000910152565b6000806040838503121561047357600080fd5b61047c83610402565b60208401519092506001600160401b038082111561049957600080fd5b818501915085601f8301126104ad57600080fd5b8151818111156104bf576104bf61041e565b604051601f8201601f19908116603f011681019083821181831017156104e7576104e761041e565b8160405282815288602084870101111561050057600080fd5b610511836020830160208801610434565b80955050505050509250929050565b60006020828403121561053257600080fd5b6102c882610402565b6000825161054d818460208701610434565b9190910192915050565b6020815260008251806020840152610576816040850160208701610434565b601f01601f19169190910160400192915050565b61034e806105996000396000f3fe60806040523661001357610011610017565b005b6100115b610027610022610067565b610100565b565b606061004e83836040518060600160405280602781526020016102f260279139610124565b9392505050565b6001600160a01b03163b151590565b90565b600061009a7fa3f0ad74e5423aebfd80d3ef4346578335a9a72aeaee59ff6cb3582b35133d50546001600160a01b031690565b6001600160a01b0316635c60da1b6040518163ffffffff1660e01b8152600401602060405180830381865afa1580156100d7573d6000803e3d6000fd5b505050506040513d601f19601f820116820180604052508101906100fb9190610249565b905090565b3660008037600080366000845af43d6000803e80801561011f573d6000f35b3d6000fd5b6060600080856001600160a01b03168560405161014191906102a2565b600060405180830381855af49150503d806000811461017c576040519150601f19603f3d011682016040523d82523d6000602084013e610181565b606091505b50915091506101928683838761019c565b9695505050505050565b6060831561020d578251610206576001600160a01b0385163b6102065760405162461bcd60e51b815260206004820152601d60248201527f416464726573733a2063616c6c20746f206e6f6e2d636f6e747261637400000060448201526064015b60405180910390fd5b5081610217565b610217838361021f565b949350505050565b81511561022f5781518083602001fd5b8060405162461bcd60e51b81526004016101fd91906102be565b60006020828403121561025b57600080fd5b81516001600160a01b038116811461004e57600080fd5b60005b8381101561028d578181015183820152602001610275565b8381111561029c576000848401525b50505050565b600082516102b4818460208701610272565b9190910192915050565b60208152600082518060208401526102dd816040850160208701610272565b601f01601f1916919091016040019291505056fe416464726573733a206c6f772d6c6576656c2064656c65676174652063616c6c206661696c6564a2646970667358221220d51e81d3bc5ed20a26aeb05dce7e825c503b2061aa78628027300c8d65b9d89a64736f6c634300080c0033416464726573733a206c6f772d6c6576656c2064656c65676174652063616c6c206661696c6564";
67:        require(address(ownerToPod[podOwner]) == msg.sender, "EigenPodManager.onlyEigenPod: not a pod");
72:        require(msg.sender == address(strategyManager), "EigenPodManager.onlyStrategyManager: not strategyManager");
76:    constructor(IETHPOSDeposit _ethPOS, IBeacon _eigenPodBeacon, IStrategyManager _strategyManager, ISlasher _slasher) {
100:        require(!hasPod(msg.sender), "EigenPodManager.createPod: Sender already has a pod");
112:    function stake(bytes calldata pubkey, bytes calldata signature, bytes32 depositDataRoot) external payable {
127:    function restakeBeaconChainETH(address podOwner, uint256 amount) external onlyEigenPod(podOwner) {
139:    function recordOvercommittedBeaconChainETH(address podOwner, uint256 beaconChainETHStrategyIndex, uint256 amount) external onlyEigenPod(podOwner) {
140:        strategyManager.recordOvercommittedBeaconChainETH(podOwner, beaconChainETHStrategyIndex, amount);
150:    function withdrawRestakedBeaconChainETH(address podOwner, address recipient, uint256 amount)
151:        external onlyStrategyManager onlyWhenNotPaused(PAUSED_WITHDRAW_RESTAKED_ETH)
161:    function updateBeaconChainOracle(IBeaconChainOracle newBeaconChainOracle) external onlyOwner {
166:    function _deployPod() internal onlyWhenNotPaused(PAUSED_NEW_EIGENPODS) returns (IEigenPod) {
186:    function _updateBeaconChainOracle(IBeaconChainOracle newBeaconChainOracle) internal {
215:    function getBeaconChainStateRoot(uint64 blockNumber) external view returns(bytes32) {
216:        bytes32 stateRoot = beaconChainOracle.beaconStateRootAtBlockNumber(blockNumber);
217:        require(stateRoot != bytes32(0), "EigenPodManager.getBeaconChainStateRoot: state root at blockNumber not yet finalized");
```

```solidity
File:2023-04-eigenlayer/src/contracts/pods/EigenPod.sol
6:import "@openzeppelin-upgrades/contracts/security/ReentrancyGuardUpgradeable.sol";
34:contract EigenPod is IEigenPod, Initializable, ReentrancyGuardUpgradeable, EigenPodPausingConstants {
91:    event FullWithdrawalRedeemed(uint40 validatorIndex, address indexed recipient, uint64 withdrawalAmountGwei);
94:    event PartialWithdrawalRedeemed(uint40 validatorIndex, address indexed recipient, uint64 partialWithdrawalAmountGwei);
97:    event RestakedBeaconChainETHWithdrawn(address indexed recipient, uint256 amount);
100:        require(msg.sender == address(eigenPodManager), "EigenPod.onlyEigenPodManager: not eigenPodManager");
105:        require(msg.sender == podOwner, "EigenPod.onlyEigenPodOwner: not podOwner");
110:        require(!eigenPodManager.slasher().isFrozen(podOwner), "EigenPod.onlyNotFrozen: pod owner is frozen");
115:        require(!hasRestaked, "EigenPod.hasNeverRestaked: restaking is enabled");
122:            "EigenPod.proofIsForValidBlockNumber: beacon chain proof must be for block number after mostRecentWithdrawalBlockNumber");
132:        require(!IPausable(address(eigenPodManager)).paused(index), "EigenPod.onlyWhenNotPaused: index is paused in EigenPodManager");
147:        require(_REQUIRED_BALANCE_WEI % GWEI_TO_WEI == 0, "EigenPod.contructor: _REQUIRED_BALANCE_WEI is not a whole number of gwei");
153:        require(_podOwner != address(0), "EigenPod.initialize: podOwner cannot be zero address");
158:    function stake(bytes calldata pubkey, bytes calldata signature, bytes32 depositDataRoot) external payable onlyEigenPodManager {
160:        require(msg.value == 32 ether, "EigenPod.stake: must initially stake for any validator with 32 ether");
161:        ethPOS.deposit{value : 32 ether}(pubkey, _podWithdrawalCredentials(), signature, depositDataRoot);
187:            "EigenPod.verifyCorrectWithdrawalCredentials: Validator must be inactive to prove withdrawal credentials");
189:        require(validatorFields[BeaconChainProofs.VALIDATOR_WITHDRAWAL_CREDENTIALS_INDEX] == bytes32(_podWithdrawalCredentials()),
190:            "EigenPod.verifyCorrectWithdrawalCredentials: Proof is not for this EigenPod");
192:        uint64 validatorCurrentBalanceGwei = BeaconChainProofs.getBalanceFromBalanceRoot(validatorIndex, proofs.balanceRoot);
196:            "EigenPod.verifyCorrectWithdrawalCredentials: ETH validator's balance must be greater than or equal to the restaked balance per validator");
199:        bytes32 beaconStateRoot = eigenPodManager.getBeaconChainStateRoot(oracleBlockNumber);
249:        require(oracleBlockNumber + VERIFY_OVERCOMMITTED_WINDOW_BLOCKS >= block.number,
250:            "EigenPod.verifyOvercommittedStake: specified blockNumber is too far in past");
252:        require(validatorStatus[validatorIndex] == VALIDATOR_STATUS.ACTIVE, "EigenPod.verifyOvercommittedStake: Validator not active");
255:        uint64 validatorCurrentBalanceGwei = BeaconChainProofs.getBalanceFromBalanceRoot(validatorIndex, proofs.balanceRoot);
258:            "EigenPod.verifyOvercommittedStake: validator's balance must be less than the restaked balance per validator");
261:        bytes32 beaconStateRoot = eigenPodManager.getBeaconChainStateRoot(oracleBlockNumber);
269:            uint64 slashedStatus = Endian.fromLittleEndianUint64(validatorFields[BeaconChainProofs.VALIDATOR_SLASHED_INDEX]);
270:            require(slashedStatus == 1, "EigenPod.verifyOvercommittedStake: Validator must be slashed to be overcommitted");
271:            //Verify the validator fields, which contain the validator's slashed status
293:        eigenPodManager.recordOvercommittedBeaconChainETH(podOwner, beaconChainETHStrategyIndex, REQUIRED_BALANCE_WEI);
325:        proofIsForValidBlockNumber(Endian.fromLittleEndianUint64(withdrawalProofs.blockNumberRoot))
331:        uint40 validatorIndex = uint40(Endian.fromLittleEndianUint64(withdrawalFields[BeaconChainProofs.WITHDRAWAL_VALIDATOR_INDEX_INDEX]));
334:            "EigenPod.verifyOvercommittedStake: Validator never proven to have withdrawal credentials pointed to this contract");
337:        bytes32 beaconStateRoot = eigenPodManager.getBeaconChainStateRoot(oracleBlockNumber);
340:        BeaconChainProofs.verifyWithdrawalProofs(beaconStateRoot, withdrawalProofs, withdrawalFields);
342:        BeaconChainProofs.verifyValidatorFields(validatorIndex, beaconStateRoot, validatorFieldsProof, validatorFields);
344:        uint64 withdrawalAmountGwei = Endian.fromLittleEndianUint64(withdrawalFields[BeaconChainProofs.WITHDRAWAL_VALIDATOR_AMOUNT_INDEX]);
354:        if (Endian.fromLittleEndianUint64(validatorFields[BeaconChainProofs.VALIDATOR_WITHDRAWABLE_EPOCH_INDEX]) <= slot/BeaconChainProofs.SLOTS_PER_EPOCH) {
355:            _processFullWithdrawal(withdrawalAmountGwei, validatorIndex, beaconChainETHStrategyIndex, podOwner, validatorStatus[validatorIndex]);
357:            _processPartialWithdrawal(slot, withdrawalAmountGwei, validatorIndex, podOwner);
375:                amountToSend = uint256(withdrawalAmountGwei - REQUIRED_BALANCE_GWEI) * uint256(GWEI_TO_WEI);
382:                eigenPodManager.recordOvercommittedBeaconChainETH(podOwner, beaconChainETHStrategyIndex, uint256(REQUIRED_BALANCE_GWEI - withdrawalAmountGwei) * GWEI_TO_WEI);
389:                amountToSend = uint256(withdrawalAmountGwei - REQUIRED_BALANCE_GWEI) * uint256(GWEI_TO_WEI);
396:                eigenPodManager.restakeBeaconChainETH(podOwner, REQUIRED_BALANCE_WEI);
404:                eigenPodManager.restakeBeaconChainETH(podOwner, uint256(withdrawalAmountGwei) * GWEI_TO_WEI);
408:            revert("EigenPod.verifyBeaconChainFullWithdrawal: VALIDATOR_STATUS is WITHDRAWN or invalid VALIDATOR_STATUS");
414:        emit FullWithdrawalRedeemed(validatorIndex, recipient, withdrawalAmountGwei);
422:    function _processPartialWithdrawal(uint64 withdrawalHappenedSlot, uint64 partialWithdrawalAmountGwei, uint40 validatorIndex, address recipient) internal {
423:        require(!provenPartialWithdrawal[validatorIndex][withdrawalHappenedSlot], "EigenPod._processPartialWithdrawal: partial withdrawal has already been proven for this slot");
426:        emit PartialWithdrawalRedeemed(validatorIndex, recipient, partialWithdrawalAmountGwei);
429:        _sendETH(recipient, uint256(partialWithdrawalAmountGwei) * uint256(GWEI_TO_WEI));
454:    function withdrawBeforeRestaking() external onlyEigenPodOwner hasNeverRestaked {
465:        delayedWithdrawalRouter.createDelayedWithdrawal{value: amountWei}(podOwner, recipient);
```

```solidity
File:2023-04-eigenlayer/src/contracts/pods/DelayedWithdrawalRouter.sol
6:import "@openzeppelin-upgrades/contracts/security/ReentrancyGuardUpgradeable.sol";
11:contract DelayedWithdrawalRouter is Initializable, OwnableUpgradeable, ReentrancyGuardUpgradeable, Pausable, IDelayedWithdrawalRouter {
33:    event DelayedWithdrawalCreated(address podOwner, address recipient, uint256 amount, uint256 index);
36:    event DelayedWithdrawalsClaimed(address recipient, uint256 amountClaimed, uint256 delayedWithdrawalsCompleted);
40:        require(address(eigenPodManager.getPod(podOwner)) == msg.sender, "DelayedWithdrawalRouter.onlyEigenPod: not podOwner's EigenPod");
45:        require(address(_eigenPodManager) != address(0), "DelayedWithdrawalRouter.constructor: _eigenPodManager cannot be zero address");
49:    function initialize(address initOwner, IPauserRegistry _pauserRegistry, uint256 initPausedStatus, uint256 _withdrawalDelayBlocks) external initializer {
59:    function createDelayedWithdrawal(address podOwner, address recipient) external payable onlyEigenPod(podOwner) {
66:            _userWithdrawals[recipient].delayedWithdrawals.push(delayedWithdrawal);
67:            emit DelayedWithdrawalCreated(podOwner, recipient, withdrawalAmount, _userWithdrawals[recipient].delayedWithdrawals.length - 1);
79:    function claimDelayedWithdrawals(address recipient, uint256 maxNumberOfDelayedWithdrawalsToClaim)
84:        _claimDelayedWithdrawals(recipient, maxNumberOfDelayedWithdrawalsToClaim);
91:    function claimDelayedWithdrawals(uint256 maxNumberOfDelayedWithdrawalsToClaim)
96:        _claimDelayedWithdrawals(msg.sender, maxNumberOfDelayedWithdrawalsToClaim);
105:    function userWithdrawals(address user) external view returns (UserDelayedWithdrawals memory) {
110:    function claimableUserDelayedWithdrawals(address user) external view returns (DelayedWithdrawal[] memory) {
111:        uint256 delayedWithdrawalsCompleted = _userWithdrawals[user].delayedWithdrawalsCompleted;
112:        uint256 delayedWithdrawalsLength = _userWithdrawals[user].delayedWithdrawals.length;
113:        uint256 claimableDelayedWithdrawalsLength = delayedWithdrawalsLength - delayedWithdrawalsCompleted;
114:        DelayedWithdrawal[] memory claimableDelayedWithdrawals = new DelayedWithdrawal[](claimableDelayedWithdrawalsLength);
116:            claimableDelayedWithdrawals[i] = _userWithdrawals[user].delayedWithdrawals[delayedWithdrawalsCompleted + i];
122:    function userDelayedWithdrawalByIndex(address user, uint256 index) external view returns (DelayedWithdrawal memory) {
127:    function userWithdrawalsLength(address user) external view returns (uint256) {
132:    function canClaimDelayedWithdrawal(address user, uint256 index) external view returns (bool) {
133:        return ((index >= _userWithdrawals[user].delayedWithdrawalsCompleted) && (block.number >= _userWithdrawals[user].delayedWithdrawals[index].blockCreated + withdrawalDelayBlocks));
137:    function _claimDelayedWithdrawals(address recipient, uint256 maxNumberOfDelayedWithdrawalsToClaim) internal {
139:        uint256 delayedWithdrawalsCompletedBefore = _userWithdrawals[recipient].delayedWithdrawalsCompleted;
140:        uint256 _userWithdrawalsLength = _userWithdrawals[recipient].delayedWithdrawals.length;
142:        while (i < maxNumberOfDelayedWithdrawalsToClaim && (delayedWithdrawalsCompletedBefore + i) < _userWithdrawalsLength) {
144:            DelayedWithdrawal memory delayedWithdrawal = _userWithdrawals[recipient].delayedWithdrawals[delayedWithdrawalsCompletedBefore + i];
146:            if (block.number < delayedWithdrawal.blockCreated + withdrawalDelayBlocks) {
157:        _userWithdrawals[recipient].delayedWithdrawalsCompleted = delayedWithdrawalsCompletedBefore + i;
162:        emit DelayedWithdrawalsClaimed(recipient, amountToSend, delayedWithdrawalsCompletedBefore + i);
167:        require(newValue <= MAX_WITHDRAWAL_DELAY_BLOCKS, "DelayedWithdrawalRouter._setWithdrawalDelayBlocks: newValue too large");
```

```solidity
File:2023-04-eigenlayer/src/contracts/libraries/Merkle.sol
48:    function processInclusionProofKeccak(bytes memory proof, bytes32 leaf, uint256 index) internal pure returns (bytes32) {
99:    function processInclusionProofSha256(bytes memory proof, bytes32 leaf, uint256 index) internal view returns (bytes32) {
107:                    if iszero(staticcall(sub(gas(), 2000), 2, 0x00, 0x40, computedHash, 0x20)) {revert(0, 0)}
115:                    if iszero(staticcall(sub(gas(), 2000), 2, 0x00, 0x40, computedHash, 0x20)) {revert(0, 0)}
124:     @notice this function returns the merkle root of a tree created from a set of leaves using sha256 as its hash function
144:            //overwrite the first numNodesInLayer nodes in layer with the pairwise hashes of their children
```

```solidity
File:2023-04-eigenlayer/src/contracts/libraries/BeaconChainProofs.sol
9://SSZ Spec: https://github.com/ethereum/consensus-specs/blob/dev/ssz/simple-serialize.md#merkleization
10://BeaconBlockHeader Spec: https://github.com/ethereum/consensus-specs/blob/dev/specs/phase0/beacon-chain.md#beaconblockheader
11://BeaconState Spec: https://github.com/ethereum/consensus-specs/blob/dev/specs/phase0/beacon-chain.md#beaconstate
53:    //refer to the eigenlayer-cli proof library.  Despite being the same dimensions as the validator tree, the balance tree is merkleized differently
130:    function computePhase0BeaconBlockHeaderRoot(bytes32[NUM_BEACON_BLOCK_HEADER_FIELDS] calldata blockHeaderFields) internal pure returns(bytes32) {
131:        bytes32[] memory paddedHeaderFields = new bytes32[](2**BEACON_BLOCK_HEADER_FIELD_TREE_HEIGHT);
140:    function computePhase0BeaconStateRoot(bytes32[NUM_BEACON_STATE_FIELDS] calldata beaconStateFields) internal pure returns(bytes32) {
141:        bytes32[] memory paddedBeaconStateFields = new bytes32[](2**BEACON_STATE_FIELD_TREE_HEIGHT);
150:    function computePhase0ValidatorRoot(bytes32[NUM_VALIDATOR_FIELDS] calldata validatorFields) internal pure returns(bytes32) {
151:        bytes32[] memory paddedValidatorFields = new bytes32[](2**VALIDATOR_FIELD_TREE_HEIGHT);
160:    function computePhase0Eth1DataRoot(bytes32[NUM_ETH1_DATA_FIELDS] calldata eth1DataFields) internal pure returns(bytes32) {
161:        bytes32[] memory paddedEth1DataFields = new bytes32[](2**ETH1_DATA_FIELD_TREE_HEIGHT);
178:   function getBalanceFromBalanceRoot(uint40 validatorIndex, bytes32 balanceRoot) internal pure returns (uint64) {
180:        bytes32 validatorBalanceLittleEndian = bytes32((uint256(balanceRoot) << bitShiftAmount));
181:        uint64 validatorBalance = Endian.fromLittleEndianUint64(validatorBalanceLittleEndian);
199:        require(validatorFields.length == 2**VALIDATOR_FIELD_TREE_HEIGHT, "BeaconChainProofs.verifyValidatorFields: Validator fields has incorrect length");
205:        require(proof.length == 32 * ((VALIDATOR_TREE_HEIGHT + 1) + BEACON_STATE_FIELD_TREE_HEIGHT), "BeaconChainProofs.verifyValidatorFields: Proof has incorrect length");
206:        uint256 index = (VALIDATOR_TREE_ROOT_INDEX << (VALIDATOR_TREE_HEIGHT + 1)) | uint256(validatorIndex);
211:        require(Merkle.verifyInclusionSha256(proof, beaconStateRoot, validatorRoot, index), "BeaconChainProofs.verifyValidatorFields: Invalid merkle proof");
227:        require(proof.length == 32 * ((BALANCE_TREE_HEIGHT + 1) + BEACON_STATE_FIELD_TREE_HEIGHT), "BeaconChainProofs.verifyValidatorBalance: Proof has incorrect length");
234:        balanceIndex = (BALANCE_INDEX << (BALANCE_TREE_HEIGHT + 1)) | balanceIndex;
236:        require(Merkle.verifyInclusionSha256(proof, beaconStateRoot, balanceRoot, balanceIndex), "BeaconChainProofs.verifyValidatorBalance: Invalid merkle proof");
250:        require(withdrawalFields.length == 2**WITHDRAWAL_FIELD_TREE_HEIGHT, "BeaconChainProofs.verifyWithdrawalProofs: withdrawalFields has incorrect length");
252:        require(proofs.blockHeaderRootIndex < 2**BLOCK_ROOTS_TREE_HEIGHT, "BeaconChainProofs.verifyWithdrawalProofs: blockRootIndex is too large");
253:        require(proofs.withdrawalIndex < 2**WITHDRAWALS_TREE_HEIGHT, "BeaconChainProofs.verifyWithdrawalProofs: withdrawalIndex is too large");
256:        require(proofs.blockHeaderProof.length == 32 * (BEACON_STATE_FIELD_TREE_HEIGHT + BLOCK_ROOTS_TREE_HEIGHT),
257:            "BeaconChainProofs.verifyWithdrawalProofs: blockHeaderProof has incorrect length");
258:        require(proofs.withdrawalProof.length == 32 * (EXECUTION_PAYLOAD_HEADER_FIELD_TREE_HEIGHT + WITHDRAWALS_TREE_HEIGHT + 1),
259:            "BeaconChainProofs.verifyWithdrawalProofs: withdrawalProof has incorrect length");
260:        require(proofs.executionPayloadProof.length == 32 * (BEACON_BLOCK_HEADER_FIELD_TREE_HEIGHT + BEACON_BLOCK_BODY_FIELD_TREE_HEIGHT),
261:            "BeaconChainProofs.verifyWithdrawalProofs: executionPayloadProof has incorrect length");
267:        uint256 blockHeaderIndex = BLOCK_ROOTS_INDEX << (BLOCK_ROOTS_TREE_HEIGHT)  | uint256(proofs.blockHeaderRootIndex);
269:        require(Merkle.verifyInclusionSha256(proofs.blockHeaderProof, beaconStateRoot, proofs.blockHeaderRoot, blockHeaderIndex),
270:            "BeaconChainProofs.verifyWithdrawalProofs: Invalid block header merkle proof");
273:        require(Merkle.verifyInclusionSha256(proofs.slotProof, proofs.blockHeaderRoot, proofs.slotRoot, SLOT_INDEX), "BeaconChainProofs.verifyWithdrawalProofs: Invalid slot merkle proof");
276:        uint256 executionPayloadIndex = BODY_ROOT_INDEX << (BEACON_BLOCK_BODY_FIELD_TREE_HEIGHT)| EXECUTION_PAYLOAD_INDEX ;
277:        require(Merkle.verifyInclusionSha256(proofs.executionPayloadProof, proofs.blockHeaderRoot, proofs.executionPayloadRoot, executionPayloadIndex),
278:            "BeaconChainProofs.verifyWithdrawalProofs: Invalid executionPayload merkle proof");
281:        require(Merkle.verifyInclusionSha256(proofs.blockNumberProof, proofs.executionPayloadRoot, proofs.blockNumberRoot, BLOCK_NUMBER_INDEX),
282:            "BeaconChainProofs.verifyWithdrawalProofs: Invalid blockNumber merkle proof");
291:        uint256 withdrawalIndex = WITHDRAWALS_INDEX << (WITHDRAWALS_TREE_HEIGHT + 1) | uint256(proofs.withdrawalIndex);
293:        require(Merkle.verifyInclusionSha256(proofs.withdrawalProof, proofs.executionPayloadRoot, withdrawalRoot, withdrawalIndex),
294:            "BeaconChainProofs.verifyWithdrawalProofs: Invalid withdrawal merkle proof");
```
*Instances (276)*

### [NC-4]  Using while for unbounded loops isn’t recommended

Don’t write loops that are unbounded as this can hit the gas limit, causing your transaction to fail.

```solidity
File:2023-04-eigenlayer/src/contracts/pods/DelayedWithdrawalRouter.sol
142:        while (i < maxNumberOfDelayedWithdrawalsToClaim && (delayedWithdrawalsCompletedBefore + i) < _userWithdrawalsLength) {
```

```solidity
File:2023-04-eigenlayer/src/contracts/libraries/Merkle.sol
143:        while (numNodesInLayer != 0) {
```
*Instances (2)*

### [NC-5] Lack of event emission after critical initialize() functions

To record the init parameters for off-chain monitoring and transparency reasons, please consider emitting an event after the initialize() functions

```solidity
File:2023-04-eigenlayer/src/contracts/core/StrategyManager.sol

146:    function initialize(address initialOwner, address initialStrategyWhitelister, IPauserRegistry _pauserRegistry, uint256 initialPausedStatus, uint256 _withdrawalDelayBlocks)
```

```solidity
File:2023-04-eigenlayer/src/contracts/pods/DelayedWithdrawalRouter.sol
49:    function initialize(address initOwner, IPauserRegistry _pauserRegistry, uint256 initPausedStatus, uint256 _withdrawalDelayBlocks) external initializer {
```

```solidity
File:2023-04-eigenlayer/src/contracts/pods/EigenPod.sol
152:    function initialize(address _podOwner) external initializer {
```
*Instances (3)*


| |Issue|Instances|
|-|:-|:-:|
| [R-1](#R-1) | Some number values can be refactored with `_` | 3 |
| [R-2](#R-2) | Compute known value `off-chain` | 2 |

### [R-1]  Some number values can be refactored with `_`

Consider using underscores for number values to improve readability.

```solidity
File:2023-04-eigenlayer/src/contracts/core/StrategyManagerStorage.sol
46:    uint256 public constant MAX_WITHDRAWAL_DELAY_BLOCKS = 50400;
```

```solidity
File:2023-04-eigenlayer/src/contracts/pods/EigenPod.sol
41:    uint256 internal constant VERIFY_OVERCOMMITTED_WINDOW_BLOCKS = 50400;
```

```solidity
File:2023-04-eigenlayer/src/contracts/pods/DelayedWithdrawalRouter.sol
24:    uint256 public constant MAX_WITHDRAWAL_DELAY_BLOCKS = 50400;
```
*Instances (3)*

### [R-2] Compute known value `off-chain` 

```solidity
File: src/contracts/core/StrategyManagerStorage.sol

17        bytes32 public constant DOMAIN_TYPEHASH =
18:           keccak256("EIP712Domain(string name,uint256 chainId,address verifyingContract)");

20        bytes32 public constant DEPOSIT_TYPEHASH =
21:           keccak256("Deposit(address strategy,address token,uint256 amount,uint256 nonce,uint256 expiry)");
```
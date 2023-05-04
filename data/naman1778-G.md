## [G-01] Using calldata instead of memory for read-only arguments in external functions saves gas

When a function with a memory array is called externally, the abi.decode() step has to use a for-loop to copy each index of the calldata to the memory index. Each iteration of this for-loop costs at least 60 gas (i.e. 60 * <mem_array>.length). Using calldata directly, obliviates the need for such a loop in the contract code and runtime execution. Note that even if an interface defines a function as having memory arguments, it’s still valid for implementation contracs to use calldata arguments instead.

If the array is passed to an internal function which passes the array to another internal function where the array is modified and therefore memory is used in the external call, it’s still more gass-efficient to use calldata when the external function uses modifiers, since the modifiers may prevent the internal functions from being called. Structs have the same overhead as an array of length one

Note that I’ve also flagged instances where the function is public but can be marked as external since it’s not called by the contract, and cases where a constructor is involved

There is 1 instance of this issue in 1 file:

    File: src/contracts/core/StrategyManager.sol

    248: function depositIntoStrategyWithSignature(
    249:     IStrategy strategy,
    250:     IERC20 token,
    251:     uint256 amount,
    252:     address staker,
    253:     uint256 expiry,
    254:     bytes memory signature
    255: )
    256:     external
    257:     onlyWhenNotPaused(PAUSED_DEPOSITS)
    258:     onlyNotFrozen(staker)
    259:     nonReentrant
    260:     returns (uint256 shares)

https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/core/StrategyManager.sol

## [G-02] abi.encode() is less efficient than abi.encodePacked()

There are 6 instances of this issue in 2 files:

    File: src/contracts/core/StrategyManager.sol

    150: DOMAIN_SEPARATOR = keccak256(abi.encode(DOMAIN_TYPEHASH, keccak256(bytes("EigenLayer")), ORIGINAL_CHAIN_ID, address(this)));

    268: bytes32 structHash = keccak256(abi.encode(DEPOSIT_TYPEHASH, strategy, token, amount, nonce, expiry));

    276: bytes32 domain_separator = keccak256(abi.encode(DOMAIN_TYPEHASH, keccak256(bytes("EigenLayer")), block.chainid, address(this)));

    878: keccak256(
    879:    abi.encode(
    880:        queuedWithdrawal.strategies,
    881:        queuedWithdrawal.shares,
    882:        queuedWithdrawal.depositor,
    883:        queuedWithdrawal.withdrawerAndNonce,
    884:        queuedWithdrawal.withdrawalStartBlock,
    885:        queuedWithdrawal.delegatedAddress
    886:        )
    887:    )
    888: }

https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/core/StrategyManager.sol

    File: src/contracts/pods/EigenPodManager.sol

    175: abi.encode(eigenPodBeacon, "")

    202: abi.encode(eigenPodBeacon, "")

https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/pods/EigenPodManager.sol

## [G-03] Use assembly to check for address(0)

There are 9 instances of this issue in 5 files:

    File: src/contracts/core/StrategyManager.sol

    343: require(withdrawer != address(0), "StrategyManager.queueWithdrawal: cannot withdraw to zero address");

    631: require(depositor != address(0), "StrategyManager._addShares: depositor cannot be zero address");

    684: require(depositor != address(0), "StrategyManager._removeShares: depositor cannot be zero address");

https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/core/StrategyManager.sol

    File: src/contracts/pods/EigenPodManager.sol

    114: if(address(pod) == address(0)) {

    196: if (address(pod) == address(0)) {

    211: return address(ownerToPod[podOwner]) != address(0);

https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/pods/EigenPodManager.sol

    File: src/contracts/pods/EigenPod.sol

    153: require(_podOwner != address(0), "EigenPod.initialize: podOwner cannot be zero address");

https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/pods/EigenPod.sol

    File: src/contracts/pods/DelayedWithdrawalRouter.sol

    45: require(address(_eigenPodManager) != address(0), "DelayedWithdrawalRouter.constructor: _eigenPodManager cannot be zero address");

https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/pods/DelayedWithdrawalRouter.sol

    File: src/contracts/permissions/Pausable.sol

    57: address(pauserRegistry) == address(0) && address(_pauserRegistry) != address(0),

https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/permissions/Pausable.sol

## [G-04] Use nested if and, avoid multiple check combinations

Using nesting is cheaper than using && multiple check combinations. There are more advantages, such as easier to read code and better coverage reports.

There are 2 instances of this issue in 1 file:

     File: src/contracts/core/StrategyManager.sol

     422: if (undelegateIfPossible && stakerStrategyList[msg.sender].length == 0) {

     562: if (indicesToSkipIndex < indicesToSkip.length && indicesToSkip[indicesToSkipIndex] == i) {

https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/core/StrategyManager.sol

## [G-05] Splitting require() statements that use && saves gas

See [this issue](https://github.com/code-423n4/2022-01-xdefi-findings/issues/128) which describes the fact that there is a larger deployment gas cost, but with enough runtime calls, the change ends up being cheaper by 3 gas.

There is 1 instance of this issue in 1 file:

     File: src/contracts/permissions/Pausable.sol

     56: require(
     57:    address(pauserRegistry) == address(0) && address(_pauserRegistry) != address(0),
     58:    "Pausable._initializePauser: _initializePauser() can only be called once"
     59: );

https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/permissions/Pausable.sol

## [G-06] Use assembly to write address storage values

There is 1 instance of this issue in 1 file:

     File: src/contracts/pods/EigenPod.sol

     154: podOwner = _podOwner;

https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/pods/EigenPod.sol

## [G-07] <array>.length should not be looked up in every loop of a for-loop
Note : This is describing an optimization that the sponsor already chose to ignore, to be thorough: https://github.com/code-423n4/2022-08-mimo/tree/main/docs/#for-loop-syntax

Reading array length at each iteration of the loop consumes more gas than necessary.

In the best case scenario (length read on a memory variable), caching the array length in the stack saves around 3 gas per iteration. In the worst case scenario (external calls at each iteration), the amount of gas wasted can be massive.

Here, consider storing the array’s length in a variable before the for-loop, and use this new variable instead.

There are 4 instances of this issue in 2 files:

    File: src/contracts/core/StrategyManager.sol

    358: for (uint256 i = 0; i < strategies.length;) {

    466: for(uint256 i = 0; i < queuedWithdrawals.length; i++) {
     
https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/core/StrategyManager.sol

    File: src/contracts/libraries/Merkle.sol

    50: for (uint256 i = 32; i <= proof.length; i+=32) {

    101: for (uint256 i = 32; i <= proof.length; i+=32) {

https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/libraries/Merkle.sol

## [G-08] Instead of counting down in for statements, count up

Counting down is more gas efficient than counting up because neither we are making zero variable to non-zero variable and also we will get gas refund in the last transaction when making non-zero to zero variable.

There are 17 instances of this issue in 4 files:

    File: src/contracts/core/StrategyManager.sol

    358: for (uint256 i = 0; i < strategies.length;) {

    466: for(uint256 i = 0; i < queuedWithdrawals.length; i++) {

    498: for (uint256 i = 0; i < strategiesLength;) {

    560: for (uint256 i = 0; i < strategiesLength;) {

    594: for (uint256 i = 0; i < strategiesToWhitelistLength;) {

    609: for (uint256 i = 0; i < strategiesToRemoveFromWhitelistLength;) {

    724: uint256 j = 0;
    725: for (; j < stratsLength;) {

    780: for (uint256 i = 0; i < strategiesLength;) {

    797: for (uint256 i = 0; i < strategiesLength;) {

    861: for (uint256 i = 0; i < strategiesLength;) {

https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/core/StrategyManager.sol

    File: src/contracts/pods/DelayedWithdrawalRouter.sol

    115: for (uint256 i = 0; i < claimableDelayedWithdrawalsLength; i++) {

https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/pods/DelayedWithdrawalRouter.sol

    File: src/contracts/libraries/BeaconChainProofs.sol

    133: for (uint256 i = 0; i < NUM_BEACON_BLOCK_HEADER_FIELDS; ++i) {

    143: for (uint256 i = 0; i < NUM_BEACON_STATE_FIELDS; ++i) {

    153: for (uint256 i = 0; i < NUM_VALIDATOR_FIELDS; ++i) {

    163: for (uint256 i = 0; i < ETH1_DATA_FIELD_TREE_HEIGHT; ++i) {

https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/libraries/BeaconChainProofs.sol

    File: src/contracts/libraries/Merkle.sol

    137: for (uint i = 0; i < numNodesInLayer; i++) {

    145: for (uint i = 0; i < numNodesInLayer; i++) {

https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/libraries/Merkle.sol

## [G-09] State variables should be cached in stack variables rather than re-reading them from storage

The instances below point to the second+ access of a state variable within a function. Caching of a state variable replaces each Gwarmaccess (100 gas) with a much cheaper stack read. Other less obvious fixes/optimizations include having local memory caches of state variable structs, or having local caches of state variable contracts/addresses.

There are 11 instance of this issue in 5 files:

    File: src/contracts/core/StrategyManager.sol

    717: if (stakerStrategyList[depositor][strategyIndex] == strategy) {
    720: stakerStrategyList[depositor][stakerStrategyList[depositor].length - 1];
    726: if (stakerStrategyList[depositor][j] == strategy) {
    728: stakerStrategyList[depositor][j] = stakerStrategyList[depositor][stakerStrategyList[depositor].length - 1];

    858: uint256 strategiesLength = stakerStrategyList[depositor].length;
    862: shares[i] = stakerStrategyShares[depositor][stakerStrategyList[depositor][i]];
    867: return (stakerStrategyList[depositor], shares);

    188: uint256 userShares = stakerStrategyShares[overcommittedPodOwner][beaconChainETHStrategy];
    197: _removeShares(overcommittedPodOwner, beaconChainETHStrategyIndex, beaconChainETHStrategy, amount);            
    201: strategies[0] = beaconChainETHStrategy;

https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/core/StrategyManager.sol

    File: src/contracts/strategies/StrategyBase.sol

    92: if (totalShares == 0) {
    99: newShares = (amount * totalShares) / priorTokenBalance;
    105: uint256 updatedTotalShares = totalShares + newShares;

    198: if (tokenBalance == 0 || totalShares == 0) {
    200: return (amountUnderlying * totalShares) / tokenBalance;

https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/strategies/StrategyBase.sol

    File: src/contracts/pods/EigenPod.sol

    333: require(validatorStatus[validatorIndex] != VALIDATOR_STATUS.INACTIVE,
    355: _processFullWithdrawal(withdrawalAmountGwei, validatorIndex, beaconChainETHStrategyIndex, podOwner, validatorStatus[validatorIndex]);
    357: _processPartialWithdrawal(slot, withdrawalAmountGwei, validatorIndex, podOwner);

https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/pods/EigenPod.sol

    File: src/contracts/pods/DelayedWithdrawalRouter.sol

    111: uint256 delayedWithdrawalsCompleted = _userWithdrawals[user].delayedWithdrawalsCompleted;
    112: uint256 delayedWithdrawalsLength = _userWithdrawals[user].delayedWithdrawals.length;
    116: claimableDelayedWithdrawals[i] = _userWithdrawals[user].delayedWithdrawals[delayedWithdrawalsCompleted + i];

    133: return ((index >= _userWithdrawals[user].delayedWithdrawalsCompleted) && (block.number >= _userWithdrawals[user].delayedWithdrawals[index].blockCreated + withdrawalDelayBlocks));

    139: uint256 delayedWithdrawalsCompletedBefore = _userWithdrawals[recipient].delayedWithdrawalsCompleted;
    140: uint256 _userWithdrawalsLength = _userWithdrawals[recipient].delayedWithdrawals.length;
    144: DelayedWithdrawal memory delayedWithdrawal = _userWithdrawals[recipient].delayedWithdrawals[delayedWithdrawalsCompletedBefore + i];
    147: _userWithdrawals[recipient].delayedWithdrawalsCompleted = delayedWithdrawalsCompletedBefore + i;

https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/pods/DelayedWithdrawalRouter.sol

    File: src/contracts/permissions/Pausable.sol

    73: require((_paused & newPausedStatus) == _paused, "Pausable.pause: invalid attempt to unpause functionality");

    94: require(((~_paused) & (~newPausedStatus)) == (~_paused), "Pausable.unpause: invalid attempt to pause functionality");

https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/permissions/Pausable.sol

## [G-10] The result of function calls should be cached rather than re-calling the function

The instances below point to the second+ call of the function within a single function. Every external call made to a contract incurs at least 100 gas of overhead.

There is 1 instance of this issue in 1 file:

    File: src/contracts/strategies/StrategyBase.sol

    150: amountToSend = _tokenBalance();
    152: amountToSend = (_tokenBalance() * amountShares) / priorTotalShares;

https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/strategies/StrategyBase.sol

## [G-11] Gas savings can be achieved by changing the model for assigning value to the structure (260 gas)

By changing the pattern of assigning value to the structure, gas savings of ~130 per instance are achieved. In addition, this use will provide significant savings in distribution costs.

There are 2 instances of this issue in 1 file:

     File: src/contracts/core/StrategyManager.sol

     390: WithdrawerAndNonce memory withdrawerAndNonce = WithdrawerAndNonce({
     391:     withdrawer: withdrawer,
     392:     nonce: nonce
     394: });

     400: queuedWithdrawal = QueuedWithdrawal({
     401:       strategies: strategies,
     402:       shares: shares,
     403:       depositor: msg.sender,
     404:       withdrawerAndNonce: withdrawerAndNonce,
     405:       withdrawalStartBlock: uint32(block.number),
     406:       delegatedAddress: delegatedAddress
     407:   });

https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/core/StrategyManager.sol
# Issue 1 - Potential Rounding Error

## Description

In src/contracts/strategies/StrategyBase.sol, some functions have the potential for rounding errors, as it makes use of integer arithmetic for calculations involving shares and the underlying token amounts. Solidity does not support floating-point numbers, so integer arithmetic is used, which can lead to truncation and rounding errors in certain cases. Here are a few places where rounding errors can occur:

In the deposit() function:

``
newShares = (amount * totalShares) / priorTokenBalance;
``

In the withdraw() function:

``
amountToSend = (_tokenBalance() * amountShares) / priorTotalShares;
``

In the sharesToUnderlyingView() function:

``
return (_tokenBalance() * amountShares) / totalShares;
``

In the underlyingToSharesView() function:
``
return (amountUnderlying * totalShares) / tokenBalance;
``

These calculations involve multiplication followed by division, which can lead to truncation and rounding errors, especially when the numbers involved are small.

## Locations
src/contracts/strategies/StrategyBase.sol#99,152,176,201

## Suggestion

To mitigate rounding errors, you can use libraries like SafeMath or implement custom logic to handle rounding more accurately. Another option is to work with larger units of the underlying token, for example, using the smallest possible denomination (e.g., wei for Ether or the smallest unit for ERC20 tokens) to reduce the impact of rounding errors. However, this approach may not eliminate rounding errors completely, so it's essential to carefully consider the potential consequences and edge cases that may arise from these errors.


# Issue 2 - No hard limit on the deposit amount
## Description
There is no hard limit on the deposit amount in the `deposit` function, which might lead to an unbounded increase in the token balance. 

## Locations
src/contracts/strategies/StrategyBase.sol#78-112

## Suggestion
To resolve the issue of not having a limit on deposit amounts, you can implement a hard cap on the deposit amount for each user or transaction. Here's an example of how you can modify the `deposit` function to include a deposit limit:

```solidity
// Add a new constant for the maximum deposit amount
uint256 public constant MAX_DEPOSIT_AMOUNT = /*desired maximum deposit amount*/;

function deposit(IERC20 token, uint256 amount)
    external
    virtual
    override
    onlyWhenNotPaused(PAUSED_DEPOSITS)
    onlyStrategyManager
    returns (uint256 newShares)
{
    require(token == underlyingToken, "StrategyBase.deposit: Can only deposit underlyingToken");
    
    // Check if the deposit amount is within the allowed limit
    require(amount <= MAX_DEPOSIT_AMOUNT, "StrategyBase.deposit: Deposit amount exceeds the maximum limit");

    // ... rest of the function
}
```

Adjust the `MAX_DEPOSIT_AMOUNT` constant to the maximum deposit amount that you want to allow. This will ensure that the deposit amount does not exceed the specified limit, mitigating the potential risks associated with unlimited deposits.


# Issue 3 - Redundant View Functions
## Description
`sharesToUnderlyingView` and `sharesToUnderlying` functions are identical, as are `underlyingToSharesView` and `underlyingToShares`. 

## Locations
src/contracts/strategies/StrategyBase.sol#172,186,196,211

## Suggestion
Consider removing the duplicate functions and renaming the remaining view functions to simplify the contract. Actually you can merge `sharesToUnderlyingView` and `sharesToUnderlying` functions into one function and merge `underlyingToSharesView` and `underlyingToShares` into one function. 

# Issue 4 - Potential Front-running attacks on `claimDelayedWithdrawals` function
## Description
It's possible to attempt a front-running attack specifically on the `claimDelayedWithdrawals` function. However, the impact of the attack is limited as the attacker cannot control the recipient of the funds.

In a front-running attack, a malicious actor attempts to manipulate the order of transactions by observing pending transactions and then submitting their own transaction with a higher gas price to ensure it gets mined first. This can give the attacker an unfair advantage in certain scenarios.

In the provided code, the `claimDelayedWithdrawals` function allow users to claim their delayed withdrawals after a certain number of blocks have passed since the withdrawal was created. A front-runner could monitor the pending transactions and attempt to call `claimDelayedWithdrawals` with a higher gas price for a recipient they observe in the transaction pool. However, the attacker cannot change the recipient of the funds, and the funds will still be sent to the intended recipient.

While the attacker can trigger the withdrawal earlier than the recipient intended, the impact is limited since they cannot control where the funds are sent. In this scenario, the attacker would only be wasting their own gas fees without any real advantage.

## Locations
src/contracts/pods/DelayedWithdrawalRouter.sol#79

## Suggestion
In the context of the provided contract, a commit-reveal scheme can help mitigate the risk of front-running attacks on the `claimDelayedWithdrawals` function. Here's a simple example of how to implement a commit-reveal scheme for this purpose:

1. Add a new mapping to store commit hashes:

```solidity
mapping(address => bytes32) private _commitHashes;
```

2. Add a new function for users to commit their intention to claim withdrawals:

```solidity
function commitToClaimWithdrawals(bytes32 commitHash) external {
    require(commitHash != 0, "Invalid commit hash");
    _commitHashes[msg.sender] = commitHash;
}
```

3. Add a reveal function that accepts the same parameters as the original `claimDelayedWithdrawals` function, plus a nonce that was used to create the commit hash:

```solidity
function revealAndClaimDelayedWithdrawals(
    address recipient,
    uint256 maxNumberOfDelayedWithdrawalsToClaim,
    uint256 nonce
) external nonReentrant onlyWhenNotPaused(PAUSED_DELAYED_WITHDRAWAL_CLAIMS) {
    // Verify the commit hash
    bytes32 commitHash = _commitHashes[msg.sender];
    require(commitHash != 0, "No commit found");
    require(keccak256(abi.encodePacked(recipient, maxNumberOfDelayedWithdrawalsToClaim, nonce)) == commitHash, "Invalid reveal parameters");

    // Remove the commit hash to prevent reuse
    delete _commitHashes[msg.sender];

    // Call the original claimDelayedWithdrawals function
    _claimDelayedWithdrawals(recipient, maxNumberOfDelayedWithdrawalsToClaim);
}
```

With these changes, users would first commit to claim their withdrawals by providing a commit hash, which is the keccak256 hash of the recipient, maxNumberOfDelayedWithdrawalsToClaim, and a random nonce. They can generate the commit hash off-chain using a tool like web3.js or ethers.js.

After committing, users can call the `revealAndClaimDelayedWithdrawals` function by providing the original parameters (recipient and maxNumberOfDelayedWithdrawalsToClaim) along with the nonce they used to create the commit hash. The contract will then verify the provided parameters against the stored commit hash and process the withdrawal claim if the verification is successful.

This commit-reveal scheme makes it difficult for attackers to front-run the withdrawal claims, as they won't know the correct parameters (including the nonce) until the user reveals them. However, it does introduce additional complexity and requires users to perform two separate transactions to claim their withdrawals.

# Issue 5 - Event Emission Order problem
## Description
In the `_setWithdrawalDelayBlocks` function, the event `WithdrawalDelayBlocksSet` is emitted before updating the `withdrawalDelayBlocks` variable. It is generally recommended to emit events after updating the state to ensure the event reflects the final state of the contract.

## Locations
src/contracts/pods/DelayedWithdrawalRouter.sol#168-169

## Suggestion
Update the function as follows:

```solidity
function _setWithdrawalDelayBlocks(uint256 newValue) internal {
    require(newValue <= MAX_WITHDRAWAL_DELAY_BLOCKS, "DelayedWithdrawalRouter._setWithdrawalDelayBlocks: newValue too large");
    uint256 previousValue = withdrawalDelayBlocks;
    withdrawalDelayBlocks = newValue;
    emit WithdrawalDelayBlocksSet(previousValue, newValue);
}
```

# Issue 6 - Unused Struct `PartialWithdrawalClaim`
## Description
The struct `PartialWithdrawalClaim` is not used anywhere. 

## Location
src/contracts/interfaces/IEigenPod.sol#30-38

## Suggestion
Double check whether `PartialWithdrawalClaim` struct is useful or not. If not, delete it. I guess it should be useful in the future but not used in the current repo yet. 




# Refactor while-loop to reduce gas usage

In the _claimDelayedWithdrawals function within the DelayedWithdrawalRouter.sol contract, the condition of the while-loop can be rewritten as follows to save gas:

Instead of defining a variable named `_userWithdrawalsLength` and then checking `(delayedWithdrawalsCompletedBefore + i) < _userWithdrawalsLength` on every iteration, define a variable like `_remainingUserWithdrawals = _userWithdrawalsLength - delayedWithdrawalsCompletedBefore`. This way, you don't repeat this calculation on every iteration of the loop and you only have to check `i < _remainingUserWithdrawals`.

This is a diff of the proposed changes:

    function _claimDelayedWithdrawals(address recipient, uint256 maxNumberOfDelayedWithdrawalsToClaim) 
    internal {
             uint256 amountToSend = 0;
             uint256 delayedWithdrawalsCompletedBefore = 
    _userWithdrawals[recipient].delayedWithdrawalsCompleted;
    -        uint256 _userWithdrawalsLength = _userWithdrawals[recipient].delayedWithdrawals.length;
    +        uint256 _remainingUserWithdrawals = _userWithdrawals[recipient].delayedWithdrawals.length - 
    delayedWithdrawalsCompletedBefore;
             uint256 i = 0;
    -        while (i < maxNumberOfDelayedWithdrawalsToClaim && (delayedWithdrawalsCompletedBefore + i) < _userWithdrawalsLength) {
    +        while (i < maxNumberOfDelayedWithdrawalsToClaim && i < _remainingUserWithdrawals) {

https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/pods/DelayedWithdrawalRouter.sol#L142
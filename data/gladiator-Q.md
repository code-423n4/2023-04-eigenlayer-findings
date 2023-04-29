The function Slasher.isFrozen is called in the modifier StrategyManager.onlyNotFrozen.

The function verify that the staker or the delegator's staker is not frozen and return true if one of them is frozen false otherwise.

The onlyNotFrozen revert on require with a unique message that stands that staker is frozen and subject to slashing.

The right way would be to have an alternative message that tells the staker that its delegator is frozen.

If an service developped upon EigenLayer want to check if a specific staker (not its operator) is frozen to apply some specific rules, it cannot.

Instead of using boolean, the isFrozen function could return a code to specify the correct frozen situation.

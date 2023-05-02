**src/contracts/core/StrategyManager.sol**
- L191 - The operation amount - userShares could be unchecked since in L190 the pre-check is done, therefore we could save gas by avoiding this check.


**src/contracts/strategies/StrategyBase.sol**
- L137 - The priorTotalShares - amountShares operation could be unchecked since the pre-check is done in L131, therefore we could save gas by avoiding this check.


**src/contracts/interfaces/IServiceManager.sol**
- L16 - The interface creates the function freezeOperator() but it is not used anywhere in the project, therefore it should be eliminated since it generates an unnecessary gas expense.


**src/contracts/interfaces/IDelegationManager.sol**
- L30/54/73/76/79 - The interface creates the functions delegateTo(), operatorShares(), isDelegated(), isNotDelegated() and isDelegated() but they are not used anywhere in the project, so they should be removed since it generates an unnecessary gas expense.


**src/contracts/interfaces/IEigenPodManager.sol**
- L61/64/81 - The interface creates the functions updateBeaconChainOracle(), ownerToPod() and hasPod() but they are not used anywhere in the project, so they should be removed as it wastes unnecessary gas.


**src/contracts/interfaces/IDelayedWithdrawalRouter.sol**
- L43/52 - The interface creates the functions claimableUserDelayedWithdrawals() and canClaimDelayedWithdrawal() but they are not used anywhere in the project, therefore they should be removed as it generates unnecessary gas waste.

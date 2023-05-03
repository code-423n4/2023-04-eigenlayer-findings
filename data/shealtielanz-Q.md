`Low Risk Findings`
# Handle
> shealtielanz.
## Protocol's Name
> EigenLayer.
### Smart Contracts in Scope of this Audit Report
> `StrategyManager`, `Strategy`. `DelayedWithdrawalRouter`, `StrategyBase`, `EigenPod`, `EigenPodManager`
## Tools Used.
> Manual Analysis.
# Report
## Low Risk Findings 
**Summary for The Low Risk Findings List**
| Number | Issue Details | Instances |
|-----:|----|-----|
| L-01 | Missing `Zero Address` checks on Critical functions in the `StrategyManager`. | 2 |
| L-02 | Critical contracts in the `EigenLayer` are Out of Scope. | 2 |
| L-03 | `BeaconChainETHStrategy` is fixed/Hardcoded.  | 1 |
| L-04 | `Intializers` can be frontrun. | 5 |
| L-05 | Constructor Missing the `_disableintializers` funtion call, in the `DelayedWithdrawalRouter` contract   | 1 |

> Total ~ 5 Issues



# [L-01] ~ Missing `Zero Address` checks on Critical functions in the `StrategyManager`
## Summary 
In the `StrategyManager` contract, the functions `slashQueuedWithdWithdraw` & `slashShares` do not contain any check to ensure the `recipient` address isn't `address(0)` which may cause the shares or tokens in a strategy to be lost forever if a wrong input is placed in the parameters.
## Vulnerability Details 
> Instances ~ 2.

> Links to functions in the `StrategyManager` contract.

- [slashShares](https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/core/StrategyManager.sol#L482)
- [slashQueuedWithdrawal](https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/core/StrategyManager.sol#L536)
      
## Impact
**High** as it would transfer the shares of tokens in a strategy to the `address(0)` causing it to be lost forever.
> Likelihood ~ **Low** as the function can only be called by the owner due to the `onlyOwner` modifier.

## Recommended Mitigation Steps
Add a `require` statement to the function to ensure that the `recipient` isn't a `Zero Address`. 
Use the code Statement below to Implement the idea.👇
``` solidity 
 require(recipient!= address(0), " Recipient cannot be a Zero Address");
```


# [L-02] ~ Critical contracts in the `EigenLayer` are Out of Scope.
## Summary.
This Issue stems from the fact that, certain contracts that were placed out of Scope in this audit like The  `Slasher` & `Delegation` Contracts can deeply affect the entire `EigenLayer` Protocol, because it heavily depends on the mechanisms implemented in those Contracts `Out of Scope`, which can be detrimental to an extent of `Stakers` going away with shares after being slashed or causing a DOS for anyone calling certain function in the `StrategyManager` if care isn't taken
## Vulnerability Details.
The Purpose of this Audit as stated by the `Docs` provided is to secure 🔐 and ensure the safety of all the assets inside the `EigenLayer` Protocol but the Contracts given for audits to ensure the previously mentions goals contain logic that heavily relies on the Contracts placed out of Scope, according to Certain `technicalities` and `Logic Flows`

> Instances ~ 2.

Contracts the were placed out of Scope, but affect the `StrategyManager` which is the Head of the `EigenLayer` protocol are 👇
- `ISlasher Contract`
- `IDelegation Contract`
 
**Detailed Explanation**
>  Scenario 1

Imagine a hypothetical scenario where a malicious `depositor` can not only steal funds from any strategy but also bypass all checks made by `isFrozen (...)` and `canwithdraw(...)` avoiding the slashing mechanism to stop them from withdrawing all funds.
Also, let's imagine that to reproduce this attack, the malicious depositor only needs to directly interact with the in-scope contracts and their various `Logic flows` which depends on the contracts placed `out of Scope`,
> Scenario 2

if a `User` ( staker/Operator) was flagged by the slasher to stop the user from withdrawing funds, but a situation where such user can still call
`_completeQueuedWithdrawal (...)` from `StrategyManager` and, bypass the check in,
`slasher.canWithdraw(...)` or make the check revert for everybody calling `_completeQueuedWithdrawal(...),`, that would be a serious Security issue.

**Note** the malicious `User` never directly calls any function in `Slasher.sol` or any other out-of-scope contract, he only sends transactions to `StrategyManager` but their logic depends on the the function's of contracts `Out of Scope`.

According to the `Docs` and `WhitePaper` of `EigenLayer` it's protocol Works Successfully if it can efficiently Implement certain work `Flows` accurately without causing loss of user `assets` and  
## Impact.
> **High**

As Auditing certain contracts without Auditing the contracts mentioned above would Heavily affect the `EigenLayer's` Paradigm, if exploited when it is deployed.
> Likelihood ~ **High**
## Recommended Mitigation Steps.
The developers should ensure to put certain contracts that functions in the `StrategyManager` deeply rely on, as to mitigate exploit Scenerios that could happen if such mechanisms contain bugs or errors.

# [L-03] ~ `BeaconChainETHStrategy` is fixed/Hardcoded..
## Summary.
This Issue Arises from the Hardcoded/Fixed bytecode for the `BeaconChainETHStrategy` in the `StrategyManagerStorage` which should not be so according to security best Pitfalls & Practices, rather than being assigned a value dynamically during contract execution. This can create a situation where the contract is inflexible and unable to adapt to changing circumstances, making it easier for attackers to exploit any weaknesses in the contract.

## Vulnerability Details.
The `BeaconChainETHStrategy` is fixed in the `StrategyManagerStorage` which should not be so.

[Link to code](https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/core/StrategyManagerStorage.sol#L70)👈
```solidity 
70. IStrategy public constant beaconChainETHStrategy = IStrategy(0xbeaC0eeEeeeeEEeEeEEEEeeEEeEeeeEeeEEBEaC0);
```
BeaconChainETHStrategy should not be fixed as it would inflexible and that isn't the best security measure.
## Impact.
> `High`.


> `Likelihood` ~ **Medium**

**As it is less likely to happen but has grave impacts to the upgradeability and Future implementations of the `BeaconChainETHStrategy`**
## Recommended Mitigation Steps.
Developers should consider making it possible to dynamically fix such variable during execution as to reduce rigidity and Certain security vectors when it comes to hardcoded Variable Implementations.

# [L-04] ~ `Intializers` can be frontrun.
## Summary.
Certain Intializers in core contracts of the `EigenLayer` can be front run by attackers who can put in their own arguments to specified parameters.
## Vulnerability Details.
- `StrategyManager`
- `StrategyBase`
- `EigenPodManager`
- `EigenPod`
- `DelayedWithdrawalRouter`

The Above contracts in the `EigenLayer` have Intializers that are vulnerable to frontrunning by an attacker.

**Lines of code👇**
- `StrategyManager` ~ [Link to Code](https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/core/StrategyManager.sol#L146)
- `StrategyBase`  ~ [Link to Code](https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/strategies/StrategyBase.sol#L51)
- `EigenPodManager`  ~ [Link to Code](https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/pods/EigenPodManager.sol#L84)
- `EigenPod`  ~ [Link to Code](https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/pods/EigenPod.sol#L152)
- `DelayedWithdrawalRouter`  ~ [Link to Code](https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/pods/DelayedWithdrawalRouter.sol#L49)

## Impact.
> **Medium**

The attacker can initialize the contract before the legitimate deployer, hoping that the victim continues to use the same contract.
In the best case for the victim, they notice it and have to redeploy their contract costing gas.
## Recommended Mitigation Steps.
Use the constructor to initialize non-proxied contracts.
For initializing proxy contracts deploy contracts using a factory contract that immediately calls initialize after deployment or make sure to call it immediately after deployment and verify the transaction succeeded.

# [L-05] ~ Constructor Missing the `_disableintializers` function call, in the `DelayedWithdrawalRouter` contract

## Summary

The `_disableintializers` function call usually written in a constructor to prevent any future reinitialization is missing.

## Vulnerability Details.
[Link to Code](https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/pods/DelayedWithdrawalRouter.sol#L44)

This can lead to a future reinitialization, which can affect the withdrawal of funds and normal contract roles, once the intializer function is called again by an attacker.
## Impact
> **High**

> Likelihood ~ **Medium**
## Recommended Mitigation Steps
In the case where it was intended by the developers, they should have specified why in the NatSpecs, else the function call should be added as to prevent any attacker from reintializing the `DelayedWithdrawal`

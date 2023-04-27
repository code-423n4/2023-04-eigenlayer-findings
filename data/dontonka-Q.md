# QA Report for EigenLayer contest
## Overview
During the audit, 1 low and 0 non-critical issues were found.

№ | Title | Risk Rating  | Instance Count
--- | --- | --- | ---
L-1 | Missing require that pauser and unpauser address should be different | Low | 2

## Low Risk Findings(1)
### L-1. Missing require that pauser and unpauser address should be different
##### Description
In the function [```_setPauser(address newPauser)```](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/permissions/PauserRegistry.sol#L41) and in the function [```_setUnpauser(address newUnpauser)```](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/permissions/PauserRegistry.sol#L47), there is no require enforcing that the pauser and unpauser address must be unique (thus different from each other). Documentation [here](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/permissions/PauserRegistry.sol#L11) and [here](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/permissions/PauserRegistry.sol#L14) it is written that the address is **Unique**, and that the unpauser address is more secure (multi-sig), which imply they should always be different.

##### Instances
- https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/permissions/PauserRegistry.sol#L42
- https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/permissions/PauserRegistry.sol#L48

##### Recommendation
Add an additional require in **_setPauser** and **_setUnpauser** respectively as follow:
```diff
require(newPauser != unpauser, "PauserRegistry._setPauser: cannot be the same as unpauser");
```
```diff
require(newUnpauser != pauser, "PauserRegistry._setUnpauser: cannot be the same as pauser");
```
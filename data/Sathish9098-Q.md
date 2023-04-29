Report contents changed:  # LOW FINDINGS

| LOW COUNT| ISSUES | INSTANCES|
|-------|-----|--------|
| [L-1]| Use abi.encode to convert safest way from uint values to bytes  | 3 |
| [L-2]| Loss of precision due to rounding | 1 |
| [L-3]| Consider using OpenZeppelin’s SafeCast library to prevent unexpected overflows when casting from uint256  | 11 |
| [L-4]| MIXING AND OUTDATED COMPILER  | 7 |
| [L-5]| abi.encodePacked() should not be used with dynamic types when passing the result to a hash function such as keccak256()  | 7 |
| [L-6]| Lack of Sanity/Threshold/Limit Checks  | 2 |
| [L-7]| Function Calls in Loop Could Lead to Denial of Service | 10 |
| [L-8]| Project Upgrade and Stop Scenario should be  | - |
| [L-9]| Front running attacks by the onlyOwner  | 3 |
| [L-10]| Use BytesLib.sol library to safely covert bytes to uint256  | 2 |
| [L-11]| Avoid infinite loops whenever possible  | 2 |
| [L-12]| In the constructor, there is no return of incorrect address identification  | 6 |
| [L-13]| Even with the onlyOwner or owner_only modifier, it is best practice to use the re-entrancy pattern  | 3 |

 # NON CRITICAL FINDINGS

| NC COUNT| ISSUES | INSTANCES|
|-------|-----|--------|
| [NC-1]| immutable should be uppercase  | 4 |
| [NC-2]| Missing NATSPEC  | - |
| [NC-3]| For functions, follow Solidity standard naming conventions (internal function style rule) | 28 |
| [NC-4]| Need Fuzzing test for unchecked  | 5 |
| [NC-5]| Remove commented out code  | - |
| [NC-6]| Inconsistent method of specifying a floating pragma  | 8 |
| [NC-7]| NO SAME VALUE INPUT CONTROL  | 1 |
| [NC-8]| Constant redefined elsewhere  | - |
| [NC-9]| According to the syntax rules, use => mapping ( instead of => mapping( using spaces as keyword| 3 |
| [NC-10]| Use SMTChecker  | - |
| [NC-11]| Constants on the left are better | 20 |
| [NC-12]| Assembly Codes Specific – Should Have Comments  | 18 |
| [NC-13]| Take advantage of Custom Error’s return value property  | 4 |
| [NC-14]| Use constants instead of using numbers directly without explanations  | 6 |
| [NC-15]| Shorthand way to write if / else statement | 4 |
| [NC-16]| For critical changes should emit both old and new values  | 1 |
| [NC-17]| Don't use named return variables its confusing  | 8 |
| [NC-18]| NON-LIBRARY/INTERFACE FILES SHOULD USE FIXED COMPILER VERSIONS, NOT FLOATING ONES  | 7 |
| [NC-19]| Constants should be in uppercase   | 7 |
| [NC-20]| TYPOS  | 4 |
| [NC-21]| Contracts should have full test coverage  | - |
| [NC-22]| Use named parameters for mapping type declarations  | 3 |
| [NC-23]| File does not contain an SPDX Identifier   | 4 |
| [NC-24]| declaration shadows an existing declaration  | - |
| [NC-25]| Event is missing indexed fields  | 2 |


##

## [L-1] Use abi.encode to convert safest way from uint values to bytes

### DRAWBACKS
Now the protocol uses direct conversion way this is not safe. bytes(value) to convert a uint to bytes is not considered a safe way because it creates an uninitialized byte array of length. This means that the contents of the byte array are undefined and may contain sensitive data from previous memory usage, which could result in security vulnerabilities.

### BENIFITS of abi.encode

In Solidity, the safest way to convert a uint to bytes is to use the abi.encode function. This function will encode the uint as a byte array using the ABI encoding rules, which ensures that the output is a deterministic and standardized representation of the uint value.

```solidity

```


```solidity


```


##


##


##

## [L-4] MIXING AND OUTDATED COMPILER

The pragma version used are: 0.8.0

The minimum required version must be 0.8.17; otherwise, contracts will be affected by the following important bug fixes:

0.8.14:

ABI Encoder: When ABI-encoding values from calldata that contain nested arrays, correctly validate the nested array length against calldatasize() in all cases.
Override Checker: Allow changing data location for parameters only when overriding external functions.

0.8.15

Code Generation: Avoid writing dirty bytes to storage when copying bytes arrays.
Yul Optimizer: Keep all memory side-effects of inline assembly blocks.

0.8.16

Code Generation: Fix data corruption that affected ABI-encoding of calldata values represented by tuples: structs at any nesting level; argument lists of external functions, events and errors; return value lists of external functions. The 32 leading bytes of the first dynamically-encoded value in the tuple would get zeroed when the last component contained a statically-encoded array.

0.8.17
Yul Optimizer: Prevent the incorrect removal of storage writes before calls to Yul functions that conditionally terminate the external EVM call.
Apart from these, there are several minor bug fixes and improvements

```solidity

```
##

## [L-6] Lack of Sanity/Threshold/Limit Checks

Devoid of sanity/threshold/limit checks, critical parameters can be configured to invalid values, causing a variety of issues and breaking expected interactions within/between contracts. Consider adding proper uint256 validation as well as zero address checks for critical changes. A worst case scenario would render the contract needing to be re-deployed in the event of human/accidental errors that involve value assignments to immutable variables. If the validation procedure is unclear or too complex to implement on-chain, document the potential issues that could produce invalid values

```solidity

```


##

## [L-7] Function Calls in Loop Could Lead to Denial of Service

Function calls made in unbounded loop are error-prone with potential resource exhaustion as it can trap the contract due to the gas limitations or failed transactions


```solidity

```

```solidity

```

##

## [L-8] Project Upgrade and Stop Scenario should be

At the start of the project, the system may need to be stopped or upgraded, I suggest you have a script beforehand and add it to the documentation. This can also be called an ” EMERGENCY STOP (CIRCUIT BREAKER) PATTERN “.

https://github.com/maxwoe/solidity_patterns/blob/master/security/EmergencyStop.sol


##

## [L-9] Front running attacks by the onlyOwner


```solidity


```


```solidity

```

##

## [L-10] Use BytesLib.sol library to safely covert bytes to uint256

Use [toUint256()](https://github.com/GNSPS/solidity-bytes-utils/blob/master/contracts/BytesLib.sol) safely convert bytes to uint256 instead of plain way of conversion

```solidity

```

##

## [L-11] Avoid infinite loops whenever possible

```solidity

```


```solidity

```

##

## [L-13] Even with the onlyOwner or owner_only modifier, it is best practice to use the re-entrancy pattern

It's still good practice to apply the reentry model as a precautionary measure in case the code is changed in the future to remove the onlyOwner modifier or the contract is used as a base contract for other contracts.

Using the reentry modifier provides an additional layer of security and ensures that your code is protected from potential reentry attacks regardless of any other security measures you take.

So even if you followed the "check-effects-interactions" pattern correctly, it's still considered good practice to use the reentry modifier


```solidity

```


```solidity

```

Recommended Mitigation:

```solidity

 


```

##

# NON CRITICAL FINDINGS

##

## [NC-1] immutable should be uppercase

```solidity


```


### Recommended Mitigation

```solidity

```



##

## [NC-2] Missing NATSPEC



##

## [NC-3] For functions, follow Solidity standard naming conventions (internal function style rule)

### Description
The above codes don’t follow Solidity’s standard naming convention,

internal and private functions : the mixedCase format starting with an underscore (_mixedCase starting with an underscore)

```solidity

```


##

## [NC-4] Need Fuzzing test for unchecked

```solidity

```
##

## [NC-5] Remove commented out code


```solidity


```


##

## [NC-6] Inconsistent method of specifying a floating pragma

Some files use >=, some use ^. The instances below are examples of the method that has the fewest instances for a specific version. Note that using >= without also specifying <= will lead to failures to compile, or external project incompatability, when the major version changes and there are breaking-changes, so ^ should be preferred regardless of the instance counts

```solidity


```
##

## [NC-7] NO SAME VALUE INPUT CONTROL

```solidity


```

##

## [NC-9] According to the syntax rules, use => mapping ( instead of => mapping( using spaces as keyword


```solidity


```


```solidity


```



## [NC-10] Use SMTChecker

The highest tier of smart contract behavior assurance is formal mathematical verification. All assertions that are made are guaranteed to be true across all inputs → The quality of your asserts is the quality of your verification

https://twitter.com/0xOwenThurm/status/1614359896350425088?t=dbG9gHFigBX85Rv29lOjIQ&s=19

##


## [NC-12] Assembly Codes Specific – Should Have Comments

Since this is a low level language that is more difficult to parse by readers, include extensive documentation, comments on the rationale behind its use, clearly explaining what each assembly instruction does.

This will make it easier for users to trust the code, for reviewers to validate the code, and for developers to build on or update the code.

Note that using Assembly removes several important security features of Solidity, which can make the code more insecure and more error-prone


```solidity


```

```solidity

```
```solidity

```


```solidity


```


```solidity

```


##

## [NC-13] Take advantage of Custom Error’s return value property

An important feature of Custom Error is that values such as address, tokenID, msg.value can be written inside the () sign, this kind of approach provides a serious advantage in debugging and examining the revert details of dapps such as tenderly


```solidity

```


```solidity


```

##

## [NC-15] Shorthand way to write if / else statement

The normal if / else statement can be refactored in a shorthand way to write it:

Increases readability
Shortens the overall SLOC


```solidity

```

```solidity

```

```solidity

```
##

## [NC-16] For critical changes should emit both old and new values

Emitting both old and new values for critical changes is a good practice in Solidity, as it provides a way for external parties (such as other smart contracts or off-chain applications) to track and verify the changes made to a smart contract state.

```solidity

```


##

## [NC-17] Don't use named return variables its confusing

```solidity

```

##

## [18] NON-LIBRARY/INTERFACE FILES SHOULD USE FIXED COMPILER VERSIONS, NOT FLOATING ONES

```solidity

```

##

## [NC-19] Constants should be in uppercase

```solidity

```


##

## [NC-22] Use named parameters for mapping type declarations

Consider using named parameters in mappings (e.g. mapping(address account => uint256 balance)) to improve readability. This feature is present since Solidity 0.8.18.

```solidity

```


```solidity


```


##

## [NC-23] File does not contain an SPDX Identifier


##

## [NC-24] declaration shadows an existing declaration

```solidity

```

##









[L‑01]	Events are missing sender information	6
[L‑02]	The owner is a single point of failure and a centralization risk	27
[L‑03]	Use Ownable2Step's transfer function rather than Ownable's for transfers of ownership	2
[L‑04]	Unsafe downcast	3
[L‑05]	Loss of precision	4
[L‑06]	Upgradeable contract not initialized	6
[L‑07]	Array lengths not checked	3
[L‑08]	Missing checks for address(0x0) when assigning values to address state variables	1
[L‑09]	abi.encodePacked() should not be used with dynamic types when passing the result to a hash function such as keccak256()	2
[L‑10]	Use Ownable2Step rather than Ownable	3

[N‑01]	Constants in comparisons should appear on the left side	118
[N‑02]	Variables need not be initialized to zero	15
[N‑03]	Imports could be organized more systematically	13
[N‑04]	Large numeric literals should use underscores for readability	14
[N‑05]	Upgradeable contract is missing a __gap[50] storage variable to allow for new storage variables in later versions	1
[N‑06]	Import declarations should import specific identifiers, rather than the whole file	71
[N‑07]	Missing initializer modifier on constructor	1
[N‑08]	Unused file	1
[N‑09]	The nonReentrant modifier should occur before all other modifiers	9
[N‑10]	Adding a return statement when the function defines a named return variable, is redundant	2
[N‑11]	public functions not called by the contract should be declared external instead	1
[N‑12]	constants should be defined rather than using magic numbers	56
[N‑13]	Cast is more restrictive than the type of the variable being assigned	1
[N‑14]	Numeric values having to do with time should use time units for readability	1
[N‑15]	Use a more recent version of solidity	3
[N‑16]	Expressions for constant values such as a call to keccak256(), should use immutable rather than constant	2
[N‑17]	Constant redefined elsewhere	6
[N‑18]	Inconsistent spacing in comments	24
[N‑19]	Lines are too long	23
[N‑20]	Variable names that consist of all capital letters should be reserved for constant/immutable variables	2
[N‑21]	Typos	21
[N‑22]	Misplaced SPDX identifier	1
[N‑23]	File is missing NatSpec	1
[N‑24]	NatSpec is incomplete	41
[N‑25]	Event is missing indexed fields	23
[N‑26]	Consider using delete rather than assigning zero to clear values	1
[N‑27]	Avoid the use of sensitive terms	31
[N‑28]	Contracts should have full test coverage	1
[N‑29]	Large or complicated code bases should implement invariant tests	1
[N‑30]	Function ordering does not follow the Solidity style guide	7
[N‑31]	Contract does not follow the Solidity style guide's suggested layout ordering	7
From lines 15 - 84; [LinkA](https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/core/Slasher.sol#L21-L88)

From lines 200 - 292; [LinkB](https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/core/Slasher.sol#L200-L292)

- The **`_revokeSlashingAbility`** function that is called within **`recordLastStakeUpdateAndRevokeSlashingAbility`** is not defined in this code snippet, so we can't assess its safety.
-The **`whitelistedContractDetails`** function returns a struct, which may be expensive in terms of gas usage. Additionally, there may be some concerns about exposing internal data structures like this to external callers.
-The **`canSlash`** function uses the **`block.number`** variable, which is the current block number and is therefore subject to manipulation by miners. This could potentially be exploited by malicious actors to bypass slashing restrictions.
-The **`isFrozen`** function checks if an address is frozen or not, but it is unclear what the implications of being frozen are or how freezing is triggered.
-The **`canWithdraw`** function contains a potentially confusing condition involving the **`size`** property of a data structure, which may be difficult for external callers to reason about.

### Reentrancy ###

- The contract uses the `Pausable` contract from OpenZeppelin, which exposes the `whenNotPaused` modifier that is applied to external functions. However, this does not protect against reentrancy attacks that can occur if the contract modifies state after an external call.[Link](https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/core/Slasher.sol)
- The comments warn against depositing tokens that allow reentrancy, but it's not clear from the code if this is completely prevented. If a malicious actor were able to trigger a reentrant call, they could potentially manipulate the balances of the contract and the strategy, leading to a loss of funds. (line 164 to line 298).[Link](https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/core/StrategyManager.sol#L164-L298)

### Integer Overflow ###

The uint256 data type is used throughout the code, but it's not clear if any checks are made to prevent integer overflow. An unchecked integer overflow can result in incorrect calculations, which can lead to unintended behavior or an attack that exploits this vulnerability. [Link](https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/core/StrategyManager.sol#L164-L298)

### Signature verification ###

The depositIntoStrategyWithSignature function requires a signature for validation, but it's not clear from the code how the signature is being verified. If the verification process is not secure, then a malicious actor could generate a fake signature and deposit funds on behalf of another user.[Link](https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/core/StrategyManager.sol)

### Lack of access control

[Link](https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/core/StrategyManager.sol)
There doesn't appear to be any access control mechanisms in place for certain functions, such as strategyWhitelister and withdrawalDelayBlocks, which could lead to unauthorized changes to these values.

### Lack of input validation

[Link](https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/core/StrategyManager.sol)
There is no input validation in the nonces mapping, which could lead to nonce reuse and replay attacks. Additionally, there is no input validation in the beaconChainETHSharesToDecrementOnWithdrawal mapping, which could lead to incorrect withdrawal amounts.

### Information leakage

The `_whitelistedContractDetails` mapping stores sensitive information about the contract's operators and the middleware with permission to slash them. Anyone who can read the storage of the contract can access this information.[Link](https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/core/Slasher.sol)

### External call to untrusted contract

The freezeOperator function allows external contracts to call it and modify the frozenStatus mapping. This could lead to potential vulnerabilities if an untrusted contract is allowed to call this function.[Link](https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/core/Slasher.sol)

### Lack of validation

The contract uses the `_optIntoSlashing` function to give permission to contracts to slash the funds of the caller. However, there is no validation to ensure that the contract being given permission is a valid contract. This could lead to potential vulnerabilities if a malicious contract is given permission to slash the funds of the caller.`[Link](https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/core/Slasher.sol)

### Signature validity check

The delegateToBySignature() function includes a check for the validity of a signature provided by the staker. The check differs depending on whether the staker is a contract or an externally owned account (EOA). However, if the contract does not implement the EIP-1271 standard correctly, this check may not be effective, and an attacker may be able to bypass it.[Link](https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/core/DelegationManager.sol)
# GAS OPTIMIZATION

| Gas Count| Issues| Instances| Gas Saved|
|-------|-----|--------|--------|
| [G-1] | State variable value only set in the constructor can be declared as a constants to save large volume of the gas   | 1 | 90000 |
| [G-2] | State variables only set in the constructor should be declared immutable  | 1 | 20000 |
| [G-3] | Using storage instead of memory for structs/arrays saves gas  | 3 | 6300 |
| [G-4] | Structs can be packed into fewer storage slots  | 1 | | 
| [G-5] | For events use 3 indexed rule to save gas  | 3 | - |
| [G-6] | Lack of input value checks cause a redeployment if any human/accidental errors | 4 | - |
| [G-7] | Use nested if and, avoid multiple check combinations  | 2 | 18 |
| [G-8] | Unnecessary look up in if condition  | 7 | - |
| [G-9] | Don't declare the variable inside the loops  | 3 | - |
| [G-10] | Functions should be used instead of modifiers to save gas  | 1 | - |
| [G-11] | Sort Solidity operations using short-circuit mode  | 5 | - |
| [G-12] | Use assembly to check for address(0)  | 7 | 42 |
| [G-13] | Shorthand way to write if / else statement can reduce the deployment cost| 4 | - |
| [G-14] | The Less gas consuming condition checks should be on top  | 1 | - |
| [G-15] | nternal functions not called by the contract should be removed to save deployment gas  | 3 | - |
| [G-16] | Modifiers or private functions only called once can be inlined to save gas | 2 | 80 |
| [G-17] | NOT USING THE NAMED RETURN VARIABLES WHEN A FUNCTION RETURNS, WASTES DEPLOYMENT GAS  | 12 | - |
| [G-18] | Use constants instead of type(uintx).max  | 5 | - |
| [G-19] | Instead of calculating bytes32(0) every time inside the contract its possible to use constants to reduce the gas cost   | 1 | - |



### TOTAL INSTANCES : 

## APROXIMATE GAS SAVED: 

##

## [G-1] State variable value only set in the constructor can be declared as a constants to save large volume of the gas

> Instances ()

> Approximate Gas Saved : 

gatewayURL string variable value is only set inside the constructor. The value is not changed anywhere inside the contract. This gatewayURL can be declared as constant to save large volume of gas

As per [Remix gas test](https://gist.github.com/sathishpic22/0079888aca5a6fe626b74f0063546138) approximately possible to saves 90000 gas

```solidity

```

```solidity

```

```solidity

```

```solidity

```


##

## [G-2] State variables only set in the constructor should be declared immutable

> Instances()

> Approximate Gas saved : 

Avoids a Gsset (20000 gas) in the constructor, and replaces the first access in each transaction (Gcoldsload - 2100 gas) and each access thereafter (Gwarmacces - 100 gas) with a PUSH32 (3 gas).

While strings are not value types, and therefore cannot be immutable/constant if not hard-coded outside of the constructor, the same behavior can be achieved by making the current contract abstract with virtual functions for the string accessors, and having a child contract override the functions with the hard-coded implementation-specific values

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



## [G-3] Using storage instead of memory for structs/arrays saves gas

> Instances()

> Approximate gas saved: 

When fetching data from a storage location, assigning the data to a memory variable causes all fields of the struct/array to be read from storage, which incurs a Gcoldsload (2100 gas) for each field of the struct/array. If the fields are read from the new memory variable, they incur an additional MLOAD rather than a cheap stack read. Instead of declearing the variable with the memory keyword, declaring the variable with the storage keyword and caching any fields that need to be re-read in stack variables, will be much cheaper, only incuring the Gcoldsload for the fields actually read. The only time it makes sense to read the whole struct/array into a memory variable, is if the full struct/array is being returned by the function, is being passed to a function that requires memory, or if the array/struct is being read from another memory array/struct

```solidity
FILE: 2023-04-eigenlayer/src/contracts/core/StrategyManager.sol

200: IStrategy[] memory strategies = new IStrategy[](1);
202: uint256[] memory shareAmounts = new uint256[](1);
859: uint256[] memory shares = new uint256[](strategiesLength);
390: WithdrawerAndNonce memory withdrawerAndNonce = WithdrawerAndNonce({
                withdrawer: withdrawer,
                nonce: nonce
            });

```
[StrategyManager.sol#L200](https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/core/StrategyManager.sol#L200)


```solidity

```


```solidity

```

##

## [G-4] Structs can be packed into fewer storage slots

> Instances ()

> Aproximate Gas Saved: 

As the solidity EVM works with 32 bytes, variables less than 32 bytes should be packed inside a struct so that they can be stored in the same slot, this saves gas when writing to storage ~20000 gas

For offset,rdataOffset,nextOffset variables uint128 alone enough. So we can avoid 2 slots (40000 gas )

```solidity


```


##

## [G-5] For events use 3 indexed rule to save gas

> Instances()

Need to declare 3 indexed fields for event parameters. If the event parameter is less than 3 should declare all event parameters indexed

```solidity
FILE: 2023-04-eigenlayer/src/contracts/core/StrategyManager.sol

54: event Deposit(
        address depositor, IERC20 token, IStrategy strategy, uint256 shares
    );

65: event ShareWithdrawalQueued(
        address depositor, uint96 nonce, IStrategy strategy, uint256 shares
    );

77: event WithdrawalQueued(
        address depositor, uint96 nonce, address withdrawer, address delegatedAddress, bytes32 withdrawalRoot
    );

82: event WithdrawalCompleted(address indexed depositor, uint96 nonce, address indexed withdrawer, bytes32 withdrawalRoot);

85: event StrategyWhitelisterChanged(address previousAddress, address newAddress);

88: event StrategyAddedToDepositWhitelist(IStrategy strategy);

91: event StrategyRemovedFromDepositWhitelist(IStrategy strategy);

94: event WithdrawalDelayBlocksSet(uint256 previousValue, uint256 newValue);




```
[StrategyManager.sol#L65-L67](https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/core/StrategyManager.sol#L65-L67)

```solidity

```

```solidity

```

```solidity

```

```solidity

```



##

## [G-6] Lack of input value checks cause a redeployment if any human/accidental errors

> Instances()

Devoid of sanity/threshold/limit checks, critical parameters can be configured to invalid values, causing a variety of issues and breaking expected interactions within/between contracts. Consider adding proper uint256 validation. A worst case scenario would render the contract needing to be re-deployed in the event of human/accidental errors that involve value assignments to immutable variables.

If any human/accidental errors happen need to redeploy the contract so this create the huge gas lose

```solidity

```


```solidity

```


```solidity

```


```solidity


```


##

## [G-7] Use nested if and, avoid multiple check combinations

> Instances()

Using nested is cheaper than using && multiple check combinations. There are more advantages, such as easier to read code and better coverage reports.

As per Solidity [reports](https://gist.github.com/sathishpic22/fe96671bafb22ceaace7fc05a66bd115) possible to save 9 gas

```solidity
FILE: 2023-04-eigenlayer/src/contracts/core/StrategyManager.sol

422: if (undelegateIfPossible && stakerStrategyList[msg.sender].length == 0) {
562: if (indicesToSkipIndex < indicesToSkip.length && indicesToSkip[indicesToSkipIndex] == i) {

```
[StrategyManager.sol#L422](https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/core/StrategyManager.sol#L422)


```solidity

```


##

## [G-8] Unnecessary look up in if condition

> Instances()

If the || condition isn’t required, the second condition will have been looked up unnecessarily.

```solidity
FILE: 2023-04-eigenlayer/src/contracts/core/StrategyManager.sol

761: require(queuedWithdrawal.withdrawalStartBlock + withdrawalDelayBlocks <= block.number 
                || queuedWithdrawal.strategies[0] == beaconChainETHStrategy,
            "StrategyManager.completeQueuedWithdrawal: withdrawalDelayBlocks period has not yet passed"
        );

```
[StrategyManager.sol#L761-L764](https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/core/StrategyManager.sol#L761-L764)


```solidity

```


```solidity

```


```solidity


```


##


## [G-9] Don't declare the variable inside the loops

> Instances()

In every iterations the new variables instance created this will consumes more gas . So just declare variables outside the loop and only use inside to save gas

```solidity

```




##

## [G-10] Functions should be used instead of modifiers to save gas

> Instances()

```solidity
FILE: 2023-04-eigenlayer/src/contracts/core/StrategyManager.sol

96: modifier onlyNotFrozen(address staker) {
        require(
            !slasher.isFrozen(staker),
            "StrategyManager.onlyNotFrozen: staker has been frozen and may be subject to slashing"
        );
        _;
    }

104: modifier onlyFrozen(address staker) {
        require(slasher.isFrozen(staker), "StrategyManager.onlyFrozen: staker has not been frozen");
        _;
    }

109: modifier onlyEigenPodManager {
        require(address(eigenPodManager) == msg.sender, "StrategyManager.onlyEigenPodManager: not the eigenPodManager");
        _;
    }

114: modifier onlyStrategyWhitelister {
        require(msg.sender == strategyWhitelister, "StrategyManager.onlyStrategyWhitelister: not the strategyWhitelister");
        _;
    }

119: modifier onlyStrategiesWhitelistedForDeposit(IStrategy strategy) {
        require(strategyIsWhitelistedForDeposit[strategy], "StrategyManager.onlyStrategiesWhitelistedForDeposit: strategy not whitelisted");
        _;
    }



```
[StrategyManager.sol#L96-L122](https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/core/StrategyManager.sol#L96-L122)

```solidity

```

```solidity

```

```solidity

```


##

## [G-11] Sort Solidity operations using short-circuit mode

> Instances()

Short-circuiting is a solidity contract development model that uses OR/AND logic to sequence different cost operations. It puts low gas cost operations in the front and high gas cost operations in the back, so that if the front is low If the cost operation is feasible, you can skip (short-circuit) the subsequent high-cost Ethereum virtual machine operation.

```

//f(x) is a low gas cost operation
//g(y) is a high gas cost operation
//Sort operations with different gas costs as follows
f(x) || g(y)
f(x) && g(y)

```

```solidity

```


```solidity

```



```solidity


```

##

## [G-12] Use assembly to check for address(0)

> Instances()

Saves 6 gas per instance

```solidity
FILE: FILE: 2023-04-eigenlayer/src/contracts/core/StrategyManager.sol 

343: require(withdrawer != address(0), "StrategyManager.queueWithdrawal: cannot withdraw to zero address");
631: require(depositor != address(0), "StrategyManager._addShares: depositor cannot be zero address");
684: require(depositor != address(0), "StrategyManager._removeShares: depositor cannot be zero address");


```
[StrategyManager.sol#L343](https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/core/StrategyManager.sol#L343)


```solidity

```


```solidity

```


##

## [G-13] Shorthand way to write if / else statement can reduce the deployment cost

> Instances()

```solidity
FILE: 2023-04-eigenlayer/src/contracts/core/StrategyManager.sol

289: if (Address.isContract(staker)) {
            require(IERC1271(staker).isValidSignature(digestHash, signature) == ERC1271_MAGICVALUE,
                "StrategyManager.depositIntoStrategyWithSignature: ERC1271 signature verification failed");
        } else {
            require(ECDSA.recover(digestHash, signature) == staker,
                "StrategyManager.depositIntoStrategyWithSignature: signature not from staker");
        }


567: if (queuedWithdrawal.strategies[i] == beaconChainETHStrategy){
                     //withdraw the beaconChainETH to the recipient
                    _withdrawBeaconChainETH(queuedWithdrawal.depositor, recipient, queuedWithdrawal.shares[i]);
                } else {
                    // tell the strategy to send the appropriate amount of funds to the recipient
                    queuedWithdrawal.strategies[i].withdraw(recipient, tokens[i], queuedWithdrawal.shares[i]);
                }

781: if (queuedWithdrawal.strategies[i] == beaconChainETHStrategy) {

                    // if the strategy is the beaconchaineth strat, then withdraw through the EigenPod flow
                    _withdrawBeaconChainETH(queuedWithdrawal.depositor, msg.sender, queuedWithdrawal.shares[i]);
                } else {
                    // tell the strategy to send the appropriate amount of funds to the depositor
                    queuedWithdrawal.strategies[i].withdraw(
                        msg.sender, tokens[i], queuedWithdrawal.shares[i]
                    );
                }


```


```solidity


```

```solidity

```

### Recommended Mitigation

```solidity

Address.isContract(staker : require(IERC1271(staker).isValidSignature(digestHash, signature) == ERC1271_MAGICVALUE,
                "StrategyManager.depositIntoStrategyWithSignature: ERC1271 signature verification failed") ? require(ECDSA.recover(digestHash, signature) == staker,
                "StrategyManager.depositIntoStrategyWithSignature: signature not from staker");  

```


##

## [G-14] The Less gas consuming condition checks should be on top

> Instances()

When writing conditional statements in smart contracts, it is generally best practice to order the conditions so that the less gas-consuming checks are performed first. This can help to optimize the gas usage of the contract and improve its overall efficiency

```solidity

```


##

## [G-15] internal functions not called by the contract should be removed to save deployment gas

> Instances()

If the functions are required by an interface, the contract should inherit from that interface and use the override keyword


```solidity

```

##

## [G-16] Modifiers or private functions only called once can be inlined to save gas

Instances ()

ITs possible to save 40-50 gas

```solidity

```


```solidity


```


##

## [G-17] NOT USING THE NAMED RETURN VARIABLES WHEN A FUNCTION RETURNS, WASTES DEPLOYMENT GAS

Instances ()

```solidity

function _depositIntoStrategy(address depositor, IStrategy strategy, IERC20 token, uint256 amount)
        internal
        onlyStrategiesWhitelistedForDeposit(strategy)
        returns (uint256 shares)
    {

```
[StrategyManager.sol#L655-L659](https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/core/StrategyManager.sol#L655-L659)

```solidity


```

```solidity

```


```solidity


```

##

## [G-18] Use constants instead of type(uintx).max

Instances ():

type(uint256).max it uses more gas in the distribution process and also for each transaction than constant usage

```solidity

```


```solidity


```


##

## [G-20] Use assembly for address state variables 


##

## [G-21]  Use solidity version 0.8.19 to gain some gas boost

Upgrade to the latest solidity version 0.8.19 to get additional gas savings. See latest release for reference: https://blog.soliditylang.org/2023/02/22/solidity-0.8.19-release-announcement/

```solidity
FILE: 2023-04-eigenlayer/src/contracts/core/StrategyManager.sol

2: pragma solidity =0.8.12;

```
## 

## [G-22] Shorten the array rather than copying to a new one

Inline-assembly can be used to shorten the array by changing the length slot, so that the entries don't have to be copied to a new, shorter array

```solidity
FILE: 2023-04-eigenlayer/src/contracts/core/StrategyManager.sol

200: IStrategy[] memory strategies = new IStrategy[](1);
202: uint256[] memory shareAmounts = new uint256[](1);
859: uint256[] memory shares = new uint256[](strategiesLength);

```
[StrategyManager.sol#L200)](https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/core/StrategyManager.sol#L200)

##

## [G-23] abi.encode() is less efficient than abi.encodepacked()

[See for more information:](https://github.com/ConnorBlockchain/Solidity-Encode-Gas-Comparison)

```solidity
FILE: 2023-04-eigenlayer/src/contracts/core/StrategyManager.sol

150: DOMAIN_SEPARATOR = keccak256(abi.encode(DOMAIN_TYPEHASH, keccak256(bytes("EigenLayer")), ORIGINAL_CHAIN_ID, address(this)));

268: bytes32 structHash = keccak256(abi.encode(DEPOSIT_TYPEHASH, strategy, token, amount, nonce, expiry));

276: bytes32 domain_separator = keccak256(abi.encode(DOMAIN_TYPEHASH, keccak256(bytes("EigenLayer")), block.chainid, address(this)));

877: return (
            keccak256(
                abi.encode(
                    queuedWithdrawal.strategies,
                    queuedWithdrawal.shares,
                    queuedWithdrawal.depositor,
                    queuedWithdrawal.withdrawerAndNonce,
                    queuedWithdrawal.withdrawalStartBlock,
                    queuedWithdrawal.delegatedAddress
                )
            )
        );

```
[StrategyManager.sol#L150](https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/core/StrategyManager.sol#L150)

##

## [G-24] Duplicated require()/revert() Checks Should Be Refactored To A Modifier Or Function

Saves deployment costs

```solidity
FILE: FILE: 2023-04-eigenlayer/src/contracts/core/StrategyManager.sol 

631: require(depositor != address(0), "StrategyManager._addShares: depositor cannot be zero address");
684: require(depositor != address(0), "StrategyManager._removeShares: depositor cannot be zero address");


```
[StrategyManager.sol#L684](https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/core/StrategyManager.sol#L684)


##

## [G-25] Use hardcode address instead address(this)

Instead of using address(this), it is more gas-efficient to pre-calculate and use the hardcoded address. Foundry's script.sol and solmate's LibRlp.sol contracts can help achieve this

[References:](https://book.getfoundry.sh/reference/forge-std/compute-create-address)

https://twitter.com/transmissions11/status/1518507047943245824

```solidity
FILE: 2023-04-eigenlayer/src/contracts/core/StrategyManager.sol

150: DOMAIN_SEPARATOR = keccak256(abi.encode(DOMAIN_TYPEHASH, keccak256(bytes("EigenLayer")), ORIGINAL_CHAIN_ID, address(this)));

276: bytes32 domain_separator = keccak256(abi.encode(DOMAIN_TYPEHASH, keccak256(bytes("EigenLayer")), block.chainid, address(this)));

```
[StrategyManager.sol#L150](https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/core/StrategyManager.sol#L150)

##

## [G-26] Public Functions To External

The following functions could be set external to save gas and improve code quality. External call cost is less expensive than of public functions.

##

## [G-27] require() Should Be Used Instead Of assert()


##

## [G-28] Non-usage of specific imports

INSTANCES : ALL CONTRACTS 

The current form of relative path import is not recommended for use because it can unpredictably pollute the namespace. Instead, the Solidity docs recommend specifying imported symbols explicitly. https://docs.soliditylang.org/en/v0.8.15/layout-of-source-files.html#importing-other-source-files

A good example:

```solidity

import {OwnableUpgradeable} from "openzeppelin-contracts-upgradeable/contracts/access/OwnableUpgradeable.sol";
import {SafeTransferLib} from "solmate/utils/SafeTransferLib.sol";
import {SafeCastLib} from "solmate/utils/SafeCastLib.sol";
import {ERC20} from "solmate/tokens/ERC20.sol";
import {IProducer} from "src/interfaces/IProducer.sol";
import {GlobalState, UserState} from "src/Common.sol";

```

##

## [G-29] Use uint256(1)/uint256(2) instead for true and false boolean states

If you don't use boolean for storage you will avoid Gwarmaccess 100 gas. In addition, state changes of boolean from true to false can cost up to ~20000 gas rather than uint256(2) to uint256(1) that would cost significantly less

```solidity
FILE: 2023-04-eigenlayer/src/contracts/core/StrategyManager.sol

415: withdrawalRootPending[withdrawalRoot] = true;
554: withdrawalRootPending[withdrawalRoot] = false;
597: strategyIsWhitelistedForDeposit[strategiesToWhitelist[i]] = true;
612: strategyIsWhitelistedForDeposit[strategiesToRemoveFromWhitelist[i]] = false;
772: withdrawalRootPending[withdrawalRoot] = false;

```
[StrategyManager.sol#L415](https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/core/StrategyManager.sol#L415)

```solidity

```

```solidity

```

```solidity

```
```solidity

```

## 

## [G-30] It Costs More Gas To Initialize Variables To Zero Than To Let The Default Of Zero Be Applied

```solidity
FILE: 2023-04-eigenlayer/src/contracts/core/StrategyManager.sol

358: for (uint256 i = 0; i < strategies.length;) {
498: for (uint256 i = 0; i < strategiesLength;) {
560: for (uint256 i = 0; i < strategiesLength;) {
724: uint256 j = 0;
780: for (uint256 i = 0; i < strategiesLength;) {
791: for (uint256 i = 0; i < strategiesLength;) {
861: for (uint256 i = 0; i < strategiesLength;) {

```
[StrategyManager.sol#L358](https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/core/StrategyManager.sol#L358)











[G‑01]	Multiple address/ID mappings can be combined into a single mapping of an address/ID to a struct, where appropriate	1	-
[G‑02]	State variables should be cached in stack variables rather than re-reading them from storage	15	1455
[G‑03]	Multiple accesses of a mapping/array should use a local variable cache	11	462
[G‑04]	<x> += <y> costs more gas than <x> = <x> + <y> for state variables	5	565
[G‑05]	internal functions only called once can be inlined to save gas	4	80
[G‑06]	Add unchecked {} for subtractions where the operands cannot underflow because of a previous require() or if-statement	5	425
[G‑07]	<array>.length should not be looked up in every loop of a for-loop	4	12
[G‑08]	++i/i++ should be unchecked{++i}/unchecked{i++} when it is not possible for them to overflow, as is the case when used in for- and while-loops	8	480
[G‑09]	require()/revert() strings longer than 32 bytes cost extra gas	86	-
[G‑10]	Optimize names to save gas	19	418
[G‑11]	Using bools for storage incurs overhead	4	68400
[G‑12]	++i costs less gas than i++, especially when it's used in for-loops (--i/i-- too)	4	20
[G‑13]	Splitting require() statements that use && saves gas	1	3
[G‑14]	Usage of uints/ints smaller than 32 bytes (256 bits) incurs overhead	4	-
[G‑15]	Using private rather than public for constants, saves gas	6	-
[G‑16]	Division by two should use bit shifting	2	40
[G‑17]	require() or revert() statements that check input arguments should be at the top of the function	1	-
[G‑18]	Use custom errors rather than revert()/require() strings to save gas	88	-
[G‑19]	Functions guaranteed to revert when called by normal users can be marked payable	36	756
[G‑20]	Constructors can be marked payable	7	147
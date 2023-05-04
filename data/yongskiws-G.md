### Gas Optimizations Issues List
| Number |Issues Details|Instance|
|:--:|:-------|:--:|
|[G-01]| Using unchecked blocks to save gas | 17 |
|[G-02]| Use calldata instead of memory for function parameters | 4 |
|[G-03]| Using delete statement can save gas | 1 |
|[G-04]| Functions guaranteed to revert when called by normal users can be marked payable | 2 |
|[G-05]| Use hardcode address instead address(this) | 3 |
|[G-06]| Sort Solidity operations using short-circuit mode | 2 |
|[G-07]| Using unchecked blocks to save gas - Increments in for loop can be unchecked ( save 30-40 gas per loop iteration) | 7 |
|[G-08]| Using storage instead of memory for structs/arrays saves gas | 7 |
|[G-09]| Multiple address/ID mappings can be combined into a single mapping of an address/ID to a struct, where appropriate | 6 |
|[G-10]| Using private rather than public for constants, saves gas | - |
|[G-11]| Using immutable for uint & address for save gas | - |
|[G-12]| internal functions not called by the contract should be removed to save deployment gas | 12 |
|[G-13]| x = x + y is cheaper than x += y; | 7 |
|[G-14]| Usage of uints/ints smaller than 32 bytes (256 bits) incurs overhead | - |
|[G-15]| Using > 0 costs more gas than != 0 when used on a uint in a require() statement | 6 |


## [G-01] Using unchecked blocks to save gas

Solidity version 0.8+ comes with implicit overflow and underflow checks on unsigned integers. When an overflow or an underflow isn’t possible (as an example, when a comparison is made before the arithmetic operation), some gas can be saved by using an unchecked block
https://github.com/ethereum/solidity/issues/10695

Gas Saved For Using unchecked blocks to save gas

The above should be modified to:

17 results - 3 files:

``` diff

before 
Deployment Cost : 4630879
after
Deployment Cost : 4628879
contracts\core\StrategyManager.sol
+193:            unchecked{
194:             amount -= debt;
+195:             }

before
Deployment Cost : 1207579
after
Deployment Cost : 1196573

contracts\strategies\StrategyBase.sol
+93:             unchecked {
94:             newShares = amount;
+95:              }

+99:                       unchecked {
100:                 newShares = amount;
+101:                 }

+103:                 unchecked {
104:                    newShares = (amount * totalShares) / priorTokenBalance;
+105:                 }

+117:         unchecked {
118:         totalShares = updatedTotalShares;
119:         return newShares;
+120:         }

+161:             unchecked {
162:             amountToSend = (_tokenBalance() * amountShares) / priorTotalShares;
+163:             }

+185:             unchecked {
186:              return amountShares;  
+187:             }

+190:                unchecked {
191:             return (_tokenBalance() * amountShares) / totalShares;
+192:                }

+203:          unchecked {
204:         return sharesToUnderlyingView(amountShares);
+205:           }
+217:                      unchecked {
218:             return amountUnderlying;
+219:                }
+221:                   unchecked {
222:             return (amountUnderlying * totalShares) / tokenBalance;
+223:             }
+234:        unchecked {
235:         return underlyingToSharesView(amountUnderlying);
+236:          }

before 
Deployment Cost : 3020945
after 
Deployment Cost : 2990476

contracts\pods\EigenPod.sol
+375:                 unchecked{
376:                 amountToSend = uint256(withdrawalAmountGwei - REQUIRED_BALANCE_GWEI) * uint256(GWEI_TO_WEI);
+377:                 }

+391:                       unchecked{
392:                 amountToSend = uint256(withdrawalAmountGwei - REQUIRED_BALANCE_GWEI) * uint256(GWEI_TO_WEI);
+393:                  }

+433:                   unchecked{
434:         _sendETH(recipient, uint256(partialWithdrawalAmountGwei) * uint256(GWEI_TO_WEI));
+435:                   }

+451:          unchecked{
452:         restakedExecutionLayerGwei -= uint64(amountWei / GWEI_TO_WEI);
+453:          }

+451:          unchecked{
452:         restakedExecutionLayerGwei -= uint64(amountWei / GWEI_TO_WEI);
+453:          }

```


## [G-02] Use calldata instead of memory for function parameters

In some cases, having function arguments in calldata instead of memory is more optimal.

Consider the following generic example:

``` solidity
contract C {
  function add(uint[] memory arr) external returns (uint sum) {
      uint length = arr.length;
      for (uint i = 0; i < arr.length; i++) {
          sum += arr[i];
      }
  }
}
```

In the above example, the dynamic array arr has the storage location memory. When the function gets called externally, the array values are kept in calldata and copied to memory during ABI decoding (using the opcode calldataload and mstore). And during the for loop, arr[i] accesses the value in memory using a mload. However, for the above example this is inefficient. Consider the following snippet instead:

``` solidity
contract C {
  function add(uint[] calldata arr) external returns (uint sum) {
      uint length = arr.length;
      for (uint i = 0; i < arr.length; i++) {
          sum += arr[i];
      }
  }
}
```

In the above snippet, instead of going via memory, the value is directly read from calldata using calldataload. That is, there are no intermediate memory operations that carries this value.

Gas savings: In the former example, the ABI decoding begins with copying value from calldata to memory in a for loop. Each iteration would cost at least 60 gas. In the latter example, this can be completely avoided. This will also reduce the number of instructions and therefore reduces the deploy time cost of the contract.

In short, use calldata instead of memory if the function argument is only read.

Note that in older Solidity versions, changing some function arguments from memory to calldata may cause "unimplemented feature error". This can be avoided by using a newer (0.8.*) Solidity compiler.

Examples Note: The following pattern is prevalent in the codebase:

``` solidity
function f(bytes memory data) external {
  (...) = abi.decode(data, (..., types, ...));
}
```

Here, changing to bytes calldata will decrease the gas. The total savings for this change across all such uses would be quite significant.

4 results - 2 files:


``` solidity
eigen\DelayedWithdrawalRouter.sol
110:     function claimableUserDelayedWithdrawals(address user) external view returns (DelayedWithdrawal[] memory) {
111:         uint256 delayedWithdrawalsCompleted = _userWithdrawals[user].delayedWithdrawalsCompleted;
112:         uint256 delayedWithdrawalsLength = _userWithdrawals[user].delayedWithdrawals.length;
113:         uint256 claimableDelayedWithdrawalsLength = delayedWithdrawalsLength - delayedWithdrawalsCompleted;
114:         DelayedWithdrawal[] memory claimableDelayedWithdrawals = new DelayedWithdrawal[](claimableDelayedWithdrawalsLength);
115:         for (uint256 i = 0; i < claimableDelayedWithdrawalsLength; i++) {
116:             claimableDelayedWithdrawals[i] = _userWithdrawals[user].delayedWithdrawals[delayedWithdrawalsCompleted + i];
117:         }
118:         return claimableDelayedWithdrawals;
119:     }


eigen\Merkle.sol
48:     function processInclusionProofKeccak(bytes memory proof, bytes32 leaf, uint256 index) internal pure returns (bytes32) {
49:         bytes32 computedHash = leaf;
50:         for (uint256 i = 32; i <= proof.length; i+=32) {
51:             if(index % 2 == 0) {
52:                 // if ith bit of index is 0, then computedHash is a left sibling
53:                 assembly {
54:                     mstore(0x00, computedHash)
55:                     mstore(0x20, mload(add(proof, i)))
56:                     computedHash := keccak256(0x00, 0x40)
57:                     index := div(index, 2)
58:                 }
59:             } else {
60:                 // if ith bit of index is 1, then computedHash is a right sibling
61:                 assembly {
62:                     mstore(0x00, mload(add(proof, i)))
63:                     mstore(0x20, computedHash)
64:                     computedHash := keccak256(0x00, 0x40)
65:                     index := div(index, 2)
66:                 }            
67:             }
68:         }
69:         return computedHash;
70:     }
71: 


99:     function processInclusionProofSha256(bytes memory proof, bytes32 leaf, uint256 index) internal view returns (bytes32) {
100:         bytes32[1] memory computedHash = [leaf];
101:         for (uint256 i = 32; i <= proof.length; i+=32) {
102:             if(index % 2 == 0) {
103:                 // if ith bit of index is 0, then computedHash is a left sibling
104:                 assembly {
105:                     mstore(0x00, mload(computedHash))
106:                     mstore(0x20, mload(add(proof, i)))
107:                     if iszero(staticcall(sub(gas(), 2000), 2, 0x00, 0x40, computedHash, 0x20)) {revert(0, 0)}
108:                     index := div(index, 2)
109:                 }
110:             } else {
111:                 // if ith bit of index is 1, then computedHash is a right sibling
112:                 assembly {
113:                     mstore(0x00, mload(add(proof, i)))
114:                     mstore(0x20, mload(computedHash))
115:                     if iszero(staticcall(sub(gas(), 2000), 2, 0x00, 0x40, computedHash, 0x20)) {revert(0, 0)}
116:                     index := div(index, 2)
117:                 }            
118:             }
119:         }
120:         return computedHash[0];
121:     }


129:     function merkleizeSha256(
130:         bytes32[] memory leaves
131:     ) internal pure returns (bytes32) {
132:         //there are half as many nodes in the layer above the leaves
133:         uint256 numNodesInLayer = leaves.length / 2;
134:         //create a layer to store the internal nodes
135:         bytes32[] memory layer = new bytes32[](numNodesInLayer);
136:         //fill the layer with the pairwise hashes of the leaves
137:         for (uint i = 0; i < numNodesInLayer; i++) {
138:             layer[i] = sha256(abi.encodePacked(leaves[2*i], leaves[2*i+1]));
139:         }
140:         //the next layer above has half as many nodes
141:         numNodesInLayer /= 2;
142:         //while we haven't computed the root
143:         while (numNodesInLayer != 0) {
144:             //overwrite the first numNodesInLayer nodes in layer with the pairwise hashes of their children
145:             for (uint i = 0; i < numNodesInLayer; i++) {
146:                 layer[i] = sha256(abi.encodePacked(layer[2*i], layer[2*i+1]));
147:             }
148:             //the next layer above has half as many nodes
149:             numNodesInLayer /= 2;
150:         }
151:         //the first node in the layer is the root
152:         return layer[0];
153:     }

```

## [G-03] Using delete statement can save gas

1 results - 1 files:

``` solidity
BathBuddy.sol
eigen\StrategyManager.sol
825:  beaconChainETHSharesToDecrementOnWithdrawal[staker] = 0;
```

## [G-04] Functions guaranteed to revert when called by normal users can be marked payable

If a function modifier or require such as onlyOwner/onlyX is used, the function will revert if a normal user tries to pay the function. Marking the function as payable will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided. The extra opcodes avoided are CALLVALUE(2), DUP1(3), ISZERO(3), PUSH2(3), JUMPI(10), PUSH1(3), DUP1(3), REVERT(0), JUMPDEST(1), POP(2) which costs an average of about 21 gas per call to the function, in addition to the extra deployment cost.

2 results - 2 files:

``` solidity
eigen\DelayedWithdrawalRouter.sol
100:     function setWithdrawalDelayBlocks(uint256 newValue) external onlyOwner {
101:         _setWithdrawalDelayBlocks(newValue);
102:     }


eigen\StrategyManager.sol
581:     /// @notice Owner-only function for modifying the value of the `withdrawalDelayBlocks` variable.
582:     function setWithdrawalDelayBlocks(uint256 _withdrawalDelayBlocks) external onlyOwner {
583:         _setWithdrawalDelayBlocks(_withdrawalDelayBlocks);
584:     }
585: 
586:     /// @notice Owner-only function to change the `strategyWhitelister` address.
587:     function setStrategyWhitelister(address newStrategyWhitelister) external onlyOwner {
588:         _setStrategyWhitelister(newStrategyWhitelister);
589:     }

```


## [G-05] Use hardcode address instead address(this)


Instead of using address(this), it is more gas-efficient to pre-calculate and use the hardcoded address. Foundry's script.sol and solmate's LibRlp.sol contracts can help achieve this.


References: https://book.getfoundry.sh/reference/forge-std/compute-create-address

https://twitter.com/transmissions11/status/1518507047943245824

3 results - 3 files:

``` solidity

eigen\EigenPod.sol
464:         _sendETH(podOwner, address(this).balance);
469:         return abi.encodePacked(bytes1(uint8(1)), bytes11(0), address(this));

eigen\StrategyBase.sol
260:   return strategyManager.stakerStrategyShares(user, IStrategy(address(this)));
266:         return underlyingToken.balanceOf(address(this));

eigen\StrategyManager.sol
150:   DOMAIN_SEPARATOR = keccak256(abi.encode(DOMAIN_TYPEHASH, keccak256(bytes("EigenLayer")), ORIGINAL_CHAIN_ID, address(this)));
151:      
276:     bytes32 domain_separator = keccak256(abi.encode(DOMAIN_TYPEHASH, keccak256(bytes("EigenLayer")), block.chainid, address(this)));
```

## [G-06] Sort Solidity operations using short-circuit mode

Short-circuiting is a solidity contract development model that uses OR/AND logic to sequence different cost operations. It puts low gas cost operations in the front and high gas cost operations in the back, so that if the front is low If the cost operation is feasible, you can skip (short-circuit) the subsequent high-cost Ethereum virtual machine operation.

2 results - 2 files:


``` solidity
eigen\StrategyBase.sol
148:         require(updatedTotalShares >= MIN_NONZERO_TOTAL_SHARES || updatedTotalShares == 0,
149:             "StrategyBase.withdraw: updated totalShares amount would be nonzero but below MIN_NONZERO_TOTAL_SHARES");
216:         if (tokenBalance == 0 || totalShares == 0) {

eigen\StrategyManager.sol
761:         require(queuedWithdrawal.withdrawalStartBlock + withdrawalDelayBlocks <= block.number 
762:                 || queuedWithdrawal.strategies[0] == beaconChainETHStrategy,
763:             "StrategyManager.completeQueuedWithdrawal: withdrawalDelayBlocks period has not yet passed"
764:         );

```

## [G-07] Using unchecked blocks to save gas - Increments in for loop can be unchecked ( save 30-40 gas per loop iteration)

The majority of Solidity for loops increment a uint256 variable that starts at 0. These increment operations never need to be checked for over/underflow because the variable will never reach the max number of uint256 (will run out of gas long before that happens). The default over/underflow check wastes gas in every iteration of virtually every for loop . eg.

e.g Let's work with a sample loop below.
``` solidity
for(uint256 i; i < 10; i++){
//doSomething
}
```
can be written as shown below.
``` diff
for(uint256 i; i < 10;) {
// loop logic
+  unchecked { i++; }
}
```
We can also write it as an inlined function like below.
``` solidity
function inc(i) internal pure returns (uint256) {
unchecked { return i + 1; }
}
for(uint256 i; i < 10; i = inc(i)) {
// doSomething
}
```

The above should be modified to:

7 results - 3 files:

``` diff
eigen\BeaconChainProofs.sol
133:         for (uint256 i = 0; i < NUM_BEACON_BLOCK_HEADER_FIELDS; ++i) {
134:             paddedHeaderFields[i] = blockHeaderFields[i];
135:         }

143:         for (uint256 i = 0; i < NUM_BEACON_STATE_FIELDS; ++i) {
144:             paddedBeaconStateFields[i] = beaconStateFields[i];
145:         }

153:         for (uint256 i = 0; i < NUM_VALIDATOR_FIELDS; ++i) {
154:             paddedValidatorFields[i] = validatorFields[i];
155:         }

163:         for (uint256 i = 0; i < ETH1_DATA_FIELD_TREE_HEIGHT; ++i) {
164:             paddedEth1DataFields[i] = eth1DataFields[i];
165:         }

eigen\DelayedWithdrawalRouter.sol
115:         for (uint256 i = 0; i < claimableDelayedWithdrawalsLength; i++) {
116:             claimableDelayedWithdrawals[i] = _userWithdrawals[user].delayedWithdrawals[delayedWithdrawalsCompleted + i];
117:         }

eigen\Merkle.sol
137:         for (uint i = 0; i < numNodesInLayer; i++) {
138:             layer[i] = sha256(abi.encodePacked(leaves[2*i], leaves[2*i+1]));
139:         }
145:             for (uint i = 0; i < numNodesInLayer; i++) {
146:                 layer[i] = sha256(abi.encodePacked(layer[2*i], layer[2*i+1]));
147:             }
```

## [G-08] Using storage instead of memory for structs/arrays saves gas

When fetching data from a storage location, assigning the data to a memory variable causes all fields of the struct/array to be read from storage, which incurs a Gcoldsload (2100 gas) for each field of the struct/array. If the fields are read from the new memory variable, they incur an additional MLOAD rather than a cheap stack read. Instead of declearing the variable with the memory keyword, declaring the variable with the storage keyword and caching any fields that need to be re-read in stack variables, will be much cheaper, only incuring the Gcoldsload for the fields actually read. The only time it makes sense to read the whole struct/array into a memory variable, is if the full struct/array is being returned by the function, is being passed to a function that requires memory, or if the array/struct is being read from another memory array/struct

7 results - 2 files:

``` solidity
eigen\BeaconChainProofs.sol
131:         bytes32[] memory paddedHeaderFields = new bytes32[](2**BEACON_BLOCK_HEADER_FIELD_TREE_HEIGHT);
132:         

141:   bytes32[] memory paddedBeaconStateFields = new bytes32[](2**BEACON_STATE_FIELD_TREE_HEIGHT);

151:         bytes32[] memory paddedValidatorFields = new bytes32[](2**VALIDATOR_FIELD_TREE_HEIGHT);
152:         

161:         bytes32[] memory paddedEth1DataFields = new bytes32[](2**ETH1_DATA_FIELD_TREE_HEIGHT);
162:         

eigen\DelayedWithdrawalRouter.sol
62:             DelayedWithdrawal memory delayedWithdrawal = DelayedWithdrawal({
63:                 amount: withdrawalAmount,
64:                 blockCreated: uint32(block.number)
65:             });

114:   DelayedWithdrawal[] memory claimableDelayedWithdrawals = new DelayedWithdrawal[](claimableDelayedWithdrawalsLength);
115: 


144:    DelayedWithdrawal memory delayedWithdrawal = _userWithdrawals[recipient].delayedWithdrawals[delayedWithdrawalsCompletedBefore + i];
145:    


```

## [G-09] Multiple address/ID mappings can be combined into a single mapping of an address/ID to a struct, where appropriate

Saves a storage slot for the mapping. Depending on the circumstances and sizes of types, can avoid a Gsset (20000 gas) per mapping combined. Reads and subsequent writes can also be cheaper when a function requires both values and they both fit in the same storage slot. Finally, if both fields are accessed in the same function, can save ~42 gas per access due to not having to recalculate the key's keccak256 hash (Gkeccak256 - 30 gas)

6 results - 2 files:

``` solidity

eigen\EigenPod.sol
79:     mapping(uint40 => mapping(uint64 => bool)) public provenPartialWithdrawal;
80: 

78:     /// @notice This is a mapping of validatorIndex to withdrawalIndex to whether or not they have proven a withdrawal for that index
79:     mapping(uint40 => mapping(uint64 => bool)) public provenPartialWithdrawal;
80: 

eigen\StrategyManagerStorage.sol
25:     mapping(address => uint256) public nonces;

53:     mapping(bytes32 => bool) public withdrawalRootPending;

55:     mapping(address => uint256) public numWithdrawalsQueued;

68:     mapping(address => uint256) public beaconChainETHSharesToDecrementOnWithdrawal;

```

## [G-10] Using private rather than public for constants, saves gas

If needed, the values can be read from the verified contract source code, or if there are multiple values there can be a single getter function that returns a tuple of the values of all currently-public constants. Saves 3406-3606 gas in deployment gas due to the compiler not having to create non-payable getter functions for deployment calldata, not having to store the bytes of the value outside of where it's used, and not adding another entry to the method ID table

There are ALL CONTRACT instances of this issue eg. :

``` diff
- uint256 public override example;
+ uint256 private override example;
```

## [G-11] Using immutable for uint & address for save gas
Avoids a Gsset (20000 gas) in the constructor, and replaces the first access in each transaction (Gcoldsload - 2100 gas) and each access thereafter (Gwarmacces - 100 gas) with a PUSH32 (3 gas).

While strings are not value types, and therefore cannot be immutable/constant if not hard-coded outside of the constructor, the same behavior can be achieved by making the current contract abstract with virtual functions for the string accessors, and having a child contract override the functions with the hard-coded implementation-specific values.

There are ALL CONTRACT instances of this issue eg. :

``` diff
- address public override example;
+ address public immutable override example;
```

## [G-12] internal functions not called by the contract should be removed to save deployment gas
If the functions are required by an interface, the contract should inherit from that interface and use the override keyword

6 results - 3 files:

``` solidity

eigen\BeaconChainProofs.sol
192:  function verifyValidatorFields(
193:         uint40 validatorIndex,
194:         bytes32 beaconStateRoot,
195:         bytes calldata proof, 
196:         bytes32[] calldata validatorFields
197:     ) internal view {

221:     function verifyValidatorBalance(
222:         uint40 validatorIndex,
223:         bytes32 beaconStateRoot,
224:         bytes calldata proof,
225:         bytes32 balanceRoot
226:     ) internal view {

245:     function verifyWithdrawalProofs(
246:         bytes32 beaconStateRoot,
247:         WithdrawalProofs calldata proofs,
248:         bytes32[] calldata withdrawalFields
249:     ) internal view {

eigen\EigenPod.sol
468:  function _podWithdrawalCredentials() internal view returns(bytes memory) {

eigen\Merkle.sol
80:     function verifyInclusionSha256(
81:         bytes memory proof,
82:         bytes32 root,
83:         bytes32 leaf,
84:         uint256 index
85:     ) internal view returns (bool) {

99:   function processInclusionProofSha256(bytes memory proof, bytes32 leaf, uint256 index) internal view returns (bytes32) {
100:   


```

## [G-13] x = x + y is cheaper than x += y;

7 results - 2 files:

``` solidity
eigen\DelayedWithdrawalRouter.sol
150:             amountToSend += delayedWithdrawal.amount;

eigen\EigenPod.sol
379:    restakedExecutionLayerGwei += REQUIRED_BALANCE_GWEI;

382: restakedExecutionLayerGwei += withdrawalAmountGwei;

395:  restakedExecutionLayerGwei += REQUIRED_BALANCE_GWEI;

403:     restakedExecutionLayerGwei += withdrawalAmountGwei;

eigen\StrategyManager.sol
192:    beaconChainETHSharesToDecrementOnWithdrawal[overcommittedPodOwner] += debt;

644:  stakerStrategyShares[depositor][strategy] += shares;
```

## [G-14] Usage of uints/ints smaller than 32 bytes (256 bits) incurs overhead
When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.

https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html
Use a larger size then downcast where needed


## [G-15] Using > 0 costs more gas than != 0 when used on a uint in a require() statement

The optimization works until solidity version 0.8.13 where there is a regression in gas costs.

6 results - 3 files:

``` solidity
eigen\Pausable.sol
44:         require(_paused == 0, "Pausable: contract is paused");

eigen\StrategyBase.sol
92:  if (totalShares == 0) {

98:      if (priorTokenBalance == 0) {

184:      if (totalShares == 0) {

216:    if (tokenBalance == 0 || totalShares == 0) {

eigen\StrategyManager.sol
700:    if (userShares == 0) {
```


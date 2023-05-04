# Gas Optimization

1. **Unused imports**

There are unused imports. They will increase the size of deployment with no real benefit. An example of such an import is:

```
import "@openzeppelin/contracts/token/ERC20/presets/ERC20PresetFixedSupply.sol";
import "@openzeppelin/contracts/proxy/transparent/TransparentUpgradeableProxy.sol";
import "../src/contracts/interfaces/IDelegationManager.sol";

```

## Recommended Mitigation Steps

Consider removing unused imports to save some gas

### 2. Refactoring mappings

Could potentially save some gas, depending on the usage patterns of the contract.

Packing Data into a Single Mapping: If multiple pieces of data are associated with each address in the mappings, such as in the case of **`operatorShares`** and **`delegationTerms`**, it may be possible to pack this data into a single mapping. For example, instead of having two separate mappings:

```
typescriptCopy code
mapping(address => mapping(IStrategy => uint256)) public operatorShares;
mapping(address => IDelegationTerms) public delegationTerms;

```

We could have a single mapping that stores a struct containing both pieces of data:

```
scssCopy code
struct OperatorInfo {
    mapping(IStrategy => uint256) shares;
    IDelegationTerms delegationTerms;
}
mapping(address => OperatorInfo) public operatorInfo;

```

This can potentially save gas by reducing the number of storage slots used by the contract, as well as reducing the number of SSTORE operations needed to modify the mappings.

**Alternatively;** Arrays for One-to-Many Relationships:

1. In the case of **`delegatedTo`**, it may be possible to save gas by using an array to store the addresses that have delegated to each operator, instead of using a separate mapping for each operator. For example, we could change the mapping:

```
cssCopy code
mapping(address => address) public delegatedTo;

```

To an array:

```
cssCopy code
mapping(address => address[]) public operators;

```

And add a function to add an address to the array:

```
arduinoCopy code
function delegateTo(address operator) public {
    delegatedTo[msg.sender] = operator;
    operators[operator].push(msg.sender);
}

```

This can potentially save gas by reducing the number of storage slots used by the contract, as well as reducing the number of SSTORE operations needed to modify the mappings.

### 

1. The contract uses the StructuredLinkedList library, which may result in high gas costs when the list becomes large.[Link](https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/core/Slasher.sol)
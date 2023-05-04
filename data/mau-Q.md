# [N-1] Use the latest Solidity version

## Comments

**Security**: Newer versions of Solidity often include security improvements and bug fixes that make the language more secure and less vulnerable to attacks. Using an older version may expose your smart contract to security vulnerabilities that have been addressed in newer versions.

**Compatibility**: Using the latest version of Solidity ensures that your smart contract is compatible with other contracts and tools in the Ethereum ecosystem that are also using the latest version. If you use an older version, you may run into compatibility issues with newer contracts and tools.

**Functionality**: Newer versions of Solidity often introduce new features and functionality that can make your smart contract more powerful and flexible. By using the latest version, you can take advantage of these new features and improve the functionality of your smart contract.

**Support**: Solidity is an open-source project that is actively maintained by a community of developers. By using the latest version, you can access the latest support and resources from the community, which can help you develop and debug your smart contract more effectively.

Overall, using the latest version of Solidity is a good practice to ensure the security, compatibility, functionality, and support of your smart contract. However, it is also important to carefully test and verify any changes that you make to your code to ensure that they work as intended.

## Instances

https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/core/DelegationManager.sol#L2
https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/core/DelegationManagerStorage.sol#L2
https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/core/Slasher.sol#L2
https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/core/StrategyManager.sol#L2
https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/core/StrategyManagerStorage.sol#L2

# [N-2] Use named parameters for mapping type declarations

## Comments

 Using named parameters in mappings can provide several advantages:

**Improved Readability**: Named parameters make it easier to understand the purpose and functionality of a mapping by indicating the meaning and types of its keys and values. This can improve the readability of the code and make it more self-explanatory, especially when dealing with more complex mappings that involve multiple keys and/or values.

**Easier Maintenance**: If you need to modify a mapping in the future, using named parameters can make the process easier and less error-prone. Instead of searching for every occurrence of the old mapping definition and updating it manually, you can simply update the named parameter. This can save time and reduce the risk of introducing bugs into the code.

**Better Type Safety**: Named parameters help to ensure type safety in the mapping by making it clear what types of keys and values the mapping is intended to store. This can reduce the risk of type-related bugs in the code and make it easier to catch errors during compilation.

**Improved Documentation**: Using named parameters in mappings can also improve the documentation of your code by providing clear and concise descriptions of the mapping's purpose and functionality. This can make it easier for other developers to understand and use your code, and can also make it easier to maintain the code over time.

## Instances

https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/core/DelegationManagerStorage.sol#LL36C5-L36C5
https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/core/DelegationManagerStorage.sol#L39
https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/core/DelegationManagerStorage.sol#L42
https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/core/DelegationManagerStorage.sol#L45
https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/core/Slasher.sol#L35
https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/core/Slasher.sol#L37
https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/core/Slasher.sol#L46https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/core/Slasher.sol#L57
https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/core/StrategyManagerStorage.sol#L49
https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/core/StrategyManagerStorage.sol#L51
https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/core/StrategyManagerStorage.sol#L53
https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/core/StrategyManagerStorage.sol#L55
https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/core/StrategyManagerStorage.sol#L57


# [N-3] Event is missing indexed fields

## Comments

If you're creating an event function with more than two parameters, it's a good idea to index the ones that you'll be searching for later. This will help you filter through the logs and find the data you need more easily. Just remember that indexing too many fields can increase the gas cost, so be mindful of that as well.

## Instances
https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/core/Slasher.sol#L60
https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/core/StrategyManager.sol#L55
https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/core/StrategyManager.sol#L65
https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/core/StrategyManager.sol#L77


# [N-4] The nonReentrant modifier should occur before all other modifiers

# Comments

It is considered a good practice to put it before all other modifiers for the following reasons:

**Safety**: Putting the nonReentrant modifier before all other modifiers ensures that it is the first modifier to be checked before any other modifier is executed. This can prevent unintended or malicious behavior in the function and can ensure the safety and security of the contract.

**Readability**: Placing the nonReentrant modifier before all other modifiers can improve the readability and clarity of the code. It makes it clear that the nonReentrant modifier is a critical part of the function's security and functionality, and allows developers to easily understand the purpose and behavior of the function.

**Consistency**: By placing the nonReentrant modifier before all other modifiers, developers can maintain consistency in their code. This ensures that the nonReentrant modifier is always checked first, regardless of the function or the context in which it is used.

**Avoids Unintended Interactions**: By placing the nonReentrant modifier before all other modifiers, you can avoid unintended interactions between the nonReentrant modifier and other modifiers. For example, if the nonReentrant modifier is placed after a modifier that modifies the state of the contract, it can cause unexpected behavior and even introduce security vulnerabilities.

# Instances

https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/core/StrategyManager.sol#L164
https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/core/StrategyManager.sol#L182
https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/core/StrategyManager.sol#L220
https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/core/StrategyManager.sol#L248
https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/core/StrategyManager.sol#L329
https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/core/StrategyManager.sol#L442
https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/core/StrategyManager.sol#L456
https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/core/StrategyManager.sol#L482
https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/core/StrategyManager.sol#L536









